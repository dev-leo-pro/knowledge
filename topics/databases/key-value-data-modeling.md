---
id: key-value-data-modeling
title: "Key-Value Data Modeling"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Modelado de Datos Clave-Valor

## TL;DR (BLUF)
- El modelado clave-valor diseña datos alrededor del acceso basado en claves.
- Úsalo cuando las consultas sean simples y predecibles.
- Trade-off: flexibilidad limitada para nuevos patrones de consulta.

## Definición
**Qué es:** Estructurar datos para que las búsquedas se hagan por claves primarias con claves de rango opcionales.
**Términos clave:** clave de partición, clave de ordenamiento, patrón de acceso.

## Por qué importa
- Es la base de DynamoDB y otros sistemas NoSQL.
- Un mal diseño de claves lleva a particiones calientes y rediseños.

## Alcance y no-objetivos
**Dentro del alcance:** modelado centrado en claves para consultas estilo KV.
**Fuera del alcance / NO resuelto por esto:** trade-offs más amplios de modelado NoSQL entre stores de documentos/columnas.

## Modelo mental / Intuición
- Diseña alrededor de "obtener por clave" y "obtener rango por clave".

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los patrones de acceso sean fijos y basados en claves.
### Evítalo cuando
- Necesites filtrado flexible a través de muchos atributos.

## Cómo lo usaría (práctico)
- **Contexto:** Log de eventos por ID de usuario.
- **Pasos:** elegir PK por usuario → SK por tiempo → diseñar índices secundarios si es necesario.
- **Cómo se ve el éxito:** latencia predecible con particiones balanceadas.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** predecible y escalable.
- **Contras / Riesgos:** patrones de consulta rígidos.
### Alternativas
- **Bases de datos SQL:** consultas flexibles.
- **Cómo elegir:** modelado clave-valor para escala y predecibilidad.

## Modos de fallo y errores comunes
- Claves monótonas causando sesgo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** throttling, latencia p95.
- **Logs:** patrones de claves calientes.
- **Alertas:** throttling repetido.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Validar patrones de acceso
  - [ ] Distribuir el espacio de claves

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Diseñar entidades antes que patrones de acceso.
  - **Por qué es malo:** rediseños costosos.
  - **Mejor enfoque:** modelar por consultas.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El modelado clave-valor comienza desde el patrón de acceso. Eliges claves que soporten tus consultas y distribuyan la carga; si las consultas cambian, el modelo debe cambiar.

### Preguntas trampa (con respuestas)
1) **P:** ¿Puedes agregar nuevas consultas sin cambiar el modelo?
   - **R:** No siempre; puedes necesitar nuevos índices o tablas.
2) **P:** ¿Los stores clave-valor siempre son más simples que SQL?
   - **R:** No necesariamente; el modelado puede ser complejo.
3) **P:** ¿Las claves solo importan para escrituras?
   - **R:** No; definen las lecturas y la escalabilidad.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir el modelado clave-valor.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un modo de fallo.
- [ ] Puedo explicar los patrones de acceso.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Patrones de acceso NoSQL](nosql-access-patterns.md)

### Temas relacionados
- [Claves DynamoDB (PK/SK)](dynamodb-keys.md)
- [Patrones de acceso NoSQL](nosql-access-patterns.md)

### Comparar con
- [Fundamentos de SQL](sql-foundations.md) — consultas declarativas vs acceso basado en claves.
