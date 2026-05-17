---
id: redis
title: "Redis"
type: technology
status: learning
importance: 65
difficulty: medium
tags: [database, cache, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Redis

## TL;DR (BLUF)
- Almacén clave-valor en memoria con estructuras de datos ricas y persistencia opcional.
- Úsalo para caché, limitación de tasa, colas y búsquedas de baja latencia.
- Trade-off: costo de memoria, opciones de durabilidad y complejidad operativa a escala.

## Definición
**Qué es:** Un almacén de datos en memoria que soporta [tipos de datos de Redis](redis-data-types.md) y [persistencia de Redis (RDB/AOF)](redis-persistence.md) opcional.
**Términos clave:** en memoria, [TTL (Time-to-Live)](ttl.md), [persistencia de Redis (RDB/AOF)](redis-persistence.md), [políticas de desalojo de Redis](redis-eviction-policies.md), [replicación y Sentinel de Redis](redis-replication-sentinel.md), [escalado de Redis](redis-scaling.md), [Redis Pub/Sub](redis-pub-sub.md), [Redis Streams](redis-streams.md).

## Por qué importa
- Habilita acceso de muy baja latencia para datos calientes y patrones de coordinación.
- El mal uso puede causar pérdida de datos o estampidas de caché.

## Alcance y no-objetivos
**Dentro del alcance:** caché, acceso rápido clave-valor, contadores, limitación de tasa, colas ligeras.
**Fuera del alcance / NO resuelto por esto:** consultas relacionales complejas, analíticas o almacenamiento primario duradero.

## Modelo mental / Intuición
- Piensa en Redis como una memoria compartida súper rápida con estructuras de datos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas acceso sub-milisegundo a datos calientes.
- Necesitas caché basado en TTL, contadores, limitación de tasa o colas de corta duración.
- Puedes tolerar persistencia eventual o aceptar Redis como almacén secundario.
### Evítalo cuando
- Requieres durabilidad fuerte como sistema de registro.
- Tu conjunto de datos no cabe en memoria o no puedes aceptar desalojo.
- Necesitas joins complejos o consultas ad-hoc.

## Cómo lo usaría (práctico)
- **Contexto:** Respuestas de API con lecturas repetidas.
- **Pasos:** agregar cache-aside → configurar TTLs → prevenir estampida → monitorear tasa de aciertos y desalojos.
- **Cómo se ve el éxito:** alta tasa de aciertos de caché y carga reducida en la BD.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** extremadamente rápido, estructuras de datos ricas, primitivas flexibles.
- **Desventajas / Riesgos:** costo de memoria; trade-offs de persistencia; complejidad de clustering.
### Alternativas
- **PostgreSQL:** almacenamiento relacional duradero.
- **DynamoDB:** clave-valor duradero a escala.
- **Cómo elegir:** usa Redis para caché y datos efímeros rápidos; usa BDs duraderas para la fuente de verdad.

## Modos de fallo y trampas
- Desalojos causando fallos de caché repentinos.
- Estampida (thundering herd) al expirar el caché.
- Claves calientes causando picos de latencia.
- Sobreuso de valores grandes causando presión de memoria.
- Retraso de replicación llevando a lecturas obsoletas si lees de réplicas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** ratio de aciertos, uso de memoria, fragmentación, conteo de desalojos, latencia p95, retraso de replicación.
- **Logs:** eventos de slowlog, errores de persistencia.
- **Alertas:** desalojos en aumento, caída del ratio de aciertos o picos de retraso de replicación.

## Notas de implementación (si aplica)
- **Checklist**
   - [ ] Elegir política de desalojo (ej., allkeys-lru)
   - [ ] Configurar TTLs en claves de caché
   - [ ] Proteger contra estampida (bloqueo o TTLs con jitter)
   - [ ] Elegir modo de persistencia (RDB vs AOF) si es necesario
   - [ ] Monitorear memoria, desalojos y ratio de aciertos

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar Redis como el único sistema de registro.
  - **Por qué es malo:** pérdida de datos en fallo o mala configuración.
  - **Mejor enfoque:** mantener una base de datos duradero como fuente de verdad.
- **Anti-patrón:** Usar `KEYS *` en producción.
   - **Por qué es malo:** bloquea el servidor y dispara la latencia.
   - **Mejor enfoque:** usar `SCAN` con paginación.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Redis es un almacén clave-valor en memoria que es genial para caché y operaciones de baja latencia. Es rápido pero no es un reemplazo para una base de datos duradero.

### Preguntas trampa (con respuestas)
1) **P:** ¿Redis es una base de datos duradero por defecto?
   - **R:** no; la persistencia es opcional y tiene trade-offs.
2) **P:** ¿Redis siempre mejora el rendimiento?
   - **R:** no; una mala estrategia de caché puede agregar complejidad sin beneficios.
3) **P:** ¿Redis puede almacenar tipos de datos complejos?
   - **R:** sí; soporta lists, sets, hashes y más.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir Redis con precisión.
- [ ] Puedo decir cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo de caché.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de caché](../system-design/caching-fundamentals.md)

### Temas relacionados
- [PostgreSQL](postgresql.md)
- [Políticas de desalojo de Redis](redis-eviction-policies.md)
- [Tipos de datos de Redis](redis-data-types.md)
- [Redis Pub/Sub](redis-pub-sub.md)
- [Escalado de Redis (vertical y horizontal)](redis-scaling.md)
- [Redis Streams](redis-streams.md)
- [Persistencia de Redis (RDB/AOF)](redis-persistence.md)
- [Replicación y Sentinel de Redis](redis-replication-sentinel.md)
### Comparar con
- [DynamoDB](dynamodb.md) — clave-valor duradero vs caché en memoria.
- [PostgreSQL](postgresql.md) — almacenamiento relacional duradero.
