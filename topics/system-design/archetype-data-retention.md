---
id: archetype-data-retention
title: "Retención de Datos, Archivado y Control de Costes"
type: pattern
status: learning
importance: 80
difficulty: medium
tags: [system-design, data-retention, archival, lifecycle, cold-storage, tiered-storage]
canonical: true
aliases: [tiered-storage, ilm, lifecycle-policies, cold-storage]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Retención de Datos, Archivado y Control de Costes

## TL;DR
- Gestionar el crecimiento de datos con reglas de retención y niveles de almacenamiento para controlar costes.
- Desafíos clave: explosión de costes, requisitos de cumplimiento (eliminación GDPR vs logs inmutables), consultar datos mezclados calientes/fríos.
- Soluciones: rollups/downsampling, políticas de ciclo de vida (mover/eliminar automáticamente), almacén de auditoría separado con acceso restringido.

## Dónde duele (por qué duele)
1. **Explosión de costes**: Mantener clickstream crudo para siempre → petabytes → inasequible
   - **Solución**: Políticas de retención (crudo 7d, rollups 1a, archivo frío para cumplimiento)
2. **Requisitos de cumplimiento**: GDPR requiere eliminación; logs de auditoría deben ser inmutables
   - **Solución**: Almacén caliente separado (eliminable) + almacén de auditoría frío (inmutable, restringido)
3. **Consultar datos mezclados calientes/fríos**: Los usuarios quieren "últimos 2 años" → consulta lenta entre niveles
   - **Solución**: Consulta federada (consultar caliente + frío); o redirigir consultas antiguas a la UI de archivo

## Reglas de decisión
- **Usar cuando**: Datos de alto volumen (logs, métricas, eventos), requisitos de retención difieren por antigüedad, optimización de costes crítica
- **Evitar cuando**: Bajo volumen (< 1TB total), retención uniforme (eliminar todo a la vez)

## Trade-offs
- **Almacenamiento caliente**: Rápido, caro → datos recientes
- **Almacenamiento frío**: Lento (minutos para recuperar), barato → datos antiguos / cumplimiento
- **Rollups**: Datos pre-agregados → baratos, consultas rápidas pero menos detalle

## Ejemplo explícito
Clickstream: Eventos crudos masivos. Retener crudo 7 días (caliente, consultable), rollups por hora 1 año (tibio), archivo crudo a S3 Glacier por 7 años (frío, cumplimiento). Las consultas de datos recientes van al almacén caliente (rápido); el análisis histórico usa rollups (rápido, menos detalle); la auditoría de cumplimiento usa Glacier (lento, raro).

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Agregación de Series Temporales](archetype-time-series-aggregation.md), [Vistas Materializadas](archetype-materialized-views.md)
