---
id: retries-and-backoff
title: "Reintentos, backoff exponencial y jitter"
type: concept
status: learning
importance: 80
difficulty: medium
tags: [reliability, integrations, networking]
canonical: true
aliases: []
created_at: 2026-01-22
updated_at: 2026-01-22
---

# Reintentos, backoff exponencial y jitter

## TL;DR (BLUF)
- Los reintentos son una herramienta de fiabilidad, pero los reintentos no controlados causan interrupciones.
- Usa **reintentos acotados** con **backoff exponencial** y **jitter**.
- Solo reintenta cuando la operación es segura (idempotente) y el error es transitorio.

## Definición
**Qué es:** Una estrategia para re-intentar operaciones fallidas con retrasos que crecen con el tiempo.
**Términos clave:** backoff exponencial, jitter, [Presupuestos de reintento](retry-budgets.md), error transitorio, tormenta de reintentos.

## Por qué importa
- Los fallos transitorios (parpadeos de red, sobrecarga) son normales; los reintentos pueden recuperarlos.
- Los reintentos mal hechos crean estampidas y [Fallo en cascada](cascading-failure.md).

## Alcance y no-objetivos
**Dentro del alcance:** reintentos del lado del cliente para HTTP y procesamiento de mensajes.
**Fuera del alcance / NO resuelto por esto:** proteger una dependencia sobrecargada (ver [Circuit breaker](circuit-breaker.md)).

## Modelo mental / Intuición
- "El reintento es carga." El backoff controla cuánta carga extra agregas mientras un sistema no está sano.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Usa reintentos cuando
- El error es probablemente transitorio (timeouts, 429/503, resets de conexión).
- La operación es idempotente (ver [Idempotencia](idempotency.md)).

### Evita reintentos cuando
- El error es permanente (400/validación, fallos de autenticación, incompatibilidad de esquema).
- Reintentar amplificaría el daño (captura de pago sin idempotencia).

## Cómo lo usaría (práctico)
- **Contexto:** Llamar a una API de terceros.
- **Pasos:**
  1) Establecer un timeout estricto por intento.
  2) Reintentar 2-4 veces con backoff exponencial + jitter.
  3) Detenerse pronto si la dependencia está claramente caída (circuit breaker).
- **Cómo se ve el éxito:** los problemas transitorios se recuperan; los incidentes no se convierten en tormentas de reintentos.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mayor tasa de éxito bajo fallos transitorios.
- **Desventajas / Riesgos:** mayor latencia de cola, carga extra, trabajo duplicado si no es idempotente.

### Alternativas
- **Cola + procesamiento asíncrono:** desacoplar la latencia del usuario de los fallos transitorios (ver [Arquitectura dirigida por eventos](../architecture/event-driven-basics.md)).
- **Fallo rápido + degradación elegante:** cuando la funcionalidad parcial es aceptable.
- **Cómo elegir:** los flujos síncronos de usuario a menudo necesitan presupuestos estrictos; los flujos asíncronos pueden reintentar más tiempo con DLQ.

## Modos de fallo y trampas
- Reintentar sin timeouts (las solicitudes se acumulan).
- Reintentar todos los errores uniformemente (los errores permanentes desperdician capacidad).
- Reintentos coordinados sin jitter (picos de reintentos).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** conteo de reintentos por llamada, % de éxito-después-de-reintento, latencia p95/p99, tasa de 429/503.
- **Logs:** incluir número de intento, retraso de backoff, clase de error.
- **Trazas:** mostrar tramos de reintento y presupuesto de tiempo total.
- **Alertas:** pico de reintentos + pico de errores + señales de saturación.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir clases de error reintentables
  - [ ] Acotar reintentos y presupuesto de tiempo total
  - [ ] Agregar jitter
  - [ ] Asegurar idempotencia o claves de idempotencia
  - [ ] Agregar circuit breaker para fallos sostenidos

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** "Reintenta para siempre hasta que funcione."
  - **Por qué es malo:** crea colas infinitas e interrupciones.
  - **Mejor enfoque:** reintentos acotados + DLQ + remediación humana/automatizada.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los reintentos deben estar acotados y espaciados con backoff+jitter; solo reintentas errores transitorios y solo cuando la operación es segura.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es el backoff exponencial siempre mejor que un retraso constante?
   - **R:** Usualmente para evitar tormentas de reintentos, pero aún necesitas ajustarte a un presupuesto de tiempo.
2) **P:** ¿Los reintentos reemplazan la necesidad de circuit breakers?
   - **R:** No; los reintentos manejan fallos transitorios, los circuit breakers protegen durante fallos sostenidos.
3) **P:** ¿Por qué agregar jitter?
   - **R:** Para evitar reintentos sincronizados entre muchos clientes.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo clasificar errores transitorios vs permanentes.
- [ ] Puedo diseñar reintentos con un presupuesto de tiempo.
- [ ] Puedo explicar jitter.
- [ ] Puedo vincular reintentos a idempotencia.
- [ ] Puedo proponer monitoreo para tormentas de reintentos.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Timeouts](timeouts.md)
- [Idempotencia](idempotency.md)

### Temas relacionados
- [Circuit breaker](circuit-breaker.md)
- [DLQ y mensajes venenosos](dlq-and-poison-messages.md)
- [HTTP](http.md)
- [Presupuestos de reintento](retry-budgets.md)
- [Cobertura de solicitudes](request-hedging.md)

### Comparar con
- [Circuit breaker](circuit-breaker.md) — los reintentos con backoff aún llaman a la dependencia; un breaker deja de llamarla.

## Notas / Bandeja de entrada (opcional)
- N/A
