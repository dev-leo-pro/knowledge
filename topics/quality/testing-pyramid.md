---
id: testing-pyramid
title: "Pirámide de Pruebas"
type: pattern
status: learning
importance: 85
difficulty: medium
tags: [testing, quality, automation, strategy]
canonical: true
aliases: [test-pyramid]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Pirámide de Pruebas

## TL;DR (BLUF)
- La pirámide de pruebas es una estrategia para equilibrar tipos de pruebas: muchas pruebas unitarias rápidas en la base, menos pruebas de integración en el medio, y pocas pruebas E2E lentas en la cima.
- Úsala para optimizar velocidad, confiabilidad y mantenibilidad de tu suite de pruebas.
- Trade-off clave: cobertura completa vs. ciclos de retroalimentación rápidos.

## Definición
**Qué es:** Una estrategia de pruebas que organiza pruebas automatizadas en capas basadas en alcance y velocidad, con la mayoría siendo pruebas unitarias rápidas y aisladas, y progresivamente menos pruebas en niveles de integración superiores.  
**Términos clave:** prueba unitaria, prueba de integración, prueba de extremo a extremo (E2E), prueba de contrato, aislamiento de pruebas, inestabilidad de pruebas.

## Por qué importa
- **Retroalimentación rápida:** Las pruebas unitarias se ejecutan en milisegundos; detectas errores inmediatamente durante el desarrollo.
- **Pruebas confiables:** Las pruebas unitarias aisladas son deterministas; las pruebas E2E son inestables debido a dependencias del entorno.
- **Rentable:** Escribir y mantener pruebas unitarias es más barato que pruebas E2E (configuración más simple, ejecución más rápida, depuración más fácil).
- **Relevancia en entrevistas:** La estrategia de pruebas es un tema común en discusiones de diseño de sistemas y procesos de ingeniería.

## Alcance y no-objetivos
**Dentro del alcance:**
- Proporción de tipos de pruebas: muchas unitarias, menos integración, E2E críticas.
- Cuándo usar cada tipo de prueba y qué validar.
- Trade-offs entre velocidad, cobertura y confianza.

**Fuera del alcance / NO resuelto por esto:**
- Pruebas manuales o QA exploratoria (complementa, no reemplaza).
- Frameworks o herramientas de pruebas específicas (pytest, Jest, etc.).

## Modelo mental / Intuición
**Forma de pirámide:**
```
       /\       ← Pruebas E2E (pocas, lentas, frágiles)
      /  \
     /____\     ← Pruebas de integración (moderadas, velocidad media)
    /      \
   /________\   ← Pruebas unitarias (muchas, rápidas, confiables)
```

La base es ancha porque las pruebas unitarias son baratas de escribir y ejecutar. La cima es estrecha porque las pruebas E2E son costosas y lentas. Si tu suite de pruebas parece una **pirámide invertida** (mayormente pruebas E2E), tendrás CI lento, pruebas inestables y ciclos largos de depuración.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Construyes sistemas de producción con CI/CD automatizado.
- Trabajas en equipos donde la confiabilidad y velocidad de las pruebas importan.
- Necesitas ciclos de retroalimentación rápidos durante el desarrollo.

### Evítalo cuando
- Prototipar código desechable (las pruebas pueden ser excesivas).
- Probar UIs altamente visuales donde QA manual es más efectivo que E2E automatizado (aunque las pruebas de accesibilidad y regresión visual pueden ayudar).

