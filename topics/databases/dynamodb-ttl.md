---
id: dynamodb-ttl
title: "DynamoDB TTL"
type: concept
status: learning
importance: 45
difficulty: easy
tags: [database, dynamodb]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# DynamoDB TTL

## TL;DR (BLUF)
- DynamoDB TTL es una implementación de [TTL (Time-to-Live)](ttl.md) en DynamoDB.
- Úsalo para datos efímeros y registros tipo caché.
- Trade-off: las eliminaciones son eventuales y no inmediatas.

## Definición
**Qué es:** Una característica de DynamoDB que elimina ítems cuando un atributo TTL designado expira.
**Términos clave:** atributo TTL, expiración, eliminación eventual.

## Por qué importa
- Automatiza la limpieza y reduce el costo de almacenamiento.
- No es preciso; el timing de eliminación es de mejor esfuerzo.

## Alcance y no-objetivos
**Dentro del alcance:** expiración automática de ítems.
**Fuera del alcance / NO resuelto por esto:** garantías de eliminación en tiempo real.

## Modelo mental / Intuición
- Piensa en TTL como un conserje en segundo plano, no un temporizador preciso.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los datos tengan una expiración natural (sesiones, tokens temporales).
### Evítalo cuando
- Necesites timing de eliminación exacto.

## Cómo lo usaría (práctico)
- **Contexto:** Registros de sesión con expiración.
- **Pasos:** agregar atributo TTL → habilitar TTL → monitorear tasa de eliminación.
- **Cómo se ve el éxito:** los datos expirados desaparecen sin limpieza manual.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** limpieza automatizada.
- **Contras / Riesgos:** eliminación retrasada; posibles lecturas obsoletas.
### Alternativas
- **Trabajos programados:** limpieza precisa.
- **Cómo elegir:** usa TTL para limpieza de mejor esfuerzo; usa trabajos para timing estricto.

## Modos de fallo y errores comunes
- Depender de TTL para cumplimiento estricto de seguridad.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de eliminación TTL, crecimiento de almacenamiento.
- **Logs:** auditar ítems expirados en la lógica de la aplicación.
- **Alertas:** almacenamiento que no disminuye como se esperaba.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Configurar atributo TTL en segundos epoch
  - [ ] No depender de TTL para eliminación inmediata

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar TTL como control de seguridad.
  - **Por qué es malo:** la eliminación es retrasada.
  - **Mejor enfoque:** imponer expiración en la lógica de la aplicación.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- DynamoDB TTL elimina ítems después de que expiran, pero es de mejor esfuerzo y no inmediato. Es genial para limpieza, no para garantías de timing estricto.

### Preguntas trampa (con respuestas)
1) **P:** ¿TTL elimina ítems exactamente en el momento de expiración?
   - **R:** No; la eliminación es eventual.
2) **P:** ¿TTL se puede usar para invalidación de sesiones en tiempo real?
   - **R:** No; usa verificaciones a nivel de aplicación.
3) **P:** ¿TTL genera eventos de Streams?
   - **R:** Sí; las eliminaciones por TTL pueden aparecer en Streams.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir TTL con precisión.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar el timing de eliminación.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [DynamoDB](dynamodb.md)
- [TTL (Time-to-Live)](ttl.md)

### Temas relacionados
- [DynamoDB Streams](dynamodb-streams.md)

### Comparar con
- [Trabajos de limpieza programados](../operations/scheduled-cleanup-jobs.md)
