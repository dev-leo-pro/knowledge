---
id: sqlite
title: "SQLite"
type: technology
status: learning
importance: 50
difficulty: easy
tags: [database, sql, embedded]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# SQLite

## TL;DR (BLUF)
- Base de datos SQL embebida almacenada en un solo archivo.
- Úsala para aplicaciones locales, pruebas y conjuntos de datos pequeños a medianos.
- Trade-off: concurrencia y escalado limitados comparado con BDs cliente-servidor.

## Definición
**Qué es:** Una biblioteca de base de datos relacional ligera, sin servidor, que almacena datos en un archivo.
**Términos clave:** base de datos embebida, basada en archivo, ACID.

## Por qué importa
- Es ubicua para almacenamiento local y pruebas.
- Proporciona SQL sin sobrecarga operativa.

## Alcance y no-objetivos
**Dentro del alcance:** almacenamiento local, SQL de bajo mantenimiento, aplicaciones de un solo nodo.
**Fuera del alcance / NO resuelto por esto:** cargas de trabajo multi-nodo de alta concurrencia.

## Modelo mental / Intuición
- Piensa en SQLite como una base de datos que envías dentro de tu aplicación.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas SQL simple con operaciones mínimas.
- Tienes concurrencia baja a moderada.
### Evítalo cuando
- Necesitas alta concurrencia de escritura o escalado horizontal.
- Necesitas funcionalidades avanzadas de BD de un servidor.

## Cómo lo usaría (práctico)
- **Contexto:** Aplicación de escritorio local o pruebas de integración.
- **Pasos:** definir esquema → usar transacciones → hacer backup del archivo.
- **Cómo se ve el éxito:** persistencia local confiable con configuración mínima.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** cero operaciones, rápido para uso local.
- **Desventajas / Riesgos:** concurrencia y escalado limitados.
### Alternativas
- **PostgreSQL:** para cargas de trabajo multi-usuario basadas en servidor.
- **Cómo elegir:** elige SQLite para necesidades embebidas/locales; cambia a PostgreSQL cuando necesites concurrencia y escala.

## Modos de fallo y trampas
- Contención de escritura bajo cargas de trabajo concurrentes.
- Corrupción del archivo sin backups apropiados.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia de escritura, contención de bloqueos.
- **Logs:** logs de aplicación para errores de BD.
- **Alertas:** errores frecuentes de bloqueo de escritura.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Usar transacciones
  - [ ] Hacer backup del archivo de base de datos

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar SQLite para cargas de trabajo de servidor de alta concurrencia.
  - **Por qué es malo:** contención de bloqueos y picos de latencia.
  - **Mejor enfoque:** usar una BD cliente-servidor como [PostgreSQL](postgresql.md).

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- SQLite es una base de datos SQL embebida en un solo archivo. Es genial para aplicaciones locales y pruebas, pero no maneja alta concurrencia como las bases de datos de servidor.

### Preguntas trampa (con respuestas)
1) **P:** ¿SQLite es un servidor al que te conectas?
   - **R:** no; está embebida en la aplicación.
2) **P:** ¿SQLite puede manejar enormes cargas de trabajo multi-tenant?
   - **R:** no bien; la concurrencia y el escalado son limitados.
3) **P:** ¿SQLite soporta transacciones?
   - **R:** sí; soporta transacciones ACID.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir SQLite con precisión.
- [ ] Puedo decir cuándo usarlo y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un caso de uso concreto.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de SQL](sql-foundations.md)

### Temas relacionados
- [PostgreSQL](postgresql.md)
### Comparar con
- [PostgreSQL](postgresql.md) — cliente-servidor vs embebido.
