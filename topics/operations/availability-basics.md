---
id: availability-basics
title: "Fundamentos de Disponibilidad"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [operations, reliability]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Disponibilidad

## TL;DR (BLUF)
- La disponibilidad mide con qué frecuencia un sistema está operativo.
- Úsala para establecer objetivos de uptime y SLOs.
- Trade-off: mayor disponibilidad cuesta más.

## Definición
**Qué es:** La proporción de tiempo que un sistema está funcionando y accesible.
**Términos clave:** uptime, downtime, SLO.

## Por qué importa
- La disponibilidad afecta la confianza del usuario y los ingresos.

## Alcance y no-objetivos
**Dentro del alcance:** objetivos de uptime y trade-offs de disponibilidad.
**Fuera del alcance / NO resuelto por esto:** semánticas de corrección/fiabilidad.

## Modelo mental / Intuición
- La disponibilidad trata sobre tiempo, no sobre corrección.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Estableces requisitos de uptime.
### Evítalo cuando
- El servicio no es crítico y es experimental.

## Cómo lo usaría (práctico)
- **Contexto:** Definir objetivos de uptime para una API.
- **Pasos:** elegir SLO -> medir uptime -> rastrear presupuestos de error.
- **Cómo se ve el éxito:** uptime estable dentro del SLO.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** objetivo claro.
- **Desventajas / Riesgos:** mayor coste.
### Alternativas
- **Disponibilidad de mejor esfuerzo:** para sistemas no críticos.
- **Cómo elegir:** alinear objetivos con el impacto al negocio.

## Modos de fallo y trampas
- Medir disponibilidad sin considerar el impacto en el usuario.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** porcentaje de uptime, tasa de errores.
- **Logs:** informes de interrupciones.
- **Alertas:** tasa de consumo de SLO.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir SLOs
  - [ ] Medir uptime con precisión

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Perseguir cinco nueves sin necesidad.
  - **Por qué es malo:** coste enorme.
  - **Mejor enfoque:** establecer objetivos realistas.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La disponibilidad es el porcentaje de tiempo que tu sistema está activo y sirviendo peticiones. Mayor disponibilidad cuesta más, así que alinea los objetivos con las necesidades del negocio.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es la disponibilidad lo mismo que la fiabilidad?
   - **R:** no; la fiabilidad incluye corrección y consistencia.
2) **P:** ¿Más redundancia siempre mejora la disponibilidad?
   - **R:** no si añade complejidad y modos de fallo.
3) **P:** ¿Es realista el 100% de disponibilidad?
   - **R:** no; apunta a SLOs realistas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir disponibilidad.
- [ ] Puedo explicar trade-offs.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo describir una señal de monitoreo.
- [ ] Puedo relacionarla con fundamentos de fiabilidad.

## Enlaces (SIN duplicación)
### Prerequisitos
- N/A

### Temas relacionados
- [Fundamentos de fiabilidad](reliability-basics.md)

### Comparar con
- [Fundamentos de fiabilidad](reliability-basics.md) — uptime vs corrección.
