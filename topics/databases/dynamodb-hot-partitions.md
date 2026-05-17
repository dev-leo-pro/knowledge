---
id: dynamodb-hot-partitions
title: "Particiones Calientes en DynamoDB"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, dynamodb, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Particiones Calientes en DynamoDB

## TL;DR (BLUF)
- Las particiones calientes en DynamoDB son un caso específico de DynamoDB de [Particiones calientes](hot-partitions.md).
- Usa claves de alta cardinalidad y patrones de sharding para distribuir la carga.
- Trade-off: diseño de claves más complejo y fan-out de consultas.

## Definición
**Qué es:** Una manifestación específica de DynamoDB del sesgo de partición donde un pequeño conjunto de claves de partición dominan el tráfico.
**Términos clave:** clave de partición, sesgo, throttling.

## Por qué importa
- Las particiones calientes causan throttling y alta latencia.
- A menudo requieren rediseño de esquema para solucionarse.

## Alcance y no-objetivos
**Dentro del alcance:** identificar y evitar particiones calientes.
**Fuera del alcance / NO resuelto por esto:** ajustar latencia de consultas no relacionada.

## Modelo mental / Intuición
- Piensa en todo el tráfico yendo a la misma caja registradora.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Ves throttling o latencia para claves específicas.
### Evítalo cuando
- Puedes diseñar claves para distribuir el tráfico uniformemente.

## Cómo lo usaría (práctico)
- **Contexto:** La clave de partición es ID de usuario con tráfico pesado en pocos usuarios.
- **Pasos:** agregar sharding de claves → distribuir la carga → monitorear throttling.
- **Cómo se ve el éxito:** el throttling baja y la latencia se estabiliza.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** rendimiento y latencia mejorados.
- **Desventajas / Riesgos:** complejidad en el diseño de claves.
### Alternativas
- **Caché:** reducir lecturas de claves calientes.
- **Cómo elegir:** arreglar el diseño de claves primero; usar caché si es necesario.

## Modos de fallo y trampas
- Hacer sharding de claves sin manejar el fan-out de lectura.
- Claves basadas en tiempo causando picos repentinos.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** throttling, latencia a nivel de partición.
- **Logs:** patrones de acceso de claves calientes.
- **Alertas:** throttles recurrentes para claves específicas.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Analizar distribución de claves
  - [ ] Agregar sharding cuando sea necesario

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar claves monotónicas para tráfico pesado.
  - **Por qué es malo:** crea particiones calientes.
  - **Mejor enfoque:** agregar aleatoriedad o sharding.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las particiones calientes ocurren cuando demasiadas solicitudes golpean la misma clave. DynamoDB entonces hace throttling a esa partición; lo arreglas mejorando la distribución de claves.

### Preguntas trampa (con respuestas)
1) **P:** ¿El throughput provisionado puede arreglar particiones calientes solo?
   - **R:** no; no resuelve claves sesgadas.
2) **P:** ¿Las particiones calientes son solo un problema de escritura?
   - **R:** no; las lecturas también pueden ser calientes.
3) **P:** ¿Puedes evitar particiones calientes solo con GSIs?
   - **R:** no si la clave base sigue sesgada.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir particiones calientes.
- [ ] Puedo explicar una mitigación.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo describir señales de detección.
- [ ] Puedo relacionarlo con el diseño de claves.

## Enlaces (SIN duplicación)
### Prerequisitos
- [DynamoDB keys](dynamodb-keys.md)
- [Hot partitions](hot-partitions.md)

### Temas relacionados
- [DynamoDB](dynamodb.md)

### Comparar con
- [Read/write contention](read-write-contention.md) — contención en sistemas relacionales.
