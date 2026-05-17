---
id: lock-based-concurrency-control
title: "Lock-Based Concurrency Control"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, concurrency]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Control de Concurrencia Basado en Bloqueos

## TL;DR (BLUF)
- El control basado en bloqueos usa locks para prevenir operaciones conflictivas.
- Úsalo cuando los conflictos sean comunes y la corrección sea crítica.
- Trade-off: concurrencia reducida y posibles deadlocks.

## Definición
**Qué es:** Una estrategia de concurrencia que usa bloqueos para serializar operaciones conflictivas.
**Términos clave:** bloqueos, bloqueo de espera, deadlocks.

## Por qué importa
- Proporciona corrección fuerte bajo contención.
- Puede reducir el throughput y aumentar la latencia.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos y trade-offs del control de concurrencia basado en bloqueos.
**Fuera del alcance / NO resuelto por esto:** bloqueo distribuido entre servicios.

## Modelo mental / Intuición
- Solo un escritor a la vez por recurso bloqueado.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los conflictos sean frecuentes y la corrección sea primordial.
### Evítalo cuando
- Los conflictos sean raros y el control optimista sea más eficiente.

## Cómo lo usaría (práctico)
- **Contexto:** Decremento de inventario bajo alta contención.
- **Pasos:** bloquear fila → actualizar → hacer commit rápidamente.
- **Cómo se ve el éxito:** conteos correctos con tiempos de espera de bloqueo manejables.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** corrección fuerte.
- **Contras / Riesgos:** bloqueo de espera y deadlocks.
### Alternativas
- **Control de concurrencia optimista:** para cargas de trabajo de bajo conflicto.
- **Cómo elegir:** basado en bloqueos para escenarios de alto conflicto.

## Modos de fallo y errores comunes
- Transacciones largas aumentando tiempos de espera de bloqueo.
- Deadlocks bajo orden de bloqueo competitivo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** esperas de bloqueo, deadlocks.
- **Logs:** errores de timeout de bloqueo.
- **Alertas:** tiempo de espera de bloqueo creciente.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Mantener transacciones cortas
  - [ ] Usar orden de bloqueo consistente

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Bloquear más datos de los necesarios.
  - **Por qué es malo:** reduce la concurrencia.
  - **Mejor enfoque:** limitar el alcance del bloqueo.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El control de concurrencia basado en bloqueos previene conflictos bloqueando el acceso. Es seguro pero puede reducir el throughput y causar deadlocks si no se gestiona cuidadosamente.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los bloqueos siempre son peores que el control optimista?
   - **R:** No; para cargas de trabajo de alto conflicto los bloqueos son más seguros.
2) **P:** ¿Los bloqueos eliminan la necesidad de reintentos?
   - **R:** No; los deadlocks aún requieren reintentos.
3) **P:** ¿Los bloqueos son solo para escrituras?
   - **R:** No; algunas lecturas también toman bloqueos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el control de concurrencia basado en bloqueos.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar el orden de bloqueos.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Bloqueos](locks.md)

### Temas relacionados
- [Deadlocks](deadlocks.md)

### Comparar con
- [Control de concurrencia optimista](optimistic-concurrency-control.md)
