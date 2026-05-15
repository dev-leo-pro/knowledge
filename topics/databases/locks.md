---
id: locks
title: "Bloqueos"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, concurrency]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Bloqueos

## TL;DR (BLUF)
- Los bloqueos protegen la consistencia de datos controlando el acceso concurrente.
- Úsalos implícitamente mediante transacciones y niveles de aislamiento.
- Trade-off: los bloqueos pueden reducir la concurrencia y causar esperas.

## Definición
**Qué es:** Un mecanismo para prevenir operaciones conflictivas sobre los mismos datos.
**Términos clave:** bloqueo de fila, bloqueo de tabla, espera de bloqueo, nivel de aislamiento.

## Por qué importa
- Los bloqueos mantienen los datos consistentes bajo escrituras concurrentes.
- El bloqueo excesivo causa contención y picos de latencia.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de bloqueos e impacto operativo.
**Fuera del alcance / NO resuelto por esto:** bloqueo distribuido entre servicios.

## Modelo mental / Intuición
- Piensa en los bloqueos como "acceso reservado" a un recurso.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas consistencia estricta en actualizaciones concurrentes.
### Evítalo cuando
- Las cargas de trabajo con muchas lecturas pueden usar aislamiento por snapshot o enfoques optimistas.

## Cómo lo usaría (práctico)
- **Contexto:** Actualización de una fila de saldo compartido.
- **Pasos:** envolver la actualización en una transacción → mantenerla corta → monitorear esperas.
- **Cómo se ve el éxito:** esperas de bloqueo mínimas con actualizaciones consistentes.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** garantías fuertes de corrección.
- **Desventajas / Riesgos:** contención y deadlocks.
### Alternativas
- **Control de concurrencia optimista:** verificaciones de versión y reintentos.
- **Cómo elegir:** bloqueos para corrección estricta; control optimista para alta concurrencia.

## Modos de fallo y trampas
- Esperas de bloqueo debido a transacciones largas.
- Deadlocks entre transacciones que compiten.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tiempo de espera de bloqueo, consultas bloqueadas.
- **Logs:** errores de timeout de bloqueo.
- **Alertas:** aumento de esperas de bloqueo o timeouts.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Mantener las transacciones cortas
  - [ ] Evitar lecturas con bloqueo innecesarias

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Bloquear más filas de las necesarias.
  - **Por qué es malo:** reduce la concurrencia.
  - **Mejor enfoque:** predicados estrechos y transacciones cortas.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los bloqueos coordinan el acceso concurrente para que las actualizaciones no entren en conflicto. Protegen la corrección pero pueden ralentizar las cosas cuando las transacciones son largas o frecuentes.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los bloqueos siempre son malos para el rendimiento?
   - **R:** no; son necesarios para la corrección pero deben minimizarse.
2) **P:** ¿Los bloqueos solo aplican a escrituras?
   - **R:** no; algunas lecturas también toman bloqueos dependiendo del aislamiento.
3) **P:** ¿Se pueden evitar los bloqueos por completo?
   - **R:** no si necesitas consistencia estricta.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir los bloqueos con precisión.
- [ ] Puedo describir un trade-off.
- [ ] Puedo nombrar un modo de fallo.
- [ ] Puedo explicar cómo reducir la contención de bloqueos.
- [ ] Puedo relacionar los bloqueos con las transacciones.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Transacciones](transactions.md)

### Temas relacionados
- [Deadlocks](deadlocks.md)

### Comparar con
- [Control de concurrencia optimista](optimistic-concurrency-control.md)
