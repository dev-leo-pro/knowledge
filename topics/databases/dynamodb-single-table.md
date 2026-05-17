---
id: dynamodb-single-table
title: "DynamoDB Single-Table Design"
type: concept
status: learning
importance: 60
difficulty: hard
tags: [database, dynamodb, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Diseño de Tabla Única en DynamoDB

## TL;DR (BLUF)
- El diseño de tabla única almacena múltiples tipos de entidad en una tabla para habilitar diversos patrones de acceso.
- Úsalo cuando necesites rendimiento predecible y modelado dirigido por patrones de acceso.
- Trade-off: mayor complejidad de modelado y diseño de claves cuidadoso.

## Definición
**Qué es:** Un enfoque de modelado en DynamoDB que usa una sola tabla con múltiples tipos de entidad y claves compuestas.
**Términos clave:** clave de partición, clave de ordenamiento, tipos de ítem, patrones de acceso.

## Por qué importa
- Permite consultas eficientes sin joins.
- Un mal diseño lleva a particiones calientes o datos no consultables.

## Alcance y no-objetivos
**Dentro del alcance:** modelado de múltiples tipos de entidad y patrones de acceso en una tabla.
**Fuera del alcance / NO resuelto por esto:** consultas ad-hoc o joins relacionales.

## Modelo mental / Intuición
- Piensa en una tabla única como un grafo de entidades indexado por rutas de acceso.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Puedas definir patrones de acceso claramente y necesites baja latencia.
- Quieras minimizar el número de tablas.
### Evítalo cuando
- Necesites consultas flexibles entre entidades no relacionadas.
- Tus patrones de acceso no sean estables.

## Cómo lo usaría (práctico)
- **Contexto:** Entidades de usuario, pedido y pago en una tabla.
- **Pasos:** definir patrones de acceso → diseñar PK/SK → codificar tipos de entidad → probar consultas.
- **Cómo se ve el éxito:** todos los patrones de acceso servidos por PK/SK o GSIs.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** consultas rápidas, menos tablas, escalable.
- **Contras / Riesgos:** esquema complejo, depuración más difícil.
### Alternativas
- **Diseño multi-tabla:** más simple pero más tablas.
- **Cómo elegir:** elige tabla única cuando los patrones de acceso estén bien definidos y el rendimiento sea crítico.

## Modos de fallo y errores comunes
- Particiones calientes por claves desbalanceadas.
- Tipos de ítem confusos que llevan a errores de consulta.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** throttling, sesgo de particiones, latencia p95.
- **Logs:** fallos de patrones de acceso.
- **Alertas:** throttling creciente o latencia p95.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Listar patrones de acceso
  - [ ] Diseñar PK/SK para cada uno
  - [ ] Codificar tipo de entidad en las claves

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Crear tabla única sin patrones de acceso documentados.
  - **Por qué es malo:** la tabla se vuelve inconsultable.
  - **Mejor enfoque:** comenzar desde los patrones de acceso y diseñar las claves.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El diseño de tabla única en DynamoDB pone múltiples tipos de entidad en una tabla para que todas las consultas sean basadas en claves. Es potente pero demanda una planificación cuidadosa de patrones de acceso.

### Preguntas trampa (con respuestas)
1) **P:** ¿Tabla única siempre es mejor que multi-tabla?
   - **R:** No; agrega complejidad y solo ayuda con patrones de acceso definidos.
2) **P:** ¿Puedes agregar nuevos patrones de acceso sin cambios?
   - **R:** No siempre; puedes necesitar nuevos GSIs o cambios de claves.
3) **P:** ¿Tabla única significa sin duplicación?
   - **R:** No; a menudo duplicas datos para patrones de acceso.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el diseño de tabla única.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo explicar el diseño de claves.
- [ ] Puedo describir un modo de fallo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Patrones de acceso NoSQL](nosql-access-patterns.md)

### Temas relacionados
- [DynamoDB](dynamodb.md)

### Comparar con
- [Diseño multi-tabla en DynamoDB](dynamodb-multi-table.md)
