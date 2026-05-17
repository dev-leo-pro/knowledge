---
title: Webhooks
---

# Webhooks

## Definición
Los webhooks son callbacks HTTP que permiten a un sistema notificar a otro sistema en tiempo real cuando ocurre un evento, enviando una solicitud HTTP POST a una URL configurada.

## Por qué importa
Permiten integraciones y automatización en tiempo real entre sistemas sin necesidad de polling.

## Cuándo usar / cuándo no usar
Úsalos para integraciones dirigidas por eventos, notificaciones o callbacks de APIs de terceros. No es ideal para entrega crítica de alta fiabilidad sin reintentos o verificación.

## Trade-offs
- + Comunicación en tiempo real y baja latencia
- + Simples de implementar
- - La entrega no está garantizada (a menos que se agreguen reintentos/verificación)
- - Riesgos de seguridad si no se validan

## Prerequisitos
- [HTTP](../operations/http.md)

## Temas relacionados
- [API Gateway](../system-design/api-gateway.md)
- [Resiliencia en integraciones con terceros](../operations/third-party-integration-resilience.md)

## Preguntas trampa
1. ¿Qué es un webhook?
   - Un callback HTTP activado por un evento en un sistema fuente.
2. ¿Cuál es un riesgo de seguridad común con webhooks?
   - No verificar la firma o el origen de la solicitud.
3. ¿Cómo puedes hacer los webhooks más fiables?
   - Implementar reintentos e idempotencia.
