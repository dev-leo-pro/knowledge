---
id: event-driven-basics
title: "Arquitectura Dirigida por Eventos"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [architecture, messaging, distributed-systems]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-21
---

# Arquitectura Dirigida por Eventos

## TL;DR (BLUF)
- La arquitectura dirigida por eventos (EDA) publica eventos y deja que los consumidores reaccionen de forma asíncrona.
- Úsala para desacoplar servicios y escalar flujos de trabajo independientemente.
- Trade-off: consistencia eventual, complejidad de ordenamiento/idempotencia y depuración más difícil.

## Definición
**Qué es:** Una arquitectura donde los productores emiten eventos inmutables a un broker y los consumidores reaccionan de forma asíncrona, a menudo construyendo su propio estado local o proyecciones.
**Términos clave:** evento, productor, consumidor, broker, topic/cola, contrato de evento, idempotencia.

## Por qué importa
- Reduce el acoplamiento, permitiendo despliegues independientes y fan-out escalable.
- Traslada los desafíos de corrección al ordenamiento, duplicación y consistencia eventual.

## Alcance y no-objetivos
**Dentro del alcance:** flujos de eventos, contratos, garantías de confiabilidad y trade-offs operacionales.
**Fuera del alcance / NO resuelto por esto:** internos del broker, algoritmos de procesamiento de streams o garantías de exactamente-una-vez.

## Modelo mental / Intuición
- Piensa en una redacción periodística: los editores anuncian hechos, los suscriptores deciden qué hacer.
- Los eventos son hechos sobre cambios de estado; los consumidores derivan su propia vista a partir de ellos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas flujos de trabajo asíncronos con escalado independiente.
- Múltiples sistemas downstream deben reaccionar al mismo cambio.
- Puedes tolerar consistencia eventual y diseñar para idempotencia.
### Evítalo cuando
- Necesitas consistencia estricta e inmediata entre servicios.
- El flujo de trabajo es simple y las llamadas síncronas son suficientes.
- Tu equipo carece de madurez operacional para depuración distribuida.

## Cómo lo usaría (práctico)
- **Contexto:** Un pedido dispara cumplimiento, email, analítica y verificaciones de fraude.
- **Pasos:**
   1) Definir un contrato de evento (esquema versionado).
   2) Publicar `OrderCreated` a un broker.
   3) Los consumidores manejan el evento de forma idempotente y almacenan su propio estado.
   4) Añadir reintentos y una cola de mensajes muertos para mensajes envenenados.
- **Cómo se ve el éxito:** escalado independiente, sin acoplamiento fuerte y visibilidad operacional clara.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** acoplamiento débil, fan-out escalable, mejor resiliencia ante fallos downstream.
- **Desventajas / Riesgos:** consistencia eventual, problemas de ordenamiento y manejo complejo de fallos.
### Alternativas
- **[Arquitectura request-response](request-response.md):** semántica más simple para flujos de trabajo síncronos y estrechos.
- **[ETL por lotes](../operations/batch-etl.md):** mejor para movimiento de datos periódico y a gran escala.
- **Cómo elegir:** usa EDA cuando necesites reacciones asíncronas y evolución desacoplada.

## Modos de fallo y trampas
- Eventos duplicados y procesamiento fuera de orden sin idempotencia.
- Deriva de esquema entre productores y consumidores.
- Mensajes envenenados causando bucles de reintento infinitos.
- Acumulaciones silenciosas por consumidores sub-aprovisionados.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** retraso del consumidor, throughput, tasa de reintentos, profundidad de DLQ, latencia de procesamiento.
- **Logs:** ID de evento, ID de correlación, versión del esquema, razón del fallo.
- **Trazas:** spans de productor → broker → consumidor con brechas de tiempo.
- **Alertas:** crecimiento sostenido del retraso, picos en DLQ o altas tasas de reintentos.

## Notas de implementación (si aplica)
- **Lista de verificación**
   - [ ] Definir contratos de eventos versionados
   - [ ] Hacer los consumidores idempotentes
   - [ ] Usar IDs de correlación para trazas
   - [ ] Planificar reintentos y colas de mensajes muertos
- **Notas de seguridad / cumplimiento**
   - Evitar poner datos sensibles en eventos a menos que sea necesario y estén protegidos.
- **Notas de rendimiento**
   - Preferir eventos pequeños e inmutables; evitar payloads grandes.
- **Notas operacionales**
   - Monitorear retraso por grupo de consumidores y establecer SLOs.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar eventos como RPC.
   - **Por qué es malo:** añade latencia y debilita las garantías de consistencia.
   - **Mejor enfoque:** usar [Arquitectura request-response](request-response.md) para bucles estrechos.
- **Anti-patrón:** Emitir filas internas de BD como eventos sin contrato.
   - **Por qué es malo:** consumidores frágiles y acoplamiento accidental.
   - **Mejor enfoque:** publicar eventos de dominio explícitos con un esquema estable.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La arquitectura dirigida por eventos publica hechos sobre cambios como eventos, y consumidores independientes reaccionan de forma asíncrona. Escala el fan-out y reduce el acoplamiento, pero requiere idempotencia, estrategias de ordenamiento y observabilidad fuerte.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los eventos tienen orden global?
   - **R:** Normalmente no; el ordenamiento es típicamente por partición o por clave.
2) **P:** ¿La arquitectura dirigida por eventos garantiza procesamiento exactamente-una-vez?
   - **R:** No; al-menos-una-vez es lo común, así que los consumidores deben ser idempotentes.
3) **P:** ¿EDA es lo mismo que event sourcing?
   - **R:** No; EDA es un estilo de comunicación, event sourcing es un patrón de persistencia.

### Auto-verificación rápida (5 ítems)
- [x] Puedo definir arquitectura dirigida por eventos.
- [x] Puedo indicar cuándo usarla.
- [x] Puedo nombrar un trade-off.
- [x] Puedo describir una trampa.
- [x] Puedo explicar las señales de observabilidad.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de mensajería](messaging-basics.md)
- [Fundamentos de sistemas distribuidos](../system-design/distributed-systems-basics.md)

### Temas relacionados
- [Kafka](kafka.md)
- [Patrón Outbox](outbox-pattern.md)
- [Patrón Dual-write](dual-write-pattern.md)
- [Change Data Capture (CDC)](../databases/change-data-capture.md)

### Comparar con
- [Arquitectura request-response](request-response.md) -- acoplamiento síncrono vs reacciones asíncronas.
