---
id: canary-deployments
title: "Despliegues Canary"
type: pattern
status: learning
importance: 80
difficulty: medium
tags: [deployment, reliability, progressive-rollout, risk-management]
canonical: true
aliases: [canary-release, progressive-deployment]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Despliegues Canary

## TL;DR (BLUF)
- El despliegue canary enruta gradualmente porcentajes crecientes de tráfico a una nueva versión (5% -> 10% -> 50% -> 100%), validando en cada paso.
- Usar para mitigación de riesgos: detectar problemas con un radio de explosión pequeño antes del despliegue completo.
- Trade-off clave: despliegue más lento (seguridad) vs. despliegue instantáneo (velocidad).

## Definición
**Qué es:** Una estrategia de despliegue que incrementa gradualmente el tráfico a una nueva versión mientras monitorea problemas, permitiendo rollback antes del despliegue completo si surgen problemas.  
**Términos clave:** canary (nueva versión con poco tráfico), baseline (versión actual), despliegue progresivo, radio de explosión, división de tráfico, rollback automatizado, métricas de salud.

## Por qué importa
- **Mitigación de riesgos:** Los problemas afectan solo a un pequeño porcentaje de usuarios (5% vs. 100%).
- **Detección temprana:** Detectar bugs, problemas de rendimiento o caídas de métricas de negocio antes del despliegue completo.
- **Decisiones basadas en datos:** Monitorear tráfico real de producción para validar cambios.
- **Coste-efectivo:** No necesitas 2x infraestructura (a diferencia de blue/green).
- **Relevancia en entrevistas:** Patrón estándar en diseño de sistemas para despliegues seguros.

## Alcance y no-objetivos
**Dentro del alcance:**
- Mecánica del despliegue canary (división de tráfico, despliegue progresivo).
- Métricas de salud para validación (tasa de errores, latencia, métricas de negocio).
- Disparadores de rollback automatizado.

**Fuera del alcance / NO resuelto por esto:**
- Implementaciones específicas de división de tráfico (service mesh, balanceador de carga — las herramientas varían).
- Feature flags (patrón separado pero complementario).
- Migraciones de base de datos (desafío separado).

## Modelo mental / Intuición
Piensa en los despliegues canary como **probar una mina de carbón con un canario**:
- Los mineros enviaban un canario a la mina primero (sensible al gas tóxico).
- Si el canario moría, sabían que la mina no era segura.
- En software: enviar 5% del tráfico a la nueva versión (el canary).
- Si las métricas se degradan, hacer rollback antes de exponer a todos los usuarios.

El "canary" es una muestra pequeña y representativa que valida la seguridad para todos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La mitigación de riesgos es crítica (servicios orientados al cliente, alto tráfico).
- Necesitas validar con tráfico real de producción (no solo tests).
- El coste de infraestructura importa (no puedes permitirte 2x para blue/green).
- Los cambios son arriesgados (refactorizaciones mayores, nuevas funcionalidades, cambios de rendimiento).

### Evítalo cuando
- El despliegue debe ser instantáneo (canary es gradual; usar blue/green en su lugar).
- El tráfico es demasiado bajo para obtener señal significativa (no puedes validar con 5% de 10 usuarios/día).
- La infraestructura no soporta división de tráfico (sin balanceador de carga/service mesh).

## Cómo lo usaría (práctico)
- **Contexto:** Desplegando una nueva versión de un servicio API en Go sobre Kubernetes con 10,000 pet/min.
- **Pasos:**
  1. **Desplegar canary (5% tráfico):**
     - Desplegar nueva versión (`api-v2`) con 1 pod.
     - Configurar balanceador de carga: 95% a `api-v1`, 5% a `api-v2`.
     - Monitorear durante 10 minutos: tasa de errores, latencia P95, métricas de negocio (conversiones).
  2. **Aumentar a 10%:**
     - Si las métricas son estables: escalar `api-v2` a 2 pods, ajustar tráfico a 90/10.
     - Monitorear durante 10 minutos.
  3. **Aumentar a 50%:**
     - Si es estable: escalar `api-v2` a 5 pods, ajustar tráfico a 50/50.
     - Monitorear durante 30 minutos (muestra más grande).
  4. **Despliegue completo (100%):**
     - Si es estable: escalar `api-v2` a 10 pods, enrutar 100% del tráfico.
     - Monitorear durante 1 hora; luego eliminar `api-v1`.
  5. **Rollback automatizado:**
     - Si tasa de errores >0.5% o latencia P95 >500ms en cualquier etapa: auto-revertir a 100% `api-v1`.
- **Cómo se ve el éxito:** Despliegue gradual en 1 hora; cero incidentes visibles para el usuario; auto-rollback si las métricas se degradan.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Bajo riesgo (radio de explosión pequeño; detectar problemas temprano).
  - Coste-efectivo (sin 2x infraestructura; canary corre junto al baseline).
  - Basado en datos (validar con tráfico real de producción).
  - Flexible (puede pausar, hacer rollback o acelerar el despliegue).
