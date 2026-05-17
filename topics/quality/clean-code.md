---
id: clean-code
title: "Código Limpio"
type: concept
status: learning
importance: 85
difficulty: medium
tags: [quality, readability, maintainability, naming, design]
canonical: true
aliases: [readable-code, maintainable-code]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Código Limpio

## TL;DR (BLUF)
- El código limpio es código que es fácil de leer, entender y modificar—optimizado para la comprensión humana.
- Usa nombres significativos, funciones pequeñas, estructura clara y estilo consistente.
- Trade-off clave: tiempo invertido en claridad vs. entregar funcionalidades rápidamente.

## Definición
**Qué es:** Código escrito con claridad, simplicidad y mantenibilidad como objetivos principales, siguiendo principios como nombres significativos, responsabilidad única y complejidad mínima.  
**Términos clave:** legibilidad, código auto-documentado, principio de responsabilidad única (SRP), DRY (Don't Repeat Yourself), YAGNI (You Aren't Gonna Need It), code smell, refactorización.

## Por qué importa
- **Desarrollo más rápido:** Los ingenieros pasan el 70-90% de su tiempo leyendo código, no escribiéndolo. Código legible = iteración más rápida.
- **Menos errores:** El código claro hace que los errores lógicos sean obvios; el código complejo oculta errores.
- **Onboarding más fácil:** Los nuevos ingenieros se adaptan más rápido en bases de código limpias.
- **Relevancia en entrevistas:** Los ingenieros senior deben escribir y defender código limpio bajo presión (entrevistas de pizarra, revisiones de código).

## Alcance y no-objetivos
**Dentro del alcance:**
- Convenciones de nomenclatura (variables, funciones, clases).
- Tamaño de funciones y clases (responsabilidad única).
- Organización y estructura del código.
- Evitar code smells (duplicación, números mágicos, anidamiento profundo).

**Fuera del alcance / NO resuelto por esto:**
- Optimización de rendimiento (a veces entra en conflicto con la legibilidad).
- Arquitectura o diseño de sistemas (preocupación separada).
- Modismos específicos de lenguaje (aunque los principios de código limpio aplican universalmente).

## Modelo mental / Intuición
Piensa en el código limpio como **escribir prosa, no poesía**:
- **Prosa:** Clara, directa, comprensible por cualquiera. Ejemplo: `calculateMonthlyPayment()`.
- **Poesía:** Ingeniosa, concisa, pero requiere interpretación. Ejemplo: `cmp()` (¿qué calcula?).

El código se lee mucho más a menudo de lo que se escribe. Optimiza para el lector, no para el escritor.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El código se mantendrá a largo plazo (sistemas de producción).
- Múltiples ingenieros trabajarán en la base de código.
- La complejidad es inevitable (el código limpio hace la lógica compleja manejable).

### Evítalo cuando
- Escribes código desechable (prototipos, scripts de un solo uso).
- Optimizas para rendimiento (a veces código ingenioso y difícil de leer es necesario para rutas críticas; úsalo con moderación y documenta).

## Cómo lo usaría (práctico)
- **Contexto:** Refactorizando un handler de API en Go que procesa pedidos.
- **Pasos:**
  1. **Nombres significativos:** Renombrar `proc()` a `processOrder()`, `u` a `user`, `res` a `result`.
  2. **Extraer funciones:** Dividir handler de 200 líneas en funciones más pequeñas (validar, obtener, cobrar, notificar).
  3. **Responsabilidad única:** Cada función hace una cosa (validateOrder verifica entradas; chargeUser maneja el pago).
  4. **Eliminar duplicación:** Extraer lógica repetida en funciones compartidas.
  5. **Reducir anidamiento:** Usar retornos tempranos en lugar de cadenas if-else profundas.
  6. **Agregar comentarios con moderación:** Solo para "por qué" (no "qué")—el código debe ser auto-documentado.
- **Cómo se ve el éxito:** El handler tiene <50 líneas; la lógica es obvia; nuevos ingenieros lo entienden en <10 minutos.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Desarrollo más rápido con el tiempo (fácil de leer = fácil de cambiar).
  - Menos errores (lógica clara hace que los errores sean obvios).
  - Menor carga cognitiva (más fácil de razonar).
- **Desventajas / Riesgos:**
  - Costo de tiempo inicial (refactorización, nomenclatura, extracción).
  - Puede entrar en conflicto con el rendimiento (a veces código "feo" es más rápido; usa profiling para decidir).
  - Subjetivo (lo que es "limpio" para una persona puede diferir).

