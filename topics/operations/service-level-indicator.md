---
id: service-level-indicator
title: "Indicador de nivel de servicio (SLI)"
type: concept
status: learning
importance: 70
difficulty: easy
tags: [reliability, observability]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Indicador de nivel de servicio (SLI)

## TL;DR (BLUF)
- Un SLI es una señal medible de la salud del servicio (por ejemplo, disponibilidad, latencia, tasa de errores).
- Usa SLIs para cuantificar la experiencia del usuario e impulsar SLOs.
- Trade-off: elegir el SLI incorrecto da falsa confianza.

## Definición
**Qué es:** Una métrica cuantitativa que refleja cómo los usuarios experimentan un servicio.  
**Términos clave:** disponibilidad, latencia, tasa de errores, eventos buenos/eventos totales.

## Por qué importa
- Los SLIs convierten la fiabilidad en señales medibles.
- SLIs malos llevan a prioridades desalineadas y alertas ruidosas.

## Alcance y no-objetivos
**Dentro del alcance:** selección y medición de señales de fiabilidad enfocadas en el usuario.  
**Fuera del alcance / NO resuelto por esto:** establecer objetivos (ver [Objetivo de nivel de servicio (SLO)](service-level-objective.md)) o promesas contractuales (ver [Acuerdo de nivel de servicio (SLA)](service-level-agreement.md)).

## Modelo mental / Intuición
- "¿Qué número me dice si los usuarios están contentos?"

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas una señal única y centrada en el usuario para decisiones de fiabilidad.
- Estás definiendo o monitoreando [Objetivos de nivel de servicio (SLOs)](service-level-objective.md).

### Evítalo cuando
- Solo rastreas métricas internas que no se mapean a la experiencia del usuario.

## Cómo lo usaría (práctico)
- **Contexto:** Una API pública con quejas de latencia.
- **Pasos:**
  1) Definir un SLI de latencia: % de solicitudes bajo 300ms.
  2) Definir un SLI de disponibilidad: % de respuestas exitosas.
  3) Rastrear SLIs por endpoint y nivel.
- **Cómo se ve el éxito:** los SLIs se alinean con el rendimiento percibido por el usuario y guían los SLOs.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** medición clara y centrada en el usuario.
- **Desventajas / Riesgos:** simplificación excesiva si los SLIs ignoran segmentos críticos.
### Alternativas
- **SLIs compuestos:** combinar múltiples señales (latencia + tasa de errores).
- **Cómo elegir:** empieza con un conjunto pequeño, luego refina por segmentos de clientes.

## Modos de fallo y trampas
- Usar métricas del sistema (CPU) como SLIs en lugar de señales del usuario.
- Agregar entre endpoints y ocultar mal rendimiento en rutas críticas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** eventos buenos/eventos totales, latencia p95, tasa de errores.
- **Logs:** solicitudes fallidas con endpoint y estado.
- **Trazas:** tramos lentos en rutas críticas.
- **Alertas:** caída del SLI bajo umbral (alerta de quema).

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Elegir señales centradas en el usuario (latencia, errores, disponibilidad)
  - [ ] Definir eventos "buenos" con precisión
  - [ ] Segmentar por nivel/endpoint
  - [ ] Validar que el SLI se alinea con las quejas
- **Notas de seguridad / cumplimiento**
  - Asegurar que los SLIs excluyan payloads sensibles
- **Notas de rendimiento**
  - Usar etiquetas de baja cardinalidad
- **Notas operacionales**
  - Revisar los SLIs después de cambios de producto

## Mini ejemplo (si aplica)
```text
SLI_latency = % of requests under 300ms over 28 days
SLI_availability = % of 2xx/3xx responses over total
```

## Anti-patrones comunes
- **Anti-patrón:** "CPU < 70% es nuestro SLI."
  - **Por qué es malo:** CPU no es experiencia de usuario.
  - **Mejor enfoque:** usar latencia/tasa de errores como SLIs.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Un SLI es un número que refleja la experiencia del usuario, como el porcentaje de solicitudes bajo 300ms o el porcentaje de respuestas exitosas.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es el uso de CPU un SLI?
   - **R:** No. Es una señal interna, no un resultado orientado al usuario.
2) **P:** ¿Puedes tener múltiples SLIs?
   - **R:** Sí. Muchos servicios rastrean SLIs de latencia y disponibilidad.
3) **P:** ¿Son los SLIs objetivos?
   - **R:** No. Los objetivos son SLOs construidos sobre SLIs.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir un SLI con precisión.
- [ ] Puedo nombrar al menos 2 SLIs comunes.
- [ ] Puedo explicar "eventos buenos/eventos totales."
- [ ] Puedo vincular SLIs a SLOs.
- [ ] Puedo nombrar un modo de fallo en la selección de SLI.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de observabilidad](observability-basics.md)

### Temas relacionados
- [Objetivo de nivel de servicio (SLO)](service-level-objective.md)
- [Acuerdo de nivel de servicio (SLA)](service-level-agreement.md)
- [Presupuestos de error](error-budgets.md)

### Comparar con
- [Objetivo de nivel de servicio (SLO)](service-level-objective.md) — SLI es la métrica; SLO es el objetivo.

## Notas / Bandeja de entrada (opcional)
- N/A
