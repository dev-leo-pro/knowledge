---
id: archetype-fan-out
title: "Fan-Out / Entrega por Difusión"
type: pattern
status: learning
importance: 85
difficulty: hard
tags: [system-design, fan-out, pub-sub, push-notifications, feeds]
canonical: true
aliases: [pub-sub-fan-out, push-notifications, feed-distribution]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Fan-Out / Entrega por Difusión

## TL;DR
- Un evento debe ser entregado a muchos consumidores/destinos (N seguidores, suscriptores, dispositivos).
- Desafíos clave: explosión de trabajo (N destinos), contrapresión y reintentos, entregar "una vez" por destino.
- Soluciones: colas asíncronas + pools de workers, pub/sub basado en topics, trade-off fan-out en escritura vs lectura.

## Dónde duele (por qué duele)
1. **Explosión de trabajo (N seguidores)**: Una publicación → 5M entregas → colapso síncrono
   - **Solución**: Colas asíncronas + pools de workers para entrega escalable; agrupar donde sea posible
2. **Contrapresión y reintentos**: Las entregas fallidas deben reintentarse sin bloquear otras
   - **Solución**: DLQ por destino; reintento con backoff exponencial; paralelismo
3. **Entregar "una vez" por destino**: Los reintentos causan duplicados
   - **Solución**: Entrega idempotente (rastrear entrega por destino); deduplicación

## Reglas de decisión
- **Usar cuando**: Notificaciones, feeds, actualizaciones en tiempo real, webhooks a partners
- **Evitar cuando**: Destinos < 10 (llamadas directas más simple), latencia estricta en tiempo real (fan-out añade retraso)

## Trade-offs
- **Fan-out en escritura**: Pre-computar/entregar inmediatamente (lecturas rápidas, escrituras lentas/caras)
- **Fan-out en lectura**: Computar bajo demanda (escrituras rápidas, lecturas lentas, almacenamiento más barato)
- **Elegir**: < 10k seguidores → fan-out en escritura; > 100k → fan-out en lectura

## Ejemplo explícito
Un creador publica contenido; 5M seguidores deben ser notificados. El fan-out síncrono colapsa el servicio. Asíncrono: publicar evento → pool de workers obtiene lista de seguidores en lotes → encola 5M tareas de entrega → workers procesan con reintentos.

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Ingestión de Eventos de Alto Rendimiento](archetype-event-ingestion.md), [Idempotencia](archetype-idempotency-dedup.md)
