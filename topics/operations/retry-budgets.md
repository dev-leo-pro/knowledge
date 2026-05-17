---
id: retry-budgets
title: "Presupuestos de reintento"
type: concept
status: learning
importance: 70
difficulty: medium
tags: [reliability, retries, performance]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Presupuestos de reintento

## TL;DR (BLUF)
- Un presupuesto de reintento limita cuánta carga adicional se permite introducir por reintentos.
- Úsalo para prevenir tormentas de reintentos y proteger la capacidad descendente.
- Trade-off: menos reintentos pueden reducir la tasa de éxito durante fallos transitorios.

## Definición
**Qué es:** Una política que limita el volumen de reintentos asignando un "presupuesto" acotado (porcentaje o tasa fija) relativo a las solicitudes exitosas durante una ventana de tiempo.  
**Términos clave:** ratio de reintentos, ventana de presupuesto, tasa de éxito, tormenta de reintentos, amplificación de carga.

## Por qué importa
- Los reintentos son carga extra; sin un presupuesto pueden amplificar fallos y provocar [Fallo en cascada](cascading-failure.md).
- Los presupuestos te obligan a equilibrar la recuperación con la estabilidad del sistema.

## Alcance y no-objetivos
**Dentro del alcance:** límites de reintentos del lado del cliente para llamadas HTTP, colas y consumidores.  
**Fuera del alcance / NO resuelto por esto:** determinar errores reintentables (ver [Reintentos, backoff exponencial y jitter](retries-and-backoff.md)) o protección contra sobrecarga por sí sola (ver [Contrapresión (Backpressure)](../system-design/backpressure.md)).

## Modelo mental / Intuición
- "Solo puedes gastar un pequeño porcentaje de tu capacidad de éxito en reintentos."

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Una dependencia es compartida entre muchos clientes o tenants.
- Ves picos de reintentos o alta latencia de cola durante interrupciones parciales.
- Necesitas barreras para evitar que los reintentos amplifiquen la carga.

### Evítalo cuando
- El sistema es pequeño y los reintentos son raros (aún mantén un límite).
- Ya aplicas límites estrictos de concurrencia y no reintentas (raro).

## Cómo lo usaría (práctico)
- **Contexto:** Un servicio llama a un proveedor de pagos con timeouts intermitentes.
- **Pasos:**
  1) Usar un token bucket local: por cada 10 solicitudes exitosas, ganar 1 token de reintento.
  2) Si una solicitud falla, gastar 1 token para reintentar; si no hay tokens, fallar rápido.
  3) Combinar con timeouts por intento y backoff exponencial.
- **Cómo se ve el éxito:** los reintentos ayudan con errores transitorios sin saturar la dependencia.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** previene tormentas de reintentos, mejora la estabilidad del sistema.
- **Desventajas / Riesgos:** menor tasa de éxito bajo errores transitorios cortos si el presupuesto es muy estricto.
### Alternativas
- **Circuit breaker:** detener llamadas durante fallos sostenidos.
- **Contrapresión / descarte de carga:** reducir la carga ascendente en lugar de reintentar.
- **Cómo elegir:** usa presupuestos cuando los reintentos son necesarios pero deben estar acotados; usa breakers cuando la dependencia no está sana.

## Modos de fallo y trampas
- Presupuesto muy alto → los reintentos aún abruman las dependencias.
- Presupuesto muy bajo → fallos innecesarios durante problemas transitorios.
- Ignorar límites por tenant → un tenant quema el presupuesto.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de reintentos, % de uso del presupuesto de reintento, % de éxito-después-de-reintento, latencia p95/p99.
- **Logs:** decisión de presupuesto (permitido/denegado), uso actual del presupuesto.
- **Trazas:** etiquetar reintentos con estado del presupuesto.
- **Alertas:** saturación del presupuesto + picos de errores.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir una ventana de tiempo (por ejemplo, 1 minuto)
  - [ ] Definir un presupuesto (por ejemplo, reintentos <= 10% de éxitos)
  - [ ] Aplicar presupuesto antes de reintentar
  - [ ] Agregar límites por tenant si es multi-tenant
- **Notas de seguridad / cumplimiento**
  - N/A
- **Notas de rendimiento**
  - Mantener las verificaciones de presupuesto de baja latencia y locales si es posible
- **Notas operacionales**
  - Ajustar presupuestos basándose en SLOs e historial de incidentes

## Mini ejemplo (si aplica)
```text
retry_tokens += floor(successes / 10)
if retry_tokens > 0: retry_tokens -= 1; retry
else: fail_fast
```

## Anti-patrones comunes
- **Anti-patrón:** "Los reintentos son gratis, solo haz más."
  - **Por qué es malo:** los reintentos agregan carga y pueden causar interrupciones.
  - **Mejor enfoque:** usa un presupuesto y backoff.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Un presupuesto de reintento es un límite sobre los reintentos relativo al tráfico exitoso, para que los reintentos ayuden a la recuperación sin abrumar las dependencias.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los presupuestos de reintento son solo para clientes HTTP?
   - **R:** No. Se aplican a colas y consumidores también, en cualquier lugar donde los reintentos agregan carga.
2) **P:** ¿Un presupuesto de reintento reemplaza los timeouts?
   - **R:** No. Los timeouts acotan cada intento; los presupuestos acotan la carga total de reintentos.
3) **P:** ¿Los presupuestos deberían ser globales o por tenant?
   - **R:** A menudo por tenant o por ruta para evitar que un tenant agote el presupuesto.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir presupuestos de reintento con precisión.
- [ ] Puedo indicar cuándo usarlos y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Timeouts](timeouts.md)
- [Reintentos, backoff exponencial y jitter](retries-and-backoff.md)

### Temas relacionados
- [Circuit breaker](circuit-breaker.md)
- [Contrapresión (Backpressure)](../system-design/backpressure.md)
- [Descarte de carga](load-shedding.md)
- [Fallo en cascada](cascading-failure.md)
- [Cobertura de solicitudes](request-hedging.md)

### Comparar con
- [Circuit breaker](circuit-breaker.md) — el breaker deja de llamar; los presupuestos de reintento aún permiten reintentos limitados.
- [Contrapresión (Backpressure)](../system-design/backpressure.md) — la contrapresión limita la carga; los presupuestos limitan la carga de reintentos.

## Notas / Bandeja de entrada (opcional)
- N/A
