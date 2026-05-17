---
id: software-quality-assurance
title: "Aseguramiento de Calidad de Software (SQA)"
type: concept
status: learning
importance: 90
difficulty: medium
tags: [quality, testing, reliability, engineering-practices]
canonical: true
aliases: [SQA, quality-assurance]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Aseguramiento de Calidad de Software (SQA)

## TL;DR (BLUF)
- El Aseguramiento de Calidad de Software es un proceso sistemático para asegurar que el software cumpla con los requisitos funcionales y estándares de calidad durante todo el desarrollo y operación.
- Usa múltiples dimensiones de calidad: calidad de código, pruebas, calidad operacional, arquitectura y proceso.
- Trade-off clave: inversión en calidad por adelantado vs. costo de fallos después.

## Definición
**Qué es:** Un conjunto sistemático de prácticas, procesos y herramientas para prevenir defectos, asegurar corrección y mantener confiabilidad a lo largo del ciclo de vida del desarrollo de software.  
**Términos clave:** compuertas de calidad, prevención de defectos, cobertura de pruebas, análisis estático, [Indicador de Nivel de Servicio (SLI)](../operations/service-level-indicator.md), [Objetivo de Nivel de Servicio (SLO)](../operations/service-level-objective.md), runbook, definición de terminado.

## Por qué importa
- **Previene incidentes en producción:** Detectar defectos temprano es 10-100x más barato que corregirlos en producción.
- **Permite entrega rápida:** La alta calidad reduce el retrabajo, haciendo a los equipos más rápidos con el tiempo.
- **Construye confianza:** Los sistemas confiables permiten a los ingenieros moverse con confianza y a los usuarios depender del producto.
- **Relevancia en entrevistas:** Los ingenieros senior deben articular estrategias de calidad y trade-offs.

## Alcance y no-objetivos
**Dentro del alcance:**
- Calidad multidimensional: código, pruebas, operaciones, arquitectura, proceso.
- Prácticas preventivas (compuertas, revisiones, automatización).
- Señales de calidad medibles ([Indicadores de Nivel de Servicio (SLIs)](../operations/service-level-indicator.md), [Presupuestos de error](../operations/error-budgets.md), métricas).

**Fuera del alcance / NO resuelto por esto:**
- Product-market fit o priorización de funcionalidades (calidad ≠ "funcionalidades correctas").
- Cero defectos (imposible; gestionar tasas de fallo aceptables con [Objetivos de Nivel de Servicio (SLOs)](../operations/service-level-objective.md)).

## Modelo mental / Intuición
Piensa en la calidad como **defensa en profundidad**:
- **Capa 1 (Prevención):** Revisiones de código, linters, sistemas de tipos.
- **Capa 2 (Detección):** Pruebas unitarias/integración/E2E detectan problemas antes del despliegue.
- **Capa 3 (Mitigación):** Feature flags, despliegues canario, circuit breakers limitan el radio de impacto.
- **Capa 4 (Respuesta):** Observabilidad, alertas, runbooks permiten recuperación rápida.

Ninguna capa individual es perfecta; la calidad viene de la combinación.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Construyes sistemas donde los fallos tienen un costo real (impacto al usuario, pérdida de datos, ingresos).
- Trabajas en equipos (las prácticas de calidad permiten colaboración y confianza).
- Operas sistemas de larga vida (la deuda técnica se acumula sin inversión en calidad).

### Evítalo cuando
- Prototipar código desechable para aprendizaje/validación (la calidad es excesiva para experimentos).
- La inversión en calidad excede el valor del sistema (no sobre-ingenierizar un proyecto de fin de semana).

## Cómo lo usaría (práctico)
- **Contexto:** Construyendo un nuevo servicio API en un entorno de equipo.
- **Pasos:**
  1. Definir compuertas de calidad: linters pasan, pruebas >80% cobertura, revisión de PR requerida, CI verde.
  2. Implementar pirámide de pruebas: muchas pruebas unitarias, menos pruebas de integración, pruebas E2E críticas.
  3. Agregar observabilidad: logs estructurados, métricas (latencia p95/p99, tasa de error), alertas en violaciones de SLO.
  4. Establecer runbooks para modos de fallo comunes (timeout de BD, límite de tasa excedido).
  5. Usar despliegues canario: 5% del tráfico por 10 minutos antes del despliegue completo.
- **Cómo se ve el éxito:** Cero incidentes visibles para el usuario en el primer mes; latencia P95 <200ms; tasa de error <0.1%.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Reduce incidentes en producción y su costo.
  - Aumenta la velocidad del equipo con el tiempo (menos retrabajo, refactorización más segura).
  - Construye confianza para moverse rápido.
