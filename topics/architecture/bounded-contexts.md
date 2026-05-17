---
id: bounded-contexts
title: "Contextos Acotados"
type: concept
status: learning
importance: 85
difficulty: hard
tags: [architecture, ddd, microservices]
canonical: true
aliases: []
created_at: 2026-01-22
updated_at: 2026-01-22
---

# Contextos Acotados

## TL;DR (BLUF)
- Un contexto acotado es la frontera donde un modelo y un lenguaje son consistentes.
- Úsalo para dividir dominios complejos en partes coherentes con contratos claros.
- Trade-off: esfuerzo de modelado inicial y complejidad de integración entre contextos.

## Definición
**Qué es:** Una frontera dentro de la cual un modelo de dominio y un lenguaje ubicuo son consistentes y se aplican.
**Términos clave:** contexto acotado, lenguaje ubicuo, mapa de contexto, contrato de integración.

## Por qué importa
- Previene "un gran modelo único" y reduce el acoplamiento accidental.
- Guía las fronteras de servicio, la propiedad y los patrones de integración.

## Alcance y no-objetivos
**Dentro del alcance:** fronteras de modelado, propiedad y contratos de integración.
**Fuera del alcance / NO resuelto por esto:** topología de despliegue u organigramas de equipo.

## Modelo mental / Intuición
- Piensa en cada contexto acotado como su propio diccionario: la misma palabra puede significar cosas diferentes entre contextos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El dominio es complejo con conceptos y reglas en conflicto.
- Múltiples equipos/servicios interactúan en la misma área de negocio.

### Evítalo cuando
- El dominio es CRUD simple con reglas mínimas.
- No puedes invertir en descubrimiento de dominio y lenguaje compartido.

## Cómo lo usaría (práctico)
- **Contexto:** Plataforma de cobro de deudas con "Casos", "Contactos" y "Pagos".
- **Pasos:**
  1) Ejecutar descubrimiento de dominio para listar términos centrales e invariantes.
  2) Separar contextos (ej., Gestión de Casos, Contacto, Pagos).
  3) Definir APIs/eventos entre contextos con contratos versionados.
- **Cómo se ve el éxito:** menos conflictos entre equipos y propiedad más clara.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** claridad, acoplamiento reducido, evolución más segura.
- **Desventajas / Riesgos:** sobrecarga de integración, duplicación de datos entre contextos.

### Alternativas
- **Modelo compartido único:** más rápido al inicio pero más difícil de escalar y evolucionar.
- **Cómo elegir:** si la complejidad del dominio o el tamaño del equipo crece, los contextos acotados valen la pena.

## Modos de fallo y trampas
- Dividir por capas técnicas en lugar de conceptos de dominio.
- Sobre-dividir en contextos diminutos con integraciones parlanchinas.
- No documentar contratos de integración (lleva a deriva semántica).

## Observabilidad (Cómo detectar problemas)
- **Métricas:** volumen de llamadas entre contextos, tasa de error de integración, tiempo de entrega de cambios por contexto.
- **Logs:** desajustes de versión de contrato o fallos de validación de esquema.
- **Trazas:** alto fan-out entre contextos indica sobre-división.
- **Alertas:** fallos de integración repetidos entre contextos específicos.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Definir lenguaje ubicuo por contexto
  - [ ] Documentar propiedad y contratos
  - [ ] Elegir estilo de integración (síncrono vs asíncrono)

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** "Un modelo de dominio global para todo."
  - **Por qué es malo:** crea acoplamiento fuerte y ralentiza los cambios.
  - **Mejor enfoque:** contextos acotados con contratos explícitos.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- Un contexto acotado es una frontera donde un modelo de dominio y un lenguaje son consistentes; ayuda a dividir un dominio complejo en partes coherentes con contratos de integración claros.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los contextos acotados son lo mismo que los microservicios?
   - **R:** No; son fronteras conceptuales que pueden mapearse a servicios o módulos.
2) **P:** ¿Cada término debería significar lo mismo entre contextos?
   - **R:** No; diferentes contextos pueden usar intencionalmente significados diferentes.
3) **P:** ¿Los contextos acotados eliminan la necesidad de trabajo de integración?
   - **R:** No; hacen la integración explícita e intencional.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir contexto acotado con precisión.
- [ ] Puedo explicar por qué reduce el acoplamiento.
- [ ] Puedo dar un ejemplo de separación de contextos.
- [ ] Puedo listar un trade-off.
- [ ] Puedo nombrar un modo de fallo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Diseño Orientado al Dominio (DDD)](domain-driven-design.md)

### Temas relacionados
- [Fundamentos de diseño de microservicios](microservices-design-basics.md)
- [Arquitectura dirigida por eventos](event-driven-basics.md)
- [Fundamentos de diseño de APIs](../system-design/api-design-basics.md)

### Comparar con
- [Arquitectura hexagonal](hexagonal-architecture.md) -- los contextos acotados tratan sobre fronteras de dominio; la hexagonal trata sobre la dirección de dependencias.

## Notas / Bandeja de entrada (opcional)
- N/A
