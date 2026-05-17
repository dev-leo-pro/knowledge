---
id: messaging-basics
title: "Messaging Basics"
type: concept
status: learning
importance: 45
difficulty: medium
tags: [architecture, messaging]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Mensajería

## TL;DR (BLUF)
- La mensajería permite la comunicación asíncrona entre servicios.
- Úsala para desacoplar productores y consumidores.
- Trade-off: consistencia eventual y complejidad operacional.

## Definición
**Qué es:** Comunicación asíncrona mediante colas o topics.
**Términos clave:** cola, topic, productor, consumidor.

## Por qué importa
- Mejora la resiliencia y la escalabilidad.
- Añade latencia y complejidad.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos de mensajería y trade-offs.
**Fuera del alcance / NO resuelto por esto:** ajuste fino específico de brokers.

## Modelo mental / Intuición
- Un mensaje es una unidad de trabajo entregada a otro servicio.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites procesamiento asíncrono o desacoplamiento.
### Evítalo cuando
- Necesites garantías síncronas.

## Cómo lo usaría (práctico)
- **Contexto:** Envío de email después del registro de usuario.
- **Pasos:** encolar mensaje → consumidor envía email → manejar reintentos.
- **Cómo se ve el éxito:** procesamiento fiable con bajo lag.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** desacoplamiento y resiliencia.
- **Contras / Riesgos:** ordenamiento y manejo de duplicados.
### Alternativas
- **Request-response:** llamadas síncronas.
- **Cómo elegir:** usa mensajería para flujos de trabajo asíncronos.

## Modos de fallo y errores comunes
- Mensajes duplicados sin idempotencia.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** profundidad de cola, lag de consumidores.
- **Logs:** errores de procesamiento.
- **Alertas:** backlog creciente.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Hacer consumidores idempotentes
  - [ ] Monitorear lag

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar mensajería para request/response.
  - **Por qué es malo:** añade latencia y complejidad.
  - **Mejor enfoque:** usar llamadas directas para necesidades síncronas.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La mensajería permite que los servicios se comuniquen asíncronamente. Es genial para desacoplar, pero debes manejar reintentos, ordenamiento y duplicados.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los mensajes siempre se entregan exactamente una vez?
   - **R:** No; at-least-once es lo común.
2) **P:** ¿Las colas y los topics son lo mismo?
   - **R:** No; las colas entregan a un consumidor, los topics a muchos.
3) **P:** ¿Puedes omitir la idempotencia?
   - **R:** No; los duplicados ocurren.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir los fundamentos de mensajería.
- [ ] Puedo indicar cuándo usarla.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar cola vs topic.

## Enlaces (SIN duplicación)
### Prerrequisitos
- N/A

### Temas relacionados
- [Fundamentos de arquitectura dirigida por eventos](event-driven-basics.md)

### Comparar con
- [Arquitectura request-response](request-response.md) — asíncrono vs síncrono.
