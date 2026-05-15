---
id: ci-cd-basics
title: "Fundamentos de CI/CD"
type: concept
status: learning
importance: 85
difficulty: medium
tags: [devops, automation, deployment, integration, delivery]
canonical: true
aliases: [continuous-integration, continuous-deployment, continuous-delivery]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Fundamentos de CI/CD

## TL;DR (BLUF)
- CI/CD automatiza la integración de código, pruebas y despliegue para reducir errores manuales y acelerar la entrega.
- CI (Integración Continua): fusionar código frecuentemente, ejecutar pruebas automatizadas.
- CD: Entrega Continua (listo para desplegar) o Despliegue Continuo (despliegue automático a producción).
- Trade-off clave: costo de configuración de automatización vs. velocidad y confiabilidad de entrega.

## Definición
**Qué es:** Un conjunto de prácticas y herramientas que automatizan el proceso de integrar cambios de código, probarlos y desplegarlos a producción.  
**Términos clave:**
- **CI (Integración Continua):** Fusionar cambios de código frecuentemente (diario/por hora) con compilaciones y pruebas automatizadas.
- **CD (Entrega Continua):** El código siempre está en un estado desplegable; el despliegue requiere aprobación manual.
- **CD (Despliegue Continuo):** Cada cambio que pasa las pruebas se despliega automáticamente a producción.
- **Pipeline:** Secuencia automatizada de pasos (compilar, probar, desplegar).
- **Artefacto:** Paquete compilado listo para despliegue (imagen Docker, binario, etc.).

## Por qué importa
- **Entrega más rápida:** Los pipelines automatizados despliegan en minutos vs. horas/días para procesos manuales.
- **Menos errores:** Las pruebas automatizadas detectan defectos antes de producción.
- **Reduce el riesgo:** Lanzamientos pequeños y frecuentes son más fáciles de depurar y revertir que lanzamientos grandes.
- **Mejor colaboración:** CI fuerza la integración temprana, detectando conflictos rápidamente.
- **Relevancia en entrevistas:** CI/CD es fundamental para discusiones sobre entrega de software moderno.

## Alcance y no-objetivos
**Dentro del alcance:**
- CI: compilaciones automatizadas, pruebas, integración.
- CD: automatización de despliegue, pipelines de staging/producción.
- Herramientas: GitHub Actions, Jenkins, GitLab CI, CircleCI (conceptualmente).

**Fuera del alcance / NO resuelto por esto:**
- Detalles de Infraestructura como Código (IaC) (Terraform, CloudFormation—tema separado).
- Plataformas cloud específicas (AWS, GCP, Azure—temas separados).

## Modelo mental / Intuición
Piensa en CI/CD como una **línea de ensamblaje para software**:
- **CI:** Control de calidad en cada estación (las pruebas automatizadas detectan defectos temprano).
- **CD (Entrega):** Producto listo para enviar (pero espera aprobación manual).
- **CD (Despliegue):** El producto se envía automáticamente cuando está listo (sin compuerta manual).

Sin CI/CD, estás elaborando cada lanzamiento a mano (lento, propenso a errores, riesgoso).

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Construyes sistemas de producción con cambios frecuentes.
- Trabajas en equipos (CI detecta conflictos de integración temprano).
- Despliegas frecuentemente (diario o más).
- Se necesitan compuertas de calidad (pruebas automatizadas antes del despliegue).

### Evítalo cuando
- Escribes código desechable (prototipos, scripts de un solo uso).
- El despliegue es extremadamente riesgoso (sistemas críticos de seguridad pueden requerir verificaciones manuales; aunque CI/CD con compuertas sigue siendo valioso).

