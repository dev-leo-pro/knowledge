---
id: postgresql
title: "PostgreSQL"
type: technology
status: learning
importance: 80
difficulty: medium
tags: [database, sql]
canonical: true
aliases: ["postgres"]
created_at: 2026-01-19
updated_at: 2026-01-19
---

# PostgreSQL

## TL;DR (BLUF)
- Base de datos relacional open-source para cargas de trabajo SQL con restricciones fuertes y extensibilidad.
- Úsala cuando necesitas garantías ACID, consultas ricas y un ecosistema maduro.
- Trade-off: el ajuste y escalado requieren diseño; una mala indexación puede perjudicar las escrituras.

## Definición
**Qué es:** Un sistema de gestión de bases de datos relacionales (RDBMS) que soporta SQL, transacciones ACID, extensiones y planificación avanzada de consultas.
**Términos clave:** ACID, restricciones, planificador de consultas, extensiones, MVCC.

## Por qué importa
- Es un estándar de backend para corrección, herramientas y confiabilidad.
- Las restricciones fuertes + índices permiten consistencia y rendimiento para consultas complejas.

## Alcance y no-objetivos
**Dentro del alcance:** modelado de datos relacional, transacciones, joins, reportes.
**Fuera del alcance / NO resuelto por esto:** escalado horizontal transparente y latencia global ultra-baja sin arquitectura adicional.

## Modelo mental / Intuición
- Piensa en un bibliotecario estricto: las reglas de integridad de datos se aplican, y el planificador elige la ruta más rápida usando índices.
- Buen esquema + buenos índices → rendimiento predecible.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas consistencia fuerte, joins y consultas complejas.
- Valoras restricciones, migraciones y madurez del ecosistema.
### Evítalo cuando
- Tu carga de trabajo es extremadamente intensiva en escrituras con patrones de acceso simples (un almacén KV puede encajar mejor).
- Necesitas latencia globalmente baja sin una arquitectura multi-región.

## Cómo lo usaría (práctico)
- **Contexto:** Servicio backend con entidades relacionales y reportes.
- **Pasos:** modelar esquema → definir restricciones → indexar basándose en consultas reales → agregar connection pooling → monitorear latencia/bloqueos.
- **Cómo se ve el éxito:** latencia p95 predecible, baja contención de bloqueos, throughput de escritura estable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** potente, flexible, confiable, rico en funcionalidades.
- **Desventajas / Riesgos:** complejidad de ajuste; malos índices perjudican escrituras; el escalado requiere diseño explícito.
### Alternativas
- **MySQL:** similar, diferente optimizador/funcionalidades.
- **DynamoDB:** no relacional, requiere diseño por patrón de acceso.
- **MongoDB:** modelo de documentos; trade-offs de joins/transacciones.
- **Cómo elegir:** elige PostgreSQL cuando la integridad relacional y la flexibilidad SQL importan más.

## Modos de fallo y trampas
- Contención de bloqueos por transacciones largas.
- Consultas lentas por índices faltantes/incorrectos.
- Bloat de tablas/índices por actualizaciones pesadas sin mantenimiento.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia p95 de consultas, esperas de bloqueo, deadlocks, ratio de aciertos de caché, bloat.
- **Logs:** logs de consultas lentas, reportes de deadlock.
- **Trazas:** spans mostrando que el tiempo de BD domina las solicitudes.
- **Alertas:** latencia p95 sostenida, esperas de bloqueo en aumento, ratio de aciertos de caché bajo.

## Notas de implementación (si aplica)
- **Checklist**
   - [ ] Definir restricciones (FK, unique)
   - [ ] Diseñar índices basados en consultas reales
   - [ ] Medir con EXPLAIN
   - [ ] Configurar backups y réplicas
- **Notas de seguridad / Cumplimiento**
   - Seguir roles de BD con privilegios mínimos.
- **Notas de rendimiento**
   - Mantener transacciones cortas; evitar índices innecesarios.
- **Notas operativas**
   - Monitorear bloat y comportamiento de vacuum.

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Modelar todo como JSON sin necesidad.
   - **Por qué es malo:** pierdes restricciones y claridad.
   - **Mejor enfoque:** modela campos estables como columnas y usa [JSONB](jsonb.md) solo para verdadera flexibilidad.
- **Anti-patrón:** Agregar índices "por si acaso."
   - **Por qué es malo:** penaliza escrituras y complica el mantenimiento.
   - **Mejor enfoque:** indexar basándose en patrones de consulta reales.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- PostgreSQL es una base de datos relacional que garantiza transacciones ACID y consultas SQL ricas. Es genial cuando necesitas integridad de datos fuerte y joins complejos, pero requiere diseño cuidadoso de esquema e índices, y el escalado requiere arquitectura deliberada.

### Preguntas trampa (con respuestas)
1) **P:** ¿Por qué un índice puede hacer un INSERT más lento?
    - **R:** cada escritura debe mantener/actualizar el índice, aumentando el trabajo y la contención.
2) **P:** ¿JSONB siempre es mejor que columnas normales?
    - **R:** no; JSONB sacrifica restricciones y claridad si se usa para datos estructurados estables.
3) **P:** ¿Postgres escala horizontalmente por sí solo?
    - **R:** no de forma transparente; necesita particionamiento, réplicas o sharding externo.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir PostgreSQL en 2-3 oraciones.
- [ ] Puedo explicar cuándo usarlo y cuándo no.
- [ ] Puedo nombrar al menos 2 trade-offs.
- [ ] Puedo dar un escenario de uso concreto.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Índice](index.md)

### Temas relacionados
- [JSONB](jsonb.md)
- [PostgreSQL MVCC](postgresql-mvcc.md)
- [PostgreSQL vacuum y autovacuum](postgresql-vacuum-autovacuum.md)
- [Bloat en PostgreSQL](postgresql-bloat.md)
- [Niveles de aislamiento en PostgreSQL](postgresql-isolation-levels.md)
- [Estadísticas y ANALYZE de PostgreSQL](postgresql-stats-and-analyze.md)

### Comparar con
- [DynamoDB](dynamodb.md) — clave-valor a escala vs SQL relacional.
- [MySQL](mysql.md) — RDBMS similar con diferentes funcionalidades/optimizador.
- [Documentos de MongoDB](mongodb-documents.md) — modelo de documentos vs restricciones relacionales.
