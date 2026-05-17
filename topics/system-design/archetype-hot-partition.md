---
id: archetype-hot-partition
title: "Partición Caliente / Problema de la Celebridad"
type: pattern
status: learning
importance: 85
difficulty: hard
tags: [system-design, hot-partition, skew, sharding, load-balancing]
canonical: true
aliases: [hotspotting, skew, key-imbalance, celebrity-problem]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Partición Caliente / Problema de la Celebridad

## TL;DR
- Un pequeño subconjunto de claves/entidades domina el tráfico, sobrecargando un shard/partición.
- Desafíos clave: una partición se funde mientras otras están ociosas, fallos en cascada (caché + BD + aguas abajo).
- Soluciones: salting de claves / replicación fan-out, infraestructura dedicada para claves calientes, caché/CDN.

## Dónde duele (por qué duele)
1. **Una partición se funde mientras otras están ociosas**: Una publicación viral recibe el 90% de las lecturas → cuello de botella en el shard
   - **Solución**: Detectar asimetría temprano (monitorear claves principales); salt en claves calientes (replicar con `key:1`, `key:2`)
2. **Fallos en cascada**: Fallo de caché en clave caliente → BD abrumada → timeouts aguas abajo
   - **Solución**: CDN / caché multi-capa; caché dedicada para claves calientes
3. **El sharding no ayuda**: Añadir más shards no arregla un hotspot de una sola clave
   - **Solución**: Replicar clave caliente a través de shards; respuestas aproximadas (muestreo)

## Reglas de decisión
- **Usar cuando**: Feeds sociales, temas tendencia, eventos en vivo, páginas de producto con picos virales
- **Evitar cuando**: Distribución de tráfico uniforme (sin celebridades)

## Trade-offs
- **Replicación (salting)**: Distribuye carga pero complica la invalidación
- **Infraestructura dedicada**: Rápida pero con overhead operativo
- **Respuestas aproximadas**: Baratas pero menos precisas

## Ejemplo explícito
Una publicación viral recibe 1M lecturas/seg. Si se shard por post_id, todas las peticiones van a un shard → se funde. Salt de clave: almacenar 10 copias (`post:123:0` ... `post:123:9`); el cliente lee una réplica aleatoria. Distribuye la carga 10× entre shards.

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Caché Distribuida](archetype-distributed-caching.md), [Particionado y Sharding](partitioning-and-sharding.md), [Particiones Calientes en DynamoDB](../databases/dynamodb-hot-partitions.md)
