---
id: requirements-basics
title: "Requirements Basics"
type: skill
status: learning
importance: 40
difficulty: easy
tags: [system-design]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Requisitos

## TL;DR (BLUF)
- Los requisitos definen qué debe hacer un sistema y sus restricciones.
- Úsalos para guiar el diseño y los trade-offs.
- Trade-off: demasiado detalle puede ralentizar el progreso.

## Definición
**Qué es:** El proceso de capturar necesidades funcionales y no funcionales.
**Términos clave:** requisitos funcionales, requisitos no funcionales, restricciones.

## Por qué importa
- Requisitos claros reducen el retrabajo.
- La ambigüedad lleva a elecciones de arquitectura incorrectas.

## Alcance y no-objetivos
**Dentro del alcance:** capturar y priorizar requisitos.
**Fuera del alcance / NO resuelto por esto:** gestión de producto detallada.

## Modelo mental / Intuición
- Los requisitos son el contrato de lo que el sistema debe lograr.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Inicies un nuevo sistema o cambio importante.
### Evítalo cuando
- Estés haciendo un cambio trivial.

## Cómo lo usaría (práctico)
- **Contexto:** Nuevo pipeline de datos.
- **Pasos:** definir objetivos → identificar restricciones → documentar trade-offs.
- **Cómo se ve el éxito:** decisiones de diseño alineadas.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** claridad.
- **Contras / Riesgos:** parálisis por análisis.
### Alternativas
- **Prototipo primero:** aprendizaje más rápido.
- **Cómo elegir:** usar requisitos cuando la corrección y la escala importan.

## Modos de fallo y errores comunes
- Pasar por alto requisitos no funcionales.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** rotación de alcance, solicitudes de cambio.
- **Logs:** cambios de requisitos.
- **Alertas:** señales de retrabajo repetido.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Capturar necesidades funcionales
  - [ ] Capturar restricciones no funcionales

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Diseñar sin claridad en los objetivos.
  - **Por qué es malo:** esfuerzo desperdiciado.
  - **Mejor enfoque:** documentar requisitos tempranamente.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los requisitos son la línea base para el diseño: qué debe hacer el sistema y qué restricciones existen. Previenen decisiones de arquitectura desalineadas.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los requisitos son solo funcionales?
   - **R:** No; las restricciones no funcionales son críticas.
2) **P:** ¿Los requisitos son fijos para siempre?
   - **R:** No; evolucionan pero deben rastrearse.
3) **P:** ¿Puedes omitir requisitos para cambios pequeños?
   - **R:** A veces, pero sé explícito.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir requisitos.
- [ ] Puedo nombrar restricciones no funcionales.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar un trade-off.
- [ ] Puedo conectar requisitos con diseño.

## Enlaces (SIN duplicación)
### Prerrequisitos
- N/A

### Temas relacionados
- [Fundamentos de diseño de API](api-design-basics.md)

### Comparar con
- [Fundamentos de rendimiento](performance-basics.md) — restricciones vs optimización.
