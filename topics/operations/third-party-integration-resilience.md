---
id: third-party-integration-resilience
title: "Resiliencia en integraciones con terceros"
type: skill
status: learning
importance: 85
difficulty: hard
tags: [reliability, integrations, operations]
canonical: true
aliases: []
created_at: 2026-01-22
updated_at: 2026-01-22
---

# Resiliencia en integraciones con terceros

## TL;DR (BLUF)
- Las integraciones fiables con terceros se construyen con: timeouts, reintentos acotados con backoff+jitter, circuit breakers e idempotencia.
- Trata los errores transitorios y permanentes de forma diferente; enruta el trabajo no reintentable a DLQ / remediación manual.
- El éxito es medible: baja tasa de errores, latencia p99 controlada y sin tormentas de reintentos.

## Definición
**Qué es:** Prácticas y patrones para integrarse con proveedores externos manteniendo tu sistema correcto y estable ante fallos del proveedor.
**Términos clave:** errores transitorios, errores permanentes, límite de tasa (rate limiting), timeout, reintento, circuit breaker, clave de idempotencia.

## Por qué importa
- Las APIs de terceros son un punto único de fallo común.
- "Funciona en dev" se rompe en producción debido a límites de tasa, interrupciones parciales y timeouts.

## Alcance y no-objetivos
**Dentro del alcance:** llamadas HTTP/servicio salientes, webhooks entrantes y pipelines asíncronos.
**Fuera del alcance / NO resuelto por esto:** elegir proveedores o detalles legales/de cumplimiento.

## Modelo mental / Intuición
- Asume que el proveedor será lento, inestable y ocasionalmente incorrecto.
- Tu trabajo es contener el radio de impacto y preservar la corrección.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Usa estos controles cuando
- La llamada está en la ruta crítica (latencia visible para el usuario).
- La llamada es de alto volumen o tiene límites de tasa.

### Evita la complejidad cuando
- La integración es de bajo volumen, mejor esfuerzo, y los duplicados/pérdidas son aceptables.

## Cómo lo usaría (práctico)
- **Contexto:** Integrando con un proveedor de voz y un proveedor de pagos.
- **Pasos:**
  1) Agregar [Timeouts](timeouts.md) estrictos.
  2) Agregar [Reintentos](retries-and-backoff.md) solo para errores transitorios y solo con un presupuesto de tiempo.
  3) Agregar [Circuit breaker](circuit-breaker.md) con sondeo half-open.
  4) Usar [Idempotencia](idempotency.md): claves de idempotencia, almacén de deduplicación y restricciones únicas.
  5) Para flujos asíncronos, enrutar fallos permanentes a [DLQ y mensajes venenosos](dlq-and-poison-messages.md).
- **Cómo se ve el éxito:** las interrupciones del proveedor degradan de forma elegante y no causan [Fallo en cascada](cascading-failure.md).

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** plataforma estable ante incidentes del proveedor.
- **Desventajas / Riesgos:** sobrecarga de ajuste; más piezas móviles; riesgo de fallar rápido demasiado agresivamente.

### Alternativas
- **Desacoplamiento con cola asíncrona:** remover al proveedor de la ruta de la solicitud.
- **Capa de abstracción de proveedor:** para cambiar de proveedores (con sus propios costos).
- **Cómo elegir:** si el proveedor es crítico, invierte en resiliencia + observabilidad.

## Modos de fallo y trampas
- Reintentar errores permanentes (400, auth, esquema) → capacidad desperdiciada.
- Sin idempotencia cuando el proveedor hace timeout después de procesar → acciones duplicadas.
- Sin limitación de tasa / [Contrapresión (Backpressure)](../system-design/backpressure.md) → tormenta auto-infligida de 429.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de errores por proveedor y clase de error, latencia p95/p99, conteos de reintentos, estado del breaker.
- **Logs:** id de correlación entre intentos; identificadores de solicitud sanitizados.
- **Trazas:** tramo de llamada externa con etiquetas (proveedor, ruta, intento).
- **Alertas:** pico en 429/503, breaker abierto, regresión de latencia p99.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Taxonomía de errores: transitorios vs permanentes
  - [ ] Timeout por llamada y presupuesto general
  - [ ] Política de reintentos con jitter y máximo de intentos
  - [ ] Umbrales del circuit breaker
  - [ ] Estrategia de idempotencia
  - [ ] Runbook para interrupciones del proveedor

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** "Si falla, reintenta más."
  - **Por qué es malo:** convierte un incidente externo en tu propia interrupción.
  - **Mejor enfoque:** reintentos con presupuesto + breaker + respaldo asíncrono.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Para integraciones con terceros empiezo con timeouts, agrego reintentos acotados con jitter para errores transitorios, protejo con un circuit breaker para fallos sostenidos y aseguro idempotencia para que los reintentos/timeouts nunca dupliquen efectos secundarios.

### Preguntas trampa (con respuestas)
1) **P:** Si el proveedor devuelve 500 a veces, ¿reintentas indefinidamente?
   - **R:** No; reintentos acotados dentro de un presupuesto de tiempo y luego degradar/encolar/DLQ.
2) **P:** ¿Un timeout significa que el proveedor no procesó la solicitud, verdad?
   - **R:** No necesariamente; los timeouts pueden ocurrir después de que el proveedor procesó la solicitud. Por eso la idempotencia importa.
3) **P:** ¿Los webhooks son más fáciles porque son entrantes?
   - **R:** No automáticamente; aún necesitas idempotencia, verificación de firma y patrones de DLQ/replay.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo clasificar errores correctamente.
- [ ] Puedo diseñar timeouts + reintentos con presupuestos.
- [ ] Puedo explicar el ajuste del circuit breaker.
- [ ] Puedo implementar claves de idempotencia.
- [ ] Puedo proponer observabilidad y alertas.

## Enlaces (SIN duplicación)
### Prerequisitos
- [HTTP](http.md)
- [Idempotencia](idempotency.md)

### Temas relacionados
- [Reintentos, backoff exponencial y jitter](retries-and-backoff.md)
- [Timeouts](timeouts.md)
- [Circuit breaker](circuit-breaker.md)
- [DLQ y mensajes venenosos](dlq-and-poison-messages.md)
- [Feature flags](feature-flags.md)

### Comparar con
- [Arquitectura dirigida por eventos](../architecture/event-driven-basics.md) — los patrones asíncronos reducen la dependencia en la ruta crítica de terceros.

## Notas / Bandeja de entrada (opcional)
- N/A
