---
id: tls
title: "TLS (Seguridad de la capa de transporte)"
type: technology
status: learning
importance: 55
difficulty: medium
tags: [operations, networking, security]
canonical: true
aliases: [ssl]
created_at: 2026-01-21
updated_at: 2026-01-21
---

# TLS (Seguridad de la capa de transporte)

## TL;DR (BLUF)
- TLS cifra datos en tránsito y autentica endpoints.
- Úsalo para cualquier tráfico de red que transporte datos sensibles o de usuario.
- Trade-off: latencia extra del handshake y complejidad operacional con certificados.

## Definición
**Qué es:** Un protocolo de seguridad que proporciona confidencialidad, integridad y (opcionalmente) autenticación mutua para conexiones de red, típicamente sobre [TCP](tcp.md).  
**Términos clave:** certificado, handshake, suite de cifrado, PKI, mTLS.

## Por qué importa
- Previene la interceptación y manipulación del tráfico de red.
- Los fallos de certificados y handshake son fuentes comunes de interrupciones.

## Alcance y no-objetivos
**Dentro del alcance:** propósito de TLS, flujo de handshake y trade-offs operacionales.  
**Fuera del alcance / NO resuelto por esto:** autenticación y autorización a nivel de aplicación.

## Modelo mental / Intuición
- TLS es un sobre seguro: envuelve tu tráfico para que solo las partes previstas puedan leerlo.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El tráfico cruza redes no confiables (internet, redes compartidas).
- Necesitas verificar la identidad del servidor.
### Evítalo cuando
- Solo en entornos raros, completamente aislados sin datos sensibles (y aceptas el riesgo).

## Cómo lo usaría (práctico)
- **Contexto:** APIs [HTTP](http.md) públicas y tráfico interno servicio-a-servicio.
- **Pasos:**
  1) Emitir certificados (CA o gestionados). 
  2) Configurar terminación TLS en el balanceador de carga o servicio.
  3) Rotar certificados y monitorear errores de handshake.
- **Cómo se ve el éxito:** tráfico cifrado, pocos fallos de handshake, rotación fluida.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** cifrado en tránsito, integridad, autenticación de endpoints.
- **Desventajas / Riesgos:** latencia del handshake, incidentes por expiración de certificados y complejidad de configuración.
### Alternativas
- **Solo red privada:** reduce la exposición pero no reemplaza el cifrado.
- **Cómo elegir:** TLS por defecto; las excepciones deberían ser raras y documentadas.

## Modos de fallo y trampas
- Certificados expirados causando interrupciones.
- Versiones o suites de cifrado TLS incompatibles.
- Terminación en el borde sin cifrado de extremo a extremo donde se requiere.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de éxito de handshake, distribución de versiones TLS, días hasta expiración de certificados.
- **Logs:** fallos de handshake, errores de validación de certificados.
- **Trazas:** picos en tiempo de establecimiento de conexión.
- **Alertas:** expiración próxima de certificados o picos en errores de handshake.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Automatizar emisión y rotación de certificados
  - [ ] Aplicar políticas de versión TLS
- **Notas de seguridad / cumplimiento**
  - Usar mTLS para tráfico interno sensible donde la identidad importa.
- **Notas de rendimiento**
  - Habilitar reanudación de sesión para reducir latencia de handshake.
- **Notas operacionales**
  - Rastrear expiración de certificados en todos los entornos.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Renovación manual de certificados.
  - **Por qué es malo:** certificados expirados causan interrupciones.
  - **Mejor enfoque:** automatizar renovación y alertar antes de la expiración.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- TLS cifra el tráfico y verifica identidades usando certificados. Protege datos en tránsito pero agrega latencia de handshake y sobrecarga de gestión de certificados.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es TLS lo mismo que HTTPS?
  - **R:** No; HTTPS es [HTTP](http.md) sobre TLS.
2) **P:** ¿TLS garantiza autorización?
   - **R:** No; solo asegura la conexión y puede autenticar endpoints.
3) **P:** ¿Es mTLS siempre requerido?
   - **R:** No; úsalo cuando necesites autenticación mutua.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo explicar qué proporciona TLS (confidencialidad, integridad, autenticación).
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo describir los riesgos del ciclo de vida de certificados.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.
- [ ] Puedo explicar HTTPS como HTTP sobre TLS.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Capas de red (OSI y TCP/IP)](network-layers.md)
- [TCP](tcp.md)

### Temas relacionados
- [HTTP](http.md)
- [Fundamentos de diseño de APIs](../system-design/api-design-basics.md)

### Comparar con
- [HTTP](http.md) — semánticas de protocolo vs seguridad de transporte.

## Notas / Bandeja de entrada (opcional)
- N/A
