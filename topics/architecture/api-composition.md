---
id: api-composition
title: "Composición de APIs"
type: pattern
status: learning
importance: 65
difficulty: medium
tags: [architecture, api-design, microservices, aggregation]
canonical: true
aliases: [api aggregation, data aggregation]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Composición de APIs

## TL;DR (BLUF)
- Agrega datos de múltiples microservicios en una sola respuesta.
- Úsalo para evitar "clientes parlanchines" y reducir viajes de ida y vuelta.
- Trade-off: latencia y complejidad añadidas; riesgo de acoplar servicios.

## Definición
**Qué es:** Un patrón donde un servicio (típicamente un [BFF](backend-for-frontend.md) o capa de API) llama a múltiples servicios downstream en paralelo o secuencialmente y combina sus respuestas en un solo payload para el cliente.

**Términos clave:** agregación, composición, ensamblaje de datos, llamadas paralelas, respuestas parciales, cliente parlanchín.

## Por qué importa
- Reduce la complejidad del cliente: una solicitud en lugar de N.
- Disminuye la latencia para los clientes (especialmente móviles en redes lentas).
- Mejora la experiencia de usuario para pantallas "compuestas" (detalles de pedido, dashboards, feeds).
- Centraliza el manejo de errores y reintentos.

## Alcance y no-objetivos
**Dentro del alcance:** agregar datos de múltiples servicios, llamadas paralelas, manejo de errores para respuestas parciales.

**Fuera del alcance / NO resuelto por esto:**
- Transacciones distribuidas (usar [Saga](saga-pattern.md))
- Consistencia fuerte entre servicios (solo consistencia eventual)
- Lógica de negocio (la agregación es ensamblaje de datos, no lógica de dominio)

## Modelo mental / Intuición
- Como armar una comida de diferentes puestos en un patio de comidas: coordinas pedidos de múltiples vendedores y entregas una bandeja.
- En APIs: `/order/123` necesita datos de order-service, user-service, payment-service e inventory-service.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Una pantalla del cliente necesita datos de 3+ servicios (detalles de pedido, perfil de usuario, recomendaciones).
- Los clientes móviles o web son "parlanchines" (muchos viajes de ida y vuelta).
- Controlas todos los servicios downstream (misma organización).
- La lógica de agregación es simple (sin joins complejos ni transformaciones).

### Evítalo cuando
- Los servicios downstream son propiedad de equipos externos y pueden cambiar independientemente.
- La agregación requiere lógica de negocio compleja (usar un servicio dedicado en su lugar).
- Un servicio lento bloquea toda la respuesta (considerar [Degradación Elegante](../operations/graceful-degradation.md)).
- Los clientes pueden obtener datos eficientemente por sí mismos (GraphQL, caché local).

## Cómo lo usaría (práctico)
- **Contexto:** Página de "Detalles del Pedido" de comercio electrónico necesita: info del pedido, perfil de usuario, estado de pago, rastreo de envío.
- **Pasos:**
  1) Implementar en [BFF](backend-for-frontend.md) (web-bff o mobile-bff).
  2) Llamar a los servicios **en paralelo** usando I/O asíncrono (Go: goroutines; Node: Promises; Java: CompletableFuture).
     ```go
     var wg sync.WaitGroup
     var order Order
     var user User
     var payment Payment
     wg.Add(3)
     go func() { order = fetchOrder(ctx, orderID); wg.Done() }()
     go func() { user = fetchUser(ctx, userID); wg.Done() }()
     go func() { payment = fetchPayment(ctx, paymentID); wg.Done() }()
     wg.Wait()
     ```
  3) Establecer [Timeouts](../operations/timeouts.md) estrictos (ej., 500ms por servicio).
  4) Implementar [Degradación Elegante](../operations/graceful-degradation.md): si payment-service falla, devolver pedido + usuario (omitir pago).
  5) Añadir [Circuit Breaker](../operations/circuit-breaker.md) por servicio downstream.
  6) Monitorear: latencia de agregación p95/p99, tasa de respuestas parciales, tasas de error downstream.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Latencia = servicio más lento (si es secuencial) | Usar **llamadas paralelas**; establecer timeouts agresivos |
| Fallo de un servicio rompe toda la respuesta | Usar [Degradación Elegante](../operations/graceful-degradation.md) (respuestas parciales) |
| Acoplamiento a múltiples contratos de servicio | Versionar APIs cuidadosamente; usar [versionado de API](../system-design/versioning-apis-and-events.md) |
| El agregador se convierte en cuello de botella | Escalar horizontalmente; cachear agregaciones costosas |
| Problema de consulta N+1 (bucles llamando a servicios) | Usar batching o data loaders (estilo GraphQL) |

## Modos de fallo / Casos límite
1. **Timeout en cascada:** El timeout del agregador es muy corto; todas las llamadas downstream fallan.
   - *Mitigación:* Establecer timeout del agregador > max(timeouts downstream) + margen.
2. **Inconsistencia en respuesta parcial:** El usuario ve el pedido pero no el estado de pago.
   - *Mitigación:* Marcar respuestas parciales en el payload; mostrar "cargando..." o fallback.
3. **Estampida al fallar la caché:** Todas las agregaciones disparan llamadas downstream simultáneas.
   - *Mitigación:* Usar coalescencia de solicitudes o caché en memoria a corto plazo.
4. **Servicio A depende de datos del Servicio B:** Las llamadas secuenciales aumentan la latencia.
   - *Mitigación:* Rediseñar para reducir dependencias; usar arquitectura dirigida por eventos.
5. **El agregador se convierte en servicio de dominio:** Los equipos añaden lógica de negocio en lugar de pura agregación.
   - *Mitigación:* Imponer política: la agregación es **solo ensamblaje de datos**.

