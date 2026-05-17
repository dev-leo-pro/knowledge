---
id: rolling-deployments
title: "Despliegues graduales (Rolling Deployments)"
type: pattern
status: learning
importance: 75
difficulty: easy
tags: [deployment, reliability, zero-downtime, operations]
canonical: true
aliases: [rolling-update, incremental-deployment]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Despliegues graduales (Rolling Deployments)

## TL;DR (BLUF)
- El despliegue gradual reemplaza instancias de la versión antigua con la nueva versión de forma incremental (una por una o en lotes).
- Úsalo para despliegues sin tiempo de inactividad con mínima sobrecarga de infraestructura.
- Trade-off clave: simplicidad y bajo costo vs. reversión más lenta (comparado con blue/green).

## Definición
**Qué es:** Una estrategia de despliegue que reemplaza gradualmente las instancias en ejecución de la versión antigua con instancias de la nueva versión, una a la vez o en lotes pequeños, hasta que todas las instancias estén actualizadas.  
**Términos clave:** reemplazo de instancias, tamaño de lote, health checks, ventana de reversión, despliegue sin tiempo de inactividad, apagado elegante, surge (instancias extra durante el despliegue).

## Por qué importa
- **Sin tiempo de inactividad:** Los usuarios no experimentan interrupción del servicio durante los despliegues.
- **Rentable:** No necesitas 2x de infraestructura (a diferencia de blue/green).
- **Simple de implementar:** Estrategia estándar en Kubernetes, AWS ECS y la mayoría de plataformas de orquestación.
- **Menor riesgo que big-bang:** El despliegue gradual limita el radio de impacto (pero más lento que canary).
- **Relevancia en entrevistas:** Patrón de despliegue común en discusiones de diseño de sistemas.

## Alcance y no-objetivos
**Dentro del alcance:**
- Mecánica del despliegue gradual (reemplazo de instancias, dimensionamiento de lotes).
- Cuándo usar rolling vs. otras estrategias.
- Validación de health checks durante el despliegue.

**Fuera del alcance / NO resuelto por esto:**
- Migraciones de base de datos (desafío separado; requiere esquemas compatibles hacia adelante).
- Implementaciones específicas de plataforma (Kubernetes, ECS — aunque los conceptos aplican).
- Entrega progresiva avanzada (usa canary para eso).

## Modelo mental / Intuición
Piensa en el despliegue gradual como **reemplazar bombillas en un candelabro una por una**:
- No apagas todas las luces a la vez (eso es un despliegue big-bang).
- Reemplazas una bombilla a la vez mientras el candelabro sigue encendido (sin tiempo de inactividad).
- Si una bombilla nueva está defectuosa, lo notas antes de reemplazar todas las bombillas (radio de impacto limitado).
- Eventualmente todas las bombillas son nuevas (despliegue completo terminado).

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Se requiere cero tiempo de inactividad (servicios orientados al cliente).
- El costo de infraestructura importa (no puedes permitir 2x para blue/green).
- El despliegue es de bajo riesgo (servicio maduro, cambios bien probados).
- La plataforma soporta actualizaciones rolling de forma nativa (Kubernetes, ECS, etc.).

### Evítalo cuando
- La reversión instantánea es crítica (usa blue/green en su lugar).
- Se necesita validación gradual (usa canary en su lugar).
- Incompatibilidad de versiones (la antigua y la nueva no pueden coexistir).
- Las migraciones de base de datos son complejas (rolling + cambios de esquema es complicado).

## Cómo lo usaría (práctico)
- **Contexto:** Desplegando un servicio API en Go en Kubernetes con 10 réplicas.
- **Pasos:**
  1. **Configurar actualización rolling:**
     - Max surge: 2 (puede agregar hasta 2 instancias extra durante el despliegue).
     - Max unavailable: 1 (como máximo 1 instancia puede estar caída durante el despliegue).
     - Esto asegura 9-12 instancias ejecutándose en todo momento (cero tiempo de inactividad).
  2. **Desplegar nueva versión:**
     - Kubernetes crea 1 nuevo pod (v2).
     - Espera a que el health check pase (readiness probe).
     - Si está sano, termina 1 pod antiguo (v1).
     - Repite hasta que los 10 pods sean v2.
  3. **Monitorear durante el despliegue:**
     - Observar tasa de errores, latencia, health checks.
     - Si se detectan problemas, pausar o revertir (pero más lento que blue/green).
  4. **Completar despliegue:**
     - Los 10 pods ahora ejecutan v2.
     - Tiempo total del despliegue: ~5-10 minutos (dependiendo del tiempo de arranque del pod).
- **Cómo se ve el éxito:** Cero tiempo de inactividad; reemplazo gradual durante 10 minutos; los health checks validan cada instancia.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Sin tiempo de inactividad (instancias reemplazadas incrementalmente).
  - Rentable (sin 2x de infraestructura).
  - Simple (soporte nativo en la mayoría de plataformas).
  - Menor radio de impacto que big-bang (los problemas afectan solo algunas instancias).