## Cómo lo usaría (práctico)
- **Contexto:** Construyendo un servicio API en Go con una base de datos PostgreSQL.
- **Pasos:**
  1. **Pruebas unitarias (70-80% de las pruebas):** Probar lógica de negocio en aislamiento (BD simulada, sin red). Ejemplo: validar parsing de entrada, imposición de reglas de negocio, manejo de errores.
  2. **Pruebas de integración (15-25% de las pruebas):** Probar interacciones con BD con una base de datos de prueba real (contenedor Docker en CI). Ejemplo: verificar que las consultas retornan datos correctos, que las transacciones hacen rollback en errores.
  3. **Pruebas de contrato (opcional):** Validar contratos de API entre servicios (Pact o similar). Ejemplo: el consumidor espera el campo `user_id`; el proveedor lo incluye.
  4. **Pruebas E2E (5-10% de las pruebas):** Probar flujos críticos de usuario de extremo a extremo (sistema completo ejecutándose). Ejemplo: registro de usuario → verificación de email → inicio de sesión.
  5. **Pruebas de rendimiento (según necesidad):** Prueba de carga en endpoints críticos bajo tráfico esperado.
- **Cómo se ve el éxito:** Las pruebas unitarias se ejecutan en <10 segundos, las de integración en <2 minutos, las E2E en <10 minutos. CI se completa en <15 minutos en total.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Retroalimentación rápida: las pruebas unitarias detectan la mayoría de errores en segundos.
  - Confiable: menos dependencias significan menos pruebas inestables.
  - Fácil de depurar: los fallos de pruebas unitarias señalan el problema exacto.
- **Desventajas / Riesgos:**
  - Las pruebas unitarias no detectan problemas de integración (ej., contratos de API desajustados, errores en consultas de BD).
  - Simular puede ocultar fallos del mundo real (ej., suposiciones incorrectas sobre comportamiento de APIs de terceros).
  - Las pruebas E2E siguen siendo necesarias para flujos críticos (no puedes probar todo unitariamente).

### Alternativas
- **Pirámide invertida (mayormente pruebas E2E):** CI lento, pruebas inestables, difícil de depurar. Evitar a menos que no haya alternativa.
- **Forma de diamante (pesado en pruebas de integración):** Equilibra velocidad y cobertura pero las pruebas de integración pueden ser más lentas y difíciles de mantener que las unitarias.
- **Forma de trofeo (pesado en pruebas de integración, menos E2E):** Popularizado por Kent C. Dodds para pruebas frontend; prioriza integración sobre unitarias para mayor confianza.

### Cómo elegir
- **Servicios backend:** Pirámide clásica (muchas unitarias, menos integración, E2E críticas).
- **Aplicaciones frontend:** La forma de trofeo puede funcionar mejor (las pruebas de integración con DOM detectan más problemas reales que las pruebas unitarias aisladas de componentes).
- **Microservicios:** Agregar pruebas de contrato para validar límites de servicio.

## Modos de fallo y trampas
- **Demasiadas pruebas E2E:** CI lento, pruebas inestables, ciclos largos de retroalimentación. Ejemplo: 500 pruebas E2E tomando 2 horas en ejecutarse.
- **Muy pocas pruebas de integración:** Las pruebas unitarias pasan pero el sistema falla cuando los componentes interactúan. Ejemplo: consulta funciona en aislamiento pero hace deadlock bajo carga concurrente.
- **Pruebas inestables:** Pruebas que fallan aleatoriamente por condiciones de carrera, timeouts o dependencias del entorno. Esto erosiona la confianza en la suite de pruebas.
- **Simular todo:** Sobre-simular oculta errores de integración. Ejemplo: BD simulada siempre retorna éxito; BD real tiene agotamiento del pool de conexiones.
- **Sin pruebas E2E:** Flujos críticos de usuario se rompen en producción porque nadie probó el sistema completo.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tiempo de ejecución de pruebas (unitarias <10s, integración <5min, E2E <15min).
  - Tasa de inestabilidad de pruebas (% de pruebas que fallan aleatoriamente; apuntar a <1%).
  - Cobertura de código por tipo de prueba (las pruebas unitarias deberían cubrir la mayoría del código).
- **Logs:** Rastrear fallos de pruebas por capa (unitarias vs integración vs E2E) para identificar puntos débiles.
- **Trazas:** N/A para la estrategia de pruebas en sí.
- **Alertas:** Picos en duración de CI (pruebas haciéndose más lentas), aumento en tasa de inestabilidad.

