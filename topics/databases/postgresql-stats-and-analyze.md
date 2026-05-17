---
id: postgresql-stats-and-analyze
title: "Estadísticas y ANALYZE de PostgreSQL"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, postgresql, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Estadísticas y ANALYZE de PostgreSQL

## TL;DR (BLUF)
- El planificador depende de estadísticas de tabla para estimar costos y elegir planes.
- Usa ANALYZE para mantener las estadísticas actualizadas después de grandes cambios de datos.
- Trade-off: recolectar estadísticas agrega sobrecarga.

## Definición
**Qué es:** Estadísticas sobre la distribución de datos usadas por el planificador, actualizadas por ANALYZE.
**Términos clave:** estadísticas, ANALYZE, histogramas.

## Por qué importa
- Estadísticas obsoletas llevan a malos planes y consultas lentas.
- Buenas estadísticas mejoran la selección de índices y el orden de joins.

## Alcance y no-objetivos
**Dentro del alcance:** frescura de estadísticas y su impacto en la planificación.
**Fuera del alcance / NO resuelto por esto:** mejores prácticas de reescritura de consultas.

## Modelo mental / Intuición
- Piensa en las estadísticas como el mapa del planificador de tus datos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Cargas o eliminas grandes cantidades de datos.
### Evítalo cuando
- Puedes confiar en autovacuum/analyze para cambios pequeños.

## Cómo lo usaría (práctico)
- **Contexto:** Carga masiva seguida de consultas lentas.
- **Pasos:** ejecutar ANALYZE → re-verificar EXPLAIN → monitorear cambios de plan.
- **Cómo se ve el éxito:** el planificador elige escaneos eficientes.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejores planes y rendimiento.
- **Desventajas / Riesgos:** sobrecarga durante la recolección de estadísticas.
### Alternativas
- **Autovacuum analyze:** actualizaciones automáticas de estadísticas.
- **Cómo elegir:** ANALYZE manual después de grandes cambios de datos.

## Modos de fallo y trampas
- Asumir que las estadísticas se actualizan inmediatamente después de cargas masivas.
- Malinterpretar estimaciones del planificador sin verificar estadísticas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** cambios de plan, latencia de consultas después de cargas masivas.
- **Logs:** logs de autovacuum/analyze.
- **Alertas:** picos de latencia después de grandes cambios de datos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Ejecutar ANALYZE después de cambios masivos
  - [ ] Monitorear estabilidad de planes

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ignorar estadísticas después de grandes eliminaciones.
  - **Por qué es malo:** el planificador elige planes pobres.
  - **Mejor enfoque:** analizar después de cambios importantes de datos.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- PostgreSQL usa estadísticas para decidir cómo ejecutar consultas. Después de grandes cambios de datos, ejecuta ANALYZE para que el planificador tenga información precisa; de lo contrario, puede elegir malos planes.

### Preguntas trampa (con respuestas)
1) **P:** ¿ANALYZE reescribe datos en disco?
   - **R:** no; solo actualiza estadísticas.
2) **P:** ¿Las estadísticas siempre son perfectamente precisas?
   - **R:** no; son estimaciones.
3) **P:** ¿Es suficiente autovacuum después de una carga masiva?
   - **R:** no siempre; ANALYZE manual puede ayudar.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar por qué las estadísticas importan.
- [ ] Puedo decir cuándo ejecutar ANALYZE.
- [ ] Puedo describir un trade-off.
- [ ] Puedo identificar un modo de fallo.
- [ ] Puedo relacionar las estadísticas con la planificación de consultas.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Planificación de consultas](query-planning.md)
- [EXPLAIN](explain.md)

### Temas relacionados
- [Selectividad](selectivity.md)

### Comparar con
- [Estadísticas de base de datos](database-statistics.md)
