---
id: testing-fundamentals
title: "Fundamentos de Pruebas"
type: concept
status: learning
importance: 90
difficulty: easy
tags: [testing, quality, automation, verification]
canonical: true
aliases: [automated-testing, test-basics]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Fundamentos de Pruebas

## TL;DR (BLUF)
- Las pruebas validan que el software se comporta correctamente bajo diversas condiciones, detectando errores antes de producción.
- Usa pruebas automatizadas para retroalimentación rápida y pruebas manuales para escenarios exploratorios.
- Trade-off clave: tiempo invertido escribiendo pruebas vs. riesgo de entregar defectos.

## Definición
**Qué es:** La práctica de ejecutar código bajo condiciones controladas para verificar que se comporta según lo esperado e identificar defectos antes de que lleguen a los usuarios.  
**Términos clave:** caso de prueba, aserción, fixture de prueba, doble de prueba (mock/stub/fake), código bajo prueba (CUT), sistema bajo prueba (SUT), pruebas de regresión, camino feliz, caso extremo.

## Por qué importa
- **Previene defectos:** Detecta errores antes de que lleguen a producción (10-100x más barato que corregir en producción).
- **Permite refactorización:** Las pruebas actúan como red de seguridad, permitiendo cambios de código con confianza.
- **Documenta comportamiento:** Las pruebas sirven como especificaciones ejecutables de cómo debería funcionar el código.
- **Retroalimentación más rápida:** Las pruebas automatizadas se ejecutan en segundos/minutos vs. horas/días para QA manual.
- **Relevancia en entrevistas:** La estrategia de pruebas es fundamental para discusiones de ingeniería senior.

## Alcance y no-objetivos
**Dentro del alcance:**
- Tipos de pruebas (unitarias, integración, E2E, rendimiento, etc.).
- Anatomía de una prueba (arrange, act, assert).
- Dobles de prueba (mocks, stubs, fakes).
- Cuándo probar y qué probar.

**Fuera del alcance / NO resuelto por esto:**
- Frameworks de pruebas específicos (pytest, Jest, JUnit—esas son herramientas).
- Procesos de aseguramiento de calidad (más amplios que solo pruebas).

## Modelo mental / Intuición
Piensa en las pruebas como **medicina preventiva**:
- **Pruebas unitarias:** Vitaminas diarias (baratas, frecuentes, preventivas).
- **Pruebas de integración:** Chequeos regulares (detectan problemas entre sistemas).
- **Pruebas E2E:** Diagnósticos mayores (costosos, completos, menos frecuentes).
- **Pruebas manuales:** Consulta especializada (exploratoria, creativa, no rutinaria).

No saltas las vitaminas porque tienes chequeos anuales. Cada capa tiene su propósito.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- El código se mantendrá a largo plazo (sistemas de producción).
- Múltiples ingenieros trabajan en la base de código (las pruebas previenen regresiones).
- La refactorización es necesaria (las pruebas permiten cambios seguros).
- Los errores son costosos (impacto al usuario, pérdida de ingresos, corrupción de datos).

### Evítalo cuando
- Escribes código desechable (prototipos, scripts de un solo uso).
- El costo de las pruebas excede el valor del código (proyecto de hackathon de fin de semana).
- Aprendes una nueva tecnología (experimenta primero, prueba después).

## Cómo lo usaría (práctico)
- **Contexto:** Construyendo un endpoint de API en Go para registro de usuarios.
- **Pasos:**
  1. **Prueba unitaria:** Validar lógica de negocio en aislamiento.
     - Prueba: formatos de email válidos aceptados, inválidos rechazados.
     - Prueba: el hash de contraseña funciona correctamente.
     - Prueba: email duplicado retorna error apropiado.
  2. **Prueba de integración:** Probar con base de datos real.
     - Prueba: registro de usuario insertado correctamente.
     - Prueba: la transacción hace rollback en error.
  3. **Prueba E2E:** Probar flujo completo vía [HTTP](../operations/http.md).
     - Prueba: POST /register → respuesta 201 → el usuario puede iniciar sesión.
  4. **Casos extremos:** Entradas vacías, intentos de inyección SQL, registros concurrentes.
- **Cómo se ve el éxito:** Todas las pruebas pasan en <10 segundos; producción tiene cero errores de registro en el primer mes.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Detecta errores temprano (más barato de corregir).
  - Permite refactorización con confianza.
  - Documenta comportamiento esperado.
  - Más rápido que pruebas manuales.
- **Desventajas / Riesgos:**
  - Costo de tiempo inicial (escribir pruebas).
  - Carga de mantenimiento (las pruebas necesitan actualizaciones cuando el código cambia).
  - Falsa confianza (pruebas malas pasan pero el código está roto).

### Alternativas
- **Solo pruebas manuales:** Lento, costoso, pierde casos extremos, no repetible.
- **Sin pruebas ("esperar que funcione"):** Rápido inicialmente pero alta tasa de defectos en producción.
- **Probar en producción:** Algunos escenarios lo requieren (observabilidad, despliegues canario), pero no debería ser la estrategia principal.

