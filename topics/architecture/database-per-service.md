---
id: database-per-service
title: "Base de Datos por Servicio"
type: pattern
status: learning
importance: 75
difficulty: hard
tags: [architecture, microservices, data-management, decoupling]
canonical: true
aliases: [private database, service-owned data]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Base de Datos por Servicio

## TL;DR (BLUF)
- Cada microservicio es dueño exclusivo de su base de datos/esquema; sin bases de datos compartidas.
- Úsalo para lograr verdadera autonomía de servicio y despliegues independientes.
- Trade-off: consistencia de datos distribuida, consultas entre servicios y duplicación de datos.

## Definición
**Qué es:** Un patrón arquitectónico donde cada microservicio tiene propiedad exclusiva de su base de datos (o esquema), y otros servicios solo pueden acceder a esos datos vía la API del servicio, nunca directamente.

**Términos clave:** autonomía de servicio, propiedad de datos, contexto acotado, persistencia políglota, sin base de datos compartida, consistencia eventual.

## Por qué importa
- Permite verdadera independencia de servicio: desplegar, escalar y evolucionar servicios por separado.
- Previene acoplamiento fuerte vía base de datos compartida (anti-patrón común de microservicios).
- Permite persistencia políglota: cada servicio elige la base de datos óptima (Postgres, Mongo, Redis).
- Refuerza las fronteras de contexto acotado ([Diseño Orientado al Dominio](domain-driven-design.md)).

## Alcance y no-objetivos
**Dentro del alcance:** propiedad de datos del servicio, acceso a datos vía API, consistencia eventual, patrones de sincronización de datos.

**Fuera del alcance / NO resuelto por esto:**
- Consistencia fuerte entre servicios (usar [ACID](../databases/acid-properties.md) dentro de un servicio)
- Joins entre servicios (usar [Composición de APIs](api-composition.md) o [CQRS](cqrs.md))
- Transacciones distribuidas (usar [Saga](saga-pattern.md))

## Modelo mental / Intuición
- Como empresas separadas: cada una tiene su propio sistema contable; intercambian facturas vía contratos, no acceso directo a la BD.
- En microservicios: `user-service` es dueño de la tabla `users`; `order-service` no puede consultarla directamente — debe llamar a la API `/users`.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Construyes microservicios con verdadera autonomía (desplegar independientemente).
- Los servicios tienen contextos acotados distintos (usuarios, pedidos, pagos).
- Los equipos son dueños de la responsabilidad de extremo a extremo (desarrollo, despliegue, esquema de BD).
- Necesitas persistencia políglota (mezclar Postgres, Mongo, DynamoDB).

### Evítalo cuando
- Construyes un monolito modular (una BD compartida está bien).
- La consistencia fuerte es crítica en todos los datos (banca, inventario).
- El equipo carece de experiencia en sistemas distribuidos.
- La sobrecarga operacional de gestionar N bases de datos es demasiado alta.

## Cómo lo usaría (práctico)
- **Contexto:** Plataforma de comercio electrónico con `user-service`, `order-service` y `payment-service`.
- **Pasos:**
  1) **Asignar propiedad de base de datos:**
     - `user-service` → `users_db` (Postgres)
     - `order-service` → `orders_db` (Postgres)
     - `payment-service` → `payments_db` (DynamoDB)
  2) **Imponer acceso solo vía API:**
     - `order-service` necesita info de usuario → llama a `GET /users/{id}` API (no consulta directa a BD).
     - Las credenciales de `users_db` son **privadas** del `user-service`.
  3) **Manejar duplicación de datos:**
     - `order-service` cachea `userName` localmente (consistencia eventual).
     - Cuando el usuario actualiza su nombre, `user-service` publica evento `UserUpdated`.
     - `order-service` se suscribe y actualiza la caché local.
  4) **Usar Saga para transacciones distribuidas:**
     - Flujo de checkout: `order-service` → `payment-service` → `inventory-service`.
     - Usar [patrón Saga](saga-pattern.md) con compensaciones.
  5) **Monitorear consistencia de datos:**
     - Rastrear retraso de sincronización entre servicios.
     - Alertar sobre datos obsoletos (si `order.userName` ≠ `user.name` por >1 hora).

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Sin joins entre servicios (SQL) | Usar [Composición de APIs](api-composition.md) o modelos de lectura [CQRS](cqrs.md) |
| Duplicación de datos (consistencia eventual) | Aceptar obsolescencia; usar eventos para sincronización; monitorear retraso |
| Complejidad de transacciones distribuidas | Usar [patrón Saga](saga-pattern.md); evitar cuando sea posible |
| Sobrecarga operacional (N bases de datos) | Usar bases de datos gestionadas (AWS RDS, Cloud SQL); automatizar aprovisionamiento |
| Migración de datos más difícil | Planificar cambios de esquema por servicio; usar versionado |