- **Desventajas / Riesgos:**
  - Costo inicial: escribir pruebas, revisiones de código, configuración de CI toma tiempo.
  - Puede ralentizar la entrega inicial si se aplica en exceso (100% de cobertura en código desechable).
  - Requiere disciplina y cambio cultural (no solo herramientas).

### Alternativas
- **"Entregar rápido, corregir errores después":** Funciona para MVPs tempranos pero crea deuda técnica que se acumula.
- **Solo QA manual:** No escala; lento y pierde casos extremos.
- **Depender solo del monitoreo:** Encuentras defectos en producción en lugar de prevenirlos.

### Cómo elegir
- **Alto riesgo + sistema de larga vida:** Invertir en SQA completo.
- **MVP temprano / prototipo:** Calidad ligera (pruebas básicas + monitoreo).
- **El tamaño del equipo importa:** Equipos más grandes necesitan compuertas de calidad más fuertes (más riesgo de coordinación).

## Modos de fallo y trampas
- **Métricas vanidosas:** 90% de cobertura de pruebas no significa nada si las pruebas no verifican comportamiento o cubren modos de fallo.
- **Demasiadas compuertas:** CI demasiado estricto que bloquea cada cambio pequeño mata la velocidad.
- **Sin responsabilidad:** La calidad es "trabajo del equipo de QA" en lugar de responsabilidad de todos.
- **Probar lo incorrecto:** Pruebas E2E que son inestables y lentas en lugar de pruebas unitarias rápidas y confiables.
- **Ignorar calidad operacional:** Código perfecto que se cae bajo carga o no tiene observabilidad.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tiempo de entrega de cambios (cuánto tiempo de commit a producción).
  - Frecuencia de despliegue (mayor es mejor con compuertas de calidad).
  - Tasa de fallo de cambios (% de despliegues que causan incidentes).
  - Tiempo medio de recuperación (MTTR).
- **Logs:** Rastrear fuentes de defectos (¿detectado en CI, staging, producción?).
- **Trazas:** N/A para SQA en sí (pero crítico para calidad operacional).
- **Alertas:** Fallos de CI, caída de cobertura de pruebas por debajo del umbral, pico en tasa de fallo de cambios.

## Notas de implementación
- **Lista de verificación**
  - [ ] Definir "definición de terminado" (qué significa calidad para tu equipo).
  - [ ] Configurar CI con compuertas de calidad (linters, pruebas, escaneos de seguridad).
  - [ ] Implementar pirámide de pruebas (muchas unitarias, menos integración, E2E críticas).
  - [ ] Agregar observabilidad a sistemas de producción (logs, métricas, trazas).
  - [ ] Establecer [Indicadores de Nivel de Servicio (SLIs)](../operations/service-level-indicator.md), [Objetivos de Nivel de Servicio (SLOs)](../operations/service-level-objective.md) y [Presupuestos de error](../operations/error-budgets.md).
  - [ ] Crear runbooks para los 3 principales modos de fallo.
  - [ ] Requerir revisiones de código con lista de verificación.
  - [ ] Usar despliegues canario o blue/green para mitigación de riesgo.

- **Notas de seguridad / cumplimiento**
  - Incluir escaneos de seguridad en CI (SAST, verificación de dependencias).
  - Rastrear defectos de seguridad por separado (correcciones críticas no esperan sprints).

- **Notas de rendimiento**
  - Agregar pruebas de rendimiento para rutas críticas (pruebas de carga bajo tráfico esperado + pico).
  - Monitorear latencia P95/P99, no solo promedios.

- **Notas operacionales**
  - La calidad no termina en el despliegue: monitorear, alertar e iterar basándose en el comportamiento de producción.
  - Post-mortems sin culpa después de incidentes mejoran el sistema.

## Mini ejemplo
```yaml
# Example CI quality gate (.github/workflows/quality.yml)
name: Quality Gates
on: [pull_request]
jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Lint
        run: golangci-lint run
      - name: Unit tests
        run: go test ./... -race -coverprofile=coverage.out
      - name: Coverage check
        run: |
          coverage=$(go tool cover -func=coverage.out | grep total | awk '{print $3}' | sed 's/%//')
          if (( $(echo "$coverage < 80" | bc -l) )); then
            echo "Coverage $coverage% is below 80%"
            exit 1
          fi
      - name: Security scan
        run: gosec ./...
```

## Anti-patrones comunes
- **Anti-patrón:** "Agregaremos pruebas después" (nunca se agregan; la deuda técnica crece).
  - **Por qué es malo:** La refactorización se vuelve riesgosa; los defectos se filtran a producción.
  - **Mejor enfoque:** Escribir pruebas mientras codificas (TDD o probar poco después del desarrollo).

