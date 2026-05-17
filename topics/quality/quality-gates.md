---
id: quality-gates
title: "Compuertas de Calidad (CI/CD)"
type: pattern
status: learning
importance: 80
difficulty: medium
tags: [quality, ci-cd, automation, deployment]
canonical: true
aliases: [ci-cd-gates, build-gates]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Compuertas de Calidad (CI/CD)

## TL;DR (BLUF)
- Las compuertas de calidad son verificaciones automatizadas en pipelines de CI/CD que previenen que código de baja calidad llegue a producción.
- Usa compuertas para linting, pruebas, escaneos de seguridad y umbrales de cobertura.
- Trade-off clave: prevenir defectos vs. velocidad del pipeline y fricción del desarrollador.

## Definición
**Qué es:** Puntos de verificación automatizados en un pipeline de CI/CD que validan la calidad, seguridad y corrección del código antes de permitir que se fusione o despliegue.  
**Términos clave:** CI (Integración Continua), CD (Despliegue/Entrega Continua), linter, análisis estático, umbral de cobertura, escaneo de seguridad (SAST/DAST), prueba de humo.

## Por qué importa
- **Previene defectos:** Detecta problemas antes de que lleguen a producción (errores, vulnerabilidades de seguridad, violaciones de estilo).
- **Impone estándares:** Asegura que todo el código cumpla un estándar mínimo de calidad sin depender de la memoria humana.
- **Permite entrega rápida:** Los equipos entregan con confianza porque las compuertas detectan regresiones automáticamente.
- **Relevancia en entrevistas:** CI/CD y automatización de calidad son temas estándar en discusiones de diseño de sistemas y DevOps.

## Alcance y no-objetivos
**Dentro del alcance:**
- Compuertas pre-merge (linting, pruebas unitarias, cobertura).
- Compuertas pre-despliegue (pruebas de integración, escaneos de seguridad, pruebas de humo).
- Compuertas post-despliegue (métricas canario, pruebas de humo en producción).

**Fuera del alcance / NO resuelto por esto:**
- QA manual o pruebas exploratorias (complementa las compuertas).
- Calidad del producto (las compuertas validan calidad técnica, no corrección de funcionalidades).

## Modelo mental / Intuición
Piensa en las compuertas de calidad como **controles de seguridad del aeropuerto**:
- No puedes abordar el avión (desplegar a producción) sin pasar seguridad (compuertas).
- Múltiples capas: verificación de identidad (linting), escaneo de equipaje (pruebas), escaneo corporal (escaneos de seguridad).
- Si fallas una verificación, no puedes continuar hasta que lo corrijas.

Sin compuertas, el código defectuoso se filtra y causa incidentes en producción.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Construyes sistemas de producción donde los defectos tienen un costo real.
- Trabajas en equipos (las compuertas imponen estándares compartidos).
- Despliegas frecuentemente (las compuertas permiten lanzamientos rápidos y seguros).

### Evítalo cuando
- Prototipar código desechable (las compuertas agregan fricción sin valor).
- Las compuertas son demasiado lentas (>30 minutos para CI mata la productividad; optimizar o relajar compuertas).

## Cómo lo usaría (práctico)
- **Contexto:** Construyendo un servicio API en Go desplegado via GitHub Actions.
- **Pasos:**
  1. **Hooks pre-commit (local):**
     - Ejecutar linter (`golangci-lint`) y formateadores (`gofmt`).
     - Bloquear commit si el linting falla (detecta problemas antes del PR).
  2. **Compuertas pre-merge (CI en PR):**
     - Linting: `golangci-lint run --timeout 5m`
     - Pruebas unitarias: `go test ./... -race -coverprofile=coverage.out`
     - Verificación de cobertura: Fallar si cobertura <80%.
     - Escaneo de seguridad: `gosec ./...` (detectar inyección SQL, código inseguro).
     - Análisis estático: `go vet`, `staticcheck`.
  3. **Compuertas pre-despliegue (CI en rama main):**
     - Pruebas de integración: Ejecutar contra contenedores Docker (PostgreSQL, Redis).
     - Pruebas de humo: Desplegar a staging, ejecutar llamadas API críticas.
     - Escaneo de seguridad de contenedores: `trivy` en imágenes Docker.
  4. **Compuertas post-despliegue (producción):**
     - Despliegue canario: 5% del tráfico por 10 minutos.
     - Monitorear tasa de error y latencia P95.
     - Auto-rollback si tasa de error >0.5%.
- **Cómo se ve el éxito:** CI se completa en <10 minutos; cero incidentes en producción por defectos prevenibles; merge de PR bloqueado por fallos de pruebas.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Previene que defectos lleguen a producción.
  - Automatiza la imposición (sin depender de memoria humana).
  - Construye confianza para moverse rápido.
- **Desventajas / Riesgos:**
  - Compuertas lentas matan la productividad (los desarrolladores esperan por CI).
  - Compuertas demasiado estrictas crean fricción (violaciones menores de estilo bloquean PRs).
  - Falsos positivos (pruebas inestables, escaneos de seguridad demasiado agresivos) erosionan la confianza.

