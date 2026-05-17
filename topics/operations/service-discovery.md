---
id: service-discovery
title: "Descubrimiento de servicios"
type: pattern
status: learning
importance: 70
difficulty: medium
tags: [operations, microservices, distributed-systems, networking]
canonical: true
aliases: [service registry, service mesh discovery]
created_at: 2026-01-26
updated_at: 2026-01-26
---

# Descubrimiento de servicios

## TL;DR (BLUF)
- Descubre dinámicamente instancias de servicio por nombre lógico en lugar de IP:puerto codificados.
- Úsalo en entornos contenerizados/cloud con autoescalado e IPs dinámicas.
- Trade-off: complejidad añadida y dependencia de la disponibilidad del registro.

## Definición
**Qué es:** Un mecanismo para que los servicios se localicen entre sí dinámicamente usando un registro de servicios (DNS, Consul, Kubernetes Service, Eureka) que mapea nombres lógicos de servicio a instancias disponibles (IP:puerto).

**Términos clave:** registro de servicios, DNS, health checks, balanceo de carga, service mesh, descubrimiento dinámico, registro de instancias.

## Por qué importa
- Permite escalado elástico: los servicios pueden agregar/remover instancias sin cambios de configuración.
- Soporta despliegues sin tiempo de inactividad: las nuevas instancias se registran, las antiguas se desregistran.
- Simplifica la comunicación servicio-a-servicio en microservicios.
- Esencial para entornos cloud-native y contenerizados (Kubernetes, ECS, Cloud Run).

## Alcance y no-objetivos
**Dentro del alcance:** registro de servicios, health checks, descubrimiento basado en DNS o registro, balanceo de carga del lado del cliente vs del servidor.

**Fuera del alcance / NO resuelto por esto:**
- Enrutamiento de API Gateway (ver [API Gateway](../system-design/api-gateway.md))
- Preocupaciones transversales (auth, reintentos) → [Sidecar](sidecar.md) o service mesh
- Lógica de negocio o agregación de datos

## Modelo mental / Intuición
- Como un directorio telefónico: en lugar de memorizar números de teléfono, buscas "La Pizza de Juan" y obtienes el número actual.
- En microservicios: en lugar de `http://192.168.1.10:8080`, llamas a `http://user-service/api/users`.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los servicios se ejecutan en contenedores (Docker, Kubernetes) con IPs dinámicas.
- Necesitas autoescalado (las instancias van y vienen frecuentemente).
- Despliegas en entornos cloud (AWS, GCP, Azure).
- Tienes muchos servicios y la configuración manual es propensa a errores.

### Evítalo cuando
- Tienes un conjunto pequeño y estático de servicios con IPs fijas.
- Los servicios se ejecutan en VMs con endpoints estables (raro en sistemas modernos).
- La sobrecarga operacional de ejecutar un registro supera los beneficios.

## Cómo lo usaría (práctico)
- **Contexto:** Plataforma de comercio electrónico en Kubernetes con order-service, user-service, payment-service.
- **Pasos:**
  1) Desplegar servicios con recursos Kubernetes Service:
     ```yaml
     apiVersion: v1
     kind: Service
     metadata:
       name: user-service
     spec:
       selector:
         app: user
       ports:
       - port: 80
         targetPort: 8080
     ```
  2) Los servicios se llaman entre sí por nombre DNS: `http://user-service/api/users`.
  3) El DNS de Kubernetes resuelve `user-service` a las IPs de pods disponibles.
  4) Agregar readiness/liveness probes:
     ```yaml
     livenessProbe:
       httpGet:
         path: /health
         port: 8080
       initialDelaySeconds: 10
       periodSeconds: 5
     ```
  5) Habilitar **balanceo de carga del lado del cliente** (round-robin por defecto) o usar service mesh (Istio, Linkerd).
  6) Monitorear: salud del registro de servicios, health checks fallidos, latencia de resolución DNS.

