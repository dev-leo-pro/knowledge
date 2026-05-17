---
id: observability-basics
title: "Fundamentos de observabilidad"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [operations, observability]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de observabilidad

## TL;DR (BLUF)
- La observabilidad te ayuda a entender el comportamiento del sistema mediante métricas, logs y trazas.
- Úsala para detectar y diagnosticar problemas rápidamente.
- Trade-off: la instrumentación agrega costo y complejidad.

## Definición
**Qué es:** La capacidad de inferir el estado interno del sistema a partir de sus salidas.
**Términos clave:** métricas, logs, trazas, RED/USE.

## Por qué importa
- Acorta el tiempo de respuesta a incidentes y depuración.
- La falta de observabilidad oculta fallos.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos básicos de observabilidad.
**Fuera del alcance / NO resuelto por esto:** configuración específica de herramientas.

## Modelo mental / Intuición
- La observabilidad es la telemetría de tu sistema.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Operas sistemas en producción.
### Evítalo cuando
- Estás en una etapa de prototipo desechable.

## Cómo lo usaría (práctico)
- **Contexto:** Problemas de latencia de API.
- **Pasos:** agregar métricas → agregar trazas → inspeccionar logs.
- **Cómo se ve el éxito:** identificación rápida de la causa raíz.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** depuración más rápida.
- **Desventajas / Riesgos:** sobrecarga y costo.
### Alternativas
- **Depuración manual:** más lenta y riesgosa.
- **Cómo elegir:** instrumenta las rutas críticas.

## Modos de fallo y trampas
- Métricas de alta cardinalidad causando picos de costo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia, tasa de errores, saturación.
- **Logs:** logs de errores estructurados.
- **Trazas:** tramos de solicitud de extremo a extremo.

## Notas de implementación (si aplica)
- **Lista de verificación**
   - [ ] Definir [Indicadores de nivel de servicio (SLIs)](service-level-indicator.md)
  - [ ] Instrumentar rutas críticas

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Registrar todo sin estructura.
  - **Por qué es malo:** ruidoso y costoso.
  - **Mejor enfoque:** logs estructurados con contexto.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La observabilidad es saber qué está haciendo tu sistema mediante métricas, logs y trazas. Es esencial para diagnosticar problemas en producción.

### Preguntas trampa (con respuestas)
1) **P:** ¿Son suficientes los logs?
   - **R:** no; las métricas y trazas también son necesarias.
2) **P:** ¿Es la observabilidad solo para operaciones?
   - **R:** no; los desarrolladores también la necesitan.
3) **P:** ¿Puedes omitir la observabilidad al principio?
   - **R:** para prototipos quizás, pero no para producción.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir observabilidad.
- [ ] Puedo explicar métricas/logs/trazas.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo describir una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- N/A

### Temas relacionados
- [Fundamentos de rendimiento](../system-design/performance-basics.md)

### Comparar con
- [Fundamentos de fiabilidad](reliability-basics.md) — visibilidad vs estabilidad.
