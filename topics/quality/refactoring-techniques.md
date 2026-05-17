---
id: refactoring-techniques
title: "Técnicas de Refactorización"
type: skill
status: learning
importance: 80
difficulty: medium
tags: [refactoring, clean-code, maintainability, technical-debt]
canonical: true
aliases: [code-refactoring, refactoring-basics]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Técnicas de Refactorización

## TL;DR (BLUF)
- La refactorización es reestructurar código para mejorar el diseño sin cambiar el comportamiento—más seguro con pruebas como red de seguridad.
- Usa cambios pequeños e incrementales (extraer función, renombrar variable, eliminar duplicación) en lugar de grandes reescrituras.
- Trade-off clave: tiempo invertido en mejorar la estructura vs. entregar funcionalidades.

## Definición
**Qué es:** El proceso de mejorar la estructura, legibilidad y mantenibilidad del código sin alterar su comportamiento externo.  
**Términos clave:** code smell (síntoma de mal diseño), deuda técnica, red-green-refactor (ciclo TDD), extraer método/función, inline, renombrar, eliminar duplicación, simplificar condicionales.

## Por qué importa
- **Previene la degradación:** El código se degrada con el tiempo sin refactorización; se vuelve inmantenible.
- **Permite velocidad:** El código limpio es más rápido de cambiar; la refactorización se paga en velocidad de entrega.
- **Reduce errores:** El código más simple tiene menos lugares donde los errores se esconden.
- **Cambios más seguros:** Las pruebas aseguran que la refactorización no rompa el comportamiento.
- **Relevancia en entrevistas:** Los ingenieros senior deben demostrar capacidad para mejorar la calidad del código manteniendo la corrección.

## Alcance y no-objetivos
**Dentro del alcance:**
- Técnicas comunes de refactorización (extraer función, renombrar, eliminar duplicación, etc.).
- Cuándo refactorizar y cuándo dejar el código como está.
- Flujo de trabajo de refactorización segura (pruebas → cambios pequeños → verificar).

**Fuera del alcance / NO resuelto por esto:**
- Reescrituras (reemplazar sistemas completos—diferente de refactorización).
- Optimización de rendimiento (preocupación separada, aunque la refactorización puede habilitarla).

## Modelo mental / Intuición
Piensa en la refactorización como **ordenar un espacio de trabajo**:
- Ordenar pequeño y frecuente (diario): Fácil, bajo riesgo, mantiene el espacio utilizable.
- Dejar que se acumule el desorden (meses): Eventualmente requiere una gran limpieza (riesgosa, costosa).
- La refactorización es orden continuo, no una reorganización anual.

También, refactorizar con pruebas es como **editar con red de seguridad**: las pruebas te atrapan si rompes el comportamiento.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El código es difícil de entender o cambiar (funciones largas, anidamiento profundo, duplicación).
- Agregar funcionalidades requiere tocar código desordenado (refactorizar primero, luego agregar funcionalidad).
- Existen pruebas (refactorizar sin pruebas es riesgoso).
- La deuda técnica está ralentizando la entrega.

### Evítalo cuando
- El código funciona y rara vez cambia (áreas estables, de baja rotación).
- No hay pruebas (refactorizar sin pruebas arriesga romper el comportamiento).
- El plazo de entrega de la funcionalidad es inmediato (refactorizar después de entregar; o refactorizar oportunísticamente).

## Cómo lo usaría (práctico)
- **Contexto:** Refactorizando una función de 300 líneas en Go que procesa pedidos.
- **Pasos:**
  1. **Asegurar que existan pruebas:** Si no, escribir pruebas de caracterización (capturar comportamiento actual).
  2. **Identificar code smells:** Función larga, anidamiento profundo, lógica de validación duplicada.
  3. **Extraer funciones:** Dividir en `validateOrder()`, `fetchUser()`, `chargePayment()`, `sendConfirmation()`.
  4. **Ejecutar pruebas después de cada extracción:** Verificar que el comportamiento no cambió.
  5. **Renombrar variables:** `o` → `order`, `u` → `user`, `res` → `result`.
  6. **Eliminar duplicación:** Extraer validación repetida en `validateEmail()`.
  7. **Simplificar condicionales:** Reemplazar if-else anidados con retornos tempranos.
  8. **Hacer commits incrementales:** Commits pequeños, cada uno con pruebas pasando.
- **Cómo se ve el éxito:** La función tiene <50 líneas; la lógica es clara; las pruebas siguen pasando; agregar una nueva funcionalidad toma <1 hora.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Mejora la mantenibilidad (más fácil de cambiar con el tiempo).
  - Reduce errores (código más simple, menos escondites).
  - Acelera la entrega a largo plazo (código limpio = iteración más rápida).
- **Desventajas / Riesgos:**
  - Costo de tiempo inicial (la refactorización toma tiempo).
  - Riesgo de romper comportamiento (mitigado por pruebas).
  - Puede ser tentador sobre-refactorizar (abstracción prematura).

