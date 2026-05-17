---
id: sql-joins
title: "SQL Joins (Inner/Left)"
type: concept
status: learning
importance: 65
difficulty: easy
tags: [database, sql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# SQL Joins (Inner/Left)

## TL;DR (BLUF)
- Los joins combinan filas de dos tablas basándose en un predicado.
- Usa INNER para solo filas coincidentes; LEFT para mantener todas las filas de la tabla izquierda.
- Trade-off: los joins pueden ser costosos sin índices y buenos predicados.

## Definición
**Qué es:** Una operación relacional que combina filas de tablas usando una condición de unión.
**Términos clave:** INNER JOIN, LEFT JOIN, predicado de join, cardinalidad.

## Por qué importa
- Los joins son centrales al modelado relacional y la expresividad de consultas.
- Joins mal usados causan resultados incorrectos y problemas de rendimiento.

## Alcance y no-objetivos
**Dentro del alcance:** INNER y LEFT joins, corrección y fundamentos de rendimiento.
**Fuera del alcance / NO resuelto por esto:** algoritmos avanzados de join e internos del planificador de consultas.

## Modelo mental / Intuición
- Piensa en unir como "emparejar filas" basándose en una clave.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas datos relacionados de múltiples tablas.
- Tienes claves claras y predicados de join.
### Evítalo cuando
- Una sola tabla puede responder la consulta con filtros más simples.
- El predicado de join no es selectivo y causa conjuntos de resultados enormes.

## Cómo lo usaría (práctico)
- **Contexto:** Tablas de usuarios y pedidos.
- **Pasos:** elegir INNER vs LEFT → unir por claves indexadas → validar conteos de filas.
- **Cómo se ve el éxito:** resultados correctos con rendimiento estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** datos normalizados y consultas flexibles.
- **Desventajas / Riesgos:** costo de rendimiento en joins grandes.
### Alternativas
- **Desnormalización:** pre-unir datos cuando las lecturas dominan.
- **Cómo elegir:** usa joins cuando la corrección importa y los datos están normalizados.

## Modos de fallo y trampas
- Productos cartesianos accidentales por predicados faltantes.
- LEFT join convirtiéndose en INNER debido a filtros WHERE en la tabla derecha.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de consultas, filas escaneadas.
- **Logs:** logs de consultas lentas.
- **Alertas:** picos repentinos en tiempo de ejecución de consultas.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Asegurar que las claves de join estén indexadas
  - [ ] Verificar conteos de filas esperados

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Unir sin claves indexadas.
  - **Por qué es malo:** escaneos completos y consultas lentas.
  - **Mejor enfoque:** indexar columnas de join.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los SQL joins combinan datos de tablas relacionadas. INNER devuelve solo coincidencias; LEFT mantiene todas las filas izquierdas incluso si no hay coincidencia. Predicados correctos e índices mantienen los joins rápidos.

### Preguntas trampa (con respuestas)
1) **P:** ¿LEFT JOIN siempre devuelve más filas que INNER?
   - **R:** no necesariamente; puede ser igual si todas las filas coinciden.
2) **P:** ¿Un LEFT JOIN puede convertirse en INNER JOIN accidentalmente?
   - **R:** sí, si filtras columnas de la tabla derecha en WHERE.
3) **P:** ¿Los joins siempre son lentos?
   - **R:** no; con índices y selectividad adecuados pueden ser rápidos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir INNER vs LEFT joins.
- [ ] Puedo decir cuándo usar cada uno.
- [ ] Puedo explicar un trade-off de rendimiento.
- [ ] Puedo describir una trampa común.
- [ ] Puedo nombrar una señal de detección.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de SQL](sql-foundations.md)

### Temas relacionados
- [Índice](index.md)

### Comparar con
- [Normalización](normalization.md) — los modelos normalizados dependen de joins.