### Cómo elegir
- **Sistemas de producción:** Pruebas automatizadas (unitarias + integración + E2E críticas).
- **Prototipos:** Pruebas mínimas o sin pruebas (validar concepto primero).
- **Sistemas críticos:** Pruebas extensivas (financiero, salud, seguridad crítica).

## Modos de fallo y trampas
- **Probar lo incorrecto:** Probar detalles de implementación en lugar de comportamiento (las pruebas se rompen al refactorizar).
- **Pruebas inestables:** Pruebas que fallan aleatoriamente por timing, condiciones de carrera, problemas del entorno (erosiona la confianza).
- **Sin aserciones:** Pruebas que ejecutan código pero no verifican resultados ("pruebas de humo" que no detectan errores).
- **Demasiados mocks:** Sobre-simular oculta errores de integración (el código pasa pruebas pero falla en producción).
- **Ignorar casos extremos:** Probar solo caminos felices (los errores se esconden en condiciones de error, valores límite).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tasa de aprobación de pruebas (debería ser >99%).
  - Tiempo de ejecución de pruebas (unitarias <10s, integración <5min).
  - Tasa de inestabilidad de pruebas (% de fallos aleatorios; apuntar a <1%).
  - Cobertura de código (% de código ejecutado por pruebas).
- **Logs:** Rastrear fallos de pruebas por categoría (unitarias vs integración vs E2E).
- **Trazas:** N/A para las pruebas en sí.
- **Alertas:** Pico en duración de suite de pruebas, aumento de inestabilidad, caída de cobertura.

## Notas de implementación
- **Lista de verificación**
  - [ ] Escribir pruebas mientras codificas (TDD o probar poco después).
  - [ ] Seguir patrón AAA: Arrange (preparar), Act (ejecutar), Assert (verificar).
  - [ ] Probar comportamiento, no detalles de implementación.
  - [ ] Usar nombres descriptivos de pruebas (qué se está probando, resultado esperado).
  - [ ] Probar camino feliz + casos extremos + condiciones de error.
  - [ ] Mantener pruebas rápidas y deterministas (sin sleeps, sin datos aleatorios).
  - [ ] Ejecutar pruebas en CI (bloquear merge en fallos).

- **Notas de seguridad / cumplimiento**
  - Incluir pruebas de seguridad: validación de entrada, inyección SQL, prevención de XSS.
  - Probar autenticación/autorización (acceso no autorizado bloqueado, permisos aplicados).

- **Notas de rendimiento**
  - Paralelizar ejecución de pruebas donde sea posible.
  - Usar dobles de prueba para evitar dependencias lentas (BD, red).

- **Notas operacionales**
  - Corregir o eliminar pruebas inestables inmediatamente (erosionan la confianza).
  - Monitorear rendimiento de la suite de pruebas (pruebas lentas matan la productividad).

## Mini ejemplo
```go
// Example: Testing user registration logic
package user

import (
    "testing"
    "github.com/stretchr/testify/assert"
)

// Unit test: validate email format
func TestValidateEmail(t *testing.T) {
    tests := []struct {
        name    string
        email   string
        wantErr bool
    }{
        {"valid email", "user@example.com", false},
        {"missing @", "userexample.com", true},
        {"missing domain", "user@", true},
        {"empty", "", true},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := ValidateEmail(tt.email)
            if tt.wantErr {
                assert.Error(t, err)
            } else {
                assert.NoError(t, err)
            }
        })
    }
}

// Integration test: database interaction
func TestCreateUser_Integration(t *testing.T) {
    // Arrange: setup test database
    db := setupTestDB(t)
    defer db.Close()
    
    user := User{
        Email:    "test@example.com",
        Password: "hashed_password",
    }
    
    // Act: create user
    err := CreateUser(db, user)
    
    // Assert: verify success
    assert.NoError(t, err)
    
    // Verify user exists in DB
    var count int
    db.QueryRow("SELECT COUNT(*) FROM users WHERE email = $1", user.Email).Scan(&count)
    assert.Equal(t, 1, count)
}

// Test edge case: duplicate email
func TestCreateUser_DuplicateEmail(t *testing.T) {
    db := setupTestDB(t)
    defer db.Close()
    
    user := User{Email: "duplicate@example.com", Password: "pass"}
    
    // Create user first time
    err := CreateUser(db, user)
    assert.NoError(t, err)
    
    // Attempt duplicate
    err = CreateUser(db, user)
    assert.Error(t, err)
    assert.Equal(t, ErrDuplicateEmail, err)
}
```

## Anti-patrones comunes
- **Anti-patrón:** Sin pruebas ("lo probaremos manualmente").
  - **Por qué es malo:** Las pruebas manuales son lentas, inconsistentes y pierden casos extremos. No escala.
  - **Mejor enfoque:** Automatizar pruebas para validación rápida y repetible.

