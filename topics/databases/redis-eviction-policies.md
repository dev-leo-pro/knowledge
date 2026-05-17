---
id: redis-eviction-policies
title: "Políticas de desalojo de Redis"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [redis, cache, memory]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Políticas de desalojo de Redis

## TL;DR (BLUF)
- Las políticas de desalojo deciden qué claves elimina Redis cuando la memoria está llena.
- Elige una política que coincida con tu estrategia de caché y uso de TTL.
- Trade-off: predecibilidad vs tasa de aciertos vs disponibilidad de escritura.

## Definición
**Qué es:** La configuración que le dice a Redis cómo liberar memoria cuando se alcanza `maxmemory` (ej., LRU, LFU, random, basado en TTL o sin desalojo).
**Términos clave:** `maxmemory`, desalojo, `allkeys-*`, `volatile-*`, LRU, LFU, noeviction.

## Por qué importa
- La política incorrecta puede causar fallos de caché en cascada o errores de escritura.
- Afecta directamente la latencia, tasa de aciertos y estabilidad operativa.

## Alcance y no-objetivos
**Dentro del alcance:** comportamiento bajo presión de memoria, supervivencia del caché y semántica de escritura.
**Fuera del alcance / NO resuelto por esto:** durabilidad de persistencia, modelado de datos o estrategia de invalidación de caché.

## Modelo mental / Intuición
- Un portero en un club lleno que decide a quién expulsar para dejar entrar nuevos invitados.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Ejecutas Redis con memoria limitada y necesitas comportamiento predecible al límite de capacidad.
- Quieres sesgar el desalojo hacia datos obsoletos o de bajo valor.
### Evítalo cuando
- Esperas que Redis se comporte como un sistema de registro duradero.
- Dependes de que las escrituras siempre tengan éxito (usa `noeviction` solo si tu app puede manejar fallos de escritura).

## Cómo lo usaría (práctico)
- **Contexto:** Cache-aside para respuestas de API con TTLs.
- **Pasos:** configurar `maxmemory` → elegir política → validar tasa de aciertos bajo carga → alertar sobre desalojos.
- **Cómo se ve el éxito:** desalojos solo bajo picos de carga y tasa de aciertos estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** uso controlado de memoria y comportamiento predecible bajo presión.
- **Desventajas / Riesgos:** los desalojos pueden eliminar claves calientes o causar fallos de escritura.
### Alternativas
- **Escalar verticalmente:** agregar memoria para reducir la presión de desalojo.
- **Redis Cluster:** fragmentar para distribuir memoria y claves calientes.
- **Cómo elegir:** si no puedes tolerar desalojos, escala verticalmente o fragmenta; de lo contrario elige una política que coincida con el uso de TTL.

## Modos de fallo y trampas
- `noeviction` causando errores de escritura y fallos downstream.
- Políticas `volatile-*` con TTLs faltantes (causa retención inesperada de claves).
- LRU/LFU en cargas de trabajo mixtas con valores enormes (desalojar claves calientes pequeñas perjudica la tasa de aciertos).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** `used_memory`, `maxmemory`, `evicted_keys`, `keyspace_hits/misses`, latencia p95.
- **Logs:** entradas de slowlog alrededor de picos de desalojo.
- **Alertas:** desalojos sostenidos o caídas repentinas de tasa de aciertos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Configurar `maxmemory` explícitamente (evitar uso ilimitado por defecto).
  - [ ] Elegir `allkeys-lru` o `allkeys-lfu` para casos de uso de caché puro.
  - [ ] Usar `volatile-ttl` solo si la mayoría de claves tienen TTLs.
  - [ ] Probar con carga con tamaños de clave y patrones de acceso realistas.
- **Notas operativas**
  - Los desalojos son normales bajo presión; desalojos sostenidos significan desajuste de capacidad.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar `volatile-*` sin TTLs en la mayoría de claves.
  - **Por qué es malo:** Redis no desalojará muchas claves, y la presión de memoria persiste.
  - **Mejor enfoque:** configurar TTLs consistentemente o usar `allkeys-*`.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las políticas de desalojo definen qué claves elimina Redis cuando la memoria está llena. Elige una política que coincida con tu estrategia de caché y uso de TTL para mantener tasas de aciertos predecibles.

### Preguntas trampa (con respuestas)
1) **P:** ¿`noeviction` significa que Redis es duradero?
   - **R:** no; solo detiene los desalojos y fallará las escrituras en su lugar.
2) **P:** ¿`volatile-lru` es seguro si olvidas los TTLs?
   - **R:** no; las claves sin TTLs no serán desalojadas y pueden causar presión de memoria.
3) **P:** ¿LRU/LFU siempre mantienen las claves más calientes?
   - **R:** no perfectamente; son aproximaciones y pueden ser sesgadas por valores grandes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir qué hacen las políticas de desalojo en Redis.
- [ ] Puedo elegir entre `allkeys-*` y `volatile-*`.
- [ ] Puedo explicar por qué `noeviction` puede romper escrituras.
- [ ] Puedo nombrar al menos 2 políticas de desalojo.
- [ ] Puedo describir cómo monitorear desalojos.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [TTL (Time-to-Live)](ttl.md)
- [Fundamentos de caché](../system-design/caching-fundamentals.md)

### Temas relacionados
- [Estrategia de caché](../system-design/caching-strategy.md)
- [Particiones calientes](hot-partitions.md)

### Comparar con
- [Particionamiento y sharding](../system-design/partitioning-and-sharding.md) — resuelve capacidad distribuyendo datos, no desalojándolos.

## Notas / Bandeja de entrada (opcional)
- N/A
