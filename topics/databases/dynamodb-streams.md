---
id: dynamodb-streams
title: "DynamoDB Streams"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, dynamodb]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# DynamoDB Streams

## TL;DR (BLUF)
- DynamoDB Streams es un mecanismo de [Change Data Capture (CDC)](change-data-capture.md) nativo de DynamoDB.
- Úsalo para flujos de trabajo dirigidos por eventos y proyecciones.
- Trade-off: entrega at-least-once y restricciones de ordenamiento.

## Definición
**Qué es:** Un flujo de registros de cambio (INSERT/MODIFY/REMOVE) para una tabla DynamoDB.
**Términos clave:** CDC, registros de stream, entrega at-least-once.

## Por qué importa
- Permite procesamiento dirigido por eventos sin polling.
- Debes manejar reintentos y eventos duplicados.

## Alcance y no-objetivos
**Dentro del alcance:** captura de cambios y consumidores downstream.
**Fuera del alcance / NO resuelto por esto:** garantías de procesamiento exactamente-una-vez.

## Modelo mental / Intuición
- Piensa en Streams como un log de cambios para tu tabla.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites reaccionar a cambios de datos.
- Quieras construir proyecciones o disparar flujos de trabajo.
### Evítalo cuando
- Necesites ordenamiento global o semántica exactamente-una-vez.

## Cómo lo usaría (práctico)
- **Contexto:** Sincronizar datos a un índice de búsqueda.
- **Pasos:** habilitar Streams → consumir con Lambda → manejar reintentos/idempotencia.
- **Cómo se ve el éxito:** estado downstream consistente.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** captura de cambios en casi-tiempo-real.
- **Contras / Riesgos:** duplicados y reintentos.
### Alternativas
- **Polling:** más simple pero más lento y costoso.
- **Cómo elegir:** usa Streams cuando necesites reacciones dirigidas por eventos.

## Modos de fallo y errores comunes
- Falta de idempotencia en los consumidores.
- Backlog causando lag.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** lag del stream, tasa de errores, edad del iterador.
- **Logs:** errores y reintentos del consumidor.
- **Alertas:** edad del iterador creciente.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Hacer consumidores idempotentes
  - [ ] Monitorear edad del iterador

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Asumir entrega exactamente-una-vez.
  - **Por qué es malo:** genera duplicados.
  - **Mejor enfoque:** hacer consumidores idempotentes.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- DynamoDB Streams es un log de cambios para actualizaciones de tabla. Permite procesamiento dirigido por eventos pero entrega eventos al menos una vez, así que los consumidores deben ser idempotentes.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los eventos de Streams son exactamente-una-vez?
   - **R:** No; son at-least-once.
2) **P:** ¿Streams preserva ordenamiento global?
   - **R:** No; el ordenamiento es por partición.
3) **P:** ¿Las eliminaciones por TTL aparecen en Streams?
   - **R:** Sí; las eliminaciones por TTL generan registros de stream.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir Streams con precisión.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar los límites de ordenamiento.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [DynamoDB](dynamodb.md)
- [Change Data Capture (CDC)](change-data-capture.md)

### Temas relacionados
- [DynamoDB TTL](dynamodb-ttl.md)

### Comparar con
- [Kafka](../architecture/kafka.md)
