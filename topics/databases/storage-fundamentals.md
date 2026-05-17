---
id: storage-fundamentals
title: "Fundamentos de almacenamiento"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [database, storage]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de almacenamiento

## TL;DR (BLUF)
- Los fundamentos de almacenamiento cubren E/S, latencia y trade-offs de espacio.
- Úsalos para razonar sobre rendimiento y mantenimiento de bases de datos.
- Trade-off: mayor rendimiento a menudo cuesta más.

## Definición
**Qué es:** Conceptos básicos de almacenamiento en disco/SSD, patrones de E/S y latencia.
**Términos clave:** E/S, throughput, latencia, fragmentación.

## Por qué importa
- El comportamiento del almacenamiento afecta directamente el rendimiento de la BD.
- Las tareas de mantenimiento como compactación y vacuum dependen del almacenamiento.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de rendimiento de almacenamiento.
**Fuera del alcance / NO resuelto por esto:** adquisición de hardware.

## Modelo mental / Intuición
- El almacenamiento es la parte más lenta del stack; planifica alrededor de ello.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Diagnosticas picos de latencia o problemas de bloat.
### Evítalo cuando
- Los problemas de rendimiento son claramente limitados por CPU.

## Cómo lo usaría (práctico)
- **Contexto:** Consultas lentas después de crecimiento de datos.
- **Pasos:** verificar métricas de E/S → revisar tipo de almacenamiento → ajustar mantenimiento.
- **Cómo se ve el éxito:** E/S y latencia estables.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejor rendimiento con almacenamiento más rápido.
- **Desventajas / Riesgos:** mayor costo.
### Alternativas
- **Caché:** reducir E/S de almacenamiento.
- **Cómo elegir:** optimiza E/S cuando el almacenamiento es el cuello de botella.

## Modos de fallo y trampas
- Ignorar señales de saturación de E/S.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** E/S de disco, profundidad de cola, latencia.
- **Logs:** errores de almacenamiento.
- **Alertas:** saturación de E/S sostenida.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Monitorear E/S y latencia
  - [ ] Planificar mantenimiento alrededor de los límites de E/S

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Ejecutar compactación durante tráfico pico.
  - **Por qué es malo:** saturación de E/S.
  - **Mejor enfoque:** programar ventanas de mantenimiento.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los fundamentos de almacenamiento explican por qué las bases de datos se ralentizan: la latencia y el throughput de E/S son factores limitantes. Entenderlos ayuda a planificar índices y mantenimiento.

### Preguntas trampa (con respuestas)
1) **P:** ¿La CPU siempre es el cuello de botella?
   - **R:** no; la E/S de almacenamiento frecuentemente lo es.
2) **P:** ¿El SSD elimina la necesidad de índices?
   - **R:** no; los índices aún reducen la E/S.
3) **P:** ¿Los problemas de almacenamiento pueden aparecer solo a escala?
   - **R:** sí; la saturación de E/S es común a escala.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir fundamentos de almacenamiento.
- [ ] Puedo nombrar métricas clave.
- [ ] Puedo describir un trade-off.
- [ ] Puedo explicar una trampa.
- [ ] Puedo vincular almacenamiento con rendimiento de BD.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de rendimiento](../system-design/performance-basics.md)

### Temas relacionados
- [Compactación de almacenamiento](storage-compaction.md)

### Comparar con
- [Fundamentos de caché](../system-design/caching-fundamentals.md) — memoria vs almacenamiento.
