---
id: caching-fundamentals
title: "Caching Fundamentals"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [system-design, caching]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Caché

## TL;DR (BLUF)
- El caché almacena datos computados o recuperados para reducir latencia y carga.
- Úsalo cuando las lecturas sean frecuentes y los datos relativamente estables.
- Trade-off: invalidación de caché y riesgo de obsolescencia.

## Definición
**Qué es:** Una técnica para almacenar y reutilizar datos para acelerar las respuestas.
**Términos clave:** cache-aside, TTL, invalidación.

## Por qué importa
- Mejora la latencia y reduce la carga en la base de datos.
- Una invalidación incorrecta causa bugs de datos obsoletos.

## Alcance y no-objetivos
**Dentro del alcance:** fundamentos de caché y trade-offs.
**Fuera del alcance / NO resuelto por esto:** patrones completos de arquitectura de caché.

## Modelo mental / Intuición
- El caché es un atajo; debes mantenerlo razonablemente fresco.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Las cargas de trabajo de lectura intensiva dominen.
### Evítalo cuando
- Los datos cambien frecuentemente y la corrección sea crítica.

## Cómo lo usaría (práctico)
- **Contexto:** Endpoint de API de lectura intensiva.
- **Pasos:** usar cache-aside → establecer TTL → monitorear tasa de aciertos.
- **Cómo se ve el éxito:** mayor tasa de aciertos y menor carga en BD.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** menor latencia, carga reducida en BD.
- **Contras / Riesgos:** datos obsoletos, complejidad de invalidación.
### Alternativas
- **Desnormalización:** precomputar lecturas.
- **Cómo elegir:** cachear cuando la carga de lectura sea alta y tolere ligera obsolescencia.

## Modos de fallo y errores comunes
- Estampidas de caché en expiración.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de aciertos, conteo de desalojos, latencia.
- **Logs:** picos de cache miss.
- **Alertas:** caída súbita de la tasa de aciertos.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir patrón de caché
  - [ ] Establecer TTLs
  - [ ] Monitorear tasa de aciertos

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Cachear sin estrategia de invalidación.
  - **Por qué es malo:** datos obsoletos.
  - **Mejor enfoque:** definir TTL y reglas de invalidación.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El caché mantiene datos recientes o costosos de computar cerca de la aplicación. Acelera las lecturas pero introduce desafíos de obsolescencia e invalidación.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cachear siempre es seguro?
   - **R:** No; puedes servir datos obsoletos.
2) **P:** ¿TTL puede reemplazar la invalidación?
   - **R:** A veces, pero no para corrección estricta.
3) **P:** ¿Los cache miss siempre son malos?
   - **R:** No; son esperados y deben manejarse.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir caché.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de rendimiento](performance-basics.md)

### Temas relacionados
- [TTL (Time-to-Live)](../databases/ttl.md)

### Comparar con
- [Estrategia de caché](caching-strategy.md) — patrones y elecciones.