### Alternativas
- **Solo revisión manual de código:** Detecta algunos problemas pero pierde lo que las herramientas automatizan (variables no usadas, riesgos de seguridad).
- **Sin compuertas ("confiar en los desarrolladores"):** Rápido inicialmente pero los defectos se filtran; los incidentes en producción aumentan.
- **Solo compuertas post-despliegue:** Entregar rápido pero detectar defectos en producción en lugar de CI (mayor radio de impacto).

### Cómo elegir
- **Sistema de producción de alto riesgo:** Usar compuertas completas (linting, pruebas, seguridad, despliegues canario).
- **Herramientas internas / prototipos:** Compuertas ligeras (linting básico, sin mandatos de cobertura).
- **Equilibrar velocidad:** Mantener compuertas rápidas (<10 minutos para CI de PR); ejecutar pruebas más lentas (E2E, pruebas de carga) en rama main o nightly.

## Modos de fallo y trampas
- **Compuertas lentas:** CI toma 30+ minutos; los desarrolladores cambian de contexto y pierden productividad.
- **Pruebas inestables:** Fallos aleatorios erosionan la confianza; los desarrolladores evitan compuertas o re-ejecutan hasta que pasen.
- **Reglas demasiado estrictas:** Violaciones menores de estilo bloquean PRs; mata velocidad y moral.
- **Teatro de seguridad:** Escanear vulnerabilidades pero no corregirlas (alertas ignoradas).
- **Sin compuertas incrementales:** Ejecutar todas las pruebas en cada commit; se debería ejecutar pruebas unitarias en PR, pruebas de integración en main.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Duración de CI (por etapa del pipeline; señalar >10 minutos para PRs).
  - Tasa de aprobación de compuertas (% de compilaciones que pasan al primer intento; apuntar a >95%).
  - Tasa de inestabilidad (% de pruebas que fallan aleatoriamente; apuntar a <1%).
  - PRs bloqueados (cuántos PRs esperando por CI; señalar >5).
- **Logs:** Rastrear fallos de compuertas por tipo (linting vs pruebas vs seguridad).
- **Trazas:** N/A para las compuertas en sí.
- **Alertas:** Pico en duración de CI, aumento en tasa de inestabilidad, fallos de escaneo de seguridad.

## Notas de implementación
- **Lista de verificación**
  - [ ] Configurar pipeline de CI (GitHub Actions, Jenkins, GitLab CI).
  - [ ] Agregar compuerta de linting (fallar en violaciones).
  - [ ] Agregar compuerta de pruebas unitarias (fallar en fallos de pruebas o caída de cobertura).
  - [ ] Agregar escaneo de seguridad (SAST para código, DAST para APIs, escaneo de contenedores para imágenes).
  - [ ] Agregar compuerta de pruebas de integración en rama main (no en cada PR para ahorrar tiempo).
  - [ ] Implementar despliegues canario con auto-rollback.
  - [ ] Monitorear métricas de compuertas (duración, tasa de aprobación, inestabilidad).
  - [ ] Poner en cuarentena o corregir pruebas inestables inmediatamente.

- **Notas de seguridad / cumplimiento**
  - Incluir SAST (análisis estático), DAST (pruebas dinámicas) y escaneos de dependencias (Snyk, Dependabot).
  - Bloquear despliegues en vulnerabilidades críticas (no solo advertir).

- **Notas de rendimiento**
  - Paralelizar ejecución de pruebas para acelerar CI.
  - Usar caché para dependencias (módulos Go, paquetes npm).
  - Ejecutar pruebas costosas (E2E, pruebas de carga) en rama main o nightly, no en cada PR.

- **Notas operacionales**
  - Las compuertas no son "configurar y olvidar": monitorear falsos positivos y ajustar umbrales.
  - La retroalimentación rápida es crítica: optimizar compuertas lentas o ejecutarlas con menos frecuencia.

## Mini ejemplo
```yaml
# GitHub Actions quality gates example
name: Quality Gates
on:
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Go
        uses: actions/setup-go@v4
        with:
          go-version: '1.21'
      - name: Run linter
        run: golangci-lint run --timeout 5m

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Go
        uses: actions/setup-go@v4
        with:
          go-version: '1.21'
      - name: Run unit tests
        run: go test ./... -race -coverprofile=coverage.out
      - name: Check coverage
        run: |
          coverage=$(go tool cover -func=coverage.out | grep total | awk '{print $3}' | sed 's/%//')
          if (( $(echo "$coverage < 80" | bc -l) )); then
            echo "Coverage $coverage% is below 80%"
            exit 1
          fi

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run security scan
        run: |
          go install github.com/securego/gosec/v2/cmd/gosec@latest
          gosec ./...

  integration-tests:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'  # Only on main branch
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: testpass
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v3
      - name: Run integration tests
        run: go test ./integration/... -tags=integration
```

## Anti-patrones comunes
- **Anti-patrón:** Sin compuertas ("detectaremos errores en la revisión de código").
  - **Por qué es malo:** Los humanos son inconsistentes; las herramientas nunca olvidan.
  - **Mejor enfoque:** Automatizar lo que se pueda automatizar (linting, pruebas, escaneos de seguridad); reservar la revisión humana para diseño y lógica.

