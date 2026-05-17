---
id: dynamodb
title: "DynamoDB"
type: technology
status: learning
importance: 70
difficulty: medium
tags: [database, nosql, aws]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# DynamoDB

## TL;DR (BLUF)
- Base de datos NoSQL gestionada clave-valor/documento optimizada para baja latencia predecible a escala.
- Úsala cuando puedes diseñar patrones de acceso de antemano y necesitas escala masiva con operaciones mínimas.
- Trade-off: el modelado es orientado a patrones de acceso; las consultas ad-hoc complejas son limitadas.

## Definición
**Qué es:** Una base de datos NoSQL completamente gestionada con almacenamiento particionado y escalado basado en throughput.
**Términos clave:** clave de partición, clave de ordenación, GSI/LSI, patrones de acceso, consistencia eventual.

## Por qué importa
- Entrega rendimiento predecible y escalabilidad con baja carga operacional.
- Un diseño pobre de patrones de acceso puede llevar a particiones calientes y refactorizaciones costosas.

## Alcance y no-objetivos
**Dentro del alcance:** acceso clave-valor, alta escala, latencia predecible.
**Fuera del alcance / NO resuelto por esto:** joins relacionales complejos o analítica ad-hoc.

## Modelo mental / Intuición
- Piensa "una tabla, muchos patrones de acceso", modelado alrededor de consultas, no entidades.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Puedes expresar el acceso a datos como búsquedas basadas en claves y consultas de rango.
- Necesitas alta escala con carga operacional mínima.
### Evítalo cuando
- Necesitas joins complejos o consultas SQL flexibles.
- Tus patrones de acceso no están claros o cambian frecuentemente.

## Cómo lo usaría (práctico)
- **Contexto:** Servicio de alto tráfico con búsquedas basadas en claves.
- **Pasos:** definir patrones de acceso → diseñar PK/SK → agregar GSIs → pruebas de carga en particiones calientes.
- **Cómo se ve el éxito:** latencia consistente con distribución de particiones estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** escalado gestionado, baja latencia, alta disponibilidad.
- **Desventajas / Riesgos:** flexibilidad de consulta limitada; rediseños costosos para nuevos patrones de acceso.
### Alternativas
- **PostgreSQL:** flexibilidad relacional con mayor carga operacional.
- **MongoDB:** consultas más flexibles, modelo de consistencia diferente.
- **Cómo elegir:** elige DynamoDB cuando los patrones de acceso son estables y la escala es crítica.

## Modos de fallo y trampas
- Particiones calientes por claves sesgadas.
- Throttling inesperado debido a picos de tráfico.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** eventos de throttle, RCUs/WCUs consumidas, latencia p95.
- **Logs:** patrones de acceso causando claves calientes.
- **Alertas:** picos de throttling o latencia creciente.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir patrones de acceso de antemano
  - [ ] Elegir PK/SK para evitar particiones calientes
  - [ ] Agregar GSIs con moderación
- **Notas de rendimiento**
  - Operaciones por lotes y limitar el tamaño de ítems.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Modelar entidades primero, consultas después.
  - **Por qué es malo:** lleva a rediseños costosos.
  - **Mejor enfoque:** empezar desde los patrones de acceso.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- DynamoDB es una base de datos NoSQL gestionada optimizada para acceso basado en claves de baja latencia a escala. Funciona mejor cuando puedes diseñar tu esquema alrededor de patrones de acceso conocidos; de lo contrario pagarás con complejidad y refactorizaciones.

### Preguntas trampa (con respuestas)
1) **P:** ¿DynamoDB soporta joins SQL ad-hoc?
   - **R:** no; es orientada a clave-valor/documento sin joins.
2) **P:** ¿Puedes arreglar particiones calientes sin cambiar claves?
   - **R:** no de forma confiable; usualmente necesitas rediseñar claves o agregar sharding.
3) **P:** ¿Siempre es eventualmente consistente?
   - **R:** no; puedes elegir lecturas fuertemente consistentes (con trade-offs).

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir DynamoDB con precisión.
- [ ] Puedo indicar cuándo usarla y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de patrón de acceso.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Index](index.md)

### Temas relacionados
- [PostgreSQL](postgresql.md)

### Comparar con
- [PostgreSQL](postgresql.md) — SQL relacional vs acceso clave-valor.
- [MongoDB documents](mongodb-documents.md) — consultas de documento vs diseño de patrón de acceso.
