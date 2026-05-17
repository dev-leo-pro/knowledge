---
id: cascading-failure
title: "Fallo en Cascada"
type: concept
status: learning
importance: 70
difficulty: medium
tags: [reliability, operations, distributed-systems]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Fallo en Cascada

## TL;DR (BLUF)
- El fallo en cascada es cuando un fallo local se propaga a través de las dependencias y se amplifica en una interrupción a nivel de sistema.
- Usualmente es causado por acoplamiento, reintentos sin presupuestos y saturación de recursos compartidos.
- Trade-off: prevenir cascadas a menudo significa rechazar o degradar trabajo para proteger la estabilidad.

## Definición
**Qué es:** Un patrón de propagación de fallos donde una falla o sobrecarga inicial en un componente causa que los componentes downstream o upstream fallen, extendiéndose por todo el sistema.  
**Términos clave:** amplificación de fallos, cadena de dependencias, tormenta de reintentos, saturación, latencia de cola.

## Por qué importa
- Las cascadas convierten fallos parciales en interrupciones completas.
- Son una de las principales causas de incidentes prolongados en sistemas distribuidos.

## Alcance y no-objetivos
**Dentro del alcance:** propagación de fallos a través de llamadas síncronas, pools compartidos y amplificación de reintentos.  
**Fuera del alcance / NO resuelto por esto:** correcciones de causa raíz (ver [Fundamentos de gestión de incidentes](incident-management-basics.md)) u objetivos de disponibilidad (ver [Fundamentos de disponibilidad](availability-basics.md)).

## Modelo mental / Intuición
- Un servicio se vuelve lento -> upstream espera -> las colas crecen -> los pools se saturan -> todo se vuelve lento o falla.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Estás diseñando o revisando cadenas de llamadas multi-servicio.
- Ves picos de latencia de cola o saturación de pools durante interrupciones parciales.

### Evítalo cuando
- El sistema está aislado sin dependencias críticas (raro en la práctica).

## Cómo lo usaría (práctico)
- **Contexto:** Una API llama a dos servicios downstream sincrónicamente y tiene SLOs de latencia estrictos.
- **Pasos:**
  1) Añadir presupuestos de tiempo y [Timeouts](timeouts.md) por salto.
  2) Aplicar [Contrapresión](../system-design/backpressure.md) y [Load shedding](load-shedding.md).
  3) Aislar con [Bulkheads](bulkheads.md).
  4) Añadir [Circuit breaker](circuit-breaker.md) para detener llamadas no saludables.
- **Cómo se ve el éxito:** los fallos parciales de dependencias no colapsan toda la ruta de petición.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** menos interrupciones sistémicas, degradación controlada.
- **Desventajas / Riesgos:** más ajuste, más rechazo y menor throughput máximo.
### Alternativas
- **Desacoplamiento asíncrono:** mover trabajo no crítico fuera de la ruta de petición.
- **Cómo elegir:** si el acoplamiento síncrono es necesario, invertir en defensas en profundidad.

## Modos de fallo y trampas
- Reintentos ilimitados sin presupuestos.
- Pools compartidos entre rutas críticas y no críticas.
- Falta de presupuestos de tiempo y propagación de cancelación.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia p95/p99, saturación (CPU, uso de pools), tasas de reintentos, profundidad de cola.
- **Logs:** timeouts, conteos de reintentos, activación de fallback.
- **Trazas:** spans elongados a través de cadenas de dependencias.
- **Alertas:** picos de latencia correlacionados entre servicios + saturación.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir presupuestos extremo a extremo y timeouts por salto
  - [ ] Acotar reintentos y añadir jitter
  - [ ] Aplicar contrapresión y load shedding
  - [ ] Aislar con bulkheads
  - [ ] Añadir circuit breakers para dependencias no saludables
- **Notas de seguridad / cumplimiento**
  - Asegurar que las respuestas degradadas no filtren datos sensibles
- **Notas de rendimiento**
  - Preferir colas acotadas sobre buffers sin límite
- **Notas operativas**
  - Documentar políticas de fallback y shedding

## Mini ejemplo (si aplica)
```text
A -> B -> C (sync)
C se vuelve lento → las colas de B crecen → A espera → el pool de A se satura → A empieza a dar timeout
```

## Anti-patrones comunes
- **Anti-patrón:** "Solo añadir más reintentos."
  - **Por qué es malo:** los reintentos amplifican la carga y aceleran la propagación del fallo.
  - **Mejor enfoque:** añadir presupuestos, contrapresión y breakers.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- El fallo en cascada ocurre cuando una falla local se propaga a través de dependencias y recursos compartidos, convirtiendo fallos parciales en una interrupción a nivel de sistema.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los fallos en cascada solo ocurren con llamadas síncronas?
   - **R:** No. Los sistemas asíncronos pueden tener cascadas via acumulación de colas, tormentas de reintentos o saturación de recursos compartidos.
2) **P:** ¿Son los reintentos siempre buenos para evitar cascadas?
   - **R:** No. Los reintentos sin límite amplifican la carga y empeoran la cascada.
3) **P:** ¿Un circuit breaker solo previene cascadas?
   - **R:** No siempre. También necesitas timeouts, contrapresión y aislamiento.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir fallo en cascada con precisión.
- [ ] Puedo explicar cómo se propagan los fallos.
- [ ] Puedo nombrar al menos 2 defensas.
- [ ] Puedo describir 1 modo de fallo.
- [ ] Puedo proponer métricas de detección.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de sistemas distribuidos](../system-design/distributed-systems-basics.md)
- [Fundamentos de rendimiento](../system-design/performance-basics.md)
- [Fundamentos de observabilidad](observability-basics.md)

### Temas relacionados
- [Timeouts](timeouts.md)
- [Reintentos, backoff exponencial y jitter](retries-and-backoff.md)
- [Circuit breaker](circuit-breaker.md)
- [Contrapresión](../system-design/backpressure.md)
- [Load shedding](load-shedding.md)
- [Bulkheads](bulkheads.md)
- [Arquitectura request-response](../architecture/request-response.md)
- [Comunicación síncrona vs asíncrona](../system-design/sync-vs-async-communication.md)

### Comparar con
- [Contrapresión](../system-design/backpressure.md) — limita carga para reducir propagación de fallos.
- [Load shedding](load-shedding.md) — descarta/degrada trabajo para mantener capacidad estable.

## Notas / Bandeja de entrada (opcional)
- N/A
