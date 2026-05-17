---
id: solid-principles
title: "Principios SOLID"
type: concept
status: learning
importance: 80
difficulty: medium
tags: [design, oop, architecture, maintainability, clean-code]
canonical: true
aliases: [solid, object-oriented-design]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Principios SOLID

## TL;DR (BLUF)
- SOLID son cinco principios de diseño orientado a objetos que promueven código mantenible y flexible: Responsabilidad Única, Abierto/Cerrado, Sustitución de Liskov, Segregación de Interfaces, Inversión de Dependencias.
- Úsalos para guiar el diseño de clases/módulos, especialmente cuando el código crece en complejidad.
- Trade-off clave: simplicidad de diseño vs. flexibilidad para cambios futuros.

## Definición
**Qué es:** Un conjunto de cinco principios de diseño para programación orientada a objetos que ayudan a crear código más fácil de mantener, extender y probar.  
**Términos clave:**
- **S**ingle Responsibility Principle (SRP) - Principio de Responsabilidad Única
- **O**pen/Closed Principle (OCP) - Principio Abierto/Cerrado
- **L**iskov Substitution Principle (LSP) - Principio de Sustitución de Liskov
- **I**nterface Segregation Principle (ISP) - Principio de Segregación de Interfaces
- **D**ependency Inversion Principle (DIP) - Principio de Inversión de Dependencias

## Por qué importa
- **Mantenibilidad:** El código SOLID es más fácil de entender, modificar y depurar.
- **Testabilidad:** El código débilmente acoplado es más fácil de probar en aislamiento.
- **Flexibilidad:** El código diseñado con SOLID es más fácil de extender sin romper funcionalidad existente.
- **Relevancia en entrevistas:** Los principios SOLID son fundamentales para discusiones de diseño en ingeniería senior.

## Alcance y no-objetivos
**Dentro del alcance:**
- Entender cada principio SOLID con ejemplos prácticos.
- Cuándo aplicar SOLID y cuándo es excesivo.
- Trade-offs entre flexibilidad y simplicidad.

**Fuera del alcance / NO resuelto por esto:**
- Patrones de diseño específicos (Strategy, Factory, etc.—aunque SOLID los habilita).
- Paradigmas de programación funcional (SOLID está enfocado en POO, aunque los principios aplican conceptualmente).

## Modelo mental / Intuición
Piensa en SOLID como **bloques de construcción para sistemas flexibles**:
- **SRP:** Cada bloque hace una cosa (pieza de Lego, no navaja suiza).
- **OCP:** Puedes agregar nuevos bloques sin modificar los existentes (extensión, no modificación).
- **LSP:** Los bloques de reemplazo funcionan de la misma manera (sustituibilidad).
- **ISP:** Solo conecta bloques que encajan (interfaces pequeñas y enfocadas).
- **DIP:** Los bloques de alto nivel no dependen de los de bajo nivel (abstracciones, no concreciones).

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El código está creciendo en complejidad y es difícil de mantener.
- Múltiples ingenieros trabajan en la base de código (SOLID permite la colaboración).
- Los requisitos cambian frecuentemente (SOLID hace el código flexible).
- Construyes sistemas de producción de larga vida.

### Evítalo cuando
- Escribes código desechable (prototipos, scripts de un solo uso).
- El código es simple y es poco probable que cambie (SOLID puede sobre-complicar).
- El equipo no está familiarizado con SOLID (la curva de aprendizaje puede ralentizar la entrega inicialmente).

## Cómo lo usaría (práctico)
- **Contexto:** Refactorizando un sistema de notificaciones en Go que envía emails, SMS y notificaciones push.
- **Pasos:**
  1. **SRP:** Dividir el monolítico `NotificationService` en `EmailSender`, `SMSSender`, `PushSender` (cada uno hace una cosa).
  2. **OCP:** Definir interfaz `Notifier`; nuevos tipos de notificación (Slack) no modifican código existente.
  3. **LSP:** Todas las implementaciones de `Notifier` siguen el mismo contrato (enviar mensaje, retornar error).
  4. **ISP:** No forzar a todos los notifiers a implementar `GetDeliveryReport` si solo email lo soporta; dividir interfaces.
  5. **DIP:** El `NotificationManager` de alto nivel depende de la interfaz `Notifier`, no del concreto `EmailSender`.
