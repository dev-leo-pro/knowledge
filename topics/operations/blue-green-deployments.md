---
id: blue-green-deployments
title: "Despliegues Blue/Green"
type: pattern
status: learning
importance: 75
difficulty: medium
tags: [deployment, reliability, zero-downtime, operations]
canonical: true
aliases: [blue-green, red-black-deployment]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Despliegues Blue/Green

## TL;DR (BLUF)
- El despliegue blue/green mantiene dos entornos de producción idénticos (blue = actual, green = nuevo); cambia el tráfico instantáneamente entre ellos.
- Usar para despliegues sin tiempo de inactividad con capacidad de rollback instantáneo.
- Trade-off clave: coste de infraestructura (2x entornos) vs. seguridad y velocidad de despliegue.

## Definición
**Qué es:** Una estrategia de despliegue que usa dos entornos de producción idénticos donde solo uno sirve tráfico en vivo a la vez, permitiendo cambio instantáneo entre versiones.  
**Términos clave:** entorno blue (producción actual), entorno green (nueva versión), cutover (cambio de tráfico), rollback, despliegue sin tiempo de inactividad, balanceador de carga, cambio de [DNS](dns.md).

## Por qué importa
- **Cero tiempo de inactividad:** Los usuarios no experimentan interrupción del servicio durante los despliegues.
- **Rollback instantáneo:** Si surgen problemas, volver al entorno blue en segundos.
- **Pruebas seguras:** Validar el entorno green con carga de producción antes del cutover.
- **Proceso simplificado:** El despliegue es solo un cambio de tráfico (sin migración compleja).
- **Relevancia en entrevistas:** Patrón común en discusiones de diseño de sistemas sobre estrategias de despliegue.

## Alcance y no-objetivos
**Dentro del alcance:**
- Mecánica del despliegue blue/green (dos entornos, cambio de tráfico).
- Cuándo usar blue/green vs. otras estrategias.
- Mecanismos de cutover (balanceador de carga, [DNS](dns.md), feature flags).

**Fuera del alcance / NO resuelto por esto:**
- Migraciones de base de datos (desafío separado; requiere esquemas compatibles hacia adelante).
- Estrategias de optimización de costes (blue/green es costoso).
- Implementaciones específicas de plataforma cloud (AWS, GCP, Azure — aunque los conceptos aplican).

## Modelo mental / Intuición
Piensa en blue/green como **dos escenarios idénticos con un foco**:
- **Escenario blue:** Actualmente iluminado (sirviendo tráfico de producción).
- **Escenario green:** Oscuro (nueva versión lista pero sin servir tráfico).
- **Despliegue:** Cambiar el foco de blue a green (cutover instantáneo).
- **Rollback:** Cambiar el foco de vuelta a blue (reversión instantánea).

Nadie nota el cambio porque ambos escenarios son infraestructura idéntica.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El cero tiempo de inactividad es crítico (servicios orientados al cliente, SLAs).
- La velocidad de rollback es esencial (capacidad de revertir en segundos).
- Se necesita validación del despliegue (smoke tests en green antes del cutover).
- El coste de infraestructura es aceptable (2x recursos durante el despliegue).

### Evítalo cuando
- El coste de infraestructura es prohibitivo (startup, servicios de pequeña escala).
- Las migraciones de base de datos son complejas (los cambios de esquema requieren planificación cuidadosa).
- Los despliegues canary son más adecuados (se prefiere despliegue gradual).
- Los recursos son limitados (no puedes permitirte 2x entornos).

## Cómo lo usaría (práctico)
- **Contexto:** Desplegando un servicio API en Go sobre Kubernetes con base de datos PostgreSQL.
- **Pasos:**
  1. **Entorno blue (actual):**
     - Deployment: `api-blue` con 10 pods sirviendo tráfico.
     - Service: `api-service` enruta a `api-blue`.
     - Base de datos: `production-db` (compartida entre blue/green).
  2. **Desplegar entorno green:**
     - Crear nuevo deployment: `api-green` con 10 pods.
     - Ejecutar smoke tests contra green (health checks, endpoints críticos).
     - Monitorear green durante 5-10 minutos (verificar métricas, logs).
  3. **Cutover (cambio de tráfico):**
     - Actualizar selector del service: `api-service` ahora enruta a `api-green`.
     - El tráfico cambia instantáneamente (los usuarios ahora llegan a green).
     - Monitorear green de cerca (tasa de errores, latencia).
  4. **Post-cutover:**
     - Si hay problemas: revertir service a `api-blue` (rollback instantáneo).
     - Si es estable: mantener `api-blue` ejecutándose durante 24 horas (opción de respaldo).
     - Después de validación: eliminar `api-blue` (ahorrar costes).
- **Cómo se ve el éxito:** Despliegue en <10 minutos; cero tiempo de inactividad para el usuario; rollback en <30 segundos si es necesario.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Cero tiempo de inactividad (experiencia de usuario sin interrupciones).
  - Rollback instantáneo (volver en segundos).
  - Validación segura (probar green antes del cutover).
  - Cutover simple (solo cambiar el enrutamiento).
