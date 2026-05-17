---
id: load-shedding
title: "Descarte de carga (Load Shedding)"
type: pattern
status: learning
importance: 70
difficulty: medium
tags: [reliability, operations, traffic-management]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Descarte de carga (Load Shedding)

## TL;DR (BLUF)
- El descarte de carga rechaza o degrada trabajo intencionalmente cuando la demanda excede la capacidad segura.
- Úsalo para proteger los SLOs de latencia y evitar [Fallo en cascada](cascading-failure.md).
- Trade-off: se descartan o degradan solicitudes para mantener el sistema estable.

## Definición
**Qué es:** Un patrón de resiliencia que activamente descarta, rechaza o degrada solicitudes bajo sobrecarga para mantener el sistema dentro de límites operativos seguros.  
**Términos clave:** control de admisión, rechazo, degradación elegante, priorización, sobrecarga.

## Por qué importa
- Previene picos de latencia en la cola y agotamiento de recursos durante oleadas de tráfico.
- Crea fallos predecibles y controlados en lugar de un colapso lento.

## Alcance y no-objetivos
**Dentro del alcance:** políticas explícitas de rechazo, niveles de prioridad y rutas de degradación durante sobrecarga.  
**Fuera del alcance / NO resuelto por esto:** escasez de capacidad a largo plazo (ver [Planificación de capacidad](capacity-planning.md)) o corrección bajo duplicados (ver [Idempotencia](idempotency.md)).

## Modelo mental / Intuición
- Como una discoteca: si está llena, dejas de dejar entrar gente en lugar de dejar que todos se amontonen dentro.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los picos de demanda son de corta duración o impredecibles.
- Los SLOs de latencia son críticos y debes proteger la capacidad descendente.
- Puedes degradar o descartar trabajo de menor prioridad de forma segura.

### Evítalo cuando
- Debes procesar cada solicitud y puedes tolerar retrasos (prefiere colas asíncronas).
- La sobrecarga es constante y sostenida (prefiere escalado y correcciones de capacidad).

## Cómo lo usaría (práctico)
- **Contexto:** Una API pública ve tráfico repentino durante una campaña de marketing.
- **Pasos:**
  1) Definir umbrales de sobrecarga (CPU, profundidad de cola, saturación del pool de BD).
  2) Agregar control de admisión con 429/503 y `Retry-After`.
  3) Preferir descartar endpoints de baja prioridad o no críticos.
  4) Proporcionar una respuesta degradada donde sea posible.
- **Cómo se ve el éxito:** la latencia p95 se mantiene dentro del SLO mientras la tasa de rechazo está acotada y es transparente.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** estabilidad, latencia controlada, fallo predecible.
- **Desventajas / Riesgos:** errores visibles para el usuario, preocupaciones de equidad y complejidad de ajuste.
### Alternativas
- **Contrapresión (Backpressure):** ralentizar o encolar en lugar de rechazar trabajo.
- **Escalar horizontalmente:** agregar capacidad si la demanda es consistentemente alta.
- **Cómo elegir:** si no puedes encolar de forma segura y debes proteger la latencia, prefiere el descarte.

## Modos de fallo y trampas
- Descartar demasiado tarde → la latencia de cola aún explota.
- Descartar demasiado agresivamente → pérdida evitable de ingresos o UX.
- Sin priorización → solicitudes críticas descartadas junto con las no críticas.
- Reintentos desde clientes sin presupuestos → estampida auto-infligida.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de rechazo, latencia p95/p99, saturación (CPU, uso de pool), profundidad de cola.
- **Logs:** decisiones de control de admisión (umbral, razón, endpoint, nivel).
- **Trazas:** identificar dónde las solicitudes son rechazadas o degradadas.
- **Alertas:** tasa de rechazo sostenida + saturación más allá de umbrales.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir umbrales y señales de sobrecarga
  - [ ] Implementar políticas escalonadas (críticas vs no críticas)
  - [ ] Agregar respuestas de error claras al cliente y `Retry-After`
  - [ ] Asegurar que los reintentos estén acotados y no amplifiquen la carga
- **Notas de seguridad / cumplimiento**
  - Evitar filtrar datos restringidos en respuestas degradadas
- **Notas de rendimiento**
  - Preferir fallo rápido sobre colas largas cuando la latencia es la prioridad
- **Notas operacionales**
  - Documentar razones de rechazo y runbooks

## Mini ejemplo (si aplica)
```text
if cpu > 85% or db_pool_saturation > 90%:
  reject non-critical requests with 503 + Retry-After
  serve cached/degraded response for read-only endpoints
```

## Anti-patrones comunes
- **Anti-patrón:** "Simplemente agrega una cola más grande."
  - **Por qué es malo:** oculta la sobrecarga y empuja los fallos a la cola.
  - **Mejor enfoque:** descarta o degrada trabajo de baja prioridad rápidamente.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- El descarte de carga es el rechazo o degradación deliberada cuando el sistema está sobrecargado, para que se mantenga estable y proteja los SLOs de latencia.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es lo mismo el descarte de carga que la contrapresión?
   - **R:** No. La contrapresión ralentiza/encola a los productores; el descarte de carga descarta o degrada solicitudes.
2) **P:** ¿Deberías siempre descartar en el borde?
   - **R:** No solo en el borde; los servicios internos también pueden necesitar descarte local para proteger pools compartidos.
3) **P:** ¿El descarte de carga reemplaza el escalado?
   - **R:** No. Maneja ráfagas; la demanda sostenida aún requiere cambios de capacidad.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir el descarte de carga con precisión.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de sistemas distribuidos](../system-design/distributed-systems-basics.md)
- [Fundamentos de rendimiento](../system-design/performance-basics.md)
- [Fundamentos de observabilidad](observability-basics.md)

### Temas relacionados
- [Contrapresión (Backpressure)](../system-design/backpressure.md)
- [Circuit breaker](circuit-breaker.md)
- [Bulkheads](bulkheads.md)
- (TODO) Limitación de tasa
- (TODO) Degradación elegante

### Comparar con
- [Contrapresión (Backpressure)](../system-design/backpressure.md) — la contrapresión ralentiza/encola; el descarte de carga rechaza/degrada.
- [Circuit breaker](circuit-breaker.md) — el breaker bloquea llamadas a dependencias no saludables; el descarte protege la capacidad general.

## Notas / Bandeja de entrada (opcional)
- N/A