### Alternativas
- **Reescribir desde cero:** Riesgoso; a menudo toma más tiempo del esperado; puede introducir nuevos errores.
- **Dejar el código como está:** Rápido a corto plazo pero acumula deuda (eventualmente ralentiza la entrega).
- **Refactorización oportunista:** Refactorizar solo al tocar código para funcionalidades (enfoque equilibrado).

### Cómo elegir
- **Código desordenado con buenas pruebas:** Refactorizar incrementalmente.
- **Código desordenado sin pruebas:** Agregar pruebas primero, luego refactorizar.
- **Código estable y funcional:** Dejarlo como está (no arreglar lo que no está roto).

## Modos de fallo y trampas
- **Refactorizar sin pruebas:** Alto riesgo de romper comportamiento; sin red de seguridad.
- **Refactorización big-bang:** Cambios grandes y riesgosos; difícil de revisar y depurar.
- **Abstracción prematura:** Refactorizar antes de entender el problema (violación de YAGNI).
- **Refactorizar con plazo:** Crea presión para tomar atajos u omitir pruebas.
- **Gold-plating:** Refactorizar más allá de lo necesario (perfeccionismo).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Complejidad ciclomática (señalar funciones >10).
  - Longitud de función (señalar >50 líneas).
  - Rotación de código (archivos cambiados frecuentemente pueden necesitar refactorización).
- **Logs:** N/A para la refactorización en sí.
- **Trazas:** N/A para la refactorización en sí.
- **Alertas:** N/A para la refactorización en sí.

## Notas de implementación
- **Lista de verificación**
  - [ ] Asegurar que las pruebas existan y pasen (si no, escribir pruebas primero).
  - [ ] Identificar code smell (función larga, duplicación, anidamiento profundo, nombres poco claros).
  - [ ] Elegir una técnica de refactorización (extraer función, renombrar, simplificar condicionales, etc.).
  - [ ] Hacer un cambio pequeño a la vez.
  - [ ] Ejecutar pruebas después de cada cambio (verificar que el comportamiento no cambió).
  - [ ] Hacer commits incrementalmente (commits pequeños, cada uno con pruebas pasando).
  - [ ] Parar cuando el código esté "suficientemente bueno" (no sobre-refactorizar).

- **Notas de seguridad / cumplimiento**
  - La refactorización no cambia el comportamiento, por lo que la seguridad no debería verse afectada.
  - Usar pruebas para verificar que las restricciones de seguridad se preserven.

- **Notas de rendimiento**
  - La refactorización a menudo mejora el rendimiento (código más simple es más fácil de optimizar).
  - Si el rendimiento regresa, perfilar y ajustar (pero probar primero).

- **Notas operacionales**
  - Refactorizar en PRs pequeños (más fácil de revisar, menos riesgoso).
  - Asignar 10-20% de la capacidad del sprint a deuda técnica/refactorización.

## Técnicas comunes de refactorización

### Extraer función
**Smell:** Función larga haciendo múltiples cosas.  
**Técnica:** Extraer bloque cohesivo en una nueva función con nombre descriptivo.  
**Ejemplo:**
```go
// Antes
func ProcessOrder(order Order) error {
    // 50 líneas de validación
    // 50 líneas de procesamiento de pago
    // 50 líneas de notificación
}

// Después
func ProcessOrder(order Order) error {
    if err := validateOrder(order); err != nil {
        return err
    }
    if err := processPayment(order); err != nil {
        return err
    }
    return sendConfirmation(order)
}
```

### Renombrar variable/función
**Smell:** Nombres poco claros (`x`, `tmp`, `proc()`).  
**Técnica:** Renombrar a nombre descriptivo.  
**Ejemplo:** `u` → `user`, `calc()` → `calculateDiscount()`.

### Eliminar duplicación (DRY)
**Smell:** Misma lógica repetida en múltiples lugares.  
**Técnica:** Extraer lógica compartida en una función.  
**Ejemplo:**
```go
// Antes
if user.Email == "" || !strings.Contains(user.Email, "@") {
    return errors.New("invalid email")
}
// ... 10 líneas después
if admin.Email == "" || !strings.Contains(admin.Email, "@") {
    return errors.New("invalid email")
}

// Después
func validateEmail(email string) error {
    if email == "" || !strings.Contains(email, "@") {
        return errors.New("invalid email")
    }
    return nil
}
```

### Simplificar condicionales
**Smell:** Anidamiento profundo, cadenas complejas de if-else.  
**Técnica:** Usar retornos tempranos, cláusulas de guarda, o extraer a funciones.  
**Ejemplo:**
```go
// Antes
func Charge(user User, amount int) error {
    if user.ID != "" {
        if user.Status == "active" {
            if user.Balance >= amount {
                // lógica de cobro
                return nil
            } else {
                return ErrInsufficientBalance
            }
        } else {
            return ErrInactive
        }
    } else {
        return ErrInvalidUser
    }
}

// Después
func Charge(user User, amount int) error {
    if user.ID == "" {
        return ErrInvalidUser
    }
    if user.Status != "active" {
        return ErrInactive
    }
    if user.Balance < amount {
        return ErrInsufficientBalance
    }
    // lógica de cobro
    return nil
}
```

