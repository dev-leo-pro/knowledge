---
id: backend-for-frontend
title: "Backend for Frontend (BFF)"
type: pattern
status: learning
importance: 70
difficulty: medium
tags: [architecture, api-design, microservices]
canonical: true
aliases: [bff]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Backend for Frontend (BFF)

## TL;DR (BLUF)
- Un servicio backend especializado adaptado a las necesidades de un cliente específico (web, móvil, socios).
- Úsalo cuando diferentes clientes necesiten payloads, caché o contratos de API distintos.
- Trade-off: duplicación de código entre BFFs y mayor sobrecarga operacional.

## Definición
**Qué es:** Un patrón donde cada tipo de frontend (web, móvil, escritorio, socios) tiene su propio servicio backend dedicado que agrega, transforma y optimiza datos de los microservicios downstream.

**Términos clave:** backend for frontend, API específica por cliente, capa de agregación, optimización de payload, evolución independiente.

## Por qué importa
- Desacopla la experiencia del frontend de la estructura de los servicios backend.
- Permite que cada cliente evolucione independientemente (releases de web vs releases de móvil).
- Optimiza payloads por cliente (móvil necesita menos datos, web necesita más).
- Previene el diseño de API de "mínimo común denominador".

## Alcance y no-objetivos
**Dentro del alcance:** agregación específica por cliente, transformación, caché, delegación de autenticación, optimización de payload.

**Fuera del alcance / NO resuelto por esto:**
- Preocupaciones transversales (auth, limitación de tasa) → [API Gateway](../system-design/api-gateway.md)
- Lógica de negocio (pertenece a servicios de dominio)
- Preocupaciones de service mesh → [Sidecar](../operations/sidecar.md)

## Modelo mental / Intuición
- Piensa en un restaurante con diferentes menús para comer en el local, para llevar y para entrega. Misma cocina (servicios), diferente presentación.
- Web-BFF: detalles completos, filtrado enriquecido.
- Mobile-BFF: payload mínimo, optimizado para redes lentas.
- Partner-BFF: campos específicos, versionado estricto.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Diferentes clientes (web, móvil, IoT) tienen diferentes necesidades de payload/rendimiento.
- Los clientes se despliegan independientemente (web cada semana, móvil cada mes).
- Necesitas caché, limitación de tasa o políticas de seguridad específicas por cliente.
- Agregar de múltiples servicios crea problemas de "cliente parlanchín".
- Quieres experimentar con nuevas funcionalidades para un cliente sin afectar a otros.

### Evítalo cuando
- Tienes un solo cliente o los clientes comparten necesidades idénticas.
- La lógica de agregación es simple y puede vivir en el cliente.
- La sobrecarga operacional de múltiples BFFs supera los beneficios.
- Los equipos carecen de recursos para mantener bases de código separadas.

## Cómo lo usaría (práctico)
- **Contexto:** Plataforma de comercio electrónico con clientes web y móvil, llamando a servicios de usuario, producto, pedido y recomendación.
- **Pasos:**
  1) Crear dos BFFs: `web-bff` y `mobile-bff`.
  2) **Web-BFF:**
     - Agrega perfil de usuario + pedidos recientes + recomendaciones.
     - Devuelve detalles completos del producto (imágenes, descripciones, reseñas).
     - Implementa paginación del lado del servidor.
  3) **Mobile-BFF:**
     - Devuelve detalles mínimos del producto (miniatura, título, precio).
     - Implementa caché agresivo (CDN + Redis).
     - Usa formatos de imagen optimizados (WebP).
  4) Ambos BFFs llaman a los mismos servicios downstream pero transforman las respuestas.
  5) [API Gateway](../system-design/api-gateway.md) enruta: `/web/*` → web-bff, `/mobile/*` → mobile-bff.
  6) Monitorear: latencia, tasa de aciertos de caché, tasa de error por BFF.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Duplicación de código (lógica de agregación compartida) | Extraer bibliotecas/SDKs comunes; usar capa de acceso a datos compartida |
| Sobrecarga operacional (desplegar, monitorear N BFFs) | Usar pipelines de despliegue con plantillas; stack de observabilidad compartido |
| Riesgo de divergencia (los BFFs se desvían) | Imponer contratos compartidos con servicios downstream; tests de integración automatizados |
| Latencia añadida (salto extra) | Optimizar con caché, llamadas paralelas, HTTP/2 |
| Más difícil mantener consistencia | Usar servicios de dominio compartidos; los BFFs solo hacen transformación |

## Modos de fallo / Casos límite
1. **El BFF se convierte en un mini-monolito:** Los equipos añaden lógica de negocio en lugar de transformación.
   - *Mitigación:* Imponer política: los BFFs son capas delgadas de agregación, no servicios de dominio.
2. **Fallos en cascada desde downstream:** Si un servicio es lento, el BFF se bloquea.
   - *Mitigación:* Combinar con [Timeout](../operations/timeouts.md), [Circuit Breaker](../operations/circuit-breaker.md) y [Degradación Elegante](../operations/graceful-degradation.md).
3. **Inconsistencias por caché obsoleto:** El Mobile BFF cachea agresivamente, muestra datos antiguos.
   - *Mitigación:* Usar invalidación de caché (eventos, TTLs); monitorear métricas de obsolescencia.
4. **Confusión de propiedad:** ¿Quién es dueño del BFF? ¿El equipo de frontend o backend?
   - *Mitigación:* Modelo de propiedad claro (típicamente el equipo de frontend es dueño del BFF).
5. **Problema de consulta N+1:** El BFF llama al servicio downstream en un bucle.
   - *Mitigación:* Usar batching, llamadas paralelas o data loaders estilo GraphQL.

