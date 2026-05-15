---
id: acid-properties
title: "Propiedades ACID"
type: concept
status: learning
importance: 65
difficulty: medium
tags: [database, transactions]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Propiedades ACID

## TL;DR (BLUF)
- ACID define las garantías transaccionales: Atomicidad, Consistencia, Aislamiento, Durabilidad.
- Úsalo para razonar sobre la corrección en bases de datos relacionales.
- Trade-off: garantías más fuertes a menudo reducen el rendimiento.

## Definición
**Qué es:** Un conjunto de garantías que aseguran la corrección transaccional.
**Términos clave:** atomicidad, consistencia, aislamiento, durabilidad.

## Por qué importa
- Previene escrituras parciales y corrupción de datos.
- Guía los trade-offs entre corrección y rendimiento.

## Alcance y no-objetivos
**Dentro del alcance:** Significado e implicaciones de ACID.
**Fuera del alcance / NO resuelto por esto:** transacciones distribuidas entre sistemas.

## Modelo mental / Intuición
- ACID es un contrato de seguridad para las transacciones.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas garantías fuertes de corrección.
### Evítalo cuando
- La consistencia eventual es aceptable y el rendimiento es más importante.

## Cómo lo usaría (práctico)
- **Contexto:** Flujos de trabajo bancarios o financieros.
- **Pasos:** usar transacciones → elegir nivel de aislamiento → verificar durabilidad.
- **Cómo se ve el éxito:** sin actualizaciones parciales ni datos perdidos.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** corrección fuerte.
- **Desventajas / Riesgos:** rendimiento reducido y mayor contención.
### Alternativas
- **Consistencia eventual:** para sistemas de gran escala.
- **Cómo elegir:** priorizar ACID para sistemas donde la corrección es crítica.

## Modos de fallo y trampas
- Malentender el aislamiento llevando a anomalías.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** fallos de transacciones, esperas de bloqueos.
- **Logs:** abortos de transacciones.
- **Alertas:** picos en abortos o deadlocks.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir el nivel de aislamiento apropiado
  - [ ] Mantener las transacciones cortas

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Asumir ACID entre microservicios.
  - **Por qué es malo:** la consistencia entre sistemas no es automática.
  - **Mejor enfoque:** usar sagas o patrones outbox.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- ACID describe cómo se comportan las transacciones: son atómicas, consistentes, aisladas y durables. Es la línea base de corrección en bases de datos relacionales.

### Preguntas trampa (con respuestas)
1) **P:** ¿Se garantiza ACID entre servicios?
   - **R:** no; típicamente es dentro de una sola base de datos.
2) **P:** ¿ACID significa alta disponibilidad?
   - **R:** no; se trata de corrección, no de disponibilidad.
3) **P:** ¿Se puede relajar el aislamiento por rendimiento?
   - **R:** sí; los niveles de aislamiento intercambian corrección por rendimiento.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir ACID con precisión.
- [ ] Puedo explicar cada letra.
- [ ] Puedo mencionar un trade-off.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo explicar los niveles de aislamiento a alto nivel.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Transactions](transactions.md)

### Temas relacionados
- [Locks](locks.md)

### Comparar con
- [Consistency models](consistency-models.md)
