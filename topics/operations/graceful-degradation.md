---
id: graceful-degradation
title: "Graceful Degradation"
type: pattern
status: learning
importance: 75
difficulty: medium
tags: [operations, reliability, resilience, user-experience]
canonical: true
aliases: [partial failure handling, fallback pattern]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Degradación Elegante

## TL;DR (BLUF)
- Devolver respuestas parciales o cacheadas cuando servicios no críticos fallan en vez de fallo completo.
- Úsala para mejorar la disponibilidad y la experiencia de usuario durante caídas parciales.
- Trade-off: los usuarios obtienen datos incompletos; agrega complejidad para decidir qué es "crítico".

## Definición
**Qué es:** Un patrón de resiliencia donde un sistema continúa funcionando con capacidad limitada cuando las dependencias fallan, devolviendo datos cacheados, omitiendo funcionalidades no críticas o usando valores de respaldo.

**Términos clave:** respuesta parcial, fallback, modo degradado, caché obsoleto, feature toggles, consistencia eventual, disponibilidad sobre completitud.

## Por qué importa
- Mejora la disponibilidad percibida: "algo funciona" es mejor que "nada funciona".
- Reduce el radio de explosión de fallos downstream.
- Mejora la experiencia de usuario durante incidentes (mostrar historial de pedidos aunque las recomendaciones fallen).
- Complementa [Circuit Breaker](circuit-breaker.md) y [Timeout](timeouts.md).

## Alcance y no-objetivos
**Dentro del alcance:** respuestas parciales, lógica de fallback, datos cacheados, feature flags, valores estáticos por defecto.

**Fuera del alcance / NO resuelto por esto:**
- Prevenir fallos (usar [Circuit Breaker](circuit-breaker.md), [Reintentos](retries-and-backoff.md))
- Consistencia fuerte (solo consistencia eventual)
- Transacciones de negocio (usar [Saga](../architecture/saga-pattern.md))

## Modelo mental / Intuición
- Como un restaurante: si no hay postre, igual sirve el plato principal (no cierres el restaurante).
- En APIs: si el servicio de recomendaciones está caído, muestra la página del producto sin recomendaciones.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los fallos en funcionalidades no críticas no deban bloquear rutas críticas (el checkout puede funcionar sin recomendaciones).
- Los datos cacheados/obsoletos sean aceptables para algunos casos de uso (feed de noticias, catálogos de productos).
- La experiencia de usuario se beneficie de funcionalidad parcial (mostrar 80% de los datos vs página de error).
- Las integraciones con terceros sean poco fiables.

### Evítalo cuando
- Todas las dependencias sean críticas (procesamiento de pagos, autenticación).
- Los datos obsoletos sean peligrosos (datos financieros, precios en tiempo real).
- Los usuarios esperen respuestas completas y consistentes (banca, salud).
- La complejidad de la lógica de fallback supere los beneficios.

## Cómo lo usaría (práctico)
- **Contexto:** Página de producto de e-commerce necesita: detalles del producto (crítico), reseñas (importante), recomendaciones (nice-to-have).
- **Pasos:**
  1) Clasificar dependencias:
     - **Crítico:** product-service (sin él, no hay página).
     - **Importante:** review-service (degradar: mostrar "Reseñas no disponibles").
     - **Opcional:** recommendation-service (omitir silenciosamente).
  2) Implementar con [Timeout](timeouts.md) + fallback:
     ```go
     product := fetchProduct(ctx, productID) // Crítico: fallar si esto falla
     
     reviews, err := fetchReviews(ctx, productID)
     if err != nil {
         reviews = cachedReviews(productID) // Fallback: usar caché
     }
     
     recommendations, err := fetchRecommendations(ctx, productID)
     if err != nil {
         recommendations = nil // Degradar: omitir recomendaciones
     }
     
     return ProductPage{
         Product: product,
         Reviews: reviews,
         Recommendations: recommendations,
         Degraded: recommendations == nil, // Señal para la UI
     }
     ```
  3) La UI maneja el flag `Degraded`: ocultar sección de recomendaciones o mostrar "Recomendaciones temporalmente no disponibles".
  4) Cachear agresivamente datos no críticos (TTL: 1-24 horas).
  5) Monitorear: frecuencia de degradación, tasa de aciertos de caché, impacto al usuario.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Los usuarios obtienen datos incompletos | Comunicar claramente la degradación en la UI ("Algunas funcionalidades no disponibles") |