- **Desventajas / Riesgos:**
  - Reversión más lenta (debe redesplegar la versión antigua y luego revertir incrementalmente).
  - Desfase de versión (la antigua y la nueva se ejecutan concurrentemente; se necesita compatibilidad de API).
  - Sin cambio de tráfico instantáneo (a diferencia de blue/green).
  - Más lento que canary para detección de riesgos (todas las instancias eventualmente reemplazadas).

### Alternativas
- **[Despliegues blue/green](blue-green-deployments.md):** Cambio instantáneo, reversión instantánea (mayor costo).
- **[Despliegues canary](canary-deployments.md):** Validación gradual con porcentaje de tráfico (más complejo).
- **Despliegue recreate:** Detener todo lo viejo, iniciar todo lo nuevo (simple pero con tiempo de inactividad).

### Cómo elegir
- **Sin tiempo de inactividad + bajo costo:** Rolling.
- **Sin tiempo de inactividad + reversión instantánea:** Blue/green.
- **Sin tiempo de inactividad + validación gradual:** Canary.
- **Servicio simple, tiempo de inactividad aceptable:** Recreate.

## Modos de fallo y trampas
- **Health checks fallidos:** La nueva instancia falla el readiness probe; el despliegue se atasca (debe corregir o revertir).
- **Despliegue muy rápido:** Tamaño de lote muy grande (por ejemplo, reemplazar 5 instancias a la vez); los problemas afectan a muchos usuarios.
- **Incompatibilidad de versiones:** Las instancias antiguas y nuevas no pueden coexistir (cambios de API incompatibles, problemas de estado compartido).
- **Arranque lento:** Las nuevas instancias tardan 5+ minutos en iniciar; el despliegue toma horas.
- **Sin monitoreo:** Desplegar sin observar métricas; los problemas no se detectan hasta que todas las instancias son nuevas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Progreso del despliegue (% de instancias actualizadas).
  - Tasa de errores (comparar nuevas instancias con antiguas; señalar picos).
  - Latencia (P95/P99; señalar si las nuevas instancias son más lentas).
  - Tasa de éxito de health checks (debería ser 100%).
- **Logs:** Comparar logs entre instancias antiguas y nuevas (buscar nuevos errores).
- **Trazas:** Validar que las nuevas instancias manejan solicitudes correctamente.
- **Alertas:** Fallos de health check, pico en tasa de errores, despliegue atascado.

## Notas de implementación
- **Lista de verificación**
  - [ ] Configurar estrategia de despliegue (max surge, max unavailable, tamaño de lote).
  - [ ] Definir health checks (readiness y liveness probes).
  - [ ] Establecer timeout razonable (arranque del pod + verificación de readiness; por ejemplo, 5 minutos).
  - [ ] Monitorear durante el despliegue (tasa de errores, latencia, health checks).
  - [ ] Probar procedimiento de reversión (redesplegar versión antigua si es necesario).
  - [ ] Usar apagado elegante (drenar conexiones antes de terminar instancias antiguas).

- **Notas de seguridad / cumplimiento**
  - Asegurar que las nuevas instancias tengan postura de seguridad idéntica (configs, secretos, roles IAM).
  - Auditar eventos de despliegue (quién desplegó, cuándo, qué versión).

- **Notas de rendimiento**
  - Pre-calentar nuevas instancias (pre-cargar cachés, conexiones) para readiness más rápida.
  - Usar drenado de conexiones en instancias antiguas (apagado elegante).

- **Notas operacionales**
  - Documentar procedimiento de reversión (debe ser ejecutable bajo presión).
  - Monitorear de cerca durante los primeros reemplazos de instancias (detectar problemas temprano).

## Mini ejemplo (Kubernetes)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2        # Can add up to 2 extra pods during rollout
      maxUnavailable: 1  # At most 1 pod can be down during rollout
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: myapp:v2.0  # New version
        ports:
        - containerPort: 8080
        readinessProbe:    # Health check before routing traffic
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
        livenessProbe:     # Health check during runtime
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10