## Trade-offs / Costos (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| El registro se convierte en punto único de fallo | Usar registro altamente disponible (Kubernetes DNS, clúster Consul); cachear lista de instancias localmente |
| Datos de instancia obsoletos (retraso en health check) | Ajustar intervalos de health check (5-10s); usar TTLs cortos |
| Mayor complejidad (registro + health checks) | Usar soluciones gestionadas (Kubernetes, AWS Cloud Map); automatizar con IaC |
| Problemas de caché DNS (los clientes cachean IPs obsoletas) | Establecer TTLs DNS bajos (1-5s); usar bibliotecas cliente que respeten TTL |
| Sobrecarga del balanceo de carga del lado del cliente | Usar service mesh para balanceo del lado del servidor (Istio, Linkerd) |

## Modos de fallo / Casos límite
1. **Caída del registro:** Los servicios no pueden descubrir nuevas instancias.
   - *Mitigación:* Cachear últimas instancias conocidas localmente; usar registro eventualmente consistente.
2. **Instancias no sanas registradas:** Los health checks se retrasan; los clientes obtienen 503.
   - *Mitigación:* Intervalos de health check agresivos (3-5s); desregistro rápido.
3. **TTL DNS muy alto:** Los clientes cachean IPs antiguas después de scale-down/despliegue.
   - *Mitigación:* Establecer TTL DNS a 1-5 segundos; usar connection pooling con refresco periódico.
4. **Split-brain (multi-región):** Diferentes registros en diferentes regiones divergen.
   - *Mitigación:* Usar registros regionales con replicación entre regiones (consistencia eventual).
5. **Colisión de nombres de servicio:** Dos equipos usan el mismo nombre de servicio.
   - *Mitigación:* Usar namespaces para servicios (por ejemplo, `equipo-a.user-service`).

## Alternativas
- **IPs/archivos de configuración codificados:** Simple pero frágil; requiere actualizaciones manuales.
- **Balanceador de carga con pool de backends estático:** Funciona para pocos servicios; no escala.
- **Service mesh (Istio, Linkerd):** Combina descubrimiento + mTLS + reintentos + observabilidad.
- **Registros DNS SRV:** Descubrimiento ligero para casos simples.

## Patrones
### 1. Descubrimiento del lado del cliente
- El cliente consulta el registro, obtiene la lista de instancias y balancea la carga.
- **Ventajas:** Sin salto extra; el cliente controla la estrategia de balanceo.
- **Desventajas:** Complejidad en el cliente; cada lenguaje necesita biblioteca.

### 2. Descubrimiento del lado del servidor
- El cliente llama al balanceador de carga (o API Gateway); el balanceador consulta el registro.
- **Ventajas:** Cliente simple; lógica de balanceo centralizada.
- **Desventajas:** Salto extra; el balanceador puede ser cuello de botella.

### 3. Service mesh (Istio, Linkerd)
- Los proxies sidecar manejan descubrimiento + balanceo + reintentos + mTLS.
- **Ventajas:** Sin cambios de código; políticas consistentes.
- **Desventajas:** Complejidad operacional; latencia añadida.

**Recomendación:** Usa **Kubernetes DNS** (más simple) o **service mesh** (si necesitas mTLS/reintentos/observabilidad).

## Combinaciones
El descubrimiento de servicios **casi siempre se usa con:**
- **[Diseño de microservicios](../architecture/microservices-design-basics.md):** Esencial para llamadas servicio-a-servicio.
- **[Health checks](../operations/reliability-basics.md):** Asegurar que solo se descubran instancias sanas.
- **[Sidecar](sidecar.md):** Los proxies del service mesh manejan descubrimiento + balanceo.
- **[Observabilidad](../operations/observability-basics.md):** Rastrear topología de servicios, salud de instancias.
- **[Circuit Breaker](circuit-breaker.md):** Proteger contra instancias no sanas.

**Combinación típica:**
- **Clúster Kubernetes:** Descubrimiento de servicios (DNS) + Health checks + Sidecar (Istio/Linkerd) + Observabilidad

## Prerequisitos
- Comprensión de [Fundamentos de redes](../operations/networking-basics.md) y [DNS](../operations/dns.md).
- Familiaridad con [Diseño de microservicios](../architecture/microservices-design-basics.md).
- Conocimiento de contenerización (Docker, Kubernetes).

