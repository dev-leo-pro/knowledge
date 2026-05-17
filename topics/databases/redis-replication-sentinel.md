---
id: redis-replication-sentinel
title: "Replicación y Sentinel de Redis"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [redis, replication, availability]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Replicación y Sentinel de Redis

## TL;DR (BLUF)
- La replicación de Redis proporciona réplicas de lectura y soporte de failover.
- Sentinel gestiona el monitoreo y el failover automatizado.
- Trade-off: mayor disponibilidad vs retraso de replicación y complejidad operativa.

## Definición
**Qué es:** La replicación de Redis copia datos de un primario a réplicas, mientras Sentinel monitorea nodos y activa el failover cuando el primario falla.
**Términos clave:** primario/réplica, retraso de replicación, failover, quórum de Sentinel.

## Por qué importa
- Mantiene Redis disponible durante fallos de nodo.
- Introduce retraso y riesgo de split-brain si está mal configurado.

## Alcance y no-objetivos
**Dentro del alcance:** escalado de lecturas, automatización de failover, consistencia de réplicas.
**Fuera del alcance / NO resuelto por esto:** consistencia fuerte cross-región o escrituras multi-master.

## Modelo mental / Intuición
- Un standby caliente con un coordinador que decide cuándo cambiar.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas mayor disponibilidad y puedes tolerar retraso de réplica.
- Quieres failover automatizado sin intervención manual.
### Evítalo cuando
- Necesitas consistencia fuerte en lecturas.
- No puedes tolerar retraso de réplica o complejidad de failover.

## Cómo lo usaría (práctico)
- **Contexto:** Caché Redis en producción con requisitos de uptime.
- **Pasos:** configurar réplicas → desplegar quórum de Sentinel → probar failover → ajustar reintentos del cliente.
- **Cómo se ve el éxito:** failover automático con retraso y tiempo de recuperación acotados.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mayor disponibilidad, escalado de lecturas.
- **Desventajas / Riesgos:** retraso, ajuste de failover, riesgo de split-brain.
### Alternativas
- **Redis Cluster:** agrega sharding y failover integrado.
- **Nodo único:** más simple si los requisitos de disponibilidad son bajos.
- **Cómo elegir:** usa replicación+Sentinel para HA sin sharding; usa Cluster si también necesitas escalar horizontalmente.

## Modos de fallo y trampas
- Retraso de réplica causando lecturas obsoletas.
- Thrashing de failover debido a redes inestables.
- Quórum mal configurado llevando a split-brain.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** retraso de replicación, estado de sincronización, conteo de failovers, errores de cliente.
- **Logs:** decisiones de failover de Sentinel y cambios de quórum.
- **Alertas:** retraso sostenido o failovers repetidos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Ejecutar Sentinel con quórum de número impar.
  - [ ] Configurar timeouts y políticas de reintento del cliente.
  - [ ] Probar failover regularmente.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Leer datos críticos de réplicas sin verificar retraso.
  - **Por qué es malo:** lecturas obsoletas rompen la corrección.
  - **Mejor enfoque:** leer del primario o tolerar la obsolescencia explícitamente.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La replicación de Redis copia datos a réplicas para disponibilidad y escalado de lecturas. Sentinel monitorea el clúster y automatiza el failover, pero debes gestionar el retraso y el quórum.

### Preguntas trampa (con respuestas)
1) **P:** ¿La replicación elimina la pérdida de datos?
   - **R:** no; la replicación asíncrona puede perder escrituras recientes en failover.
2) **P:** ¿Sentinel es lo mismo que Redis Cluster?
   - **R:** no; Sentinel maneja failover, Cluster agrega sharding y hash slots.
3) **P:** ¿Las lecturas de réplica siempre son seguras?
   - **R:** no; el retraso puede devolver datos obsoletos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar replicación vs Sentinel.
- [ ] Puedo describir los impactos del retraso de replicación.
- [ ] Puedo elegir entre Sentinel y Cluster.
- [ ] Puedo nombrar una trampa de failover.
- [ ] Puedo listar métricas clave de HA.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [Fundamentos de disponibilidad](../operations/availability-basics.md)
- [Fundamentos de sistemas distribuidos](../system-design/distributed-systems-basics.md)

### Temas relacionados
- [Escalado de Redis (vertical y horizontal)](redis-scaling.md)

### Comparar con
- [Particionamiento y sharding](../system-design/partitioning-and-sharding.md) — distribución para escala vs HA.

## Notas / Bandeja de entrada (opcional)
- N/A