## Cómo lo usaría (práctico)
- **Contexto:** Configurando CI/CD para un servicio API en Go desplegado en AWS.
- **Pasos:**
  1. **CI (en cada PR):**
     - Disparador: Pull request abierto.
     - Pasos: Checkout de código → Instalar dependencias → Lint → Ejecutar pruebas unitarias → Verificar cobertura.
     - Resultado: Bloquear merge si el linting falla o las pruebas fallan.
  2. **CD (al fusionar a main):**
     - Disparador: Código fusionado a la rama main.
     - Pasos: Compilar imagen Docker → Ejecutar pruebas de integración → Subir imagen al registro → Etiquetar con versión.
     - Resultado: Artefacto listo para despliegue.
  3. **CD (desplegar a staging):**
     - Disparador: Nueva imagen en el registro.
     - Pasos: Desplegar al entorno de staging → Ejecutar pruebas de humo → Monitorear por 10 minutos.
     - Resultado: Entorno de staging actualizado automáticamente.
  4. **CD (desplegar a producción):**
     - Opción A (Entrega Continua): Se requiere aprobación manual.
     - Opción B (Despliegue Continuo): Despliegue automático si staging tiene éxito + despliegue canario (5% del tráfico por 10 min).
- **Cómo se ve el éxito:** El código va de commit a producción en <30 minutos; cero pasos de despliegue manual; tasa de error <0.1%.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Entrega más rápida (automatizar tareas repetitivas).
  - Menos defectos (las pruebas automatizadas detectan errores temprano).
  - Riesgo reducido (lanzamientos pequeños y frecuentes).
  - Mejor visibilidad (el pipeline muestra el estado de compilación/pruebas).
- **Desventajas / Riesgos:**
  - Costo inicial de configuración (escribir pipelines, configurar herramientas).
  - Requiere disciplina (las pruebas deben ser confiables, sin pruebas inestables).
  - Curva de aprendizaje inicial (los equipos necesitan conocimiento de CI/CD).

### Alternativas
- **Compilaciones/despliegues manuales:** Lento, propenso a errores, no escala.
- **Lanzamientos programados (semanal/mensual):** Menos despliegues pero lanzamientos más grandes y riesgosos.
- **Sin automatización de pruebas:** Más rápido inicialmente pero alta tasa de defectos en producción.

### Cómo elegir
- **Sistemas de producción:** CI/CD completo con pruebas automatizadas y despliegues.
- **Herramientas internas:** CI ligero (pruebas) + despliegues manuales puede ser suficiente.
- **Prototipos:** Sin CI/CD (excesivo para código desechable).

## Modos de fallo y trampas
- **Pipelines lentos:** CI toma >30 minutos; los desarrolladores cambian de contexto y pierden productividad.
- **Pruebas inestables:** Fallos aleatorios en pruebas bloquean despliegues; los equipos evitan CI o ignoran fallos.
- **Sin estrategia de rollback:** El despliegue rompe producción sin forma rápida de revertir.
- **Desplegar todo a producción:** Sin entorno de staging para detectar problemas antes de producción.
- **Pipelines demasiado complicados:** Demasiadas etapas, aprobaciones manuales o dependencias (mata la velocidad).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Duración del pipeline (señalar >30 minutos para PRs, >60 minutos para despliegues).
  - Tasa de éxito del pipeline (apuntar a >95%).
  - Frecuencia de despliegue (mayor es mejor con CI/CD).
  - Tiempo medio de recuperación (MTTR; ¿qué tan rápido puedes hacer rollback?).
- **Logs:** Rastrear fallos del pipeline por etapa (compilación vs pruebas vs despliegue).
- **Trazas:** N/A para CI/CD en sí mismo.
- **Alertas:** Pico en duración del pipeline, caída en tasa de éxito, fallo en despliegue.

## Notas de implementación
- **Lista de verificación**
  - [ ] Configurar CI para PRs (lint, pruebas unitarias, verificación de cobertura).
  - [ ] Configurar CD para la rama main (compilar artefacto, pruebas de integración).
  - [ ] Definir etapas de despliegue (staging → producción).
  - [ ] Implementar estrategia de rollback (canario, blue/green, o reversión manual).
  - [ ] Monitorear métricas del pipeline (duración, tasa de éxito).
  - [ ] Corregir o poner en cuarentena pruebas inestables inmediatamente.
  - [ ] Usar gestión de secretos (no codificar credenciales en los pipelines).

