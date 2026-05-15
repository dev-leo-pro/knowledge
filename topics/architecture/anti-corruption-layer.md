---
id: anti-corruption-layer
title: "Capa Anti-Corrupción (ACL)"
type: pattern
status: learning
importance: 70
difficulty: medium
tags: [architecture, domain-driven-design, integration, legacy]
canonical: true
aliases: [acl, adapter layer, translation layer]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Capa Anti-Corrupción (ACL)

## TL;DR (BLUF)
- Una capa de traducción que protege tu modelo de dominio de sistemas externos/heredados con modelos incompatibles.
- Úsala cuando integres con terceros, sistemas heredados o APIs mal diseñadas.
- Trade-off: complejidad añadida y sobrecarga de traducción.

## Definición
**Qué es:** Una capa (adaptador, fachada, traductor) que se sitúa entre tu modelo de dominio limpio y un sistema externo, traduciendo entre modelos y evitando que conceptos externos "se filtren" a tu dominio central.

**Términos clave:** adaptador, fachada, traducción, protección del dominio, frontera, capa de integración, desajuste de impedancia.

## Por qué importa
- Preserva la integridad del dominio: los modelos externos no contaminan tu núcleo.
- Permite la migración gradual desde sistemas heredados.
- Reduce el acoplamiento con APIs de terceros (si la API cambia, solo cambia el ACL).
- Refuerza el lenguaje ubicuo dentro de tu contexto acotado (DDD).

## Alcance y no-objetivos
**Dentro del alcance:** traducción de modelos, validación, normalización, manejo de errores, adaptación de protocolo.

**Fuera del alcance / NO resuelto por esto:**
- Lógica de negocio (pertenece a la capa de dominio)
- Persistencia de datos (usar repositorios)
- Funciones del API Gateway (enrutamiento, limitación de tasa) → [API Gateway](../system-design/api-gateway.md)

## Modelo mental / Intuición
- Como un traductor en una reunión de negocios: convierte el idioma/conceptos extranjeros a los tuyos para que tu equipo entienda.
- En sistemas: "Customer" heredado (ID, Name, Addr1, Addr2) → `Customer` del dominio (ID, Name, objeto de valor Address).

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Integres con sistemas heredados con modelos pobres/inconsistentes.
- APIs de terceros usan terminología o estructura diferente.
- El modelo externo es inestable o cambia frecuentemente.
- Quieras aislar el dominio de los detalles de integración.
- Migres de monolito a microservicios ([Strangler Fig](strangler-fig.md)).

### Evítalo cuando
- El sistema externo se alinea bien con tu dominio (raro).
- La integración es trivial (simple paso de datos).
- La sobrecarga de traducción supera los beneficios.
- Eres dueño de ambos sistemas y puedes alinear modelos directamente.

## Cómo lo usaría (práctico)
- **Contexto:** Sistema de comercio electrónico integrándose con un ERP heredado que usa datos planos y desnormalizados.
- **Pasos:**
  1) **Definir modelo de dominio (limpio):**
     ```go
     type Order struct {
         ID       string
         Customer Customer
         Items    []OrderItem
         Address  ShippingAddress // Value object
         Status   OrderStatus     // Enum
     }
     ```
  2) **Modelo del ERP externo (desordenado):**
     ```xml
     <ORDER>
       <ORDER_ID>12345</ORDER_ID>
       <CUST_NAME>John Doe</CUST_NAME>
       <CUST_ID>C001</CUST_ID>
       <SHIP_ADDR1>123 Main St</SHIP_ADDR1>
       <SHIP_ADDR2>Apt 4B</SHIP_ADDR2>
       <SHIP_CITY>NYC</SHIP_CITY>
       <STATUS_CODE>2</STATUS_CODE> <!-- Magic number -->
     </ORDER>
     ```
  3) **ACL (adaptador):**
     ```go
     type ERPAdapter struct {}
     
     func (a *ERPAdapter) FetchOrder(erpOrderID string) (*Order, error) {
         erpOrder := erpClient.GetOrder(erpOrderID) // XML mess
         
         // Translate to domain model
         return &Order{
             ID: erpOrder.OrderID,
             Customer: Customer{
                 ID:   erpOrder.CustomerID,
                 Name: erpOrder.CustomerName,
             },
             Address: ShippingAddress{
                 Street: erpOrder.ShipAddr1 + " " + erpOrder.ShipAddr2,
                 City:   erpOrder.ShipCity,
             },
             Status: translateStatus(erpOrder.StatusCode), // 2 → "Shipped"
         }, nil
     }
     
     func translateStatus(code int) OrderStatus {
         switch code {
         case 1: return OrderStatusPending
         case 2: return OrderStatusShipped
         default: return OrderStatusUnknown
         }
     }
     ```
  4) **La capa de dominio solo ve el modelo limpio `Order`**, nunca toca el XML del ERP.
  5) Si el ERP cambia, solo cambia el ACL (el dominio no se toca).

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Sobrecarga de traducción (CPU, latencia) | Cachear objetos traducidos; usar serialización eficiente |
| Desajuste de impedancia (conceptos no mapean 1:1) | Aceptar pérdida de datos; documentar campos no mapeables |
| El ACL se vuelve complejo | Mantenerlo delgado (solo traducción); extraer lógica de dominio a servicios |
| Duplicación (modelo de dominio + modelo externo) | Costo aceptable por aislamiento del dominio; usar generación de código si es posible |
| Versionado de ambos modelos | Versionar el ACL; usar un adaptador por versión de API externa |