- **Desventajas / Riesgos:**
  - Despliegue más lento (gradual vs. instantáneo).
  - Complejidad (requiere división de tráfico y monitoreo).
  - Desfase de versión (dos versiones ejecutándose concurrentemente; necesita compatibilidad de API).
  - Bajo tráfico invalida la señal (no puedes validar con 5% de 10 usuarios).

### Alternativas
- **[Despliegues blue/green](blue-green-deployments.md):** Cutover instantáneo al 100% (más rápido pero más arriesgado).
- **Despliegues rolling:** Reemplazar instancias una por una (más simple pero rollback más lento).
- **Feature flags:** Desplegar código pero mantener funcionalidades desactivadas; habilitar gradualmente (desacopla despliegue de release).

### Cómo elegir
- **Sensible al riesgo + coste-efectivo:** Canary.
- **Cero tiempo de inactividad + rollback instantáneo:** Blue/green.
- **Servicios simples, riesgo mínimo:** Rolling.
- **Habilitación gradual de funcionalidades:** Feature flags + canary.

## Modos de fallo y trampas
- **Monitoreo insuficiente:** Desplegar canary sin rastrear métricas; perder problemas hasta el despliegue completo.
- **Despliegue demasiado rápido:** Aumentar tráfico demasiado rápido (ej., 5% -> 100%); perder problemas que aparecen a escala.
- **Sin rollback automatizado:** Intervención manual requerida; respuesta lenta a problemas.
- **Incompatibilidad de versión:** Canary y baseline no pueden coexistir (cambios de API incompatibles, problemas de estado compartido).
- **Bajo tráfico valida pobremente:** 5% de 100 usuarios/día = 5 usuarios; no es suficiente para detectar problemas.

## Observabilidad (Cómo detectar problemas)
- **Métricas (comparar canary con baseline):**
  - Tasa de errores (% de peticiones fallando; señalar si canary > baseline).
  - Latencia (P95, P99; señalar si canary es significativamente más lento).
  - Métricas de negocio (tasa de conversión, éxito de checkout; señalar caídas).
  - Utilización de recursos (CPU, memoria; señalar si canary usa más).
- **Logs:** Comparar logs de errores entre canary y baseline (buscar nuevos errores).
- **Trazas:** Validar que canary maneja peticiones correctamente (sin picos de timeout, lógica correcta).
- **Alertas:** Disparadores de rollback automatizado (tasa de errores >0.5%, latencia >500ms, etc.).

## Notas de implementación
- **Checklist**
  - [ ] Definir métricas de salud (tasa de errores, latencia, métricas de negocio).
  - [ ] Establecer umbrales de rollback (ej., tasa de errores >0.5%, latencia >500ms).
  - [ ] Desplegar canary (5% tráfico) y monitorear durante 10 minutos.
  - [ ] Aumentar tráfico gradualmente (10%, 25%, 50%, 100%) con validación en cada paso.
  - [ ] Automatizar rollback (si las métricas se degradan, revertir a 100% baseline).
  - [ ] Mantener baseline ejecutándose hasta que canary llegue a 100% y se estabilice.
  - [ ] Eliminar baseline después del período de validación.

- **Notas de seguridad / cumplimiento**
  - Asegurar que canary y baseline tienen postura de seguridad idéntica.
  - Auditar decisiones de despliegue (quién aprobó, qué métricas dispararon el rollback).

- **Notas de rendimiento**
  - Calentar instancias canary (pre-cargar cachés, conexiones).
  - Monitorear uso de recursos (canary puede tener características de rendimiento diferentes).

- **Notas operativas**
  - Documentar plan de despliegue (porcentajes de tráfico, duraciones, disparadores de rollback).
  - Runbook para rollback manual (si la automatización falla).

## Mini ejemplo (Kubernetes + Istio)
```yaml
# Baseline deployment (v1)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-v1
spec:
  replicas: 10
  selector:
    matchLabels:
      app: api
      version: v1
  template:
    metadata:
      labels:
        app: api
        version: v1
    spec:
      containers:
      - name: api
        image: myapp:v1.0

---
# Canary deployment (v2)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-v2
spec:
  replicas: 1  # Start small
  selector:
    matchLabels:
      app: api
      version: v2
  template:
    metadata:
      labels:
        app: api
        version: v2
    spec:
      containers:
      - name: api
        image: myapp:v2.0

---
# Istio VirtualService (traffic splitting)
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: api
spec:
  hosts:
  - api.example.com
  http:
  - match:
    - uri:
        prefix: /
    route:
    - destination:
        host: api
        subset: v1
      weight: 95  # Baseline gets 95%
    - destination:
        host: api
        subset: v2
      weight: 5   # Canary gets 5%
```

## Anti-patrones comunes
- **Anti-patrón:** Sin monitoreo durante el despliegue canary.
  - **Por qué es malo:** Los problemas pasan desapercibidos hasta el despliegue completo; anula el propósito del canary.
  - **Mejor enfoque:** Definir métricas, monitorear continuamente, automatizar rollback.

