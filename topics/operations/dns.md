---
id: dns
title: "DNS (Domain Name System)"
type: technology
status: learning
importance: 50
difficulty: medium
tags: [operations, networking]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# DNS (Sistema de Nombres de Dominio)

## TL;DR (BLUF)
- DNS mapea nombres legibles por humanos a direcciones IP y otros registros.
- Úsalo para enrutar tráfico y desacoplar servicios de cambios de IP.
- Trade-off: el caché y los retrasos de propagación pueden causar enrutamiento obsoleto.

## Definición
**Qué es:** Un sistema de nombres distribuido que resuelve nombres de dominio a registros de recursos (A/AAAA, CNAME, TXT, MX, etc.).
**Términos clave:** resolver, servidor autoritativo, TTL, caché, A/AAAA, CNAME.

## Por qué importa
- Los fallos de DNS parecen caídas incluso cuando los servicios están sanos.
- Los TTLs y el caché afectan la velocidad de despliegue y el comportamiento de failover.

## Alcance y no-objetivos
**Dentro del alcance:** flujo de resolución de nombres, TTLs, caché y trade-offs operacionales.
**Fuera del alcance / NO resuelto por esto:** algoritmos de balanceo de carga o diseño de CDN.

## Modelo mental / Intuición
- DNS es una guía telefónica: preguntas por un nombre y obtienes un número (IP).
- Los cachés aceleran las búsquedas pero pueden mantener números antiguos por un tiempo.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesites nombres estables para infraestructura cambiante.
- Necesites enrutar tráfico entre regiones o proveedores.
### Evítalo cuando
- Requieras cutovers instantáneos (el caché DNS puede retrasar).

## Cómo lo usaría (práctico)
- **Contexto:** Cutover de despliegue blue/green.
- **Pasos:**
  1) Establecer un TTL bajo antes de cambios planificados.
  2) Actualizar registros DNS durante el cutover.
  3) Monitorear resolución y tasas de error.
- **Cómo se ve el éxito:** propagación predecible e impacto mínimo al usuario.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** desacopla la identidad del servicio de las IPs; alcance global.
- **Contras / Riesgos:** retrasos de caché, variabilidad de resolvers y propagación parcial.
### Alternativas
- **Cutover por balanceador de carga:** más rápido y controlable para cambio de tráfico.
- **Cómo elegir:** usa DNS para nombres estables y enrutamiento grueso, no para failover instantáneo.

## Modos de fallo y errores comunes
- Registros mal configurados causando agujeros negros de tráfico.
- Cachés obsoletos previniendo rollback rápido.
- DNS split-horizon generando enrutamiento inconsistente.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de resolución, tasa de NXDOMAIN, ratio de aciertos de caché.
- **Logs:** errores de resolver, cambios de registros.
- **Trazas:** latencia aumentada antes de timeouts a nivel de aplicación.
- **Alertas:** picos en NXDOMAIN o fallos de resolución.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir TTLs por tipo de registro
  - [ ] Automatizar cambios DNS con pistas de auditoría
- **Notas de seguridad / cumplimiento**
  - Proteger cambios DNS con control de acceso fuerte.
- **Notas de rendimiento**
  - Usar resolvers locales o caché para reducir latencia.
- **Notas operacionales**
  - Documentar pasos de rollback y tiempo esperado de propagación.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar DNS para cutover instantáneo.
  - **Por qué es malo:** los retrasos de caché hacen el cambio lento e inconsistente.
  - **Mejor enfoque:** usar un balanceador de carga o service mesh para cambios rápidos.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- DNS mapea nombres a IPs usando un sistema distribuido con caché. Es esencial para el enrutamiento pero puede introducir retrasos debido al caché.

### Preguntas trampa (con respuestas)
1) **P:** ¿Bajar el TTL garantiza propagación instantánea?
   - **R:** No; los cachés pueden retener registros antiguos y los resolvers se comportan diferente.
2) **P:** ¿DNS es altamente consistente globalmente?
   - **R:** No; es eventualmente consistente debido al caché y la propagación.
3) **P:** ¿DNS puede reemplazar un balanceador de carga?
   - **R:** No para failover instantáneo; DNS es más lento y menos controlable.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar cómo funciona la resolución DNS end-to-end.
- [ ] Puedo describir los trade-offs de TTL.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.
- [ ] Puedo explicar por qué los cutovers DNS son lentos.
- [ ] Puedo relacionar DNS con la disponibilidad.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de redes](networking-basics.md)
- [Capas de red (OSI y TCP/IP)](network-layers.md)

### Temas relacionados
- [TCP](tcp.md)
- [HTTP](http.md)
- [Despliegues blue/green](blue-green-deployments.md)

### Comparar con
- (TODO) Balanceo de carga — enrutamiento DNS vs control de tráfico L4/L7.

## Notas / Bandeja de entrada (opcional)
- N/A