- **Anti-patrón:** Compuertas que toman >30 minutos.
  - **Por qué es malo:** Los desarrolladores pierden contexto, la productividad cae, la frustración aumenta.
  - **Mejor enfoque:** Paralelizar pruebas, usar caché, ejecutar pruebas costosas con menos frecuencia (solo rama main o nightly).

- **Anti-patrón:** Ignorar pruebas inestables ("solo re-ejecuta CI").
  - **Por qué es malo:** Erosiona la confianza en las compuertas; los desarrolladores evitan o ignoran fallos.
  - **Mejor enfoque:** Poner en cuarentena pruebas inestables inmediatamente; corregir causa raíz (condiciones de carrera, tiempos de espera) o eliminar.

- **Anti-patrón:** Demasiadas compuertas en cada PR.
  - **Por qué es malo:** Ralentiza los ciclos de retroalimentación; mata la productividad.
  - **Mejor enfoque:** Escalonar compuertas—compuertas rápidas en PRs (lint, pruebas unitarias), compuertas más lentas en main (integración, E2E, pruebas de carga).

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
Las compuertas de calidad son verificaciones automatizadas en CI/CD que previenen que código malo llegue a producción. Se ejecutan en diferentes etapas: linting y pruebas unitarias en cada PR, pruebas de integración en la rama main, y despliegues canario en producción. El objetivo es detectar defectos temprano e imponer estándares de calidad automáticamente. Las compuertas deben ser rápidas (menos de 10 minutos para PRs) y confiables (sin pruebas inestables) o matan la productividad. Escalonas compuertas basándote en el costo—ejecutar pruebas baratas y rápidas en cada cambio; ejecutar pruebas costosas y lentas con menos frecuencia.

### Preguntas trampa (con respuestas)
1) **P:** ¿Las compuertas de calidad no ralentizan la entrega?
   - **R:** Solo si se implementan mal. Compuertas rápidas (<10 minutos) realmente aceleran la entrega al detectar defectos antes de que lleguen a producción, reduciendo retrabajo y tiempo de respuesta a incidentes. Las compuertas lentas o inestables sí perjudican la productividad—optimizar o escalonar.

2) **P:** ¿Cuál es la compuerta de calidad más importante?
   - **R:** No hay una sola compuerta más importante. Usa capas: el linting detecta problemas de estilo/corrección, las pruebas detectan errores de lógica, los escaneos de seguridad detectan vulnerabilidades. Cada compuerta previene diferentes modos de fallo. Si me obligan a elegir, las pruebas tienen mayor impacto (detectan la mayoría de los errores).

3) **P:** ¿Las compuertas deberían ser 100% estrictas (nunca permitir excepciones)?
   - **R:** No. Usa excepciones basadas en riesgo: las vulnerabilidades de seguridad siempre deberían bloquear, pero violaciones menores de linting podrían ser aceptables en emergencias. Registrar excepciones y corregirlas después. No hacer las compuertas tan estrictas que incentiven evadirlas.

4) **P:** ¿Cómo manejas pruebas inestables en las compuertas?
   - **R:** Poner en cuarentena inmediatamente (omitirlas o moverlas a una suite separada). Las pruebas inestables erosionan la confianza—los desarrolladores evitarán las compuertas o ignorarán fallos. Corregir la causa raíz (condiciones de carrera, dependencias del entorno) o eliminar. Nunca tolerar la inestabilidad.

5) **P:** ¿Deberías ejecutar todas las pruebas en cada PR?
   - **R:** No. Escalonar pruebas: pruebas unitarias rápidas en cada PR (<5 minutos), pruebas de integración en rama main (<10 minutos), E2E y pruebas de carga nightly o en releases. Ejecutar todas las pruebas en cada PR hace CI demasiado lento y mata la productividad.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo nombrar al menos 4 tipos de compuertas de calidad y su propósito.
- [ ] Puedo explicar el trade-off entre estrictez de compuertas y velocidad del desarrollador.
- [ ] Puedo describir cómo escalonar compuertas (PR vs rama main vs producción).
- [ ] Puedo articular por qué las pruebas inestables son inaceptables en las compuertas.
- [ ] Puedo dar un ejemplo de cuándo relajar una compuerta (excepción basada en riesgo).

## Enlaces (SIN duplicación)
### Prerequisitos
- [Aseguramiento de Calidad de Software](software-quality-assurance.md)
- [Pirámide de pruebas](testing-pyramid.md)
- [Fundamentos de CI/CD](ci-cd-basics.md)

### Temas relacionados
- [Calidad de código](code-quality.md)
- [Código limpio](clean-code.md)

### Comparar con
- [Revisión manual de código](../operations/operational-planning.md) — Las compuertas automatizan lo que se puede automatizar; las revisiones se enfocan en diseño y lógica.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: configuración de CI de Heimdall, escaneos de seguridad de Opportunity Actions.
- Considerar agregar sección sobre compuertas post-despliegue (despliegues canario, feature flags, monitoreo).