## Notas de implementación
- **Lista de verificación**
  - [ ] Escribir pruebas unitarias primero (TDD o probar poco después).
  - [ ] Usar dobles de prueba (mocks, stubs, fakes) para dependencias externas en pruebas unitarias.
  - [ ] Ejecutar pruebas de integración contra dependencias reales en entornos aislados (Docker, testcontainers).
  - [ ] Limitar pruebas E2E a caminos felices críticos y modos de fallo de alto riesgo.
  - [ ] Hacer pruebas deterministas (evitar datos aleatorios, sleeps, inestabilidad basada en tiempo).
  - [ ] Ejecutar pruebas unitarias en cada commit; pruebas de integración/E2E solo en CI (o menos frecuentemente).

- **Notas de seguridad / cumplimiento**
  - Incluir pruebas de seguridad en cada capa: pruebas unitarias para validación de entrada, pruebas de integración para autenticación/autorización, pruebas E2E para flujos de seguridad de extremo a extremo.

- **Notas de rendimiento**
  - Paralelizar ejecución de pruebas (ejecutar pruebas unitarias en paralelo por defecto).
  - Usar bases de datos en memoria o dobles de prueba rápidos para acelerar pruebas de integración.

- **Notas operacionales**
  - Monitorear inestabilidad de pruebas en CI; corregir o eliminar pruebas inestables inmediatamente.
  - Mantener las suites de pruebas rápidas; pruebas lentas matan la productividad del desarrollador.

## Mini ejemplo
Nota: El ejemplo E2E usa [HTTP](../operations/http.md).
```go
// Unit test (fast, isolated, no DB)
func TestCalculateDiscount(t *testing.T) {
    order := Order{Total: 100, UserTier: "premium"}
    discount := CalculateDiscount(order)
    assert.Equal(t, 20, discount) // 20% for premium users
}

// Integration test (real DB in Docker)
func TestCreateUser_Integration(t *testing.T) {
    db := setupTestDB(t) // Docker container with PostgreSQL
    defer db.Close()
    
    user := User{Email: "test@example.com", Name: "Test"}
    err := CreateUser(db, user)
    assert.NoError(t, err)
    
    // Verify user exists in DB
    var count int
    db.QueryRow("SELECT COUNT(*) FROM users WHERE email = $1", user.Email).Scan(&count)
    assert.Equal(t, 1, count)
}

// E2E test (full system running, HTTP requests)
func TestUserSignupFlow_E2E(t *testing.T) {
    // Assume API server is running on localhost:8080
    resp, err := http.Post("http://localhost:8080/signup", "application/json",
        strings.NewReader(`{"email":"user@example.com","password":"secret"}`))
    assert.NoError(t, err)
    assert.Equal(t, 201, resp.StatusCode)
    
    // Verify user can log in
    loginResp, err := http.Post("http://localhost:8080/login", "application/json",
        strings.NewReader(`{"email":"user@example.com","password":"secret"}`))
    assert.NoError(t, err)
    assert.Equal(t, 200, loginResp.StatusCode)
}
```

## Anti-patrones comunes
- **Anti-patrón:** Cono de helado (mayormente pruebas E2E, pocas pruebas unitarias).
  - **Por qué es malo:** CI lento, pruebas inestables, difícil de depurar, costoso de mantener.
  - **Mejor enfoque:** Invertir a forma de pirámide; reescribir pruebas E2E como pruebas unitarias/integración donde sea posible.

- **Anti-patrón:** Probar detalles de implementación en pruebas unitarias (ej., verificar llamadas a métodos internos).
  - **Por qué es malo:** Las pruebas se rompen al refactorizar incluso cuando el comportamiento es correcto.
  - **Mejor enfoque:** Probar comportamiento de la API pública, no internos.

