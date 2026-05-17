---
id: strangler-fig
title: "Strangler Fig Pattern"
type: pattern
status: learning
importance: 75
difficulty: hard
tags: [architecture, migration, legacy, refactoring]
canonical: true
aliases: [strangler pattern, incremental migration]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Patrón Strangler Fig

## TL;DR (BLUF)
- Migra incrementalmente un sistema heredado redirigiendo tráfico a nuevos servicios mientras el sistema antiguo sigue funcionando.
- Úsalo para evitar reescrituras "big bang" arriesgadas de monolitos o sistemas heredados.
- Trade-off: período largo de migración con mayor complejidad gestionando ambos sistemas.

## Definición
**Qué es:** Una estrategia de migración donde gradualmente reemplazas partes de un sistema heredado interceptando peticiones (vía proxy/gateway), enrutando algunas a nuevos servicios y otras al sistema antiguo, hasta que el sistema antiguo es completamente "estrangulado" y puede ser decomisionado.

**Términos clave:** migración incremental, modernización de sistemas heredados, capa proxy, enrutamiento, ejecución paralela, reescritura big bang (anti-patrón), desarrollo brownfield.

## Por qué importa
- Reduce el riesgo de reescrituras fallidas (evita el "síndrome del segundo sistema").
- Permite entrega continua: migra una funcionalidad a la vez.
- Mantiene el negocio funcionando durante la migración (sin tiempo de inactividad).
- Proporciona ruta de rollback (enrutar de vuelta al sistema heredado si el nuevo servicio falla).

## Alcance y no-objetivos
**Dentro del alcance:** estrategias de enrutamiento, configuración de proxy/gateway, migración de datos, manejo de escritura dual, planificación del cutover.

**Fuera del alcance / NO resuelto por esto:**
- Consistencia de datos durante la migración (ver [Escritura dual](dual-write-pattern.md))
- Modelado de dominio (ver [Diseño Dirigido por Dominio](domain-driven-design.md))
- Cronograma de decomisionamiento del sistema heredado

## Modelo mental / Intuición
- Nombrado por el árbol strangler fig: crece alrededor del árbol huésped, eventualmente reemplazándolo.
- En sistemas: los nuevos microservicios "envuelven" al monolito, gradualmente tomando funcionalidad.
- Como renovar una casa habitación por habitación mientras vives en ella.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El sistema heredado sea grande y arriesgado de reescribir de una vez.
- El negocio no pueda permitirse tiempo de inactividad o cutover big bang.
- Necesites entregar valor incrementalmente (nuevas funcionalidades en nuevos servicios).
- El equipo no entienda completamente el sistema heredado (descubrir mientras se migra).
- El sistema heredado tenga calidad mixta (migrar partes valiosas, descartar lo innecesario).

### Evítalo cuando
- El sistema heredado sea lo bastante pequeño para reescribirlo rápido (< 6 meses).
- El sistema heredado sea estable y de bajo mantenimiento (sin caso de negocio para migrar).
- El equipo sea demasiado pequeño para gestionar sistemas duales.
- La complejidad de migración de datos sea prohibitiva.

## Cómo lo usaría (práctico)
- **Contexto:** Migrando una app de e-commerce monolítica a microservicios.
- **Pasos:**
  1) **Configurar proxy/gateway:** Desplegar [API Gateway](../system-design/api-gateway.md) delante del monolito.
     ```
     Cliente → API Gateway → (decisión de ruta) → Nuevos Servicios O Monolito Heredado
     ```
  2) **Identificar el slice de migración:** Empezar con una funcionalidad de bajo riesgo y alto valor (ej., catálogo de productos).
  3) **Construir nuevo servicio:** Crear `product-service` (microservicio).
  4) **Escritura dual (si es necesario):** Escribir tanto en la BD heredada como en la BD del nuevo servicio durante la transición.
     ```go
     // Temporalmente escribir en ambos
     legacyDB.SaveProduct(product)
     newProductService.CreateProduct(product)
     ```
  5) **Enrutar lecturas al nuevo servicio:** Configurar gateway:
     ```
     GET /products/* → product-service (nuevo)
     POST /orders/* → monolito (heredado)
     ```
  6) **Monitorear y validar:** Comparar respuestas (modo shadow), rastrear tasas de error.
  7) **Cutover:** Cambiar escrituras al nuevo servicio; deprecar código heredado.
  8) **Repetir:** Migrar siguiente slice (pedidos, usuarios, pagos).
  9) **Decomisionar:** Cuando todas las funcionalidades estén migradas, apagar el monolito.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Migración larga (meses/años) | Establecer cronograma realista; entregar valor incrementalmente; evitar perfeccionismo |
