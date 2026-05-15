---
id: cassandra
title: "Cassandra"
type: technology
status: learning
importance: 55
difficulty: hard
tags: [database, nosql, distributed]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Cassandra

## TL;DR (BLUF)
- Base de datos distribuida de columna ancha diseñada para alta disponibilidad y escalabilidad de escritura.
- Úsala para series temporales y cargas de trabajo intensivas en escritura con patrones de acceso predecibles.
- Trade-off: el modelado de datos es orientado a consultas y la consistencia es configurable.

## Definición
**Qué es:** Una base de datos NoSQL distribuida que usa un modelo de columna ancha y consistencia configurable.
**Términos clave:** clave de partición, factor de replicación, consistencia configurable, columna ancha.

## Por qué importa
- Maneja cargas de trabajo de escritura intensiva a gran escala con alta disponibilidad.
- Un modelado de datos pobre lleva a particiones calientes e ineficiencia.

## Alcance y no-objetivos
**Dentro del alcance:** cargas de trabajo de escritura intensiva, distribuidas globalmente, con consultas simples.
**Fuera del alcance / NO resuelto por esto:** joins complejos o analítica ad-hoc.

## Modelo mental / Intuición
- Piensa en Cassandra como un log distribuido y particionado optimizado para consultas predecibles.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas alto rendimiento de escritura y disponibilidad multi-región.
- Las consultas son predecibles y basadas en claves.
### Evítalo cuando
- Necesitas consultas SQL flexibles o consistencia fuerte por defecto.
- No puedes diseñar en torno a patrones de acceso.

## Cómo lo usaría (práctico)
- **Contexto:** Eventos de series temporales con altas tasas de escritura.
- **Pasos:** diseñar claves de partición → modelar tablas por consulta → probar particiones calientes.
- **Cómo se ve el éxito:** rendimiento de escritura estable con particiones balanceadas.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** alta disponibilidad, escalado de escritura lineal.
- **Desventajas / Riesgos:** modelado complejo; consistencia eventual por defecto.
### Alternativas
- **DynamoDB:** almacén clave-valor gestionado con diseño similar orientado a patrones de acceso.
- **PostgreSQL:** consultas relacionales con consistencia más fuerte.
- **Cómo elegir:** elige Cassandra para cargas de trabajo de escritura intensiva a gran escala con patrones de acceso estables.

## Modos de fallo y trampas
- Particiones calientes por claves sesgadas.
- Amplificación de lectura por filas anchas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de escritura, distribución de tamaño de particiones, métricas de compactación.
- **Logs:** timeouts y errores de lectura/escritura.
- **Alertas:** timeouts crecientes o acumulación de compactación.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Diseñar claves de partición para distribuir la carga
  - [ ] Modelar tablas por consulta
  - [ ] Monitorear compactación y latencia

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Diseñar tablas como un modelo relacional.
  - **Por qué es malo:** lleva a consultas ineficientes y particiones calientes.
  - **Mejor enfoque:** modelar por patrones de acceso.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Cassandra es una base de datos distribuida de columna ancha construida para alto rendimiento de escritura y disponibilidad. Es excelente para patrones de acceso predecibles pero requiere un diseño cuidadoso de claves de partición y acepta consistencia eventual.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cassandra soporta joins?
   - **R:** no; es orientada a consultas y evita los joins.
2) **P:** ¿Cassandra es fuertemente consistente por defecto?
   - **R:** no; la consistencia es configurable y frecuentemente eventual.
3) **P:** ¿Se puede cambiar la clave primaria sin rediseñar?
   - **R:** no; es central al modelo de datos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir Cassandra con precisión.
- [ ] Puedo indicar cuándo usarla y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo de series temporales.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Partitioning & sharding](../system-design/partitioning-and-sharding.md)

### Temas relacionados
- [DynamoDB](dynamodb.md)
### Comparar con
- [DynamoDB](dynamodb.md) — clave-valor gestionado vs columna ancha autogestionada.
- [PostgreSQL](postgresql.md) — SQL + consistencia fuerte vs consistencia configurable.
