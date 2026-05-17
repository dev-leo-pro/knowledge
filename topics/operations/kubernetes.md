---
id: kubernetes
title: "Kubernetes (platform basics)"
type: technology
status: learning
importance: 85
difficulty: medium
tags: [kubernetes, platform, containers, operations]
canonical: true
aliases: [k8s]
created_at: 2026-02-18
updated_at: 2026-02-18
---

# Kubernetes (conceptos básicos de plataforma)

## TL;DR (BLUF)
- Kubernetes orquesta contenedores para despliegues declarativos, escalables y auto-reparables; separa la unidad de ejecución (Pod) de los controladores (Deployment/StatefulSet) y el acceso estable (Service/Ingress).
- Úsalo para ejecutar microservicios en producción con autoescalado, actualizaciones graduales y enrutamiento dirigido por salud; costos: complejidad operacional y modos de fallo adicionales.

## Definición
**Qué es:** Un sistema de orquestación de contenedores de código abierto que gestiona la programación, redes, almacenamiento y ciclo de vida de contenedores a través de un clúster de máquinas.
**Términos clave:** Pod, Deployment, StatefulSet, Service, Ingress, ConfigMap, Secret, HPA, sondas de liveness/readiness, kubelet, plano de control.

## Por qué importa
- Proporciona primitivas para ejecutar cargas de trabajo containerizadas de forma fiable: reconciliación de estado deseado, autoescalado, actualizaciones graduales y descubrimiento de servicios.
- Permite a los equipos estandarizar despliegues, desacoplar infraestructura del código de aplicación y operar con mayor throughput con comportamiento predecible.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos fundamentales de Kubernetes y respuestas prácticas listas para entrevistas sobre conversaciones de plataforma.
**Fuera del alcance / NO resuelto por esto:** ejecutar un plano de control gestionado (EKS/GKE/AKS), playbooks operacionales e internos profundos de CNI (enlace a capas de red).

## Modelo mental / Intuición
- Piensa en Kubernetes como un sistema de bucle de control: declaras el estado deseado (manifiestos) y los controladores reconcilian continuamente el clúster para coincidir con él.
- Los Pods son runtimes efímeros; los controladores (Deployment/StatefulSet/DaemonSet) aseguran la continuidad y el conteo deseado; los Services proporcionan redes estables.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites programación automatizada, escalado horizontal, actualizaciones graduales o primitivas de plataforma estandarizadas entre equipos.
- Ejecutes muchos servicios que se beneficien de funcionalidades compartidas de plataforma (ingress, service mesh, observabilidad centralizada).
### Evítalo cuando
- Tu superficie de aplicación sea pequeña, el presupuesto de ops sea mínimo, o puedas usar un PaaS u oferta serverless que reduzca la carga operacional.

## Cómo lo usaría (práctico)
- **Contexto:** Desplegar una API sin estado y un worker en segundo plano.
- **Pasos:** construir imágenes de contenedor pequeñas → escribir Deployment para cada servicio (réplicas, requests/limits de recursos) → exponer vía Service ClusterIP → agregar Ingress para enrutamiento externo → definir sondas de liveness/readiness → agregar HPA basado en CPU/métricas personalizadas → usar ConfigMaps/Secrets para configuración.
- **Cómo se ve el éxito:** releases sin tiempo de inactividad con actualizaciones graduales, autoescalado bajo carga y métricas de salud claras (reinicios de pods, gating de readiness).

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** declarativo, extensible, ecosistema estándar (Helm, operadores, CNI, CSI).
- **Contras / Riesgos:** complejidad operacional, actualizaciones de versión, disponibilidad del plano de control, efectos de vecino ruidoso si los recursos no están aislados.
### Alternativas
- **PaaS / FaaS (ej., Cloud Run, Lambda):** ops más simples; mejor cuando quieres trabajo mínimo de infraestructura.
- **Kubernetes Gestionado (EKS/GKE/AKS):** reduce las ops del plano de control pero aún requiere ops de workers/infraestructura.

## Modos de fallo y errores comunes
- Requests/limits de recursos mal configurados → OOM del nodo/inestabilidad del kube-scheduler.
- Confusión entre readiness y liveness → tráfico enrutado a contenedores que no están listos o reiniciados innecesariamente.
- Plano de control sobrecargado durante grandes despliegues → throttling de API / reconciliación lenta.
- Cargas de trabajo con estado colocadas sin planificación de PersistentVolume → pérdida de datos o modos de acceso incorrectos.

