---
id: gin-index
title: "GIN index (PostgreSQL)"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, postgresql, indexing]
canonical: true
aliases: ["gin"]
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Índice GIN (PostgreSQL)

## TL;DR (BLUF)
- GIN es un índice invertido optimizado para buscar dentro de colecciones (arrays/JSONB).
- Úsalo cuando consultes claves/rutas JSONB o contenido de arrays.
- Trade-off: mayor costo de escritura y mantenimiento; los operadores deben coincidir con el índice.

## Definición
**Qué es:** Un Índice Invertido Generalizado que mapea elementos a filas, permitiendo consultas rápidas de contención y membresía.
**Términos clave:** índice invertido, operadores, JSONB, contención de arrays.

## Por qué importa
- Desbloquea consultas eficientes sobre contenido interno donde B-Tree es inefectivo.
- Es la elección de índice estándar para consultas de contención JSONB.

## Alcance y no-objetivos
**Dentro del alcance:** consultas de contención/membresía en JSONB y arrays.
**Fuera del alcance / NO resuelto por esto:** consultas de igualdad/rango escalares y mal modelado de datos.

## Modelo mental / Intuición
- Imagina un mapa de etiqueta-a-documento: cada elemento apunta a todas las filas que lo contienen.
- Genial para consultas "contiene X"; no ideal para igualdad simple en columnas escalares.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Consultes JSONB por claves/rutas con operadores que GIN soporta.
- Tu patrón de acceso sea "contiene X dentro de la estructura".
### Evítalo cuando
- Raramente consultes contenido interno.
- Tu carga de trabajo sea extremadamente intensiva en escrituras y el costo del índice sea demasiado alto.

## Cómo lo usaría (práctico)
- **Contexto:** Una tabla con atributos JSONB consultados por clave/valor.
- **Pasos:** identificar patrones de acceso JSONB → crear índice GIN → validar con EXPLAIN → monitorear impacto en escrituras.
- **Cómo se ve el éxito:** consultas JSONB más rápidas sin degradación inaceptable de escrituras.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** búsquedas internas rápidas para JSONB/arrays.
- **Contras / Riesgos:** amplificación de escritura, costo de mantenimiento, sensibilidad a operadores.
### Alternativas
- **B-Tree:** mejor para igualdad/rango en columnas escalares.
- **GiST:** útil para otros tipos de datos (ej., geoespaciales).
- **Cómo elegir:** elige GIN para consultas de contención; de lo contrario considera B-Tree/GiST.

## Modos de fallo y errores comunes
- Índice no usado debido a desajuste de operadores.
- Ralentización significativa de escrituras por actualizaciones intensivas.
- Inflación del índice por modificaciones frecuentes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de consultas para filtros JSONB, latencia de escritura, crecimiento del tamaño del índice.
- **Logs:** logs de consultas lentas para operadores JSONB.
- **Trazas:** spans de BD mostrando consultas JSONB como puntos calientes.
- **Alertas:** aumento sostenido de latencia de escritura después de la creación del índice.

## Notas de implementación (si aplica)
- **Checklist**
   - [ ] Confirmar patrones de acceso JSONB y operadores
   - [ ] Crear índice GIN y validar con EXPLAIN
   - [ ] Medir impacto en escrituras
- **Notas de rendimiento**
   - Limitar tamaño de JSONB y evitar actualizaciones innecesarias.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Crear GIN "por si acaso".
   - **Por qué es malo:** sobrecarga de escritura sin beneficio en consultas.
   - **Mejor enfoque:** agregar solo para patrones de acceso probados.
- **Anti-patrón:** Usar JSONB para todo y depender de GIN para arreglarlo.
   - **Por qué es malo:** enmascara mal modelado y perjudica las escrituras.
   - **Mejor enfoque:** modelar campos estables como columnas y usar JSONB para datos realmente variables.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- GIN es un índice invertido en PostgreSQL que es genial para buscar dentro de JSONB o arrays. Es rápido para consultas de contención pero más costoso en escrituras y solo ayuda cuando los operadores coinciden con el índice.

### Preguntas trampa (con respuestas)
1) **P:** ¿GIN siempre es mejor que B-Tree?
    - **R:** No; depende del tipo de acceso. B-Tree es ideal para igualdad/rangos en valores escalares.
2) **P:** ¿Si tengo GIN, ya no necesito modelar?
    - **R:** Falso; los índices ayudan, pero no arreglan un modelo/consultas pobres.
3) **P:** ¿GIN no afecta las escrituras?
    - **R:** Sí las afecta: INSERT/UPDATE deben actualizar las estructuras del índice.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir GIN con precisión.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de uso.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Índice](index.md)
- [JSONB](jsonb.md)
- [PostgreSQL](postgresql.md)

### Temas relacionados
- [Índice B-Tree](btree-index.md)
- [GiST](gist-index.md)
- [Selectividad](selectivity.md)
- [EXPLAIN](explain.md)
