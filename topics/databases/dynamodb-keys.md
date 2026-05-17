---
id: dynamodb-keys
title: "Claves de DynamoDB (PK/SK)"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, dynamodb, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Claves de DynamoDB (PK/SK)

## TL;DR (BLUF)
- La clave de partición (PK) y la clave de ordenación (SK) definen la ubicación del ítem y el acceso a consultas.
- Usa claves compuestas para soportar múltiples patrones de acceso.
- Trade-off: un diseño de claves pobre causa particiones calientes y consultas limitadas.

## Definición
**Qué es:** El esquema de clave primaria en DynamoDB: PK para particionamiento y SK opcional para ordenación.
**Términos clave:** clave de partición, clave de ordenación, clave compuesta, patrón de acceso.

## Por qué importa
- El diseño de claves determina la escalabilidad y la capacidad de consulta.
- Claves malas causan throttling y carga desigual.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de PK/SK y principios de diseño.
**Fuera del alcance / NO resuelto por esto:** índices secundarios y streams.

## Modelo mental / Intuición
- PK elige el bucket; SK ordena los ítems dentro de él.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas rendimiento predecible y acceso basado en claves.
### Evítalo cuando
- Requieres consultas ad-hoc a través de muchos atributos.

## Cómo lo usaría (práctico)
- **Contexto:** Logs de actividad de usuario.
- **Pasos:** elegir PK para usuario → SK para timestamp → consultar por rango de tiempo.
- **Cómo se ve el éxito:** particiones balanceadas y consultas de rango eficientes.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** consultas basadas en claves eficientes.
- **Desventajas / Riesgos:** patrones de acceso rígidos.
### Alternativas
- **GSI/LSI:** para patrones de acceso secundarios.
- **Cómo elegir:** empezar con PK/SK para patrones de acceso principales, luego agregar índices.

## Modos de fallo y trampas
- Particiones calientes por claves monotónicas.
- PK sobrecargada llevando a particiones grandes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** throttles, sesgo de partición, latencia.
- **Logs:** patrones de acceso de claves calientes.
- **Alertas:** throttling sostenido.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Distribuir valores de PK uniformemente
  - [ ] Usar SK para consultas de rango

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar una sola PK estática para todos los ítems.
  - **Por qué es malo:** crea una partición caliente.
  - **Mejor enfoque:** particionar por una clave de alta cardinalidad.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- DynamoDB usa claves de partición para distribuir datos y claves de ordenación para ordenar ítems dentro de una partición. Un buen diseño de claves lo es todo para el rendimiento y la escalabilidad.

### Preguntas trampa (con respuestas)
1) **P:** ¿Puede existir una tabla sin clave de ordenación?
   - **R:** sí; puede tener solo una clave de partición.
2) **P:** ¿Una buena PK garantiza buen rendimiento?
   - **R:** no si crea particiones calientes o sesgo.
3) **P:** ¿Puedes consultar por atributos que no son clave sin índices?
   - **R:** no eficientemente; necesitas GSIs/LSIs.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir PK y SK.
- [ ] Puedo explicar por qué las claves importan.
- [ ] Puedo describir una partición caliente.
- [ ] Puedo dar un ejemplo de diseño de claves.
- [ ] Puedo nombrar un trade-off.

## Enlaces (SIN duplicación)
### Prerequisitos
- [NoSQL access patterns](nosql-access-patterns.md)

### Temas relacionados
- [DynamoDB](dynamodb.md)
- [DynamoDB GSI/LSI](dynamodb-gsi-lsi.md)

### Comparar con
- [PostgreSQL](postgresql.md) — claves relacionales vs claves de patrón de acceso.
