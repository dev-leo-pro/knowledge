---
id: pacelc
title: "PACELC"
type: concept
status: learning
importance: 40
difficulty: medium
tags: [system-design, distributed]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# PACELC

## TL;DR (BLUF)
- PACELC extiende CAP con trade-offs de latencia durante operación normal.
- Úsalo para razonar sobre consistencia vs latencia incluso sin particiones.
- Trade-off: consistencia más fuerte frecuentemente aumenta la latencia.

## Definición
**Qué es:** Un modelo que establece: si hay Partición (P), elegir Disponibilidad (A) o Consistencia (C); si no (E, Else), elegir Latencia (L) o Consistencia (C).
**Términos clave:** latencia, consistencia, partición.

## Por qué importa
- Explica por qué los sistemas eligen entre latencia y consistencia incluso cuando están sanos.

## Alcance y no-objetivos
**Dentro del alcance:** trade-offs conceptuales para sistemas distribuidos.
**Fuera del alcance / NO resuelto por esto:** pruebas formales.

## Modelo mental / Intuición
- Incluso sin particiones, aún intercambias latencia por consistencia.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Evalúes elecciones de consistencia de BDs distribuidas.
### Evítalo cuando
- Estés en una BD de un solo nodo.

## Cómo lo usaría (práctico)
- **Contexto:** Elegir entre réplicas de lectura o consistencia fuerte.
- **Pasos:** identificar objetivos de latencia → elegir nivel de consistencia → documentar comportamiento.
- **Cómo se ve el éxito:** latencia y corrección predecibles.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** encuadre de trade-offs más claro.
- **Contras / Riesgos:** sigue siendo una abstracción.
### Alternativas
- **Teorema CAP:** vista solo de particiones.
- **Cómo elegir:** usa PACELC para trade-offs en operación normal.

## Modos de fallo y errores comunes
- Ignorar los costos de latencia de la consistencia fuerte.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** latencia p95, reportes de lecturas obsoletas.
- **Logs:** errores de consistencia.
- **Alertas:** picos de latencia bajo consistencia fuerte.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Documentar expectativas de latencia vs consistencia

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Asumir que CAP explica todo.
  - **Por qué es malo:** ignora trade-offs de latencia en condiciones normales.
  - **Mejor enfoque:** usar PACELC para incluir latencia.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- PACELC dice: bajo particiones, eliges disponibilidad o consistencia; de lo contrario, eliges latencia o consistencia. Extiende CAP con trade-offs en operación normal.

### Preguntas trampa (con respuestas)
1) **P:** ¿PACELC reemplaza CAP?
   - **R:** No; lo extiende.
2) **P:** ¿La latencia solo es una preocupación durante particiones?
   - **R:** No; importa siempre.
3) **P:** ¿PACELC garantiza corrección?
   - **R:** No; es un modelo.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir PACELC.
- [ ] Puedo explicar las elecciones P/EL/C.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir un error común.
- [ ] Puedo relacionarlo con CAP.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Teorema CAP (práctico)](cap-theorem.md)

### Temas relacionados
- [Modelos de consistencia](../databases/consistency-models.md)

### Comparar con
- [Teorema CAP (práctico)](cap-theorem.md) — trade-offs de particiones vs latencia.
