---
id: cqrs
title: "CQRS (Segregación de Responsabilidad de Comando y Consulta)"
type: pattern
status: learning
importance: 55
difficulty: medium
tags: [architecture, data, scalability]
canonical: true
aliases: []
created_at: 2026-01-21
updated_at: 2026-01-21
---

# CQRS (Segregación de Responsabilidad de Comando y Consulta)

## TL;DR (BLUF)
- CQRS separa las operaciones de escritura (comandos) de las operaciones de lectura (consultas).
- Úsalo cuando las necesidades de lectura y escritura divergen o necesitan escalado independiente.
- Trade-off: consistencia eventual e infraestructura adicional.

## Definición
**Qué es:** Un patrón que usa modelos distintos para comandos (lado de escritura) y consultas (lado de lectura), a menudo con proyecciones para construir vistas de lectura optimizadas.
**Términos clave:** modelo de comando, modelo de consulta, modelo de lectura, modelo de escritura, proyección, retraso del lado de lectura.

## Por qué importa
- Permite diferentes estrategias de optimización para lecturas y escrituras.
- Puede simplificar reglas de negocio complejas del lado de escritura mientras mantiene las consultas rápidas.

## Alcance y no-objetivos
**Dentro del alcance:** separar modelos y flujos de datos para lecturas y escrituras.
**Fuera del alcance / NO resuelto por esto:** consistencia garantizada entre los modelos de lectura y escritura.

## Modelo mental / Intuición
- Los comandos cambian el estado; las consultas hacen preguntas.
- El modelo de lectura es una proyección de los hechos del lado de escritura.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Las cargas de trabajo de lectura y escritura tienen necesidades muy diferentes.
- El dominio tiene invariantes complejos del lado de escritura.
- Puedes aceptar consistencia eventual entre modelos.
### Evítalo cuando
- Un solo modelo es suficiente y más simple.
- Necesitas consistencia estricta de lectura-después-de-escritura en todas partes.
- No puedes mantener la infraestructura de proyecciones.

## Cómo lo usaría (práctico)
- **Contexto:** Un sistema de pedidos con alto tráfico de lectura y reglas de validación complejas.
- **Pasos:**
  1) Definir APIs de comando que apliquen invariantes.
  2) Persistir cambios y emitir eventos.
  3) Construir modelos de lectura vía proyecciones optimizadas para consultas.
  4) Monitorear el retraso del lado de lectura y reconstruir proyecciones cuando sea necesario.
- **Cómo se ve el éxito:** consultas rápidas, reglas de escritura estables y retraso de lectura predecible.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** lecturas optimizadas, reglas de escritura más claras, escalado independiente.
- **Desventajas / Riesgos:** consistencia eventual, modelos duplicados y más sobrecarga operacional.
### Alternativas
- **CRUD con modelo único:** más simple para sistemas pequeños.
- **Cómo elegir:** aplica CQRS cuando las necesidades de lectura y escritura divergen significativamente.

## Modos de fallo y trampas
- Lecturas obsoletas causando confusión al usuario.
- Proyecciones quedándose atrás o fallando silenciosamente.
- Escrituras duales accidentales si los eventos y el estado se actualizan por separado.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** retraso de proyección, tasa de fallo de comandos, tiempo de reconstrucción del modelo de lectura.
- **Logs:** IDs de comando, errores de proyección, versión del modelo de lectura.
- **Trazas:** tiempos de comando → evento → actualización de proyección.
- **Alertas:** retraso sostenido o fallos repetidos de proyección.

## Notas de implementación (si aplica)
- **Lista de verificación**
  - [ ] Hacer los comandos idempotentes
  - [ ] Versionar modelos de lectura y proyecciones
  - [ ] Proporcionar mecanismos de recarga y reconstrucción
- **Notas de seguridad / cumplimiento**
  - Asegurar logs de auditoría para la ejecución de comandos.
- **Notas de rendimiento**
  - Mantener los modelos de lectura desnormalizados para velocidad de consulta.
- **Notas operacionales**
  - Monitorear y alertar sobre el retraso del lado de lectura.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Aplicar CQRS en todas partes por defecto.
  - **Por qué es malo:** añade complejidad sin beneficios claros.
  - **Mejor enfoque:** usarlo solo donde la divergencia lectura/escritura es real.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
- CQRS separa cómo cambias el estado de cómo lo lees, lo que ayuda a escalar y optimizar cada lado independientemente pero introduce consistencia eventual.

### Preguntas trampa (con respuestas)
1) **P:** ¿CQRS requiere event sourcing?
   - **R:** No; se puede implementar con persistencia tradicional también.
2) **P:** ¿Los modelos de lectura y escritura deben usar bases de datos diferentes?
   - **R:** No; pueden compartir la misma base de datos permaneciendo lógicamente separados.
3) **P:** ¿CQRS garantiza mejor rendimiento?
   - **R:** No necesariamente; añade sobrecarga y solo ayuda cuando las necesidades de lectura/escritura divergen.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir CQRS con precisión en 2-3 oraciones.
- [ ] Puedo indicar cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo concreto de memoria.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Transacciones](../databases/transactions.md)
- [Fundamentos de modelado de datos](../databases/data-modeling-basics.md)
- [Arquitectura dirigida por eventos](event-driven-basics.md)

### Temas relacionados
- [Event sourcing](event-sourcing.md)
- [Diseño Orientado al Dominio (DDD)](domain-driven-design.md)
- [Patrón Outbox](outbox-pattern.md)
- [Patrón Dual-write](dual-write-pattern.md)

### Comparar con
- [Event sourcing](event-sourcing.md) -- CQRS separa modelos de lectura/escritura; event sourcing almacena el historial de cambios.

## Notas / Bandeja de entrada (opcional)
- N/A