| El caché obsoleto puede confundir usuarios | Establecer TTLs apropiados; mostrar timestamp de "última actualización" |
| Complejidad decidiendo qué es crítico | Documentar niveles de criticidad; usar feature flags para experimentación |
| Fallos silenciosos (logs ignorados) | Alertar sobre tasa de degradación; rastrear impacto en SLO |
| Preocupaciones de privacidad de datos cacheados | Respetar permisos de usuario; cifrar datos cacheados |

## Modos de fallo / Casos límite
1. **Sobre-degradación:** Demasiadas funcionalidades degradadas; experiencia de usuario inaceptable.
   - *Mitigación:* Definir respuesta mínima viable; fallar rápido si hay demasiada degradación.
2. **Envenenamiento de caché obsoleto:** Datos malos cacheados servidos por horas.
   - *Mitigación:* Validar datos cacheados antes de servir; TTLs cortos para datos críticos.
3. **Cascada de degradaciones:** Múltiples servicios se degradan simultáneamente.
   - *Mitigación:* Monitorear degradación general; alertar cuando múltiples servicios estén afectados.
4. **Usuarios no conscientes de la degradación:** Omisiones silenciosas confunden a los usuarios.
   - *Mitigación:* Mostrar banners ("Algunas funcionalidades limitadas por mantenimiento").
5. **Desvío de feature flags:** El modo de degradación permanece habilitado después del incidente.
   - *Mitigación:* Automatizar expiración de feature flags; revisión post-incidente.

## Alternativas
- **Fallar rápido (503):** Devolver error inmediatamente; más simple pero peor UX.
- **[Circuit Breaker](circuit-breaker.md):** Previene fallos en cascada; complementa la degradación.
- **[Reintentos](retries-and-backoff.md):** Intentar recuperación; usar antes de la degradación.
- **Procesamiento basado en colas:** Aceptar petición, procesar después (asíncrono).

## Patrones de implementación
### 1. Fallback estático
- Devolver valor por defecto hardcodeado: lista vacía, imagen placeholder, configuración por defecto.
- **Uso:** funcionalidades no críticas (recomendaciones, anuncios).

### 2. Respuesta cacheada
- Servir datos obsoletos del caché (Redis, CDN).
- **Uso:** contenido que cambia infrecuentemente (catálogos de productos, noticias).

### 3. Respuesta parcial
- Devolver subconjunto de datos (omitir secciones fallidas).
- **Uso:** páginas compuestas (dashboard, vistas agregadas).

### 4. Feature flag
- Deshabilitar funcionalidad completamente durante incidentes.
- **Uso:** funcionalidades experimentales, funcionalidad no esencial.

### 5. Notificación al usuario
- Mostrar banner: "Búsqueda temporalmente no disponible".
- **Uso:** cuando la degradación impacta significativamente la UX.

## Combinaciones
La Degradación Elegante **casi siempre se usa con:**
- **[Timeout](timeouts.md):** Detectar fallo rápidamente.
- **[Circuit Breaker](circuit-breaker.md):** Fallar rápido cuando la dependencia está enferma.
- **[Caché](../system-design/caching-fundamentals.md):** Servir datos obsoletos como fallback.
- **[Observabilidad](observability-basics.md):** Rastrear tasa de degradación, impacto en SLO.
- **[Composición de API](../architecture/api-composition.md):** Manejar fallos parciales en agregación.

**Combinación típica:**
- **Escenario de servicio resiliente:** Timeout + Circuit Breaker + Degradación Elegante + Caché + Observabilidad

## Prerrequisitos
- Comprensión de [Fundamentos de caché](../system-design/caching-fundamentals.md).
- Familiaridad con [Circuit Breaker](circuit-breaker.md) y [Timeout](timeouts.md).
- Conocimiento de [Observabilidad](observability-basics.md) (monitorear degradación).

