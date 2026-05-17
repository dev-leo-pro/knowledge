---
id: slow-query-diagnosis
title: "Diagnóstico de consultas lentas"
type: skill
status: learning
importance: 55
difficulty: medium
tags: [database, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Diagnóstico de consultas lentas

## TL;DR (BLUF)
- Diagnostica consultas lentas combinando EXPLAIN, estadísticas y contexto de carga de trabajo.
- Usa un checklist repetible para evitar adivinanzas.
- Trade-off: el análisis profundo toma tiempo; las correcciones rápidas pueden ser incorrectas.

## Definición
**Qué es:** Un proceso sistemático para identificar y corregir consultas de base de datos lentas.
**Términos clave:** EXPLAIN, selectividad, índices, estadísticas.

## Por qué importa
- Las consultas lentas dominan la latencia y el costo.
- Correcciones incorrectas pueden empeorar escrituras u otras consultas.

## Alcance y no-objetivos
**Dentro del alcance:** flujo de trabajo de diagnóstico para causas comunes de consultas lentas.
**Fuera del alcance / NO resuelto por esto:** planificación de capacidad a nivel de base de datos.

## Modelo mental / Intuición
- Trata las consultas lentas como un síntoma; encuentra la causa raíz antes de cambiar índices.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La latencia p95 tiene picos o se incumplen SLAs.
### Evítalo cuando
- No has verificado que la consulta es el cuello de botella.

## Cómo lo usaría (práctico)
- **Contexto:** Un endpoint con alta latencia.
- **Pasos:** capturar consulta → ejecutar EXPLAIN → verificar escaneos/selectividad → validar índices → medir impacto.
- **Cómo se ve el éxito:** latencia reducida sin regresiones.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** correcciones dirigidas con impacto medible.
- **Desventajas / Riesgos:** consume tiempo y requiere experiencia.
### Alternativas
- **Caché:** a veces más rápido que cambios profundos de consultas.
- **Cómo elegir:** diagnostica primero; solo usa caché si la consulta es estable y segura.

## Modos de fallo y trampas
- Agregar índices sin validar planes.
- Ignorar cambios en la distribución de datos.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** conteo de consultas lentas, latencia p95, CPU y E/S.
- **Logs:** logs de consultas lentas.
- **Alertas:** picos sostenidos de consultas lentas.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar consultas lentas
  - [ ] Ejecutar EXPLAIN
  - [ ] Validar selectividad
  - [ ] Medir antes/después

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Agregar índices sin entender la consulta.
  - **Por qué es malo:** puede empeorar escrituras y no ayudar lecturas.
  - **Mejor enfoque:** analizar plan y selectividad primero.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El diagnóstico de consultas lentas es un proceso disciplinado: captura la consulta, inspecciona el plan, verifica selectividad, ajusta índices y mide resultados. Evita las adivinanzas.

### Preguntas trampa (con respuestas)
1) **P:** ¿Un índice siempre arregla una consulta lenta?
   - **R:** no; depende de la selectividad y la forma de la consulta.
2) **P:** ¿EXPLAIN es suficiente por sí solo?
   - **R:** no; también necesitas distribución de datos y contexto de carga de trabajo.
3) **P:** ¿Siempre deberías usar caché en su lugar?
   - **R:** no; el caché puede ocultar problemas subyacentes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo describir un flujo de trabajo de diagnóstico.
- [ ] Puedo explicar por qué la selectividad importa.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo describir una métrica de éxito.
- [ ] Puedo listar las herramientas utilizadas.

## Enlaces (SIN duplicación)
### Prerequisitos
- [EXPLAIN](explain.md)
- [Selectividad](selectivity.md)

### Temas relacionados
- [Índice](index.md)

### Comparar con
- [Estrategia de caché](../system-design/caching-strategy.md)