| Complejidad de escritura dual | Usar [Change Data Capture](../databases/change-data-capture.md) o sincronización dirigida por eventos |
| Problemas de consistencia de datos | Aceptar consistencia eventual; usar [Saga](saga-pattern.md) para flujos críticos |
| Sobrecarga operacional (dos sistemas) | Automatizar despliegue; usar feature flags para alternar enrutamiento |
| Riesgo de "strangler que nunca termina" | Establecer plazos; rastrear progreso de migración; celebrar hitos |

## Modos de fallo / Casos límite
1. **Desvío de escritura dual:** Las BDs del sistema heredado y nuevo divergen.
   - *Mitigación:* Usar [patrón Outbox](outbox-pattern.md) o CDC; [reconciliar](../operations/data-reconciliation.md) periódicamente.
2. **La complejidad de enrutamiento explota:** Cientos de reglas if-else en el gateway.
   - *Mitigación:* Usar enrutamiento declarativo (basado en configuración); refactorizar incrementalmente.
3. **Dependencias ocultas:** El nuevo servicio llama al heredado, creando acoplamiento estrecho.
   - *Mitigación:* Usar [Capa Anti-Corrupción](anti-corruption-layer.md) para aislar.
4. **La migración se estanca:** El equipo pierde impulso después del primer slice.
   - *Mitigación:* Dedicar recursos; rastrear progreso públicamente; celebrar victorias.
5. **El sistema heredado se convierte en zombie:** Nunca se decomisiona completamente.
   - *Mitigación:* Establecer plazo firme; presupuestar la limpieza final; medir uso del sistema heredado.

## Alternativas
- **Reescritura big bang:** Reemplazar todo el sistema de una vez (alto riesgo).
- **Congelamiento de funcionalidades + reescritura:** Parar nuevas funcionalidades, reescribir, luego cutover (impacto al negocio).
- **Ejecución paralela:** Ejecutar heredado + nuevo en paralelo, cambiar después de validación (complejo).
- **Lift and shift:** Migrar a la nube sin refactorizar (no moderniza).

## Fases
### Fase 1: Preparación
- Desplegar proxy/gateway.
- Identificar primer slice de migración.
- Establecer monitoreo y observabilidad.

### Fase 2: Construir Nuevo Servicio
- Desarrollar microservicio.
- Migrar esquema de datos (si es necesario).
- Probar exhaustivamente (unitarias, integración, contrato).

### Fase 3: Escritura Dual (si es necesario)
- Escribir en ambos sistemas temporalmente.
- Validar consistencia.

### Fase 4: Enrutar Tráfico
- Modo shadow: enrutar lecturas al nuevo, comparar con heredado.
- Cutover: enrutar tráfico de producción al nuevo servicio.
- Plan de rollback listo.

### Fase 5: Decomisionamiento
- Detener escrituras duales.
- Eliminar código heredado.
- Migrar siguiente slice.

## Combinaciones
Strangler Fig se usa **casi siempre con:**
- **[API Gateway](../system-design/api-gateway.md):** Enruta tráfico a nuevo vs heredado.
- **[Capa Anti-Corrupción](anti-corruption-layer.md):** Protege nuevos servicios de modelos heredados.
- **[Change Data Capture](../databases/change-data-capture.md):** Sincroniza datos entre sistemas.
- **[Observabilidad](../operations/observability-basics.md):** Monitorea ambos sistemas durante la migración.
- **[Feature flags](../quality/software-quality-assurance.md):** Alterna enrutamiento dinámicamente.

**Combinación típica:**
- **Escenario de migración heredada:** Strangler Fig + API Gateway + ACL + Observabilidad + Feature Flags

## Prerrequisitos
- Comprensión de [diseño de microservicios](microservices-design-basics.md).
- Familiaridad con [API Gateway](../system-design/api-gateway.md) y enrutamiento.
- Conocimiento de [migración de datos](../databases/data-modeling-basics.md) y consistencia.