## Alternativas
- **GraphQL:** Los clientes especifican exactamente qué campos necesitan; el servidor resuelve dependencias.
- **Composición del lado del cliente:** El cliente llama a los servicios directamente (simple pero más parlanchín).
- **[CQRS](cqrs.md) + Modelo de lectura:** Pre-agregar datos en un almacén optimizado para lectura.
- **Vistas materializadas dirigidas por eventos:** Los servicios downstream publican eventos; el agregador mantiene una vista local.

## Combinaciones
La Composición de APIs **casi siempre se usa con:**
- **[BFF (Backend for Frontend)](backend-for-frontend.md):** Los BFFs son el lugar natural para la composición.
- **[Timeout](../operations/timeouts.md):** Evitar que servicios lentos bloqueen la agregación.
- **[Circuit Breaker](../operations/circuit-breaker.md):** Fallar rápido cuando el downstream no está saludable.
- **[Degradación Elegante](../operations/graceful-degradation.md):** Devolver datos parciales si servicios no críticos fallan.
- **[Observabilidad](../operations/observability-basics.md):** Rastrear latencia downstream, tasas de error, tasas de respuestas parciales.

**Combinación típica:**
- **Escenario de UI compuesta:** BFF + Composición de APIs + Timeout + Circuit Breaker + Degradación Elegante + Observabilidad

## Prerequisitos
- Comprensión de [Diseño de Microservicios](microservices-design-basics.md).
- Familiaridad con I/O asíncrono y ejecución paralela.
- Conocimiento de [Timeouts](../operations/timeouts.md) y [Circuit Breaker](../operations/circuit-breaker.md).

## Temas relacionados
- [BFF (Backend for Frontend)](backend-for-frontend.md): Donde típicamente ocurre la composición.
- [Degradación Elegante](../operations/graceful-degradation.md): Manejar fallos parciales.
- [CQRS](cqrs.md): Pre-agregar datos en modelos de lectura.
- [GraphQL](../system-design/api-design-basics.md): Alternativa a la composición explícita.
- [Timeout](../operations/timeouts.md): Crítico para la agregación.

## Ejemplos del mundo real
1. **Netflix:** Los BFFs agregan perfil de usuario + historial de visualización + recomendaciones.
2. **Página de producto de Amazon:** Agrega detalles del producto + reseñas + inventario + recomendaciones.
3. **Detalles de viaje de Uber:** Agrega info del viaje + perfil del conductor + pago + ruta.

## Lista de verificación (auto-evaluación)
- [ ] Puedo identificar cuándo usar composición de APIs vs obtención del lado del cliente.
- [ ] Sé cómo implementar llamadas paralelas para reducir la latencia.
- [ ] Puedo diseñar manejo de respuestas parciales (degradación elegante).
- [ ] Entiendo el problema de consulta N+1 y cómo evitarlo.
- [ ] Puedo monitorear la salud de la agregación (latencia, tasas de error, respuestas parciales).

## Recordatorios / Conclusiones clave
- Siempre usar **llamadas paralelas** para minimizar la latencia.
- Establecer [Timeouts](../operations/timeouts.md) estrictos por servicio downstream.
- Usar [Degradación Elegante](../operations/graceful-degradation.md): las respuestas parciales son mejores que un fallo total.
- La Composición de APIs es **ensamblaje de datos**, no lógica de negocio.

## Preguntas trampa (con respuestas)
### P1: ¿Debería usar composición de APIs o GraphQL?
**R:** **Depende del control y la complejidad**. Usa **composición de APIs** cuando controlas todos los servicios y la agregación es simple (consultas fijas). Usa **GraphQL** cuando los clientes necesitan consultas flexibles o cuando la lógica de agregación es compleja. GraphQL añade sobrecarga (parseo de consultas, lógica de resolvers) pero da más poder a los clientes. Para BFFs internos, la composición es más simple.

### P2: ¿Qué pasa si un servicio es lento? ¿Debería esperarlo?
**R:** **No, usa timeouts y degradación elegante**. Establece un [Timeout](../operations/timeouts.md) estricto (ej., 500ms). Si un servicio no crítico excede el timeout, devuelve datos parciales con un flag (`"payment": null, "paymentUnavailable": true`). Si los servicios críticos (ej., detalles del pedido) fallan, devolver 503. Ver [Degradación Elegante](../operations/graceful-degradation.md).

### P3: ¿Cómo evito hacer consultas N+1 en la agregación?
**R:** **Usa batching o data loaders**. En lugar de:
```
for user in users:
    fetch orders(user.id)  // N calls
```
Haz:
```
userIds = [u.id for u in users]
orders = fetchOrdersBatch(userIds)  // 1 call
```
O usa data loaders estilo GraphQL que agrupan y deduplicar solicitudes dentro de un solo contexto de solicitud.

### P4: ¿La composición de APIs funciona para operaciones de escritura (POST, PUT)?
**R:** **Generalmente no**. La composición es para lecturas. Para escrituras entre múltiples servicios, usa el [patrón Saga](saga-pattern.md) (transacciones orquestadas o coreografiadas con compensaciones). La agregación asume **solo lectura** y **consistencia eventual**.

### P5: ¿Debería cachear las respuestas agregadas?
**R:** **Sí, si los datos cambian con poca frecuencia**. Cachear en la capa de agregación (Redis, en memoria) con TTLs apropiados. Ten cuidado con datos específicos del usuario (privacidad) y asegúrate de que la invalidación de caché funcione. Para datos que cambian frecuentemente (precios de acciones, marcadores en vivo), usa TTLs cortos u omite el caché. Ver [Estrategia de caché](../system-design/caching-strategy.md).
