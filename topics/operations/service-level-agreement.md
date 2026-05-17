---
id: service-level-agreement
title: "Acuerdo de nivel de servicio (SLA)"
type: concept
status: learning
importance: 70
difficulty: easy
tags: [reliability, contracts]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Acuerdo de nivel de servicio (SLA)

## TL;DR (BLUF)
- Un SLA es una promesa contractual a los clientes sobre los niveles de servicio.
- Usa SLAs para establecer expectativas externas y penalizaciones.
- Trade-off: SLAs más estrictos aumentan el costo operacional y el riesgo.

## Definición
**Qué es:** Un contrato entre proveedor y cliente que define niveles mínimos de servicio, reportes y remedios/penalizaciones si no se cumplen.  
**Términos clave:** contrato, penalización, créditos de servicio, ventana de reporte.

## Por qué importa
- Los SLAs generan obligaciones legales y financieras.
- SLAs desalineados pueden crear riesgo de negocio y pérdida de clientes.

## Alcance y no-objetivos
**Dentro del alcance:** compromisos externos y remedios.  
**Fuera del alcance / NO resuelto por esto:** objetivos internos (ver [Objetivo de nivel de servicio (SLO)](service-level-objective.md)).

## Modelo mental / Intuición
- "Lo que garantizamos a los clientes, con consecuencias."

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Vendes un servicio con compromisos explícitos de fiabilidad.
- Necesitas reglas claras de escalación y compensación.

### Evítalo cuando
- No puedes medir o reportar de forma confiable.

## Cómo lo usaría (práctico)
- **Contexto:** Un producto API de pago con clientes empresariales.
- **Pasos:**
  1) Establecer SLOs internos más altos que el SLA.
  2) Definir ventanas de reporte y exclusiones.
  3) Definir créditos de servicio por incumplimientos.
- **Cómo se ve el éxito:** los clientes entienden las expectativas; los equipos internos mantienen suficiente margen.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** expectativas claras del cliente; rendición de cuentas.
- **Desventajas / Riesgos:** exposición legal, mayor costo de fiabilidad.
### Alternativas
- **Servicio de mejor esfuerzo:** sin garantías contractuales.
- **Cómo elegir:** si los clientes necesitan garantías, formaliza un SLA.

## Modos de fallo y trampas
- SLA más estricto que el SLO interno → incumplimientos constantes.
- Exclusiones vagas o ventanas de reporte poco claras.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** % de cumplimiento del SLA, tiempo de actividad en la ventana del contrato.
- **Logs:** registro de auditoría para interrupciones y exclusiones.
- **Trazas:** N/A
- **Alertas:** riesgo de incumplimiento del SLA (alertas de quema).

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir alcance del SLA y exclusiones
  - [ ] Alinear SLOs internos por encima del SLA
  - [ ] Definir ventanas de reporte
  - [ ] Especificar penalizaciones/remedios
- **Notas de seguridad / cumplimiento**
  - Asegurar que las métricas contractuales sean auditables
- **Notas de rendimiento**
  - N/A
- **Notas operacionales**
  - Revisar términos del SLA anualmente

## Mini ejemplo (si aplica)
```text
SLA: 99.5% monthly availability, service credits if below
```

## Anti-patrones comunes
- **Anti-patrón:** "Prometer 99.99% sin capacidad."
  - **Por qué es malo:** garantiza incumplimientos y riesgo financiero.
  - **Mejor enfoque:** establecer SLOs internos primero, luego establecer el SLA.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Un SLA es una promesa contractual externa con remedios si no se cumple, y debería ser más holgado que los SLOs internos.

### Preguntas trampa (con respuestas)
1) **P:** ¿Son los SLAs lo mismo que los SLOs?
   - **R:** No. El SLA es contractual; el SLO es interno.
2) **P:** ¿Deberían ser idénticos el SLA y el SLO?
   - **R:** No. Los SLOs deberían ser más estrictos para proporcionar margen.
3) **P:** ¿Los SLAs incluyen todos los incidentes?
   - **R:** No necesariamente; las exclusiones deben ser explícitas.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir un SLA con precisión.
- [ ] Puedo explicar cómo difiere del SLO.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo dar un ejemplo concreto.
- [ ] Puedo nombrar una trampa.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Objetivo de nivel de servicio (SLO)](service-level-objective.md)

### Temas relacionados
- [Indicador de nivel de servicio (SLI)](service-level-indicator.md)
- [Presupuestos de error](error-budgets.md)

### Comparar con
- [Objetivo de nivel de servicio (SLO)](service-level-objective.md) — objetivo interno vs promesa contractual.

## Notas / Bandeja de entrada (opcional)
- N/A
