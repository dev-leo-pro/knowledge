---
id: nosql-access-patterns
title: "Patrones de acceso NoSQL"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Patrones de acceso NoSQL

## TL;DR (BLUF)
- El modelado de datos NoSQL empieza desde los patrones de acceso, no desde las entidades.
- Úsalo cuando puedes definir las consultas por adelantado y necesitas escala.
- Trade-off: nuevas consultas frecuentemente requieren nuevas tablas o índices.

## Definición
**Qué es:** Diseñar estructuras de datos alrededor de patrones de consulta conocidos en sistemas NoSQL.
**Términos clave:** patrón de acceso, desnormalización, clave de partición.

## Por qué importa
- Previene rediseños costosos y particiones calientes.
- Patrones de acceso desalineados causan cuellos de botella de rendimiento.

## Alcance y no-objetivos
**Dentro del alcance:** modelado orientado por patrones de acceso en sistemas NoSQL.
**Fuera del alcance / NO resuelto por esto:** mecánicas a nivel de clave y detalles de sintaxis de consultas.

## Modelo mental / Intuición
- Empieza con "¿Cómo voy a consultar?" y luego construye la tabla para esa consulta.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Conoces las consultas críticas por adelantado.
- Necesitas escala y latencia predecibles.
### Evítalo cuando
- Las consultas son desconocidas o cambian constantemente.

## Cómo lo usaría (práctico)
- **Contexto:** Tabla de DynamoDB para actividad de usuario.
- **Pasos:** listar patrones de acceso → diseñar PK/SK → agregar GSIs para consultas secundarias.
- **Cómo se ve el éxito:** latencia estable y sin particiones calientes.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** rendimiento escalable y predecible.
- **Desventajas / Riesgos:** consultas rígidas y costo de rediseño.
### Alternativas
- **PostgreSQL:** consultas SQL flexibles.
- **Cómo elegir:** usa patrones de acceso NoSQL cuando la escala y la predecibilidad importan más que la flexibilidad.

## Modos de fallo y trampas
- Particiones calientes por claves sesgadas.
- Sobrecargar una tabla única con patrones de acceso no relacionados.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** sesgo de partición, throttling, latencia.
- **Logs:** patrones de acceso causando claves calientes.
- **Alertas:** throttles en aumento o latencia p95.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Listar patrones de acceso explícitamente
  - [ ] Diseñar PK/SK para cada uno
  - [ ] Validar con pruebas de carga

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Modelar entidades primero, consultas después.
  - **Por qué es malo:** lleva a refactorizaciones costosas.
  - **Mejor enfoque:** empezar desde los patrones de acceso.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los patrones de acceso NoSQL significan que diseñas el modelo de datos alrededor de cómo consultas. Es genial para escalar pero requiere conocer tus consultas por adelantado.

### Preguntas trampa (con respuestas)
1) **P:** ¿Se pueden agregar nuevas consultas gratis?
   - **R:** no; usualmente agregas nuevos índices o tablas.
2) **P:** ¿NoSQL siempre es más rápido que SQL?
   - **R:** no; depende de los patrones de acceso y la carga de trabajo.
3) **P:** ¿Los patrones de acceso importan en DynamoDB?
   - **R:** sí; son centrales al diseño.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el modelado por patrón de acceso.
- [ ] Puedo explicar cuándo usarlo.
- [ ] Puedo nombrar un modo de fallo.
- [ ] Puedo describir un paso de diseño.
- [ ] Puedo comparar con SQL.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Modelado de datos clave-valor](key-value-data-modeling.md)

### Temas relacionados
- [DynamoDB](dynamodb.md)
- [Cassandra](cassandra.md)
- [Modelado de datos clave-valor](key-value-data-modeling.md)

### Comparar con
- [PostgreSQL](postgresql.md) — SQL flexible vs diseño por patrón de acceso.
