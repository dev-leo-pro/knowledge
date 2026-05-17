---
id: dynamodb-multi-table
title: "DynamoDB Multi-Table Design"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, dynamodb, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Diseño Multi-Tabla en DynamoDB

## TL;DR (BLUF)
- El diseño multi-tabla modela cada entidad o patrón de acceso en su propia tabla.
- Úsalo cuando los patrones de acceso sean simples o los equipos necesiten aislamiento.
- Trade-off: más tablas y sobrecarga operacional.

## Definición
**Qué es:** Un enfoque de modelado en DynamoDB que usa múltiples tablas para diferentes entidades o patrones de acceso.
**Términos clave:** tabla-por-entidad, patrón de acceso, aislamiento.

## Por qué importa
- Es más simple de razonar pero puede limitar consultas entre entidades.
- Puede reducir la complejidad comparado con el diseño de tabla única.

## Alcance y no-objetivos
**Dentro del alcance:** cuándo dividir en múltiples tablas.
**Fuera del alcance / NO resuelto por esto:** consultas ad-hoc entre entidades.

## Modelo mental / Intuición
- Piensa "una tabla por concepto de dominio".

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los patrones de acceso sean directos y no necesiten joins entre entidades.
- Quieras aislamiento por equipo o dominio.
### Evítalo cuando
- Necesites múltiples patrones de acceso entre entidades en una tabla.

## Cómo lo usaría (práctico)
- **Contexto:** Tablas separadas para usuarios y pedidos.
- **Pasos:** definir patrones de acceso por tabla → diseñar claves → mantener GSIs al mínimo.
- **Cómo se ve el éxito:** consultas simples con baja sobrecarga operacional.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** diseño y depuración más simples.
- **Contras / Riesgos:** más tablas, menos flexibilidad.
### Alternativas
- **Diseño de tabla única:** más potente pero complejo.
- **Cómo elegir:** usa multi-tabla cuando la simplicidad y el aislamiento sean prioridades.

## Modos de fallo y errores comunes
- Datos duplicados entre tablas para patrones de acceso.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** throttling por tabla, latencia.
- **Logs:** fallos de patrones de acceso.
- **Alertas:** picos de throttling por tabla.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir patrones de acceso por tabla
  - [ ] Mantener GSIs al mínimo

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Crear muchas tablas para variaciones menores.
  - **Por qué es malo:** dispersión operacional.
  - **Mejor enfoque:** consolidar donde los patrones de acceso se solapen.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El diseño multi-tabla en DynamoDB mantiene cada entidad en su propia tabla, lo cual es más simple pero menos flexible. El diseño de tabla única es más potente pero complejo.

### Preguntas trampa (con respuestas)
1) **P:** ¿Multi-tabla siempre es más simple?
   - **R:** A menudo sí, pero aún puede ser complejo si los patrones de acceso se solapan.
2) **P:** ¿Multi-tabla elimina la duplicación de datos?
   - **R:** No; aún puedes duplicar para patrones de acceso.
3) **P:** ¿Se requiere tabla única para escalar?
   - **R:** No; multi-tabla también puede escalar.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el diseño multi-tabla.
- [ ] Puedo compararlo con tabla única.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un caso de uso.
- [ ] Puedo identificar un error común.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Patrones de acceso NoSQL](nosql-access-patterns.md)

### Temas relacionados
- [Diseño de tabla única en DynamoDB](dynamodb-single-table.md)

### Comparar con
- [Diseño de tabla única en DynamoDB](dynamodb-single-table.md) — flexibilidad vs simplicidad.
