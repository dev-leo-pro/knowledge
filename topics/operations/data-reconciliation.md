---
id: data-reconciliation
title: "Data Reconciliation"
type: pattern
status: learning
importance: 75
difficulty: medium
tags: [operations, data-consistency, distributed-systems, reliability]
canonical: true
aliases: [reconciliation, data sync, consistency check]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Reconciliación de Datos

## TL;DR (BLUF)
- Un proceso que detecta y corrige inconsistencias de datos entre sistemas comparando estados y resolviendo diferencias.
- Úsalo cuando la consistencia eventual sea aceptable pero la divergencia deba estar acotada y corregida.
- Trade-off: complejidad operacional y latencia en detectar/corregir inconsistencias.

## Definición
**Qué es:** Un patrón donde los sistemas periódicamente o bajo demanda comparan su estado de datos con fuentes autoritativas (o entre sí) para detectar y corregir inconsistencias que surgen de escrituras fallidas, eventos perdidos, particiones de red o bugs.

**Términos clave:** comparación de estado, detección de desvío, fuente de verdad, acción compensatoria, reconciliación periódica, reconciliación bajo demanda, consistencia de datos, convergencia.

## Por qué importa
- Proporciona red de seguridad para sistemas de consistencia eventual (dirigidos por eventos, escenarios de escritura dual).
- Detecta bugs en producción (eventos perdidos, compensaciones fallidas, condiciones de carrera).
- Permite la recuperación de fallos de infraestructura (particiones de red, caídas de servicio).
- Genera confianza en sistemas distribuidos a pesar de la falta de consistencia fuerte.

## Alcance y no-objetivos
**Dentro del alcance:** detectar inconsistencias, resolver conflictos, verificaciones periódicas, reconciliación manual vs automatizada.

**Fuera del alcance / NO resuelto por esto:**
- Prevenir inconsistencias (usar [Outbox](../architecture/outbox-pattern.md), [Saga](../architecture/saga-pattern.md))
- Consistencia fuerte (usar transacciones ACID dentro del servicio)
- Resolución de conflictos en tiempo real (la reconciliación acepta lag)

## Modelo mental / Intuición
- Como balancear tu cuenta bancaria: periódicamente comparar tus registros vs el extracto del banco, identificar diferencias, investigar y corregir.
- En sistemas distribuidos: comparar BD de order-service vs BD de payment-service; si existe la orden pero falta el pago → investigar y corregir.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tengas consistencia eventual entre sistemas (dirigido por eventos, escritura dual, sincronización de caché).
- Las inconsistencias sean raras pero deban detectarse y corregirse (no ignorarse).
- Puedas tolerar obsolescencia acotada (minutos a horas).
- Los sistemas puedan acordar una "fuente de verdad" o reglas de resolución de conflictos.
- La intervención manual para casos límite sea aceptable.

### Evítalo cuando
- Necesites consistencia en tiempo real (usar llamadas síncronas o transacciones distribuidas).
- El costo de reconciliación exceda el costo de inconsistencia (ej., analítica donde 99.9% de precisión está bien).
- No puedas definir fuente de verdad o reglas de resolución de conflictos.
- Los sistemas cambien demasiado frecuentemente (la reconciliación siempre va detrás).

## Cómo lo usaría (práctico)
- **Contexto:** E-commerce con order-service y payment-service. Los eventos los sincronizan, pero ocasionalmente se pierden eventos.
- **Pasos:**
  1) **Definir fuente de verdad:** payment-service es autoritativo para el estado de pago.
  2) **Programar reconciliación periódica:** cada 1 hora, comparar las últimas 24 horas de órdenes.
  3) **Lógica de detección:**
     ```sql
     SELECT o.order_id, o.payment_status, p.status
     FROM orders o
     LEFT JOIN payments p ON o.order_id = p.order_id
     WHERE o.payment_status != p.status
       OR (o.payment_status = 'PAID' AND p.id IS NULL)
     ```
  4) **Reglas de resolución de conflictos:**
     - Si existe pago pero falta orden → crear orden (raro, investigar).
     - Si orden PAID pero pago faltante → marcar orden PENDING, alertar a ops (investigar).
     - Si los estados difieren → confiar en payment-service, actualizar order-service.
  5) **Acciones automatizadas:**
     - Registrar discrepancias en dashboard.
     - Auto-corregir casos simples (desajuste de estado).
     - Crear tickets para revisión manual (registros faltantes).
  6) **Reconciliación bajo demanda:** Disparada después de timeout en llamada API de pago.
     ```go
     // Después de timeout en API de pago
     if err == TimeoutError {
         // No reintentar; reconciliar en su lugar
         go reconcilePaymentStatus(orderID) // verificación asíncrona de estado
     }
     ```
  7) **Monitorear:** lag de reconciliación, tasa de discrepancias, ratio auto-corrección vs manual.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Lag entre inconsistencia y detección | Ejecutar reconciliación más frecuentemente; agregar disparadores bajo demanda |