## Modos de fallo / Casos límite
1. **Un servicio consulta la BD de otro servicio directamente:** Viola la encapsulación.
   - *Mitigación:* Imponer políticas de red (firewall de BD); revisiones de código.
2. **Explosión de consultas entre servicios:** "Detalles de pedido" necesita 10 llamadas API.
   - *Mitigación:* Usar [BFF](backend-for-frontend.md) o modelo de lectura [CQRS](cqrs.md).
3. **Deriva de consistencia de datos:** `order.userName` obsoleto después de actualización de usuario.
   - *Mitigación:* Usar [patrón Outbox](outbox-pattern.md) para eventos confiables; monitorear obsolescencia.
4. **Bloqueo de migración de esquema:** El Servicio A depende del esquema del Servicio B.
   - *Mitigación:* Usar [versionado de API](../system-design/versioning-apis-and-events.md); compatibilidad hacia atrás.
5. **Pesadilla de reportes/analítica:** Necesidad de juntar datos de 10 servicios.
   - *Mitigación:* Usar [Change Data Capture](../databases/change-data-capture.md) para replicar al data warehouse.

## Alternativas
- **Base de datos compartida (monolito):** Simple pero acopla servicios fuertemente.
- **Esquema compartido por contexto acotado:** Múltiples servicios comparten BD pero diferentes esquemas (híbrido).
- **Capa de Base-de-Datos-como-Servicio:** Un servicio central de datos es dueño de la BD, otros lo llaman (centralizado).
- **Event sourcing + CQRS:** Almacenar eventos centralmente, construir modelos de lectura por servicio.

## Patrones de acceso a datos
### 1. Llamadas API (síncronas)
- El Servicio A llama a la API REST/gRPC del Servicio B.
- **Ventajas:** Simple, fuertemente tipado.
- **Desventajas:** Latencia, fallos en cascada.

### 2. Dirigido por eventos (asíncrono)
- El Servicio A publica un evento; el Servicio B se suscribe y actualiza su caché local.
- **Ventajas:** Desacoplado, resiliente.
- **Desventajas:** Consistencia eventual, complejidad.

### 3. Modelo de lectura CQRS
- Base de datos de lectura dedicada que agrega datos de múltiples servicios.
- **Ventajas:** Consultas rápidas, sin joins en tiempo de ejecución.
- **Desventajas:** Consistencia eventual, problemas de escritura dual.

### 4. Data warehouse / ETL
- Replicar datos por lotes al warehouse para analítica.
- **Ventajas:** Optimizado para reportes.
- **Desventajas:** No para consultas operacionales; obsolescencia.

**Recomendación:** Usar **llamadas API** para tiempo real, **eventos** para sincronización asíncrona, **CQRS** para consultas complejas, **warehouse** para analítica.

## Combinaciones
Base de Datos por Servicio **casi siempre se usa con:**
- **[Patrón Saga](saga-pattern.md):** Transacciones distribuidas entre servicios.
- **[Patrón Outbox](outbox-pattern.md):** Publicación confiable de eventos.
- **[CQRS](cqrs.md):** Modelos de lectura separados para consultas.
- **[Composición de APIs](api-composition.md):** Agregar datos en tiempo de ejecución.
- **[Contextos Acotados](bounded-contexts.md):** Base DDD para fronteras de servicio.
- **[Observabilidad](../operations/observability-basics.md):** Rastrear consistencia de datos, retraso de sincronización.

**Combinación típica:**
- **Arquitectura de microservicios:** Base de Datos por Servicio + Saga + Outbox + CQRS (si es necesario) + Observabilidad

## Prerequisitos
- Comprensión de [Diseño de Microservicios](microservices-design-basics.md).
- Familiaridad con [Diseño Orientado al Dominio](domain-driven-design.md) y [Contextos Acotados](bounded-contexts.md).
- Conocimiento de [Arquitectura dirigida por eventos](event-driven-basics.md) y [Saga](saga-pattern.md).

## Temas relacionados
- [Patrón Saga](saga-pattern.md): Transacciones distribuidas.
- [Patrón Outbox](outbox-pattern.md): Publicación confiable de eventos.
- [CQRS](cqrs.md): Modelos de lectura separados.
- [Composición de APIs](api-composition.md): Agregar datos vía APIs.
- [Contextos Acotados](bounded-contexts.md): Definir fronteras de servicio.
- [Change Data Capture](../databases/change-data-capture.md): Replicar datos para analítica.

