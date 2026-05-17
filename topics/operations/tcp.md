---
id: tcp
title: "TCP (Protocolo de Control de Transmisión)"
type: technology
status: learning
importance: 50
difficulty: medium
tags: [operations, networking, transport]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# TCP (Protocolo de Control de Transmisión)

## TL;DR (BLUF)
- TCP es un protocolo de transporte fiable y orientado a conexión con entrega ordenada.
- Úsalo para datos sensibles a la corrección (APIs, bases de datos, transferencia de archivos).
- Trade-off: mayor sobrecarga y latencia vs UDP.

## Definición
**Qué es:** Un protocolo de capa de transporte que proporciona entrega fiable, ordenada y en flujo de bytes entre endpoints usando acuses de recibo y retransmisiones.  
**Términos clave:** conexión, handshake, ACK, retransmisión, control de congestión, control de flujo.

## Por qué importa
- La mayoría de protocolos de aplicación ([HTTP](http.md), drivers de base de datos) asumen la fiabilidad de TCP.
- El comportamiento de TCP afecta la latencia, el rendimiento y el rendimiento de cola.

## Alcance y no-objetivos
**Dentro del alcance:** garantías de TCP, handshake, control de flujo/congestión y casos de uso típicos.  
**Fuera del alcance / NO resuelto por esto:** semánticas de reintento a nivel de aplicación o cifrado ([TLS](tls.md)).

## Modelo mental / Intuición
- Piensa en TCP como un servicio de entrega con seguimiento: recibes acuses de recibo (ACKs) y re-envías si se pierde.
- Optimiza para corrección y orden, no para latencia mínima.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas entrega fiable y ordenada.
- La aplicación puede tolerar algo de latencia por corrección.
### Evítalo cuando
- Necesitas ultra-baja latencia y puedes manejar pérdidas o reordenamiento.
- Envías audio/video en tiempo real donde las pérdidas son preferibles a los retrasos.

## Cómo lo usaría (práctico)
- **Contexto:** Llamadas API entre servicios y conexiones de base de datos.
- **Pasos:**
  1) Usar protocolos basados en TCP ([HTTP](http.md), gRPC, drivers de base de datos).
  2) Establecer timeouts de conexión y solicitud.
  3) Observar retransmisiones y picos de latencia.
- **Cómo se ve el éxito:** conexiones estables, latencia predecible y bajas tasas de error.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** entrega fiable y ordenada; soporte generalizado.
- **Desventajas / Riesgos:** mayor sobrecarga, bloqueo de cabeza de línea y recuperación más lenta ante pérdidas.
### Alternativas
- **[UDP](udp.md):** menor latencia pero sin garantías de entrega.
- **Cómo elegir:** usa TCP por defecto a menos que puedas tolerar pérdidas y necesites menor latencia.

## Modos de fallo y trampas
- Bloqueo de cabeza de línea ante pérdida de paquetes.
- Timeouts mal configurados causando reintentos y picos de carga.
- Conexiones de larga vida sin keepalives llevando a caídas silenciosas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de retransmisión, errores de conexión, latencia de handshake.
- **Logs:** resets de conexión, errores de timeout.
- **Trazas:** picos en tiempo de red entre tramos.
- **Alertas:** retransmisiones elevadas o fallos de conexión.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Establecer timeouts razonables de conexión y lectura
  - [ ] Usar keepalives para conexiones de larga vida
- **Notas de seguridad / cumplimiento**
  - Usar [TLS](tls.md) para cifrado en la capa de aplicación.
- **Notas de rendimiento**
  - Vigilar Nagle vs cargas de trabajo sensibles a la latencia.
- **Notas operacionales**
  - Monitorear backlog de SYN y límites de conexión.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Depender de reintentos infinitos sobre TCP.
  - **Por qué es malo:** amplifica la carga durante incidentes de red.
  - **Mejor enfoque:** reintentos acotados con backoff y timeouts.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- TCP establece una conexión y garantiza entrega ordenada acusando recibo de paquetes y retransmitiendo los perdidos. Es fiable pero agrega latencia y sobrecarga.

### Preguntas trampa (con respuestas)
1) **P:** ¿TCP garantiza cero pérdidas?
   - **R:** No; detecta pérdidas y retransmite, pero las pérdidas aún pueden ocurrir y agregar retraso.
2) **P:** ¿TCP está orientado a mensajes?
   - **R:** No; es un flujo de bytes sin límites de mensaje.
3) **P:** ¿TCP es siempre más rápido que UDP?
   - **R:** No; la fiabilidad y el ordenamiento de TCP agregan sobrecarga.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir las garantías de TCP en 2-3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de redes](networking-basics.md)
- [Capas de red (OSI y TCP/IP)](network-layers.md)

### Temas relacionados
- [UDP](udp.md)
- [HTTP](http.md)
- [TLS](tls.md)
- [Fundamentos de diseño de APIs](../system-design/api-design-basics.md)

### Comparar con
- [UDP](udp.md) — flujo ordenado fiable vs datagramas de mejor esfuerzo.

## Notas / Bandeja de entrada (opcional)
- N/A
