---
id: design-patterns
title: "Patrones de Diseño (Resumen)"
type: concept
status: learning
importance: 75
difficulty: medium
tags: [design, architecture, oop, reusability, best-practices]
canonical: true
aliases: [gof-patterns, software-patterns]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Patrones de Diseño (Resumen)

## TL;DR (BLUF)
- Los patrones de diseño son soluciones reutilizables a problemas comunes de diseño de software (ej., Strategy, Factory, Observer).
- Úsalos para resolver problemas recurrentes con enfoques probados, no como planos para aplicar en todas partes.
- Trade-off clave: flexibilidad y reutilización vs. simplicidad y directividad.

## Definición
**Qué es:** Mejores prácticas formalizadas para resolver problemas comunes de diseño en software, categorizados en patrones creacionales, estructurales y de comportamiento.  
**Términos clave:** Gang of Four (GoF), patrones creacionales (cómo se crean los objetos), patrones estructurales (cómo se componen los objetos), patrones de comportamiento (cómo interactúan los objetos), anti-patrón.

## Por qué importa
- **Vocabulario compartido:** "Usa un Factory" transmite una idea compleja rápidamente a otros ingenieros.
- **Soluciones probadas:** Los patrones codifican soluciones que han funcionado en muchos proyectos.
- **Mantenimiento más fácil:** Los patrones crean estructura reconocible, haciendo el código más fácil de entender.
- **Relevancia en entrevistas:** Los patrones de diseño son comunes en discusiones de diseño de sistemas y arquitectura.

## Alcance y no-objetivos
**Dentro del alcance:**
- Resumen de categorías de patrones (creacionales, estructurales, de comportamiento).
- Cuándo usar patrones y cuándo son excesivos.
- Patrones comunes: Singleton, Factory, Strategy, Observer, Adapter, Decorator.

**Fuera del alcance / NO resuelto por esto:**
- Catálogo exhaustivo de todos los patrones (23 patrones GoF + más).
- Implementación en lenguajes específicos (aunque se proporcionan ejemplos en Go).
- Patrones específicos de dominio (patrones de microservicios, patrones cloud—temas separados).

## Modelo mental / Intuición
Piensa en los patrones de diseño como **planos arquitectónicos**:
- No diseñas una casa desde cero cada vez; adaptas diseños probados (planta abierta, split-level, etc.).
- Los patrones son plantillas, no reglas rígidas—adáptalos a tu contexto.
- Usar un patrón donde no encaja es como construir una mansión para un perro (sobre-ingeniería).

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Encuentras un problema de diseño recurrente (ej., "¿Cómo creo objetos sin acoplarme a clases específicas?").
- El código está creciendo en complejidad y es difícil de extender (los patrones proporcionan estructura).
- El equipo se beneficia de un vocabulario compartido (los patrones comunican intención de diseño).

