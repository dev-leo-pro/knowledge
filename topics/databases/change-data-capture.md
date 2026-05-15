---
id: change-data-capture
title: "Captura de Datos de Cambio (CDC)"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, streaming]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Captura de Datos de Cambio (CDC)

## TL;DR (BLUF)
- CDC captura cambios de datos y los emite a sistemas downstream.
- Úsalo para proyecciones, sincronización y flujos de trabajo orientados a eventos.
- Trade-off: entrega al-menos-una-vez y límites de ordenamiento.

## Definición
**Qué es:** Un mecanismo para transmitir inserciones/actualizaciones/eliminaciones de una base de datos a consumidores.
**Términos clave:** CDC basado en log, flujo de cambios, ordenamiento.

## Por qué importa
- Permite integración casi en tiempo real sin polling.
- Requiere consumidores idempotentes y manejo de repeticiones.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos de CDC y consumo downstream.
**Fuera del alcance / NO resuelto por esto:** garantías de exactamente-una-vez entre sistemas.

## Modelo mental / Intuición
- Piensa en CDC como un log de cambios alimentando suscriptores.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas propagar cambios de datos a otros sistemas.
### Evítalo cuando
- Una sincronización por lotes es suficiente y más simple.

## Cómo lo usaría (práctico)
- **Contexto:** Sincronizar cambios de la BD a un índice de búsqueda.
- **Pasos:** habilitar CDC → consumir el flujo → asegurar idempotencia → monitorear el retraso.
- **Cómo se ve el éxito:** estado downstream consistente con bajo retraso.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** sincronización casi en tiempo real.
- **Desventajas / Riesgos:** duplicados, restricciones de ordenamiento, gestión de retraso.
### Alternativas
- **Polling:** más simple pero más lento.
- **Cómo elegir:** usa CDC cuando la oportunidad importa y puedes manejar duplicados.

## Modos de fallo y trampas
- Consumidores no idempotentes.
- Acumulación y picos de retraso.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** retraso, tasa de errores, tasa de reintentos.
- **Logs:** errores de consumidores.
- **Alertas:** retraso creciente o picos de reintentos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Hacer consumidores idempotentes
  - [ ] Monitorear el retraso

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Asumir entrega exactamente-una-vez.
  - **Por qué es malo:** los duplicados son normales.
  - **Mejor enfoque:** diseñar consumidores idempotentes.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- CDC transmite cambios de la base de datos a otros sistemas. Es poderoso para sincronización en tiempo real, pero debes manejar duplicados y retraso.

### Preguntas trampa (con respuestas)
1) **P:** ¿CDC es siempre exactamente-una-vez?
   - **R:** no; la mayoría de los sistemas CDC son al-menos-una-vez.
2) **P:** ¿CDC es siempre mejor que polling?
   - **R:** no; el polling puede ser más simple para uso de baja frecuencia.
3) **P:** ¿Se puede ignorar el ordenamiento?
   - **R:** no; el ordenamiento importa para la corrección.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir CDC.
- [ ] Puedo explicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un modo de fallo.
- [ ] Puedo describir una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Event-driven basics](../architecture/event-driven-basics.md)

### Temas relacionados
- [DynamoDB Streams](dynamodb-streams.md)

### Comparar con
- [Batch ETL](../operations/batch-etl.md)
