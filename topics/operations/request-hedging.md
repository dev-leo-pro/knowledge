---
id: request-hedging
title: "Cobertura de solicitudes (Request Hedging)"
type: pattern
status: learning
importance: 65
difficulty: medium
tags: [reliability, performance, latency]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Cobertura de solicitudes (Request Hedging)

## TL;DR (BLUF)
- La cobertura de solicitudes envía una segunda solicitud si la primera es lenta para reducir la latencia de cola.
- Úsala solo para lecturas idempotentes y dentro de presupuestos de reintento.
- Trade-off: aumenta la carga y puede amplificar interrupciones si no está acotada.

## Definición
**Qué es:** Un patrón de optimización de latencia donde se emite una solicitud duplicada después de un umbral (a menudo p95) y la respuesta más rápida gana.  
**Términos clave:** latencia de cola, solicitud cubierta, umbral p95, cancelación, idempotente.

## Por qué importa
- La latencia de cola (p99) a menudo domina la experiencia del usuario.
- La cobertura puede reducir la latencia de cola sin esperar a nodos lentos.

## Alcance y no-objetivos
**Dentro del alcance:** operaciones de solo lectura, idempotentes con coberturas acotadas.  
**Fuera del alcance / NO resuelto por esto:** operaciones de escritura o acciones no idempotentes (ver [Idempotencia](idempotency.md)).

## Modelo mental / Intuición
- "Apuesta por dos caballos" cuando es probable que el primero sea lento.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La latencia de cola es el dolor dominante del usuario.
- Las operaciones son de solo lectura o idempotentes.
- Puedes controlar la carga extra con [Presupuestos de reintento](retry-budgets.md).

### Evítalo cuando
- La operación no es idempotente o es costosa.
- La dependencia ya está saturada.

## Cómo lo usaría (práctico)
- **Contexto:** Una API con muchas lecturas con nodos ocasionalmente lentos.
- **Pasos:**
  1) Enviar solicitud A.
  2) Si A excede p95 (por ejemplo, 60ms), enviar solicitud B.
  3) Usar la primera respuesta, cancelar la otra.
  4) Aplicar la cobertura dentro de los límites del presupuesto de reintento.
- **Cómo se ve el éxito:** la latencia p99 baja sin aumentar tasas de error o saturación.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejor latencia de cola.
- **Desventajas / Riesgos:** carga extra, riesgo de amplificación de sobrecarga.
### Alternativas
- **Timeouts + reintentos:** para fallos transitorios, no latencia de cola.
- **Caché:** evitar llamadas lentas repetidas.
- **Cómo elegir:** cubre solo cuando la latencia de cola es el problema dominante y puedes permitir carga extra.

## Modos de fallo y trampas
- Cubrir cada solicitud → sobrecarga.
- Usar cobertura en escrituras → efectos secundarios duplicados.
- Sin cancelación → duplica la carga innecesariamente.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de cobertura, % de carga extra, latencia p95/p99, tasa de cancelación.
- **Logs:** razón de activación de cobertura y umbral.
- **Trazas:** tramos paralelos y selección del ganador.
- **Alertas:** pico en tasa de cobertura + aumento de saturación.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Establecer umbral de cobertura (por ejemplo, p95)
  - [ ] Restringir a lecturas idempotentes
  - [ ] Aplicar límites de presupuesto de reintento
  - [ ] Cancelar solicitudes perdedoras
- **Notas de seguridad / cumplimiento**
  - N/A
- **Notas de rendimiento**
  - Rastrear la carga incremental de las coberturas
- **Notas operacionales**
  - Ajustar el umbral para evitar cobertura excesiva

## Mini ejemplo (si aplica)
```text
send A
if A > p95_threshold: send B
use first response; cancel the other
```

## Anti-patrones comunes
- **Anti-patrón:** "Cubrir todas las solicitudes."
  - **Por qué es malo:** duplica la carga y puede provocar interrupciones.
  - **Mejor enfoque:** cubrir selectivamente con un presupuesto.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La cobertura de solicitudes emite una solicitud de respaldo después de un umbral de latencia para reducir la latencia de cola, pero debe estar acotada y solo usarse para lecturas idempotentes.

### Preguntas trampa (con respuestas)
1) **P:** ¿Puedes cubrir solicitudes de escritura?
   - **R:** No. A menos que sean estrictamente idempotentes, arriesgas efectos secundarios duplicados.
2) **P:** ¿La cobertura reemplaza los reintentos?
   - **R:** No. La cobertura apunta a la latencia de cola; los reintentos apuntan a la recuperación de fallos transitorios.
3) **P:** ¿Es segura la cobertura sin presupuestos?
   - **R:** No. Puede amplificar la carga y causar sobrecarga.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir la cobertura de solicitudes con precisión.
- [ ] Puedo indicar cuándo usarla y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de rendimiento](../system-design/performance-basics.md)
- [Idempotencia](idempotency.md)

### Temas relacionados
- [Presupuestos de reintento](retry-budgets.md)
- [Timeouts](timeouts.md)
- [Contrapresión (Backpressure)](../system-design/backpressure.md)

### Comparar con
- [Reintentos, backoff exponencial y jitter](retries-and-backoff.md) — los reintentos recuperan fallos; la cobertura reduce la latencia de cola.
- [Fundamentos de caché](../system-design/caching-fundamentals.md) — el caché reduce llamadas; la cobertura duplica llamadas.

## Notas / Bandeja de entrada (opcional)
- N/A
