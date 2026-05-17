---
id: selectivity
title: "Selectividad (consultas de base de datos)"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Selectividad (consultas de base de datos)

## TL;DR (BLUF)
- La selectividad mide qué tan bien un predicado filtra filas.
- Úsala para decidir si un índice realmente será útil.
- Trade-off: índices de alta selectividad ayudan las lecturas pero agregan costo de escritura.

## Definición
**Qué es:** La fracción de filas que satisfacen un predicado; mayor selectividad significa que menos filas coinciden.
**Términos clave:** predicado, cardinalidad, selectividad, planificador de consultas.

## Por qué importa
- El planificador de consultas elige el uso de índices basándose en estimaciones de selectividad.
- Baja selectividad a menudo significa que los índices son ignorados o ineficaces.

## Alcance y no-objetivos
**Dentro del alcance:** decidir la utilidad de índices para filtros y joins.
**Fuera del alcance / NO resuelto por esto:** arreglar lógica de consultas pobre o modelado de datos.

## Modelo mental / Intuición
- Un filtro altamente selectivo es como una aguja en un pajar; los índices brillan.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Eliges índices para predicados y joins.
- Evalúas si una consulta puede beneficiarse de un índice.
### Evítalo cuando
- Necesitas optimizar consultas sin entender la distribución real de datos.

## Cómo lo usaría (práctico)
- **Contexto:** Consulta lenta con un filtro WHERE.
- **Pasos:** verificar distribución → estimar selectividad → decidir índice o reescritura de consulta.
- **Cómo se ve el éxito:** el planificador usa el índice y la latencia baja.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejores decisiones de índices, menos índices desperdiciados.
- **Desventajas / Riesgos:** las estimaciones pueden ser incorrectas si las estadísticas están obsoletas.
### Alternativas
- **EXPLAIN:** validar decisiones del planificador.
- **Cómo elegir:** usa selectividad para guiar el diseño de índices, luego valida con EXPLAIN.

## Modos de fallo y trampas
- Estadísticas obsoletas causando estimaciones incorrectas de selectividad.
- Asumir que la selectividad es constante a lo largo del tiempo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** ratio de uso de índices, latencia de consultas.
- **Logs:** logs de consultas lentas.
- **Alertas:** latencia en aumento después de cambios en la distribución de datos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Actualizar estadísticas (ANALYZE)
  - [ ] Validar con EXPLAIN

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Indexar columnas de baja selectividad sin estrategia.
  - **Por qué es malo:** el índice se ignora frecuentemente; las escrituras se ralentizan.
  - **Mejor enfoque:** indexar claves compuestas o replantear el predicado.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La selectividad te dice cuántas filas coincide un filtro. Si coincide con demasiadas filas, el planificador puede saltar el índice, así que entender la selectividad es clave para buena indexación.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los índices siempre ayudan?
   - **R:** no; predicados de baja selectividad frecuentemente no se benefician.
2) **P:** ¿La selectividad puede cambiar con el tiempo?
   - **R:** sí; cambios en la distribución de datos pueden cambiar las elecciones del planificador.
3) **P:** ¿La selectividad es solo para filtros de igualdad?
   - **R:** no; aplica a rangos y joins también.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir selectividad con precisión.
- [ ] Puedo explicar por qué importa para la indexación.
- [ ] Puedo nombrar 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Índice](index.md)

### Temas relacionados
- [EXPLAIN](explain.md)

### Comparar con
- [EXPLAIN](explain.md) — salida del planificador vs distribución de datos.
