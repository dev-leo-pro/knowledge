---
id: redis-scaling
title: "Escalado de Redis (vertical y horizontal)"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [redis, scaling, clustering]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Escalado de Redis (vertical y horizontal)

## TL;DR (BLUF)
- Escala verticalmente primero (más memoria/CPU), luego horizontalmente vía sharding o Redis Cluster.
- Escalar horizontalmente mejora la capacidad pero agrega complejidad y límites cross-clave.
- Trade-off: simplicidad vs sobrecarga operativa distribuida.

## Definición
**Qué es:** Técnicas para crecer la capacidad y throughput de Redis: escalado vertical, réplicas para lecturas y particionamiento horizontal (sharding del lado del cliente o hash slots de Redis Cluster).
**Términos clave:** escalar verticalmente, escalar horizontalmente, replicación, sentinel, cluster, hash slots, claves calientes.

## Por qué importa
- Redis está limitado por memoria; las decisiones de escalado determinan costo y confiabilidad.
- Un mal escalado crea hotspots y limitaciones cross-slot.

## Alcance y no-objetivos
**Dentro del alcance:** crecimiento de capacidad, comportamiento de clúster/shard, replicación para lecturas.
**Fuera del alcance / NO resuelto por esto:** consistencia global, garantías de replicación multi-región.

## Modelo mental / Intuición
- Escalar verticalmente es un refrigerador más grande; escalar horizontalmente son múltiples refrigeradores con reglas de etiquetado.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Te acercas a los límites de memoria o alta latencia en tráfico pico.
- Puedes particionar claves limpiamente entre nodos.
### Evítalo cuando
- Necesitas transacciones multi-clave entre claves no relacionadas.
- Tu carga de trabajo está dominada por pocas claves calientes que no pueden particionarse.

## Cómo lo usaría (práctico)
- **Contexto:** Huella de caché creciente con tráfico pesado de lecturas.
- **Pasos:** escalar verticalmente → agregar réplicas de lectura → evaluar Redis Cluster → aplicar diseño de claves con hash tags.
- **Cómo se ve el éxito:** latencia estable con memoria y CPU balanceados entre nodos.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** más capacidad y throughput.
- **Desventajas / Riesgos:** complejidad operativa, límites de comandos cross-slot, ajuste de failover.
### Alternativas
- **Usar un almacén KV duradero:** cuando Redis se vuelve demasiado complejo de operar.
- **Particionamiento a nivel de aplicación:** si Redis Cluster es demasiado rígido.
- **Cómo elegir:** escalar verticalmente hasta el límite de costo, luego fragmentar con un diseño de claves que evite particiones calientes.

## Modos de fallo y trampas
- Distribución desigual de claves llevando a nodos calientes.
- Operaciones cross-slot fallando o requiriendo workarounds.
- Retraso de replicación causando lecturas obsoletas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia por nodo, CPU, `used_memory`, retraso de replicación, distribución de slots.
- **Logs:** eventos de failover, operaciones de rebalanceo.
- **Alertas:** CPU/memoria de nodo caliente, failovers frecuentes, desbalance de slots.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Configurar nombrado de claves con hash tags para operaciones multi-clave.
  - [ ] Medir claves calientes y aplicar sharding/particionamiento.
  - [ ] Probar failover y comportamiento de reintento del cliente.
  - [ ] Planificar resharding durante ventanas de bajo tráfico.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Fragmentar sin medir claves calientes primero.
  - **Por qué es malo:** un shard aún se derrite y anula el escalado.
  - **Mejor enfoque:** detectar claves calientes y dividirlas o separar el caché.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El escalado de Redis empieza con máquinas más grandes y réplicas de lectura. Cuando eso no es suficiente, fragmentas datos con Redis Cluster o particionamiento del lado del cliente, lo que agrega complejidad y restricciones cross-clave.

### Preguntas trampa (con respuestas)
1) **P:** ¿Redis Cluster permite transacciones multi-clave entre cualquier clave?
   - **R:** no; las claves deben estar en el mismo hash slot.
2) **P:** ¿Escalar horizontalmente siempre es mejor que verticalmente?
   - **R:** no; escalar horizontalmente agrega complejidad y puede reducir la simplicidad para cargas de trabajo pequeñas.
3) **P:** ¿Las réplicas eliminan la necesidad de escalar escrituras?
   - **R:** no; las escrituras aún van al nodo primario.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar escalado vertical vs horizontal para Redis.
- [ ] Puedo decir cuándo usar Redis Cluster.
- [ ] Puedo describir la limitación cross-slot.
- [ ] Puedo nombrar un modo de fallo de clave caliente.
- [ ] Puedo listar señales básicas de observabilidad de escalado.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [Particionamiento y sharding](../system-design/partitioning-and-sharding.md)
- [Planificación de capacidad](../operations/capacity-planning.md)

### Temas relacionados
- [Particiones calientes](hot-partitions.md)
- [Modelos de consistencia](consistency-models.md)

### Comparar con
- [DynamoDB](dynamodb.md) — escalado horizontal gestionado vs Redis auto-gestionado.

## Notas / Bandeja de entrada (opcional)
- N/A
