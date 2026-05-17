---
id: archetype-external-integrations
title: "Integraciones Externas (Dependencias No Fiables)"
type: pattern
status: learning
importance: 95
difficulty: medium
tags: [system-design, third-party, webhooks, circuit-breaker, resilience, integrations]
canonical: true
aliases: [webhook-processing, third-party-api-integration, partner-systems]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Integraciones Externas (Dependencias No Fiables)

## TL;DR
- Interactuar con sistemas externos (APIs de terceros, webhooks, partners) que tienen latencia y modos de fallo desconocidos.
- Desafíos clave: timeouts y fallos parciales, reintentos que causan duplicados, límites de tasa del proveedor.
- Soluciones: timeouts + circuit breaker + reintentos con jitter, procesamiento asíncrono, DLQ + replay.

## Dónde duele (por qué duele)
1. **Timeouts y fallos parciales**: La llamada a la API de impuestos durante el checkout es lenta/está caída → se pierden conversiones o se arriesga el cumplimiento
   - **Solución**: Timeouts (agresivos), circuit breaker (fallar rápido), flujos asíncronos (desacoplar la ruta del usuario)
2. **Reintentos que causan duplicados**: Reintentar webhook → el partner recibe duplicado → doble procesamiento
   - **Solución**: Idempotencia en ambos lados (enviar clave de idempotencia); deduplicación en el receptor
3. **Límites de tasa del proveedor**: Se alcanza el límite de tasa de la API → todas las peticiones fallan → servicio degradado
   - **Solución**: Rate limiting local (mantenerse bajo los límites del proveedor), backoff, cachear respuestas

## Reglas de decisión
- **Usar cuando**: Proveedores de pago, APIs de KYC/cumplimiento, APIs de envío, integraciones CRM, webhooks
- **Evitar cuando**: Servicios internos completamente controlados (se aplican patrones diferentes)

## Trade-offs
- **Integración síncrona (bloquear usuario)**: Simple pero el usuario espera una llamada externa lenta/fallida
- **Asíncrona (desacoplar)**: El usuario no espera pero el resultado es eventual (webhook/polling)
- **Elegir**: Ruta crítica (checkout) → asíncrona si es posible; no crítica → síncrona aceptable

## Ejemplo explícito
El checkout llama a la API de impuestos para el cálculo de IVA. La API es lenta (2s p99) o está caída. Si el checkout bloquea → conversiones perdidas. Solución: Llamar a la API de impuestos de forma asíncrona (después de hacer el pedido), enviar email al usuario con el precio final; o usar impuesto cacheado/estimado (notificar si se necesita ajuste). Circuit breaker: después de 5 fallos, dejar de llamar por 30s; devolver impuesto estimado.

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Idempotencia](archetype-idempotency-dedup.md), [Orquestación de Flujos de Trabajo](archetype-workflow-orchestration.md)  
**Patrones**: Circuit Breaker, Bulkheads, Timeouts, DLQ (ver [Fundamentos de Fiabilidad](../operations/reliability-basics.md))