### Evítalo cuando
- El problema es simple y no se repite (la solución directa es más clara).
- Aplicas patrones prematuramente (YAGNI—You Aren't Gonna Need It).
- El equipo no está familiarizado con patrones (la curva de aprendizaje puede ralentizar la entrega).

## Cómo lo usaría (práctico)
- **Contexto:** Construyendo un sistema de notificaciones que envía emails, SMS y notificaciones push.
- **Pasos:**
  1. **Identificar problema:** Necesito seleccionar el método de notificación en tiempo de ejecución sin codificarlo fijo.
  2. **Elegir patrón:** Patrón Strategy (encapsular algoritmos, hacerlos intercambiables).
  3. **Implementación:**
     - Definir interfaz `Notifier`.
     - Crear implementaciones `EmailNotifier`, `SMSNotifier`, `PushNotifier`.
     - `NotificationService` depende de la interfaz `Notifier`, no de clases concretas.
  4. **Beneficio:** Agregar notificaciones de Slack requiere solo una nueva clase `SlackNotifier`, sin cambios al código existente.
- **Cómo se ve el éxito:** Nuevos métodos de notificación agregados en <1 hora con cero cambios al servicio central.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Resuelven problemas comunes con enfoques probados.
  - Mejoran la estructura y flexibilidad del código.
  - El vocabulario compartido acelera la comunicación.
- **Desventajas / Riesgos:**
  - Pueden sobre-complicar problemas simples.
  - Aplicación prematura de patrones (resolver problemas que no tienes).
  - Los patrones son específicos de lenguaje/paradigma (los patrones GoF están enfocados en POO).

### Alternativas
- **Sin patrones:** Resolver problemas directamente. Funciona para código simple y estable.
- **Patrones funcionales:** Composición, funciones de orden superior, inmutabilidad (paradigma diferente).
- **Enfoque pragmático:** Aprender patrones, aplicar cuando encajen, no forzarlos.

### Cómo elegir
- **Problema recurrente y complejo:** Usa un patrón.
- **Problema simple y único:** La solución directa es más clara.
- **Refactorización:** Introduce patrones cuando el código se vuelva difícil de mantener.

## Modos de fallo y trampas
- **Fiebre de patrones:** Aplicar patrones en todas partes, incluso cuando el código simple es suficiente.
- **Patrón equivocado:** Usar Factory cuando Strategy encaja mejor (solución desajustada).
- **Abuso de Singleton:** Singleton a menudo es un anti-patrón (estado global, difícil de probar).
- **Patrones cargo-cult:** Usar patrones sin entender por qué (seguir reglas ciegamente).
- **Ignorar la simplicidad:** Los patrones deberían simplificar, no complicar.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** N/A para los patrones en sí.
- **Logs:** N/A para los patrones en sí.
- **Trazas:** N/A para los patrones en sí.
- **Alertas:** N/A para los patrones en sí.

## Notas de implementación
- **Lista de verificación**
  - [ ] Identificar el problema recurrente o desafío de diseño.
  - [ ] Elegir un patrón que encaje (no forzar un patrón).
  - [ ] Implementar el patrón, adaptándolo a tu contexto.
  - [ ] Validar que el patrón simplifica el código (no lo complica).

- **Notas de seguridad / cumplimiento**
  - Los patrones no mejoran inherentemente la seguridad, pero la estructura puede ayudar (ej., Adapter aísla dependencias de terceros).

- **Notas de rendimiento**
  - Los patrones pueden introducir ligera sobrecarga (indirección, interfaces), pero generalmente insignificante.
  - No sacrificar rendimiento por pureza del patrón sin perfilar.

- **Notas operacionales**
  - Los patrones hacen el código más fácil de mantener y extender (cuando se aplican correctamente).

## Patrones comunes (Resumen breve)

### Patrones creacionales (Creación de objetos)
- **Factory:** Crear objetos sin especificar la clase exacta. Úsalo cuando la creación de objetos es compleja o varía según el contexto.
- **Builder:** Construir objetos complejos paso a paso. Úsalo para objetos con muchos parámetros opcionales.
- **Singleton:** Asegurar que solo exista una instancia. A menudo un anti-patrón (estado global, difícil de probar); usar con moderación.

### Patrones estructurales (Composición de objetos)
- **Adapter:** Envolver una interfaz incompatible para que coincida con la interfaz esperada. Úsalo al integrar bibliotecas de terceros.
- **Decorator:** Agregar comportamiento a objetos dinámicamente sin usar herencia. Úsalo para combinaciones flexibles de funcionalidades.
- **Facade:** Proporcionar una interfaz simplificada a un subsistema complejo. Úsalo para ocultar complejidad a los clientes.

### Patrones de comportamiento (Interacción de objetos)
- **Strategy:** Encapsular algoritmos, hacerlos intercambiables. Úsalo para selección de comportamiento en tiempo de ejecución.
- **Observer:** Notificar a múltiples objetos cuando cambia el estado. Úsalo para sistemas basados en eventos.
- **Command:** Encapsular solicitudes como objetos. Úsalo para deshacer/rehacer, encolamiento, registro.

## Mini ejemplo (Patrón Strategy en Go)
```go
// Strategy pattern: encapsulate algorithms
type Notifier interface {
    Send(ctx context.Context, userID string, message string) error
}

type EmailNotifier struct {
    client *EmailClient
}

func (n *EmailNotifier) Send(ctx context.Context, userID string, message string) error {
    return n.client.SendEmail(userID, message)
}

type SMSNotifier struct {
    client *SMSClient
}

func (n *SMSNotifier) Send(ctx context.Context, userID string, message string) error {
    return n.client.SendSMS(userID, message)
}

// Context uses strategy
type NotificationService struct {
    notifier Notifier // Strategy injected
}

func (s *NotificationService) Notify(ctx context.Context, userID string, message string) error {
    return s.notifier.Send(ctx, userID, message) // Strategy executed
}

// Usage: select strategy at runtime
emailService := &NotificationService{notifier: &EmailNotifier{client: emailClient}}
smsService := &NotificationService{notifier: &SMSNotifier{client: smsClient}}
```

## Anti-patrones comunes
- **Anti-patrón:** Usar Singleton para todo.
  - **Por qué es malo:** Estado global, difícil de probar, acoplamiento fuerte.
  - **Mejor enfoque:** Usar inyección de dependencias; limitar Singleton a recursos verdaderamente globales (logger, configuración).

- **Anti-patrón:** Aplicar patrones sin entender el problema.
  - **Por qué es malo:** Sobre-complica el código; los patrones deberían simplificar, no oscurecer.
  - **Mejor enfoque:** Entender el problema primero, luego elegir un patrón (si es necesario).

- **Anti-patrón:** Objetos dios disfrazados de patrones.
  - **Por qué es malo:** Una clase "Manager" o "Handler" haciendo todo no es un patrón; es un code smell.
  - **Mejor enfoque:** Seguir SRP; dividir en clases enfocadas.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
Los patrones de diseño son soluciones reutilizables a problemas comunes de diseño de software. Se categorizan en creacionales (cómo crear objetos), estructurales (cómo componer objetos) y de comportamiento (cómo interactúan los objetos). Por ejemplo, el patrón Strategy te permite intercambiar algoritmos en tiempo de ejecución—en lugar de codificar fijo "enviar email", defines una interfaz Notifier y creas EmailNotifier, SMSNotifier, etc. Los patrones proporcionan un vocabulario compartido (decir "usa un Factory" es más rápido que explicar la lógica de creación de objetos) y soluciones probadas. La clave es usar patrones cuando encajen, no forzarlos en todas partes—los patrones deberían simplificar el código, no complicarlo.

### Preguntas trampa (con respuestas)
1) **P:** ¿Siempre deberías usar patrones de diseño?
   - **R:** No. Usa patrones cuando resuelvan un problema real. Para código simple y estable, las soluciones directas son más claras. Los patrones son herramientas, no mandatos. Aplícalos cuando la complejidad justifique la estructura.