- **Cómo se ve el éxito:** Agregar notificaciones de Slack requiere cero cambios al código existente; solo nueva implementación de `SlackNotifier`.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Más fácil de extender (nuevas funcionalidades no rompen código existente).
  - Más fácil de probar (unidades pequeñas y enfocadas).
  - Reduce acoplamiento (cambios localizados).
- **Desventajas / Riesgos:**
  - Más clases/interfaces (puede sentirse sobre-ingenierizado para casos simples).
  - Costo de diseño inicial (pensar en las abstracciones).
  - Puede llevar a abstracción prematura (violación de YAGNI).

### Alternativas
- **Sin principios:** Rápido inicialmente pero se vuelve inmantenible conforme el código crece.
- **Programación funcional:** Paradigma diferente pero comparte objetivos (inmutabilidad, funciones puras, composición).
- **POO pragmática:** Aplicar SOLID selectivamente donde la complejidad lo justifica.

### Cómo elegir
- **Sistemas complejos y en evolución:** Aplicar SOLID para gestionar la complejidad.
- **Código simple y estable:** Mantenerlo simple; no sobre-ingenierizar.
- **Refactorización:** Introducir SOLID cuando el código se vuelva difícil de mantener.

## Modos de fallo y trampas
- **Abstracción prematura:** Crear interfaces antes de necesitarlas (viola YAGNI).
- **Explosión de interfaces:** Demasiadas interfaces pequeñas; difícil navegar la base de código.
- **Violar SRP:** Clases dios que hacen todo (imposible de probar o mantener).
- **Malentender LSP:** Subclases que cambian comportamiento de formas sorprendentes (rompe el polimorfismo).
- **Sobre-aplicar DIP:** Inyectar dependencias en todas partes (agrega complejidad sin beneficio).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tamaño de clase (líneas de código; señalar >500).
  - Acoplamiento (cuántas dependencias tiene una clase; señalar >10).
  - Complejidad de métodos (complejidad ciclomática; señalar >10).
- **Logs:** N/A para SOLID en sí.
- **Trazas:** N/A para SOLID en sí.
- **Alertas:** N/A para SOLID en sí.

## Notas de implementación
- **Lista de verificación**
  - [ ] Asegurar que cada clase tenga una sola razón para cambiar (SRP).
  - [ ] Diseñar para extensión, no modificación (OCP).
  - [ ] Verificar que las subclases puedan reemplazar a las clases base (LSP).
  - [ ] Mantener interfaces pequeñas y enfocadas (ISP).
  - [ ] Depender de abstracciones, no de concreciones (DIP).

- **Notas de seguridad / cumplimiento**
  - SOLID hace la seguridad más fácil (clases pequeñas y enfocadas son más fáciles de auditar).

- **Notas de rendimiento**
  - SOLID puede introducir ligera sobrecarga (interfaces, indirección), pero generalmente insignificante.
  - La optimización prematura es el enemigo; priorizar mantenibilidad.

- **Notas operacionales**
  - El código SOLID es más fácil de depurar (responsabilidades claras, acoplamiento débil).

