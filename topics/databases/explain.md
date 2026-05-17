---
id: explain
title: "EXPLAIN (PostgreSQL)"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, postgresql, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# EXPLAIN (PostgreSQL)

## TL;DR (BLUF)
- EXPLAIN muestra el plan de consulta y las estimaciones de costo elegidas por el planificador.
- Úsalo para validar índices y la forma de la consulta antes de optimizar.
- Trade-off: los planes son estimaciones; pueden ser incorrectos si las estadísticas están desactualizadas.

## Definición
**Qué es:** Un comando que muestra cómo PostgreSQL planea ejecutar una consulta.
**Términos clave:** plan de consulta, estimación de costo, planificador, ANALYZE.

## Por qué importa
- Te ayuda a entender por qué una consulta es lenta.
- Previene las adivinanzas al agregar o eliminar índices.

## Alcance y no-objetivos
**Dentro del alcance:** diagnosticar planes de consulta y validar índices.
**Fuera del alcance / NO resuelto por esto:** corregir errores lógicos en SQL.

## Modelo mental / Intuición
- Piensa en EXPLAIN como un "mapa" de cómo la BD recorrerá los datos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Estés agregando índices u optimizando consultas lentas.
- Quieras verificar las decisiones del planificador para consultas críticas.
### Evítalo cuando
- Necesites tiempo de ejecución sin ejecutar la consulta (usa EXPLAIN ANALYZE con cuidado).

## Cómo lo usaría (práctico)
- **Contexto:** Una consulta lenta de un endpoint.
- **Pasos:** ejecutar EXPLAIN → verificar seq scan vs index scan → ajustar índices → re-ejecutar.
- **Cómo se ve el éxito:** el planificador usa los índices esperados y el costo baja.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** visibilidad de las decisiones del planificador.
- **Contras / Riesgos:** puede confundir si las estadísticas están desactualizadas o la distribución de datos cambió.
### Alternativas
- **EXPLAIN ANALYZE:** tiempo de ejecución real, pero ejecuta la consulta.
- **Cómo elegir:** empieza con EXPLAIN, valida con EXPLAIN ANALYZE cuando sea seguro.

## Modos de fallo y errores comunes
- Confundir costos con tiempo real.
- Ignorar estimaciones de filas y orden de joins.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** frecuencia de consultas lentas, frescura de estadísticas del planificador.
- **Logs:** logs de consultas lentas con captura de plan si está disponible.
- **Alertas:** alta latencia después de cambios de esquema o datos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Asegurar que las estadísticas estén frescas (ANALYZE)
  - [ ] Comparar estimaciones de filas vs reales con EXPLAIN ANALYZE

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Agregar índices sin verificar EXPLAIN.
  - **Por qué es malo:** puedes agregar índices que el planificador no usará.
  - **Mejor enfoque:** usar EXPLAIN para guiar el diseño de índices.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- EXPLAIN muestra cómo PostgreSQL planea ejecutar una consulta y si usará índices. Es la primera herramienta que debes usar antes de hacer cambios de rendimiento.

### Preguntas trampa (con respuestas)
1) **P:** ¿EXPLAIN muestra el tiempo de ejecución real?
   - **R:** No; eso es EXPLAIN ANALYZE.
2) **P:** ¿EXPLAIN puede estar equivocado?
   - **R:** Sí; si las estadísticas están desactualizadas, el plan puede ser engañoso.
3) **P:** ¿Siempre debes confiar en el planificador?
   - **R:** Generalmente sí, pero valida cuando los resultados parezcan incorrectos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir EXPLAIN con precisión.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de optimización.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Índice](index.md)

### Temas relacionados
- [Selectividad](selectivity.md)

### Comparar con
- [Selectividad](selectivity.md) — estimaciones del planificador vs distribución de datos.
