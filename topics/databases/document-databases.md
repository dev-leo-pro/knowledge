---
id: document-databases
title: "Bases de Datos de Documentos (Visión General)"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Bases de Datos de Documentos (Visión General)

## TL;DR (BLUF)
- Las bases de datos de documentos almacenan datos como documentos tipo JSON con esquema flexible.
- Úsalas para datos jerárquicos y evolución rápida de esquema.
- Trade-off: joins limitados y trade-offs de consistencia.

## Definición
**Qué es:** Un modelo de base de datos donde cada registro es un documento con campos anidados.
**Términos clave:** documento, embedding, referenciación, flexibilidad de esquema.

## Por qué importa
- Permite iteración más rápida para datos de producto en evolución.
- Un modelado pobre puede causar duplicación y datos inconsistentes.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos del modelo de documentos y trade-offs.
**Fuera del alcance / NO resuelto por esto:** comportamiento específico del proveedor (detalles de MongoDB).

## Modelo mental / Intuición
- Un documento es un objeto autocontenido que representa una entidad del mundo real.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los datos son naturalmente jerárquicos y se leen juntos.
- Necesitas esquemas flexibles con iteración rápida.
### Evítalo cuando
- Necesitas restricciones relacionales fuertes y joins.

## Cómo lo usaría (práctico)
- **Contexto:** Catálogo de productos con atributos variables.
- **Pasos:** decidir embed vs referencia → indexar campos críticos → imponer esquema en la app.
- **Cómo se ve el éxito:** baja latencia de consulta con mínima duplicación.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** esquema flexible, modelado anidado natural.
- **Desventajas / Riesgos:** duplicación, restricciones más débiles.
### Alternativas
- **PostgreSQL + JSONB:** campos flexibles con restricciones relacionales.
- **Cómo elegir:** usar documentos cuando las lecturas anidadas dominan y el esquema cambia frecuentemente.

## Modos de fallo y trampas
- Sobre-embedding llevando a documentos grandes.
- Referencias excesivas causando múltiples viajes de ida y vuelta.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** crecimiento del tamaño de documentos, latencia de consultas.
- **Logs:** logs de consultas lentas.
- **Alertas:** latencia creciente en consultas de documentos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir reglas de embed vs referencia
  - [ ] Indexar campos de alto tráfico

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Embeber todo sin límites de tamaño.
  - **Por qué es malo:** documentos grandes y escrituras lentas.
  - **Mejor enfoque:** embeber solo lo que se lee junto.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las bases de datos de documentos almacenan documentos tipo JSON, lo que las hace ideales para datos jerárquicos y cambios de esquema. El costo es menos restricciones y joins más difíciles.

### Preguntas trampa (con respuestas)
1) **P:** ¿Las bases de datos de documentos eliminan la necesidad de diseño de esquema?
   - **R:** no; aún diseñas para patrones de acceso.
2) **P:** ¿Las bases de datos de documentos son siempre más rápidas?
   - **R:** no; el rendimiento depende del indexado y modelado.
3) **P:** ¿Se pueden imponer restricciones relacionales fácilmente?
   - **R:** no como en bases de datos relacionales.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir una base de datos de documentos.
- [ ] Puedo indicar cuándo usarla.
- [ ] Puedo explicar un trade-off.
- [ ] Puedo dar un ejemplo de modelado.
- [ ] Puedo nombrar una trampa.

## Enlaces (SIN duplicación)
### Prerequisitos
- [NoSQL access patterns](nosql-access-patterns.md)

### Temas relacionados
- [MongoDB documents](mongodb-documents.md)

### Comparar con
- [PostgreSQL](postgresql.md) — restricciones relacionales vs flexibilidad de documentos.
