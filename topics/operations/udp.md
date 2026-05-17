---
id: udp
title: "UDP (Protocolo de datagramas de usuario)"
type: technology
status: learning
importance: 45
difficulty: medium
tags: [operations, networking, transport]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# UDP (Protocolo de datagramas de usuario)

## TL;DR (BLUF)
- UDP es un protocolo de transporte sin conexión y de mejor esfuerzo sin garantías de orden ni fiabilidad.
- Úsalo para cargas de trabajo sensibles a la latencia o tolerantes a pérdidas.
- Trade-off: debes manejar la fiabilidad en la capa de aplicación si es necesario.

## Definición
**Qué es:** Un protocolo de capa de transporte que envía datagramas independientes sin establecimiento de conexión, garantías de entrega ni ordenamiento.  
**Términos clave:** datagrama, sin conexión, mejor esfuerzo, pérdida, reordenamiento.

## Por qué importa
- UDP permite baja latencia y transporte simple para sistemas en tiempo real.
- Transfiere la responsabilidad de fiabilidad y orden a la aplicación.

## Alcance y no-objetivos
**Dentro del alcance:** características de UDP, casos de uso típicos y trade-offs.  
**Fuera del alcance / NO resuelto por esto:** cifrado, control de congestión o fiabilidad a nivel de aplicación.

## Modelo mental / Intuición
- Piensa en UDP como postales: las envías y esperas que lleguen en orden.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La latencia es más importante que la entrega perfecta.
- La aplicación puede tolerar pérdidas o implementar su propia fiabilidad.
### Evítalo cuando
- Requieres entrega fiable y ordenada.
- No puedes manejar pérdidas en la capa de aplicación.

## Cómo lo usaría (práctico)
- **Contexto:** Recolección de métricas, streaming de medios o juegos en línea.
- **Pasos:**
  1) Usar UDP para mensajes pequeños y frecuentes.
  2) Implementar reintentos o secuenciamiento solo si es necesario.
  3) Monitorear pérdidas y latencia.
- **Cómo se ve el éxito:** baja latencia con tasas de pérdida aceptables.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** baja sobrecarga, baja latencia, sin establecimiento de conexión.
- **Desventajas / Riesgos:** pérdida, duplicación y reordenamiento.
### Alternativas
- **[TCP](tcp.md):** entrega fiable y ordenada para datos sensibles a la corrección.
- **Cómo elegir:** usa UDP cuando la puntualidad supera a la fiabilidad.

## Modos de fallo y trampas
- Pérdida silenciosa de paquetes sin detección.
- Lógica de aplicación que asume un orden que no existe.
- Fragmentación causando mayores tasas de pérdida.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de pérdida de paquetes, jitter, latencia.
- **Logs:** contadores de paquetes descartados si están disponibles.
- **Trazas:** huecos o números de secuencia desordenados.
- **Alertas:** pérdida sostenida o picos de jitter.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Mantener datagramas pequeños para evitar fragmentación
  - [ ] Agregar números de secuencia si el orden importa
- **Notas de seguridad / cumplimiento**
  - Validar payloads y limitar la tasa para prevenir abuso.
- **Notas de rendimiento**
  - Agrupar envíos donde sea posible.
- **Notas operacionales**
  - Monitorear jitter y pérdida de red.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar UDP para datos que requieren entrega garantizada.
  - **Por qué es malo:** los paquetes perdidos corrompen flujos de negocio.
  - **Mejor enfoque:** usar [TCP](tcp.md) o agregar fiabilidad a nivel de aplicación.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- UDP envía datagramas sin conexión y no ofrece garantías de entrega ni orden. Es rápido y ligero pero requiere que la aplicación maneje las pérdidas si es necesario.

### Preguntas trampa (con respuestas)
1) **P:** ¿UDP garantiza la entrega?
   - **R:** No; es de mejor esfuerzo.
2) **P:** ¿Pueden los paquetes UDP llegar desordenados?
   - **R:** Sí; el orden no está garantizado.
3) **P:** ¿UDP es siempre inseguro?
   - **R:** No; la seguridad depende de la aplicación (por ejemplo, DTLS o cifrado).

### Auto-verificación rápida (5 elementos)
- [ ] Puedo definir UDP con precisión.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de redes](networking-basics.md)
- [Capas de red (OSI y TCP/IP)](network-layers.md)

### Temas relacionados
- [TCP](tcp.md)
- [Fundamentos de diseño de APIs](../system-design/api-design-basics.md)

### Comparar con
- [TCP](tcp.md) — datagramas de mejor esfuerzo vs flujo ordenado fiable.

## Notas / Bandeja de entrada (opcional)
- N/A
