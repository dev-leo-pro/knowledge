---
id: transactions
title: "Transacciones"
type: concept
status: learning
importance: 70
difficulty: medium
tags: [database, sql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Transacciones

## TL;DR (BLUF)
- Las transacciones agrupan operaciones en una unidad de todo-o-nada.
- Úsalas para mantener la consistencia a través de múltiples escrituras.
- Trade-off: transacciones más largas aumentan los bloqueos y la contención.

## Definición
**Qué es:** Una secuencia de operaciones ejecutadas atómicamente con garantías de aislamiento.
**Términos clave:** ACID, niveles de aislamiento, commit, rollback.

## Por qué importa
- Previene actualizaciones parciales y corrupción de datos.
- El mal uso de transacciones lleva a contención de bloqueos y deadlocks.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de atomicidad y aislamiento, cuándo usar transacciones.
**Fuera del alcance / NO resuelto por esto:** transacciones distribuidas entre sistemas.

## Modelo mental / Intuición
- Piensa en una transacción como un contenedor seguro: o todo tiene éxito o nada cambia.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas consistencia a través de múltiples escrituras relacionadas.
- Debes prevenir la exposición de estado parcial.
### Evítalo cuando
- Puedes tolerar consistencia eventual y quieres mayor throughput.
- Necesitas atomicidad cross-servicio (usa sagas/outbox).

## Cómo lo usaría (práctico)
- **Contexto:** Transferir fondos entre cuentas.
- **Pasos:** iniciar transacción → debitar → acreditar → commit → manejar rollback en errores.
- **Cómo se ve el éxito:** sin transferencias parciales y saldos consistentes.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** consistencia y corrección fuertes.
- **Desventajas / Riesgos:** contención, deadlocks, throughput reducido.
### Alternativas
- **Patrón outbox:** para consistencia cross-servicio.
- **Cómo elegir:** usa transacciones para atomicidad en una sola base de datos.

## Modos de fallo y trampas
- Transacciones largas causando acumulación de bloqueos.
- Deadlocks bajo alta concurrencia.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** esperas de bloqueo, conteo de deadlocks, duración de transacciones.
- **Logs:** logs de deadlock.
- **Alertas:** picos sostenidos de espera de bloqueo.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Mantener transacciones cortas
  - [ ] Manejar reintentos en fallos de serialización

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Mantener transacciones abiertas durante llamadas de red.
  - **Por qué es malo:** contención de bloqueos y timeouts.
  - **Mejor enfoque:** hacer llamadas externas fuera de la transacción.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las transacciones hacen que un grupo de operaciones de BD sea atómico y aislado. Mantienen los datos consistentes, pero transacciones largas pueden ralentizar el sistema y causar deadlocks.

### Preguntas trampa (con respuestas)
1) **P:** ¿Las transacciones siempre son necesarias?
   - **R:** no; algunos flujos de trabajo pueden tolerar consistencia eventual.
2) **P:** ¿Las transacciones previenen deadlocks?
   - **R:** no; aún pueden producirse deadlocks bajo concurrencia.
3) **P:** ¿Una transacción es solo para escrituras?
   - **R:** no; las lecturas pueden estar en transacciones para snapshots consistentes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir una transacción con precisión.
- [ ] Puedo decir cuándo usarla.
- [ ] Puedo describir un trade-off.
- [ ] Puedo nombrar un modo de fallo.
- [ ] Puedo explicar el aislamiento a alto nivel.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Propiedades ACID](acid-properties.md)

### Temas relacionados
- [Bloqueos](locks.md)
- [Deadlocks](deadlocks.md)

### Comparar con
- [Patrón outbox](../architecture/outbox-pattern.md)