- **Notas de seguridad / cumplimiento**
  - Incluir escaneos de seguridad en CI (SAST, verificación de dependencias).
  - Usar credenciales con mínimos privilegios para despliegue (roles IAM, cuentas de servicio).
  - Auditar despliegues (quién desplegó qué, cuándo).

- **Notas de rendimiento**
  - Paralelizar pasos del pipeline donde sea posible (lint + pruebas en paralelo).
  - Cachear dependencias (módulos Go, paquetes npm) para acelerar compilaciones.
  - Ejecutar pruebas costosas (E2E, pruebas de carga) con menos frecuencia (solo rama main, nightly).

- **Notas operacionales**
  - Monitorear producción después de despliegues (tasa de error, picos de latencia).
  - Automatizar rollback si las métricas se degradan (canario con auto-reversión).

## Mini ejemplo (GitHub Actions)
```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with:
          go-version: '1.21'
      - name: Run linter
        run: golangci-lint run --timeout 5m

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with:
          go-version: '1.21'
      - name: Run tests
        run: go test ./... -race -coverprofile=coverage.out
      - name: Check coverage
        run: |
          coverage=$(go tool cover -func=coverage.out | grep total | awk '{print $3}' | sed 's/%//')
          if (( $(echo "$coverage < 80" | bc -l) )); then
            echo "Coverage $coverage% is below 80%"
            exit 1
          fi

# .github/workflows/cd.yml
name: CD
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      - name: Push to registry
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin
          docker push myapp:${{ github.sha }}

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        run: |
          # Deploy to staging environment (kubectl, AWS CLI, etc.)
          kubectl set image deployment/myapp myapp=myapp:${{ github.sha }} -n staging
      - name: Smoke tests
        run: |
          # Run basic smoke tests against staging
          curl -f https://staging.example.com/health || exit 1
```

## Anti-patrones comunes
- **Anti-patrón:** Sin pipeline de CI ("probaremos localmente").
  - **Por qué es malo:** Error humano; pruebas omitidas; código roto llega a main.
  - **Mejor enfoque:** Automatizar pruebas en CI; bloquear merge en fallos.

- **Anti-patrón:** Desplegar directamente a producción (sin staging).
  - **Por qué es malo:** Problemas descubiertos en producción; alto radio de impacto.
  - **Mejor enfoque:** Desplegar primero a staging; validar antes de producción.

- **Anti-patrón:** Pasos de despliegue manuales.
  - **Por qué es malo:** Lento, propenso a errores, no repetible.
  - **Mejor enfoque:** Automatizar despliegue con scripts o herramientas (kubectl, AWS CLI).

- **Anti-patrón:** Ignorar pruebas inestables ("solo re-ejecuta CI").
  - **Por qué es malo:** Erosiona la confianza en CI; los desarrolladores evitan o ignoran fallos.
  - **Mejor enfoque:** Corregir o poner en cuarentena pruebas inestables inmediatamente.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
CI/CD automatiza el camino desde el commit de código hasta producción. CI significa fusionar código frecuentemente y ejecutar pruebas automatizadas para detectar errores temprano. CD tiene dos sabores: Entrega Continua significa que el código siempre es desplegable pero requiere aprobación manual para ir a producción, mientras que Despliegue Continuo significa que cada cambio que pasa las pruebas se despliega automáticamente. Los beneficios clave son velocidad (automatizar tareas repetitivas), calidad (las pruebas automatizadas detectan defectos) y riesgo reducido (lanzamientos pequeños y frecuentes son más fáciles de depurar y revertir). Un pipeline típico ejecuta linting y pruebas unitarias en cada PR, compila artefactos y ejecuta pruebas de integración en main, y despliega a staging y producción con las compuertas apropiadas.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cuál es la diferencia entre Entrega Continua y Despliegue Continuo?
   - **R:** 
     - **Entrega Continua:** El código siempre está en un estado desplegable, pero el despliegue a producción requiere aprobación manual.
     - **Despliegue Continuo:** Cada cambio que pasa las pruebas automatizadas se despliega a producción automáticamente (sin compuerta manual).
     - El Despliegue Continuo es más automatizado pero requiere mayor confianza en las pruebas.

