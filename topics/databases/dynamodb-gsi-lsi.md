---
id: dynamodb-gsi-lsi
title: "DynamoDB GSI/LSI"
type: concept
status: learning
importance: 60
difficulty: hard
tags: [database, dynamodb, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# DynamoDB GSI/LSI

## TL;DR (BLUF)
- Los GSIs y LSIs proporcionan patrones de acceso secundarios en DynamoDB.
- Úsalos cuando necesitas patrones de consulta adicionales más allá de PK/SK.
- Trade-off: costo de escritura extra y complejidad.

## Definición
**Qué es:** Índices secundarios en DynamoDB: Índice Secundario Global (GSI) e Índice Secundario Local (LSI).
**Términos clave:** GSI, LSI, proyección, amplificación de escritura.

## Por qué importa
- Permiten rutas de consulta alternativas sin escaneos completos de tabla.
- El uso excesivo aumenta los costos de escritura y la latencia.

## Alcance y no-objetivos
**Dentro del alcance:** propósito de índices secundarios y trade-offs.
**Fuera del alcance / NO resuelto por esto:** analítica ad-hoc o joins.

## Modelo mental / Intuición
- Piensa en los GSIs/LSIs como "vistas" adicionales de los mismos datos con claves diferentes.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas patrones de consulta secundarios no servidos por PK/SK.
### Evítalo cuando
- Puedes modelar con un diseño de tabla única sin índices extra.

## Cómo lo usaría (práctico)
- **Contexto:** Consultar ítems por estado además de por usuario.
- **Pasos:** agregar GSI en estado → proyectar atributos necesarios → monitorear costo de escritura.
- **Cómo se ve el éxito:** consultas secundarias dentro del SLA sin escaneos pesados.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** habilitan nuevos patrones de acceso.
- **Desventajas / Riesgos:** amplificación de escritura y costo extra.
### Alternativas
- **Rediseño de tabla:** ajustar claves para evitar índices extra.
- **Cómo elegir:** agregar solo cuando los patrones de acceso son reales y frecuentes.

## Modos de fallo y trampas
- Throttling de GSI bajo escrituras pesadas.
- Proyectar demasiados atributos aumenta el costo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** throttles de escritura de GSI, tamaño del índice, latencia de consultas.
- **Logs:** errores de throttling.
- **Alertas:** errores de escritura de GSI crecientes.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Confirmar necesidad del patrón de acceso
  - [ ] Proyectar atributos mínimos
  - [ ] Monitorear costo de escritura

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Agregar GSIs para consultas hipotéticas.
  - **Por qué es malo:** costo extra sin beneficio.
  - **Mejor enfoque:** agregar índices solo para consultas probadas.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los GSIs y LSIs son los índices secundarios de DynamoDB. Permiten consultar por claves alternativas pero agregan costo de escritura y complejidad.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los GSIs son gratis de agregar?
   - **R:** no; aumentan el costo de escritura y pueden hacer throttling.
2) **P:** ¿Los LSIs pueden tener claves de partición diferentes?
   - **R:** no; los LSIs comparten la clave de partición de la tabla base.
3) **P:** ¿Los GSIs garantizan consistencia fuerte?
   - **R:** no; las consultas de GSI son eventualmente consistentes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir GSI y LSI.
- [ ] Puedo indicar cuándo usarlos.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar la consistencia de GSI.

## Enlaces (SIN duplicación)
### Prerequisitos
- [DynamoDB keys](dynamodb-keys.md)

### Temas relacionados
- [DynamoDB](dynamodb.md)

### Comparar con
- [Index](index.md) — índices relacionales vs índices secundarios de DynamoDB.
