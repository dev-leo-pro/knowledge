---
id: postgresql-scaling-playbook
title: "Guía de escalado de PostgreSQL"
type: concept
status: learning
importance: 70
difficulty: medium
tags: [postgresql, scaling, performance, operations]
canonical: true
aliases: []
created_at: 2026-02-05
updated_at: 2026-02-05
---

# Guía de escalado de PostgreSQL

## TL;DR (BLUF)
- Escala en orden: optimizar → escalar verticalmente → réplicas de lectura → particionamiento → sharding.
- Usa señales claras (CPU/RAM/IO, latencia, bloat, retraso de replicación) para elegir el siguiente paso.
- Trade-off: cada paso compra capacidad pero aumenta la complejidad operativa.

## Definición
**Qué es:** Una escalera de decisión para cuándo y cómo escalar PostgreSQL, priorizando el ajuste antes de agregar nodos.
**Términos clave:** escalado vertical (scale-up), réplica de lectura, retraso de replicación, particionamiento, sharding, WAL, p95/p99.

## Por qué importa
- Evita el sharding prematuro y el costo operativo que conlleva.
- Mantiene la latencia predecible mientras controla costo y riesgo.

## Alcance y no-objetivos
**Dentro del alcance:** decisiones de escalado de PostgreSQL en un solo clúster y sus señales.
**Fuera del alcance / NO resuelto por esto:** replicación activa-activa multi-región (ver TODO abajo).

## Modelo mental / Intuición
- Una escalera: cada peldaño cuesta más operar, así que sube solo cuando el peldaño anterior se haya agotado.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Ves latencia sostenida o saturación y necesitas un siguiente paso estructurado.
- Necesitas un plan de escalado defendible en lugar de correcciones ad-hoc.

### Evítalo cuando
- Tu carga de trabajo es pequeña o intermitente y puede resolverse con ajuste básico.
- No puedes medir CPU, IO y latencia (arregla la observabilidad primero).

## Cómo lo usaría (práctico)
- **Contexto:** Un servicio de feed de productos en PostgreSQL con tráfico creciente.
- **Pasos:**
  1) **Ajustar primero (sin escalar):** arreglar [índices](index.md), malos [planes de consulta](query-planning.md), evitar consultas N+1, agregar [connection pooling](connection-pooling.md), ajustar [autovacuum](postgresql-vacuum-autovacuum.md), reducir [bloat](postgresql-bloat.md).
  2) **Escalar verticalmente:** si CPU o RAM están saturados y el ajuste de consultas está hecho.
  3) **Réplicas de lectura:** si las lecturas dominan y puedes tolerar [consistencia eventual](consistency-models.md) en lecturas.
  4) **Particionamiento:** si las tablas son enormes, [VACUUM](postgresql-vacuum-autovacuum.md) tiene dificultades, y las consultas por rango de tiempo dominan.
  5) **Sharding:** si las escrituras o la contención de bloqueos exceden un solo nodo y el escalado vertical/réplicas ya no ayudan.
- **Cómo se ve el éxito:** la latencia p95 se estabiliza, vuelve el margen de CPU/IO, y las tareas de mantenimiento se completan a tiempo.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** ruta de decisión predecible, evita complejidad distribuida prematura.
- **Desventajas / Riesgos:** las réplicas agregan retraso; particionamiento/sharding aumentan la complejidad de consultas y operaciones.

### Alternativas
- **Escrituras asíncronas basadas en colas:** reducen la presión síncrona sobre la BD.
- **Caché:** descarga lecturas para rutas calientes.
- **Cómo elegir:** si el cuello de botella es de lecturas, prefiere réplicas/caché; si es de escrituras, planifica sharding o rediseño de carga de trabajo.

## Modos de fallo y trampas
- Escalar antes de arreglar [consultas lentas](slow-query-diagnosis.md).
- Malinterpretar 4xx o timeouts de aplicación como saturación de BD.
- Depender de réplicas ignorando el retraso de replicación para rutas read-your-writes.
- Particionar por una clave sesgada → [particiones calientes](hot-partitions.md).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** CPU, iowait, ratio de aciertos de caché, latencia p95/p99, tasa de WAL, retraso de replicación.
- **Logs:** advertencias de autovacuum y checkpoint, logs de consultas lentas.
- **Trazas:** endpoints calientes con alto tiempo de span de BD.
- **Alertas:** degradación sostenida de p95 + saturación por >N minutos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Confirmar planes de consulta e índices primero
  - [ ] Medir saturación de CPU/RAM/IO y ratio de aciertos de caché
  - [ ] Agregar réplicas de lectura solo para rutas con muchas lecturas
  - [ ] Particionar por patrón de acceso (tiempo/tenant), no por conveniencia
  - [ ] Hacer sharding solo después de agotar escalado vertical y réplicas

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Hacer sharding primero porque "Postgres no escala."
  - **Por qué es malo:** heredas complejidad distribuida sin arreglar las causas raíz.
  - **Mejor enfoque:** ajustar → escalar verticalmente → réplicas → particionamiento → sharding.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El escalado de PostgreSQL es una escalera: arregla consultas y mantenimiento primero, luego escala verticalmente, luego agrega réplicas de lectura, luego particiona tablas grandes, y solo entonces haz sharding si un solo nodo no puede manejar las escrituras.

### Preguntas trampa (con respuestas)
1) **P:** ¿Deberías agregar réplicas antes de arreglar consultas lentas?
   - **R:** No; las réplicas copian la ineficiencia. Arregla planes de consulta e índices primero.
2) **P:** ¿Las réplicas de lectura ayudan con cuellos de botella de escritura?
   - **R:** No realmente; ayudan con cargas de trabajo de lectura, no con throughput de escritura.
3) **P:** ¿El particionamiento es lo mismo que el sharding?
   - **R:** No; el particionamiento divide datos dentro de una base de datos, el sharding los distribuye entre nodos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo listar la escalera de escalado en orden.
- [ ] Puedo nombrar las señales para cada paso.
- [ ] Puedo explicar los riesgos del retraso de replicación.
- [ ] Puedo explicar cuándo el particionamiento ayuda.
- [ ] Puedo justificar cuándo el sharding es inevitable.

## Enlaces (SIN duplicación)
### Prerequisitos
- [PostgreSQL](postgresql.md)
- [Índice](index.md)
- [Planificación de consultas](query-planning.md)
- [Diagnóstico de consultas lentas](slow-query-diagnosis.md)
- [Connection pooling](connection-pooling.md)
- [PostgreSQL vacuum y autovacuum](postgresql-vacuum-autovacuum.md)
- [Bloat en PostgreSQL](postgresql-bloat.md)
- [Estadísticas y ANALYZE de PostgreSQL](postgresql-stats-and-analyze.md)
- (TODO) Fundamentos de replicación en PostgreSQL
- (TODO) Problema de consultas N+1

### Temas relacionados
- [Particionamiento y sharding](../system-design/partitioning-and-sharding.md)
- [Particiones calientes](hot-partitions.md)
- [Contención de lectura/escritura](read-write-contention.md)
- [Planificación de capacidad](../operations/capacity-planning.md)

### Comparar con
- [Escalado de Redis (vertical y horizontal)](redis-scaling.md) — diferentes trade-offs para sistemas en memoria.

## Notas / Bandeja de entrada (opcional)
- N/A
