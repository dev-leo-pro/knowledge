---
id: kafka
title: "Kafka (Core Concepts)"
type: technology
status: learning
importance: 50
difficulty: medium
tags: [architecture, messaging]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Kafka (Conceptos Fundamentales)

## TL;DR (BLUF)
- Kafka es un log distribuido para streaming de eventos de alto rendimiento.
- Úsalo para pipelines de eventos durables y sistemas desacoplados.
- Trade-off: complejidad operacional y consistencia eventual.

## Definición
**Qué es:** Un sistema de mensajería distribuido basado en log con particiones y grupos de consumidores.
**Términos clave:** partición, offset, grupo de consumidores, retención.

## Por qué importa
- Escala el procesamiento de eventos y permite el replay.
- Una mala configuración puede causar lag y pérdida de datos.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos fundamentales de Kafka.
**Fuera del alcance / NO resuelto por esto:** configuración profunda de brokers.

## Modelo mental / Intuición
- Piensa en Kafka como un log durable de solo-append.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites flujos de eventos durables y replay.
### Evítalo cuando
- Necesites colas simples con operaciones mínimas.

## Cómo lo usaría (práctico)
- **Contexto:** Pipeline de eventos para analítica.
- **Pasos:** definir topics → particionar por clave → construir consumidores con offsets.
- **Cómo se ve el éxito:** bajo lag y procesamiento fiable.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** escalable, durable, reproducible.
- **Contras / Riesgos:** complejidad operacional y límites de ordenamiento.
### Alternativas
- **SQS/SNS:** colas gestionadas más simples.
- **Cómo elegir:** usa Kafka para streaming de alto rendimiento.

## Modos de fallo y errores comunes
- Acumulación de lag por consumidores lentos.
- Claves de partición pobres causando particiones calientes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** lag de consumidores, throughput, tasa de errores.
- **Logs:** errores de broker.
- **Alertas:** lag creciente o desbalance de particiones.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir buenas claves de partición
  - [ ] Monitorear lag de consumidores

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Tratar Kafka como una cola sin planificar la retención.
  - **Por qué es malo:** riesgo de pérdida de datos.
  - **Mejor enfoque:** configurar retención y estrategia de replay.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Kafka es un log de eventos distribuido. Los productores agregan eventos a particiones, los consumidores los leen usando offsets, y puedes reproducir eventos cuando sea necesario.

### Preguntas trampa (con respuestas)
1) **P:** ¿Kafka garantiza ordenamiento global?
   - **R:** No; el ordenamiento es por partición.
2) **P:** ¿Kafka es una base de datos?
   - **R:** No; es un log de streaming.
3) **P:** ¿Los consumidores son basados en push?
   - **R:** No; hacen pull por offsets.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir Kafka.
- [ ] Puedo explicar particiones y offsets.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar el lag de consumidores.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de arquitectura dirigida por eventos](event-driven-basics.md)

### Temas relacionados
- [Change Data Capture (CDC)](../databases/change-data-capture.md)

### Comparar con
- [RabbitMQ vs Kafka](rabbitmq-vs-kafka.md)
