---
id: query-planning
title: "Planificación de consultas"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Planificación de consultas

## TL;DR (BLUF)
- El planificador de consultas elige cómo ejecutar una consulta basándose en estimaciones de costo.
- Usa herramientas del planificador para entender el uso de índices y el orden de joins.
- Trade-off: los planes son estimaciones y pueden ser incorrectos si las estadísticas están obsoletas.

## Definición
**Qué es:** El componente de la base de datos que decide las estrategias de ejecución (tipos de escaneo, orden de joins, índices).
**Términos clave:** estimación de costo, estadísticas, orden de joins, tipo de escaneo.

## Por qué importa
- Las elecciones del planificador a menudo determinan el rendimiento de consultas.
- Estimaciones incorrectas llevan a consultas lentas e índices mal utilizados.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos de planificación y cómo validarlos.
**Fuera del alcance / NO resuelto por esto:** algoritmos específicos del optimizador por BD.

## Modelo mental / Intuición
- Piensa en ello como un planificador de rutas eligiendo el camino más barato.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Diagnosticas consultas lentas o agregas índices.
### Evítalo cuando
- Solo necesitas validar el tiempo de ejecución (usa EXPLAIN ANALYZE con cuidado).

## Cómo lo usaría (práctico)
- **Contexto:** Pico de latencia de consultas después de crecimiento de datos.
- **Pasos:** ejecutar EXPLAIN → verificar tipos de escaneo → validar orden de joins → ajustar índices/estadísticas.
- **Cómo se ve el éxito:** el planificador elige escaneos eficientes y buen orden de joins.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** visibilidad de las decisiones de rendimiento.
- **Desventajas / Riesgos:** las estimaciones de costo pueden ser incorrectas.
### Alternativas
- **EXPLAIN ANALYZE:** tiempos reales, pero ejecuta la consulta.
- **Cómo elegir:** empieza con EXPLAIN, valida con ANALYZE si es seguro.

## Modos de fallo y trampas
- Estadísticas obsoletas causando malos planes.
- Malinterpretar el costo como tiempo real.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** frecuencia de consultas lentas, cambios de plan a lo largo del tiempo.
- **Logs:** logs de consultas lentas.
- **Alertas:** picos repentinos de latencia después de cambios en la distribución de datos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Mantener estadísticas actualizadas (ANALYZE)
  - [ ] Comparar estimaciones vs valores reales

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Agregar índices sin verificar planes.
  - **Por qué es malo:** el planificador puede ignorarlos.
  - **Mejor enfoque:** validar con EXPLAIN.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La planificación de consultas es la BD decidiendo la forma más barata de ejecutar una consulta. Usa estadísticas para elegir índices y orden de joins, y lo validas con herramientas como EXPLAIN.

### Preguntas trampa (con respuestas)
1) **P:** ¿El planificador siempre es correcto?
   - **R:** no; malas estadísticas o sesgo pueden engañarlo.
2) **P:** ¿EXPLAIN muestra el tiempo de ejecución real?
   - **R:** no; EXPLAIN ANALYZE sí.
3) **P:** ¿Los índices pueden ser ignorados?
   - **R:** sí, si el planificador estima que no ayudarán.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir la planificación de consultas.
- [ ] Puedo explicar por qué las estadísticas importan.
- [ ] Puedo nombrar un modo de fallo.
- [ ] Puedo describir un paso de validación.
- [ ] Puedo comparar EXPLAIN vs EXPLAIN ANALYZE.

## Enlaces (SIN duplicación)
### Prerequisitos
- [EXPLAIN](explain.md)

### Temas relacionados
- [Selectividad](selectivity.md)

### Comparar con
- [EXPLAIN](explain.md) — salida del planificador vs ejecución.
