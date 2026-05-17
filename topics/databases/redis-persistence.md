---
id: redis-persistence
title: "Persistencia de Redis (RDB/AOF)"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [redis, durability, storage]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Persistencia de Redis (RDB/AOF)

## TL;DR (BLUF)
- La persistencia de Redis controla cómo los datos en memoria se escriben a disco.
- Los snapshots RDB son compactos pero pueden perder escrituras recientes; AOF es más duradero pero más pesado.
- Trade-off: durabilidad vs rendimiento y sobrecarga operativa.

## Definición
**Qué es:** Los mecanismos que Redis usa para persistir datos: RDB (snapshots periódicos) y AOF (log de solo-adición de escrituras).
**Términos clave:** snapshot RDB, AOF, fsync, reescritura, ventana de durabilidad.

## Por qué importa
- Determina cuántos datos puedes perder en un fallo.
- Malas configuraciones pueden causar picos de latencia o reinicios largos.

## Alcance y no-objetivos
**Dentro del alcance:** persistencia por snapshot vs log, comportamiento de recuperación, ventanas de durabilidad.
**Fuera del alcance / NO resuelto por esto:** durabilidad transaccional completa o garantías de consistencia multi-nodo.

## Modelo mental / Intuición
- RDB es una foto periódica; AOF es un diario de cada cambio.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Quieres que los datos de Redis sobrevivan a reinicios.
- Puedes ajustar durabilidad vs rendimiento según la carga de trabajo.
### Evítalo cuando
- Redis es puramente efímero y puede reconstruirse rápidamente.
- Requieres cero pérdida de datos (Redis no está diseñado para eso).

## Cómo lo usaría (práctico)
- **Contexto:** Caché con durabilidad parcial para sesiones.
- **Pasos:** habilitar AOF con `everysec` → agregar snapshots RDB periódicos → probar tiempo de recuperación.
- **Cómo se ve el éxito:** ventana de pérdida de datos aceptable y latencia estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** los datos sobreviven a reinicios; flexibilidad en durabilidad.
- **Desventajas / Riesgos:** E/S de disco extra, escrituras más lentas, pausas largas de reescritura.
### Alternativas
- **Usar Redis solo como caché:** aceptar pérdida de datos.
- **Usar una BD duradero:** cuando los requisitos de durabilidad son estrictos.
- **Cómo elegir:** si perder algunas escrituras recientes es aceptable, ajusta AOF + RDB; de lo contrario mantén Redis como caché.

## Modos de fallo y trampas
- Reescritura de AOF causando picos de latencia o presión de disco.
- Intervalos de snapshot RDB que permiten demasiada pérdida de datos.
- Tiempo de reinicio dominado por archivos AOF enormes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de persistencia, duración de fsync, tamaño de AOF, última hora de guardado.
- **Logs:** errores de guardado en segundo plano, fallos de reescritura de AOF.
- **Alertas:** fallos de guardado repetidos o tiempos de fsync largos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir política de fsync de AOF (`always`, `everysec` o `no`).
  - [ ] Configurar intervalos de snapshot RDB para objetivos de recuperación.
  - [ ] Monitorear crecimiento de AOF y éxito de reescritura.
  - [ ] Probar tiempo de reinicio y recuperación regularmente.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Habilitar AOF `always` en cargas de trabajo sensibles a latencia.
  - **Por qué es malo:** fsync síncrono puede disparar la latencia.
  - **Mejor enfoque:** usar `everysec` o modo solo-caché.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La persistencia de Redis puede hacer snapshot de memoria (RDB) o registrar cada escritura (AOF). RDB es más rápido pero pierde más datos; AOF es más duradero pero más pesado.

### Preguntas trampa (con respuestas)
1) **P:** ¿AOF garantiza cero pérdida de datos?
   - **R:** no; `everysec` puede perder hasta un segundo de datos, y los fallos pueden perder más.
2) **P:** ¿Es RDB suficiente para durabilidad estricta?
   - **R:** no; los snapshots son periódicos y pueden perder escrituras recientes.
3) **P:** ¿La configuración de persistencia puede afectar la latencia?
   - **R:** sí; las operaciones de fsync y reescritura pueden introducir picos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar RDB vs AOF.
- [ ] Puedo elegir una política de fsync.
- [ ] Puedo describir la ventana de pérdida de datos.
- [ ] Puedo nombrar un modo de fallo de persistencia.
- [ ] Puedo monitorear la salud de la persistencia.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Redis](redis.md)
- [Fundamentos de almacenamiento](storage-fundamentals.md)

### Temas relacionados
- [Políticas de desalojo de Redis](redis-eviction-policies.md)
- [Escalado de Redis (vertical y horizontal)](redis-scaling.md)

### Comparar con
- [PostgreSQL](postgresql.md) — RDBMS duradero completo vs persistencia opcional de Redis.

## Notas / Bandeja de entrada (opcional)
- N/A
