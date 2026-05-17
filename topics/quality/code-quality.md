---
id: code-quality
title: "Calidad de Código"
type: concept
status: learning
importance: 85
difficulty: medium
tags: [quality, code-review, static-analysis, maintainability]
canonical: true
aliases: [code-quality-metrics]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Calidad de Código

## TL;DR (BLUF)
- La calidad de código mide qué tan legible, mantenible y correcto es el código, usando métricas como complejidad, cobertura de pruebas y hallazgos de análisis estático.
- Usa herramientas automatizadas (linters, revisión de código) combinadas con revisión manual para mantener la calidad.
- Trade-off clave: tiempo invertido en mejoras de calidad vs. entregar funcionalidades.

## Definición
**Qué es:** Una evaluación multidimensional del código fuente basada en corrección, legibilidad, mantenibilidad, testabilidad y adherencia a estándares.  
**Términos clave:** complejidad ciclomática, cobertura de código, análisis estático, linter, revisión de código, deuda técnica, principios SOLID, code smell.

## Por qué importa
- **Mantenibilidad:** El código de alta calidad es más fácil de cambiar, refactorizar y depurar.
- **Velocidad:** Los equipos avanzan más rápido en bases de código limpias; el código de baja calidad crea fricción (corrección de errores, confusión, miedo a romper cosas).
- **Onboarding:** Los nuevos ingenieros se adaptan más rápido cuando el código es legible y bien estructurado.
- **Relevancia en entrevistas:** Los ingenieros senior deben articular trade-offs entre calidad de código y velocidad, y defender decisiones de diseño.

## Alcance y no-objetivos
**Dentro del alcance:**
- Señales de calidad automatizadas: linters, métricas de complejidad, cobertura de pruebas.
- Señales de calidad humanas: revisiones de código, legibilidad, nomenclatura.
- Prevenir defectos comunes a través de análisis estático.

**Fuera del alcance / NO resuelto por esto:**
- Calidad del producto (que las funcionalidades funcionen según lo previsto; requiere pruebas).
- Rendimiento (preocupación separada, aunque el código limpio a menudo rinde mejor).

## Modelo mental / Intuición
Piensa en la calidad de código como **fricción en una máquina**:
- Código de alta calidad: engranajes suaves, fácil de cambiar, comportamiento predecible.
- Código de baja calidad: engranajes oxidados, alta fricción, efectos secundarios impredecibles.

Los problemas pequeños de calidad se acumulan con el tiempo. Una sola función de 200 líneas es molesta. Una base de código con 500 funciones así es inmantenible.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El código se mantendrá a largo plazo (sistemas de producción, bibliotecas compartidas).
- Múltiples ingenieros trabajarán en la base de código (la calidad permite la colaboración).
- Necesitas incorporar nuevos ingenieros rápidamente.

### Evítalo cuando
- Escribes código desechable (prototipos, scripts de un solo uso).
- La inversión en calidad excede el valor del código (proyecto de hackathon de fin de semana).

## Cómo lo usaría (práctico)
- **Contexto:** Manteniendo un servicio API en Go en un equipo de 5 ingenieros.
- **Pasos:**
  1. **Automatizar linting:** Ejecutar `golangci-lint` en CI (detecta variables no usadas, brechas en manejo de errores, violaciones de estilo).
  2. **Establecer límites de complejidad:** Fallar CI si la complejidad ciclomática >10 para cualquier función (fuerza a descomponer lógica compleja).
  3. **Requerir cobertura de pruebas:** Fallar CI si la cobertura cae por debajo del 80% (asegura que el código nuevo esté probado).
  4. **Revisiones de código:** Requerir 1 aprobación antes de merge; usar lista de verificación (manejo de errores, nomenclatura, pruebas, seguridad).
  5. **Refactorizar regularmente:** Dedicar 10-20% de la capacidad del sprint a deuda técnica (simplificar funciones complejas, eliminar duplicación).
- **Cómo se ve el éxito:** Las revisiones de PR toman <30 minutos; cero errores en producción por manejo de errores omitido; nuevos ingenieros productivos en <2 semanas.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Reduce errores (el análisis estático detecta errores que los humanos pasan por alto).
  - Desarrollo más rápido con el tiempo (fácil cambiar código limpio).
  - Menor carga cognitiva (código legible = menos esfuerzo mental).
- **Desventajas / Riesgos:**
  - Costo de tiempo inicial (refactorización, escribir pruebas, revisiones de código).
  - Puede ralentizar la entrega inicial si se aplica en exceso (código perfecto para prototipos desechables).
  - Requiere disciplina y herramientas (configuración de CI, aceptación del equipo).

