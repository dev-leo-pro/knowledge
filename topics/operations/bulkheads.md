---
id: bulkheads
title: "Bulkheads (Mamparos)"
type: pattern
status: learning
importance: 70
difficulty: medium
tags: [reliability, isolation, concurrency]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Bulkheads (Mamparos)

## TL;DR (BLUF)
- Los bulkheads aíslan recursos para que un fallo en un componente no hunda todo el sistema.
- Úsalos para prevenir "vecinos ruidosos" y [Fallos en cascada](cascading-failure.md).
- Trade-off: fragmentación de capacidad y mayor complejidad operativa.

## Definición
**Qué es:** Un patrón de resiliencia que particiona recursos (hilos, pools, colas o servicios) para contener fallos dentro de un dominio de fallo acotado.  
**Términos clave:** aislamiento, pools particionados, dominio de fallo, vecino ruidoso, saturación.

## Por qué importa
- Previene que una dependencia o inquilino agote recursos compartidos.
- Mantiene las rutas críticas sanas incluso cuando las rutas no críticas fallan.

## Alcance y no-objetivos
**Dentro del alcance:** pools por dependencia, cuotas por inquilino y grupos de workers aislados.  
**Fuera del alcance / NO resuelto por esto:** recuperación de causa raíz (ver [Circuit breaker](circuit-breaker.md)) o rechazo por sobrecarga (ver [Load shedding](load-shedding.md)).

## Modelo mental / Intuición
- Como compartimentos en un barco: una inundación en un compartimento no hunde toda la embarcación.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tienes múltiples dependencias con diferentes perfiles de riesgo.
- Los sistemas multi-inquilino sufren de vecinos ruidosos.
- Los pools compartidos (hilos/conexiones de BD) son una fuente frecuente de incidentes.

### Evítalo cuando
- El sistema es pequeño y los recursos compartidos no son un cuello de botella.
- No puedes permitirte la fragmentación de capacidad inactiva.

## Cómo lo usaría (práctico)
- **Contexto:** Un servicio llama a dos APIs externas: una fiable, otra inestable.
- **Pasos:**
  1) Separar pools de workers y límites de conexión por dependencia.
  2) Añadir límites de concurrencia por inquilino.
  3) Monitorear saturación por pool y ajustar límites.
- **Cómo se ve el éxito:** los fallos en la dependencia inestable no afectan a la fiable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** aislamiento fuerte, radio de explosión reducido.
- **Desventajas / Riesgos:** capacidad inactiva, más ajuste y posible infrautilización.
### Alternativas
- **Contrapresión:** limitar la carga general cuando la capacidad es ajustada.
- **Load shedding:** rechazar o degradar trabajo de baja prioridad.
- **Cómo elegir:** si el principal riesgo es el impacto cruzado entre componentes, elegir bulkheads.

## Modos de fallo y trampas
- Dimensionamiento pobre -> un pool se queda sin recursos mientras otro está infrautilizado.
- Sin aislamiento por inquilino -> un solo inquilino sigue dominando un pool.
- Sobre-particionamiento -> overhead operativo con beneficio mínimo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** utilización por pool, profundidad de cola por pool, tasa de rechazo por pool.
- **Logs:** eventos de saturación del pool con identificadores de inquilino/dependencia.
- **Trazas:** mostrar qué pool usó una petición y dónde esperó.
- **Alertas:** saturación del pool > umbral, picos de dominancia por inquilino.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar dependencias e inquilinos críticos
  - [ ] Particionar pools (hilos, colas, conexiones)
  - [ ] Establecer límites por pool y umbrales de alerta
  - [ ] Validar aislamiento en pruebas de carga
- **Notas de seguridad / cumplimiento**
  - Asegurar que el aislamiento de inquilinos no filtre datos en fallbacks compartidos
- **Notas de rendimiento**
  - Medir utilización para evitar desperdiciar capacidad
- **Notas operativas**
  - Documentar lógica de dimensionamiento del pool y playbooks de re-ajuste

## Mini ejemplo (si aplica)
```text
critical_pool = 50 connections
noncritical_pool = 10 connections
# los endpoints críticos nunca compiten con los no críticos
```

## Anti-patrones comunes
- **Anti-patrón:** "Un pool gigante para todo."
  - **Por qué es malo:** un vecino ruidoso puede agotar la capacidad compartida.
  - **Mejor enfoque:** separar pools por dependencia o prioridad.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los bulkheads aíslan capacidad para que un componente que falla o es ruidoso no pueda tumbar el resto del sistema.

### Preguntas trampa (con respuestas)
1) **P:** ¿Eliminan los bulkheads la necesidad de circuit breakers?
   - **R:** No. Los bulkheads limitan el radio de explosión; los breakers detienen llamadas a dependencias no saludables.
2) **P:** ¿Son los bulkheads solo para microservicios?
   - **R:** No. Puedes aplicarlos dentro de un monolito con límites por pool.
3) **P:** ¿Es siempre mejor un pool por ruta de petición?
   - **R:** No. El sobre-particionamiento desperdicia capacidad y añade overhead.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir bulkheads con precisión.
- [ ] Puedo indicar cuándo usarlos y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de sistemas distribuidos](../system-design/distributed-systems-basics.md)
- [Fundamentos de rendimiento](../system-design/performance-basics.md)
- [Fundamentos de observabilidad](observability-basics.md)

### Temas relacionados
- [Circuit breaker](circuit-breaker.md)
- [Contrapresión](../system-design/backpressure.md)
- [Load shedding](load-shedding.md)
- [Timeouts](timeouts.md)
- [Concurrencia adaptativa](adaptive-concurrency.md)

### Comparar con
- [Circuit breaker](circuit-breaker.md) — el breaker detiene llamadas no saludables; los bulkheads aíslan capacidad.
- [Contrapresión](../system-design/backpressure.md) — la contrapresión limita carga general; los bulkheads aíslan por componente.

## Notas / Bandeja de entrada (opcional)
- N/A
