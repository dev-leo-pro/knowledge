---
id: timeouts
title: "Timeouts y presupuestos de tiempo"
type: concept
status: learning
importance: 85
difficulty: medium
tags: [reliability, integrations, distributed-systems]
canonical: true
aliases: []
created_at: 2026-01-22
updated_at: 2026-01-22
---

# Timeouts y presupuestos de tiempo

## TL;DR (BLUF)
- Los timeouts previenen trabajo atascado y limitan el radio de impacto.
- Usa un **presupuesto de tiempo** para toda la operación y asígnalo por salto.
- Reintentos sin timeouts son un asesino común en producción.

## Definición
**Qué es:** Un tiempo máximo permitido para una operación (solicitud, llamada a BD, procesamiento de mensajes) antes de que sea cancelada/fallida.
**Términos clave:** timeout, deadline, presupuesto de tiempo, cancelación.

## Por qué importa
- Sin timeouts, la latencia y el uso de recursos crecen sin límite ante fallos.
- Los timeouts permiten latencia de cola predecible y previenen [Fallo en cascada](cascading-failure.md).

## Alcance y no-objetivos
**Dentro del alcance:** timeouts para llamadas HTTP, llamadas a BD, procesamiento de mensajes.
**Fuera del alcance / NO resuelto por esto:** corrección bajo reintentos (ver [Idempotencia](idempotency.md)).

## Modelo mental / Intuición
- Cada solicitud tiene un "cronómetro". Si se agota, debes detener el trabajo y liberar recursos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Usa timeouts cuando
- Cualquier operación cruza un límite de red.
- El trabajo usa recursos compartidos (hilos, conexiones de BD, workers de cola).

### Evita timeouts excesivamente agresivos cuando
- Crearías fallos falsos para operaciones conocidas como largas; prefiere procesamiento asíncrono.

## Cómo lo usaría (práctico)
- **Contexto:** Una llamada API activa 2 servicios descendentes + escritura en BD.
- **Pasos:**
  1) Definir presupuesto SLO de extremo a extremo (por ejemplo, 800ms).
  2) Asignar por salto (por ejemplo, 150ms + 150ms + 200ms + sobrecarga).
  3) Asegurar que los reintentos quepan en el presupuesto restante.
- **Cómo se ve el éxito:** p99 acotado y sin pools de workers atascados.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** latencia de cola acotada, capacidad predecible.
- **Desventajas / Riesgos:** timeouts falsos causan reintentos y carga; se requiere ajuste.

### Alternativas
- **Asíncrono con cola:** mover trabajo largo fuera de la ruta de la solicitud.
- **Degradación elegante:** devolver resultados parciales o "intenta de nuevo más tarde".
- **Cómo elegir:** si el usuario espera, mantén los presupuestos ajustados; si no, usa asíncrono.

## Modos de fallo y trampas
- Sin timeout en absoluto (infinito por defecto).
- Mismo timeout para todas las llamadas (ignora dependencias).
- Timeouts sin propagación de cancelación → trabajo desperdiciado continúa.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de timeout, percentiles de latencia, saturación de pool, reintentos provocados por timeouts.
- **Logs:** "deadline exceeded" con presupuesto restante y dependencia.
- **Trazas:** mostrar dónde se gasta el tiempo antes del timeout.
- **Alertas:** aumento sostenido de tasa de timeout + saturación.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir presupuesto de tiempo de extremo a extremo
  - [ ] Establecer timeouts por salto
  - [ ] Propagar cancelación/deadlines entre llamadas
  - [ ] Asegurar que los reintentos quepan dentro del presupuesto

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Aumentar timeouts para "arreglar" fallos.
  - **Por qué es malo:** oculta la sobrecarga y empeora los incidentes.
  - **Mejor enfoque:** agregar [Contrapresión (Backpressure)](../system-design/backpressure.md), reintentos con presupuestos y breakers.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los timeouts son un límite protector: detienen trabajo atascado, limitan el [Fallo en cascada](cascading-failure.md) y te obligan a asignar un presupuesto de latencia explícito.

### Preguntas trampa (con respuestas)
1) **P:** Si agregas reintentos, ¿deberías aumentar los timeouts?
   - **R:** No automáticamente; los reintentos deben caber en un presupuesto, de lo contrario aumentas la latencia de cola y la carga.
2) **P:** ¿Los timeouts son solo para HTTP?
   - **R:** No; las llamadas a BD, procesamiento de colas y cualquier operación con recursos acotados los necesitan.
3) **P:** ¿Es suficiente un timeout sin cancelación?
   - **R:** No; si el trabajo continúa después del timeout, aún quemas recursos y puedes duplicar efectos secundarios.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo explicar por qué los timeouts previenen el [Fallo en cascada](cascading-failure.md).
- [ ] Puedo definir un presupuesto de tiempo y asignarlo.
- [ ] Puedo conectar timeouts con reintentos.
- [ ] Puedo proponer métricas para timeouts.
- [ ] Puedo describir la propagación de cancelación.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de sistemas distribuidos](../system-design/distributed-systems-basics.md)

### Temas relacionados
- [Reintentos, backoff exponencial y jitter](retries-and-backoff.md)
- [Circuit breaker](circuit-breaker.md)
- [HTTP](http.md)

### Comparar con
- [Reintentos, backoff exponencial y jitter](retries-and-backoff.md) — los reintentos aumentan el tiempo; los presupuestos limitan los reintentos.

## Notas / Bandeja de entrada (opcional)
- N/A