- **Anti-patrón:** Probar detalles de implementación (ej., "la función X llama a la función Y").
  - **Por qué es malo:** Las pruebas se rompen al refactorizar incluso cuando el comportamiento es correcto.
  - **Mejor enfoque:** Probar comportamiento de la API pública (entradas → salidas), no llamadas internas.

- **Anti-patrón:** Una prueba gigante que prueba todo.
  - **Por qué es malo:** Difícil de depurar (¿qué aserción falló?), difícil de mantener, lento.
  - **Mejor enfoque:** Muchas pruebas pequeñas y enfocadas (un comportamiento por prueba).

- **Anti-patrón:** Pruebas sin aserciones.
  - **Por qué es malo:** El código se ejecuta pero no verifica corrección; los errores se filtran.
  - **Mejor enfoque:** Siempre verificar resultados esperados (valores de retorno, cambios de estado, efectos secundarios).

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
Las pruebas son cómo verificamos que el software funciona correctamente antes de que llegue a los usuarios. Escribimos código que ejercita nuestro código de producción y verifica que los resultados coincidan con las expectativas. Hay diferentes niveles: las pruebas unitarias verifican funciones individuales en aislamiento, las pruebas de integración verifican que los componentes funcionen juntos, y las pruebas E2E validan flujos completos de usuario. Las pruebas detectan errores temprano (cuando son baratos de corregir), permiten refactorización segura (las pruebas actúan como red de seguridad), y documentan comportamiento esperado. La clave es equilibrar cobertura de pruebas con velocidad—escribir suficientes pruebas para detectar errores críticos, pero mantener la suite rápida para obtener retroalimentación rápida.

### Preguntas trampa (con respuestas)
1) **P:** ¿Por qué no solo probar en producción? Los usuarios nos dirán si algo se rompe.
   - **R:** Probar en producción significa que los usuarios experimentan errores, lo que daña la confianza y puede causar pérdida de datos o impacto en ingresos. Corregir errores en producción es 10-100x más costoso que detectarlos en desarrollo. Usa pruebas para prevenir defectos, no para descubrirlos en producción.

2) **P:** Si tenemos 100% de cobertura de código, ¿estamos libres de errores?
   - **R:** No. La cobertura mide qué código se ejecuta, no qué se valida. Puedes tener 100% de cobertura con pruebas que no verifican nada. Además, la cobertura no prueba casos extremos, condiciones de carrera o problemas de integración. La cobertura es una señal de lo que no está probado, no una garantía de calidad.

3) **P:** ¿Cada línea de código debería tener una prueba?
   - **R:** No. Usa pruebas basadas en riesgo. Enfócate en lógica de negocio, manejo de errores y casos extremos. Getters/setters simples o código trivial puede no necesitar pruebas. Apunta a alta cobertura en rutas críticas, no 100% de cobertura en todas partes.

4) **P:** ¿Cómo pruebas código que llama APIs externas?
   - **R:** Usa dobles de prueba. En pruebas unitarias, simula la API para aislar tu lógica. En pruebas de integración, usa un servidor API falso o entorno de prueba. En pruebas E2E, usa la API real (o una versión de staging). Cada capa valida diferentes preocupaciones.

5) **P:** ¿Cuál es la diferencia entre mocks, stubs y fakes?
   - **R:** 
     - **Stub:** Retorna valores codificados (el más simple, para consultas).
     - **Mock:** Registra llamadas y verifica expectativas (para verificación de comportamiento).
     - **Fake:** Implementación funcional con atajos (ej., BD en memoria en lugar de BD real).
     - Usa stubs para la mayoría de casos; mocks cuando necesitas verificar interacciones; fakes para dependencias complejas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar el propósito de las pruebas (prevenir defectos, permitir refactorización, documentar comportamiento).
- [ ] Puedo describir el patrón AAA (Arrange, Act, Assert).
- [ ] Puedo diferenciar entre pruebas unitarias, de integración y E2E.
- [ ] Puedo explicar por qué probar detalles de implementación es un anti-patrón.
- [ ] Puedo nombrar al menos 3 tipos de dobles de prueba y cuándo usar cada uno.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Aseguramiento de Calidad de Software](software-quality-assurance.md)

### Temas relacionados
- [Pirámide de pruebas](testing-pyramid.md)
- [Calidad de código](code-quality.md)
- [Compuertas de calidad](quality-gates.md)
- [Código limpio](clean-code.md)

### Comparar con
- [Pirámide de pruebas](testing-pyramid.md) — Los fundamentos de pruebas explican qué son las pruebas; la pirámide explica cómo equilibrar tipos de pruebas.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: probar lógica de Step Functions de Heimdall, llamadas a APIs de terceros de Opportunity Actions.
- Considerar agregar sección sobre TDD (Desarrollo Dirigido por Pruebas) como flujo de trabajo.
- Enlazar a frameworks de pruebas específicos (Go: testing, testify; Python: pytest) cuando esos temas sean creados.
