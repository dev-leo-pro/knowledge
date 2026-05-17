---
id: storage-compaction
title: "Compactación de almacenamiento"
type: concept
status: learning
importance: 45
difficulty: medium
tags: [database, storage]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Compactación de almacenamiento

## TL;DR (BLUF)
- La compactación reclama espacio y reorganiza el almacenamiento.
- Úsala para controlar el bloat y mejorar el rendimiento de lectura.
- Trade-off: la compactación es intensiva en recursos.

## Definición
**Qué es:** Un proceso que reescribe datos para reducir la fragmentación y reclamar espacio.
**Términos clave:** compactación, fragmentación, reescritura.

## Por qué importa
- Estabiliza el uso de almacenamiento y el rendimiento de lectura.
- Puede consumir E/S y afectar la latencia.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos y trade-offs de compactación.
**Fuera del alcance / NO resuelto por esto:** internos de compactación específicos de cada base de datos.

## Modelo mental / Intuición
- Piensa en compactar un disco para eliminar espacios vacíos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El almacenamiento crece a pesar de un tamaño de datos estable.
### Evítalo cuando
- Puedes tolerar el bloat y quieres mantenimiento mínimo.

## Cómo lo usaría (práctico)
- **Contexto:** Tabla grande con actualizaciones pesadas.
- **Pasos:** programar compactación → monitorear E/S → verificar reducción de tamaño.
- **Cómo se ve el éxito:** tamaño de almacenamiento reducido y latencia estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** espacio reclamado y mejor localidad.
- **Desventajas / Riesgos:** picos de E/S y ralentización de escrituras.
### Alternativas
- **Vacuum/autovacuum:** limpieza más ligera en Postgres.
- **Cómo elegir:** usa compactación cuando la limpieza más ligera no es suficiente.

## Modos de fallo y trampas
- Compactación durante tráfico pico causando picos de latencia.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** E/S de disco, latencia, tamaño de tabla.
- **Logs:** logs de actividad de compactación.
- **Alertas:** picos de E/S sostenidos durante la compactación.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Programar durante ventanas de bajo tráfico
  - [ ] Monitorear impacto de E/S

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Compactación durante tráfico pico.
  - **Por qué es malo:** causa picos de latencia.
  - **Mejor enfoque:** usar ventanas de mantenimiento.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La compactación reescribe datos para reclamar espacio y mejorar la localidad de lectura. Es útil pero costosa, así que prográmala con cuidado.

### Preguntas trampa (con respuestas)
1) **P:** ¿La compactación siempre mejora el rendimiento?
   - **R:** no siempre; puede reducir el rendimiento durante el proceso.
2) **P:** ¿La compactación es lo mismo que vacuum?
   - **R:** no; vacuum es más ligero y a menudo no reescribe todos los datos.
3) **P:** ¿La compactación puede ser continua?
   - **R:** algunos sistemas lo hacen, pero aún consume recursos.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir compactación.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar cuándo usarla.
- [ ] Puedo conectarla con ventanas de mantenimiento.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de almacenamiento](storage-fundamentals.md)

### Temas relacionados
- [Bloat en PostgreSQL](postgresql-bloat.md)

### Comparar con
- [PostgreSQL vacuum y autovacuum](postgresql-vacuum-autovacuum.md) — limpieza más ligera vs compactación.
