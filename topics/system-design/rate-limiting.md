---
id: rate-limiting
title: "Rate Limiting"
type: pattern
status: learning
importance: 75
difficulty: medium
tags: [system-design, reliability, security, api-design]
canonical: true
aliases: [throttling, quota]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Limitación de Tasa (Rate Limiting)

## TL;DR (BLUF)
- Controla la tasa de peticiones por cliente/usuario/IP/endpoint para prevenir abuso y sobrecarga.
- Úsalo para APIs públicas, control de costos y protección de recursos compartidos.
- Trade-off: usuarios legítimos pueden ser limitados durante ráfagas.

## Definición
**Qué es:** Un mecanismo de control que limita el número de peticiones que un cliente puede hacer dentro de una ventana de tiempo (ej., 100 peticiones por minuto), rechazando peticiones excesivas con 429 (Too Many Requests).

**Términos clave:** cuota, throttling, token bucket, leaky bucket, ventana deslizante, ventana fija, límite de tasa, contrapresión.

## Por qué importa
- Protege sistemas de abuso (DDoS, scrapers, scripts descontrolados).
- Asegura asignación justa de recursos entre usuarios/tenants.
- Controla costos de infraestructura (llamadas API, consultas de BD, tarifas de terceros).
- Complementa [Contrapresión](backpressure.md) y [Descarte de carga](../operations/load-shedding.md).

## Alcance y no-objetivos
**Dentro del alcance:** cuotas por usuario, por IP, por API-key, por endpoint; algoritmos (token bucket, leaky bucket, ventana deslizante).

**Fuera del alcance / NO resuelto por esto:**
- Proteger contra peticiones lentas *ya aceptadas* (usar [Timeout](../operations/timeouts.md))
- Limitación de tasa interna servicio-a-servicio (usar [Bulkhead](../operations/bulkheads.md) o [Concurrencia adaptativa](../operations/adaptive-concurrency.md))
- Errores de lógica a nivel de aplicación (usar circuit breakers y reintentos)

## Modelo mental / Intuición
- Como un portero de discoteca: solo X personas por hora. Si la cuota está llena, esperas o eres rechazado.
- En APIs: cada cliente tiene un "presupuesto de tokens" que se rellena con el tiempo. Las peticiones consumen tokens.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tengas una API pública o sistema multi-tenant.
- Necesites prevenir abuso, scraping o automatización descontrolada.
- Los costos de infraestructura escalen con el uso (terceros de pago por petición, APIs cloud).
- Quieras imponer niveles de SLA (gratuito: 100 req/min, premium: 10k req/min).

### Evítalo cuando
- El sistema sea privado/interno con clientes confiables (agrega sobrecarga).
- Puedas escalar infinitamente sin preocupaciones de costo (raro).
- Ya tengas suficiente [Contrapresión](backpressure.md) y [Descarte de carga](../operations/load-shedding.md).

## Cómo lo usaría (práctico)
- **Contexto:** Una API SaaS con usuarios gratuitos y premium.
- **Pasos:**
  1) Elegir algoritmo: **token bucket** (permite ráfagas cortas) o **ventana deslizante** (equidad estricta).
  2) Definir límites por nivel:
     - Gratuito: 100 peticiones/minuto
     - Premium: 10,000 peticiones/minuto
  3) Implementar en [API Gateway](api-gateway.md) o capa de aplicación (basado en Redis).
  4) Devolver `429 Too Many Requests` con headers:
     ```
     X-RateLimit-Limit: 100
     X-RateLimit-Remaining: 0
     X-RateLimit-Reset: 1643728800
     Retry-After: 60
     ```
  5) Monitorear: peticiones rechazadas, patrones de ráfagas, quejas de usuarios.
  6) Ajustar límites basándose en uso real y presupuestos de SLO.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Tráfico legítimo en ráfagas limitado | Usar token bucket (permite ráfagas); proporcionar "asignación de ráfaga" |
