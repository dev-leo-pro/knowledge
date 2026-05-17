---
id: operational-planning
title: "Planificación operacional"
type: skill
status: learning
importance: 40
difficulty: easy
tags: [operations]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Planificación operacional

## TL;DR (BLUF)
- La planificación operacional define cómo y cuándo ejecutar mantenimiento de forma segura.
- Úsala para reducir el impacto en los usuarios y coordinar cambios.
- Trade-off: mayor sobrecarga de procesos.

## Definición
**Qué es:** Planificar tareas como mantenimiento, respaldos y actualizaciones.
**Términos clave:** runbook, ventana, evaluación de riesgos.

## Por qué importa
- Previene interrupciones y confusión.
- Una mala planificación causa tiempo de inactividad y pérdida de datos.

## Alcance y no-objetivos
**Dentro del alcance:** planificación y coordinación de mantenimiento.
**Fuera del alcance / NO resuelto por esto:** remediación automatizada.

## Modelo mental / Intuición
- Planifica antes de cambiar producción.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los cambios impactan sistemas en producción.
### Evítalo cuando
- El cambio es reversible y de bajo riesgo.

## Cómo lo usaría (práctico)
- **Contexto:** Mantenimiento de base de datos.
- **Pasos:** evaluar riesgo → programar ventana → comunicar → ejecutar.
- **Cómo se ve el éxito:** resultados predecibles e impacto mínimo.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** riesgo reducido.
- **Desventajas / Riesgos:** ejecución más lenta.
### Alternativas
- **Cambios ad-hoc:** más rápidos pero más riesgosos.
- **Cómo elegir:** planifica para cambios de alto impacto.

## Modos de fallo y trampas
- Omitir la comunicación con los interesados.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de éxito de cambios.
- **Logs:** fallos de cambios.
- **Alertas:** eventos de reversión.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Evaluar riesgo
  - [ ] Notificar a interesados
  - [ ] Validar después del cambio

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ejecutar cambios sin un plan de reversión.
  - **Por qué es malo:** interrupciones prolongadas.
  - **Mejor enfoque:** definir pasos de reversión.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La planificación operacional trata sobre programar y ejecutar cambios en producción de forma segura. Reduce el riesgo y asegura que los equipos estén alineados.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es siempre necesaria la planificación?
   - **R:** para cambios de alto impacto, sí.
2) **P:** ¿Puedes omitir la comunicación?
   - **R:** no; los interesados deben ser informados.
3) **P:** ¿La planificación ralentiza la entrega?
   - **R:** agrega pasos pero reduce el riesgo.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir planificación operacional.
- [ ] Puedo indicar cuándo usarla.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar una señal de éxito.

## Enlaces (SIN duplicación)
### Prerequisitos
- N/A

### Temas relacionados
- [Ventanas de mantenimiento](maintenance-windows.md)

### Comparar con
- [Fundamentos de gestión de incidentes](incident-management-basics.md)
