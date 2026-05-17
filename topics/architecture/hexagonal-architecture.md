---
id: hexagonal-architecture
title: "Hexagonal Architecture"
type: pattern
status: learning
importance: 55
difficulty: medium
tags: [architecture, design, modularity]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# Arquitectura Hexagonal

## TL;DR (BLUF)
- La arquitectura hexagonal mantiene el núcleo de la aplicación independiente de los sistemas externos usando puertos y adaptadores.
- Úsala para mejorar la testabilidad e intercambiar infraestructura sin tocar la lógica de dominio.
- Trade-off: capas de abstracción adicionales y esfuerzo de diseño inicial.

## Definición
**Qué es:** Un patrón arquitectónico que aísla el núcleo de la aplicación detrás de puertos (interfaces) bien definidos, con adaptadores implementando las integraciones (BD, [HTTP](../operations/http.md), mensajería) en los bordes.
**Términos clave:** puertos, adaptadores, adaptadores primarios/secundarios, inversión de dependencias, núcleo de aplicación.

## Por qué importa
- Evita que los detalles de infraestructura se filtren en la lógica de negocio.
- Facilita el testing sustituyendo adaptadores con fakes o mocks.

## Alcance y no-objetivos
**Dentro del alcance:** fronteras, dirección de dependencias y diseño de integraciones.
**Fuera del alcance / NO resuelto por esto:** estrategia de modelado de dominio o consistencia distribuida.

## Modelo mental / Intuición
- Imagina un hexágono: el núcleo está en el centro, los adaptadores se conectan en los lados.
- Las dependencias apuntan hacia adentro; el núcleo define lo que necesita, los adaptadores lo satisfacen.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tengas múltiples integraciones externas (BD, colas, APIs).
- Quieras una testabilidad fuerte del núcleo de la aplicación.
- Esperes cambios de infraestructura con el tiempo.
### Evítalo cuando
- El sistema sea simple y de corta vida.
- El equipo no pueda asumir la sobrecarga de abstracción.
- El acoplamiento estrecho sea aceptable y se requiera entrega más rápida.

## Cómo lo usaría (práctico)
- **Contexto:** Un servicio con BD, un broker de mensajes y APIs externas.
- **Pasos:**
  1) Definir puertos de entrada (casos de uso) y puertos de salida (dependencias).
  2) Mantener el núcleo libre de frameworks.
  3) Implementar adaptadores para [HTTP](../operations/http.md), BD y mensajería.
  4) Cablear dependencias con DI y probar con adaptadores en memoria.
- **Cómo se ve el éxito:** lógica del núcleo probada sin infraestructura y adaptadores reemplazables con cambios mínimos.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** alta testabilidad, fronteras claras, flexibilidad en cambios de infraestructura.
- **Contras / Riesgos:** más boilerplate, mayor carga cognitiva y entrega inicial más lenta.
### Alternativas
- **Arquitectura en capas:** más simple para sistemas pequeños pero menos flexible en las fronteras.
- **Cómo elegir:** prefiere hexagonal cuando la mantenibilidad a largo plazo y la rotación de integraciones sean probables.

## Modos de fallo y errores comunes
- Dejar que los frameworks se filtren en el núcleo de la aplicación.
- Puertos que reflejan detalles del adaptador en vez de necesidades del dominio.
- Adaptadores excesivamente fragmentados que generan complejidad sin beneficio.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tiempo de entrega de cambios en integraciones, cobertura de tests del núcleo.
- **Logs:** errores de adaptadores separados de fallos de validación del núcleo.
- **Trazas:** spans que distinguen claramente la lógica del núcleo vs las llamadas a adaptadores.
- **Alertas:** tasas de error crecientes en adaptadores o regresiones de acoplamiento creciente.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir puertos basados en casos de uso
  - [ ] Mantener las dependencias apuntando hacia adentro
  - [ ] Usar DTOs específicos del adaptador en los bordes
- **Notas de seguridad / cumplimiento**
  - Validar entradas en los adaptadores antes de entrar al núcleo.
- **Notas de rendimiento**
  - Evitar capas de mapeo excesivas; mantener los adaptadores delgados.
- **Notas operacionales**
  - Monitorear la salud de los adaptadores por separado de la lógica del núcleo.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Tratar los adaptadores como la fuente de verdad.
  - **Por qué es malo:** invierte las dependencias y contamina el núcleo.
  - **Mejor enfoque:** mantener las reglas de negocio en el núcleo y los adaptadores en el borde.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La arquitectura hexagonal mantiene el núcleo de negocio independiente definiendo puertos y conectando adaptadores en los bordes, haciendo la infraestructura reemplazable y el testing más fácil.

### Preguntas trampa (con respuestas)
1) **P:** ¿La arquitectura hexagonal obliga a usar microservicios?
   - **R:** No; es un enfoque de diseño modular que funciona dentro de monolitos o servicios.
2) **P:** ¿Los puertos son simplemente puertos de red?
   - **R:** No; son interfaces que definen cómo se usa el núcleo o qué necesita.
3) **P:** ¿Los adaptadores pueden depender del núcleo?
   - **R:** Sí; las dependencias deben apuntar hacia adentro, hacia el núcleo.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir la arquitectura hexagonal con precisión.
- [ ] Puedo indicar cuándo usarla y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Principios SOLID](../quality/solid-principles.md)
- [Patrones de diseño (resumen)](../quality/design-patterns.md)
- [Fundamentos de diseño de API](../system-design/api-design-basics.md)

### Temas relacionados
- [Diseño Dirigido por Dominio (DDD)](domain-driven-design.md)
- [CQRS](cqrs.md)
- [Arquitectura dirigida por eventos](event-driven-basics.md)
- [Patrón Outbox](outbox-pattern.md)

### Comparar con
- [Arquitectura dirigida por eventos](event-driven-basics.md) — estructura vs estilo de comunicación.

## Notas / Bandeja de entrada (opcional)
- N/A