- **Anti-patrón:** Saltar de 5% a 100% sin pasos intermedios.
  - **Por qué es malo:** Los problemas que aparecen a escala (10% o 50% de tráfico) se pierden.
  - **Mejor enfoque:** Despliegue gradual (5% -> 10% -> 25% -> 50% -> 100%) con validación en cada paso.

- **Anti-patrón:** Solo rollback manual.
  - **Por qué es malo:** Tiempo de respuesta lento; los problemas afectan a más usuarios mientras se espera intervención humana.
  - **Mejor enfoque:** Disparadores de rollback automatizado (umbrales de tasa de errores, latencia).

- **Anti-patrón:** Despliegue canary con cambios de API incompatibles.
  - **Por qué es malo:** Canary y baseline no pueden coexistir (los clientes fallan en uno u otro).
  - **Mejor enfoque:** Asegurar compatibilidad de API; usar endpoints versionados si se necesitan cambios incompatibles.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
El despliegue canary es una estrategia de despliegue gradual donde empiezas enviando un pequeño porcentaje de tráfico (ej., 5%) a una nueva versión mientras mantienes el resto en la versión actual. Monitoreas métricas clave — tasa de errores, latencia, métricas de negocio — y solo aumentas el tráfico si el canary está sano. Si las métricas se degradan, haces rollback automáticamente al baseline. El proceso continúa incrementalmente (5% -> 10% -> 50% -> 100%) hasta el despliegue completo. El principal beneficio es la mitigación de riesgos: los problemas afectan solo a un pequeño porcentaje de usuarios, dándote tiempo para detectarlos y arreglarlos antes de que todos se vean afectados. Es coste-efectivo (sin 2x infraestructura como blue/green) pero más lento.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cómo decides qué porcentaje de tráfico enrutar al canary?
   - **R:** Empezar pequeño (5% o menos) para limitar el radio de explosión. Aumentar gradualmente basándose en el volumen de tráfico y la confianza. Los servicios de alto tráfico pueden validar rápidamente con 5%; los servicios de bajo tráfico pueden necesitar 10-25% para señal significativa. Aumentar cuando las métricas son estables (tasa de errores, latencia, métricas de negocio dentro de umbrales).

2) **P:** ¿Qué métricas monitoreas durante un despliegue canary?
   - **R:** 
     - **Técnicas:** Tasa de errores (5xx, 4xx), latencia (P95, P99), throughput.
     - **Recursos:** CPU, memoria, uso del pool de conexiones.
     - **Negocio:** Tasa de conversión, éxito de checkout, uso de funcionalidades.
     - Comparar canary con baseline; señalar si canary es significativamente peor.

3) **P:** ¿En qué se diferencia canary de A/B testing?
   - **R:** 
     - **Canary:** Mecanismo de seguridad de despliegue. El objetivo es validar que la nueva versión funciona correctamente (sin errores, latencia OK). El éxito significa despliegue al 100%.
     - **A/B testing:** Experimento de producto. El objetivo es medir impacto en el negocio (¿la variante B aumenta conversiones?). El éxito significa aprendizaje, no necesariamente despliegue.
     - Ambos usan división de tráfico, pero con diferentes objetivos.

4) **P:** ¿Qué pasa si el canary se ve sano al 5% pero falla al 50%?
   - **R:** Esto valida la estrategia canary. Los problemas que aparecen a escala (carga de base de datos, agotamiento del pool de conexiones, fugas de memoria) se detectan antes del despliegue completo. Hacer rollback al baseline, investigar, arreglar y reintentar. El despliegue gradual previene que el 100% de los usuarios experimenten el problema.

5) **P:** ¿Se puede combinar canary con despliegues blue/green?
   - **R:** Sí. Desplegar entorno green, luego hacer canary del tráfico de blue a green (5% -> 100%). Una vez validado, hacer cutover completo a green. Esto combina el rollback instantáneo de blue/green con la validación gradual de canary. Más complejo pero muy seguro.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar cómo funciona el despliegue canary (aumento gradual de tráfico).
- [ ] Puedo nombrar al menos 3 métricas para monitorear durante el despliegue canary.
- [ ] Puedo describir disparadores de rollback automatizado y por qué son importantes.
- [ ] Puedo comparar canary con blue/green (trade-offs, cuándo usar cada uno).
- [ ] Puedo explicar cómo manejar incompatibilidad de versiones en despliegues canary.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)
- [Fundamentos de fiabilidad](reliability-basics.md)
- [Fundamentos de observabilidad](observability-basics.md)

### Temas relacionados
- [Despliegues blue/green](blue-green-deployments.md)
- [Feature flags](feature-flags.md)
- [Despliegues rolling](rolling-deployments.md)

### Comparar con
- [Despliegues blue/green](blue-green-deployments.md) — Blue/green es cambio instantáneo; canary es despliegue gradual.

## Notas / Bandeja de entrada
- Añadir ejemplos de proyectos reales: despliegue canary de cambios en Heimdall, estrategia de despliegue de Opportunity Actions.
- Considerar añadir sección sobre herramientas de análisis canary (Kayenta, Flagger).
- Enlazar a implementaciones de service mesh (Istio, Linkerd) cuando se creen.
