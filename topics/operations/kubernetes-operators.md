---
id: kubernetes-operators
title: "Operadores de Kubernetes (controladores personalizados)"
type: technology
status: learning
importance: 78
difficulty: medium
tags: [kubernetes, operators, controller, go, platform]
canonical: true
aliases: [operator]
created_at: 2026-02-18
updated_at: 2026-02-18
---

# Operadores de Kubernetes (controladores personalizados)

## TL;DR (BLUF)
- Los Operadores son controladores de Kubernetes que extienden la API con Custom Resource Definitions (CRDs) y automatizan conocimiento operacional (desplegar, respaldar, escalar, sanar) para aplicaciones complejas. La mayoría de los operadores en producción están escritos en Go usando controller-runtime / Operator SDK porque Go se adapta bien a las APIs de Kubernetes y a los patrones de concurrencia.

## Definición
**Qué es:** Un Operador es un controlador de Kubernetes emparejado con CRDs que codifica lógica operacional específica del dominio (reconciliación) para gestionar el ciclo de vida de una aplicación automáticamente.
**Términos clave:** Controller, bucle de reconciliación, CRD (CustomResourceDefinition), Operator SDK, controller-runtime, finalizers, informers.

## Por qué importa
- Los Operadores permiten a los ingenieros de plataforma capturar runbooks como código: automatizar operaciones del día 2 (respaldo/restauración, escalado, despliegues de configuración) y proporcionar abstracciones de mayor nivel para los equipos de aplicaciones.
- Reducen el trabajo manual, mejoran la fiabilidad y hacen que las cargas de trabajo con estado complejas sean cloud-native y gestionables mediante kubectl y GitOps.

## Alcance y no-objetivos
**Dentro del alcance:** Diseño de CRD, patrones de reconciliación, finalizers, elección de líder, pruebas de operadores y cómo Go encaja en el desarrollo de operadores.
**Fuera del alcance / NO resuelto por esto:** reemplazar una plataforma de orquestación; los operadores automatizan el ciclo de vida de la aplicación pero no eliminan la necesidad de monitoreo, planificación de capacidad o gobernanza adecuada de la plataforma.

## Modelo mental / Intuición
- Piensa en un Operador como un demonio Unix que observa recursos y fuerza el estado deseado: observa objetos CR, calcula el delta y ejecuta pasos imperativos (crear/actualizar recursos hijos) hasta que el mundo coincida con la especificación deseada.

## Reglas de decisión (Cuándo construir un Operador)
### Construye un Operador cuando
- La complejidad operacional es alta (ciclo de vida con estado, respaldos, actualizaciones graduales elegantes, cambios de configuración complejos).
- Necesitas automatizaciones consistentes entre clústeres y equipos, y quieres exponer una API de mayor nivel a los propietarios de aplicaciones.
### Evita construir un Operador cuando
- Las acciones operacionales son simples (aplicaciones sin estado donde Deployments + Helm son suficientes), o no tienes capacidad para mantenerlo a largo plazo.

## Cómo lo usaría (práctico)
- **Contexto:** Automatizar el ciclo de vida de Postgres (provisionar, respaldar, failover).
- **Pasos:**
  1. Definir un CRD `PostgresCluster` con spec (réplicas, storageClass, backupPolicy).
  2. Implementar el bucle de reconciliación del controlador: observar `PostgresCluster`, asegurar que StatefulSet, Services y respaldos existan y estén configurados.
  3. Agregar finalizers para manejar limpieza y acciones asíncronas.
  4. Escribir pruebas e2e y ejecutar el operador via Operator SDK en un clúster de staging.
  5. Empaquetar el operador como una imagen y entregar via Helm/OLM.

## Cómo está involucrado Go (por qué Go para Operadores)
- El plano de control de Kubernetes y las bibliotecas cliente (`client-go`, `controller-runtime`) son bibliotecas nativas de Go; son maduras, eficientes y proporcionan APIs fuertemente tipadas y primitivas de concurrencia idiomáticas (goroutines, channels) que hacen que el código de reconciliación sea directo.
- Los binarios de Go son ejecutables estáticos únicos fáciles de contenerizar (imágenes base pequeñas como distroless), y las características de rendimiento (baja latencia, bajo impacto de GC) se adaptan a controladores de larga ejecución.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** Los Operadores proporcionan automatización potente, se integran estrechamente con los eventos de K8s y exponen APIs claras para los equipos de aplicaciones.
- **Desventajas:** Construir y operar operadores requiere mantenimiento, pruebas y gestión del ciclo de vida; errores en los controladores pueden causar impacto generalizado en el clúster.
### Alternativas
- **Helm charts + flujos de CI/CD:** más simples para despliegue; menos capaces para automatización continua.
- **Automatización externa (Terraform, scripts):** pueden orquestar recursos pero no obtendrán semánticas de reconciliación dirigida por eventos dentro del clúster.