### Función inline
**Smell:** Función que solo se llama una vez y no agrega claridad.  
**Técnica:** Poner el cuerpo de la función en el llamador.  
**Usar con moderación:** Solo cuando la función no mejora la legibilidad.

### Reemplazar números mágicos con constantes
**Smell:** Números codificados directamente (`if status == 3`).  
**Técnica:** Definir constantes o enums.  
**Ejemplo:**
```go
// Antes
if user.Status == 3 {
    // ...
}

// Después
const (
    StatusInactive = 1
    StatusPending  = 2
    StatusActive   = 3
)
if user.Status == StatusActive {
    // ...
}
```

## Anti-patrones comunes
- **Anti-patrón:** Refactorizar sin pruebas.
  - **Por qué es malo:** Sin red de seguridad; alto riesgo de romper comportamiento.
  - **Mejor enfoque:** Escribir pruebas primero (o al menos, pruebas de caracterización).

- **Anti-patrón:** Refactorización big-bang (reescribir módulo completo).
  - **Por qué es malo:** Riesgoso, difícil de revisar, bloquea otro trabajo.
  - **Mejor enfoque:** Refactorización incremental (cambios pequeños, commits frecuentes).

- **Anti-patrón:** Refactorizar por el gusto de refactorizar.
  - **Por qué es malo:** Desperdicia tiempo; rotación de código sin beneficio.
  - **Mejor enfoque:** Refactorizar al agregar funcionalidades o corregir errores (oportunístico).

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
La refactorización es mejorar la estructura del código sin cambiar el comportamiento. Es como ordenar un espacio de trabajo—limpiezas pequeñas y frecuentes mantienen las cosas utilizables. Las técnicas comunes incluyen extraer funciones largas en más pequeñas, renombrar variables poco claras, eliminar duplicación y simplificar condicionales anidados. La clave es refactorizar en pasos pequeños con pruebas como red de seguridad. Cada cambio debe ser lo suficientemente pequeño para verificar rápidamente. La refactorización previene que la deuda técnica se acumule y mantiene el código mantenible. El trade-off es tiempo—la refactorización toma tiempo inicial pero se paga en entrega más rápida a largo plazo.

### Preguntas trampa (con respuestas)
1) **P:** ¿Cuándo deberías refactorizar?
   - **R:** Refactorizar oportunísticamente (al tocar código para funcionalidades), cuando el código es difícil de entender o cambiar, o cuando la deuda técnica está ralentizando la entrega. No refactorizar código que funciona y rara vez cambia. Siempre asegurar que existan pruebas antes de refactorizar.

2) **P:** ¿Cómo sabes cuándo parar de refactorizar?
   - **R:** Parar cuando el código esté "suficientemente bueno"—legible, testeable y fácil de cambiar. No perseguir la perfección (rendimientos decrecientes). Usar heurísticas: funciones <50 líneas, complejidad <10, sin duplicación obvia. Si agregar una funcionalidad es fácil, ya terminaste.

3) **P:** ¿Qué pasa si no tienes pruebas?
   - **R:** Escribir pruebas de caracterización primero (pruebas que capturan el comportamiento actual, incluso si tiene errores). Luego refactorizar. Refactorizar sin pruebas es riesgoso—no tienes forma de verificar que no rompiste nada.

4) **P:** ¿La refactorización no es solo tiempo perdido? ¿Por qué no reescribir?
   - **R:** Las reescrituras son riesgosas: toman más tiempo del esperado, a menudo introducen nuevos errores, y pueden no entregar valor. La refactorización es incremental y segura (con pruebas). Refactorizar cuando el costo se justifica; reescribir solo cuando el sistema está fundamentalmente roto.

5) **P:** ¿Cómo refactorizas código legacy sin pruebas?
   - **R:** Agregar pruebas incrementalmente alrededor del área que necesitas cambiar (pruebas de caracterización). Refactorizar piezas pequeñas a la vez. Usar técnicas como "sprout method" (agregar nueva función probada; llamar desde código legacy) para mejorar gradualmente. Es lento pero más seguro que reescrituras grandes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo nombrar al menos 5 técnicas de refactorización (extraer función, renombrar, eliminar duplicación, simplificar condicionales, etc.).
- [ ] Puedo explicar por qué refactorizar sin pruebas es riesgoso.
- [ ] Puedo describir el ciclo red-green-refactor (TDD).
- [ ] Puedo identificar un code smell (función larga, anidamiento profundo, duplicación) y corregirlo.
- [ ] Puedo articular cuándo refactorizar y cuándo dejar el código como está.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Código limpio](clean-code.md)
- [Fundamentos de pruebas](testing-fundamentals.md)

### Temas relacionados
- [Calidad de código](code-quality.md)
- [Principios SOLID](solid-principles.md)
- [Patrones de diseño](design-patterns.md)

### Comparar con
- [Código limpio](clean-code.md) — El código limpio es el objetivo; la refactorización es el proceso para llegar ahí.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: refactorizar handlers de Heimdall, simplificar lógica de Opportunity Actions.
- Considerar agregar sección sobre el patrón "Strangler Fig" para refactorizar sistemas grandes.
- Enlazar al libro "Refactoring" de Martin Fowler (enlace externo en versiones futuras).
