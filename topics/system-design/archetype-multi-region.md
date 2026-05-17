---
id: archetype-multi-region
title: "Multi-Región, Replicación y Consistencia"
type: pattern
status: learning
importance: 90
difficulty: hard
tags: [system-design, multi-region, geo-replication, active-active, active-passive, dr]
canonical: true
aliases: [geo-replication, active-active, active-passive, disaster-recovery]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Multi-Región, Replicación y Consistencia

## TL;DR
- Servir a usuarios en diferentes regiones y sobrevivir fallos regionales con infraestructura geo-distribuida.
- Desafíos clave: consistencia de datos entre regiones, complejidad del failover, trade-offs de latencia.
- Soluciones: active-passive con failover, CRDT/resolución de conflictos para multi-escritor, lecturas locales por región + replicación asíncrona.

## Dónde duele (por qué duele)
1. **Consistencia de datos entre regiones**: Perfil actualizado en EU y US simultáneamente → conflictos
   - **Solución**: Active-passive (un escritor) o CRDTs / resolución de conflictos (multi-escritor)
2. **Complejidad del failover**: Fallo de región → intervención manual → RTO en horas
   - **Solución**: Health checks automatizados + failover; simulacros de DR
3. **Trade-offs de latencia**: Escribir en región remota → alta latencia para algunos usuarios
   - **Solución**: Lecturas locales por región + replicación asíncrona para escrituras; aceptar consistencia eventual

## Reglas de decisión
- **Usar cuando**: Base de usuarios global, requisitos de recuperación ante desastres (DR), residencia regulatoria de datos
- **Evitar cuando**: Base de usuarios regional, coste/complejidad supera las necesidades de DR

## Trade-offs
- **Active-passive**: Simple, consistencia fuerte; mayor latencia para usuarios remotos
- **Active-active (multi-escritor)**: Baja latencia en todas partes; complejidad de resolución de conflictos
- **Elegir**: Simplicidad / consistencia fuerte → active-passive; baja latencia global → active-active (si los conflictos son manejables)

## Ejemplo explícito
App global: usuarios EU + usuarios US. Active-passive: US es primario (escrituras); EU es réplica (solo lectura). Si US falla, failover manual a EU (RTO 15 min). Active-active: ambas regiones aceptan escrituras; usar CRDT (ej., last-write-wins con timestamps) para resolver conflictos.

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Teorema CAP](cap-theorem.md), [PACELC](pacelc.md), [Modelos de Consistencia](../databases/consistency-models.md)
