---
id: redis-pub-sub
title: "Redis Pub/Sub"
type: pattern
status: learning
importance: 50
difficulty: medium
tags: [redis, messaging, pubsub]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Redis Pub/Sub

## TL;DR (BLUF)
- Redis Pub/Sub difunde mensajes a suscriptores actualmente conectados.
- Es de ultra-baja latencia pero no tiene persistencia ni garantías de entrega.
- Trade-off: simplicidad y velocidad vs confiabilidad y replay.

## Definición
**Qué es:** Una funcionalidad de Redis que entrega mensajes publicados a suscriptores activos en canales o patrones, sin almacenarlos.
**Términos clave:** canales, patrones, fan-out, entrega como-máximo-una-vez.

## Por qué importa
- Es una forma rápida de enviar eventos pero puede silenciosamente descartar mensajes.
- El mal uso lleva a actualizaciones perdidas e inconsistencia de datos.

## Alcance y no-objetivos
**Dentro del alcance:** notificaciones en tiempo real, fan-out de mejor esfuerzo, mensajería transitoria.
**Fuera del alcance / NO resuelto por esto:** colas duraderas, reintentos o replay.

## Modelo mental / Intuición
- Es una transmisión de radio en vivo: solo escuchas lo que está sonando mientras estás sintonizado.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas fan-out de baja latencia y puedes tolerar pérdida ocasional.
- Los consumidores están en línea y pueden recalcular estado si pierden eventos.
### Evítalo cuando
- Necesitas durabilidad, garantías de ordenamiento o replay.
- Los consumidores deben procesar cada mensaje exactamente una vez.

## Cómo lo usaría (práctico)
- **Contexto:** Notificaciones de UI en vivo para clientes conectados.
- **Pasos:** publicar eventos → clientes se suscriben mientras están conectados → recurrir a polling al reconectar.
- **Cómo se ve el éxito:** los usuarios ven actualizaciones casi en tiempo real con brechas ocasionales aceptables.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** extremadamente rápido, topología simple.
- **Desventajas / Riesgos:** sin persistencia, sin acuses de recibo, sin replay.
### Alternativas
- **[Redis Streams](redis-streams.md):** agregan persistencia y grupos de consumidores.
- **Kafka:** log duradero con replay y ordenamiento.
- **Cómo elegir:** si necesitas durabilidad, usa streams o un broker de mensajes.

## Modos de fallo y trampas
- Desconexiones de suscriptores causan pérdida silenciosa de mensajes.
- Tráfico en ráfagas abruma a clientes lentos.
- Sobrecargar un solo canal con eventos no relacionados.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** conteo de suscriptores, tasa de publicación, latencia de clientes.
- **Logs:** desconexiones y reconexiones de clientes.
- **Alertas:** caída repentina de suscriptores o picos en la tasa de publicación.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Asegurar que los clientes manejen reconexiones y resincronización de estado.
  - [ ] Separar canales por dominio para reducir ruido.
  - [ ] Agregar idempotencia en los consumidores si es posible.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar Pub/Sub para procesamiento duradero de trabajos.
  - **Por qué es malo:** los mensajes se pierden al desconectar o sobrecargar.
  - **Mejor enfoque:** usar Redis Streams o una cola.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Redis Pub/Sub es un sistema de difusión de dispara-y-olvida. Es rápido pero no almacena mensajes, así que los suscriptores solo reciben eventos mientras están conectados.

### Preguntas trampa (con respuestas)
1) **P:** ¿Redis Pub/Sub garantiza la entrega?
   - **R:** no; es entrega como-máximo-una-vez a suscriptores activos.
2) **P:** ¿Se pueden reproducir mensajes perdidos?
   - **R:** no; usa Redis Streams o Kafka para replay.
3) **P:** ¿Pub/Sub es bueno para trabajos en segundo plano?
   - **R:** no; carece de durabilidad y acuses de recibo.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar Redis Pub/Sub en un minuto.
- [ ] Puedo decir cuándo es apropiado vs cuándo no.
- [ ] Puedo nombrar una alternativa duradero.
- [ ] Puedo describir el modo de fallo al desconectarse.
- [ ] Puedo diseñar una estrategia de reconexión/resincronización.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [Fundamentos de mensajería](../architecture/messaging-basics.md)

### Temas relacionados
- [Fundamentos de arquitectura dirigida por eventos](../architecture/event-driven-basics.md)

### Comparar con
- [Kafka](../architecture/kafka.md) — log duradero con replay vs difusión transitoria.
- [RabbitMQ vs Kafka](../architecture/rabbitmq-vs-kafka.md) — opciones de broker con garantías de entrega.

## Notas / Bandeja de entrada (opcional)
- N/A
