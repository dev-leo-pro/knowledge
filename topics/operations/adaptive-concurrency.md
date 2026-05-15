---
id: adaptive-concurrency
title: "Concurrencia Adaptativa"
type: pattern
status: learning
importance: 65
difficulty: medium
tags: [reliability, performance, concurrency]
canonical: true
aliases: []
created_at: 2026-01-23
updated_at: 2026-01-23
---

# Concurrencia Adaptativa

## TL;DR (BLUF)
- La concurrencia adaptativa ajusta los límites de peticiones en vuelo basándose en la latencia observada, en lugar de usar límites fijos.
- Úsala cuando los límites estáticos de bulkhead/pool de hilos son difíciles de ajustar.
- Trade-off: puede oscilar si las señales son ruidosas o retrasadas.

## Definición
**Qué es:** Un enfoque de control por retroalimentación que aumenta o reduce dinámicamente la concurrencia basándose en señales como RTT/latencia para mantener la carga dentro de un rango operativo seguro.  
**Términos clave:** bucle de retroalimentación, RTT, límite de concurrencia, saturación, estabilidad.

## Por qué importa
- Los límites estáticos pueden infrautilizar la capacidad o sobrecargar una dependencia degradada.
- El control adaptativo previene la sobrecarga autoinfligida durante fallos parciales.

## Alcance y no-objetivos
**Dentro del alcance:** control de concurrencia del lado del cliente para dependencias HTTP/RPC/DB.  
**Fuera del alcance / NO resuelto por esto:** políticas de reintentos (ver [Presupuestos de reintentos](retry-budgets.md)) o detección de salud de dependencias (ver [Circuit breaker](circuit-breaker.md)).

## Modelo mental / Intuición
- "Sentir la presión": cuando la latencia sube, retroceder; cuando la latencia baja, aumentar con cautela.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- No puedes establecer de forma fiable un límite de concurrencia estático seguro.
- La latencia es un buen indicador de saturación.

### Evítalo cuando
- Las señales de latencia son ruidosas o dominadas por factores no relacionados.
- Necesitas límites fijos estrictos por cumplimiento normativo o cuotas.

## Cómo lo usaría (práctico)
- **Contexto:** Un servicio llama a una dependencia con capacidad variable a lo largo del tiempo.
- **Pasos:**
  1) Rastrear RTT/latencia con ventana móvil (ej., p95).
  2) Aumentar la concurrencia cuando la latencia está por debajo del objetivo; disminuir cuando está por encima.
  3) Aplicar límites mínimos/máximos para evitar desbordamientos.
- **Cómo se ve el éxito:** latencia estable con alta utilización y menos picos de sobrecarga.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejor utilización, autoprotección ante degradación.
- **Desventajas / Riesgos:** oscilaciones, complejidad de ajuste, sensibilidad a señales ruidosas.
### Alternativas
- **Bulkheads estáticos:** pools fijos por dependencia o inquilino.
- **Limitación de tasa:** limitar la entrada independientemente de la latencia.
- **Cómo elegir:** si la capacidad cambia frecuentemente, preferir adaptativo; de lo contrario, usar límites estáticos.

## Modos de fallo y trampas
- Bucle de retroalimentación demasiado agresivo -> oscilaciones.
- Picos de RTT por causas no relacionadas -> limitación innecesaria.
- Falta de límites máximos -> concurrencia desbocada.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** límite de concurrencia a lo largo del tiempo, RTT p95, saturación, tasa de errores.
- **Logs:** ajustes de límites con motivo.
- **Trazas:** correlación de picos de latencia con cambios de límites.
- **Alertas:** oscilaciones sostenidas o límite fijo en mín/máx.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir ventana de latencia objetivo (ej., p95)
  - [ ] Elegir tamaños de paso de ajuste
  - [ ] Aplicar límites mín/máx de concurrencia
  - [ ] Usar suavizado para reducir ruido
- **Notas de seguridad / cumplimiento**
  - N/A
- **Notas de rendimiento**
  - Usar mediciones de latencia de bajo overhead
- **Notas operativas**
  - Documentar parámetros de ajuste y procedimiento de rollback

## Mini ejemplo (si aplica)
```text
if rtt_p95 > target: limit = max(min_limit, limit - step)
else: limit = min(max_limit, limit + step)
```

## Anti-patrones comunes
- **Anti-patrón:** "Sin límites, dejar que aprenda para siempre."
  - **Por qué es malo:** puede sobrepasar y sobrecargar dependencias.
  - **Mejor enfoque:** aplicar límites mín/máx y amortiguación.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- La concurrencia adaptativa cambia el número de peticiones en vuelo basándose en señales de latencia, para que el sistema se autoproteja sin ajuste estático.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es la concurrencia adaptativa un reemplazo de los bulkheads?
   - **R:** No. Puede complementar los bulkheads pero aún necesita aislamiento rígido para vecinos ruidosos.
2) **P:** ¿Elimina la necesidad de timeouts?
   - **R:** No. Los timeouts siguen siendo necesarios por cada intento.
3) **P:** ¿Puede usar tasa de errores en lugar de latencia?
   - **R:** Puede, pero la latencia suele ser una señal de saturación más rápida.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir la concurrencia adaptativa con precisión.
- [ ] Puedo explicar por qué la latencia es la señal de control.
- [ ] Puedo nombrar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de rendimiento](../system-design/performance-basics.md)
- [Fundamentos de observabilidad](observability-basics.md)

### Temas relacionados
- [Bulkheads](bulkheads.md)
- [Contrapresión](../system-design/backpressure.md)
- [Presupuestos de reintentos](retry-budgets.md)
- [Circuit breaker](circuit-breaker.md)

### Comparar con
- [Bulkheads](bulkheads.md) — los bulkheads aíslan capacidad; la concurrencia adaptativa ajusta capacidad.
- [Contrapresión](../system-design/backpressure.md) — la contrapresión limita carga; la concurrencia adaptativa ajusta límites.

## Notas / Bandeja de entrada (opcional)
- N/A
