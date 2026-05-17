---
id: database-statistics
title: "Estadísticas de Base de Datos"
type: concept
status: learning
importance: 45
difficulty: medium
tags: [database, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Estadísticas de Base de Datos

## TL;DR (BLUF)
- Las estadísticas describen la distribución de datos y guían a los planificadores de consultas.
- Úsalas para mejorar la calidad de los planes y el rendimiento.
- Trade-off: la recolección de estadísticas añade sobrecarga.

## Definición
**Qué es:** Metadatos sobre la distribución de datos usados para planificación basada en costos.
**Términos clave:** histogramas, cardinalidad, selectividad.

## Por qué importa
- Estadísticas malas llevan a planes malos y consultas lentas.
- Las estadísticas ayudan a elegir índices y orden de joins.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos generales de estadísticas en bases de datos.
**Fuera del alcance / NO resuelto por esto:** ajuste específico del motor.

## Modelo mental / Intuición
- Las estadísticas son el "mapa" del planificador sobre los datos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Ves regresiones de planes o consultas lentas después de grandes cambios de datos.
### Evítalo cuando
- Las estadísticas ya están frescas y el problema está en otro lugar.

## Cómo lo usaría (práctico)
- **Contexto:** Consulta se ralentizó después de una carga grande.
- **Pasos:** actualizar estadísticas → re-verificar plan → monitorear latencia.
- **Cómo se ve el éxito:** planes mejorados y menor latencia.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejor planificación y rendimiento.
- **Desventajas / Riesgos:** sobrecarga y estimaciones imperfectas.
### Alternativas
- **Hints de plan:** cuando están disponibles, pero arriesgados.
- **Cómo elegir:** confiar en estadísticas primero, hints como último recurso.

## Modos de fallo y trampas
- Asumir que las estadísticas son exactas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** cambios de plan, latencia de consultas.
- **Logs:** logs de actualización de estadísticas.
- **Alertas:** picos de latencia después de cambios de datos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Actualizar estadísticas después de cambios grandes
  - [ ] Validar mejoras en el plan

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ignorar las estadísticas después de eliminaciones masivas.
  - **Por qué es malo:** el planificador usa información obsoleta.
  - **Mejor enfoque:** refrescar las estadísticas.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las estadísticas de base de datos son resúmenes de la distribución de datos usados por los planificadores de consultas. Mantenerlas frescas ayuda al planificador a elegir planes eficientes.

### Preguntas trampa (con respuestas)
1) **P:** ¿Las estadísticas son siempre precisas?
   - **R:** no; son estimaciones.
2) **P:** ¿Las estadísticas pueden perjudicar el rendimiento?
   - **R:** recolectarlas tiene sobrecarga.
3) **P:** ¿Las estadísticas eliminan la necesidad de índices?
   - **R:** no; solo ayudan a elegir planes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir estadísticas de base de datos.
- [ ] Puedo explicar por qué importan.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo relacionar estadísticas con planificación.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Selectivity](selectivity.md)

### Temas relacionados
- [Query planning](query-planning.md)

### Comparar con
- [PostgreSQL statistics & ANALYZE](postgresql-stats-and-analyze.md) — especificidades de Postgres.
