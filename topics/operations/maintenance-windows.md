---
id: maintenance-windows
title: "Ventanas de mantenimiento"
type: pattern
status: learning
importance: 45
difficulty: easy
tags: [operations, maintenance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Ventanas de mantenimiento

## TL;DR (BLUF)
- Las ventanas de mantenimiento son períodos planificados para tareas de mantenimiento pesadas.
- Úsalas para operaciones que impactan el rendimiento (vacuum full, compactación).
- Trade-off: disponibilidad reducida durante la ventana.

## Definición
**Qué es:** Una ventana de tiempo programada para tareas de mantenimiento disruptivas.
**Términos clave:** mantenimiento, tiempo de inactividad, horas de baja demanda.

## Por qué importa
- Reduce el impacto en los usuarios de operaciones pesadas.
- Requiere coordinación y comunicación.

## Alcance y no-objetivos
**Dentro del alcance:** planificación de tareas de mantenimiento.
**Fuera del alcance / NO resuelto por esto:** actualizaciones automatizadas sin tiempo de inactividad.

## Modelo mental / Intuición
- Piénsalo como una pausa de servicio planificada.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Las tareas de mantenimiento son pesadas y disruptivas.
### Evítalo cuando
- Puedes hacer mantenimiento en línea sin impacto.

## Cómo lo usaría (práctico)
- **Contexto:** VACUUM FULL en tablas grandes.
- **Pasos:** programar ventana → notificar a interesados → ejecutar tareas.
- **Cómo se ve el éxito:** mantenimiento completado con impacto limitado.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** riesgo controlado.
- **Desventajas / Riesgos:** tiempo de inactividad o rendimiento degradado.
### Alternativas
- **Mantenimiento en línea:** menor disrupción.
- **Cómo elegir:** usa ventanas cuando las opciones en línea no son seguras.

## Modos de fallo y trampas
- El mantenimiento se extiende causando tiempo de inactividad prolongado.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** duración del mantenimiento, tasa de errores.
- **Logs:** fallos de tareas.
- **Alertas:** extensiones del mantenimiento.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Anunciar ventana
  - [ ] Ejecutar tareas pesadas
  - [ ] Validar salud del servicio

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ejecutar mantenimiento pesado durante el tráfico pico.
  - **Por qué es malo:** impacto en los usuarios.
  - **Mejor enfoque:** programar ventanas en horas de baja demanda.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Las ventanas de mantenimiento son tiempos planificados para ejecutar operaciones pesadas con mínimo impacto en los usuarios. Son un trade-off entre disponibilidad y seguridad.

### Preguntas trampa (con respuestas)
1) **P:** ¿Son siempre necesarias las ventanas de mantenimiento?
   - **R:** no; algunas operaciones pueden ser en línea.
2) **P:** ¿Deberías omitir la comunicación para ventanas cortas?
   - **R:** no; los interesados deben ser informados.
3) **P:** ¿Es lo mismo el mantenimiento que los respaldos?
   - **R:** no; los respaldos son tareas operacionales separadas.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir ventanas de mantenimiento.
- [ ] Puedo indicar cuándo usarlas.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Planificación operacional](operational-planning.md)

### Temas relacionados
- [PostgreSQL vacuum y autovacuum](../databases/postgresql-vacuum-autovacuum.md)

### Comparar con
- [Compactación de almacenamiento](../databases/storage-compaction.md) — mantenimiento pesado vs limpieza rutinaria.
