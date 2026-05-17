---
id: redis-streams
title: "Redis Streams"
type: technology
status: learning
importance: 55
difficulty: medium
tags: [redis, messaging, streams]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Redis Streams

## TL;DR (BLUF)
- Redis Streams proporciona un log de solo-adición con grupos de consumidores.
- Úsalos para procesamiento de eventos duradero con reintento y replay.
- Trade-off: más complejidad operativa que Pub/Sub.

## Definición
**Qué es:** Un tipo de dato y funcionalidad de Redis que almacena mensajes ordenados con IDs y soporta grupos de consumidores para procesamiento al-menos-una-vez.
**Términos clave:** stream, ID de entrada, grupo de consumidores, lista de entradas pendientes (PEL), acuses de recibo.

## Por qué importa
- Proporciona durabilidad y replay que Redis Pub/Sub no tiene.
- Habilita flujos de trabajo dirigidos por eventos sin un broker externo.

## Alcance y no-objetivos
**Dentro del alcance:** mensajería duradero, replay, grupos de consumidores, procesamiento al-menos-una-vez.
**Fuera del alcance / NO resuelto por esto:** entrega exactamente-una-vez, enrutamiento complejo o transacciones cross-stream.

## Modelo mental / Intuición
- Piensa en ello como un commit log ligero dentro de Redis.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas eventos persistentes con replay y seguimiento de consumidores.
- Quieres colas nativas de Redis sin un broker separado.
### Evítalo cuando
- Necesitas semántica exactamente-una-vez o durabilidad multi-región.
- Necesitas enrutamiento complejo y [Backpressure](../system-design/backpressure.md) a través de muchos topics.

## Cómo lo usaría (práctico)
- **Contexto:** Eventos de pedidos procesados por múltiples grupos de workers.
- **Pasos:** `XADD` → `XGROUP CREATE` → consumidores `XREADGROUP` → `XACK` y reintentar pendientes.
- **Cómo se ve el éxito:** retraso de procesamiento estable con entradas pendientes acotadas.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** durabilidad, replay, grupos de consumidores.
- **Desventajas / Riesgos:** ajuste operativo, crecimiento de memoria si no se recorta.
### Alternativas
- **Redis Pub/Sub:** ultra-baja latencia pero sin persistencia.
- **Kafka:** durabilidad y ecosistema más fuertes.
- **Cómo elegir:** usa Streams para durabilidad nativa de Redis; usa Kafka para pipelines de streaming a gran escala.

## Modos de fallo y trampas
- No recortar streams, llevando a crecimiento ilimitado de memoria.
- Entradas pendientes sin acusar que nunca se reintentan.
- Usar streams sin controles de [Backpressure](../system-design/backpressure.md).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** longitud del stream, retraso del consumidor, conteo de entradas pendientes, uso de memoria.
- **Logs:** procesamiento fallido de consumidores y conteos de reintento.
- **Alertas:** lista de pendientes creciente o retraso más allá del SLO.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir estrategia de recorte (`MAXLEN` o manual).
  - [ ] Monitorear retraso del consumidor y entradas pendientes.
  - [ ] Implementar reintento y manejo de dead-letter.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Tratar Streams como Pub/Sub sin política de retención.
  - **Por qué es malo:** la memoria crece sin límite.
  - **Mejor enfoque:** configurar recorte y retención.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Redis Streams es un log de solo-adición con grupos de consumidores. A diferencia de Pub/Sub, persisten los mensajes y permiten reproducirlos o reintentarlos.

### Preguntas trampa (con respuestas)
1) **P:** ¿Redis Streams es exactamente-una-vez?
   - **R:** no; es al-menos-una-vez y requiere consumidores idempotentes.
2) **P:** ¿Streams reemplaza a Kafka en todos los casos?
   - **R:** no; Kafka escala y persiste mejor para pipelines grandes.
3) **P:** ¿Streams se recorta automáticamente por defecto?
   - **R:** no; debes configurar el recorte o gestionar la retención.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar Streams en 2-3 oraciones.
- [ ] Puedo describir grupos de consumidores y entradas pendientes.
- [ ] Puedo establecer una estrategia de retención/recorte.
- [ ] Puedo explicar la semántica al-menos-una-vez.
- [ ] Puedo decir cuándo elegir Streams vs Pub/Sub.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [Tipos de datos de Redis](redis-data-types.md)
- [Fundamentos de mensajería](../architecture/messaging-basics.md)

### Temas relacionados
- [Redis Pub/Sub](redis-pub-sub.md)
- [Fundamentos de arquitectura dirigida por eventos](../architecture/event-driven-basics.md)

### Comparar con
- [Kafka](../architecture/kafka.md) — log duradero a mayor escala con garantías más fuertes.

## Notas / Bandeja de entrada (opcional)
- N/A