## Modos de fallo y trampas
- La reconciliación insegura (por ejemplo, actualizaciones destructivas) puede causar pérdida de datos — incluye verificaciones de seguridad y soporte de dry-run.
- Condiciones de carrera del operador si los controladores no usan elección de líder en configuraciones multi-réplica.
- Reintentos ilimitados en errores transitorios pueden causar bucles de caída apretados; implementa backoff y clasificación de errores.

## Observabilidad (Cómo detectar problemas)
**Métricas:** duración del bucle de reconciliación, conteo de errores, longitud de la cola de reconciliación, número de recursos gestionados.
**Logs:** logs estructurados por reconciliación con correlación al UID del recurso y hash de la spec.
**Trazas:** rastrear la ejecución de reconciliación y las llamadas API descendentes.
**Alertas:** errores de reconciliación altos, reinicios del operador, duraciones de reconciliación largas.

## Notas de implementación (si aplica)
**Lista de verificación**
- [ ] Diseñar CRD con separación clara de spec/status.
- [ ] Implementar reconciliación de forma idempotente.
- [ ] Usar controller-runtime para cliente y manager.
- [ ] Agregar elección de líder para HA.
- [ ] Agregar pruebas: unitarias para reconciliación, integración con envtest, e2e en KinD.

**Ciclo de vida del operador**
- Desarrollar localmente con `operator-sdk` o `kubebuilder`.
- Ejecutar con `make run` en dev; construir imagen y desplegar en staging para pruebas de integración.

## Mini ejemplo (esqueleto de reconciliación en Go)
```go
func (r *MyController) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {
    var cr myv1alpha1.MyResource
    if err := r.Get(ctx, req.NamespacedName, &cr); err != nil {
        return ctrl.Result{}, client.IgnoreNotFound(err)
    }

    // 1) compute desired child resources from CR spec
    desired := desiredStateFor(cr)

    // 2) ensure child resources exist and are up-to-date
    if err := r.ensureChildren(ctx, &cr, desired); err != nil {
        return ctrl.Result{RequeueAfter: time.Minute}, err
    }

    // 3) update status
    cr.Status.Ready = true
    if err := r.Status().Update(ctx, &cr); err != nil {
        return ctrl.Result{}, err
    }

    return ctrl.Result{}, nil
}
```

## Ejercicios / Práctica
1) Diseña un CRD para un clúster de caché (réplicas, persistencia, política de desalojo). Describe los pasos de reconciliación para escalado y cambio de configuración.
2) Implementa pseudo-lógica de reconciliación idempotente para asegurar que un Job de respaldo se ejecute semanalmente.
3) Explica cómo agregarías elección de líder y por qué es necesaria cuando se ejecutan múltiples réplicas del operador.

## Anti-patrones comunes
- **Anti-patrón:** Operadores que realizan trabajo síncrono pesado y de larga ejecución en reconciliación sin delegar a Jobs; causa reconciliaciones lentas y saturación de la API.
  - **Mejor enfoque:** crear Jobs o controladores hijos para tareas de larga ejecución y mantener la reconciliación rápida e idempotente.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Los Operadores son controladores más CRDs que codifican conocimiento operacional. Observan un recurso personalizado, calculan el estado deseado y ejecutan acciones imperativas para alcanzar ese estado — habilitando la automatización de tareas complejas del ciclo de vida de aplicaciones.

### Preguntas trampa (con respuestas)
1) **P:** ¿Por qué usar Go para Operadores en lugar de Python o Node?  
   - **R:** Go tiene bibliotecas maduras de Kubernetes (`client-go`, `controller-runtime`), compila a un solo binario estático (fácil contenerización) y su modelo de concurrencia se adapta a patrones de controladores; puedes escribir operadores en otros lenguajes, pero Go es el estándar práctico.
2) **P:** ¿Cómo evitas bucles de reconciliación infinitos?  
   - **R:** Reconcilia de forma idempotente, compara observado vs deseado (usa un hash de la spec) y evita modificar campos que re-dispararán reconciliaciones sin cambios; usa el subrecurso de status y anotaciones cuidadosamente.
3) **P:** ¿Cuándo deberías preferir Helm+hooks vs un Operador?  
   - **R:** Usa Helm para despliegues con plantillas y ciclos de vida simples; construye un Operador cuando necesites reconciliación activa y automatización de operaciones complejas del día 2.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo describir el bucle de reconciliación en 30 segundos.
- [ ] Puedo listar cuándo construir un Operador vs usar Helm.
- [ ] Puedo esbozar una spec de CRD y campos de status típicos.
- [ ] Puedo explicar elección de líder, finalizers y conceptos básicos de idempotencia.
- [ ] Puedo señalar controller-runtime y Operator SDK como herramientas basadas en Go.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Kubernetes (conceptos básicos de plataforma)](kubernetes.md)

### Temas relacionados
- [StatefulSet](../operations/rolling-deployments.md)
- [Patrones de respaldos y operadores (TODO)](TODO)

## Notas / Bandeja de entrada (opcional)
- Considerar agregar un pequeño operador de ejemplo con KinD y un flujo de CI para construir/probar operadores.
