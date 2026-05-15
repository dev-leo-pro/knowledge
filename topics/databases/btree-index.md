---
id: btree-index
title: "Índice B-Tree"
type: concept
status: learning
importance: 60
difficulty: medium
tags: [database, indexing]
canonical: true
aliases: ["b-tree"]
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Índice B-Tree

## TL;DR (BLUF)
- Tipo de índice predeterminado para consultas de igualdad y rango en columnas escalares.
- Úsalo para ORDER BY, BETWEEN y coincidencias exactas.
- Trade-off: no es ideal para consultas de contención en arrays/JSON.

## Definición
**Qué es:** Un índice de árbol balanceado optimizado para datos ordenados y escaneos de rango.
**Términos clave:** igualdad, escaneo de rango, ordenamiento, selectividad.

## Por qué importa
- Es el tipo de índice más común y potencia consultas rápidas de rango/igualdad.
- Usarlo incorrectamente para patrones de contención lleva a un rendimiento pobre.

## Alcance y no-objetivos
**Dentro del alcance:** predicados de igualdad/rango escalares, ORDER BY, coincidencias por prefijo.
**Fuera del alcance / NO resuelto por esto:** consultas de contención dentro de JSON/arrays.

## Modelo mental / Intuición
- Piensa en una guía telefónica ordenada: la búsqueda binaria y los escaneos de rango son rápidos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Filtras por igualdad o rangos en columnas escalares.
- Necesitas ORDER BY o LIMIT + ORDER BY rápido.
### Evítalo cuando
- Consultas dentro de arrays o documentos JSON.
- Necesitas consultas geoespaciales o de tipo búsqueda de texto completo.

## Cómo lo usaría (práctico)
- **Contexto:** Filtrar por `created_at` o `user_id` con ordenamiento.
- **Pasos:** agregar índice B-Tree → validar con EXPLAIN → monitorear uso.
- **Cómo se ve el éxito:** baja latencia y uso del índice en los planes de consulta.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** excelente para datos ordenados y consultas de rango.
- **Desventajas / Riesgos:** no es útil para consultas de contención.
### Alternativas
- **Índice GIN:** para contención en JSONB/arrays.
- **GiST:** para tipos especializados como geoespacial.
- **Cómo elegir:** usa B-Tree para igualdad/rangos escalares; cambia para operadores especializados.

## Modos de fallo y trampas
- Índice no utilizado debido a baja selectividad o funciones envolventes.
- Orden del índice compuesto no coincide con las consultas.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** uso del índice, latencia de consultas en filtros de rango.
- **Logs:** logs de consultas lentas.
- **Alertas:** latencia sostenida en consultas ordenadas.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Asegurar que los predicados coincidan con el orden del índice
  - [ ] Validar con EXPLAIN

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar B-Tree para contención en JSON.
  - **Por qué es malo:** no usará el índice eficientemente.
  - **Mejor enfoque:** usar [GIN index](gin-index.md).

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Un índice B-Tree es el índice predeterminado para datos ordenados. Es ideal para consultas de igualdad y rango, pero no ayuda con consultas de contención dentro de JSON o arrays.

### Preguntas trampa (con respuestas)
1) **P:** ¿Es B-Tree siempre el mejor tipo de índice?
	- **R:** no; depende de los operadores y patrones de acceso.
2) **P:** ¿B-Tree acelera la contención en JSONB?
	- **R:** no; usa GIN para consultas de contención.
3) **P:** ¿El orden del índice importa?
	- **R:** sí; los índices compuestos son sensibles al orden.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir los índices B-Tree con precisión.
- [ ] Puedo indicar cuándo usarlos y cuándo no.
- [ ] Puedo explicar al menos 2 trade-offs.
- [ ] Puedo dar un ejemplo de consulta de rango.
- [ ] Puedo nombrar 1 modo de fallo y cómo detectarlo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Index](index.md)

### Temas relacionados
- [GIN index](gin-index.md)

### Comparar con
- [GIN index](gin-index.md) — consultas de contención vs escaneos ordenados.
- [GiST](gist-index.md) — tipos especializados vs escalares ordenados.