- **Anti-patrón:** Mandato de 100% de cobertura de código.
  - **Por qué es malo:** Incentiva pruebas sin sentido que no validan comportamiento.
  - **Mejor enfoque:** Enfocar cobertura en rutas críticas y modos de fallo; aceptar menor cobertura en getters/setters simples.

- **Anti-patrón:** Sin compuertas de calidad en CI ("confiar en que los desarrolladores ejecuten pruebas localmente").
  - **Por qué es malo:** Los humanos olvidan; código roto llega a la rama main.
  - **Mejor enfoque:** Automatizar compuertas en CI (linting, pruebas, escaneos de seguridad) que bloqueen el merge.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
El Aseguramiento de Calidad de Software es un enfoque multicapa para prevenir defectos y asegurar confiabilidad. Combina calidad de código (revisiones, linters, pruebas), calidad operacional (observabilidad, SLOs, runbooks) y disciplina de proceso (compuertas de CI, despliegues canario). El objetivo no es cero defectos—es detectar problemas temprano y limitar el radio de impacto cuando ocurren fallos. La calidad es una inversión: cuesta tiempo por adelantado pero se paga en entrega más rápida y menos incidentes en producción.

### Preguntas trampa (con respuestas)
1) **P:** ¿La alta calidad no ralentiza la entrega?
   - **R:** Inicialmente sí, pero la calidad acelera la entrega con el tiempo. Sin pruebas y revisiones, los equipos acumulan deuda técnica que requiere apagar incendios constantemente y cambios riesgosos. La calidad permite refactorización segura e iteración con confianza.

2) **P:** Si tenemos 90% de cobertura de pruebas, ¿nuestra calidad es alta?
   - **R:** No necesariamente. La cobertura es una señal de lo que *no* está probado, pero no mide la calidad de las pruebas. Puedes tener 90% de cobertura con pruebas que no verifican comportamiento o cubren modos de fallo. Enfocarse en probar rutas críticas y casos extremos, no en alcanzar un número de cobertura.

3) **P:** ¿Deberíamos corregir todos los errores antes de entregar?
  - **R:** No. Usar priorización basada en riesgo: errores críticos (pérdida de datos, seguridad) bloquean lanzamientos; errores menores (problemas cosméticos) pueden entregarse con trade-offs conocidos. Definir tu estándar de calidad con SLOs—no apuntar a cero defectos, apuntar a tasas de fallo aceptables.

4) **P:** ¿Quién es responsable de la calidad—los desarrolladores o QA?
   - **R:** Todos. Los desarrolladores escriben pruebas y son responsables de la calidad del código; QA (si existe) diseña estrategias de pruebas y detecta brechas. La calidad es responsabilidad del equipo, no una transferencia.

5) **P:** ¿Cómo equilibras velocidad y calidad?
   - **R:** Usar trade-offs basados en riesgo. Para prototipos, menor calidad está bien (sin pruebas, revisiones mínimas). Para sistemas de producción, invertir en compuertas de calidad pero mantenerlas ágiles (pruebas rápidas, listas de verificación claras para revisiones). Usar feature flags y despliegues canario para entregar rápido con radio de impacto limitado.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir aseguramiento de calidad en múltiples dimensiones (código, pruebas, operaciones, proceso).
- [ ] Puedo explicar por qué la calidad acelera la entrega a largo plazo a pesar del costo inicial.
- [ ] Puedo nombrar al menos 3 compuertas de calidad y su propósito.
- [ ] Puedo articular la diferencia entre cobertura de pruebas como métrica vs. calidad.
- [ ] Puedo describir un modo de fallo (ej., pruebas inestables, sin observabilidad) y cómo prevenirlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de confiabilidad](../operations/reliability-basics.md)
- [Fundamentos de observabilidad](../operations/observability-basics.md)
- [Fundamentos de pruebas](testing-fundamentals.md)

### Temas relacionados
- [Pirámide de pruebas](testing-pyramid.md)
- [Calidad de código](code-quality.md)
- [Compuertas de calidad](quality-gates.md)
- [Código limpio](clean-code.md)
- [Fundamentos de gestión de incidentes](../operations/incident-management-basics.md)

### Comparar con
- [Fundamentos de confiabilidad](../operations/reliability-basics.md) — La confiabilidad es el resultado; SQA es el proceso para lograrlo.

## Notas / Bandeja de entrada (opcional)
- Considerar agregar ejemplos de incidentes reales (fuga de leads, bucle de failover de Heimdall) para ilustrar brechas de calidad.
- Enlazar a métricas DORA (tiempo de entrega, frecuencia de despliegue, tasa de fallo de cambios, MTTR) cuando ese tema sea creado.
