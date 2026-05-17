---
id: dual-write-pattern
title: "Patrón Dual-Write"
type: pattern
status: learning
importance: 40
difficulty: medium
tags: [architecture, messaging]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Patrón Dual-Write

## TL;DR (BLUF)
- Dual-write actualiza dos sistemas en una sola operación lógica.
- Úsalo solo cuando puedas tolerar inconsistencias ocasionales.
- Trade-off: riesgo de fallo parcial y divergencia de datos.

## Definición
**Qué es:** Escribir en dos sistemas diferentes (ej., BD y cola) en pasos separados.
**Términos clave:** dual-write, inconsistencia, reintentos.

## Por qué importa
- Es común pero arriesgado sin salvaguardas.
- Los fallos entre escrituras causan divergencia.

## Alcance y no-objetivos
**Dentro del alcance:** riesgos y mitigaciones del dual-write.
**Fuera del alcance / NO resuelto por esto:** garantías de exactamente-una-vez.

## Modelo mental / Intuición
- Dos escrituras = dos oportunidades de fallar.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Puedas [reconciliar](../operations/data-reconciliation.md) inconsistencias después.
### Evítalo cuando
- Necesites consistencia fuerte entre sistemas.

## Cómo lo usaría (práctico)
- **Contexto:** Escritura en BD más publicación de evento.
- **Pasos:** escribir BD → publicar evento → manejar fallos con reintentos.
- **Cómo se ve el éxito:** baja divergencia y playbooks de recuperación.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** simple de implementar.
- **Desventajas / Riesgos:** estado inconsistente en fallo parcial.
### Alternativas
- **Patrón Outbox:** publicación de eventos más segura.
- **Cómo elegir:** preferir outbox cuando la corrección importa.

## Modos de fallo y trampas
- Evento publicado sin commit de BD.
- Commit de BD sin evento publicado.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasas de desajuste, conteos de reintentos.
- **Logs:** fallos de publicación.
- **Alertas:** picos de desajuste.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Construir proceso de [reconciliación](../operations/data-reconciliation.md)
  - [ ] Añadir reintentos con backoff

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Asumir que dual-write es "suficientemente bueno."
  - **Por qué es malo:** divergencia de datos oculta.
  - **Mejor enfoque:** usar un outbox.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Dual-write actualiza dos sistemas por separado. Es simple pero arriesgado porque los fallos parciales crean estado inconsistente, así que normalmente se prefiere un outbox.

### Preguntas trampa (con respuestas)
1) **P:** ¿Dual-write garantiza consistencia?
   - **R:** no; los fallos pueden crear divergencia.
2) **P:** ¿Los reintentos pueden arreglar todos los problemas?
   - **R:** no; los reintentos aún pueden fallar o duplicar.
3) **P:** ¿Es aceptable dual-write para datos críticos?
   - **R:** generalmente no sin [reconciliación](../operations/data-reconciliation.md).

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir dual-write.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un modo de fallo.
- [ ] Puedo explicar una mitigación.
- [ ] Puedo comparar con outbox.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de arquitectura dirigida por eventos](event-driven-basics.md)

### Temas relacionados
- [Patrón Outbox](outbox-pattern.md)

### Comparar con
- [Patrón Outbox](outbox-pattern.md) -- publicación de eventos más segura.