- **Anti-patrón:** Sin pruebas de integración ("las pruebas unitarias son suficientes").
  - **Por qué es malo:** Pierde errores en consultas de BD, contratos de API, concurrencia.
  - **Mejor enfoque:** Agregar pruebas de integración para rutas críticas (interacciones con BD, APIs de terceros).

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
La pirámide de pruebas es una estrategia para equilibrar tipos de pruebas en velocidad y confiabilidad. La base son pruebas unitarias—pruebas rápidas y aisladas de funciones individuales. El medio son pruebas de integración que verifican que los componentes funcionen juntos (ej., tu código con una base de datos real). La cima son pruebas E2E que validan flujos completos de usuario. Quieres muchas pruebas unitarias porque son rápidas y baratas, menos pruebas de integración porque son más lentas, y muy pocas pruebas E2E porque son lentas e inestables. Si tu suite de pruebas está invertida (mayormente E2E), tendrás CI lento y pruebas poco confiables.

### Preguntas trampa (con respuestas)
1) **P:** ¿Por qué no solo escribir pruebas E2E para todo? Prueban el sistema real.
   - **R:** Las pruebas E2E son lentas (minutos vs. segundos), inestables (muchas dependencias que pueden fallar) y difíciles de depurar (el fallo puede estar en cualquier parte del sistema). Pierdes ciclos de retroalimentación rápidos y productividad del desarrollador. Usa pruebas E2E con moderación solo para flujos críticos.

2) **P:** Si las pruebas unitarias usan mocks, ¿no dan falsa confianza?
   - **R:** Sí, sobre-simular puede ocultar errores de integración. La solución es equilibrio: usa pruebas de integración para validar interacciones reales (BD, APIs), y limita la simulación a dependencias externas que son costosas o difíciles de controlar. Los mocks deben reflejar comportamiento real.

3) **P:** ¿Cuál es la proporción ideal de pruebas unitarias/integración/E2E?
   - **R:** No hay respuesta universal, pero una guía común es 70% unitarias, 20% integración, 10% E2E. Ajustar según tu sistema: servicios backend se inclinan más hacia pruebas unitarias; aplicaciones frontend pueden necesitar más pruebas de integración (probar con DOM real).

4) **P:** ¿Cómo manejas pruebas inestables?
   - **R:** Poner en cuarentena pruebas inestables inmediatamente (omitirlas o moverlas a una suite separada). Investigar causa raíz: condiciones de carrera, timeouts, dependencias del entorno. Corregir o eliminar—las pruebas inestables erosionan la confianza en la suite de pruebas.

5) **P:** ¿Puedes omitir pruebas E2E si tienes buenas pruebas unitarias y de integración?
   - **R:** Para la mayoría de sistemas, no. Las pruebas E2E detectan problemas que solo aparecen cuando el sistema completo se ejecuta (ej., headers CORS faltantes, configuración incorrecta del balanceador de carga, flujo de autenticación que se rompe). Mantener pruebas E2E mínimas pero no eliminarlas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo dibujar la pirámide de pruebas y explicar cada capa.
- [ ] Puedo indicar la proporción ideal de tipos de pruebas (aproximada).
- [ ] Puedo explicar por qué las pruebas E2E deben ser mínimas (lentas, inestables, costosas).
- [ ] Puedo dar un ejemplo de cuándo las pruebas de integración detectan errores que las unitarias no.
- [ ] Puedo describir el anti-patrón del "cono de helado" y por qué es malo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Aseguramiento de Calidad de Software](software-quality-assurance.md)
- [Fundamentos de pruebas](testing-fundamentals.md)

### Temas relacionados
- [Calidad de código](code-quality.md)
- [Compuertas de calidad](quality-gates.md)

### Comparar con
- Forma de trofeo (Kent C. Dodds) — Prioriza pruebas de integración sobre unitarias para aplicaciones frontend; equilibrio diferente que la pirámide clásica.

## Notas / Bandeja de entrada (opcional)
- Agregar ejemplos de proyectos reales: Step Functions de Heimdall (pruebas E2E para orquestación), Opportunity Actions (pruebas de integración para llamadas a APIs de terceros).
- Considerar agregar pruebas de contrato como capa separada para microservicios.