## Observabilidad (Cómo detectar problemas)
**Métricas:** CPU/memoria de pods, conteo de reinicios de pods, réplicas disponibles vs deseadas, latencia del API server, longitud de la cola del scheduler, eventos de escala del HPA.
**Logs:** logs de kubelet y controller-manager, logs de pods (aplicación), errores de plugins CSI/CNI.
**Trazas:** tiempos de colocación/reconciliación para controladores, rutas de petición a través de Ingress/Service Mesh.
**Alertas:** pod en crashloop > N, alta presión de recursos asignables del nodo, HPA oscilando, fallos de aprovisionamiento de volúmenes persistentes.

## Notas de implementación (si aplica)
**Checklist**
- [ ] Definir requests y limits de recursos para pods.
- [ ] Agregar sondas de liveness/readiness que reflejen la preparación real.
- [ ] Usar ConfigMap/Secret para configuración externa y credenciales.
- [ ] Usar Deployment para sin estado, StatefulSet para con estado con identidad estable.
- [ ] Usar actualizaciones graduales y gating de readiness para cero tiempo de inactividad.

**Notas de seguridad / cumplimiento**
- Ejecutar pods con privilegio mínimo (`runAsNonRoot`, PodSecurityPolicies / alternativas de PSP), políticas de red para restringir tráfico, y cifrar Secrets en reposo cuando sea posible.

**Notas de rendimiento**
- Dimensionar correctamente los requests para mejorar el bin-packing; establecer limits para prevenir vecinos ruidosos; usar autoescalado vertical/horizontal con cooldowns sensatos.

**Notas operacionales**
- Mantener kube-apiserver disponible (plano de control HA), monitorear salud de etcd y respaldos, practicar actualizaciones de clúster en staging.

## Mini ejemplo (YAML)
```yaml
# Deployment (sin estado)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-api
  template:
    metadata:
      labels:
        app: my-api
    spec:
      containers:
        - name: my-api
          image: my-api:latest
          ports:
            - containerPort: 8080
          readinessProbe:
            httpGet:
              path: /ready
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 5

# Service (acceso estable)
---
apiVersion: v1
kind: Service
metadata:
  name: my-api-svc
spec:
  selector:
    app: my-api
  ports:
    - port: 80
      targetPort: 8080
  type: ClusterIP
```

## Anti-patrones comunes
- **Anti-patrón:** Ejecutar bases de datos en Pods efímeros sin StatefulSet+PVC.
  - **Por qué es malo:** pérdida de datos y problemas de ordenamiento.
  - **Mejor enfoque:** usar BDs gestionadas o StatefulSets con PVs estables y respaldos.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Kubernetes gestiona contenedores a través de máquinas: los Pods ejecutan contenedores, los Deployments aseguran que un conjunto deseado de Pods exista, y los Services proporcionan un endpoint de red estable para alcanzar esos Pods.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cuál es la diferencia entre un Pod y un Deployment?
   - **R:** Pod es la unidad de ejecución; Deployment es un controlador que asegura que el número deseado de Pods exista y gestiona actualizaciones/rollbacks.
2) **P:** ¿Cuándo usarías un StatefulSet en vez de un Deployment?
   - **R:** Cuando necesites IDs de red estables y almacenamiento persistente por réplica (bases de datos, brokers Kafka) y semánticas de inicio/parada ordenadas.
3) **P:** ¿Por qué son importantes las sondas de readiness para actualizaciones graduales?
   - **R:** Las sondas de readiness previenen que el tráfico llegue a un Pod hasta que esté listo; durante actualizaciones graduales, solo los Pods listos reciben tráfico, habilitando cero tiempo de inactividad.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar Pod vs Deployment vs Service en una oración cada uno.
- [ ] Puedo esbozar un YAML de Deployment + Service de memoria.
- [ ] Puedo listar cuándo elegir StatefulSet vs Deployment.
- [ ] Puedo explicar sondas de readiness vs liveness y por qué difieren.
- [ ] Puedo nombrar al menos 3 alertas operacionales para un clúster.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de redes](../operations/networking-basics.md)
- [Patrón Sidecar](../operations/sidecar.md)

### Temas relacionados
- [Despliegues blue/green](../operations/blue-green-deployments.md)
- [Despliegues canary](../operations/canary-deployments.md)
- [Despliegues graduales](../operations/rolling-deployments.md)
- [Descubrimiento de servicios](../operations/service-discovery.md)

### Comparar con
- [Terraform](../operations/terraform.md) — Terraform define infraestructura; Kubernetes gestiona cargas de trabajo en ejecución.

## Notas / Bandeja de entrada (opcional)
- Considerar agregar una guía corta de comandos comunes de `kubectl` y un checklist de actualización de una página.
