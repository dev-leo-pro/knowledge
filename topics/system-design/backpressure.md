---
id: backpressure
title: "Backpressure"
type: concept
status: learning
importance: 70
difficulty: medium
tags: [reliability, performance, concurrency, messaging]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Contrapresión (Backpressure)

## TL;DR (BLUF)
- La contrapresión limita el trabajo entrante cuando la capacidad downstream está saturada para mantener el sistema estable.
- Úsala con [Timeouts](../operations/timeouts.md), [Reintentos](../operations/retries-and-backoff.md) y [Circuit breaker](../operations/circuit-breaker.md).
- Trade-off: puedes rechazar, retrasar o degradar peticiones para proteger el throughput y la latencia.

## Definición
**Qué es:** Un mecanismo de control de flujo que intencionalmente ralentiza, almacena en buffer o rechaza trabajo cuando los consumidores están sobrecargados, para que el sistema se mantenga dentro de los límites de capacidad seguros.
**Términos clave:** concurrencia acotada, profundidad de cola, [descarte de carga](../operations/load-shedding.md), degradación elegante, lag de consumidores.

## Por qué importa
- Previene [fallos en cascada](../operations/cascading-failure.md) cuando los productores upstream superan la capacidad downstream.
- Convierte un "colapso lento" en rechazo controlado o servicio degradado.

## Alcance y no-objetivos
**Dentro del alcance:** limitar concurrencia, encolamiento, descarte y degradación para mantener la latencia y las tasas de error acotadas.
**Fuera del alcance / NO resuelto por esto:** corrección bajo duplicados (ver [Idempotencia](../operations/idempotency.md)), o entrega garantizada (ver [Fundamentos de mensajería](../architecture/messaging-basics.md)).

## Modelo mental / Intuición
- Piensa en un restaurante: si la cocina está sobrecargada, dejas de sentar mesas nuevas, ofreces un menú reducido o agregas una lista de espera.
- Tu objetivo es *estabilidad primero*, no máxima aceptación a cualquier costo.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tengas tráfico en ráfagas o capacidad desigual entre productor/consumidor.
- Los recursos downstream se saturen (pools de BD, CPU, APIs externas).
- Necesites proteger SLOs de latencia y presupuestos de error.

### Evítalo cuando
- La carga de trabajo sea trivial y la capacidad esté muy por encima del pico.
- No puedas rechazar o retrasar trabajo (raro; aún así prefiere buffering asíncrono).

## Cómo lo usaría (práctico)
- **Contexto:** Una API dispara un pipeline asíncrono que llama a 3 servicios externos.
- **Pasos:**
  1) Establecer concurrencia acotada por grupo de workers.
  2) Limitar la profundidad de cola y rechazar/redirigir carga excesiva.
  3) Degradar respuestas (ej., resultados parciales) cuando esté saturado.
  4) Agregar [presupuestos de reintento](../operations/retry-budgets.md) explícitos para evitar amplificación de carga.
- **Cómo se ve el éxito:** latencia p95 estable y lag de cola acotado bajo tráfico pico.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** protege la salud del sistema, mejora la latencia de cola bajo carga, evita fallos en cascada.
- **Contras / Riesgos:** trabajo rechazado o retrasado, degradación visible para el usuario, complejidad de ajuste fino.
### Alternativas
- **Escalar horizontalmente:** agregar capacidad si la demanda es consistentemente alta.
- **Desacoplamiento asíncrono:** sacar trabajo de la ruta crítica con colas.
- **Cómo elegir:** si la sobrecarga es transitoria o en picos, la contrapresión es más barata que escalar.

## Modos de fallo y errores comunes
- Colas no acotadas → picos de latencia y presión de memoria.
- Contrapresión solo en el borde → servicios internos aún se sobrecargan entre sí.
- Reintentar sin presupuestos → amplifica la sobrecarga en vez de aliviarla.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** profundidad de cola, lag de consumidores, utilización de workers, CPU, latencia p95/p99, peticiones rechazadas.
- **Logs:** eventos de contrapresión (razón, umbral, carga actual).
- **Trazas:** tiempo esperando en colas vs procesando.
- **Alertas:** profundidad de cola > umbral, saturación sostenida, tasa de rechazo > X%.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir límites seguros de concurrencia y cola por dependencia
  - [ ] Decidir rechazo vs retraso vs degradación
  - [ ] Imponer [presupuestos de reintento](../operations/retry-budgets.md) para evitar amplificación
  - [ ] Agregar aislamiento por tenant o por ruta