## Temas relacionados
- [Circuit Breaker](circuit-breaker.md): Falla rápido cuando la dependencia está caída.
- [Timeout](timeouts.md): Detecta dependencias lentas.
- [Estrategia de caché](../system-design/caching-strategy.md): Fuente de datos de respaldo.
- [Composición de API](../architecture/api-composition.md): Agregar con fallos parciales.
- [Observabilidad](observability-basics.md): Monitorear métricas de degradación.

## Ejemplos del mundo real
1. **Netflix:** Si el servicio de personalización falla, muestra recomendaciones genéricas.
2. **Amazon:** La página de producto carga sin reseñas si el servicio de reseñas está caído.
3. **Twitter:** El timeline carga incluso si los temas trending fallan.
4. **Spotify:** La app funciona offline con playlists cacheadas (degradación extrema).

## Checklist (auto-evaluación)
- [ ] Puedo clasificar dependencias como críticas/importantes/opcionales.
- [ ] Sé cómo implementar lógica de fallback (caché, valores estáticos por defecto, omisión).
- [ ] Entiendo cuándo mostrar datos parciales vs fallar completamente.
- [ ] Puedo monitorear frecuencia de degradación e impacto al usuario.
- [ ] Puedo comunicar la degradación claramente a los usuarios.

## Recordatorios / Puntos clave
- **Disponibilidad > Completitud** para funcionalidades no críticas.
- Siempre clasificar dependencias: crítica, importante, opcional.
- Cachear agresivamente datos no críticos.
- Comunicar claramente la degradación a los usuarios (banners de UI, flags de respuesta).
- Monitorear tasa de degradación e impacto en [SLO](service-level-objective.md).

## Preguntas trampa (con respuestas)
### P1: ¿Debo siempre degradar en vez de devolver un error?
**R:** **No**. Solo degradar **funcionalidades no críticas**. Para rutas críticas (auth, pago, creación de orden), **fallar rápido con error claro**. La degradación es para mejorar la UX cuando la funcionalidad parcial es aceptable. No degradar sistemas de seguridad crítica (salud, aviación).

### P2: ¿Cuánto tiempo puedo servir datos cacheados obsoletos?
**R:** **Depende del caso de uso**. Para catálogos de productos, 1-24 horas está bien. Para precios de acciones, 1 minuto puede ser demasiado. Establecer TTL basado en **sensibilidad de datos** y **expectativas del usuario**. Siempre mostrar timestamp de "última actualización" para datos obsoletos.

### P3: ¿Cuál es la diferencia entre degradación elegante y circuit breaker?
**R:** **[Circuit Breaker](circuit-breaker.md)** **detecta el fallo** y falla rápido para prevenir cascadas. **La Degradación Elegante** **maneja el fallo** devolviendo datos parciales/cacheados. Usar ambos: circuit breaker se abre → degradación elegante devuelve fallback. Circuit breaker es un **detector de fallos**; degradación es un **manejador de fallos**.

### P4: ¿Debo alertar en cada degradación?
**R:** **No, pero rastrear métricas**. Alertar cuando:
1. La tasa de degradación exceda el umbral (ej., >10% de peticiones).
2. Servicios críticos se degraden (no solo funcionalidades opcionales).
3. Múltiples servicios se degraden simultáneamente.
Rastrear todas las degradaciones en dashboards para análisis post-incidente. Ver [Observabilidad](observability-basics.md).

### P5: ¿Puedo usar degradación elegante para operaciones de escritura (POST, PUT)?
**R:** **Raramente**. La degradación es principalmente para **lecturas**. Para escrituras, generalmente no puedes "parcialmente" crear una orden o "cachear" un pago. En su lugar:
- Encolar escritura para procesamiento posterior (asíncrono).
- Devolver 503 con header `Retry-After`.
- Usar [patrón Saga](../architecture/saga-pattern.md) para transacciones distribuidas.
Excepción: Escrituras no críticas (analítica, logs) pueden descartarse durante incidentes.