## Temas relacionados
- [API Gateway](../system-design/api-gateway.md): Enrutamiento de tráfico norte-sur.
- [Sidecar](sidecar.md): Service mesh para descubrimiento + mTLS + reintentos.
- [Balanceo de carga](../system-design/api-design-basics.md): Del lado del cliente vs del servidor.
- [Observabilidad](../operations/observability-basics.md): Monitorear topología de servicios.
- [DNS](../operations/dns.md): Base para el descubrimiento.

## Ejemplos del mundo real
1. **Kubernetes:** Descubrimiento de servicios basado en DNS integrado; cada Service obtiene un nombre DNS.
2. **Netflix Eureka:** Registro de descubrimiento del lado del cliente usado en Spring Cloud.
3. **HashiCorp Consul:** Registro de servicios multi-DC con health checks y almacén KV.
4. **AWS Cloud Map:** Descubrimiento de servicios gestionado para ECS y EKS.

## Lista de verificación (auto-test)
- [ ] Entiendo el descubrimiento del lado del cliente vs del servidor.
- [ ] Puedo configurar health checks para instancias de servicio.
- [ ] Sé cómo los TTLs DNS afectan la obsolescencia del descubrimiento.
- [ ] Puedo explicar cuándo usar service mesh vs Kubernetes DNS nativo.
- [ ] Puedo monitorear la salud del registro de servicios y la disponibilidad de instancias.

## Recordatorios / Conclusiones clave
- El descubrimiento de servicios es **esencial para entornos dinámicos** (contenedores, autoescalado).
- Siempre usa **health checks** para evitar enrutar a instancias no sanas.
- Prefiere **Kubernetes DNS** por simplicidad, **service mesh** para funcionalidades avanzadas.
- Cachea las listas de instancias localmente para sobrevivir a caídas del registro.

## Preguntas trampa (con respuestas)
### P1: ¿Puedo usar descubrimiento de servicios para APIs externas (servicios de terceros)?
**R:** **No**. El descubrimiento de servicios es para comunicación **interna servicio-a-servicio** dentro de tu clúster. Para APIs externas, usa URLs codificadas o DNS externo. El descubrimiento de servicios asume que controlas el ciclo de vida de las instancias y los health checks.

### P2: ¿Cuál es la diferencia entre descubrimiento de servicios y un API Gateway?
**R:** El **descubrimiento de servicios** es para **tráfico este-oeste** (servicio→servicio dentro del clúster). El **[API Gateway](../system-design/api-gateway.md)** es para **tráfico norte-sur** (cliente→servicio desde fuera del clúster). El descubrimiento maneja IPs dinámicas; el gateway maneja enrutamiento, auth, limitación de tasa.

### P3: ¿Debería usar descubrimiento del lado del cliente o del servidor?
**R:** **Depende de la plataforma**. Para **Kubernetes**, usa del lado del servidor (Kubernetes Service + DNS) por simplicidad. Para entornos **no contenerizados** o **políglotas**, usa del lado del cliente (Consul, Eureka) por flexibilidad. Service mesh (Istio) combina ambos: el sidecar maneja el descubrimiento (lado del cliente) pero es transparente para el código de la aplicación.

### P4: ¿Qué pasa si el registro de servicios se cae?
**R:** **Los clientes no pueden descubrir nuevas instancias**, pero las conexiones existentes pueden funcionar. Mitigación: 
1. Cachear últimas instancias conocidas localmente.
2. Usar **registro altamente disponible** (Kubernetes DNS es distribuido; Consul se ejecuta en clúster).
3. Establecer TTLs de caché largos como respaldo (sacrificar frescura por disponibilidad).

### P5: ¿Cómo manejo el versionado de servicios con descubrimiento?
**R:** Usa **nombre de servicio + versión** en el descubrimiento (por ejemplo, `user-service-v1`, `user-service-v2`) o **etiquetas de metadatos** (Consul). Los clientes especifican qué versión llamar. Alternativamente, usa [API Gateway](../system-design/api-gateway.md) para enrutamiento de versiones y mantén el descubrimiento agnóstico a versiones. Ver [Versionado de APIs y eventos](../system-design/versioning-apis-and-events.md).