### Alternativas
- **"Entregar rápido, limpiar después":** Funciona para MVPs pero crea deuda técnica que rara vez se paga.
- **Solo revisión manual de código:** Pierde problemas que las herramientas detectan automáticamente (variables no usadas, riesgos de inyección SQL).
- **Sin estándares de calidad:** Rápido inicialmente pero la velocidad cae conforme la base de código crece (alta tasa de defectos, difícil de cambiar).

### Cómo elegir
- **Sistema de producción de larga vida:** Invertir en calidad de código completa (linters, revisiones, pruebas, límites de complejidad).
- **Prototipo / MVP:** Calidad ligera (linting básico, sin mandatos de cobertura).
- **Biblioteca compartida:** Escrutinio extra (diseño de API, documentación, compatibilidad hacia atrás).

## Modos de fallo y trampas
- **Métricas vanidosas:** Alta cobertura de pruebas pero las pruebas no verifican comportamiento (solo llaman código sin validación).
- **Reglas demasiado estrictas:** Bloquear PRs por problemas menores de estilo mata velocidad y moral.
- **Sin presupuesto de refactorización:** La calidad se degrada con el tiempo; la deuda técnica se acumula.
- **"Revisión de código como guardián":** Los revisores critican estilo en lugar de enfocarse en corrección y diseño.
- **Ignorar complejidad:** Funciones de 500 líneas que nadie entiende; imposible de probar o depurar.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Complejidad ciclomática (por función; señalar >10).
  - Cobertura de pruebas (% de código ejecutado por pruebas).
  - Violaciones del linter (deberían ser cero en la rama main).
  - Tiempo de revisión de PR (revisiones largas sugieren código poco claro).
  - Tasa de defectos (errores por 1000 líneas de código).
- **Logs:** Rastrear violaciones del linter a lo largo del tiempo (deberían disminuir, no aumentar).
- **Trazas:** N/A para calidad de código en sí.
- **Alertas:** Pico de complejidad en código nuevo, caída de cobertura por debajo del umbral.

## Notas de implementación
- **Lista de verificación**
  - [ ] Configurar linter en CI (golangci-lint, ESLint, pylint, etc.).
  - [ ] Definir límites de complejidad (complejidad ciclomática <10, longitud de función <50 líneas).
  - [ ] Requerir cobertura de pruebas (>80% para código nuevo, rastrear tendencia).
  - [ ] Establecer lista de verificación de revisión de código (manejo de errores, pruebas, nomenclatura, seguridad).
  - [ ] Bloquear merge en fallos del linter o fallos de pruebas.
  - [ ] Dedicar tiempo para refactorización (10-20% de la capacidad del sprint).

- **Notas de seguridad / cumplimiento**
  - Incluir linters de seguridad (gosec, Bandit, semgrep) en CI.
  - Señalar vulnerabilidades comunes (inyección SQL, XSS, secretos en código).

- **Notas de rendimiento**
  - Usar profilers para identificar rutas críticas (no optimizar prematuramente).
  - La complejidad a menudo se correlaciona con mal rendimiento (código más simple es más fácil de optimizar).

- **Notas operacionales**
  - La calidad no termina en el despliegue: monitorear errores de producción y rastrearlos hasta problemas de código.
  - Usar post-mortems para identificar brechas de calidad de código (ej., manejo de errores faltante, lógica poco clara).

## Mini ejemplo
```go
// MAL: Alta complejidad, difícil de probar, lógica poco clara
func ProcessOrder(order Order) error {
    if order.Total > 0 {
        if order.UserID != "" {
            user, err := getUser(order.UserID)
            if err != nil {
                return err
            }
            if user.Status == "active" {
                if user.Balance >= order.Total {
                    // ... 50 más líneas de lógica anidada
                    return nil
                } else {
                    return errors.New("insufficient balance")
                }
            } else {
                return errors.New("user not active")
            }
        } else {
            return errors.New("missing user ID")
        }
    } else {
        return errors.New("invalid order total")
    }
}

// BIEN: Baja complejidad, testeable, lógica clara
func ProcessOrder(order Order) error {
    if err := validateOrder(order); err != nil {
        return fmt.Errorf("invalid order: %w", err)
    }
    
    user, err := getActiveUser(order.UserID)
    if err != nil {
        return fmt.Errorf("user lookup failed: %w", err)
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

func getActiveUser(userID string) (*User, error) {
    user, err := getUser(userID)
    if err != nil {
        return nil, err
    }
    if user.Status != "active" {
        return nil, ErrUserNotActive
    }
    return user, nil
}
```

## Anti-patrones comunes
- **Anti-patrón:** Objetos dios / Funciones dios (una sola función/clase haciendo todo).
  - **Por qué es malo:** Imposible de probar, depurar o razonar; viola el principio de responsabilidad única.
  - **Mejor enfoque:** Dividir en funciones/clases más pequeñas y enfocadas con responsabilidades claras.

