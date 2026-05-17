---
id: brin-index
title: "BRIN Index (PostgreSQL)"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, postgresql, indexing]
canonical: true
aliases: ["brin"]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Índice BRIN (PostgreSQL)

## TL;DR (BLUF)
- BRIN (Block Range Index) almacena resúmenes min/max por rango de bloques en vez de indexar cada fila.
- Úsalo para tablas grandes con correlación física natural (timestamps, IDs auto-incrementales).
- Trade-off: tamaño de índice diminuto y escrituras rápidas, pero más lento y menos preciso que B-Tree para consultas aleatorias.

## Definición
**Qué es:** Un índice de rango de bloques que almacena metadatos de resumen (valores min/max) para rangos contiguos de bloques, permitiendo la poda rápida de bloques no coincidentes sin indexar cada fila.
**Términos clave:** rango de bloques, correlación física, resumen min/max, tamaño de índice, cargas de trabajo de solo-append.

## Por qué importa
- BRIN reduce dramáticamente el tamaño del índice (a menudo 100-1000x más pequeño que B-Tree) para tablas grandes.
- Es ideal para datos de series temporales o de solo-append donde el orden físico se correlaciona con los filtros de consulta.
- El uso incorrecto en datos ordenados aleatoriamente resulta en escaneos completos de tabla.

## Alcance y no-objetivos
**Dentro del alcance:**
- Tablas grandes con correlación física natural (created_at, IDs auto-incrementales).
- Cargas de trabajo de solo-append con consultas de rango en columnas correlacionadas.
- Reducción de almacenamiento y sobrecarga de escritura para indexación.

**Fuera del alcance / NO resuelto por esto:**
- Búsquedas rápidas en datos ordenados aleatoriamente (usar B-Tree o GIN).
- Consultas de igualdad de alta selectividad (BRIN escanea rangos de bloques completos).
- Operadores de contención o complejos (usar GIN o GiST).

## Modelo mental / Intuición
- Imagina una biblioteca donde los libros están físicamente ordenados por año de publicación. En vez de catalogar cada libro, simplemente etiquetas cada estante: "1990-1995", "1996-2000", etc. Si quieres libros de 1998, saltas los estantes etiquetados antes de 1996.
- BRIN funciona igual: almacena "este rango de bloques contiene valores entre X e Y". Si tu consulta necesita valores fuera de ese rango, se salta el rango de bloques completo.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La tabla sea grande (millones+ de filas) y consultes por una columna con correlación física natural (ej., `created_at`).
- La carga de trabajo sea de solo-append o los datos estén particionados por la columna indexada.
- Necesites reducir el tamaño del índice y la sobrecarga de escritura.
- Los patrones de consulta usen filtros de rango (BETWEEN, >, <) en columnas correlacionadas.

### Evítalo cuando
- Los datos estén ordenados aleatoriamente o se actualicen frecuentemente in-place (la correlación física se rompe).
- Necesites alta selectividad para consultas de igualdad (B-Tree es más rápido).
- Los valores de la columna estén distribuidos uniformemente entre bloques (BRIN no podará efectivamente).
- La tabla sea lo bastante pequeña para que la sobrecarga de B-Tree sea aceptable.

## Cómo lo usaría (práctico)
- **Contexto:** Una tabla de eventos con 100M filas, consultada por rangos de `created_at`, con inserciones cronológicas.
- **Pasos:**
  1. Verificar correlación física: `SELECT ctid, created_at FROM events ORDER BY ctid LIMIT 1000;`
  2. Crear índice BRIN: `CREATE INDEX idx_events_created_brin ON events USING BRIN (created_at);`
  3. Validar con EXPLAIN: asegurar bitmap scans y filtrado de rangos de bloques.
  4. Monitorear rendimiento de consultas vs línea base B-Tree.
- **Cómo se ve el éxito:** 95%+ de reducción en tamaño de índice, escrituras más rápidas, latencia de consulta aceptable en filtros de rango.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:**
  - Tamaño de índice extremadamente pequeño (a menudo < 1% de B-Tree).
  - Sobrecarga de escritura mínima (solo actualiza resúmenes al llenar bloques).
  - Creación y mantenimiento de índice rápidos.
- **Contras / Riesgos:**
  - Más lento y menos preciso que B-Tree para consultas (escanea rangos de bloques completos).
  - Inefectivo si la correlación física es baja.
  - No apto para consultas de igualdad de alta selectividad.
  - Los UPDATEs que mueven filas pueden degradar la correlación con el tiempo.

### Alternativas
- **Índice B-Tree:** cuando los datos estén ordenados aleatoriamente o necesites búsquedas de igualdad rápidas.
- **Particionamiento:** combinar con BRIN por partición para mejor poda.
- **Sin índice:** si las consultas son raras o los escaneos completos son aceptables.
- **Cómo elegir:** usa BRIN para datos grandes, de solo-append y correlacionados; B-Tree para acceso aleatorio o de alta selectividad.

## Modos de fallo y errores comunes
- **La correlación física se degrada:** UPDATEs frecuentes o inserciones aleatorias rompen los límites min/max, reduciendo la eficiencia de poda.
- **BRIN no se usa:** el planificador de consultas ignora el índice si el costo estimado es mayor que seq scan.
- **Elección incorrecta de columna:** indexar una columna distribuida aleatoriamente resulta en cero poda de bloques.
- **Autovacuum insuficiente:** datos nuevos no reflejados en resúmenes BRIN hasta que se ejecuta la sumarización.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tamaño de índice vs comparación con B-Tree.
  - Latencia de consultas en filtros de rango.
  - Conteos de bitmap heap scan y bloques podados (de EXPLAIN).