## Temas relacionados
- [API Gateway](../system-design/api-gateway.md): Capa de enrutamiento para strangler.
- [Capa Anti-Corrupción](anti-corruption-layer.md): Aislar nuevos servicios del heredado.
- [Patrón de escritura dual](dual-write-pattern.md): Sincronización temporal durante migración.
- [Change Data Capture](../databases/change-data-capture.md): Alternativa a escritura dual.
- [Diseño de microservicios](microservices-design-basics.md): Arquitectura objetivo.

## Ejemplos del mundo real
1. **Shopify:** Migró monolito Rails a microservicios durante 5+ años usando strangler.
2. **SoundCloud:** Usó strangler para extraer servicios de usuario/reproducción del monolito.
3. **Artículo original de Martin Fowler (2004):** Acuñó el término basado en un proyecto real.

## Checklist (auto-evaluación)
- [ ] Puedo explicar por qué las reescrituras big bang son arriesgadas.
- [ ] Sé cómo configurar un proxy/gateway de enrutamiento.
- [ ] Entiendo los desafíos de escritura dual y sus alternativas.
- [ ] Puedo planificar migración en slices (priorizar por valor/riesgo).
- [ ] Puedo monitorear tanto servicios heredados como nuevos durante la migración.

## Recordatorios / Puntos clave
- **Incremental > Big Bang:** Reducir riesgo, entregar valor continuamente.
- Usar [API Gateway](../system-design/api-gateway.md) para enrutamiento.
- Usar [ACL](anti-corruption-layer.md) para proteger nuevos servicios de modelos heredados.
- Planificar migración de datos cuidadosamente (escritura dual, CDC, [reconciliación](../operations/data-reconciliation.md)).
- Establecer **plazo de migración** para evitar zombie heredado.

## Preguntas trampa (con respuestas)
### P1: ¿Debo migrar todo el esquema de base de datos de una vez?
**R:** **No**. Migra datos **incrementalmente** por slice de servicio. Usa:
1. **Escritura dual:** Escribir temporalmente en BD heredada + nueva.
2. **[Change Data Capture](../databases/change-data-capture.md):** Transmitir cambios de BD heredada a nueva.
3. **Migración por lotes:** Copiar datos históricos offline; sincronizar deltas.
Evita "una gran migración" — demasiado arriesgado. Acepta consistencia eventual durante la transición.

### P2: ¿Cuánto debería durar una migración strangler?
**R:** **Depende del tamaño del monolito y la capacidad del equipo**. Típico: 1-3 años para monolitos grandes. Directrices:
- Establecer **cronograma realista** (no "6 meses" para un monolito de 10 años).
- Entregar valor incrementalmente (nuevas funcionalidades en nuevos servicios).
- Rastrear progreso (ej., "60% del tráfico en nuevos servicios").
- Establecer **plazo firme** para el cutover final (evitar zombie heredado).

### P3: ¿Qué pasa si el nuevo servicio falla durante la migración?
**R:** **Tener plan de rollback**. Con strangler, puedes:
1. Enrutar tráfico **de vuelta al heredado** instantáneamente (alternar enrutamiento del gateway).
2. Ejecutar ambos sistemas en paralelo (fallback al heredado).
3. Monitorear tasas de error y hacer rollback automáticamente (circuit breaker).
Esta es la ventaja clave de strangler: **rollback de bajo riesgo** vs big bang (sin rollback).

### P4: ¿Puedo agregar nuevas funcionalidades al sistema heredado durante la migración?
**R:** **Generalmente evítalo**. Agregar funcionalidades al heredado:
- Aumenta el esfuerzo de migración (más para migrar después).
- Acopla al equipo al stack tecnológico heredado.
**Mejor:** Construir nuevas funcionalidades en **nuevos microservicios** desde el día 1. Usar proxy strangler para enrutar nuevos endpoints a nuevos servicios. Esto entrega valor mientras se migra.

### P5: ¿Cómo manejo datos compartidos (ej., tabla de usuarios usada por múltiples funcionalidades)?
**R:** **Opciones:**
1. **[Base de datos por servicio](database-per-service.md):** Extraer user-service, poseer tabla de usuarios, exponer vía API.
2. **BD compartida temporalmente:** Permitir que nuevos servicios lean BD heredada (vía ACL) durante migración; migrar después.
3. **Sincronización dirigida por eventos:** El heredado publica eventos de usuario; nuevos servicios consumen y construyen vista local.
Elegir según tolerancia al acoplamiento. Idealmente, extraer datos compartidos en servicio dedicado tempranamente. Ver [Contextos Acotados](bounded-contexts.md).
