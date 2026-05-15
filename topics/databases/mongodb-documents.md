---
id: mongodb-documents
title: "Documentos de MongoDB"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, nosql, documents]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Documentos de MongoDB

## TL;DR (BLUF)
- El modelo de documentos almacena datos flexibles tipo JSON con estructuras embebidas.
- Úsalo cuando tus datos son naturalmente jerárquicos y necesitas esquema flexible.
- Trade-off: los joins son limitados; el modelado de datos se centra en embeber vs referenciar.

## Definición
**Qué es:** Un modelo de datos orientado a documentos donde cada registro es un documento tipo JSON con campos anidados.
**Términos clave:** embedding, referencing, esquema de documento, pipeline de agregación.

## Por qué importa
- Permite iteración rápida con esquemas flexibles.
- Malas decisiones de modelado pueden causar duplicación o ineficiencia en consultas.

## Alcance y no-objetivos
**Dentro del alcance:** patrones de modelado de documentos específicos de MongoDB.
**Fuera del alcance / NO resuelto por esto:** conceptos generales de bases de datos de documentos.

## Modelo mental / Intuición
- Piensa en cada documento como un objeto autocontenido, como un blob JSON con estructura.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los datos son naturalmente jerárquicos y se leen juntos.
- Necesitas flexibilidad de esquema para cambios rápidos.
### Evítalo cuando
- Necesitas joins complejos o restricciones relacionales estrictas.
- Necesitas transacciones fuertes multi-entidad a través de muchas colecciones.

## Cómo lo usaría (práctico)
- **Contexto:** Catálogo de productos con atributos variables por categoría.
- **Pasos:** elegir embeber vs referenciar → diseñar índices → validar esquema a nivel de aplicación.
- **Cómo se ve el éxito:** baja latencia de consultas con mínima duplicación.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** esquema flexible, modelado natural para datos anidados.
- **Desventajas / Riesgos:** duplicación de datos, joins limitados, posible inconsistencia.
### Alternativas
- **PostgreSQL + JSONB:** campos flexibles con restricciones relacionales.
- **DynamoDB:** modelado orientado por patrones de acceso.
- **Cómo elegir:** usa documentos cuando las lecturas jerárquicas dominan y el esquema evoluciona.

## Modos de fallo y trampas
- Sobre-embedding causando documentos enormes y actualizaciones lentas.
- Referenciación excesiva llevando a múltiples viajes de ida y vuelta.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de consultas, crecimiento del tamaño de documentos, uso de índices.
- **Logs:** logs de consultas lentas y métricas del pipeline de agregación.
- **Alertas:** latencia de consultas en aumento o picos en el tamaño de documentos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Decidir embeber vs referenciar por patrón de acceso
  - [ ] Agregar índices para consultas comunes
  - [ ] Aplicar esquema con validación

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Embeber todo sin límites.
  - **Por qué es malo:** documentos enormes y sobrecarga de actualización.
  - **Mejor enfoque:** embeber solo lo que se lee junto.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El modelo de documentos de MongoDB almacena datos flexibles, anidados, tipo JSON. Es genial cuando tus datos son jerárquicos y evolucionan, pero debes equilibrar embeber vs referenciar y aceptar menos garantías relacionales.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los documentos siempre son mejores que las tablas relacionales?
   - **R:** no; intercambian restricciones relacionales por flexibilidad.
2) **P:** ¿Embeber siempre mejora el rendimiento?
   - **R:** no siempre; documentos grandes pueden ralentizar escrituras y actualizaciones.
3) **P:** ¿Se puede evitar el diseño de esquema en MongoDB?
   - **R:** no; aún necesitas diseñar para patrones de acceso y consistencia.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el modelo de documentos con precisión.
- [ ] Puedo decir cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo de embeber vs referenciar.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Bases de datos de documentos (visión general)](document-databases.md)

### Temas relacionados
- [JSONB](jsonb.md)

### Comparar con
- [PostgreSQL](postgresql.md) — restricciones relacionales vs flexibilidad de esquema.
- [DynamoDB](dynamodb.md) — modelado por patrón de acceso vs consultas de documentos.
