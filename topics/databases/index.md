---
id: index
title: "Index (Database Index)"
type: concept
status: learning
importance: 85
difficulty: medium
tags: [database, performance]
canonical: true
aliases: ["db-index"]
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Índice (Índice de Base de Datos)

## TL;DR (BLUF)
- Un índice es una estructura de datos que acelera las lecturas a costa de escrituras y almacenamiento.
- Úsalo para filtros selectivos, joins y ordenamiento en consultas críticas.
- Trade-off: demasiados índices o los incorrectos perjudican el rendimiento de escritura y el mantenimiento.

## Definición
**Qué es:** Una estructura que permite a la base de datos localizar filas más rápido que un escaneo completo.
**Términos clave:** selectividad, planificador de consultas, B-Tree, índice compuesto.

## Por qué importa
- A menudo es la diferencia entre consultas rápidas y timeouts en datasets grandes.
- Malas decisiones de indexación pueden degradar silenciosamente el throughput de escritura.

## Alcance y no-objetivos
**Dentro del alcance:** mejorar el rendimiento de lectura para patrones de acceso conocidos.
**Fuera del alcance / NO resuelto por esto:** arreglar consultas mal diseñadas o límites de paginación faltantes.

## Modelo mental / Intuición
- Como el índice de un libro: intercambias páginas extra para encontrar capítulos rápidamente.
- Pagas en escrituras para mantener el índice actualizado.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Una consulta sea lenta y selectiva (WHERE / JOIN / ORDER BY) y aparezca frecuentemente.
- EXPLAIN muestre escaneos costosos.
### Evítalo cuando
- La columna tenga baja selectividad y no haya estrategia de soporte.
- La tabla sea intensiva en escrituras y el índice no sea crítico.

## Cómo lo usaría (práctico)
- **Contexto:** Una API de alto tráfico con endpoints de lectura lentos.
- **Pasos:** identificar consultas lentas → ejecutar EXPLAIN → agregar el índice más pequeño que ayude → medir → monitorear.
- **Cómo se ve el éxito:** menor latencia p95 con impacto mínimo en escrituras.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** reduce la latencia de lectura.
- **Contras / Riesgos:** empeora las escrituras, aumenta el almacenamiento, puede agregar contención.
### Alternativas
- **Vistas materializadas:** para cargas de trabajo de lectura intensiva.
- **Desnormalización:** cuando los joins son el cuello de botella.
- **Caché a nivel de aplicación:** cuando la frescura de datos lo permite.
- **Cómo elegir:** si las lecturas son críticas y selectivas, los índices son generalmente el primer paso.

## Modos de fallo y errores comunes
- Índice no usado porque la consulta no coincide con el orden del índice.
- Inflación del índice por actualizaciones frecuentes.
- Índices redundantes aumentando el costo de escritura sin beneficio.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de consultas p95, ratio de uso de índices, inflación, latencia de escritura.
- **Logs:** logs de consultas lentas, decisiones del planificador.
- **Trazas:** spans de BD mostrando escaneos completos de tabla.
- **Alertas:** picos sostenidos de p95 o latencia de escritura creciente después de cambios de índices.

## Notas de implementación (si aplica)
- **Checklist**
   - [ ] Identificar consultas críticas
   - [ ] Revisar selectividad
   - [ ] Validar con EXPLAIN
   - [ ] Medir impacto en escrituras
- **Notas de rendimiento**
   - Preferir el índice más pequeño que satisfaga la consulta.
- **Notas operacionales**
   - Revisar periódicamente índices sin usar.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** "Indexar todas las cosas".
   - **Por qué es malo:** alto costo de escritura con poca ganancia.
   - **Mejor enfoque:** indexar solo para patrones de consulta conocidos.
- **Anti-patrón:** Ignorar el orden del índice en índices compuestos.
   - **Por qué es malo:** el planificador no puede usar el índice efectivamente.
   - **Mejor enfoque:** ordenar columnas para coincidir con WHERE/JOIN/ORDER BY.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Un índice es como una tabla de búsqueda que acelera las lecturas intercambiando almacenamiento extra y sobrecarga de escritura. Úsalo para consultas frecuentes y selectivas; evítalo para tablas intensivas en escrituras o columnas de baja selectividad.

### Preguntas trampa (con respuestas)
1) **P:** ¿Por qué demasiados índices perjudican al sistema?
    - **R:** Penalizan INSERT/UPDATE/DELETE porque cada índice debe actualizarse.
2) **P:** ¿Un índice siempre se usa?
    - **R:** No; el planificador puede saltárselo si el costo estimado no compensa.
3) **P:** ¿Un índice arregla cualquier consulta lenta?
    - **R:** No; si el filtro no reduce mucho o la consulta está mal diseñada, el índice no ayudará.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir un índice con precisión.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [PostgreSQL](postgresql.md)

### Temas relacionados
- [JSONB](jsonb.md)
- [Índice GIN](gin-index.md)

### Comparar con
- [Índice B-Tree](btree-index.md) — escaneos ordenados y rangos.
- [Índice GIN](gin-index.md) — consultas de contención en JSONB/arrays.
- [GiST](gist-index.md) — tipos especializados (geoespaciales, rangos).
