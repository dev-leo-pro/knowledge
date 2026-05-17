---
id: caching-strategy
title: "Caching Strategy"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [system-design, caching]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Estrategia de Caché

## TL;DR (BLUF)
- Una estrategia define dónde, qué y cómo cachear.
- Úsala para balancear latencia, frescura y costo.
- Trade-off: más capas de caché agregan complejidad.

## Definición
**Qué es:** El plan para capas de caché, TTLs, invalidación y propiedad.
**Términos clave:** cache-aside, write-through, invalidación.

## Por qué importa
- Una mala estrategia lleva a datos obsoletos o caché desperdiciado.
- Una buena estrategia reduce la carga de BD y la latencia.

## Alcance y no-objetivos
**Dentro del alcance:** elecciones estratégicas para caché.
**Fuera del alcance / NO resuelto por esto:** detalles de implementación de caché a bajo nivel.

## Modelo mental / Intuición
- Cachear es una decisión de producto: ¿qué puede estar obsoleto?

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Múltiples servicios dependan de los mismos datos.
### Evítalo cuando
- La corrección requiera datos estrictamente frescos.

## Cómo lo usaría (práctico)
- **Contexto:** Catálogo de productos con lectura intensiva.
- **Pasos:** elegir cache-aside → establecer TTLs → invalidar en actualizaciones.
- **Cómo se ve el éxito:** carga reducida en BD con obsolescencia aceptable.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** menor latencia y carga.
- **Contras / Riesgos:** datos obsoletos y complejidad de invalidación.
### Alternativas
- **Desnormalización:** precomputar modelos de lectura.
- **Cómo elegir:** cachear cuando la carga de lectura domine y la obsolescencia sea aceptable.

## Modos de fallo y errores comunes
- Estampidas de caché e invalidación inconsistente.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de aciertos, reportes de lecturas obsoletas.
- **Logs:** cache misses.
- **Alertas:** caídas de tasa de aciertos o picos de datos obsoletos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir requisitos de frescura
  - [ ] Elegir patrón de caché
  - [ ] Monitorear tasa de aciertos

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Cachear todo sin medir.
  - **Por qué es malo:** complejidad sin beneficio.
  - **Mejor enfoque:** cachear solo rutas calientes.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Una estrategia de caché decide qué cachear, por cuánto tiempo y cómo invalidar. Es un balance entre frescura y rendimiento.

### Preguntas trampa (con respuestas)
1) **P:** ¿Debes siempre cachear en cada capa?
   - **R:** No; cada capa agrega complejidad.
2) **P:** ¿Puedes ignorar la invalidación si el TTL es corto?
   - **R:** No para datos donde la corrección es crítica.
3) **P:** ¿La tasa de aciertos del caché es la única métrica?
   - **R:** No; los datos obsoletos y las penalizaciones por miss importan.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir estrategia de caché.
- [ ] Puedo indicar trade-offs.
- [ ] Puedo nombrar un error común.
- [ ] Puedo describir una señal de monitoreo.
- [ ] Puedo comparar con desnormalización.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de caché](caching-fundamentals.md)

### Temas relacionados
- [TTL (Time-to-Live)](../databases/ttl.md)

### Comparar con
- [Desnormalización](../databases/denormalization.md) — precomputar vs cachear.
