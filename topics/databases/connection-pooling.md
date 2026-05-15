---
id: connection-pooling
title: "Pool de Conexiones"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, performance]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Pool de Conexiones

## TL;DR (BLUF)
- Un pool reutiliza conexiones de BD para reducir la sobrecarga de conexión.
- Úsalo para estabilizar la latencia y limitar la carga en la BD.
- Trade-off: demasiadas conexiones pueden sobrecargar la BD.

## Definición
**Qué es:** Un conjunto gestionado de conexiones de base de datos reutilizables.
**Términos clave:** tamaño del pool, máximo de conexiones, timeout de inactividad.

## Por qué importa
- Crear conexiones es costoso y puede limitar la BD.
- Un tamaño de pool inadecuado causa picos de latencia o sobrecarga.

## Alcance y no-objetivos
**Dentro del alcance:** dimensionamiento del pool, encolamiento e impacto operacional.
**Fuera del alcance / NO resuelto por esto:** configuración de timeout/reintentos del cliente.

## Modelo mental / Intuición
- Piensa en un conjunto compartido de sockets reutilizables hacia la BD.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Tienes acceso frecuente a la BD desde muchas solicitudes.
### Evítalo cuando
- Ya saturas la BD con demasiadas conexiones.

## Cómo lo usaría (práctico)
- **Contexto:** Servicio API con alta tasa de solicitudes.
- **Pasos:** establecer tamaño del pool según capacidad de la BD → configurar timeouts → monitorear encolamiento.
- **Cómo se ve el éxito:** latencia estable y sin tormentas de conexiones.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** menor latencia y carga controlada en la BD.
- **Desventajas / Riesgos:** pools mal configurados pueden causar encolamiento o sobrecarga.
### Alternativas
- **Proxies de BD serverless:** pooling gestionado.
- **Cómo elegir:** usar pooling por defecto y ajustar según los límites de la BD.

## Modos de fallo y trampas
- Pool demasiado grande causando sobrecarga de la BD.
- Pool demasiado pequeño causando encolamiento de solicitudes.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** conexiones activas, tiempo de espera del pool, latencia de consultas.
- **Logs:** errores de timeout de conexión.
- **Alertas:** tiempo de espera del pool creciente o saturación de conexiones de la BD.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Establecer máximo de conexiones por servicio
  - [ ] Configurar timeout de inactividad
  - [ ] Monitorear tiempo de espera del pool

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Cada solicitud abre una nueva conexión a la BD.
  - **Por qué es malo:** tormentas de conexiones y picos de latencia.
  - **Mejor enfoque:** usar un pool.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- El pool de conexiones reutiliza un número fijo de conexiones de BD para reducir la sobrecarga y proteger la base de datos. Mejora la latencia pero debe dimensionarse cuidadosamente.

### Preguntas trampa (con respuestas)
1) **P:** ¿Un pool más grande siempre es mejor?
   - **R:** no; demasiadas conexiones pueden sobrecargar la BD.
2) **P:** ¿El pooling puede ocultar consultas lentas?
   - **R:** no; de hecho puede aumentar el encolamiento.
3) **P:** ¿Necesitas pooling para serverless?
   - **R:** frecuentemente sí, o usar un proxy gestionado.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir pool de conexiones.
- [ ] Puedo indicar cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo explicar un modo de fallo.
- [ ] Puedo describir una señal de monitoreo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Database client basics](database-client-basics.md)

### Temas relacionados
- [Slow query diagnosis](slow-query-diagnosis.md)

### Comparar con
- [Database proxies](../operations/database-proxies.md)
