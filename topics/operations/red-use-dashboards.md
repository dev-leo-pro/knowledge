---
title: Dashboards RED/USE
---

# Dashboards RED/USE

## Definición
RED y USE son metodologías de monitoreo para construir dashboards que se enfocan en métricas clave: Requests, Errors, Duration (RED) y Utilization, Saturation, Errors (USE).

## Por qué importa
Proporcionan una forma estandarizada de monitorear la salud y el rendimiento del sistema, ayudando a los equipos a identificar rápidamente cuellos de botella y problemas.

## Cuándo usar / cuándo no usar
Úsalo para equipos de SRE, DevOps y operaciones para monitorear servicios e infraestructura. No es necesario para sistemas muy simples.

## Trade-offs
- + Métricas estandarizadas y accionables
- + Ayuda a priorizar alertas y solución de problemas
- - Puede no capturar todas las señales específicas del negocio
- - Puede ser abrumador si no se adapta al contexto

## Prerequisitos
- [Fundamentos de observabilidad](../operations/observability-basics.md)

## Temas relacionados
- [Datadog APM](datadog-apm.md)
- [SLI/SLO](../operations/service-level-indicator.md)

## Preguntas trampa
1. ¿Qué significa RED?
   - Requests, Errors, Duration.
2. ¿Qué significa USE?
   - Utilization, Saturation, Errors.
3. ¿Cuál es un error común con los dashboards RED/USE?
   - Enfocarse solo en métricas técnicas y perder el impacto de negocio.
