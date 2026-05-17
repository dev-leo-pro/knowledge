---
id: cap-theorem
title: "CAP Theorem (Practical)"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [system-design, distributed]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Teorema CAP (Práctico)

## TL;DR (BLUF)
- CAP describe los trade-offs entre consistencia, disponibilidad y tolerancia a particiones.
- Úsalo para razonar sobre el comportamiento de sistemas distribuidos bajo particiones.
- Trade-off: no puedes tener consistencia y disponibilidad completas durante particiones.

## Definición
**Qué es:** Un teorema que describe los trade-offs entre consistencia, disponibilidad y tolerancia a particiones.
**Términos clave:** consistencia, disponibilidad, tolerancia a particiones.

## Por qué importa
- Enmarca las decisiones de diseño bajo particiones de red.
- El malentendido lleva a expectativas incorrectas de fiabilidad.

## Alcance y no-objetivos
**Dentro del alcance:** trade-offs prácticos de CAP.
**Fuera del alcance / NO resuelto por esto:** pruebas formales o matices de PACELC.

## Modelo mental / Intuición
- Cuando la red se divide, debes elegir entre datos frescos y mantenerte disponible.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Diseñes sistemas de datos distribuidos.
### Evítalo cuando
- Solo ejecutes una base de datos de un solo nodo.

## Cómo lo usaría (práctico)
- **Contexto:** Escrituras multi-región.
- **Pasos:** decidir objetivos de consistencia → elegir configuración del sistema → documentar comportamiento.
- **Cómo se ve el éxito:** expectativas claras de lecturas obsoletas o tiempo de inactividad.

## Trade-offs y Alternativas
### Trade-offs
- **Pros:** decisiones de diseño más claras.
- **Contras / Riesgos:** sobresimplificación si se usa ingenuamente.
### Alternativas
- **PACELC:** agrega trade-offs de latencia.
- **Cómo elegir:** usa CAP para escenarios de partición, luego refina.

## Modos de fallo y errores comunes
- Asumir que CAP dice "elige dos" en todo momento.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** disponibilidad, tasa de errores durante particiones.
- **Logs:** errores relacionados con particiones.
- **Alertas:** timeouts o fallos de lectura aumentados.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Documentar elecciones de consistencia/disponibilidad
  - [ ] Probar comportamiento bajo particiones

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Tratar CAP como una elección operacional diaria.
  - **Por qué es malo:** solo aplica bajo particiones.
  - **Mejor enfoque:** planificar para particiones explícitamente.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- CAP dice que durante una partición de red debes elegir entre datos consistentes y disponibilidad. Es un trade-off que informa el diseño de sistemas distribuidos.

### Preguntas trampa (con respuestas)
1) **P:** ¿Puedes tener C y A sin P?
   - **R:** No en sistemas distribuidos; las particiones son inevitables.
2) **P:** ¿CAP aplica a BDs de un solo nodo?
   - **R:** Realmente no; las particiones no son relevantes.
3) **P:** ¿CAP es "elige dos" siempre?
   - **R:** Solo bajo condiciones de partición.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir CAP.
- [ ] Puedo explicar cuándo importa.
- [ ] Puedo nombrar un error común.
- [ ] Puedo describir un trade-off.
- [ ] Puedo conectar CAP con modelos de consistencia.

## Enlaces (SIN duplicación)
### Prerrequisitos
- [Fundamentos de sistemas distribuidos](distributed-systems-basics.md)

### Temas relacionados
- [Modelos de consistencia](../databases/consistency-models.md)

### Comparar con
- [PACELC](pacelc.md)
