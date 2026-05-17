---
id: api-gateway
title: "API Gateway"
type: pattern
status: learning
importance: 80
difficulty: medium
tags: [architecture, api-design, system-design, microservices]
canonical: true
aliases: []
created_at: 2026-01-26
updated_at: 2026-01-26
---

# API Gateway

## TL;DR (BLUF)
- Un punto de entrada único que enruta peticiones a microservicios y aplica políticas transversales.
- Úsalo para APIs públicas, escenarios multi-cliente y para centralizar autenticación, rate limiting y observabilidad.
- Trade-off: latencia añadida y potencial punto único de fallo.

## Definición
**Qué es:** Un proxy inverso que actúa como punto de entrada único para peticiones de clientes, enrutándolas a microservicios backend mientras maneja preocupaciones transversales como autenticación, rate limiting, terminación TLS y transformación de peticiones/respuestas.

**Términos clave:** enrutamiento, proxy inverso, terminación TLS, autenticación, rate limiting, agregación de peticiones, punto de entrada único.

## Por qué importa
- Simplifica la integración del cliente ocultando la topología interna de servicios.
- Centraliza políticas de seguridad (autenticación, CORS, TLS) y preocupaciones operativas (logging, tracing, métricas).
- Reduce el acoplamiento entre clientes y servicios, permitiendo evolución independiente.
- Protege los servicios backend del abuso y la sobrecarga.

## Alcance y no-objetivos
**Dentro del alcance:** enrutamiento, aplicación de autenticación, rate limiting, transformación de peticiones/respuestas, traducción de protocolos (HTTP→gRPC), terminación TLS.

**Fuera del alcance / NO resuelto por esto:**
- Lógica de negocio (pertenece a los servicios o [BFF](../architecture/backend-for-frontend.md))
- Agregación de datos para clientes específicos (ver [API Composition](../architecture/api-composition.md) o [BFF](../architecture/backend-for-frontend.md))
- Preocupaciones de service mesh dentro del clúster (ver [Sidecar](../operations/sidecar.md))

## Modelo mental / Intuición
- Piénsalo como el conserje de un hotel: una entrada, gestiona la identificación de huéspedes, los dirige a las habitaciones (servicios) y aplica las reglas de la casa.
- En microservicios: los clientes llaman a una URL; el gateway sabe qué servicio maneja cada ruta.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tienes múltiples microservicios detrás de una API pública.
- Necesitas autenticación centralizada, rate limiting o terminación TLS.
- Los clientes (web, móvil, partners) no deben conocer la estructura interna de servicios.
- Quieres versionar APIs o realizar enrutamiento A/B.
- Necesitas transformación de peticiones/respuestas o traducción de protocolos.

### Evítalo cuando
- Tienes un monolito o un servicio único (añade complejidad innecesaria).
- Llamadas internas servicio-a-servicio (usa [Service Discovery](../operations/service-discovery.md) en su lugar).
- Necesitas lógica de agregación específica por cliente (usa [BFF](../architecture/backend-for-frontend.md) en su lugar).

## Cómo lo usaría (práctico)
- **Contexto:** Una plataforma de e-commerce con servicios separados para usuarios, productos, pedidos y pagos expuestos a clientes web y móvil.
- **Pasos:**
  1) Desplegar un API Gateway (Kong, AWS API Gateway o NGINX).
  2) Configurar reglas de enrutamiento: `/users/*` → user-service, `/products/*` → product-service.
  3) Habilitar validación JWT en el gateway para todos los endpoints autenticados.
  4) Aplicar rate limiting por cliente (100 req/min para usuarios gratuitos, 1000 para premium).
  5) Habilitar terminación TLS y reenviar tráfico interno como HTTP.
  6) Añadir cabeceras de tracing distribuido (X-Request-ID, contexto de traza).
  7) Monitorear métricas del gateway: tasa de peticiones, latencia p95/p99, tasa de errores por ruta.

## Trade-offs / Costes (y su mitigación)
| Trade-off | Mitigación |
|-----------|-----------|
| Latencia añadida (~5-20ms) | Desplegar gateways cerca de los clientes; optimizar reglas de enrutamiento |
| Punto único de fallo | Ejecutar múltiples instancias del gateway detrás de un balanceador de carga; usar health checks |
| Complejidad de configuración | Usar configs declarativos (YAML/JSON); automatizar con IaC |
| Puede convertirse en cuello de botella | Escalar horizontalmente; evitar poner lógica de negocio en el gateway |
| Proliferación de versiones | Aplicar políticas de deprecación; rastrear uso con [Observabilidad](../operations/observability-basics.md) |

## Modos de fallo / Casos extremos
1. **El gateway se convierte en cuello de botella:** Si todo el tráfico fluye por una instancia, satura CPU/red.
   - *Mitigación:* Escalar horizontalmente con balanceador de carga.
2. **Deriva de configuración entre entornos:** Staging vs prod tienen reglas de enrutamiento diferentes.
   - *Mitigación:* Usar GitOps y despliegues automatizados.
3. **Infiltración de lógica de negocio:** Los equipos añaden transformaciones/validaciones en el gateway.
   - *Mitigación:* Aplicar política: el gateway solo maneja enrutamiento + preocupaciones transversales.
4. **Fallos en cascada:** Si el gateway tiene [Timeouts](../operations/timeouts.md) agresivos, puede amplificar la lentitud del backend.
   - *Mitigación:* Combinar con [Circuit Breaker](../operations/circuit-breaker.md) y [Degradación Elegante](../operations/graceful-degradation.md).