- **Anti-patrón:** Números mágicos y nombres poco claros (`if status == 3`).
  - **Por qué es malo:** Los lectores no saben qué significa `3`; requiere buscar en el código o documentación.
  - **Mejor enfoque:** Usar constantes o enums (`if status == StatusActive`).

- **Anti-patrón:** Código copiado y pegado (duplicación en múltiples archivos).
  - **Por qué es malo:** Los errores deben corregirse en múltiples lugares; alta carga de mantenimiento.
  - **Mejor enfoque:** Extraer lógica compartida en funciones o bibliotecas (DRY: Don't Repeat Yourself).

- **Anti-patrón:** Sin manejo de errores ("esperar que funcione").
  - **Por qué es malo:** Fallos silenciosos llevan a incidentes en producción.
  - **Mejor enfoque:** Verificar cada error, registrar contexto, retornar o manejar apropiadamente.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
La calidad de código se trata de hacer el código fácil de leer, mantener y cambiar. La medimos con herramientas automatizadas como linters (detectan problemas de estilo y corrección), métricas de complejidad (señalan funciones demasiado complejas) y cobertura de pruebas (aseguran que el código está probado). Pero la calidad no es solo métricas—también se trata de nomenclatura clara, buena estructura y diseño reflexivo. El objetivo es equilibrar calidad con velocidad: invertir lo suficiente para mantener la base de código mantenible sin sobre-ingenierizar. El código de alta calidad se paga con el tiempo porque los equipos avanzan más rápido y encuentran menos errores.

### Preguntas trampa (con respuestas)
1) **P:** ¿Enfocarse en calidad de código no ralentiza la entrega?
   - **R:** Inicialmente sí, pero acelera la entrega a largo plazo. El código de baja calidad crea deuda técnica que requiere apagar incendios constantemente, cambios riesgosos y depuración lenta. El código limpio es fácil de cambiar, así que los equipos avanzan más rápido después de la inversión inicial.

2) **P:** Si tenemos 90% de cobertura de pruebas, ¿nuestro código es de alta calidad?
   - **R:** No necesariamente. La cobertura mide qué se ejecuta, no qué se valida. Puedes tener 90% de cobertura con pruebas que no verifican nada. La calidad también incluye legibilidad, complejidad y diseño—no solo pruebas.

3) **P:** ¿Cuál es la métrica de calidad de código más importante?
   - **R:** No hay una sola métrica. Usa una combinación: complejidad ciclomática (simplicidad), cobertura de pruebas (confianza), violaciones del linter (corrección) y retroalimentación de revisión de código (diseño). Las métricas son indicadores, no objetivos.

4) **P:** ¿Cómo equilibras refactorización con entrega de funcionalidades?
   - **R:** Dedicar 10-20% de la capacidad del sprint a deuda técnica. Refactorizar oportunísticamente (al tocar código para funcionalidades) y estratégicamente (dedicar sprints a áreas de alto impacto). Usar métricas (complejidad, tasa de defectos) para priorizar.

5) **P:** ¿Todo el código debería seguir los mismos estándares de calidad?
   - **R:** No, usa estándares basados en riesgo. Las APIs de producción necesitan alta calidad (linters, pruebas, revisiones). Los prototipos desechables no. Las bibliotecas compartidas necesitan escrutinio extra (diseño de API, documentación, compatibilidad hacia atrás).

### Auto-verificación rápida (5 ítems)
- [ ] Puedo nombrar al menos 3 métricas de calidad de código y su propósito.
- [ ] Puedo explicar por qué la complejidad ciclomática importa y qué mide.
- [ ] Puedo describir cómo equilibrar calidad de código con velocidad de entrega.
- [ ] Puedo dar un ejemplo de un code smell y cómo corregirlo.
- [ ] Puedo articular la diferencia entre cobertura de pruebas y calidad de pruebas.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Aseguramiento de Calidad de Software](software-quality-assurance.md)
- [Principios SOLID](solid-principles.md)
- [Patrones de diseño](design-patterns.md)

### Temas relacionados
- [Pirámide de pruebas](testing-pyramid.md)
- [Compuertas de calidad](quality-gates.md)
- [Código limpio](clean-code.md)

### Comparar con
- [Pirámide de pruebas](testing-pyramid.md) — Las pruebas validan comportamiento; la calidad de código mide estructura y mantenibilidad.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: simplificar funciones complejas en Heimdall, refactorizar Opportunity Actions para testabilidad.
- Considerar agregar sección sobre mejores prácticas de revisión de código (en qué enfocarse, cómo dar retroalimentación).