## Modos de fallo / Casos límite
1. **El ACL se convierte en un cajón de sastre:** La lógica de negocio se filtra al ACL.
   - *Mitigación:* Imponer política: el ACL es solo traducción; la lógica queda en el dominio.
2. **Cambios en el modelo externo rompen el ACL:** Campos nuevos, campos eliminados.
   - *Mitigación:* Parseo defensivo (ignorar campos desconocidos); monitorear tests de integración.
3. **La traducción pierde significado semántico:** "status=3" externo poco claro.
   - *Mitigación:* Documentar mapeos; usar valores seguros por defecto (ej., Unknown).
4. **Inconsistencia en traducción bidireccional:** Dominio → Externo → Dominio ≠ original.
   - *Mitigación:* Probar conversiones ida y vuelta; aceptar traducciones con pérdida si es aceptable.
5. **ACL saltado:** Los desarrolladores llaman al sistema externo directamente.
   - *Mitigación:* Encapsular el cliente externo en el ACL; hacerlo privado.

## Alternativas
- **Shared kernel:** Alinear modelos entre sistemas (requiere propiedad).
- **Conformista:** Adoptar el modelo externo tal cual (contamina el dominio).
- **Open Host Service:** Publicar una API bien definida para que otros consuman.
- **Lenguaje publicado:** Estandarizar en un modelo común (JSON Schema, Protocol Buffers).

## Combinaciones
La Capa Anti-Corrupción **casi siempre se usa con:**
- **[Strangler Fig](strangler-fig.md):** Migrar desde sistemas heredados envolviendo con ACL.
- **[Arquitectura Hexagonal](hexagonal-architecture.md):** El ACL es un adaptador (patrón puerto/adaptador).
- **[Diseño Orientado al Dominio](domain-driven-design.md):** Protege el contexto acotado.
- **[Circuit Breaker](../operations/circuit-breaker.md):** Proteger contra sistemas externos inestables.
- **[Timeout](../operations/timeouts.md):** Evitar que llamadas externas lentas bloqueen.

**Combinación típica:**
- **Escenario de integración heredada:** ACL + Strangler Fig + Circuit Breaker + Timeout + Observabilidad

## Prerequisitos
- Comprensión de [Diseño Orientado al Dominio](domain-driven-design.md) y [Contextos Acotados](bounded-contexts.md).
- Familiaridad con [Arquitectura Hexagonal](hexagonal-architecture.md) (puertos/adaptadores).
- Conocimiento de patrones de integración y diseño de APIs.

## Temas relacionados
- [Strangler Fig](strangler-fig.md): Migrar sistemas heredados incrementalmente.
- [Arquitectura Hexagonal](hexagonal-architecture.md): El ACL es un adaptador.
- [Contextos Acotados](bounded-contexts.md): El ACL protege las fronteras de contexto.
- [Diseño Orientado al Dominio](domain-driven-design.md): Base del ACL.
- [Circuit Breaker](../operations/circuit-breaker.md): Resiliencia para llamadas externas.

