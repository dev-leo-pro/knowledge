---
id: scheduled-cleanup-jobs
title: "Trabajos de limpieza programados"
type: pattern
status: learning
importance: 45
difficulty: easy
tags: [operations, maintenance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Trabajos de limpieza programados

## TL;DR (BLUF)
- Los trabajos programados eliminan o archivan datos expirados en una cadencia fija.
- Úsalos cuando el TTL es demasiado impreciso.
- Trade-off: sobrecarga operacional y fallos de trabajos.

## Definición
**Qué es:** Un proceso periódico que elimina o archiva registros expirados.
**Términos clave:** cron, trabajo batch, retención.

## Por qué importa
- Aplica reglas de retención de datos y cumplimiento.
- Trabajos mal diseñados pueden bloquear tablas o provocar picos de I/O.

## Alcance y no-objetivos
**Dentro del alcance:** patrones de limpieza batch y trade-offs.
**Fuera del alcance / NO resuelto por esto:** garantías de eliminación en tiempo real.

## Modelo mental / Intuición
- Piénsalo como un conserje programado.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas temporización precisa de eliminación o cumplimiento.
### Evítalo cuando
- El TTL de mejor esfuerzo es suficiente y más económico.

## Cómo lo usaría (práctico)
- **Contexto:** Eliminar sesiones expiradas cada noche.
- **Pasos:** programar trabajo → eliminar en lotes → monitorear duración.
- **Cómo se ve el éxito:** datos expirados eliminados con carga acotada.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** control preciso.
- **Desventajas / Riesgos:** sobrecarga operacional.
### Alternativas
- **TTL:** limpieza de mejor esfuerzo.
- **Cómo elegir:** usa trabajos para temporización estricta; TTL para conveniencia.

## Modos de fallo y trampas
- Trabajos de larga ejecución bloqueando tablas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** duración del trabajo, filas eliminadas.
- **Logs:** fallos de trabajos.
- **Alertas:** ejecuciones perdidas o timeouts.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Eliminar en lotes
  - [ ] Ejecutar durante horas de baja demanda

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Eliminar millones de filas en una sola transacción.
  - **Por qué es malo:** bloqueos y timeouts.
  - **Mejor enfoque:** procesar en lotes por bloques.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los trabajos de limpieza programados son tareas periódicas que eliminan datos expirados cuando el TTL no es lo suficientemente preciso. Proporcionan control pero requieren cuidado operacional.

### Preguntas trampa (con respuestas)
1) **P:** ¿Son siempre mejores los trabajos programados que el TTL?
   - **R:** no; el TTL es más simple cuando no se requiere precisión.
2) **P:** ¿Pueden los trabajos de limpieza ejecutarse en tráfico pico?
   - **R:** pueden, pero arriesga contención.
3) **P:** ¿Los trabajos de limpieza garantizan eliminación inmediata?
   - **R:** no; se ejecutan según un cronograma.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir trabajos de limpieza programados.
- [ ] Puedo indicar cuándo usarlos.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [TTL (Time-to-Live)](../databases/ttl.md)

### Temas relacionados
- [DynamoDB TTL](../databases/dynamodb-ttl.md)

### Comparar con
- [TTL (Time-to-Live)](../databases/ttl.md) — mejor esfuerzo vs programado.