- **Logs:** logs de consultas lentas para consultas de rango en columnas con índice BRIN.
- **Trazas:** spans de consultas de BD mostrando escaneos completos en vez de uso de índice.
- **Alertas:** aumento sostenido de latencia en consultas de rango temporal después de que la correlación de datos se degrada.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Verificar correlación física con inspección de `ctid`
  - [ ] Crear índice BRIN en columna correlacionada
  - [ ] Validar con EXPLAIN ANALYZE
  - [ ] Monitorear tamaño de índice y rendimiento de consultas
  - [ ] Programar VACUUM periódico o autovacuum para actualizar resúmenes
- **Notas de rendimiento**
  - Configurar `pages_per_range` según tamaño de tabla y correlación (por defecto 128).
  - Combinar con particionamiento de tabla para mejor poda.
  - Evitar en columnas con UPDATEs aleatorios frecuentes.
- **Notas operacionales**
  - BRIN requiere autovacuum o VACUUM manual para sumarizar nuevos bloques.
  - Usar `brin_summarize_new_values(index_oid)` para forzar actualizaciones de resumen.

## Mini ejemplo (si aplica)
```sql
-- Tabla grande de eventos con inserciones cronológicas
CREATE TABLE events (
  id BIGSERIAL,
  created_at TIMESTAMPTZ NOT NULL,
  event_data JSONB
);

-- Índice BRIN en created_at (asumiendo correlación física)
CREATE INDEX idx_events_created_brin ON events USING BRIN (created_at);

-- Consulta: eventos de los últimos 7 días
EXPLAIN ANALYZE
SELECT * FROM events
WHERE created_at >= NOW() - INTERVAL '7 days';

-- Plan esperado: Bitmap Heap Scan + Bitmap Index Scan (BRIN poda bloques antiguos)
```

## Anti-patrones comunes
- **Anti-patrón:** Usar BRIN en datos ordenados aleatoriamente (ej., user_id en tabla multi-tenant).
  - **Por qué es malo:** sin correlación física → todos los bloques escaneados → sin beneficio.
  - **Mejor enfoque:** usar [índice B-Tree](btree-index.md) o particionar por user_id.

- **Anti-patrón:** Crear BRIN "por si acaso" sin verificar correlación.
  - **Por qué es malo:** desperdicia recursos y confunde al planificador de consultas.
  - **Mejor enfoque:** analizar `ctid` vs valores de columna primero; solo agregar BRIN si la correlación es fuerte.

- **Anti-patrón:** Esperar que BRIN reemplace B-Tree para todas las consultas.
  - **Por qué es malo:** BRIN está optimizado para escaneos de rango en datos correlacionados, no para indexación de propósito general.
  - **Mejor enfoque:** usar BRIN para consultas de rango correlacionadas; mantener B-Tree para igualdad/acceso aleatorio.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
BRIN es un índice eficiente en espacio de PostgreSQL que almacena resúmenes min/max para rangos de bloques en vez de indexar cada fila. Es ideal para tablas grandes, de solo-append con correlación física natural — como timestamps o IDs auto-incrementales. El trade-off es menor tamaño de índice y escrituras más rápidas, pero consultas más lentas y menos precisas comparado con B-Tree.

### Preguntas trampa (con respuestas)
1) **P:** ¿BRIN siempre es mejor que B-Tree porque es más pequeño?
   - **R:** No. BRIN solo es efectivo cuando los datos tienen fuerte correlación física. Para datos ordenados aleatoriamente, no proporciona beneficio de poda y B-Tree será mucho más rápido.

2) **P:** ¿BRIN se actualiza automáticamente cuando se insertan nuevas filas?
   - **R:** No inmediatamente. Los resúmenes BRIN se actualizan durante autovacuum o VACUUM manual. Puedes forzar actualizaciones con `brin_summarize_new_values()`.

3) **P:** ¿BRIN puede acelerar consultas de igualdad como `WHERE id = 12345`?
   - **R:** No de forma fiable. BRIN escanea rangos de bloques completos que podrían contener el valor. B-Tree es mucho mejor para consultas de igualdad de alta selectividad.

4) **P:** ¿Qué pasa con la efectividad de BRIN si hago UPDATE de filas frecuentemente?
   - **R:** La correlación física puede degradarse, especialmente si los UPDATEs causan movimiento de filas. Esto reduce la eficiencia de poda de bloques, haciendo BRIN menos útil con el tiempo.

5) **P:** ¿Debo usar BRIN en tablas pequeñas?
   - **R:** No. Los beneficios de BRIN (tamaño de índice pequeño, baja sobrecarga de escritura) solo importan para tablas grandes. En tablas pequeñas, la sobrecarga de B-Tree es despreciable y mucho más rápida.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir BRIN con precisión en 2–3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no (correlación + tamaño).
- [ ] Puedo explicar al menos 2 trade-offs (tamaño vs precisión, escrituras vs lecturas).
- [ ] Puedo dar un ejemplo concreto de memoria (columna timestamp en tabla grande).
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo (correlación se degrada → consultas lentas).

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Índice](index.md)
- [Índice B-Tree](btree-index.md)

### Temas relacionados
- [PostgreSQL MVCC](postgresql-mvcc.md)
- [PostgreSQL vacuum y autovacuum](postgresql-vacuum-autovacuum.md)
- [EXPLAIN](explain.md)

### Comparar con
- [Índice B-Tree](btree-index.md) — BRIN intercambia velocidad de consulta por tamaño de índice en datos correlacionados; B-Tree es más rápido pero más grande.
- [Índice GIN](gin-index.md) — GIN para consultas de contención; BRIN para consultas de rango en columnas correlacionadas.

## Notas / Bandeja de entrada (opcional)
- Considerar agregar ejemplo mostrando análisis de correlación `ctid`.
- Mencionar ajuste de `pages_per_range` para diferentes tamaños de tabla.