## Ejemplos del mundo real
1. **Comercio electrónico + ERP heredado:** El ACL traduce el CSV plano del ERP a objetos `Order` del dominio.
2. **SaaS integrando Salesforce:** El ACL adapta el `Account` de Salesforce al `Customer` interno.
3. **Integración de pasarela de pagos:** El ACL normaliza las respuestas de Stripe/PayPal al `Payment` del dominio.

## Lista de verificación (auto-evaluación)
- [ ] Puedo identificar cuándo los modelos externos amenazan la integridad del dominio.
- [ ] Sé cómo diseñar lógica de traducción (parseo defensivo, valores por defecto).
- [ ] Puedo explicar la diferencia entre ACL y un adaptador simple.
- [ ] Entiendo cómo encaja el ACL en la arquitectura hexagonal (capa de adaptadores).
- [ ] Puedo monitorear la salud del ACL (errores de traducción, cambios en la API externa).

## Recordatorios / Conclusiones clave
- El ACL **protege tu dominio** de la contaminación externa.
- Mantén el ACL **delgado**: solo traducción, sin lógica de negocio.
- Úsalo con [Strangler Fig](strangler-fig.md) para migrar sistemas heredados.
- Siempre combinar con [Circuit Breaker](../operations/circuit-breaker.md) y [Timeout](../operations/timeouts.md) para llamadas externas.

## Preguntas trampa (con respuestas)
### P1: ¿Puedo poner lógica de validación en el ACL?
**R:** **Sí, pero solo para datos externos**. El ACL debería validar que los datos externos están bien formados (campos no nulos, enums válidos) antes de la traducción. Sin embargo, la **validación del dominio** (reglas de negocio como "el total del pedido > 0") pertenece a la capa de dominio, no al ACL. El ACL valida la **forma de los datos**, el dominio valida los **invariantes de negocio**.

### P2: ¿Cuál es la diferencia entre ACL y un adaptador simple?
**R:** **El ACL es un tipo específico de adaptador** con un **propósito protector**: evita que modelos externos corrompan tu dominio. Un adaptador genérico podría simplemente traducir por conveniencia. El ACL está motivado por la **integridad del dominio** (concepto de DDD), mientras que un adaptador simple podría simplemente adaptar protocolos (HTTP → gRPC). Si te importan los contextos acotados y el lenguaje ubicuo, necesitas ACL.

### P3: ¿Debería crear un ACL para cada integración externa?
**R:** **No siempre**. Usa ACL cuando:
1. El modelo externo es desordenado/inestable.
2. Los conceptos externos no se alinean con tu dominio.
3. Quieres aislar el dominio de los detalles de integración.

Omite el ACL cuando:
1. La API externa está bien diseñada y se alinea con tu dominio.
2. La integración es trivial (obtener configuración, health checks).
3. La sobrecarga no se justifica (proyecto pequeño, plazo ajustado).

### P4: ¿El ACL puede manejar traducción bidireccional (lectura + escritura)?
**R:** **Sí**. El ACL traduce en ambas direcciones:
- **Entrada:** Externo → Dominio (al leer del sistema externo).
- **Salida:** Dominio → Externo (al escribir al sistema externo).

Ejemplo: `fetchOrder()` traduce ERP → Dominio; `syncOrder()` traduce Dominio → ERP. Ten cuidado con las **traducciones con pérdida**: el viaje ida y vuelta podría no preservar todos los datos.

### P5: ¿Cómo pruebo un ACL?
**R:**
1. **Tests unitarios:** Probar la lógica de traducción (DTO externo → modelo de dominio).
2. **Tests de contrato:** Asegurar que la API externa no ha cambiado (detectar cambios que rompen).
3. **Tests de integración:** Probar el flujo completo (llamar al sistema externo vía ACL).
4. **Tests de ida y vuelta:** Dominio → Externo → Dominio (validar consistencia).
Usar fixtures de prueba para respuestas externas para evitar tests frágiles. Ver [Pirámide de testing](../quality/testing-pyramid.md).