5. **Bypass de autenticación:** Rutas mal configuradas permiten acceso no autenticado.
   - *Mitigación:* Política de denegación por defecto; pruebas de seguridad automatizadas.

## Alternativas
- **Service mesh (Istio, Linkerd):** Para tráfico interno servicio-a-servicio con mTLS, reintentos, tracing.
- **BFF (Backend for Frontend):** Cuando necesitas lógica o agregación específica por cliente.
- **Exposición directa del servicio:** Más simple pero expone topología interna y carece de políticas centralizadas.

## Combinaciones
API Gateway **casi siempre se usa con:**
- **[Rate Limiting](rate-limiting.md):** Protege servicios backend del abuso.
- **[Timeout](../operations/timeouts.md):** Previene peticiones colgadas.
- **[Circuit Breaker](../operations/circuit-breaker.md):** Falla rápido cuando los backends están caídos.
- **[Observabilidad](../operations/observability-basics.md):** Logs, métricas, tracing en el borde.
- **[BFF](../architecture/backend-for-frontend.md):** El gateway enruta a BFFs, que agregan/transforman.

**Combinación típica:**
- **Escenario de API pública:** API Gateway + Rate Limiting + Timeout + Retry (con backoff) + Circuit Breaker + Observabilidad

## Prerequisitos
- Comprensión de [HTTP](../operations/http.md) y proxies inversos.
- Familiaridad con [diseño de Microservicios](../architecture/microservices-design-basics.md).
- Conocimiento básico de [TLS](../operations/tls.md) y autenticación (JWT, OAuth).

## Temas relacionados
- [BFF (Backend for Frontend)](../architecture/backend-for-frontend.md): Capa de agregación específica por cliente.
- [Service Discovery](../operations/service-discovery.md): Localización dinámica de servicios.
- [Rate Limiting](rate-limiting.md): Protección contra abuso.
- [Observabilidad](../operations/observability-basics.md): Monitoreo de salud del gateway.
- [Versionado de API](versioning-apis-and-events.md): Gestión de evolución de API.

## Ejemplos del mundo real
1. **Netflix Zuul / Spring Cloud Gateway:** Enruta peticiones a cientos de microservicios.
2. **Stripe API Gateway:** Maneja autenticación, rate limiting, versionado para API pública.
3. **AWS API Gateway:** Servicio gestionado con autenticación integrada, throttling, caché.

## Checklist (auto-evaluación)
- [ ] Entiendo cuándo usar un API Gateway vs BFF vs Service Mesh.
- [ ] Puedo diseñar reglas de enrutamiento para APIs multi-servicio.
- [ ] Sé cómo aplicar rate limiting y autenticación en el gateway.
- [ ] Puedo explicar los modos de fallo (cuello de botella, deriva de config, infiltración de lógica).
- [ ] Puedo monitorear latencia del gateway, tasa de errores y throughput.

## Recordatorios / Conclusiones clave
- El gateway es para **preocupaciones transversales**, no lógica de negocio.
- Siempre combinar con [Timeout](../operations/timeouts.md), [Circuit Breaker](../operations/circuit-breaker.md) y [Observabilidad](../operations/observability-basics.md).
- Escalar horizontalmente para evitar cuellos de botella.
- Usar BFF cuando necesites agregación específica por cliente.

## Preguntas trampa (con respuestas)
### P1: ¿Puedo usar un API Gateway para comunicación interna servicio-a-servicio?
**R:** Técnicamente sí, pero **no recomendado**. El API Gateway añade latencia y está diseñado para tráfico norte-sur (cliente→servicio). Para tráfico este-oeste (servicio→servicio), usa [Service Discovery](../operations/service-discovery.md) o un [Service Mesh](../operations/sidecar.md) (Istio, Linkerd) que proporciona mTLS, reintentos y observabilidad sin enrutamiento centralizado.

### P2: ¿Debería poner lógica de agregación de datos en el API Gateway?
**R:** **No**. La agregación es lógica de negocio y pertenece a los servicios o a un [BFF](../architecture/backend-for-frontend.md). Los gateways solo deben manejar enrutamiento, autenticación, rate limiting y traducción de protocolos. Mezclar preocupaciones convierte al gateway en un monolito y lo acopla a los internos del servicio.

### P3: Si mi gateway tiene alta disponibilidad, ¿aún necesito circuit breakers?
**R:** **Sí**. La alta disponibilidad protege al gateway en sí, pero no a los backends. Si un servicio backend es lento/falla, el gateway aún puede saturar sus pools de conexiones y propagar el fallo en cascada. Combinar con [Circuit Breaker](../operations/circuit-breaker.md) y [Timeout](../operations/timeouts.md) para fallar rápido.

### P4: ¿Puede un API Gateway reemplazar un balanceador de carga?
**R:** **Parcialmente**. Un gateway puede enrutar a múltiples instancias de un servicio (balanceo de carga básico), pero típicamente aún necesitas un balanceador de carga **delante del gateway** para HA y escalado. Además, los balanceadores de carga de Capa 4 (TCP/UDP) son más eficientes para distribución de tráfico bruto que los gateways de Capa 7.

### P5: ¿Cuál es la diferencia entre API Gateway y BFF?
**R:** **API Gateway** es infraestructura: enrutamiento, autenticación, rate limiting para **todos los clientes**. **BFF** es un servicio: agregación y transformación específica por cliente (un BFF por tipo de cliente: web-BFF, mobile-BFF). A menudo se usan ambos: el Gateway enruta a los BFFs, que agregan desde los servicios backend. Ver [BFF](../architecture/backend-for-frontend.md).
