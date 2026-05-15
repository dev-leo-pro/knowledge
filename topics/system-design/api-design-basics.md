---
id: api-design-basics
title: "Fundamentos de Diseño de API"
type: concept
status: learning
importance: 45
difficulty: easy
tags: [system-design, api]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Diseño de API

## TL;DR (BLUF)
- El diseño de API define recursos, métodos y contratos.
- Úsalo para asegurar claridad, consistencia y capacidad de evolución.
- Trade-off: contratos más estrictos pueden ralentizar la iteración.

## Definición
**Qué es:** El proceso de definir endpoints de API, payloads y comportamiento.
**Términos clave:** recurso, método, contrato, versionado.

## Por qué importa
- Reduce bugs de integración y ambigüedad.
- Un mal diseño lleva a cambios que rompen la compatibilidad y confusión.

## Alcance y no-objetivos
**Dentro del alcance:** Estructura de API y contratos.
**Fuera del alcance / NO resuelto por esto:** detalles de implementación.

## Modelo mental / Intuición
- Una API es un contrato; sé explícito y consistente.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Expones APIs a clientes u otros equipos.
### Evítalo cuando
- Solo necesitas prototipos internos sin consumidores.

## Cómo lo usaría (práctico)
- **Contexto:** API REST pública.
- **Pasos:** definir recursos → elegir métodos → documentar respuestas.
- **Cómo se ve el éxito:** integraciones estables y pocos cambios que rompan compatibilidad.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** claridad y consistencia.
- **Desventajas / Riesgos:** cambios más lentos.
### Alternativas
- **Contratos flexibles:** más rápidos pero arriesgados.
- **Cómo elegir:** usa diseño estricto para APIs de larga duración.

## Modos de fallo y trampas
- Sobrecargar endpoints con múltiples comportamientos.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de errores, fallos de integración del cliente.
- **Logs:** errores de desajuste de contrato.
- **Alertas:** picos en 4xx/5xx.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir esquemas de petición/respuesta
  - [ ] Versionar cambios que rompen compatibilidad

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Cambiar la forma de la respuesta sin versionado.
  - **Por qué es malo:** rompe clientes.
  - **Mejor enfoque:** versionar o añadir campos de forma compatible.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El diseño de API trata de definir contratos estables: recursos, métodos y payloads. Un buen diseño previene romper clientes y hace los sistemas más fáciles de integrar.

### Preguntas trampa (con respuestas)
1) **P:** ¿El versionado siempre es necesario?
   - **R:** para cambios que rompen compatibilidad, sí.
2) **P:** ¿Deberían las APIs exponer tablas de la base de datos directamente?
   - **R:** no; diseña alrededor de casos de uso.
3) **P:** ¿Los campos opcionales siempre son seguros?
   - **R:** no; pueden crear ambigüedad.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir los fundamentos del diseño de API.
- [ ] Puedo mencionar un trade-off.
- [ ] Puedo nombrar una trampa.
- [ ] Puedo describir una señal de monitoreo.
- [ ] Puedo explicar el versionado.

## Enlaces (SIN duplicación)
### Prerequisitos
- N/A

### Temas relacionados
- [Validación de API](api-validation.md)

### Comparar con
- [Restricciones vs aplicación a nivel de app](../databases/constraints-vs-app.md) — reglas de API vs BD.
