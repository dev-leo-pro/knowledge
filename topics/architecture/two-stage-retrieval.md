---
id: two-stage-retrieval
title: "Two-Stage Retrieval"
type: pattern
status: learning
importance: 70
difficulty: medium
tags: [retrieval,search,cache,architecture]
canonical: true
aliases: []
created_at: 2026-02-23
updated_at: 2026-02-23
---

# Recuperación en Dos Etapas

## TL;DR (BLUF)
- La recuperación en dos etapas significa: usar una primera etapa barata y de grano grueso para reducir candidatos, luego aplicar una segunda etapa costosa y precisa sobre ese conjunto más pequeño.
- Úsala cuando las búsquedas de precisión total sean costosas o grandes (ej., búsqueda vectorial, texto difuso, rangos de fechas). Trade-off: complejidad extra a cambio de grandes mejoras de rendimiento.

## Definición
**Qué es:** Un patrón de recuperación donde las consultas se ejecutan en dos fases: un filtro rápido, aproximado o grueso (etapa 1) que devuelve un conjunto de candidatos, seguido de un re-ranking/filtro costoso, exacto o de mayor fidelidad (etapa 2) aplicado solo a esos candidatos.
**Términos clave:** generación de candidatos, re-ranking, filtro grueso, filtro exacto, índice, locality-sensitive hashing (LSH), índice invertido, bloom filter.

## Por qué importa
- Reduce el trabajo de CPU/IO evitando operaciones costosas sobre el dataset completo.
- Permite resultados de baja latencia para consultas complejas (búsqueda semántica, coincidencia difusa, grafos grandes) mientras mantiene alta precisión al reverificar candidatos.

## Alcance y no-objetivos
**Dentro del alcance:** problemas de búsqueda y recuperación en texto, vectores, series temporales, cachés y grafos donde los conjuntos de candidatos pueden podarse de forma barata.
**Fuera del alcance:** recuperación exacta de una sola etapa donde el tamaño de los datos es lo bastante pequeño para escanear de forma barata.

## Modelo mental / Intuición
- Piénsalo como "tamiz grueso + peine fino": el tamiz descarta la mayoría de ítems irrelevantes de forma barata; el peine inspecciona los restantes cuidadosamente.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La operación exacta sea costosa (scoring pesado, distancias vectoriales, expansión de grafos).
- El conjunto de candidatos pueda aproximarse eficientemente (ej., buckets temporales gruesos, índices de prefijo, ANN vectorial, particiones).
### Evítalo cuando
- El dataset sea lo bastante pequeño para que las consultas exactas sean baratas.
- El filtro grueso frecuentemente omita verdaderos positivos (alta pérdida de recall).

## Cómo lo usaría (práctico)
- **Contexto:** Un event store grande donde los usuarios consultan rangos de timestamps precisos y similitud de contenido.
- **Pasos:** 1) Construir un índice grueso (buckets por día, claves de prefijo, índice ANN) 2) Consultar índice grueso para obtener candidatos 3) Re-filtrar/re-rankear candidatos en memoria usando criterios exactos 4) Devolver resultados ordenados
- **Cómo se ve el éxito:** 90% de reducción en lecturas de disco y latencia p95 sub-100ms para consultas típicas.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** Menor latencia, I/O y cómputo reducidos, escalable a corpus grandes.
- **Contras:** Más complejidad, potencial pérdida de recall si la etapa gruesa es demasiado agresiva, depuración/observabilidad más difícil.
### Alternativas
- Índice exacto de una sola etapa (más simple, pero mayor costo). Usar cuando los datos sean pequeños.
- Vistas materializadas para consultas comunes (rápido pero alto mantenimiento).

## Modos de fallo y errores comunes
- El índice grueso pierde verdaderos positivos (mal recall) — ajustar para favorecer recall.
- Conjunto de candidatos demasiado grande (etapa gruesa inefectiva) — rediseñar índice grueso.
- Índices desactualizados entre etapas (problemas de consistencia) — asegurar actualizaciones sincronizadas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** ratio de reducción de candidatos (conteo_grueso / conteo_total), recall @k, latencia p95, tasa de errores.
- **Logs:** parámetros de consulta gruesa, conteos de candidatos, tiempo de re-ranking por consulta.
- **Trazas:** span para etapa gruesa + span para procesamiento de segunda etapa para encontrar cuellos de botella.

## Notas de implementación (si aplica)
- Checklist:
  - [ ] Asegurar que el índice grueso favorezca recall sobre precisión.
  - [ ] Medir reducción de candidatos y latencia end-to-end.
  - [ ] Agregar alertas si los conteos de candidatos superan umbrales.

## Mini ejemplo (pseudocódigo)
```pseudo
candidates = coarse_index.query(q)
results = []
for c in candidates:
  score = exact_score(c, q)
  if score >= threshold: results.append((c, score))
return sort_by_score(results)
```

## Anti-patrones comunes
- **Anti-patrón:** Filtros gruesos agresivos que optimizan latencia pero descartan muchos verdaderos positivos.
  - **Por qué es malo:** reduce el recall y la satisfacción del usuario.
  - **Mejor enfoque:** ajustar filtro grueso o aumentar la ventana de candidatos.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La recuperación en dos etapas significa que primero encuentras un conjunto pequeño de posibles coincidencias rápidamente, luego haces la verificación exacta costosa solo sobre esos — como revisar solo una lista corta de candidatos en vez de a todos.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cómo eliges entre recall y precisión para la etapa gruesa?
   - **R:** Prefiere recall para la etapa gruesa; ajusta umbrales y mide recall@k. Ajusta la ventana de candidatos o expande los buckets si el recall cae.
2) **P:** ¿Cómo mantienes las etapas consistentes con las actualizaciones?
   - **R:** Usa escrituras síncronas a ambos índices o diseña verificaciones de consistencia eventual; incluye números de secuencia/timestamps para detectar obsolescencia.
3) **P:** ¿Cuándo la recuperación en dos etapas agrega complejidad innecesaria?
   - **R:** Cuando los datos son pequeños o las consultas exactas ya son rápidas — entonces la búsqueda exacta de una sola etapa puede ser más simple y barata en general.

## Auto-verificación rápida (5 ítems)
- [ ] Puedo definirlo con precisión en 2–3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Pipeline de Búsqueda e Indexación](system-design/archetype-search-indexing.md)
- [Fundamentos de caché](system-design/caching-fundamentals.md)

### Temas relacionados
- [Vistas Materializadas / CQRS](system-design/archetype-materialized-views.md)
- [Búsqueda vectorial / ANN (TODO)](topics/_aliases/ann.md)
- [Índice invertido (TODO)](topics/_aliases/inverted-index.md)

### Comparar con
- [Arquitectura Kappa](architecture/kappa-architecture.md) — recuperación en dos etapas es un patrón de indexación/consulta; Kappa es una arquitectura de procesamiento de datos.

## Notas / Bandeja de entrada (opcional)
- Ejemplos y archivos de resolución de problemas guardados en `assessments/problem-solving/`.
