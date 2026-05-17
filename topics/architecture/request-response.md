---
id: request-response
title: "Request-Response Architecture"
type: concept
status: learning
importance: 40
difficulty: easy
tags: [architecture, communication]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Arquitectura Request-Response

## TL;DR (BLUF)
- Request-response es comunicación síncrona entre servicios.
- Úsala para respuestas inmediatas de baja latencia.
- Trade-off: acoplamiento más estrecho y mayor impacto de fallos.

## Definición
**Qué es:** Un patrón síncrono donde un cliente envía una petición y espera una respuesta.
**Términos clave:** síncrono, timeout, reintento.

## Por qué importa
- Es simple y fácil de razonar.
- Puede propagar fallos y aumentar la latencia.

## Alcance y no-objetivos
**Dentro del alcance:** trade-offs de comunicación síncrona.
**Fuera del alcance / NO resuelto por esto:** patrones de mensajería asíncrona.

## Modelo mental / Intuición
- Como una llamada telefónica: esperas la respuesta.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites resultados inmediatos.
### Evítalo cuando
- El trabajo pueda ser asíncrono.

## Cómo lo usaría (práctico)
- **Contexto:** Consulta de perfil de usuario.
- **Pasos:** petición → respuesta → manejar timeouts.
- **Cómo se ve el éxito:** baja latencia con manejo de errores claro.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** simplicidad y respuesta inmediata.
- **Contras / Riesgos:** acoplamiento y [fallo en cascada](../operations/cascading-failure.md).
### Alternativas
- **Mensajería:** procesamiento asíncrono.
- **Cómo elegir:** usa request-response para requisitos estrictos de latencia.

## Modos de fallo y errores comunes
- [Fallo en cascada](../operations/cascading-failure.md) debido a dependencias síncronas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia, tasa de errores.
- **Logs:** errores de timeout.
- **Alertas:** latencia p95 elevada.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Configurar timeouts
  - [ ] Usar reintentos con backoff

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Cadenas largas de llamadas síncronas.
  - **Por qué es malo:** alta latencia y propagación de fallos.
  - **Mejor enfoque:** usar asíncrono donde sea posible.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Request-response es un patrón síncrono donde el cliente espera una respuesta. Es simple pero puede aumentar el acoplamiento y la latencia.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los reintentos siempre son seguros?
   - **R:** No; pueden amplificar la carga y duplicar trabajo.
2) **P:** ¿Request-response puede ser completamente fiable?
   - **R:** Depende de los timeouts y el manejo de errores.
3) **P:** ¿Lo asíncrono siempre es mejor?
   - **R:** No; algunos casos de uso necesitan respuestas inmediatas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir request-response.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo explicar señales de observabilidad.

## Enlaces (SIN duplicación)
### Prerrequisitos
- N/A

### Temas relacionados
- [Fundamentos de mensajería](messaging-basics.md)

### Comparar con
- [Fundamentos de arquitectura dirigida por eventos](event-driven-basics.md) — asíncrono vs síncrono.