- **Desventajas / Riesgos:**
  - Alto coste de infraestructura (2x entornos durante el despliegue).
  - Complejidad de base de datos (los cambios de esquema deben ser compatibles hacia adelante).
  - Los servicios con estado son desafiantes (estado de sesión, trabajo en progreso).
  - Desperdicio de recursos (entorno blue inactivo después del cutover).

### Alternativas
- **[Despliegues canary](canary-deployments.md):** Despliegue gradual a un subconjunto del tráfico (menor riesgo, menor coste).
- **Despliegues rolling:** Reemplazar instancias una por una (sin coste extra pero rollback más lento).
- **Despliegue recreate:** Detener antiguo, iniciar nuevo (simple pero tiene tiempo de inactividad).

### Cómo elegir
- **Cero tiempo de inactividad + rollback instantáneo requerido:** Blue/green.
- **Sensible al coste o validación gradual preferida:** Canary.
- **Servicios simples, tiempo de inactividad aceptable:** Rolling o recreate.
- **Estado complejo o migraciones de BD:** Considerar feature flags + rolling.

## Modos de fallo y trampas
- **Incompatibilidad de base de datos:** Green usa nuevo esquema; blue no puede leerlo (rompe el rollback).
- **Problemas de estado compartido:** Sesiones, cachés, colas causan inconsistencias entre blue/green.
- **Sin smoke tests en green:** Cambiar a un entorno roto; descubrir problemas en producción.
- **Eliminación prematura de blue:** Eliminar blue demasiado pronto; no se puede hacer rollback cuando aparecen problemas después.
- **Retrasos en cutover por [DNS](dns.md):** La caché de DNS causa cambio gradual en lugar de instantáneo (usar balanceador de carga en su lugar).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tasa de errores (monitorear green durante y después del cutover; comparar con baseline de blue).
  - Latencia (P95/P99; señalar picos en green).
  - Distribución de tráfico (asegurar 100% a green después del cutover).
  - Utilización de recursos (CPU, memoria en ambos entornos).
- **Logs:** Comparar logs de errores entre blue y green (buscar nuevos errores).
- **Trazas:** Validar que green maneja peticiones correctamente (sin picos de timeout).
- **Alertas:** Pico en tasa de errores, aumento de latencia, fallos de health check en green.

## Notas de implementación
- **Checklist**
  - [ ] Asegurar que el esquema de base de datos es compatible hacia adelante (green y blue pueden leer/escribir).
  - [ ] Desplegar entorno green (idéntico a blue pero con nuevo código).
  - [ ] Ejecutar smoke tests en green (health checks, endpoints críticos).
  - [ ] Monitorear green durante 5-10 minutos antes del cutover.
  - [ ] Cambiar tráfico de blue a green (balanceador de carga o selector de service).
  - [ ] Monitorear green de cerca post-cutover (tasa de errores, latencia, logs).
  - [ ] Mantener blue ejecutándose durante período de respaldo (24 horas recomendado).
  - [ ] Eliminar blue después de validación (ahorrar costes).

- **Notas de seguridad / cumplimiento**
  - Asegurar que ambos entornos tienen postura de seguridad idéntica (configs, secretos, roles IAM).
  - Auditar eventos de cutover (quién cambió el tráfico, cuándo).

- **Notas de rendimiento**
  - Calentar entorno green antes del cutover (poblar cachés, pre-cargar datos).
  - Usar connection draining en blue (cierre elegante de peticiones en vuelo).

- **Notas operativas**
  - Automatizar cutover (scripts, CI/CD) para reducir error humano.
  - Documentar procedimiento de rollback (debe ser ejecutable bajo presión).

## Mini ejemplo (Kubernetes)
```yaml
# Blue deployment (current production)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-blue
spec:
  replicas: 10
  selector:
    matchLabels:
      app: api
      version: blue
  template:
    metadata:
      labels:
        app: api
        version: blue
    spec:
      containers:
      - name: api
        image: myapp:v1.0

---
# Green deployment (new version)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-green
spec:
  replicas: 10
  selector:
    matchLabels:
      app: api
      version: green
  template:
    metadata:
      labels:
        app: api
        version: green
    spec:
      containers:
      - name: api
        image: myapp:v2.0

---
# Service (controls traffic routing)
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  selector:
    app: api
    version: blue  # Switch to "green" for cutover
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080

# Cutover: kubectl patch svc api-service -p '{"spec":{"selector":{"version":"green"}}}'
# Rollback: kubectl patch svc api-service -p '{"spec":{"selector":{"version":"blue"}}}'
```

## Anti-patrones comunes
- **Anti-patrón:** Sin smoke tests en green antes del cutover.
  - **Por qué es malo:** Cambiar a un entorno roto; descubrir problemas en producción.
  - **Mejor enfoque:** Ejecutar smoke tests, monitorear green durante 5-10 minutos antes del cutover.