| Carga del job de reconciliación en BDs | Ejecutar en horas valle; usar réplicas de lectura; paginar consultas |
| Ambigüedad en resolución de conflictos | Definir reglas claras; escalar casos ambiguos a revisión manual |
| Falsos positivos (inconsistencias transitorias) | Agregar período de gracia (ej., ignorar si < 5 min de antigüedad) |
| Sobrecarga operacional (monitoreo, alertas) | Automatizar correcciones simples; construir dashboards; establecer SLOs para lag de reconciliación |

## Modos de fallo / Casos límite
1. **El job de reconciliación falla:** Las inconsistencias se acumulan sin detectar.
   - *Mitigación:* Monitorear salud del job; alertar en fallos; reintentar con backoff.
2. **Ambos sistemas incorrectos:** Sin fuente de verdad clara.
   - *Mitigación:* Usar log de eventos como fuente de verdad; reproducir eventos.
3. **Bucle infinito de reconciliación:** Auto-corrección dispara auto-corrección opuesta.
   - *Mitigación:* Agregar idempotencia; versionar datos; prevenir ping-pong con flag de conflicto.
4. **La reconciliación crea nueva inconsistencia:** La corrección introduce bug.
   - *Mitigación:* Probar lógica de reconciliación exhaustivamente; modo dry-run; aprobación manual para lotes grandes.
5. **Datos de alta cardinalidad:** Millones de registros, reconciliación lenta.
   - *Mitigación:* Reconciliación incremental (últimas N horas); usar checksums/hashes para comparación masiva.

## Patrones
### 1. Reconciliación Periódica (Batch)
- Job programado (cron, Airflow) compara dataset completo o ventana temporal.
- **Pros:** Simple, exhaustivo.
- **Contras:** Alta latencia (horas), intensivo en recursos.
- **Uso:** Reconciliación nocturna de órdenes, inventario, facturación.

### 2. Reconciliación Bajo Demanda (Reactiva)
- Disparada por eventos específicos (timeout, queja de usuario, patrón sospechoso).
- **Pros:** Rápida para casos críticos.
- **Contras:** No detecta problemas desconocidos.
- **Uso:** Después de timeout de pago, verificar estado de pago antes de reintentar.

### 3. Reconciliación Continua (Stream)
- Comparación en tiempo real usando flujos de eventos (Kafka, Change Data Capture).
- **Pros:** Baja latencia (segundos).
- **Contras:** Complejo, mayor uso de recursos.
- **Uso:** Transacciones de alto valor, detección de fraude en tiempo real.

### 4. Reconciliación basada en Checksum
- Comparar checksums agregados (hash del dataset) en vez de fila por fila.
- **Pros:** Rápido para datasets grandes.
- **Contras:** No identifica inconsistencias específicas.
- **Uso:** Detectar desvío; profundizar solo si los checksums difieren.

## Estrategias de Resolución de Conflictos
1. **Gana la fuente de verdad:** Un sistema es autoritativo (más simple).
2. **Gana la última escritura (LWW):** Usar timestamp; la actualización más nueva gana (Cassandra, DynamoDB).
3. **Resolución manual:** Escalar a ops/soporte para decisión humana.
4. **Merge / unión:** Combinar ambas versiones si no conflictúan (CRDTs).
5. **Acción compensatoria:** Disparar compensación de saga (ej., reembolso + cancelar orden).

## Combinaciones
La Reconciliación de Datos **casi siempre se usa con:**
- **[Idempotencia](idempotency.md):** Las acciones de reconciliación deben ser seguras de reintentar.
- **[Patrón Outbox](../architecture/outbox-pattern.md):** Reduce la necesidad de reconciliación (eventos fiables).
- **[Saga](../architecture/saga-pattern.md):** Reconciliar compensaciones fallidas.
- **[Observabilidad](observability-basics.md):** Monitorear desvío, lag de reconciliación, tasa de corrección.
- **[Change Data Capture](../databases/change-data-capture.md):** Transmitir cambios para reconciliación continua.

**Combinación típica:**
- **Sistema dirigido por eventos:** Outbox (reducir desvío) + Idempotencia (correcciones seguras) + Reconciliación (red de seguridad) + Observabilidad

## Prerrequisitos
- Comprensión de [consistencia eventual](../databases/consistency-models.md).
- Familiaridad con [arquitectura dirigida por eventos](../architecture/event-driven-basics.md).
- Conocimiento del [patrón de escritura dual](../architecture/dual-write-pattern.md) y sus problemas.

