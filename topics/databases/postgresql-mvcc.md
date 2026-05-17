---
id: postgresql-mvcc
title: "PostgreSQL MVCC"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, postgresql, concurrency]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# PostgreSQL MVCC

## TL;DR (BLUF)
- MVCC permite que lectores y escritores procedan sin bloquearse usando versiones de filas.
- Úsalo para entender el aislamiento, vacuum y comportamiento de bloat.
- Trade-off: las versiones antiguas de filas se acumulan y deben ser limpiadas con vacuum.

## Definición
**Qué es:** Control de Concurrencia Multi-Versión almacena múltiples versiones de filas para proporcionar lecturas por snapshot.
**Términos clave:** versiones de fila, snapshots, visibilidad.

## Por qué importa
- Explica por qué las lecturas no bloquean las escrituras (y viceversa) en PostgreSQL.
- También explica el bloat y la necesidad de vacuum.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de MVCC, visibilidad, impacto en almacenamiento.
**Fuera del alcance / NO resuelto por esto:** concurrencia distribuida entre nodos.

## Modelo mental / Intuición
- Piensa en cada actualización como escribir una nueva versión mientras las versiones antiguas permanecen hasta ser limpiadas con vacuum.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas razonar sobre aislamiento y rendimiento bajo concurrencia.
### Evítalo cuando
- Intentas razonar solo sobre bloqueos (MVCC aún importa).

## Cómo lo usaría (práctico)
- **Contexto:** Investigando bloat de tabla y actualizaciones lentas.
- **Pasos:** verificar comportamiento de MVCC → comprobar tuplas muertas → ajustar autovacuum.
- **Cómo se ve el éxito:** tamaño de tabla estable y latencia de consultas predecible.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** alta concurrencia de lectura/escritura.
- **Desventajas / Riesgos:** bloat y sobrecarga de mantenimiento.
### Alternativas
- **Solo concurrencia basada en bloqueos:** más simple pero más bloqueante.
- **Cómo elegir:** MVCC es central en Postgres; enfócate en ajustar vacuum.

## Modos de fallo y trampas
- Transacciones de larga duración previniendo la limpieza.
- Mala interpretación de la visibilidad llevando a actualizaciones "faltantes".

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tuplas muertas, crecimiento del tamaño de tabla.
- **Logs:** actividad de autovacuum.
- **Alertas:** bloat en aumento o retraso de vacuum.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Monitorear transacciones de larga duración
  - [ ] Monitorear tuplas muertas

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Permitir que transacciones largas se ejecuten indefinidamente.
  - **Por qué es malo:** vacuum no puede limpiar versiones antiguas.
  - **Mejor enfoque:** mantener las transacciones cortas.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- MVCC en Postgres significa que cada actualización crea una nueva versión de fila. Los lectores ven un snapshot sin bloquear a los escritores, pero las versiones antiguas deben ser limpiadas por vacuum para evitar bloat.

### Preguntas trampa (con respuestas)
1) **P:** ¿MVCC elimina todos los bloqueos?
   - **R:** no; los bloqueos aún existen para ciertas operaciones.
2) **P:** ¿Las versiones antiguas de filas se eliminan inmediatamente?
   - **R:** no; vacuum las elimina después.
3) **P:** ¿Las transacciones largas afectan la limpieza de MVCC?
   - **R:** sí; previenen que las versiones antiguas sean eliminadas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir MVCC.
- [ ] Puedo explicar por qué vacuum es necesario.
- [ ] Puedo nombrar un modo de fallo.
- [ ] Puedo describir una señal.
- [ ] Puedo relacionar MVCC con el aislamiento.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Transacciones](transactions.md)

### Temas relacionados
- [Bloqueos](locks.md)
- [Bloat en PostgreSQL](postgresql-bloat.md)
- [Vacuum y autovacuum](postgresql-vacuum-autovacuum.md)

### Comparar con
- [Control de concurrencia basado en bloqueos](lock-based-concurrency-control.md)
