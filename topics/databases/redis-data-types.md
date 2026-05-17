---
id: redis-data-types
title: "Tipos de datos de Redis"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [redis, data-structures, nosql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Tipos de datos de Redis

## TL;DR (BLUF)
- Redis ofrece estructuras de datos especializadas para diferentes patrones de acceso.
- Elegir el tipo correcto reduce memoria y mejora las operaciones atómicas; los sorted sets destacan para datos ordenados y clasificados.
- Trade-off: primitivas potentes vs modelado cuidadoso de claves y memoria.

## Definición
**Qué es:** Las estructuras de datos centrales que Redis expone (strings, hashes, lists, sets, sorted sets, [Redis Streams](redis-streams.md), bitmaps, hyperloglogs e índices geoespaciales).
**Términos clave:** strings, hashes, lists, sets, sorted sets (zsets), [Redis Streams](redis-streams.md), bitmaps, HyperLogLog, geo.

## Por qué importa
- La elección del tipo de dato afecta el uso de memoria, la latencia y la corrección.
- Habilita operaciones atómicas sin lógica extra de aplicación.

## Alcance y no-objetivos
**Dentro del alcance:** seleccionar tipos de datos para coincidir con patrones de acceso y restricciones.
**Fuera del alcance / NO resuelto por esto:** consultas estilo SQL o joins complejos.

## Modelo mental / Intuición
- Trata Redis como una caja de herramientas de contenedores especializados, no solo un mapa clave-valor.
- **Sorted sets (zsets)** son una tabla de clasificación en vivo: los elementos se mantienen ordenados a medida que los puntajes cambian.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas contadores atómicos, sets, clasificaciones o colas con baja latencia.
- Quieres evitar bloqueos o ordenamiento del lado de la aplicación.
- Necesitas datos continuamente ordenados (ej., top-N, elementos ordenados por tiempo) sin re-ordenar en cada lectura.
### Evítalo cuando
- Necesitas consultas multi-atributo en grandes conjuntos de datos.
- Esperas documentos con esquemas que evolucionan y analíticas pesadas.
- Necesitas ordenamiento global a través de múltiples claves o joins complejos.

## Cómo lo usaría (práctico)
- **Contexto:** Tablas de clasificación en tiempo real y datos de sesión por usuario.
- **Pasos:** usar zsets para puntajes y consultas de ranking → hashes para campos de sesión → sets para membresía.
- **Cómo se ve el éxito:** consultas top-N rápidas sin ordenar en la app, uso de memoria predecible.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** operaciones atómicas, baja latencia, estructuras diseñadas para propósitos específicos.
- **Desventajas / Riesgos:** sobrecarga de memoria para claves pequeñas; complejidad de modelado; la memoria de zset crece con el conteo de miembros.
### Alternativas
- **Bases de datos de documentos:** mejores para documentos flexibles y consultas.
- **Bases de datos relacionales:** mejores para joins complejos y restricciones.
- **Cómo elegir:** usa tipos de datos de Redis cuando la velocidad y la atomicidad importan más que la flexibilidad de consultas.

## Modos de fallo y trampas
- Valores grandes en strings causando presión de memoria.
- Lists usadas como colas sin recortar (crecimiento ilimitado).
- Usar sets donde se requiere ordenamiento (deberían usarse sorted sets).
- Zsets sin normalización de puntaje (ej., mezclar timestamps y rangos) llevando a ordenamiento confuso.
- Sobreuso de zsets para clasificación multi-atributo (debería computarse el puntaje o usar otro almacén).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** uso de memoria por keyspace, detección de `bigkeys`, latencia p95.
- **Logs:** entradas de slowlog para operaciones pesadas.
- **Alertas:** crecimiento de claves grandes o picos de fragmentación de memoria.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Mapear cada patrón de acceso a un tipo de dato.
  - [ ] Mantener valores pequeños; preferir hashes para muchos campos.
   - [ ] Usar zsets para clasificación y ordenamiento por series de tiempo.
  - [ ] Recortar lists o streams para limitar memoria.
   - [ ] Definir una función de puntaje consistente para zsets (ej., rango, timestamp o puntaje ponderado).

## Mini ejemplo (si aplica)
### Tipos de datos: cuándo usar y ejemplos
- **Strings (valores simples)**
   - **Úsalo cuando:** contadores, flags, blobs en caché, tokens simples.
   - **Evítalo cuando:** necesitas actualizaciones a nivel de campo o lecturas parciales.
   - **Ejemplo:** `SET session:123 "..."` y `INCR rate_limit:user:42`.

- **Hashes (objetos con campos)**
   - **Úsalo cuando:** muchos campos pequeños por clave (perfiles, sesiones).
   - **Evítalo cuando:** necesitas consultas complejas a través de muchos hashes.
   - **Ejemplo:** `HSET user:42 name "Ana" plan "pro"`.

- **Lists (ordenadas, basadas en índice, push/pop)**
   - **Úsalo cuando:** pilas/colas con semántica push/pop.
   - **Evítalo cuando:** necesitas ordenamiento por puntaje o tiempo a través de muchos elementos.
   - **Ejemplo:** `LPUSH queue:emails id123` luego `RPOP`.

- **Sets (membresía única, sin orden)**
   - **Úsalo cuando:** verificaciones de membresía, deduplicación, operaciones de conjuntos.
   - **Evítalo cuando:** necesitas ordenamiento o clasificación.
   - **Ejemplo:** `SADD feature:beta user:42` luego `SISMEMBER`.

- **Sorted sets (zsets: ordenados por puntaje)**
   - **Úsalo cuando:** tablas de clasificación, feeds ordenados por tiempo, consultas top-N.
   - **Evítalo cuando:** necesitas ordenamiento multi-campo (computa un puntaje o usa otro almacén).
   - **Ejemplo:** `ZADD leaderboard 9800 user:42` luego `ZREVRANGE leaderboard 0 9 WITHSCORES`.

- **[Redis Streams](redis-streams.md) (log de solo-adición)**
   - **Úsalo cuando:** procesamiento de eventos duradero con replay.
   - **Evítalo cuando:** solo necesitas notificaciones de tipo dispara-y-olvida.
   - **Ejemplo:** `XADD orders * id 123 status created`.

- **Bitmaps (operaciones de bits sobre strings)**
   - **Úsalo cuando:** flags a gran escala o seguimiento de actividad diaria.
   - **Evítalo cuando:** necesitas sets dispersos con IDs variables (usa sets).
   - **Ejemplo:** `SETBIT active:2026-01-19 123 1`.

- **HyperLogLog (cardinalidad aproximada)**
   - **Úsalo cuando:** conteos únicos a gran escala con poca memoria.
   - **Evítalo cuando:** necesitas conteos exactos.
   - **Ejemplo:** `PFADD uniques page:home user:42` luego `PFCOUNT`.

- **Índices geoespaciales (geo sets)**
   - **Úsalo cuando:** consultas por radio y vecinos más cercanos.
   - **Evítalo cuando:** necesitas consultas espaciales complejas o polígonos.
   - **Ejemplo:** `GEOADD stores -74.0 40.7 store:1` luego `GEORADIUS`.

## Anti-patrones comunes
- **Anti-patrón:** Almacenar un blob de documento completo en un string para actualizaciones frecuentes de campos.
  - **Por qué es malo:** requiere reescritura de valores grandes y desperdicia memoria.
  - **Mejor enfoque:** usar hashes para actualizaciones a nivel de campo.
- **Anti-patrón:** Usar lists para tablas de clasificación.
   - **Por qué es malo:** re-ordenar en cada lectura es costoso y propenso a errores.
   - **Mejor enfoque:** usar sorted sets y mantener el ordenamiento del lado del servidor.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los tipos de datos de Redis son estructuras especializadas (hashes, sets, zsets, lists, etc.) que permiten hacer operaciones atómicas de forma eficiente. Elegir el tipo correcto ahorra memoria y simplifica la lógica.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los hashes de Redis siempre son más eficientes en memoria que strings JSON?
   - **R:** no siempre; depende del conteo de campos y la codificación, pero los hashes permiten actualizaciones de campos sin reescribir todo el valor.
2) **P:** ¿Una list es la mejor opción para una tabla de clasificación?
   - **R:** no; los sorted sets están diseñados para clasificación.
3) **P:** ¿Los sets preservan el orden?
   - **R:** no; usa sorted sets cuando el orden importa.
4) **P:** ¿Los sorted sets mantienen el orden automáticamente después de actualizaciones?
   - **R:** sí; actualizar el puntaje de un miembro lo reordena automáticamente.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo listar los tipos de datos comunes de Redis y sus casos de uso.
- [ ] Puedo elegir entre list vs stream vs zset.
- [ ] Puedo explicar por qué los hashes ayudan con actualizaciones de campos.
- [ ] Puedo nombrar al menos una trampa de memoria.
- [ ] Puedo asociar un tipo de dato con un patrón de acceso.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [Modelado de datos clave-valor](key-value-data-modeling.md)

### Temas relacionados
- [Fundamentos de caché](../system-design/caching-fundamentals.md)
- [Patrones de acceso NoSQL](nosql-access-patterns.md)

### Comparar con
- [Bases de datos de documentos (visión general)](document-databases.md) — esquema flexible y consultas vs estructuras en memoria.

## Notas / Bandeja de entrada (opcional)
- N/A