## Temas relacionados
- [Patrón Outbox](../architecture/outbox-pattern.md): Previene inconsistencias proactivamente.
- [Patrón Saga](../architecture/saga-pattern.md): Reconciliar compensaciones fallidas.
- [Patrón de escritura dual](../architecture/dual-write-pattern.md): Fuente común de desvío.
- [Idempotencia](idempotency.md): Requerida para reconciliación segura.
- [Change Data Capture](../databases/change-data-capture.md): Reconciliación basada en streams.
- [Observabilidad](observability-basics.md): Detectar desvío tempranamente.

## Ejemplos del mundo real
1. **Stripe:** Reconcilia eventos de pago con archivos de liquidación bancaria diariamente.
2. **Amazon:** Reconcilia estado de órdenes entre almacenes, inventario y envío.
3. **Banca:** Transacciones de ATM reconciliadas nocturnamente con sistema bancario central.
4. **Netflix:** Reconcilia historial de visualización de usuarios entre centros de datos regionales.

## Checklist (auto-evaluación)
- [ ] Puedo identificar cuándo se necesita reconciliación vs cuándo es mejor la prevención.
- [ ] Sé cómo definir fuente de verdad y reglas de resolución de conflictos.
- [ ] Puedo diseñar reconciliación periódica vs bajo demanda vs continua.
- [ ] Entiendo el trade-off entre frecuencia de reconciliación y costo.
- [ ] Puedo monitorear la salud de la reconciliación (lag, tasa de discrepancias, tasa de corrección).

## Recordatorios / Puntos clave
- La reconciliación es una **red de seguridad**, no una estrategia primaria (prevenir inconsistencias primero).
- Siempre definir **fuente de verdad** y **reglas de resolución de conflictos** por adelantado.
- Usar **[Idempotencia](idempotency.md)** para acciones de reconciliación (seguras de reintentar).
- Monitorear **tasa de desvío** y **lag de reconciliación** (SLOs).
- Combinar con **[Outbox](../architecture/outbox-pattern.md)** para minimizar la necesidad de reconciliación.

## Preguntas trampa (con respuestas)
### P1: ¿Debo usar reconciliación en vez de arreglar el problema de escritura dual?
**R:** **No**. La reconciliación es una **red de seguridad**, no una solución. Primero, eliminar la escritura dual usando [patrón Outbox](../architecture/outbox-pattern.md) o [Change Data Capture](../databases/change-data-capture.md). Luego agregar reconciliación para detectar casos límite (bugs, fallos de infraestructura). Depender solo de la reconciliación significa aceptar inconsistencias frecuentes.

### P2: ¿Con qué frecuencia debo ejecutar la reconciliación?
**R:** **Depende de la tolerancia al desvío**. Directrices:
- **Datos críticos (pagos, órdenes):** Cada 5-15 minutos O bajo demanda después de timeouts.
- **Datos importantes (inventario, perfiles de usuario):** Cada hora.
- **Analítica, cachés:** Diario o semanal.
Balance: frecuencia vs carga en BD. Monitorear **tasa de desvío** — si es alta, arreglar la causa raíz, no solo reconciliar más rápido.

### P3: ¿Qué pasa si la reconciliación encuentra datos conflictivos en ambos sistemas?
**R:** **Definir reglas de resolución de conflictos**:
1. **Fuente de verdad:** Un sistema es autoritativo (más simple).
2. **Gana la última escritura:** Usar timestamps (cuidado con el desfase de reloj).
3. **Revisión manual:** Escalar a ops (para casos complejos).
4. **Log de eventos:** Reproducir eventos para reconstruir el estado correcto.
Si ninguna regla aplica, registrar para investigación manual. Nunca elegir silenciosamente uno — eso oculta bugs.

### P4: ¿La reconciliación puede reemplazar la idempotencia?
**R:** **No, son complementarios**. La **[Idempotencia](idempotency.md)** asegura que **las peticiones duplicadas no causen efectos secundarios**. La **reconciliación** **detecta y corrige inconsistencias** que ya ocurrieron. Necesitas ambas: la idempotencia previene duplicados durante la reconciliación; la reconciliación detecta casos donde la idempotencia falló (ej., TTL expirado).

### P5: Después de un timeout en una llamada API de pago, ¿debo reintentar o reconciliar?
**R:** **Reconciliar primero, luego decidir**. El timeout significa que no sabes si el pago tuvo éxito. Opciones:
1. **Verificar estado** (GET /payments/{id}) usando clave de [Idempotencia](idempotency.md).
2. Si "Procesando" → esperar/consultar (no reintentar POST).
3. Si "Exitoso" → marcar orden pagada (sin reintento).
4. Si "Fallido" → seguro reintentar POST.
Nunca reintentar ciegamente POST después de timeout — riesgo de doble cargo. Ver [Timeouts](timeouts.md) y [Resiliencia de integración con terceros](../operations/third-party-integration-resilience.md).
