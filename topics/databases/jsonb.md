---
id: jsonb
title: "JSONB (PostgreSQL)"
type: concept
status: learning
importance: 70
difficulty: medium
tags: [database, postgresql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# JSONB (PostgreSQL)

## TL;DR (BLUF)
- JSONB almacena JSON en formato binario con soporte de operadores e indexación en PostgreSQL.
- Úsalo para campos semi-estructurados o en evolución que aún necesiten transacciones y joins.
- Trade-off: garantías de estructura más débiles y problemas potenciales de rendimiento si se abusa.

## Definición
**Qué es:** Un tipo de dato de PostgreSQL que almacena JSON en formato binario optimizado para consultas e indexación.
**Términos clave:** JSONB, operadores, índice GIN.

## Por qué importa
- Proporciona flexibilidad de esquema sin salir de una base de datos relacional.
- Permite búsqueda sobre claves/rutas JSON con indexación adecuada.

## Alcance y no-objetivos
**Dentro del alcance:** atributos semi-estructurados, metadatos flexibles, campos personalizados.
**Fuera del alcance / NO resuelto por esto:** modelado relacional estricto para esquemas estables e integridad JSON garantizada sin validación extra.

## Modelo mental / Intuición
- Piensa en JSONB como un "sobre flexible" adjunto a una fila relacional.
- Es potente para variabilidad, pero debes decidir qué pertenece a columnas vs JSONB.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tengas atributos en evolución o campos dispersos.
- Aún necesites joins SQL y transacciones ACID.
### Evítalo cuando
- El esquema sea estable y se consulte frecuentemente.
- Requieras cumplimiento de estructura fuerte sin validación adicional.

## Cómo lo usaría (práctico)
- **Contexto:** Feature flags o campos personalizados por tenant.
- **Pasos:** modelar campos estables como columnas → almacenar campos variables en JSONB → agregar [índice GIN](gin-index.md) para rutas de acceso → validar JSON en la app.
- **Cómo se ve el éxito:** esquema flexible con latencia de consulta predecible e integridad de datos mantenible.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** flexibilidad y consultas expresivas.
- **Contras / Riesgos:** desorden sin esquema, carga de validación, costo potencial de rendimiento.
### Alternativas
- **Columnas normales:** mejor integridad y claridad para campos estables.
- **Tabla EAV:** para consultas de atributos complejos (con trade-offs).
- **Cómo elegir:** usa JSONB solo para las partes realmente variables del modelo.

## Modos de fallo y errores comunes
- JSONB crece sin límite, causando inflación y actualizaciones lentas.
- Las consultas se vuelven lentas por índices faltantes o incorrectos.
- Forma inconsistente entre filas lleva a complejidad en la lógica de aplicación.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de consultas en filtros JSONB, uso de índices, inflación.
- **Logs:** logs de consultas lentas para consultas de rutas JSON.
- **Trazas:** spans de BD mostrando operadores JSONB como puntos calientes.
- **Alertas:** latencia creciente para endpoints intensivos en JSONB.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar campos variables vs columnas estables
  - [ ] Agregar [índice GIN](gin-index.md) para rutas de acceso
  - [ ] Validar la forma del JSON en la capa de aplicación
- **Notas de rendimiento**
  - Mantener documentos JSONB pequeños y enfocados.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Almacenar entidades completas en JSONB.
  - **Por qué es malo:** pierdes restricciones relacionales e indexación eficiente.
  - **Mejor enfoque:** mantener campos core como columnas y usar JSONB para campos realmente flexibles.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- JSONB es el tipo JSON binario de PostgreSQL. Es genial para atributos flexibles o en evolución mientras sigues usando SQL, pero traslada la validación a la aplicación y puede perjudicar el rendimiento si se usa para todo.

### Preguntas trampa (con respuestas)
1) **P:** ¿JSONB siempre es más rápido que JSON?
   - **R:** Depende; JSONB permite operaciones/indexación, pero agrega costo de almacenamiento/transformación.
2) **P:** ¿JSONB elimina las migraciones?
   - **R:** Reduce algunas, pero traslada el problema a validación/consistencia a nivel de aplicación.
3) **P:** ¿JSONB se puede indexar?
   - **R:** Sí, con tipos de índice como [índice GIN](gin-index.md) dependiendo de los patrones de acceso.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir JSONB con precisión en 2–3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un caso de uso concreto.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [PostgreSQL](postgresql.md)
- [Índice](index.md)

### Temas relacionados
- [Índice GIN](gin-index.md)

### Comparar con
- [Documentos MongoDB](mongodb-documents.md) — modelo de documentos vs JSONB relacional.