## Mini ejemplo (Go)
```go
// ANTES: Viola SRP, OCP, DIP
type NotificationService struct {
    emailClient *EmailClient
    smsClient   *SMSClient
}

func (s *NotificationService) Send(userID string, message string, method string) error {
    if method == "email" {
        return s.emailClient.Send(userID, message)
    } else if method == "sms" {
        return s.smsClient.Send(userID, message)
    }
    return errors.New("unsupported method")
}
// Problema: Agregar Slack requiere modificar Send() (viola OCP)
// Problema: Depende de EmailClient/SMSClient concretos (viola DIP)

// DESPUÉS: Sigue SRP, OCP, DIP
type Notifier interface { // DIP: depender de abstracción
    Send(ctx context.Context, userID string, message string) error
}

type EmailNotifier struct {
    client *EmailClient
}

func (n *EmailNotifier) Send(ctx context.Context, userID string, message string) error {
    return n.client.Send(userID, message)
}

type SMSNotifier struct {
    client *SMSClient
}

func (n *SMSNotifier) Send(ctx context.Context, userID string, message string) error {
    return n.client.Send(userID, message)
}

type NotificationManager struct {
    notifiers map[string]Notifier // DIP: depender de interfaz
}

func (m *NotificationManager) Send(ctx context.Context, method string, userID string, message string) error {
    notifier, ok := m.notifiers[method]
    if !ok {
        return errors.New("unsupported method")
    }
    return notifier.Send(ctx, userID, message) // OCP: agregar Slack no modifica este código
}
// SRP: Cada notifier tiene responsabilidad única
// OCP: Agregar SlackNotifier sin cambiar NotificationManager
// LSP: Todos los notifiers siguen el mismo contrato
// DIP: NotificationManager depende de la interfaz Notifier
```

## Cada principio explicado

### Principio de Responsabilidad Única (SRP)
**Definición:** Una clase debería tener una, y solo una, razón para cambiar.

**Ejemplo de violación:** `UserService` maneja autenticación, autorización, notificaciones y logging.  
**Corrección:** Dividir en `AuthService`, `AuthzService`, `NotificationService`, `Logger`.

**Por qué importa:** Las clases pequeñas y enfocadas son más fáciles de entender, probar y modificar.

### Principio Abierto/Cerrado (OCP)
**Definición:** Las entidades de software deben estar abiertas para extensión pero cerradas para modificación.

**Ejemplo de violación:** Agregar un nuevo método de pago requiere modificar la clase `PaymentProcessor`.  
**Corrección:** Definir interfaz `PaymentMethod`; agregar nuevas implementaciones sin cambiar código existente.

**Por qué importa:** Extender funcionalidad sin arriesgar código existente (menos regresiones).

### Principio de Sustitución de Liskov (LSP)
**Definición:** Las subclases deben ser sustituibles por sus clases base sin alterar la corrección.

**Ejemplo de violación:** `Square` extiende `Rectangle` pero rompe el contrato de `setWidth()`/`setHeight()` (cambiar ancho también cambia alto).  
**Corrección:** No usar herencia donde no encaja; usar composición o jerarquías separadas.

**Por qué importa:** El polimorfismo funciona correctamente solo cuando se mantiene la sustituibilidad.

### Principio de Segregación de Interfaces (ISP)
**Definición:** Los clientes no deberían ser forzados a depender de interfaces que no usan.

**Ejemplo de violación:** La interfaz `Worker` tiene `work()`, `eat()`, `sleep()`. `Robot` implementa `Worker` pero no necesita `eat()` ni `sleep()`.  
**Corrección:** Dividir en interfaces `Workable`, `Eatable`, `Sleepable`.

**Por qué importa:** Las interfaces pequeñas reducen acoplamiento y hacen las implementaciones más simples.

### Principio de Inversión de Dependencias (DIP)
**Definición:** Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.

**Ejemplo de violación:** `OrderService` instancia directamente `MySQLDatabase`.  
**Corrección:** `OrderService` depende de la interfaz `Database`; inyectar implementación (MySQL, PostgreSQL, fake).

**Por qué importa:** El acoplamiento débil permite pruebas, flexibilidad e intercambio de implementaciones.

## Anti-patrones comunes
- **Anti-patrón:** Clase dios (una clase haciendo todo).
  - **Por qué es malo:** Viola SRP; imposible de probar o mantener.
  - **Mejor enfoque:** Dividir en clases enfocadas con responsabilidades únicas.