### Alternativas
- **"Solo hazlo funcionar":** Rápido inicialmente pero crea deuda técnica (difícil de cambiar, propenso a errores).
- **Sobre-ingeniería:** Abstracción prematura (demasiadas interfaces, capas) hace el código más difícil de seguir.
- **Código ingenioso:** Conciso pero críptico (difícil de entender para otros).

### Cómo elegir
- **Sistema de producción de larga vida:** Invertir en código limpio (nomenclatura, estructura, refactorización).
- **Prototipo / MVP:** Priorizar velocidad; limpiar después si se convierte en código de producción.
- **Ruta crítica de rendimiento:** Perfilar primero; optimizar solo rutas críticas; documentar por qué el código es "feo".

## Modos de fallo y trampas
- **Abstracción prematura:** Crear interfaces/capas antes de necesitarlas (violación de YAGNI).
- **Exceso de comentarios:** Explicar "qué" en lugar de "por qué" (el código debe ser auto-documentado).
- **Nombres poco claros:** `data`, `temp`, `x`, `proc()` (los lectores deben adivinar el significado).
- **Funciones/clases dios:** Una sola función haciendo todo (imposible de probar o entender).
- **Anidamiento profundo:** 5+ niveles de if-else (difícil seguir la lógica).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Complejidad ciclomática (señalar funciones >10).
  - Longitud de función (señalar funciones >50 líneas).
  - Profundidad de anidamiento (señalar >3 niveles).
  - Tiempo de revisión de PR (revisiones largas sugieren código poco claro).
- **Logs:** N/A para código limpio en sí.
- **Trazas:** N/A para código limpio en sí.
- **Alertas:** Picos de complejidad en código nuevo.

## Notas de implementación
- **Lista de verificación**
  - [ ] Usar nombres significativos (variables, funciones, clases).
  - [ ] Mantener funciones pequeñas (<50 líneas, responsabilidad única).
  - [ ] Evitar anidamiento profundo (usar retornos tempranos, cláusulas de guarda).
  - [ ] Eliminar duplicación (DRY: extraer lógica compartida).
  - [ ] Evitar números mágicos (usar constantes o enums).
  - [ ] Comentar "por qué", no "qué" (el código se explica solo).
  - [ ] Usar estilo consistente (los linters lo imponen).
  - [ ] Refactorizar oportunísticamente (al tocar código para funcionalidades).

- **Notas de seguridad / cumplimiento**
  - El código limpio hace las revisiones de seguridad más fáciles (lógica clara = más fácil detectar vulnerabilidades).
  - Evitar código criptográfico ingenioso (usar bibliotecas; limpio y seguro).

- **Notas de rendimiento**
  - El código limpio a menudo es eficiente (lógica más simple = más fácil de optimizar).
  - Si la optimización requiere código "feo", aislarlo y documentar por qué.

- **Notas operacionales**
  - El código limpio reduce incidentes en producción (lógica clara = menos errores).
  - Usar post-mortems para identificar brechas de claridad en el código (lógica confusa llevó a corrección incorrecta).

## Mini ejemplo
```go
// ANTES: Poco claro, complejo, difícil de seguir
func p(o Order, u User) error {
    if o.T > 0 && u.ID != "" {
        if u.B >= o.T {
            if u.S == "a" {
                // ... lógica de cobro
                return nil
            } else {
                return errors.New("inactive")
            }
        } else {
            return errors.New("low balance")
        }
    }
    return errors.New("invalid")
}

// DESPUÉS: Claro, simple, auto-documentado
func processOrder(order Order, user User) error {
    if err := validateOrder(order); err != nil {
        return fmt.Errorf("invalid order: %w", err)
    }
    
    if user.Status != "active" {
        return ErrUserInactive
    }
    
    if user.Balance < order.Total {
        return ErrInsufficientBalance
    }
    
    return chargeUser(user, order.Total)
}

func validateOrder(order Order) error {
    if order.Total <= 0 {
        return errors.New("order total must be positive")
    }
    if order.UserID == "" {
        return errors.New("user ID is required")
    }
    return nil
}
```

## Anti-patrones comunes
- **Anti-patrón:** Abreviar todo (`usr`, `ord`, `proc`, `res`).
  - **Por qué es malo:** Ahorra 2 caracteres pero cuesta 10 segundos cada vez que alguien lo lee. Multiplica por miles de lecturas.
  - **Mejor enfoque:** Usar palabras completas (`user`, `order`, `process`, `result`).

- **Anti-patrón:** Funciones que hacen múltiples cosas no relacionadas.
  - **Por qué es malo:** Difícil de probar, entender o reusar; viola el principio de responsabilidad única.
  - **Mejor enfoque:** Dividir en funciones enfocadas (`validateOrder`, `chargeUser`, `sendNotification`).

