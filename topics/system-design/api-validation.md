---
id: api-validation
title: "Validación de API"
type: concept
status: learning
importance: 45
difficulty: easy
tags: [system-design, api]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Validación de API

## TL;DR (BLUF)
- La validación de API aplica corrección de entrada en el límite del sistema.
- Úsala para prevenir que datos inválidos entren al sistema.
- Trade-off: una validación más estricta puede reducir la flexibilidad.

## Definición
**Qué es:** Reglas y verificaciones aplicadas a las entradas de la API antes del procesamiento.
**Términos clave:** validación de esquema, restricciones, sanitización de entrada.

## Por qué importa
- Previene que datos incorrectos se propaguen al almacenamiento.
- Reduce bugs y inconsistencias aguas abajo.

## Alcance y no-objetivos
**Dentro del alcance:** validar payloads y aplicar restricciones.
**Fuera del alcance / NO resuelto por esto:** garantías de integridad a nivel de base de datos.

## Modelo mental / Intuición
- Trata la validación como la primera barrera de protección.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Clientes externos envían datos que deben ser verificados.
### Evítalo cuando
- Puedes confiar en las entradas y necesitas máxima flexibilidad (raro).

## Cómo lo usaría (práctico)
- **Contexto:** Endpoint de registro de usuario.
- **Pasos:** validar esquema → aplicar campos requeridos → rechazar entradas inválidas.
- **Cómo se ve el éxito:** baja tasa de datos inválidos.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** datos más limpios y menos bugs.
- **Desventajas / Riesgos:** fricción para los clientes.
### Alternativas
- **Restricciones de base de datos:** integridad más fuerte en el almacenamiento.
- **Cómo elegir:** valida en el límite de la API y aplica invariantes fundamentales en la BD.

## Modos de fallo y trampas
- Validación excesivamente estricta que bloquea cambios válidos.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** tasa de errores de validación.
- **Logs:** payloads inválidos.
- **Alertas:** picos en fallos de validación.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Definir esquemas
  - [ ] Devolver mensajes de error accionables

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Aceptar cualquier payload sin validación.
  - **Por qué es malo:** riesgo de corrupción de datos.
  - **Mejor enfoque:** validar campos requeridos y tipos.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- La validación de API verifica la entrada en el límite del sistema para que los datos incorrectos no entren. Complementa las restricciones de la base de datos y mantiene los datos limpios.

### Preguntas trampa (con respuestas)
1) **P:** ¿La validación de API reemplaza las restricciones de BD?
   - **R:** no; las restricciones de BD aún protegen la integridad.
2) **P:** ¿La validación es solo sobre tipos?
   - **R:** no; incluye reglas de negocio y rangos.
3) **P:** ¿Se puede omitir la validación para APIs internas?
   - **R:** no siempre; los bugs internos también ocurren.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir la validación de API.
- [ ] Puedo indicar cuándo usarla.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de diseño de API](api-design-basics.md)

### Temas relacionados
- [Fundamentos de integridad de datos](../databases/data-integrity-basics.md)

### Comparar con
- [Restricciones vs aplicación a nivel de app](../databases/constraints-vs-app.md) — verificaciones de BD vs API.
