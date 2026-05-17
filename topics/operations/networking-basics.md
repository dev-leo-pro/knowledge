---
id: networking-basics
title: "Fundamentos de redes"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [operations, networking]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de redes

## TL;DR (BLUF)
- Los fundamentos de redes cubren latencia, conexiones y timeouts.
- Úsalos para razonar sobre el rendimiento cliente-servidor.
- Trade-off: timeouts más estrictos reducen la latencia de cola pero aumentan los errores.

## Definición
**Qué es:** Conceptos fundamentales de la comunicación en red.
**Términos clave:** latencia, ancho de banda, timeout, reintento.

## Por qué importa
- El comportamiento de la red impacta la conectividad y el rendimiento de la base de datos.
- Timeouts mal configurados causan [Fallo en cascada](cascading-failure.md).

## Alcance y no-objetivos
**Dentro del alcance:** conceptos básicos de redes.
**Fuera del alcance / NO resuelto por esto:** internos profundos de TCP.

## Modelo mental / Intuición
- Las redes no son confiables; diseña para timeouts y reintentos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Solucionas problemas de conexión.
### Evítalo cuando
- Los problemas son claramente locales al proceso de BD.

## Cómo lo usaría (práctico)
- **Contexto:** Timeouts del cliente de BD.
- **Pasos:** verificar latencia → ajustar timeouts → monitorear errores.
- **Cómo se ve el éxito:** conexiones estables y menos timeouts.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejor resiliencia con timeouts correctos.
- **Desventajas / Riesgos:** timeouts excesivamente agresivos causan errores.
### Alternativas
- **Caché local:** reducir llamadas de red.
- **Cómo elegir:** ajustar timeouts basándose en la latencia observada.

## Modos de fallo y trampas
- Reintentar sin backoff causando picos.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia p95, tasa de errores.
- **Logs:** errores de conexión.
- **Alertas:** picos en errores de timeout.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Establecer timeouts
  - [ ] Usar backoff exponencial

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Reintentos infinitos.
  - **Por qué es malo:** tormentas de tráfico.
  - **Mejor enfoque:** reintentos acotados con backoff.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los fundamentos de redes tratan sobre latencia, ancho de banda y timeouts. La configuración correcta de timeout y reintentos es esencial para servicios estables.

### Preguntas trampa (con respuestas)
1) **P:** ¿Son opcionales los timeouts?
   - **R:** no; previenen fugas de recursos.
2) **P:** ¿Es siempre seguro reintentar?
   - **R:** no; los reintentos pueden amplificar la carga.
3) **P:** ¿Puede el caché local reemplazar las preocupaciones de red?
   - **R:** no; las llamadas de red siguen ocurriendo.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir latencia y timeout.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo identificar una señal de monitoreo.
- [ ] Puedo vincularlo a clientes de BD.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de fiabilidad](reliability-basics.md)

### Temas relacionados
- [Capas de red (OSI y TCP/IP)](network-layers.md)
- [DNS](dns.md)
- [TCP](tcp.md)
- [UDP](udp.md)
- [TLS](tls.md)
- [HTTP](http.md)
- [Fundamentos de clientes de base de datos](../databases/database-client-basics.md)

### Comparar con
- [Connection pooling](../databases/connection-pooling.md) — límites de red vs límites de BD.