| Sobrecarga de rastreo por usuario | Usar estructuras de datos eficientes (sorted sets de Redis, contadores de ventana deslizante) |
| Coordinación en sistemas distribuidos | Usar limitador centralizado (Redis) o aceptar consistencia eventual |
| Falsos positivos (IPs compartidas, NAT) | Limitar por API key/ID de usuario en vez de IP |
| Clientes no manejan 429 elegantemente | Devolver mensajes de error claros + header `Retry-After`; documentar política de reintento |

## Modos de fallo / Casos límite
1. **Estampida después del reset del límite:** Todos los clientes reintentan al mismo tiempo cuando la ventana se resetea.
   - *Mitigación:* Usar ventana deslizante o agregar jitter a reintentos del cliente.
2. **Desvío de limitación distribuida:** Múltiples instancias del gateway tienen contadores inconsistentes.
   - *Mitigación:* Centralizar estado en Redis/Memcached con operaciones atómicas.
3. **Bypass vía rotación de IP:** Atacantes rotan IPs para evadir límites.
   - *Mitigación:* Limitar por API key o ID de usuario; agregar CAPTCHA para patrones sospechosos.
4. **El limitador se convierte en cuello de botella:** Redis/almacén centralizado se satura.
   - *Mitigación:* Usar aproximación local (límites por instancia) o hashing consistente.
5. **Usuarios legítimos castigados durante incidentes:** Altas tasas de error consumen cuota.
   - *Mitigación:* Solo contar peticiones exitosas; excluir errores 5xx de la cuota.

## Alternativas
- **[Contrapresión](backpressure.md):** Empujar hacia atrás upstream cuando downstream está saturado.
- **[Descarte de carga](../operations/load-shedding.md):** Descartar peticiones basándose en carga del sistema, no cuotas por usuario.
- **[Circuit Breaker](../operations/circuit-breaker.md):** Falla rápido cuando la dependencia está caída (lado cliente).
- **Control de admisión basado en cola:** Aceptar todas las peticiones en una cola, procesar a tasa fija.

## Algoritmos
### 1. Ventana Fija
- Contador simple que se resetea cada N segundos.
- **Problema:** Ráfaga en límite de ventana (100 req a 0:59, 100 a 1:00 = 200 req en 1 seg).

### 2. Ventana Deslizante
- Conteo ponderado de peticiones en ventana de tiempo rodante.
- **Pros:** Cumplimiento más suave.
- **Contras:** Más complejo de implementar.

### 3. Token Bucket
- Los tokens se rellenan a tasa constante; las peticiones consumen tokens; permite ráfagas.
- **Pros:** Flexible, maneja ráfagas elegantemente.
- **Contras:** Ligeramente más estado para rastrear.

### 4. Leaky Bucket
- Las peticiones entran en cola; procesadas a tasa fija.
- **Pros:** Tasa de salida suave.
- **Contras:** Puede retrasar peticiones (no siempre deseable para APIs).

**Recomendación:** Usar **token bucket** para APIs (permite ráfagas) o **ventana deslizante** para equidad estricta.

## Combinaciones
La Limitación de Tasa **casi siempre se usa con:**
- **[API Gateway](api-gateway.md):** Cumplimiento centralizado en el borde.
- **[Timeout](../operations/timeouts.md):** Previene que peticiones lentas retengan recursos.
- **[Bulkhead](../operations/bulkheads.md):** Aísla el impacto del tráfico de un cliente.
- **[Observabilidad](../operations/observability-basics.md):** Rastrear tasas de 429, principales infractores, patrones de ráfagas.
- **[Degradación Elegante](../operations/graceful-degradation.md):** Devolver resultados cacheados/parciales en vez de rechazo duro.

**Combinación típica:**
- **Escenario de API pública:** API Gateway + Limitación de Tasa + Timeout + Reintento (lado cliente con backoff) + Circuit Breaker + Observabilidad

## Prerrequisitos
- Comprensión de [códigos de estado HTTP](../operations/http.md) (429, 503).
- Familiaridad con [diseño de API](api-design-basics.md) y [Contrapresión](backpressure.md).
- Conocimiento básico de sistemas distribuidos (compartición de estado, consistencia).

