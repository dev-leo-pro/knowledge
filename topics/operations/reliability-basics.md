---
id: reliability-basics
title: "Fundamentos de fiabilidad"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [operations, reliability]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de fiabilidad

## TL;DR (BLUF)
- La fiabilidad trata sobre un comportamiento consistente y predecible del sistema.
- Usa [Indicadores de nivel de servicio (SLIs)](service-level-indicator.md) y [Objetivos de nivel de servicio (SLOs)](service-level-objective.md) para medir y gestionar la fiabilidad.
- Trade-off: mayor fiabilidad generalmente cuesta más.

## Definición
**Qué es:** La probabilidad de que un sistema funcione correctamente a lo largo del tiempo.
**Términos clave:** [Indicador de nivel de servicio (SLI)](service-level-indicator.md), [Objetivo de nivel de servicio (SLO)](service-level-objective.md), [Presupuestos de error](error-budgets.md).

## Por qué importa
- La fiabilidad afecta la confianza del usuario y los resultados del negocio.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos de corrección-en-el-tiempo y marco de SLO.
**Fuera del alcance / NO resuelto por esto:** objetivos de solo disponibilidad.

## Modelo mental / Intuición
- La fiabilidad es consistencia a lo largo del tiempo, no perfección.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Estableces objetivos de tiempo de actividad o latencia.
### Evítalo cuando
- El sistema no es crítico y es experimental.

## Cómo lo usaría (práctico)
- **Contexto:** Objetivos de tiempo de actividad de API.
- **Pasos:** definir SLO → monitorear → actuar sobre [Presupuestos de error](error-budgets.md).
- **Cómo se ve el éxito:** tiempo de actividad estable y trade-offs claros.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** comportamiento predecible.
- **Desventajas / Riesgos:** mayor costo y cadencia de cambio más lenta.
### Alternativas
- **Fiabilidad de mejor esfuerzo:** para servicios no críticos.
- **Cómo elegir:** ajusta los objetivos de fiabilidad al impacto del negocio.

## Modos de fallo y trampas
- Establecer SLOs poco realistas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de errores, latencia p95.
- **Logs:** logs de errores.
- **Alertas:** quema de SLO.

## Notas de implementación (si aplica)
- **Lista de verificación**
   - [ ] Definir [Indicadores de nivel de servicio (SLIs)](service-level-indicator.md)
   - [ ] Establecer [Objetivos de nivel de servicio (SLOs)](service-level-objective.md)

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Perseguir 100% de tiempo de actividad.
  - **Por qué es malo:** impráctico y costoso.
  - **Mejor enfoque:** establecer SLOs realistas.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La fiabilidad es la capacidad de un sistema de comportarse correctamente a lo largo del tiempo. Usa SLIs/SLOs y [Presupuestos de error](error-budgets.md) para gestionarla.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es alcanzable el 100% de tiempo de actividad?
   - **R:** no; es impráctico.
2) **P:** ¿Son los SLOs solo para equipos de operaciones?
   - **R:** no; guían los trade-offs de ingeniería.
3) **P:** ¿Significa la fiabilidad cero errores?
   - **R:** no; se trata de tasas de error aceptables.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir fiabilidad.
- [ ] Puedo explicar SLIs/SLOs.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar [Presupuestos de error](error-budgets.md).

## Enlaces (SIN duplicación)
### Prerequisitos
- N/A

### Temas relacionados
- [Fundamentos de redes](networking-basics.md)
- [Fundamentos de disponibilidad](availability-basics.md)
- [Indicador de nivel de servicio (SLI)](service-level-indicator.md)
- [Objetivo de nivel de servicio (SLO)](service-level-objective.md)
- [Acuerdo de nivel de servicio (SLA)](service-level-agreement.md)
- [Presupuestos de error](error-budgets.md)

### Comparar con
- [Fundamentos de disponibilidad](availability-basics.md)
