---
id: deadlocks
title: "Deadlocks"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, concurrency]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Deadlocks

## TL;DR (BLUF)
- Un deadlock ocurre cuando las transacciones se bloquean mutuamente en un ciclo.
- Usa transacciones cortas y orden consistente de bloqueos para evitarlos.
- Trade-off: mayor seguridad puede reducir la concurrencia.

## Definición
**Qué es:** Una espera cíclica donde cada transacción mantiene un bloqueo que la otra necesita.
**Términos clave:** orden de bloqueos, grafo de espera, rollback.

## Por qué importa
- Los deadlocks causan fallos de transacciones y reintentos.
- Altas tasas de deadlocks señalan contención o patrones de acceso pobres.

## Alcance y no-objetivos
**Dentro del alcance:** deadlocks en BD y patrones de mitigación.
**Fuera del alcance / NO resuelto por esto:** deadlocks distribuidos entre servicios.

## Modelo mental / Intuición
- Piensa en dos personas sosteniendo las llaves de los candados del otro.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas consistencia transaccional estricta y puedes tolerar reintentos.
### Evítalo cuando
- Puedes rediseñar el acceso para reducir la superposición de bloqueos.

## Cómo lo usaría (práctico)
- **Contexto:** Actualizaciones concurrentes en filas compartidas.
- **Pasos:** imponer orden consistente de bloqueos → mantener transacciones cortas → reintentar en deadlock.
- **Cómo se ve el éxito:** tasa de deadlocks cercana a cero.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** preserva la corrección bajo concurrencia.
- **Desventajas / Riesgos:** reintentos y picos de latencia.
### Alternativas
- **Control de concurrencia optimista:** verificaciones de versión.
- **Cómo elegir:** usar reintentos de deadlock cuando se requiere consistencia estricta.

## Modos de fallo y trampas
- Transacciones largas manteniendo bloqueos demasiado tiempo.
- Orden inconsistente de bloqueos entre rutas de código.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** conteo de deadlocks, reintentos de transacciones.
- **Logs:** logs del detector de deadlocks.
- **Alertas:** picos en errores de deadlocks.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Mantener transacciones cortas
  - [ ] Ordenar la adquisición de bloqueos consistentemente
  - [ ] Reintentar con backoff

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Reintentar sin arreglar la causa raíz.
  - **Por qué es malo:** oculta la contención.
  - **Mejor enfoque:** reducir la superposición de bloqueos.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Un deadlock es cuando dos transacciones mantienen cada una un bloqueo que la otra necesita, así que ninguna puede avanzar. Las bases de datos detectan esto y abortan una transacción; debes mantener las transacciones cortas y bloquear filas en un orden consistente.

### Preguntas trampa (con respuestas)
1) **P:** ¿Pueden ocurrir deadlocks sin escrituras?
   - **R:** sí; algunas lecturas toman bloqueos dependiendo del aislamiento.
2) **P:** ¿Los deadlocks son un bug en la base de datos?
   - **R:** no; son una realidad de concurrencia con bloqueos.
3) **P:** ¿Se pueden ignorar los deadlocks?
   - **R:** no; debes manejar reintentos y reducir la contención.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir deadlocks con precisión.
- [ ] Puedo nombrar una causa.
- [ ] Puedo explicar una mitigación.
- [ ] Puedo identificar una señal.
- [ ] Puedo explicar la estrategia de reintentos.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Locks](locks.md)

### Temas relacionados
- [Transactions](transactions.md)

### Comparar con
- [Optimistic concurrency control](optimistic-concurrency-control.md)
