---
id: postgresql-bloat
title: "Bloat en PostgreSQL"
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

# Bloat en PostgreSQL

## TL;DR (BLUF)
- El bloat es espacio desperdiciado por tuplas muertas y crecimiento de índices.
- Usa vacuum y buenos patrones de actualización para controlarlo.
- Trade-off: una limpieza agresiva aumenta la sobrecarga de escritura.

## Definición
**Qué es:** Espacio extra en tablas/índices que no se usa activamente pero no se ha reclamado.
**Términos clave:** tuplas muertas, vacuum, fillfactor.

## Por qué importa
- El bloat aumenta la E/S y ralentiza las consultas.
- Puede hacer crecer silenciosamente los costos de almacenamiento.

## Alcance y no-objetivos
**Dentro del alcance:** causas del bloat y mitigaciones.
**Fuera del alcance / NO resuelto por esto:** compresión en la capa de almacenamiento.

## Modelo mental / Intuición
- Piensa en el bloat como "asientos vacíos" dejados por las actualizaciones de filas.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Ves que el tamaño de la tabla crece más rápido que los datos.
### Evítalo cuando
- No has validado las tuplas muertas o las estadísticas de la tabla.

## Cómo lo usaría (práctico)
- **Contexto:** la tabla crece a pesar de un conteo de filas estable.
- **Pasos:** medir tuplas muertas → ajustar autovacuum → considerar VACUUM FULL.
- **Cómo se ve el éxito:** tamaño estable de tabla/índice.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** menor E/S y mejor utilización de caché.
- **Desventajas / Riesgos:** sobrecarga de limpieza.
### Alternativas
- **VACUUM FULL:** pesado pero compacta.
- **Cómo elegir:** usar vacuum regular; reservar vacuum full para bloat extremo.

## Modos de fallo y trampas
- Transacciones largas previniendo la limpieza.
- Mala interpretación del crecimiento del tamaño de tabla.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tuplas muertas, tamaño de tabla, ratio de aciertos de caché.
- **Logs:** logs de autovacuum.
- **Alertas:** indicadores de bloat en aumento.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Rastrear tuplas muertas
  - [ ] Ajustar autovacuum
  - [ ] Revisar patrones de actualización

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ignorar el bloat hasta que los discos se llenen.
  - **Por qué es malo:** el rendimiento se degrada gradualmente.
  - **Mejor enfoque:** monitorear y ajustar temprano.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El bloat es espacio desperdiciado por versiones antiguas de filas en Postgres. Vacuum lo limpia, pero si se retrasa, las tablas e índices crecen y las consultas se ralentizan.

### Preguntas trampa (con respuestas)
1) **P:** ¿Vacuum siempre reduce los archivos de tabla?
   - **R:** no; principalmente libera espacio para reutilización.
2) **P:** ¿El bloat es solo un problema de tablas?
   - **R:** no; los índices también pueden sufrir bloat.
3) **P:** ¿Las transacciones largas pueden causar bloat?
   - **R:** sí; previenen la limpieza de versiones antiguas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir bloat.
- [ ] Puedo explicar cómo ocurre.
- [ ] Puedo nombrar una mitigación.
- [ ] Puedo describir una señal.
- [ ] Puedo relacionarlo con MVCC.

## Enlaces (SIN duplicación)
### Prerequisitos
- [PostgreSQL MVCC](postgresql-mvcc.md)

### Temas relacionados
- [PostgreSQL vacuum y autovacuum](postgresql-vacuum-autovacuum.md)

### Comparar con
- [Compactación de almacenamiento](storage-compaction.md)
