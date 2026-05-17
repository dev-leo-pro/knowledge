---
id: partitioning-and-sharding
title: "Partitioning & Sharding"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [system-design, scaling]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Particionamiento y Sharding

## TL;DR (BLUF)
- El particionamiento divide datos por claves; el sharding los distribuye entre nodos.
- Úsalo cuando se alcancen los límites de un solo nodo.
- Trade-off: consultas y operaciones más complejas.

## Definición
**Qué es:** Técnicas para dividir datos por clave y (opcionalmente) distribuirlos entre nodos. El particionamiento puede ser dentro de una base de datos; el sharding es particionamiento a través de múltiples bases de datos/servidores.
**Términos clave:** clave de partición, shard, hashing consistente, replicación.

## Por qué importa
- Permite escalabilidad horizontal.
- Un mal particionamiento lleva a particiones calientes y carga desigual.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de particionamiento y sharding.
**Fuera del alcance / NO resuelto por esto:** estrategias de replicación.

## Modelo mental / Intuición
- El particionamiento es dividir dentro de un sistema; el sharding es dividir entre máquinas.
- La replicación es diferente: copia los mismos datos para disponibilidad/escala de lectura, no distribución.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El volumen de datos o el throughput exceda un solo nodo.
### Evítalo cuando
- Puedas escalar verticalmente u optimizar consultas en su lugar.

## Cómo lo usaría (práctico)
- **Contexto:** Dataset multi-tenant creciendo rápidamente.
- **Pasos:** elegir clave de shard → distribuir datos → manejar rebalanceo.
- **Cómo se ve el éxito:** carga balanceada y latencia predecible.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** escalabilidad.
- **Contras / Riesgos:** complejidad operacional, consultas entre shards.
### Alternativas
- **Escalado vertical:** más simple pero limitado.
- **Cómo elegir:** hacer sharding cuando el escalado de un solo nodo sea insuficiente.

## Modos de fallo y errores comunes
- Shards calientes por claves sesgadas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** desbalance entre shards, latencia p95.
- **Logs:** errores de enrutamiento de shards.
- **Alertas:** carga desigual entre shards.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir una buena clave de shard
  - [ ] Planificar para re-sharding

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Hacer sharding por tiempo con tráfico sesgado.
  - **Por qué es malo:** shards calientes.
  - **Mejor enfoque:** agregar distribución basada en hash.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El particionamiento divide datos por clave; el sharding distribuye particiones entre máquinas. Permite escalar pero hace las consultas y operaciones más complejas.

### Preguntas trampa (con respuestas)
1) **P:** ¿El sharding elimina la necesidad de índices?
   - **R:** No; cada shard aún necesita índices.
2) **P:** ¿El sharding siempre es más rápido?
   - **R:** No; las consultas entre shards pueden ser más lentas.
3) **P:** ¿Puedes cambiar las claves de shard fácilmente?
   - **R:** Generalmente no; es costoso.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir particionamiento vs sharding.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar la elección de clave de shard.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de sistemas distribuidos](distributed-systems-basics.md)

### Temas relacionados
- [Particiones calientes](../databases/hot-partitions.md)

### Comparar con
- (TODO) Fundamentos de replicación — la replicación copia datos; el sharding los distribuye.
- [Hashing consistente](../databases/consistency-models.md) — consistencia hash vs réplica.
