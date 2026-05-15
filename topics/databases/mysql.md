---
id: mysql
title: "MySQL"
type: technology
status: learning
importance: 60
difficulty: medium
tags: [database, sql]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# MySQL

## TL;DR (BLUF)
- Base de datos relacional open-source popular con ecosistema sólido y soporte SQL.
- Úsala para cargas de trabajo relacionales estándar y amplio soporte de herramientas.
- Trade-off: el conjunto de funcionalidades y el optimizador difieren de PostgreSQL.

## Definición
**Qué es:** Un sistema de gestión de bases de datos relacionales (RDBMS) que soporta SQL con un gran ecosistema.
**Términos clave:** ACID, motor de almacenamiento, optimizador de consultas.

## Por qué importa
- Es común en sistemas de producción y procesos de contratación.
- Entender las diferencias con [PostgreSQL](postgresql.md) ayuda en la toma de decisiones.

## Alcance y no-objetivos
**Dentro del alcance:** modelado relacional, consultas SQL, cargas de trabajo transaccionales.
**Fuera del alcance / NO resuelto por esto:** acceso orientado a documentos o escala masiva sin arquitectura.

## Modelo mental / Intuición
- Piensa en MySQL como un caballo de batalla relacional pragmático con amplia compatibilidad.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas una base de datos relacional ampliamente adoptada con herramientas maduras.
- No necesitas funcionalidades avanzadas o extensiones de Postgres.
### Evítalo cuando
- Requieres funcionalidades o extensiones específicas de Postgres.
- Necesitas modelado estilo documento sin joins.

## Cómo lo usaría (práctico)
- **Contexto:** Aplicación web estándar con datos relacionales.
- **Pasos:** modelar esquema → agregar índices → usar InnoDB → monitorear consultas lentas.
- **Cómo se ve el éxito:** latencia de consultas estable y operaciones manejables.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** ecosistema maduro, familiaridad operativa, amplias opciones de hosting.
- **Desventajas / Riesgos:** diferencias de paridad de funcionalidades con Postgres; el comportamiento del optimizador puede diferir.
### Alternativas
- **PostgreSQL:** extensiones y funcionalidades SQL más ricas.
- **Cómo elegir:** preferir Postgres para funcionalidades avanzadas; elegir MySQL por ubicuidad o compatibilidad.

## Modos de fallo y trampas
- Regresiones de rendimiento por malas elecciones de índices.
- Diferencias entre motores de almacenamiento causando sorpresas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia p95 de consultas, conteo de consultas lentas, ratio de aciertos del buffer pool.
- **Logs:** logs de consultas lentas.
- **Alertas:** picos sostenidos de consultas lentas.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Elegir InnoDB
  - [ ] Diseñar índices basados en consultas reales
  - [ ] Habilitar logs de consultas lentas

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** asumir paridad de funcionalidades con Postgres.
  - **Por qué es malo:** lleva a expectativas incorrectas y migraciones.
  - **Mejor enfoque:** validar requisitos de funcionalidades por adelantado.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- MySQL es una base de datos relacional ampliamente usada con fuerte soporte SQL y herramientas. Es un buen valor por defecto para muchas aplicaciones, pero sus funcionalidades y optimizador difieren de PostgreSQL, así que elige según necesidades específicas.

### Preguntas trampa (con respuestas)
1) **P:** ¿MySQL es "lo mismo que Postgres"?
   - **R:** no; las funcionalidades, extensiones y comportamiento del optimizador difieren.
2) **P:** ¿MySQL solo usa un motor de almacenamiento?
   - **R:** no; InnoDB es común, pero existen otros con diferentes características.
3) **P:** ¿Los índices son opcionales para el rendimiento?
   - **R:** no; los índices son frecuentemente esenciales para el rendimiento de consultas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir MySQL con precisión.
- [ ] Puedo decir cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo compararlo con PostgreSQL.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Índice](index.md)

### Temas relacionados
- [PostgreSQL](postgresql.md)

### Comparar con
- [PostgreSQL](postgresql.md) — extensiones y funcionalidades SQL más ricas.
