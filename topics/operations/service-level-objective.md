---
id: service-level-objective
title: "Objetivo de nivel de servicio (SLO)"
type: concept
status: learning
importance: 75
difficulty: easy
tags: [reliability, observability]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Objetivo de nivel de servicio (SLO)

## TL;DR (BLUF)
- Un SLO es el nivel objetivo de servicio que aspiras a entregar, medido mediante SLIs.
- Usa SLOs para equilibrar fiabilidad y velocidad con un presupuesto de error.
- Trade-off: SLOs más estrictos aumentan el costo y ralentizan la cadencia de cambio.

## Definición
**Qué es:** Un objetivo de fiabilidad (por ejemplo, 99.9% de disponibilidad, 95% de solicitudes bajo 300ms) definido sobre una ventana de tiempo.  
**Términos clave:** objetivo, ventana de tiempo, presupuesto de error, tasa de quema.

## Por qué importa
- Los SLOs crean expectativas compartidas entre producto e ingeniería.
- Guían la priorización cuando la fiabilidad decae.

## Alcance y no-objetivos
**Dentro del alcance:** establecimiento de objetivos para fiabilidad orientada al usuario.  
**Fuera del alcance / NO resuelto por esto:** promesas contractuales (ver [Acuerdo de nivel de servicio (SLA)](service-level-agreement.md)).

## Modelo mental / Intuición
- "¿Qué tan buenos necesitamos ser para que los usuarios estén satisfechos?"

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas objetivos claros de fiabilidad vinculados a la experiencia del usuario.
- Quieres gestionar trade-offs con [Presupuestos de error](error-budgets.md).

### Evítalo cuando
- No puedes medir el SLI de forma confiable.

## Cómo lo usaría (práctico)
- **Contexto:** Una API B2B con clientes empresariales.
- **Pasos:**
  1) Definir SLIs para latencia y disponibilidad.
  2) Establecer SLOs (por ejemplo, 99.9% de disponibilidad, 95% < 300ms).
  3) Usar la quema del presupuesto de error para gestionar riesgo de lanzamientos.
- **Cómo se ve el éxito:** las decisiones se alinean con el cumplimiento del SLO y las expectativas del cliente.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** objetivos claros y reglas de decisión.
- **Desventajas / Riesgos:** SLOs excesivamente estrictos aumentan el costo y reducen la velocidad.
### Alternativas
- **Fiabilidad de mejor esfuerzo:** para herramientas internas de bajo impacto.
- **Cómo elegir:** basa los SLOs en la tolerancia del usuario y el impacto del negocio.

## Modos de fallo y trampas
- Establecer SLOs "aspiracionales" sin capacidad para cumplirlos.
- Usar promedios en lugar de percentiles para SLOs de latencia.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** % de cumplimiento del SLI, tasa de quema del presupuesto de error.
- **Logs:** incumplimientos del SLO con contexto.
- **Trazas:** rutas más lentas que causan violaciones.
- **Alertas:** alertas de tasa de quema (rápida/lenta).

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir SLIs y ventanas de tiempo
  - [ ] Establecer objetivos realistas con los interesados
  - [ ] Monitorear quema del presupuesto de error
  - [ ] Vincular política de lanzamiento a la tasa de quema
- **Notas de seguridad / cumplimiento**
  - N/A
- **Notas de rendimiento**
  - Usar percentiles para objetivos de latencia
- **Notas operacionales**
  - Revisar SLOs trimestralmente

## Mini ejemplo (si aplica)
```text
SLO: 99.9% availability over 30 days
SLO: 95% of requests under 300ms over 7 days
```

## Anti-patrones comunes
- **Anti-patrón:** "Establecer SLO de 100% de tiempo de actividad."
  - **Por qué es malo:** poco realista; elimina el presupuesto de error necesario para el cambio.
  - **Mejor enfoque:** establecer un objetivo realista con un presupuesto claro.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Un SLO es un objetivo de fiabilidad medido mediante SLIs, e impulsa presupuestos de error y decisiones de lanzamiento.

### Preguntas trampa (con respuestas)
1) **P:** ¿Son los SLOs lo mismo que los SLAs?
   - **R:** No. Los SLOs son objetivos internos; los SLAs son contratos externos.
2) **P:** ¿Deberían los SLOs ser más estrictos que los SLAs?
   - **R:** Generalmente sí, para proporcionar margen antes de las penalizaciones contractuales.
3) **P:** ¿Puedes tener múltiples SLOs?
   - **R:** Sí. Muchos servicios rastrean SLOs tanto de disponibilidad como de latencia.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir un SLO con precisión.
- [ ] Puedo indicar cómo los SLOs usan SLIs.
- [ ] Puedo explicar presupuestos de error.
- [ ] Puedo describir un trade-off.
- [ ] Puedo nombrar un modo de fallo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Indicador de nivel de servicio (SLI)](service-level-indicator.md)

### Temas relacionados
- [Acuerdo de nivel de servicio (SLA)](service-level-agreement.md)
- [Presupuestos de error](error-budgets.md)
- [Fundamentos de fiabilidad](reliability-basics.md)

### Comparar con
- [Acuerdo de nivel de servicio (SLA)](service-level-agreement.md) — SLO es interno; SLA es contractual.

## Notas / Bandeja de entrada (opcional)
- N/A