2) **P:** ¿Cuál es el patrón de diseño más importante?
   - **R:** No hay un solo patrón más importante; depende del problema. Strategy y Factory son muy comunes para flexibilidad. Singleton a menudo se usa en exceso (anti-patrón). Aprende el problema que cada patrón resuelve, luego aplica apropiadamente.

3) **P:** ¿Los patrones de diseño no son solo para programación orientada a objetos?
   - **R:** Los patrones GoF están enfocados en POO, pero los principios aplican a otros paradigmas. La programación funcional tiene sus propios patrones (composición, funciones de orden superior, mónadas). La idea central—soluciones reutilizables a problemas comunes—trasciende paradigmas.

4) **P:** ¿Cómo sabes qué patrón usar?
   - **R:** Identifica el problema primero. ¿Necesitas crear objetos sin acoplamiento? Factory. ¿Necesitas intercambiar algoritmos en tiempo de ejecución? Strategy. ¿Necesitas notificar a múltiples objetos de cambios de estado? Observer. No empieces con un patrón; empieza con el problema.

5) **P:** ¿Los patrones pueden perjudicar la calidad del código?
   - **R:** Sí, si se aplican mal. Sobre-ingenierizar con patrones hace el código más difícil de entender (más indirección, más clases). Usa patrones para simplificar, no para demostrar conocimiento. Si una solución directa es más clara, úsala.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo nombrar al menos 5 patrones de diseño y categorizarlos (creacional/estructural/comportamiento).
- [ ] Puedo explicar cuándo usar el patrón Strategy y cuándo es excesivo.
- [ ] Puedo describir por qué Singleton a menudo es un anti-patrón.
- [ ] Puedo articular el trade-off entre flexibilidad (patrones) y simplicidad (código directo).
- [ ] Puedo dar un ejemplo de un problema y elegir un patrón apropiado.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Principios SOLID](solid-principles.md)
- [Código limpio](clean-code.md)

### Temas relacionados
- [Calidad de código](code-quality.md)
- [Técnicas de refactorización](refactoring-techniques.md)

### Comparar con
- [Principios SOLID](solid-principles.md) — SOLID proporciona principios de diseño; los patrones proporcionan soluciones concretas.

## Notas / Bandeja de entrada (opcional)
- Considerar crear temas detallados para patrones individuales (Strategy, Factory, Observer, etc.).
- Agregar ejemplos de proyectos reales: Factory para crear clientes de base de datos, Strategy para enrutamiento de notificaciones.
- Enlazar al concepto "Refactoring to Patterns" cuando sea creado.