- **Anti-patrón:** Números mágicos (`if status == 3`).
  - **Por qué es malo:** ¿Qué significa `3`? El lector debe buscar en la base de código o documentación.
  - **Mejor enfoque:** Usar constantes o enums (`if status == StatusActive`).

- **Anti-patrón:** Anidamiento profundo (5+ niveles de if-else).
  - **Por qué es malo:** Difícil seguir la lógica; alta complejidad ciclomática.
  - **Mejor enfoque:** Usar retornos tempranos, cláusulas de guarda, o extraer funciones.

- **Anti-patrón:** Comentarios explicando "qué" hace el código.
  - **Por qué es malo:** Si el código necesita comentarios para explicar "qué", no es claro. El código debe ser auto-documentado.
  - **Mejor enfoque:** Renombrar variables/funciones para claridad; comentar solo "por qué" (reglas de negocio, trade-offs).

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
El código limpio es código optimizado para lectores humanos. Usa nombres significativos para que no tengas que adivinar qué significa `x` o `proc()`. Mantiene funciones pequeñas y enfocadas (responsabilidad única). Evita anidamiento profundo y duplicación. El objetivo es hacer el código tan claro que los comentarios rara vez sean necesarios—el código mismo explica lo que hace. El código limpio se paga a largo plazo porque los ingenieros pasan la mayor parte de su tiempo leyendo, no escribiendo. Cuanto más fácil es de leer, más rápido puedes iterar.

### Preguntas trampa (con respuestas)
1) **P:** ¿El código limpio no toma más tiempo de escribir?
   - **R:** Inicialmente sí, pero ahorra tiempo durante la vida de la base de código. Los ingenieros pasan el 70-90% de su tiempo leyendo código. Código claro = iteración más rápida, depuración más fácil, refactorización más segura. El costo inicial se recupera rápidamente.

2) **P:** ¿El código limpio es solo sobre formato y estilo?
   - **R:** No. El estilo (formato, indentación) es la parte más pequeña. El código limpio se trata de estructura (funciones pequeñas, responsabilidad única), nomenclatura (nombres significativos) y lógica (evitar complejidad, duplicación). Los linters imponen estilo; los principios de código limpio guían el diseño.

3) **P:** ¿El código limpio puede perjudicar el rendimiento?
   - **R:** Rara vez. El código más simple a menudo es más rápido porque es más fácil de optimizar. Si el rendimiento requiere código "feo" (por ejemplo, desenrollado manual de bucles), aislarlo, perfilar para confirmar que es necesario y documentar por qué. No sacrificar claridad prematuramente.

4) **P:** ¿Cómo equilibras código limpio con plazos de entrega?
   - **R:** El código limpio es más rápido a largo plazo. Para MVPs, priorizar entregar pero asignar tiempo para limpieza antes de que se convierta en código de producción. Usar seguimiento de deuda técnica para asegurar que la limpieza ocurra. No dejar que "lo limpiaremos después" se vuelva permanente.

5) **P:** ¿Cuál es el principio de código limpio más importante?
   - **R:** Nombres significativos. Si variables, funciones y clases tienen nombres claros, la mayoría del código se vuelve auto-documentado. En segundo lugar está la responsabilidad única—funciones que hacen una cosa son más fáciles de entender, probar y reusar.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar por qué el código limpio acelera el desarrollo a largo plazo.
- [ ] Puedo nombrar al menos 3 principios de código limpio (nomenclatura, SRP, DRY, etc.).
- [ ] Puedo refactorizar una función compleja en funciones más pequeñas y enfocadas.
- [ ] Puedo identificar un code smell (anidamiento profundo, números mágicos, nombres poco claros) y corregirlo.
- [ ] Puedo articular cuándo priorizar rendimiento sobre legibilidad (y cuándo no).

## Enlaces (SIN duplicación)
### Prerequisitos
- [Calidad de código](code-quality.md)
- [Aseguramiento de Calidad de Software](software-quality-assurance.md)

### Temas relacionados
- [Pirámide de pruebas](testing-pyramid.md)
- [Compuertas de calidad](quality-gates.md)
- [Principios SOLID](solid-principles.md)
- [Técnicas de refactorización](refactoring-techniques.md)

### Comparar con
- [Calidad de código](code-quality.md) — La calidad de código mide estructura y corrección; el código limpio optimiza para legibilidad.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: refactorizar handlers de Heimdall, simplificar lógica de Opportunity Actions.
- Considerar agregar sección sobre código limpio para lenguajes específicos (modismos de Go, modismos de Python).
- Referenciar "Clean Code" de Robert C. Martin (Uncle Bob) para lectura más profunda (enlace externo en versiones futuras).
