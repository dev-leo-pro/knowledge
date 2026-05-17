---
id: saga-pattern
title: "Saga Pattern"
type: pattern
status: learning
importance: 55
difficulty: medium
tags: [architecture, distributed-systems, reliability]
canonical: true
aliases: [saga]
created_at: 2026-01-21
updated_at: 2026-01-21
---

# Patrón Saga

## TL;DR (BLUF)
- Una saga coordina una transacción distribuida como una secuencia de transacciones locales con compensaciones.
- Úsala para flujos de trabajo entre servicios donde ACID entre servicios no es posible.
- Trade-off: consistencia eventual y manejo de fallos complejo.

## Definición
**Qué es:** Un patrón para gestionar flujos de trabajo multi-paso y entre servicios usando transacciones locales y acciones compensatorias para deshacer trabajo parcial.
**Términos clave:** saga, compensación, orquestación, coreografía, transacción local, consistencia eventual.

## Por qué importa
- Proporciona una forma fiable de manejar fallos en flujos de trabajo distribuidos.
- Evita el acoplamiento estrecho y los bloqueos globales entre servicios.

## Alcance y no-objetivos
**Dentro del alcance:** flujo de saga, estrategia de compensación y trade-offs de fiabilidad.
**Fuera del alcance / NO resuelto por esto:** consistencia fuerte entre servicios o equivalentes de 2PC.

## Modelo mental / Intuición
- Piensa en una reserva de viaje: vuelo + hotel + coche. Si uno falla, cancelas los otros.
- Cada paso hace commit local; las compensaciones deshacen los efectos de negocio.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites flujos de trabajo entre servicios sin transacciones distribuidas.
- Cada paso pueda definir una acción compensatoria.
- La consistencia eventual sea aceptable.
### Evítalo cuando
- Necesites atomicidad estricta entre servicios.
- La compensación sea imposible o insegura.
- El flujo de trabajo sea lo bastante simple para una transacción de un solo servicio.

## Cómo lo usaría (práctico)
- **Contexto:** Creación de orden que abarca servicios de pagos, inventario y envío.
- **Pasos:**
  1) Modelar cada paso como una transacción local.
  2) Definir compensaciones para cada paso.
  3) Elegir orquestación (coordinador central) o coreografía (eventos).
  4) Agregar timeouts, reintentos y una cola de mensajes muertos.
- **Cómo se ve el éxito:** sin recursos huérfanos y rutas de recuperación claras.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** evita bloqueos distribuidos, escala entre servicios, resiliente a fallos parciales.
- **Contras / Riesgos:** consistencia eventual, lógica de compensación compleja y observabilidad más difícil.
### Alternativas
- **[Arquitectura request-response](request-response.md):** más simple para flujos de trabajo síncronos de un solo servicio.
- **[Patrón Outbox](outbox-pattern.md):** publicación de eventos más segura para transacciones locales.
- **Cómo elegir:** usa sagas cuando la coordinación multi-servicio y la recuperación de fallos sean lo más importante.

## Modos de fallo y errores comunes
- Compensaciones no idempotentes, causando sobre-corrección.
- La orquestación convirtiéndose en un punto único de fallo.
- La coreografía generando acoplamiento oculto y propiedad del flujo poco clara.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tiempo de completitud de saga, tasa de rollback, fallos de compensación.
- **Logs:** ID de saga, transiciones de pasos, resultados de compensación.
- **Trazas:** spans end-to-end de saga con tiempos por paso.
- **Alertas:** alta tasa de rollback, sagas atascadas o fallos de compensación repetidos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir compensaciones para cada paso
  - [ ] Asegurar idempotencia para pasos y compensaciones
  - [ ] Correlacionar todos los pasos con un ID de saga
- **Notas de seguridad / cumplimiento**
  - Asegurar que las compensaciones preserven las pistas de auditoría.
- **Notas de rendimiento**
  - Evitar bloqueos de larga duración; usar timeouts y reintentos asíncronos.
- **Notas operacionales**
  - Monitorear sagas atascadas o de larga duración.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar sagas para imitar ACID estricto entre servicios.
  - **Por qué es malo:** las compensaciones no son rollbacks reales y pueden dejar efectos secundarios.
  - **Mejor enfoque:** rediseñar el flujo de trabajo o consolidar en un solo servicio cuando sea posible.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Una saga divide una transacción distribuida en transacciones locales y usa acciones compensatorias para deshacer trabajo parcial. Intercambia consistencia estricta por resiliencia y escalabilidad.

### Preguntas trampa (con respuestas)
1) **P:** ¿Las compensaciones son lo mismo que rollbacks de base de datos?
   - **R:** No; son acciones a nivel de negocio que pueden no deshacer completamente los efectos secundarios.
2) **P:** ¿Una saga garantiza ejecución exactamente-una-vez?
   - **R:** No; los pasos deben ser idempotentes y resilientes a reintentos.
3) **P:** ¿La coreografía siempre es mejor que la orquestación?
   - **R:** No; la coreografía puede volverse opaca y estrechamente acoplada sin propiedad clara.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el patrón saga con precisión.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Arquitectura dirigida por eventos](event-driven-basics.md)
- [Transacciones](../databases/transactions.md)
- [Propiedades ACID](../databases/acid-properties.md)

### Temas relacionados
- [Patrón Outbox](outbox-pattern.md)
- [Patrón de escritura dual](dual-write-pattern.md)
- [Change Data Capture (CDC)](../databases/change-data-capture.md)

### Comparar con
- [Arquitectura request-response](request-response.md) — flujo síncrono vs flujo de trabajo distribuido.

## Notas / Bandeja de entrada (opcional)
- N/A
