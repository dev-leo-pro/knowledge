---
id: http
title: "HTTP (Hypertext Transfer Protocol)"
type: technology
status: learning
importance: 55
difficulty: medium
tags: [operations, networking, application]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# HTTP (Protocolo de Transferencia de Hipertexto)

## TL;DR (BLUF)
- HTTP es un protocolo de capa de aplicación para comunicación request/response.
- Úsalo para APIs y comunicación web.
- Trade-off: latencia de request/response y restricciones de statelessness.

## Definición
**Qué es:** Un protocolo de capa de aplicación sin estado que define métodos, headers, códigos de estado y semántica de payload para interacciones request/response, generalmente sobre [TCP](tcp.md) y frecuentemente asegurado con [TLS](tls.md).
**Términos clave:** request/response, código de estado, método, header, sin estado, idempotencia.

## Por qué importa
- Sustenta la mayoría de APIs y servicios web.
- El comportamiento de HTTP moldea el rendimiento (latencia, caché) y la fiabilidad.

## Alcance y no-objetivos
**Dentro del alcance:** semántica HTTP, códigos de estado y trade-offs operacionales.
**Fuera del alcance / NO resuelto por esto:** decisiones de diseño de API o mecanismos de autenticación.

## Modelo mental / Intuición
- HTTP es como un envío de formulario: envías una petición, recibes una respuesta con estado y datos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites semántica de request/response estandarizada.
- Quieras amplia compatibilidad entre sistemas.
### Evítalo cuando
- Necesites streaming o comunicación bidireccional en tiempo real.
- Necesites ultra-baja latencia con protocolos personalizados.

## Cómo lo usaría (práctico)
- **Contexto:** API REST pública para un catálogo de productos.
- **Pasos:**
  1) Definir recursos y métodos.
  2) Elegir códigos de estado y contratos de error.
  3) Agregar caché y timeouts.
- **Cómo se ve el éxito:** respuestas predecibles y manejo de errores claro.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** ubicuo, bien entendido, fácil de depurar.
- **Contras / Riesgos:** sobrecarga de request/response y latencia.
### Alternativas
- **gRPC:** mejor para contratos estrictos y rendimiento.
- **WebSockets:** mejor para streams bidireccionales en tiempo real.
- **Cómo elegir:** usa HTTP para amplia interoperabilidad; elige alternativas para necesidades especializadas.

## Modos de fallo y errores comunes
- Sobrecargar 200 OK con payloads de error.
- Timeouts faltantes causando peticiones atascadas.
- Tratar reintentos como seguros para métodos no idempotentes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia p95/p99, tasa de errores por código de estado, throughput.
- **Logs:** IDs de petición, códigos de estado, payloads de error.
- **Trazas:** spans de petición con timing downstream.
- **Alertas:** picos en errores 5xx o regresiones de latencia.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Usar códigos de estado correctos
  - [ ] Configurar timeouts y reintentos apropiadamente
- **Notas de seguridad / cumplimiento**
  - Usar [TLS](tls.md) para cualquier dato de usuario o sensible.
- **Notas de rendimiento**
  - Habilitar compresión y caché donde sea seguro.
- **Notas operacionales**
  - Definir SLIs/SLOs claros para latencia y tasas de error.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar HTTP para RPC interno sin timeouts.
  - **Por qué es malo:** latencia de cola y fugas de recursos.
  - **Mejor enfoque:** configurar timeouts y considerar gRPC si es apropiado.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- HTTP es un protocolo sin estado de request/response usado por la web. Define métodos y códigos de estado para que clientes y servidores puedan comunicarse de forma fiable.

### Preguntas trampa (con respuestas)
1) **P:** ¿HTTP siempre va sobre TCP?
  - **R:** Mayormente, pero HTTP/3 corre sobre QUIC (basado en [UDP](udp.md)).
2) **P:** ¿GET siempre es seguro de reintentar?
   - **R:** Solo si es idempotente y el servidor lo respeta.
3) **P:** ¿HTTPS es un protocolo diferente?
   - **R:** Es HTTP sobre [TLS](tls.md).

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir HTTP claramente.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de redes](networking-basics.md)
- [TCP](tcp.md)

### Temas relacionados
- [TLS](tls.md)
- [Fundamentos de diseño de API](../system-design/api-design-basics.md)

### Comparar con
- [Fundamentos de diseño de API](../system-design/api-design-basics.md) — protocolo vs semántica de API.

## Notas / Bandeja de entrada (opcional)
- N/A
