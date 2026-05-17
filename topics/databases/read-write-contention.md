---
id: read-write-contention
title: "Contención de lectura/escritura"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, performance, concurrency]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Contención de lectura/escritura

## TL;DR (BLUF)
- La contención ocurre cuando lecturas y escrituras concurrentes compiten por los mismos recursos.
- Usa indexación y transacciones más cortas para reducir la contención.
- Trade-off: más índices pueden ralentizar las escrituras.

## Definición
**Qué es:** Degradación del rendimiento causada por acceso concurrente a las mismas filas, índices o bloqueos.
**Términos clave:** contención de bloqueos, filas calientes, amplificación de escritura.

## Por qué importa
- La contención puede dominar la latencia incluso con buen hardware.
- A menudo aparece solo a escala.

## Alcance y no-objetivos
**Dentro del alcance:** fuentes de contención y patrones de mitigación.
**Fuera del alcance / NO resuelto por esto:** cuellos de botella a nivel de red.

## Modelo mental / Intuición
- Piensa en una caja registradora ocupada; todos hacen cola detrás de un bloqueo compartido.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Observas esperas de bloqueo o alta latencia de escritura.
### Evítalo cuando
- La carga de trabajo es naturalmente particionable y puede ser fragmentada.

## Cómo lo usaría (práctico)
- **Contexto:** Fila de contador caliente actualizada por muchas solicitudes.
- **Pasos:** reducir actualizaciones de filas calientes → fragmentar contadores → acortar transacciones.
- **Cómo se ve el éxito:** menores esperas de bloqueo y latencia estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** throughput mejorado.
- **Desventajas / Riesgos:** complejidad añadida (sharding, reintentos).
### Alternativas
- **Caché:** descargar lecturas.
- **Cómo elegir:** mitigar la contención antes de escalar hardware.

## Modos de fallo y trampas
- Filas calientes por IDs monótonamente crecientes.
- Escalada de bloqueos por actualizaciones grandes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** esperas de bloqueo, latencia de escritura, deadlocks.
- **Logs:** consultas lentas con esperas de bloqueo.
- **Alertas:** tiempo de espera de bloqueo en aumento.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar filas/índices calientes
  - [ ] Reducir alcance de transacciones
  - [ ] Considerar sharding o procesamiento por lotes

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Contador global único para actualizaciones de alto tráfico.
  - **Por qué es malo:** crea una fila caliente.
  - **Mejor enfoque:** fragmentar contadores o usar aproximaciones.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La contención de lectura/escritura es cuando muchas operaciones pelean por las mismas filas o bloqueos, ralentizando todo. Se arregla reduciendo el tiempo de bloqueo, fragmentando e indexando.

### Preguntas trampa (con respuestas)
1) **P:** ¿Más índices siempre reducen la contención?
   - **R:** no; los índices pueden aumentar el costo de escritura y la contención.
2) **P:** ¿La contención puede resolverse solo con mejor hardware?
   - **R:** no; a menudo es un problema de patrón de acceso a datos.
3) **P:** ¿Las lecturas siempre son gratuitas?
   - **R:** no; pueden bloquear o ser bloqueadas dependiendo del aislamiento.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir contención con precisión.
- [ ] Puedo nombrar una mitigación.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar una señal.
- [ ] Puedo comparar con particiones calientes.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Bloqueos](locks.md)

### Temas relacionados
- [Deadlocks](deadlocks.md)

### Comparar con
- [Particiones calientes en DynamoDB](dynamodb-hot-partitions.md) — sesgo de claves en NoSQL.