- **Anti-patrón:** Esquema de base de datos incompatible entre blue y green.
  - **Por qué es malo:** El rollback a blue falla (no puede leer el esquema de green).
  - **Mejor enfoque:** Usar esquemas compatibles hacia adelante (blue puede leer los datos de green, aunque no use los campos nuevos).

- **Anti-patrón:** Eliminación instantánea de blue después del cutover.
  - **Por qué es malo:** Los problemas pueden aparecer horas después; no se puede hacer rollback.
  - **Mejor enfoque:** Mantener blue ejecutándose durante 24 horas (o más para sistemas críticos).

- **Anti-patrón:** Usar [DNS](dns.md) para el cutover.
  - **Por qué es malo:** La caché de DNS causa un cambio lento e impredecible (no instantáneo).
  - **Mejor enfoque:** Usar balanceador de carga o service mesh para cambio de tráfico instantáneo.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
El despliegue blue/green usa dos entornos de producción idénticos. Blue es la versión actual sirviendo tráfico, y green es la nueva versión que está desplegada pero aún no activa. Cuando green está listo, cambias el tráfico de blue a green instantáneamente — típicamente cambiando la configuración de un balanceador de carga o el selector de un Service de Kubernetes. Si surgen problemas, vuelves a blue en segundos. Los principales beneficios son cero tiempo de inactividad y rollback instantáneo. El principal coste es ejecutar dos entornos completos durante el despliegue. Es ideal cuando el tiempo de inactividad es inaceptable y la velocidad de rollback es crítica.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cómo manejas las migraciones de base de datos en despliegues blue/green?
   - **R:** Usar esquemas compatibles hacia adelante. Los cambios de esquema de green deben permitir que blue siga leyendo/escribiendo (ej., añadir columnas nullable, no eliminar columnas). Ejecutar migraciones antes de desplegar green. Para migraciones complejas, usar el patrón expand/contract: expandir esquema (añadir nuevos campos), desplegar green (usa los nuevos campos), contraer esquema (eliminar campos antiguos) después de retirar blue.

2) **P:** ¿Cuál es la diferencia entre despliegues blue/green y canary?
   - **R:** Blue/green cambia el 100% del tráfico de la versión antigua a la nueva instantáneamente. Canary enruta un pequeño porcentaje a la nueva versión gradualmente (5%, 10%, 50%, 100%). Blue/green tiene cutover y rollback más rápidos pero mayor coste (2x infraestructura). Canary tiene menor coste y riesgo (validación gradual) pero despliegue más lento.

3) **P:** ¿Cómo manejas servicios con estado (sesiones, WebSockets) en blue/green?
   - **R:** Opciones: (1) Usar sticky sessions (enrutar usuario al mismo entorno hasta que expire la sesión). (2) Almacén de sesiones externo (Redis) compartido por blue y green. (3) Connection draining elegante (dejar que las conexiones existentes terminen en blue antes de cambiar). (4) Reconexión del cliente (WebSockets se reconectan a green después del cutover).

4) **P:** ¿No es blue/green un desperdicio (2x coste de infraestructura)?
   - **R:** Sí, es costoso. Blue y green se ejecutan ambos durante el despliegue. Después del cutover, blue está inactivo (pero se mantiene para rollback). Para escenarios sensibles al coste, usar despliegues canary o rolling en su lugar. Blue/green se justifica cuando el cero tiempo de inactividad y el rollback instantáneo son críticos.

5) **P:** ¿Puede blue/green funcionar con microservicios?
   - **R:** Sí, pero es complejo. Cada servicio necesita entornos blue/green, y debes coordinar el cutover entre servicios. Alternativa: usar despliegues canary por servicio o feature flags para desacoplar despliegue de release.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar cómo funciona el despliegue blue/green (dos entornos, cambio de tráfico).
- [ ] Puedo describir el proceso de cutover y el mecanismo de rollback.
- [ ] Puedo articular el trade-off entre coste y seguridad.
- [ ] Puedo explicar cómo manejar migraciones de base de datos en blue/green.
- [ ] Puedo comparar blue/green con despliegues canary y rolling.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)
- [Fundamentos de fiabilidad](reliability-basics.md)

### Temas relacionados
- [Despliegues canary](canary-deployments.md)
- [Despliegues rolling](rolling-deployments.md)
- [Feature flags](feature-flags.md)

### Comparar con
- [Despliegues canary](canary-deployments.md) — Canary es despliegue gradual; blue/green es cambio instantáneo.

## Notas / Bandeja de entrada
- Añadir ejemplos de proyectos reales: desplegando Heimdall con blue/green, estrategia de cutover de Opportunity Actions.
- Considerar añadir sección sobre blue/green con feature flags (desacoplar despliegue de release).
- Enlazar a implementaciones específicas de cloud (AWS ECS blue/green, estrategias de Kubernetes) cuando se creen.
