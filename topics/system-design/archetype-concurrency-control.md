---
id: archetype-concurrency-control
title: "Control de Concurrencia (Evitar Doble Reserva / Condiciones de Carrera)"
type: pattern
status: learning
importance: 95
difficulty: hard
tags: [system-design, locking, optimistic-locking, pessimistic-locking, concurrency, race-conditions]
canonical: true
aliases: [optimistic-locking, pessimistic-locking, leasing, compare-and-swap]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Control de Concurrencia (Evitar Doble Reserva / Condiciones de Carrera)

## TL;DR
- Garantizar corrección cuando múltiples actores actualizan el mismo recurso (inventario, asientos, billeteras).
- Desafíos clave: sobreventa, alta contención en filas/claves calientes, tensión latencia vs corrección.
- Soluciones: concurrencia optimista (campo de versión), bloqueo pesimista / lease, cola por clave de recurso.

## Dónde duele (por qué duele)
1. **Sobreventa de asientos / inventario**: Dos usuarios compran el último boleto de concierto simultáneamente → ambos confirmados
   - **Solución**: Bloqueo pesimista (SELECT FOR UPDATE) u optimista (verificación de versión + reintento)
2. **Alta contención en filas/claves calientes**: Artículo popular → todas las peticiones se serializan en el bloqueo → pico de latencia
   - **Solución**: Cola por recurso; lease con expiración; sharding (si aplica)
3. **Tensión latencia vs corrección**: Bloqueo estricto → lento; optimista → reintentos bajo contención
   - **Solución**: Elegir según contención: baja → optimista; alta → pesimista o cola

## Reglas de decisión
- **Usar cuando**: Recursos escasos (inventario, asientos, billeteras), corrección financiera crítica, actualizaciones concurrentes esperadas
- **Evitar cuando**: No hay actualizaciones concurrentes (escritor único), consistencia eventual aceptable

## Trade-offs
- **Optimista**: Rápido (sin bloqueos), reintento en conflicto → bueno para baja contención
- **Pesimista**: Corrección estricta, serializado → bueno para alta contención, recursos críticos
- **Elegir**: Baja contención → optimista; alta contención / crítico → pesimista

## Ejemplo explícito
Dos usuarios intentan comprar el último boleto de concierto. Sin control de concurrencia, ambas transacciones leen "1 disponible", ambas decrementan a 0, ambas confirman → sobreventa. Con bloqueo pesimista: la primera transacción bloquea la fila, la segunda espera; la primera confirma (agotado), la segunda ve 0 disponibles.

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Control de Concurrencia Optimista](../databases/optimistic-concurrency-control.md), [Concurrencia Basada en Bloqueos](../databases/lock-based-concurrency-control.md), [Transacciones](../databases/transactions.md)
