---
id: constraints-vs-app
title: "Restricciones vs Validación a Nivel de Aplicación"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, modeling]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Restricciones vs Validación a Nivel de Aplicación

## TL;DR (BLUF)
- Las restricciones de BD imponen reglas en la capa de almacenamiento; la lógica de aplicación las impone en código.
- Usa restricciones para invariantes fundamentales y lógica de aplicación para reglas complejas o entre sistemas.
- Trade-off: las restricciones protegen la integridad pero pueden añadir complejidad de escritura.

## Definición
**Qué es:** Una comparación entre restricciones de base de datos (FK, unique, check) y validación a nivel de aplicación.
**Términos clave:** restricciones, invariantes, validación.

## Por qué importa
- Elegir la capa incorrecta lleva a inconsistencia de datos o flexibilidad reducida.
- Las restricciones son la última línea de defensa para invariantes fundamentales.

## Alcance y no-objetivos
**Dentro del alcance:** decidir qué pertenece a la BD vs la aplicación.
**Fuera del alcance / NO resuelto por esto:** garantías entre servicios.

## Modelo mental / Intuición
- Las restricciones son guardarraíles en el almacén de datos; la lógica de aplicación son reglas de negocio.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La regla es fundamental y debe cumplirse siempre.
- Quieres garantías incluso si otros sistemas escriben datos.
### Evítalo cuando
- La regla requiere contexto externo o verificaciones entre sistemas.

## Cómo lo usaría (práctico)
- **Contexto:** Email único por usuario.
- **Pasos:** aplicar restricción unique → manejar conflictos en la app.
- **Cómo se ve el éxito:** sin duplicados incluso bajo condiciones de carrera.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** integridad de datos fuerte.
- **Desventajas / Riesgos:** escrituras más estrictas, migraciones pueden ser más difíciles.
### Alternativas
- **Validación solo en app:** flexible pero con garantías más débiles.
- **Cómo elegir:** usar restricciones de BD para invariantes fundamentales, app para reglas contextuales.

## Modos de fallo y trampas
- Depender solo de verificaciones en la app y obtener duplicados por condiciones de carrera.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de violaciones de restricciones.
- **Logs:** errores de restricciones.
- **Alertas:** picos en violaciones de restricciones.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar invariantes
  - [ ] Agregar restricciones para invariantes fundamentales
  - [ ] Manejar errores de restricciones en la app

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Omitir restricciones para avanzar más rápido.
  - **Por qué es malo:** corrupción de datos con el tiempo.
  - **Mejor enfoque:** aplicar restricciones mínimas fundamentales.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las restricciones imponen reglas fundamentales de datos en la capa de base de datos, mientras que la lógica de aplicación maneja reglas de negocio. Usa restricciones para invariantes que nunca quieres que se violen.

### Preguntas trampa (con respuestas)
1) **P:** ¿Las verificaciones a nivel de app pueden reemplazar las restricciones de BD?
   - **R:** no de forma confiable; las condiciones de carrera pueden eludirlas.
2) **P:** ¿Las restricciones siempre valen la pena?
   - **R:** para invariantes fundamentales, sí; para reglas complejas, la lógica de app se ajusta mejor.
3) **P:** ¿Las restricciones ralentizan cada escritura?
   - **R:** añaden sobrecarga, pero generalmente vale la pena por la integridad.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo indicar cuándo usar restricciones.
- [ ] Puedo describir un ejemplo de condición de carrera.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo explicar casos de uso de validación a nivel de app.
- [ ] Puedo explicar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Data integrity basics](data-integrity-basics.md)

### Temas relacionados
- [Transactions](transactions.md)

### Comparar con
- [API validation](../system-design/api-validation.md)