2) **P:** ¿CI/CD no significa desplegar código sin probar a producción?
   - **R:** No. CI/CD se basa en pruebas automatizadas en cada etapa. El código que falla las pruebas se bloquea para fusionar o desplegar. El objetivo es automatizar las *pruebas* y el *despliegue*, no omitir las pruebas.

3) **P:** ¿Cómo previenes despliegues malos en Despliegue Continuo?
   - **R:** Usa estrategias de despliegue progresivo:
     - **Despliegues canario:** Desplegar a un pequeño porcentaje del tráfico primero; hacer rollback si los errores aumentan.
     - **Feature flags:** Desplegar código pero mantener funcionalidades apagadas; habilitar gradualmente.
     - **Rollback automatizado:** Monitorear métricas (tasa de error, latencia); auto-revertir si se superan los umbrales.

4) **P:** ¿Qué pasa si el pipeline de CI es demasiado lento (>1 hora)?
   - **R:** Optimizar:
     - Paralelizar pasos (ejecutar lint + pruebas en paralelo).
     - Cachear dependencias (módulos Go, paquetes npm).
     - Escalonar pruebas (pruebas rápidas en PR; pruebas lentas en main).
     - Usar hardware o runners más rápidos.
     - Objetivo: <10 minutos para PRs, <30 minutos para pipeline completo.

5) **P:** ¿Todos los proyectos deberían tener CI/CD?
   - **R:** No necesariamente. Usa CI/CD para sistemas de producción con cambios frecuentes y múltiples ingenieros. Para prototipos, scripts de un solo uso, o sistemas estables con lanzamientos raros, el costo de configuración puede superar el beneficio. Pero para la mayoría del software moderno, CI/CD es esencial.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar la diferencia entre CI, Entrega Continua y Despliegue Continuo.
- [ ] Puedo describir un pipeline típico de CI/CD (etapas y compuertas).
- [ ] Puedo nombrar al menos 3 beneficios de CI/CD (velocidad, calidad, reducción de riesgo).
- [ ] Puedo explicar estrategias para prevenir despliegues malos (canario, feature flags, rollback).
- [ ] Puedo articular cuándo CI/CD es excesivo (y cuándo es esencial).

## Enlaces (SIN duplicación)
### Prerequisitos
- [Aseguramiento de Calidad de Software](software-quality-assurance.md)
- [Fundamentos de pruebas](testing-fundamentals.md)

### Temas relacionados
- [Compuertas de calidad](quality-gates.md)
- [Pirámide de pruebas](testing-pyramid.md)
- [Infraestructura como Código](../operations/infrastructure-as-code.md)
- [Despliegues blue/green](../operations/blue-green-deployments.md)
- [Despliegues canario](../operations/canary-deployments.md)

### Comparar con
- [Compuertas de calidad](quality-gates.md) — Las compuertas de calidad son los puntos de verificación dentro de un pipeline de CI/CD que imponen estándares de calidad.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: configuración de CI/CD de Heimdall, pipeline de despliegue de Opportunity Actions.
- Considerar agregar sección sobre comparación de herramientas de CI/CD (GitHub Actions vs Jenkins vs GitLab CI).
- Enlazar a estrategias de despliegue (blue/green, canario, feature flags) cuando esos temas sean creados.
