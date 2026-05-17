---
id: data-integrity-basics
title: "Fundamentos de Integridad de Datos"
type: concept
status: learning
importance: 45
difficulty: easy
tags: [database, modeling]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Fundamentos de Integridad de Datos

## TL;DR (BLUF)
- La integridad de datos asegura que los datos sean correctos, consistentes y confiables.
- Usa restricciones y validación para imponer invariantes.
- Trade-off: verificaciones más estrictas pueden ralentizar las escrituras.

## Definición
**Qué es:** La corrección y consistencia de los datos almacenados a través de las operaciones.
**Términos clave:** restricciones, invariantes, validación.

## Por qué importa
- Una integridad pobre lleva a decisiones incorrectas y bugs.
- Las violaciones de integridad se acumulan con el tiempo.

## Alcance y no-objetivos
**Dentro del alcance:** conceptos fundamentales de integridad y capas de aplicación.
**Fuera del alcance / NO resuelto por esto:** garantías de integridad entre sistemas.

## Modelo mental / Intuición
- La integridad es una red de seguridad que previene estados imposibles.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- La corrección de datos es crítica para la lógica de negocio.
### Evítalo cuando
- Los datos son efímeros y la corrección es menos importante (raro).

## Cómo lo usaría (práctico)
- **Contexto:** Unicidad de email de usuario.
- **Pasos:** agregar restricción unique → validar en la app → manejar conflictos.
- **Cómo se ve el éxito:** sin duplicados bajo concurrencia.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** corrección fuerte.
- **Desventajas / Riesgos:** sobrecarga de escritura.
### Alternativas
- **Validación solo en app:** más flexible pero con garantías más débiles.
- **Cómo elegir:** imponer invariantes fundamentales en la BD.

## Modos de fallo y trampas
- Depender solo de verificaciones en la app bajo condiciones de carrera.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** violaciones de restricciones.
- **Logs:** errores de integridad.
- **Alertas:** picos en violaciones.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar invariantes
  - [ ] Agregar restricciones

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Omitir restricciones para avanzar más rápido.
  - **Por qué es malo:** corrupción de datos a largo plazo.
  - **Mejor enfoque:** agregar restricciones mínimas fundamentales.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La integridad de datos significa que la base de datos nunca almacena estados inválidos. Las restricciones y validaciones la imponen; el trade-off es verificaciones extra en las escrituras.

### Preguntas trampa (con respuestas)
1) **P:** ¿Se puede depender solo de la validación en la app?
   - **R:** no de forma segura bajo concurrencia.
2) **P:** ¿Las restricciones eliminan todos los errores?
   - **R:** no; las reglas de negocio aún requieren lógica de app.
3) **P:** ¿La integridad es solo sobre unicidad?
   - **R:** no; incluye la corrección de todos los invariantes.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir integridad de datos.
- [ ] Puedo nombrar las capas de aplicación.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [ACID properties](acid-properties.md)

### Temas relacionados
- [Constraints vs app-level enforcement](constraints-vs-app.md)

### Comparar con
- [API validation](../system-design/api-validation.md)
