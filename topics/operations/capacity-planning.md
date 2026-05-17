---
id: capacity-planning
title: "Planificación de Capacidad"
type: skill
status: learning
importance: 40
difficulty: medium
tags: [operations, scalability]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Planificación de Capacidad

## TL;DR (BLUF)
- La planificación de capacidad asegura que los sistemas puedan manejar la carga futura.
- Úsala para prevenir interrupciones y sobreaprovisionamiento.
- Trade-off: previsiones inexactas causan desperdicio o riesgo.

## Definición
**Qué es:** Estimar las necesidades de recursos basándose en proyecciones de carga.
**Términos clave:** margen de seguridad, previsión, utilización.

## Por qué importa
- Evita la degradación repentina del rendimiento.
- Equilibra coste y fiabilidad.

## Alcance y no-objetivos
**Dentro del alcance:** planificar la capacidad de recursos.
**Fuera del alcance / NO resuelto por esto:** lógica de autoescalado en tiempo real.

## Modelo mental / Intuición
- Planificar el crecimiento antes de alcanzar los límites.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tienes crecimiento predecible o picos estacionales.
### Evítalo cuando
- Tu sistema está completamente autoescalado y estable (raro).

## Cómo lo usaría (práctico)
- **Contexto:** Previsiones de tráfico creciente.
- **Pasos:** estimar demanda -> verificar utilización -> añadir margen de seguridad.
- **Cómo se ve el éxito:** sin incidentes relacionados con la capacidad.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** estabilidad y preparación.
- **Desventajas / Riesgos:** sobreaprovisionamiento.
### Alternativas
- **Autoescalado:** reduce la planificación manual.
- **Cómo elegir:** combinar autoescalado con planificación para los límites.

## Modos de fallo y trampas
- Depender de tendencias de uso obsoletas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** utilización de CPU, memoria, I/O.
- **Logs:** eventos de escalado.
- **Alertas:** utilización alta sostenida.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Monitorear tendencias de utilización
  - [ ] Establecer objetivos de margen de seguridad

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Planificar solo para la carga promedio.
  - **Por qué es malo:** los picos causan interrupciones.
  - **Mejor enfoque:** planificar para p95/p99.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La planificación de capacidad predice las necesidades de recursos para que no alcances los límites. Equilibra coste con fiabilidad añadiendo margen de seguridad para el crecimiento.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es suficiente el autoescalado?
   - **R:** no siempre; los límites rígidos siguen existiendo.
2) **P:** ¿Deberías planificar para la carga promedio?
   - **R:** no; planificar para los picos.
3) **P:** ¿Puedes saltarte la planificación si el tráfico es bajo?
   - **R:** no si esperas crecimiento.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir planificación de capacidad.
- [ ] Puedo explicar margen de seguridad.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo identificar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- N/A

### Temas relacionados
- [Fundamentos de rendimiento](../system-design/performance-basics.md)

### Comparar con
- [Fundamentos de fiabilidad](reliability-basics.md) — capacidad vs estabilidad.