- **Anti-patrón:** Modificar código existente para agregar funcionalidades.
  - **Por qué es malo:** Viola OCP; arriesga romper funcionalidad existente.
  - **Mejor enfoque:** Usar interfaces y polimorfismo para extender sin modificar.

- **Anti-patrón:** Subclases que cambian el comportamiento de la clase base inesperadamente.
  - **Por qué es malo:** Viola LSP; rompe el polimorfismo y causa errores.
  - **Mejor enfoque:** Asegurar que las subclases respeten los contratos de la clase base.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
SOLID son cinco principios de diseño para escribir código orientado a objetos mantenible. SRP dice que cada clase debe hacer una cosa. OCP dice que debes extender código sin modificarlo (usar interfaces). LSP dice que las subclases deben funcionar en cualquier lugar donde funcione la clase base. ISP dice mantener interfaces pequeñas y enfocadas. DIP dice depender de abstracciones, no de implementaciones concretas. Juntos, estos principios hacen el código más fácil de probar, extender y mantener. Son especialmente valiosos conforme los sistemas crecen en complejidad y los requisitos cambian.

### Preguntas trampa (con respuestas)
1) **P:** ¿SOLID no lleva a demasiadas clases y complejidad?
   - **R:** Puede, si se sobre-aplica. Usa SOLID cuando la complejidad lo justifica (requisitos en evolución, múltiples ingenieros, sistemas de larga vida). Para código simple y estable, mantenlo simple. SOLID es una herramienta, no un mandato.

2) **P:** ¿Cuál es el principio SOLID más importante?
   - **R:** SRP (Responsabilidad Única). Si cada clase hace una cosa, la mayoría de los otros principios se siguen naturalmente. SRP es la base; los demás se construyen sobre él.

3) **P:** ¿Puedes aplicar SOLID en lenguajes no-POO como Go?
   - **R:** Sí, conceptualmente. Go no tiene clases pero tiene structs e interfaces. SRP aplica a funciones/paquetes. OCP usa interfaces para extensión. DIP usa inyección de interfaces. Los principios se adaptan al paradigma.

4) **P:** ¿Cómo sabes cuándo estás violando SRP?
   - **R:** Pregunta "¿cuántas razones podría tener esta clase para cambiar?" Si la respuesta es más de una (ej., cambiar lógica de negocio Y cambiar almacenamiento de datos), viola SRP. También observa clases largas (>500 líneas) o nombres vagos como `Manager`, `Helper`, `Util`.

5) **P:** ¿DIP no hace el código más difícil de seguir (demasiada indirección)?
   - **R:** A veces, sí. Usa DIP cuando necesitas flexibilidad (pruebas, intercambiar implementaciones, desacoplar capas). Para dependencias simples y estables, el acoplamiento directo está bien. Equilibrar flexibilidad con simplicidad.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo nombrar los cinco principios SOLID y explicar cada uno en una oración.
- [ ] Puedo dar un ejemplo de violar SRP y cómo corregirlo.
- [ ] Puedo explicar la diferencia entre OCP y LSP.
- [ ] Puedo describir cuándo SOLID es excesivo (y cuándo es esencial).
- [ ] Puedo refactorizar una clase dios en clases enfocadas siguiendo SRP.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Código limpio](clean-code.md)
- [Calidad de código](code-quality.md)

### Temas relacionados
- [Fundamentos de pruebas](testing-fundamentals.md)
- [Patrones de diseño](design-patterns.md)
- [Técnicas de refactorización](refactoring-techniques.md)

### Comparar con
- [Código limpio](clean-code.md) — El código limpio se enfoca en legibilidad; SOLID se enfoca en estructura y flexibilidad.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: refactorizar Heimdall para testabilidad, sistema de notificaciones de Opportunity Actions.
- Considerar agregar sección sobre SOLID en Go vs. otros lenguajes (diferencias en aplicar los principios).
- Enlazar a patrones de diseño específicos (Strategy, Factory, Observer) cuando sean creados.