## Temas relacionados
- [API Gateway](api-gateway.md): Donde la limitación de tasa se aplica típicamente.
- [Contrapresión](backpressure.md): Control de flujo a nivel de sistema.
- [Descarte de carga](../operations/load-shedding.md): Descartar peticiones basándose en carga del sistema.
- [Bulkhead](../operations/bulkheads.md): Aislamiento de recursos.
- [Observabilidad](../operations/observability-basics.md): Monitorear métricas de throttling.

## Ejemplos del mundo real
1. **API de GitHub:** 5,000 peticiones/hora para usuarios autenticados, 60 para no autenticados.
2. **Stripe:** Limita tasa por API key con ventana deslizante; devuelve header `Retry-After`.
3. **API de Twitter:** Límites escalonados (gratuito, básico, enterprise) con ventanas de 15 minutos.
4. **AWS API Gateway:** Throttling integrado (10,000 req/seg por defecto, configurable).

## Checklist (auto-evaluación)
- [ ] Puedo explicar la diferencia entre ventana fija, ventana deslizante y token bucket.
- [ ] Sé cuándo limitar por IP vs API key vs ID de usuario.
- [ ] Entiendo cómo manejar respuestas 429 en clientes (backoff exponencial).
- [ ] Puedo diseñar límites de tasa para SaaS multi-nivel (gratuito/premium).
- [ ] Puedo monitorear la efectividad de la limitación (tasa de rechazo, falsos positivos).

## Recordatorios / Puntos clave
- La limitación de tasa es **preventiva**: detiene el abuso antes de que sobrecargue el sistema.
- Siempre devolver header `Retry-After` para ayudar a los clientes a comportarse correctamente.
- Usar token bucket para APIs (permite ráfagas cortas), ventana deslizante para equidad estricta.
- Combinar con [Timeout](../operations/timeouts.md), [Circuit Breaker](../operations/circuit-breaker.md) y [Observabilidad](../operations/observability-basics.md).

## Preguntas trampa (con respuestas)
### P1: ¿Debo limitar por dirección IP o API key?
**R:** **Depende del caso de uso**. Para APIs públicas, usar **API key** (más preciso, evita problemas de NAT/IP compartida). Para endpoints no autenticados (login, registro), usar **dirección IP** para prevenir fuerza bruta. Para configuraciones sofisticadas, combinar ambos: cuotas por usuario + topes por IP.

### P2: ¿Cuál es la diferencia entre limitación de tasa y contrapresión?
**R:** La **limitación de tasa** es **proactiva**: rechaza peticiones excesivas en el borde basándose en cuotas. La **[Contrapresión](backpressure.md)** es **reactiva**: ralentiza productores cuando los consumidores están saturados. La limitación protege del abuso; la contrapresión protege de la sobrecarga. Usar ambas: limitación de tasa en API Gateway, contrapresión internamente.

### P3: Si devuelvo 429, ¿el cliente debe reintentar inmediatamente?
**R:** **No**. Los clientes deben implementar **backoff exponencial** y respetar el header `Retry-After`. Los reintentos inmediatos causan estampidas y empeoran la situación. Recomendado: esperar `Retry-After` segundos + jitter. Ver [Reintentos y backoff](../operations/retries-and-backoff.md).

### P4: ¿La limitación de tasa puede prevenir ataques DDoS?
**R:** **Parcialmente**. La limitación mitiga DDoS de capa de aplicación (inundaciones HTTP) limitando peticiones por IP/por usuario. Sin embargo, no protege contra **DDoS de capa de red** (inundaciones SYN, amplificación UDP). Para protección completa, combinar con: balanceador L4, CDN (Cloudflare, AWS Shield), WAF.

### P5: ¿Debo contar peticiones fallidas (4xx, 5xx) contra la cuota?
**R:** **Depende**. Para **4xx (errores del cliente)**, sí — previene que atacantes sondeen sin penalización. Para **5xx (errores del servidor)**, **no** — no castigar usuarios por tus fallos. Excepción: el propio 429 no debe consumir cuota (permite reintento elegante).
