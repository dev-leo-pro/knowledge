---
id: outbox-pattern
title: "Outbox Pattern"
type: pattern
status: learning
importance: 50
difficulty: medium
tags: [architecture, messaging]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Patrón Outbox

## TL;DR (BLUF)
- El patrón outbox escribe eventos en una tabla dentro de la misma transacción que los datos de negocio.
- Úsalo para garantizar la publicación fiable de eventos.
- Trade-off: almacenamiento extra y complejidad de procesamiento.

## Definición
**Qué es:** Un patrón transaccional que persiste eventos en una tabla outbox y luego los publica posteriormente.
**Términos clave:** tabla outbox, CDC, idempotencia.

## Por qué importa
- Previene la pérdida de eventos cuando las transacciones se confirman pero la publicación falla.
- Permite flujos de trabajo dirigidos por eventos fiables.

## Alcance y no-objetivos
**Dentro del alcance:** publicación fiable de eventos desde una BD.
**Fuera del alcance / NO resuelto por esto:** entrega exactamente-una-vez.

## Modelo mental / Intuición
- Escribe tu intención en la base de datos primero, luego publica de forma segura.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites consistencia transaccional entre escrituras de BD y eventos.
### Evítalo cuando
- Puedas tolerar eventos perdidos ocasionalmente.

## Cómo lo usaría (práctico)
- **Contexto:** Evento de orden creada.
- **Pasos:** escribir orden + fila outbox → CDC/worker publica → marcar como enviado.
- **Cómo se ve el éxito:** sin eventos perdidos bajo fallos.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** publicación fiable de eventos.
- **Contras / Riesgos:** procesamiento y almacenamiento extra.
### Alternativas
- **Escrituras duales:** más simple pero arriesgado.
- **Cómo elegir:** usa outbox cuando se requiera fiabilidad.

## Modos de fallo y errores comunes
- Crecimiento de la tabla outbox sin limpieza.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** lag del outbox, tasa de errores de publicación.
- **Logs:** fallos de publicación.
- **Alertas:** backlog creciente del outbox.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Escribir outbox en la misma transacción
  - [ ] Publicar con reintentos e idempotencia

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Publicar directamente antes del commit.
  - **Por qué es malo:** eventos perdidos en rollback.
  - **Mejor enfoque:** usar una tabla outbox.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El patrón outbox escribe eventos en una tabla de BD en la misma transacción que tus datos, luego los publica asíncronamente. Esto evita perder eventos si la publicación falla.

### Preguntas trampa (con respuestas)
1) **P:** ¿El outbox garantiza entrega exactamente-una-vez?
   - **R:** No; aún necesitas consumidores idempotentes.
2) **P:** ¿Puedes omitir la limpieza?
   - **R:** No; las tablas outbox pueden crecer indefinidamente.
3) **P:** ¿El outbox es solo para Kafka?
   - **R:** No; funciona con cualquier transporte de eventos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el patrón outbox.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un modo de fallo.
- [ ] Puedo explicar cómo ayuda CDC.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Transacciones](../databases/transactions.md)

### Temas relacionados
- [Change Data Capture (CDC)](../databases/change-data-capture.md)

### Comparar con
- [Patrón de escritura dual](dual-write-pattern.md)