# Rollout process:
# 1. kubectl apply -f deployment.yaml
# 2. kubectl rollout status deployment/api
# 3. kubectl rollout undo deployment/api  (if rollback needed)
```

## Anti-patrones comunes
- **Anti-patrón:** Sin health checks definidos.
  - **Por qué es malo:** Las instancias rotas reciben tráfico; los usuarios ven errores.
  - **Mejor enfoque:** Definir readiness y liveness probes; solo enrutar tráfico a instancias sanas.

- **Anti-patrón:** Max unavailable = réplicas (reemplazar todo a la vez).
  - **Por qué es malo:** Anula el propósito del despliegue gradual; equivale a recreate (tiempo de inactividad).
  - **Mejor enfoque:** Mantener max unavailable pequeño (1-2) para asegurar que el servicio siempre esté disponible.

- **Anti-patrón:** Incompatibilidad de versiones (cambios de API incompatibles).
  - **Por qué es malo:** Las instancias antiguas y nuevas no pueden coexistir; los clientes fallan en una u otra.
  - **Mejor enfoque:** Asegurar compatibilidad hacia atrás; usar endpoints versionados si se necesitan cambios incompatibles.

- **Anti-patrón:** Sin plan de reversión.
  - **Por qué es malo:** Se descubren problemas a mitad del despliegue; sin forma rápida de revertir.
  - **Mejor enfoque:** Documentar procedimiento de reversión (`kubectl rollout undo` o redesplegar versión antigua).

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
El despliegue gradual reemplaza instancias antiguas con instancias nuevas de forma gradual — una a la vez o en lotes pequeños. Por ejemplo, si tienes 10 instancias, Kubernetes podría iniciar 1 nueva instancia, esperar a que esté sana, y luego terminar 1 instancia antigua. Repite esto hasta que las 10 sean la nueva versión. Los beneficios son cero tiempo de inactividad (siempre hay instancias sanas sirviendo tráfico) y bajo costo (sin 2x de infraestructura como blue/green). Las desventajas son reversión más lenta (debe redesplegar la versión antigua e ir revirtiéndola incrementalmente) y desfase de versión durante el despliegue (la antigua y la nueva coexisten temporalmente). Es la estrategia predeterminada en la mayoría de plataformas de orquestación porque es simple y efectiva.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cómo se diferencia el despliegue gradual del blue/green?
   - **R:** 
     - **Rolling:** Reemplaza instancias incrementalmente (una por una); sin 2x de infraestructura; reversión más lenta.
     - **Blue/green:** Dos entornos completos; cambio de tráfico instantáneo; reversión instantánea; 2x de costo de infraestructura.
     - Rolling es rentable; blue/green es más rápido y seguro.

2) **P:** ¿Qué pasa si una nueva instancia falla los health checks durante el despliegue gradual?
   - **R:** El despliegue se pausa. Kubernetes (o el orquestador) no terminará instancias antiguas si las nuevas instancias no están listas. Debes corregir el problema (bug de código, error de configuración) y redesplegar, o revertir a la versión antigua. Esto previene que instancias rotas reciban tráfico.

3) **P:** ¿Puedes tener cero tiempo de inactividad con despliegues graduales?
   - **R:** Sí, si se configura correctamente. Usa `maxSurge` para permitir instancias extra durante el despliegue y `maxUnavailable` para limitar cuántas pueden estar caídas a la vez. Por ejemplo, con 10 réplicas, maxSurge=2, maxUnavailable=1, siempre tienes 9-12 instancias sanas sirviendo tráfico (cero tiempo de inactividad).

4) **P:** ¿Cómo manejas migraciones de base de datos con despliegues graduales?
   - **R:** Usa esquemas compatibles hacia adelante (el código antiguo y nuevo pueden leer/escribir ambos). Ejecuta las migraciones antes de desplegar el código nuevo. Para cambios complejos, usa el patrón expand/contract: expandir esquema (agregar nuevos campos), desplegar código nuevo (usa los nuevos campos), contraer esquema (remover campos antiguos) después de que todas las instancias son nuevas.

5) **P:** ¿Cuál es la diferencia entre despliegues rolling y canary?
   - **R:** 
     - **Rolling:** Reemplaza todas las instancias incrementalmente; sin control de tráfico (las instancias reciben tráfico igual).
     - **Canary:** Enruta un pequeño porcentaje de tráfico a la nueva versión; aumenta gradualmente (5% → 100%); monitorea métricas antes del despliegue completo.
     - Rolling es más simple; canary proporciona mejor mitigación de riesgos y validación.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo explicar cómo funciona el despliegue gradual (reemplazo incremental de instancias).
- [ ] Puedo describir el rol de los health checks en los despliegues graduales.
- [ ] Puedo articular el trade-off entre costo y velocidad de reversión.
- [ ] Puedo comparar rolling con despliegues blue/green y canary.
- [ ] Puedo explicar cómo configurar actualizaciones rolling sin tiempo de inactividad (maxSurge, maxUnavailable).

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)
- [Fundamentos de fiabilidad](reliability-basics.md)

### Temas relacionados
- [Despliegues blue/green](blue-green-deployments.md)
- [Despliegues canary](canary-deployments.md)
- [Feature flags](feature-flags.md)

### Comparar con
- [Despliegues blue/green](blue-green-deployments.md) — Blue/green es cambio instantáneo; rolling es reemplazo incremental.
- [Despliegues canary](canary-deployments.md) — Canary controla porcentaje de tráfico; rolling reemplaza instancias.

## Notas / Bandeja de entrada
- Agregar ejemplos de proyectos reales: actualizaciones rolling para Heimdall, estrategia de despliegue de Opportunity Actions.
- Considerar agregar sección sobre velocidad de despliegue (cómo acelerar o ralentizar los despliegues).
- Vincular a detalles de actualizaciones rolling de Kubernetes cuando se creen.