## Ejemplos del mundo real
1. **Netflix:** Cada microservicio es dueño de su keyspace de Cassandra o base de datos.
2. **Amazon:** Los servicios usan diferentes bases de datos (DynamoDB, Aurora, S3) según necesidades.
3. **Uber:** Bases de datos separadas para pasajeros, conductores, viajes, pagos.

## Lista de verificación (auto-evaluación)
- [ ] Entiendo la diferencia entre base de datos por servicio y base de datos compartida.
- [ ] Puedo diseñar contratos de API para acceso a datos entre servicios.
- [ ] Sé cómo manejar duplicación de datos y consistencia eventual.
- [ ] Puedo explicar cuándo usar Saga vs CQRS para consultas entre servicios.
- [ ] Puedo monitorear la consistencia de datos y el retraso de sincronización.

## Recordatorios / Conclusiones clave
- **Un servicio, una base de datos** (o esquema).
- **Sin acceso directo a BD** entre servicios (imponer con políticas de red).
- Aceptar **consistencia eventual** para datos entre servicios.
- Usar [Saga](saga-pattern.md) para transacciones distribuidas.
- Usar [CQRS](cqrs.md) para consultas complejas entre servicios.

## Preguntas trampa (con respuestas)
### P1: ¿Pueden dos servicios compartir la misma instancia de base de datos pero diferentes esquemas?
**R:** **Técnicamente sí, pero arriesgado**. Compartir una instancia de BD (ej., servidor Postgres) está bien para ahorro de costos, pero cada servicio debe tener su **propio esquema** sin **consultas entre esquemas**. Mejor: usar bases de datos completamente separadas para imponer aislamiento y permitir persistencia políglota. La instancia compartida aún puede crear acoplamiento (límites de recursos compartidos, conflictos de migración de esquema).

### P2: ¿Cómo consulto datos de múltiples servicios (ej., pedido + usuario + pago)?
**R:** **Opciones:**
1. **[Composición de APIs](api-composition.md):** Llamar a múltiples servicios, agregar en [BFF](backend-for-frontend.md) (sobrecarga en tiempo de ejecución).
2. **[CQRS](cqrs.md):** Construir vista de solo lectura que se suscriba a eventos de todos los servicios (consistencia eventual).
3. **Data warehouse:** Enviar datos por ETL al warehouse para reportes (no para consultas operacionales).
Elegir según latencia, consistencia y complejidad de la consulta. Para UI, usar Composición de APIs o CQRS.

### P3: ¿Qué pasa si necesito consistencia fuerte entre servicios?
**R:** **No se puede** (las transacciones distribuidas son un anti-patrón en microservicios). En su lugar:
1. **Mantener los datos que necesitan consistencia fuerte en un solo servicio** (ej., pedido + ítems del pedido en order-service).
2. Usar [patrón Saga](saga-pattern.md) para **consistencia eventual** con compensaciones.
3. Reconsiderar las fronteras del servicio: quizás los datos pertenecen juntos ([Contextos Acotados](bounded-contexts.md)).
Si realmente necesitas ACID entre servicios, podrías necesitar un monolito o monolito modular.

### P4: ¿Puedo usar una base de datos compartida de solo lectura para reportes?
**R:** **Sí, pero no directamente de las bases de datos de los servicios**. En su lugar:
1. Usar [Change Data Capture](../databases/change-data-capture.md) para replicar datos al **data warehouse**.
2. Las consultas de analítica se ejecutan en el warehouse, no en las BDs de producción.
3. Evitar: servicios consultando directamente las BDs de otros (rompe la encapsulación).
Los reportes son un caso de uso válido para replicación de datos, no para romper la regla de base de datos por servicio.

### P5: ¿Cómo migro de base de datos compartida a base de datos por servicio?
**R:** Usar **[Strangler Fig](strangler-fig.md)**:
1. Identificar contextos acotados (futuros servicios).
2. Extraer un servicio a la vez.
3. Migrar datos incrementalmente (escritura dual, CDC, migración por lotes).
4. Refactorizar consultas entre servicios a llamadas API o eventos.
5. Eventualmente descomisionar la base de datos compartida.
NO intentar "una gran división" — demasiado arriesgado. Migración incremental con plan de rollback claro. Ver [Strangler Fig](strangler-fig.md).
