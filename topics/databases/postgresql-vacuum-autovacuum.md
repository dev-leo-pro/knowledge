---
id: postgresql-vacuum-autovacuum
title: "PostgreSQL Vacuum y Autovacuum"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, postgresql, maintenance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# PostgreSQL Vacuum y Autovacuum

## TL;DR (BLUF)
- Vacuum reclama espacio de tuplas muertas; autovacuum lo hace automáticamente.
- Úsalo para controlar el bloat y mantener el rendimiento.
- Trade-off: un vacuum agresivo puede agregar sobrecarga de escritura.

## Definición
**Qué es:** Un proceso de mantenimiento que limpia tuplas muertas y actualiza mapas de visibilidad.
**Términos clave:** tuplas muertas, autovacuum, freeze.

## Por qué importa
- Sin vacuum, las tablas e índices sufren bloat y las consultas se ralentizan.
- La configuración de autovacuum impacta directamente la estabilidad del rendimiento.

## Alcance y no-objetivos
**Dentro del alcance:** propósito de vacuum, fundamentos de ajuste de autovacuum.
**Fuera del alcance / NO resuelto por esto:** mantenimiento manual de disco más allá de vacuum.

## Modelo mental / Intuición
- Vacuum es el conserje que limpia las versiones antiguas de filas.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Ves tuplas muertas crecientes o bloat de tabla.
### Evítalo cuando
- Necesitas cero sobrecarga de mantenimiento (no es realista).

## Cómo lo usaría (práctico)
- **Contexto:** Tamaño de tabla creciente y consultas lentas.
- **Pasos:** verificar tuplas muertas → ajustar umbrales de autovacuum → ejecutar vacuum manual si es necesario.
- **Cómo se ve el éxito:** tamaño de tabla estable y latencia de consultas consistente.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** reclama espacio y mantiene índices saludables.
- **Desventajas / Riesgos:** E/S extra y sobrecarga de escritura.
### Alternativas
- **VACUUM FULL manual:** pesado, bloquea escrituras.
- **Cómo elegir:** preferir autovacuum; usar VACUUM FULL solo cuando sea necesario.

## Modos de fallo y trampas
- Autovacuum demasiado laxo → bloat.
- Autovacuum demasiado agresivo → amplificación de escritura.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tuplas muertas, retraso de autovacuum, tamaño de tabla.
- **Logs:** actividad y duración de autovacuum.
- **Alertas:** tuplas muertas o bloat en aumento.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Monitorear tuplas muertas
  - [ ] Ajustar umbrales de autovacuum
  - [ ] Evitar transacciones de larga duración

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Deshabilitar autovacuum globalmente.
  - **Por qué es malo:** bloat y degradación del rendimiento.
  - **Mejor enfoque:** ajustarlo por carga de trabajo.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Vacuum en Postgres elimina versiones muertas de filas creadas por MVCC. Autovacuum automatiza esto; ajustarlo mantiene el rendimiento estable y previene el bloat.

### Preguntas trampa (con respuestas)
1) **P:** ¿VACUUM reduce inmediatamente el archivo?
   - **R:** no; marca el espacio como reutilizable pero no siempre reduce los archivos.
2) **P:** ¿Se puede deshabilitar autovacuum de forma segura?
   - **R:** generalmente no; el bloat se acumulará.
3) **P:** ¿VACUUM FULL siempre es mejor?
   - **R:** no; es bloqueante y pesado.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir vacuum y autovacuum.
- [ ] Puedo explicar por qué ocurre el bloat.
- [ ] Puedo nombrar una palanca de ajuste.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar VACUUM vs VACUUM FULL.

## Enlaces (SIN duplicación)
### Prerequisitos
- [PostgreSQL MVCC](postgresql-mvcc.md)

### Temas relacionados
- [Bloat en PostgreSQL](postgresql-bloat.md)
- [Diagnóstico de consultas lentas](slow-query-diagnosis.md)

### Comparar con
- [Ventanas de mantenimiento](../operations/maintenance-windows.md)
