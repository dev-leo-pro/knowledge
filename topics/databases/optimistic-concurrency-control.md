---
id: optimistic-concurrency-control
title: "Control de concurrencia optimista"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, concurrency]
canonical: true
aliases: ["occ"]
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Control de concurrencia optimista

## TL;DR (BLUF)
- El control de concurrencia optimista asume que los conflictos son raros y verifica versiones al escribir.
- Úsalo para cargas de trabajo de alta concurrencia con bajas tasas de conflicto.
- Trade-off: los conflictos requieren reintentos y manejo de errores.

## Definición
**Qué es:** Un enfoque de concurrencia que usa verificaciones de versión para detectar conflictos en el momento del commit.
**Términos clave:** columna de versión, compare-and-swap, reintentos.

## Por qué importa
- Reduce la sobrecarga de bloqueos y mejora la concurrencia.
- Un mal manejo de reintentos lleva a actualizaciones perdidas o errores.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos y trade-offs de OCC.
**Fuera del alcance / NO resuelto por esto:** transacciones distribuidas.

## Modelo mental / Intuición
- Piensa "verifica antes de escribir; reintenta si cambió."

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los conflictos son raros y el throughput importa.
### Evítalo cuando
- Los conflictos son comunes y los reintentos serían costosos.

## Cómo lo usaría (práctico)
- **Contexto:** Actualizando configuraciones de perfil de usuario.
- **Pasos:** leer versión → actualizar con verificación de versión → reintentar en conflicto.
- **Cómo se ve el éxito:** pocos conflictos y reintentos mínimos.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** alta concurrencia sin bloqueos pesados.
- **Desventajas / Riesgos:** reintentos y complejidad en el manejo de conflictos.
### Alternativas
- **Control de concurrencia basado en bloqueos:** para cargas de trabajo con muchos conflictos.
- **Cómo elegir:** elige OCC cuando los conflictos son bajos.

## Modos de fallo y trampas
- Verificaciones de versión faltantes que llevan a actualizaciones perdidas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de conflictos, tasa de reintentos.
- **Logs:** conflictos de actualización.
- **Alertas:** picos en reintentos o errores de conflicto.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Agregar columna de versión
  - [ ] Implementar reintento con backoff

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ignorar errores de conflicto.
  - **Por qué es malo:** pérdida silenciosa de datos.
  - **Mejor enfoque:** exponer errores y reintentar.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El control de concurrencia optimista verifica una versión al escribir. Si alguien más cambió la fila, la escritura falla y reintentas. Es genial cuando los conflictos son raros.

### Preguntas trampa (con respuestas)
1) **P:** ¿OCC elimina los conflictos?
   - **R:** no; los detecta y requiere reintentos.
2) **P:** ¿OCC siempre es mejor que el bloqueo?
   - **R:** no; para cargas de trabajo con muchos conflictos, el bloqueo puede ser mejor.
3) **P:** ¿Se puede hacer OCC sin un campo de versión?
   - **R:** no de forma confiable; necesitas un detector de conflictos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir OCC.
- [ ] Puedo decir cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un modo de fallo.
- [ ] Puedo explicar el comportamiento de reintento.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Transacciones](transactions.md)

### Temas relacionados
- [Bloqueos](locks.md)

### Comparar con
- [Control de concurrencia basado en bloqueos](lock-based-concurrency-control.md)
