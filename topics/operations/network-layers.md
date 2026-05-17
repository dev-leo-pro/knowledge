---
id: network-layers
title: "Capas de red (OSI y TCP/IP)"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [operations, networking]
canonical: true
aliases: [osi-model, tcp-ip-model]
created_at: 2026-01-21
updated_at: 2026-01-21
---

# Capas de red (OSI y TCP/IP)

## TL;DR (BLUF)
- Las capas de red organizan responsabilidades desde la transmisión física hasta los protocolos de aplicación.
- Úsalas para localizar problemas y razonar sobre dónde pertenece un fallo.
- Trade-off: los modelos simplifican la realidad y no se mapean perfectamente a las implementaciones.

## Definición
**Qué es:** Modelos conceptuales que dividen las redes en capas (OSI: 7 capas; TCP/IP: 4-5 capas) para definir responsabilidades e interfaces.  
**Términos clave:** física, enlace de datos, red, transporte, sesión, presentación, aplicación.

## Por qué importa
- Ayuda a depurar problemas al reducir la capa del fallo.
- Clarifica dónde viven protocolos como [TCP](tcp.md), [UDP](udp.md), [HTTP](http.md) y [TLS](tls.md).

## Alcance y no-objetivos
**Dentro del alcance:** responsabilidades de cada capa y ubicación de protocolos.  
**Fuera del alcance / NO resuelto por esto:** implementaciones detalladas de protocolos o diseño de hardware.

## Modelo mental / Intuición
- Las capas son como un sistema postal: cada capa agrega su propio sobre.
- Las capas superiores dependen de las inferiores sin conocer sus detalles.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas localizar problemas de red ([DNS](dns.md) vs [TCP](tcp.md) vs enrutamiento).
- Estás aprendiendo protocolos y sus responsabilidades.
### Evítalo cuando
- Necesitas detalles de implementación específicos del proveedor.

## Cómo lo usaría (práctico)
- **Contexto:** Los usuarios reportan timeouts intermitentes de la API.
- **Pasos:**
  1) Verificar la capa de aplicación (errores [HTTP](http.md)).
  2) Inspeccionar la capa de transporte (resets [TCP](tcp.md), retransmisiones).
  3) Verificar la capa de red (enrutamiento, MTU).
- **Cómo se ve el éxito:** identificación clara de la capa que falla.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** modelo mental sólido para solución de problemas.
- **Desventajas / Riesgos:** los sistemas reales difuminan los límites (por ejemplo, [TLS](tls.md) sobre [TCP](tcp.md)).
### Alternativas
- **Modelo TCP/IP:** modelo en capas más simple enfocado en protocolos de internet.
- **Cómo elegir:** usa OSI para claridad conceptual; usa TCP/IP para mapeo práctico.

## Modos de fallo y trampas
- Atribuir erróneamente problemas de aplicación a capas de red.
- Ignorar interacciones entre capas (por ejemplo, timeouts de handshake [TLS](tls.md)).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de [DNS](dns.md), retransmisiones [TCP](tcp.md), pérdida de paquetes, tasa de error [HTTP](http.md).
- **Logs:** fallos de [DNS](dns.md), resets de conexión, errores de handshake [TLS](tls.md).
- **Trazas:** identificar retrasos en tramos de transporte vs aplicación.
- **Alertas:** picos en retransmisiones o tasas de fallo de [DNS](dns.md).

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Identificar protocolo por capa en tu sistema
  - [ ] Usar métricas específicas por capa para diagnóstico
- **Notas de seguridad / cumplimiento**
  - Mapear [TLS](tls.md) a responsabilidades de presentación/sesión.
- **Notas de rendimiento**
  - Vigilar MTU y fragmentación en la capa de red.
- **Notas operacionales**
  - Mantener runbooks por capa.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Tratar OSI como implementación literal.
  - **Por qué es malo:** los stacks modernos combinan capas y agregan excepciones.
  - **Mejor enfoque:** úsalo como un modelo de diagnóstico, no como un plano.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los modelos OSI y TCP/IP dividen las redes en capas para que podamos razonar sobre responsabilidades y depurar problemas por capa.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es [TLS](tls.md) parte de la capa de aplicación?
   - **R:** Usualmente se mapea a presentación/sesión en OSI, pero en la práctica vive sobre TCP.
2) **P:** ¿Es HTTP un protocolo de transporte?
   - **R:** No; es un protocolo de capa de aplicación.
3) **P:** ¿Es el modelo OSI un plano de implementación estricto?
   - **R:** No; es un modelo conceptual.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo nombrar las capas OSI y sus responsabilidades.
- [ ] Puedo ubicar [TCP](tcp.md)/[UDP](udp.md) y [HTTP](http.md) en las capas correctas.
- [ ] Puedo explicar cómo OSI se mapea a TCP/IP.
- [ ] Puedo usar capas para depurar un problema.
- [ ] Puedo nombrar 1 trampa del modelo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de redes](networking-basics.md)

### Temas relacionados
- [TCP](tcp.md)
- [UDP](udp.md)
- [DNS](dns.md)
- [HTTP](http.md)
- [TLS](tls.md)

### Comparar con
- [Fundamentos de redes](networking-basics.md) — capas conceptuales vs preocupaciones operacionales.

## Notas / Bandeja de entrada (opcional)
- N/A
