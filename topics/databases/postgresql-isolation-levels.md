---
id: postgresql-isolation-levels
title: "Niveles de aislamiento en PostgreSQL"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, postgresql, transactions]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Niveles de aislamiento en PostgreSQL

## TL;DR (BLUF)
- El aislamiento controla cómo las transacciones ven los cambios concurrentes.
- Usa un aislamiento mayor para corrección; acepta uno menor para throughput.
- Trade-off: un aislamiento más fuerte puede aumentar reintentos y contención.

## Definición
**Qué es:** Reglas de visibilidad de transacciones: Read Committed, Repeatable Read, Serializable.
**Términos clave:** nivel de aislamiento, fallos de serialización, anomalías.

## Por qué importa
- Determina las garantías de corrección bajo concurrencia.
- Un aislamiento incorrecto puede llevar a bugs sutiles.

## Alcance y no-objetivos
**Dentro del alcance:** niveles de aislamiento de Postgres y trade-offs.
**Fuera del alcance / NO resuelto por esto:** aislamiento de transacciones distribuidas.

## Modelo mental / Intuición
- Piensa en el aislamiento como qué tan estable es tu snapshot durante una transacción.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas corrección más fuerte (ej., operaciones financieras).
### Evítalo cuando
- El throughput es más importante que el aislamiento estricto.

## Cómo lo usaría (práctico)
- **Contexto:** Transferencias de saldos de cuentas.
- **Pasos:** usar Serializable o Repeatable Read → manejar reintentos → mantener transacciones cortas.
- **Cómo se ve el éxito:** corrección con reintentos manejables.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** corrección más fuerte.
- **Desventajas / Riesgos:** mayor contención y reintentos.
### Alternativas
- **Verificaciones a nivel de aplicación:** cuando el aislamiento es demasiado costoso.
- **Cómo elegir:** usa el aislamiento más bajo que aún garantice corrección.

## Modos de fallo y trampas
- Fallos de serialización no manejados con reintentos.
- Asumir que Repeatable Read previene todas las anomalías.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** fallos de serialización, tasa de reintentos.
- **Logs:** abortos de transacciones.
- **Alertas:** picos en fallos de serialización.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir el aislamiento mínimo seguro
  - [ ] Implementar lógica de reintento para errores de serialización

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ejecutar todas las transacciones en Serializable.
  - **Por qué es malo:** contención innecesaria.
  - **Mejor enfoque:** solo aumentar el aislamiento cuando sea necesario.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los niveles de aislamiento definen qué tan estable es tu vista de los datos mientras una transacción se ejecuta. Postgres usa Read Committed por defecto; niveles más altos dan garantías más fuertes pero pueden aumentar los reintentos.

### Preguntas trampa (con respuestas)
1) **P:** ¿Repeatable Read previene todas las anomalías?
   - **R:** no; serializable es el más fuerte y aún puede fallar con reintentos.
2) **P:** ¿Los fallos de serialización son bugs?
   - **R:** no; son esperados y deben reintentarse.
3) **P:** ¿Read Committed siempre es seguro?
   - **R:** no; algunos flujos de trabajo requieren garantías más fuertes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo nombrar los niveles de aislamiento de Postgres.
- [ ] Puedo explicar un trade-off.
- [ ] Puedo describir las necesidades de reintento.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo relacionar el aislamiento con MVCC.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Transacciones](transactions.md)
- [PostgreSQL MVCC](postgresql-mvcc.md)

### Temas relacionados
- [Deadlocks](deadlocks.md)

### Comparar con
- [Modelos de consistencia](consistency-models.md)
