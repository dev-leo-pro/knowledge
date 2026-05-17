---
id: domain-driven-design
title: "Diseño Orientado al Dominio (DDD)"
type: concept
status: learning
importance: 60
difficulty: hard
tags: [architecture, modeling, design]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# Diseño Orientado al Dominio (DDD)

## TL;DR (BLUF)
- DDD alinea el software con el dominio de negocio modelando conceptos centrales explícitamente.
- Úsalo para dominios complejos con sistemas de larga vida y muchas reglas.
- Trade-off: mayor esfuerzo de modelado inicial y disciplina continua.

## Definición
**Qué es:** Un enfoque de diseño que centra el modelo de dominio y un lenguaje compartido entre ingenieros y expertos de dominio, asegurando que el código refleje los conceptos de negocio.
**Términos clave:** modelo de dominio, lenguaje ubicuo, contexto acotado, agregado, entidad, objeto de valor, repositorio.

## Por qué importa
- Reduce la deriva semántica entre las necesidades del negocio y el código.
- Mejora la modularidad al dividir el dominio en fronteras claras.

## Alcance y no-objetivos
**Dentro del alcance:** modelar conceptos de dominio, definir fronteras y aplicar invariantes.
**Fuera del alcance / NO resuelto por esto:** organización de equipos, adopción de microservicios o elecciones de infraestructura.

## Modelo mental / Intuición
- Piensa en el modelo como la "fuente de verdad" de cómo funciona el negocio.
- El lenguaje ubicuo se convierte en el contrato entre las personas y el código.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El dominio es complejo con muchas reglas y casos límite.
- El sistema es de larga vida y necesita evolucionar de forma segura.
- Múltiples equipos necesitan alineación en conceptos centrales del negocio.
### Evítalo cuando
- El dominio es CRUD simple con poca lógica de negocio.
- La velocidad de salida al mercado es la única prioridad y el sistema es de corta vida.
- No hay acceso a expertos de dominio o lenguaje compartido.

## Cómo lo usaría (práctico)
- **Contexto:** Una plataforma de pagos con reglas complejas y requisitos de cumplimiento.
- **Pasos:**
  1) Ejecutar descubrimiento de dominio con expertos para definir el lenguaje ubicuo.
  2) Identificar contextos acotados y definir sus APIs.
  3) Modelar agregados e invariantes dentro de cada contexto.
  4) Mantener las integraciones explícitas vía eventos o APIs.
- **Cómo se ve el éxito:** terminología consistente, menos bugs de lógica y propiedad clara de las reglas de dominio.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mejor alineación con el negocio, mejor modularidad, evolución más segura.
- **Desventajas / Riesgos:** mayor esfuerzo inicial, parálisis de modelado y deriva si la disciplina decae.
### Alternativas
- **Script de transacción / diseño CRUD-first:** más rápido para dominios simples, pero menos expresivo.
- **[Fundamentos de modelado de datos](../databases/data-modeling-basics.md):** se enfoca en esquemas en lugar de comportamiento.
- **Cómo elegir:** aplica DDD cuando la complejidad del dominio y la longevidad justifican la inversión.

## Modos de fallo y trampas
- Lenguaje ubicuo no adoptado, llevando a términos fragmentados.
- Contextos acotados excesivamente grandes creando un "monolito distribuido".
- Modelos de dominio anémicos donde el comportamiento se empuja a los servicios.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** frecuencia de violaciones de invariantes, tiempo de entrega de cambios para reglas de dominio.
- **Logs:** eventos de dominio o logs de auditoría indicando fallos en la aplicación de reglas.
- **Trazas:** flujos entre contextos mostrando acoplamiento excesivo.
- **Alertas:** picos en errores de validación o tasas de anulación de reglas.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir contextos acotados y propiedad
  - [ ] Documentar lenguaje ubicuo
  - [ ] Capturar invariantes en agregados
- **Notas de seguridad / cumplimiento**
  - Asegurar que las reglas de dominio apliquen restricciones de cumplimiento.
- **Notas de rendimiento**
  - Mantener las fronteras de agregados pequeñas para reducir la contención.
- **Notas operacionales**
  - Revisar cambios del modelo con expertos de dominio regularmente.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Un modelo de dominio global compartido entre todos los contextos.
  - **Por qué es malo:** aumenta el acoplamiento y ralentiza la evolución.
  - **Mejor enfoque:** contextos acotados con contratos de integración explícitos.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- DDD se trata de construir software que refleja el dominio de negocio, usando un lenguaje compartido y fronteras claras para que las reglas vivan en el lugar correcto.

### Preguntas trampa (con respuestas)
1) **P:** ¿DDD es solo para microservicios?
   - **R:** No; es un enfoque de diseño que aplica a cualquier arquitectura.
2) **P:** ¿DDD significa modelar todo en el negocio?
   - **R:** No; enfócate en el dominio central y evita sobre-modelar.
3) **P:** ¿Los contextos acotados son solo fronteras de equipo?
   - **R:** No necesariamente; son fronteras conceptuales que pueden influir en la estructura del equipo.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir DDD con precisión en 2-3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de modelado de datos](../databases/data-modeling-basics.md)
- [Fundamentos de requisitos](../system-design/requirements-basics.md)
- (TODO) Contextos acotados

### Temas relacionados
- [Arquitectura dirigida por eventos](event-driven-basics.md)
- [Event sourcing](event-sourcing.md)
- [CQRS](cqrs.md)

### Comparar con
- [Fundamentos de modelado de datos](../databases/data-modeling-basics.md) -- DDD modela comportamiento e invariantes, no solo esquema.

## Notas / Bandeja de entrada (opcional)
- N/A