- **Go (práctico)**
  - Usar pools de goroutines acotados, semáforos o canales con buffer.
  - Propagar cancelación con `context.Context` para detener trabajo desperdiciado.
- **Consumidores Kafka (práctico)**
  - Ajustar `max.poll.records` y usar `pause()`/`resume()` para coincidir con la capacidad de procesamiento.
  - Mantener el tiempo de procesamiento por debajo de `max.poll.interval.ms` para evitar rebalanceos.
- **Consumidores SQS (práctico)**
  - Limitar mensajes en vuelo y ajustar tamaño de lote por capacidad de worker.
  - Establecer visibility timeout para exceder el peor caso de tiempo de procesamiento.
- **Notas de seguridad / cumplimiento**
  - Asegurar que las respuestas degradadas no filtren datos restringidos.
- **Notas de rendimiento**
  - Preferir colas acotadas sobre buffers no acotados.
- **Notas operacionales**
  - Documentar playbook de sobrecarga y umbrales.

## Mini ejemplo (si aplica)
```go
// Pool de workers acotado con contrapresión vía canal con buffer.
const maxWorkers = 8
const maxQueue = 100

jobs := make(chan Job, maxQueue)

for i := 0; i < maxWorkers; i++ {
	go func() {
		for job := range jobs {
			process(job)
		}
	}()
}

func Enqueue(job Job) error {
	select {
	case jobs <- job:
		return nil
	default:
		return errors.New("cola llena") // señal de contrapresión
	}
}
```

## Anti-patrones comunes
- **Anti-patrón:** "Solo agrega reintentos cuando esté lento."
  - **Por qué es malo:** los reintentos aumentan la carga y empeoran la sobrecarga.
  - **Mejor enfoque:** acotar la concurrencia y rechazar o diferir trabajo.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La contrapresión es una estrategia de control de flujo: cuando los consumidores están saturados, ralentizas, almacenas en buffer o rechazas productores para mantener el sistema dentro de la capacidad segura y prevenir fallos en cascada.

### Preguntas trampa (con respuestas)
1) **P:** ¿Una cola más grande siempre es mejor para la contrapresión?
   - **R:** No. Las colas no acotadas ocultan la sobrecarga y explotan la latencia de cola; las colas acotadas fuerzan trade-offs explícitos.
2) **P:** ¿La contrapresión puede reemplazar los timeouts?
   - **R:** No. La contrapresión protege la capacidad; los timeouts acotan cuánto tiempo el trabajo puede consumir recursos.
3) **P:** ¿Contrapresión significa "descartar todas las peticiones" bajo carga?
   - **R:** No necesariamente. Puedes rechazar selectivamente, retrasar o degradar dependiendo del SLA.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir contrapresión con precisión en 2–3 oraciones.
- [ ] Puedo indicar cuándo usarla y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de sistemas distribuidos](distributed-systems-basics.md)
- [Fundamentos de rendimiento](performance-basics.md)
- [Fundamentos de observabilidad](../operations/observability-basics.md)

### Temas relacionados
- [Fundamentos de mensajería](../architecture/messaging-basics.md)
- [Fundamentos de arquitectura dirigida por eventos](../architecture/event-driven-basics.md)
- [Timeouts](../operations/timeouts.md)
- [Reintentos, backoff exponencial y jitter](../operations/retries-and-backoff.md)
- [Circuit breaker](../operations/circuit-breaker.md)
- [Descarte de carga](../operations/load-shedding.md)
- [Bulkheads](../operations/bulkheads.md)
- [Kafka](../architecture/kafka.md)
- (TODO) SQS/SNS
- (TODO) Limitación de tasa

### Comparar con
- [Circuit breaker](../operations/circuit-breaker.md) — el breaker bloquea llamadas a una dependencia fallando; la contrapresión limita la carga general.
- [Reintentos, backoff exponencial y jitter](../operations/retries-and-backoff.md) — los reintentos agregan carga; la contrapresión la limita.
- [Descarte de carga](../operations/load-shedding.md) — el descarte elimina/degrada trabajo; la contrapresión lo ralentiza o encola.

## Notas / Bandeja de entrada (opcional)
- N/A
