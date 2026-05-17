---
id: data-modeling-basics
title: "Fundamentos de Modelado de Datos"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [database, modeling]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Modelado de Datos

## TL;DR (BLUF)
- El modelado de datos define cómo se estructuran y relacionan los datos.
- Úsalo para alinear el almacenamiento con los patrones de acceso y la corrección.
- Trade-off: más estructura puede reducir la flexibilidad.

## Definición
**Qué es:** El proceso de estructurar entidades, atributos y relaciones.
**Términos clave:** entidad, relación, esquema.

## Por qué importa
- Buenos modelos reducen bugs y mejoran el rendimiento de consultas.
- Malos modelos causan duplicación y migraciones costosas.

## Alcance y no-objetivos
**Dentro del alcance:** entidades, relaciones y elecciones de claves.
**Fuera del alcance / NO resuelto por esto:** tácticas de normalización/desnormalización y ajuste de rendimiento.

## Modelo mental / Intuición
- Modelar es elegir la forma de tus datos antes de almacenarlos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Estás diseñando nuevas tablas o colecciones.
### Evítalo cuando
- Los datos son transitorios y desechables (raro).

## Cómo lo usaría (práctico)
- **Contexto:** Nueva funcionalidad que almacena preferencias de usuario.
- **Pasos:** identificar entidades → elegir claves → decidir normalización.
- **Cómo se ve el éxito:** datos consistentes y consultas claras.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** claridad y corrección.
- **Desventajas / Riesgos:** los cambios de esquema pueden ser costosos.
### Alternativas
- **Almacenamiento sin esquema:** iteración más rápida pero más arriesgado.
- **Cómo elegir:** modelar de antemano cuando la corrección importa.

## Modos de fallo y trampas
- Sobre-modelar para requisitos no probados.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** frecuencia de migraciones, complejidad de consultas.
- **Logs:** errores de migración.
- **Alertas:** rollbacks de esquema repetidos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir entidades y claves
  - [ ] Decidir nivel de normalización

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Diseñar tablas solo alrededor de vistas de UI.
  - **Por qué es malo:** el modelo de datos se vuelve frágil.
  - **Mejor enfoque:** modelar alrededor de entidades de negocio y patrones de acceso.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El modelado de datos es decidir cómo estructurar los datos para que sean consistentes y fáciles de consultar. Equilibra corrección, rendimiento y flexibilidad.

### Preguntas trampa (con respuestas)
1) **P:** ¿El diseño de esquema es opcional en NoSQL?
   - **R:** no; aún modelas para patrones de acceso.
2) **P:** ¿Deberías modelar solo para consultas actuales?
   - **R:** principalmente, pero ten en cuenta cambios futuros.
3) **P:** ¿La desnormalización siempre es mala?
   - **R:** no; es un trade-off de rendimiento.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir modelado de datos.
- [ ] Puedo mencionar un trade-off.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo describir un paso de modelado.
- [ ] Puedo comparar normalización vs desnormalización.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Requirements basics](../system-design/requirements-basics.md)

### Temas relacionados
- [Normalization](normalization.md)
- [Relational model basics](relational-model-basics.md)

### Comparar con
- [NoSQL access patterns](nosql-access-patterns.md) — modelar para consultas vs relaciones.