## Alternativas
- **API Gateway con transformación:** Más ligero pero limitado a transformaciones simples.
- **GraphQL:** Los clientes consultan exactamente lo que necesitan; reduce la lógica del BFF.
- **Composición de API en el cliente:** El cliente llama a múltiples servicios directamente (más parlanchín, más lento).
- **API backend compartida:** Una API para todos los clientes (fuerza el mínimo común denominador).

## Combinaciones
El BFF **casi siempre se usa con:**
- **[API Gateway](../system-design/api-gateway.md):** Enruta tráfico al BFF apropiado.
- **[Composición de APIs](api-composition.md):** El BFF agrega datos de múltiples servicios.
- **[Timeout](../operations/timeouts.md):** Evita que llamadas downstream lentas bloqueen.
- **[Circuit Breaker](../operations/circuit-breaker.md):** Falla rápido cuando el downstream no está saludable.
- **[Degradación Elegante](../operations/graceful-degradation.md):** Devuelve datos parciales si servicios no críticos fallan.
- **[Observabilidad](../operations/observability-basics.md):** Rastrear latencia, tasa de error por BFF.

**Combinación típica:**
- **Escenario Móvil + Web:** API Gateway + BFF (web/móvil) + Composición de APIs (en BFF) + Observabilidad

## Prerequisitos
- Comprensión de [Diseño de Microservicios](microservices-design-basics.md).
- Familiaridad con [Diseño de APIs](../system-design/api-design-basics.md) y [REST/GraphQL](../system-design/api-design-basics.md).
- Conocimiento de [Caché](../system-design/caching-fundamentals.md) y [HTTP](../operations/http.md).

## Temas relacionados
- [API Gateway](../system-design/api-gateway.md): Enruta a los BFFs.
- [Composición de APIs](api-composition.md): Patrón de agregación usado dentro de los BFFs.
- [Degradación Elegante](../operations/graceful-degradation.md): Respuestas parciales cuando los servicios fallan.
- [Diseño de Microservicios](microservices-design-basics.md): Contexto para el patrón BFF.
- [Estrategia de caché](../system-design/caching-strategy.md): Optimizar el rendimiento del BFF.

## Ejemplos del mundo real
1. **Netflix:** BFFs separados para clientes web, móvil y TV, cada uno optimizado para las restricciones del dispositivo.
2. **SoundCloud:** El Web-BFF agrega listas de reproducción + recomendaciones; el Mobile-BFF devuelve datos mínimos.
3. **Spotify:** El Mobile-BFF usa caché agresivo y tamaños de imagen optimizados.

## Lista de verificación (auto-evaluación)
- [ ] Puedo explicar cuándo se necesita un BFF vs cuándo es suficiente un API Gateway.
- [ ] Entiendo el modelo de propiedad (el equipo de frontend es dueño del BFF).
- [ ] Puedo diseñar lógica de agregación sin poner reglas de negocio en el BFF.
- [ ] Sé cómo manejar fallos parciales (circuit breaker, degradación elegante).
- [ ] Puedo monitorear la salud del BFF (latencia, tasa de aciertos de caché, tasa de error).

## Recordatorios / Conclusiones clave
- El BFF es una **capa delgada de agregación**, no un servicio de dominio.
- Un BFF por tipo de cliente: web-BFF, mobile-BFF, partner-BFF.
- Siempre combinar con [Timeout](../operations/timeouts.md), [Circuit Breaker](../operations/circuit-breaker.md) y [Degradación Elegante](../operations/graceful-degradation.md).
- Usar [API Gateway](../system-design/api-gateway.md) para enrutar tráfico a los BFFs.

## Preguntas trampa (con respuestas)
### P1: ¿Puedo poner lógica de negocio en un BFF?
**R:** **No**. Los BFFs solo deben **agregar, transformar y optimizar** datos de los servicios downstream. La lógica de negocio (validación, cálculos, reglas de dominio) pertenece a los servicios de dominio. Violar esto crea acoplamiento fuerte y duplica lógica entre BFFs.

### P2: ¿Cuál es la diferencia entre BFF y API Gateway?
**R:** El **API Gateway** es infraestructura: enrutamiento, auth, limitación de tasa, terminación TLS para **todos los clientes**. El **BFF** es un servicio: agregación y transformación específica por cliente. Usas ambos: el Gateway enruta a los BFFs (`/web/*` → web-bff, `/mobile/*` → mobile-bff). Ver [API Gateway](../system-design/api-gateway.md).

### P3: ¿Debería crear un BFF para cada página/pantalla individual?
**R:** **No**. Crea BFFs por **tipo de cliente** (web, móvil, socios), no por página. Dentro de un BFF, diferentes endpoints manejan diferentes pantallas. Crear demasiados BFFs aumenta la sobrecarga operacional sin beneficio significativo.

### P4: ¿Los BFFs pueden compartir código?
**R:** **Sí, pero con cuidado**. Extraer lógica compartida en bibliotecas (clientes HTTP, helpers de auth, mapeadores de datos). Sin embargo, no fuerces lógica de agregación compartida; los BFFs existen precisamente para divergir según las necesidades del cliente. Balance: infraestructura compartida, transformaciones de negocio independientes.

### P5: ¿Cómo evito que los BFFs se conviertan en cuellos de botella?
**R:**
1. Usar **llamadas paralelas** a servicios downstream (no secuenciales).
2. Implementar **caché** (Redis, CDN) para agregaciones costosas.
3. Añadir [Circuit Breaker](../operations/circuit-breaker.md) y [Timeout](../operations/timeouts.md) para fallar rápido.
4. Escalar los BFFs horizontalmente (diseño sin estado).
5. Monitorear latencia y optimizar endpoints lentos. Ver [Fundamentos de rendimiento](../system-design/performance-basics.md).
