---
id: event-sourcing
title: "Event Sourcing"
type: pattern
status: learning
importance: 60
difficulty: hard
tags: [architecture, data, messaging]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# Event Sourcing

## TL;DR (BLUF)
- Persiste el estado como una secuencia de eventos inmutables y reconstruye el estado a partir de ellos.
- Úsalo cuando la auditabilidad, el historial y los flujos de trabajo complejos importan.
- Trade-off: mayor complejidad, consistencia eventual y sobrecarga operacional.

## Definición
**Qué es:** Un patrón de persistencia donde todos los cambios de estado se almacenan como eventos de solo-append en un event store, y el estado actual se deriva mediante proyecciones. Frecuentemente se combina con [Arquitectura dirigida por eventos](event-driven-basics.md).
**Términos clave:** event store, proyección, snapshot, replay, solo-append, evolución de esquemas.

## Por qué importa
- Proporciona una pista de auditoría completa y permite depuración de viaje en el tiempo.
- Soporta múltiples modelos de lectura sin mutar la verdad histórica.

## Alcance y no-objetivos
**Dentro del alcance:** almacenamiento de eventos, construcción de proyecciones y semánticas de replay.
**Fuera del alcance / NO resuelto por esto:** fiabilidad de mensajería en tiempo real u operaciones de broker.

## Modelo mental / Intuición
- Trata los eventos como un libro contable bancario: nunca sobrescribes el historial, solo agregas.
- El estado es un fold sobre el flujo de eventos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites una pista de auditoría completa o trazabilidad regulatoria.
- La lógica de negocio sea compleja y se beneficie del análisis temporal.
- Puedas tolerar consistencia eventual en los modelos de lectura.
### Evítalo cuando
- La simplicidad CRUD sea suficiente y el historial no sea necesario.
- No puedas invertir en infraestructura de proyecciones y disciplina de versionado.
- Las lecturas síncronas de baja latencia del estado autoritativo sean críticas.

## Cómo lo usaría (práctico)
- **Contexto:** Un libro contable financiero que debe soportar auditorías y consultas históricas.
- **Pasos:**
  1) Modelar eventos de dominio y definir esquemas de eventos.
  2) Persistir eventos en un event store de solo-append.
  3) Construir proyecciones para casos de uso de consulta.
  4) Usar snapshots para reducir el tiempo de replay.
- **Cómo se ve el éxito:** reconstrucciones rápidas, proyecciones precisas e historial trazable.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** historial completo, auditoría fácil, modelos de lectura flexibles.
- **Contras / Riesgos:** infraestructura compleja, lag en proyecciones y dolor en evolución de esquemas.
### Alternativas
- **CRUD + tablas de auditoría:** más simple pero menos flexible para consultas de viaje en el tiempo.
- **[Change Data Capture (CDC)](../databases/change-data-capture.md):** captura cambios de BD pero carece de semántica explícita de dominio.
- **Cómo elegir:** usa event sourcing cuando el historial y el replay sean requisitos de primera clase.

## Modos de fallo y errores comunes
- Desviación de proyecciones cuando los eventos se reproducen con lógica modificada.
- Reconstrucciones lentas sin snapshots.
- Evolución de esquemas de eventos rompiendo replays antiguos.
- Bugs de escritura dual si los eventos y el estado se escriben por separado.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de escritura del event store, lag de proyecciones, duración de replay, cobertura de snapshots.
- **Logs:** versión de evento, errores de proyección, fallos de replay.
- **Trazas:** append de evento → spans de actualización de proyección.
- **Alertas:** lag creciente en proyecciones o fallos repetidos de replay.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir esquemas de eventos estables y versionados
  - [ ] Construir proyecciones idempotentes
  - [ ] Agregar snapshots para agregados grandes
- **Notas de seguridad / cumplimiento**
  - Cifrar payloads sensibles de eventos en reposo.
- **Notas de rendimiento**
  - Particionar eventos por ID de agregado para mantener el replay eficiente.
- **Notas operacionales**
  - Monitorear el lag de proyección por consumidor.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Escribir el estado primero y emitir eventos después.
  - **Por qué es malo:** crea inconsistencias de escritura dual.
  - **Mejor enfoque:** tratar los eventos como la fuente de verdad y derivar el estado.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Event sourcing almacena cada cambio como un evento y deriva el estado actual de esos eventos. Da un historial perfecto pero añade complejidad alrededor de proyecciones y evolución de esquemas.

### Preguntas trampa (con respuestas)
1) **P:** ¿Event sourcing es lo mismo que arquitectura dirigida por eventos?
  - **R:** No; event sourcing trata sobre persistencia, la [arquitectura dirigida por eventos](event-driven-basics.md) trata sobre comunicación.
2) **P:** ¿Puedes reconstruir el estado si cambias los esquemas de eventos?
   - **R:** Solo si mantienes versiones retrocompatibles o lógica de migración.
3) **P:** ¿Aún necesitas idempotencia en las proyecciones?
   - **R:** Sí; los replays y reintentos pueden aplicar el mismo evento múltiples veces.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir event sourcing con precisión en 2–3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Arquitectura dirigida por eventos](event-driven-basics.md)
- [Fundamentos de mensajería](messaging-basics.md)
- [Fundamentos de modelado de datos](../databases/data-modeling-basics.md)
- [CQRS](cqrs.md)

### Temas relacionados
- [Patrón Outbox](outbox-pattern.md)
- [Patrón de escritura dual](dual-write-pattern.md)
- [Kafka](kafka.md)
- [Diseño Dirigido por Dominio (DDD)](domain-driven-design.md)

### Comparar con
- [Change Data Capture (CDC)](../databases/change-data-capture.md) — CDC captura cambios de BD; event sourcing modela la intención del dominio.

## Notas / Bandeja de entrada (opcional)
- N/A
