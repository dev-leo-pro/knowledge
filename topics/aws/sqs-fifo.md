---
title: SQS FIFO
---

# SQS FIFO

## Definición
SQS FIFO (First-In-First-Out) es una cola de mensajes administrada por AWS que garantiza el orden de entrega de mensajes y soporta deduplicación.

## Por qué importa
Permite el procesamiento confiable y ordenado de eventos en sistemas distribuidos, crítico para flujos de trabajo que requieren secuenciación estricta.

## Cuándo usar / cuándo no usar
Úsalo para flujos de trabajo donde se requiere orden y procesamiento exactamente una vez. No es necesario para cargas de trabajo desordenadas y de alto rendimiento (usa SQS Standard).

## Trade-offs
- + Garantiza orden y deduplicación
- + Administrado por AWS, escala automáticamente
- - Menor rendimiento que SQS Standard
- - Límites en la ventana de deduplicación

## Prerequisitos
- [Semánticas de entrega de mensajes](../architecture/message-delivery-semantics.md)

## Temas relacionados
- [SNS](sns.md) (TODO si falta)
- [Reintentos y Backoff](../operations/retries-and-backoff.md)

## Preguntas trampa
1. ¿Cuál es una característica clave de SQS FIFO?
   - Garantiza el orden de mensajes y deduplicación.
2. ¿Cuándo deberías evitar SQS FIFO?
   - Para cargas de trabajo desordenadas y de alto rendimiento.
3. ¿Cuál es un error común con SQS FIFO?
   - Exceder la ventana de deduplicación o los límites de grupos de mensajes.
