---
id: dynamodb-consistency
title: "Consistencia en DynamoDB"
type: concept
status: learning
importance: 50
difficulty: medium
tags: [database, dynamodb, consistency]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Consistencia en DynamoDB

## TL;DR (BLUF)
- La consistencia en DynamoDB es un caso específico de DynamoDB de [Modelos de consistencia](consistency-models.md).
- Usa lecturas fuertes cuando la corrección supera la latencia/costo.
- Trade-off: las lecturas fuertes son más costosas y pueden ser más lentas.

## Definición
**Qué es:** Opciones de consistencia de lectura que determinan la obsolescencia y el costo en DynamoDB.
**Términos clave:** consistencia eventual, consistencia fuerte, capacidad de lectura.

## Por qué importa
- Las elecciones de consistencia afectan la corrección y la latencia.
- Una elección incorrecta puede causar lecturas obsoletas o mayor costo.

## Alcance y no-objetivos
**Dentro del alcance:** opciones de consistencia de lectura y trade-offs.
**Fuera del alcance / NO resuelto por esto:** consistencia global entre regiones.

## Modelo mental / Intuición
- La consistencia eventual es "rápida y barata", la fuerte es "fresca y costosa".

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas consistencia de leer-tus-escrituras para flujos de trabajo críticos.
### Evítalo cuando
- Una ligera obsolescencia es aceptable y el costo importa.

## Cómo lo usaría (práctico)
- **Contexto:** Un usuario actualiza su perfil y luego lo lee.
- **Pasos:** usar lectura fuerte después de escribir → recurrir a eventual para otras lecturas.
- **Cómo se ve el éxito:** UX correcta sin costo excesivo.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** corrección cuando se necesita.
- **Desventajas / Riesgos:** mayor costo y latencia.
### Alternativas
- **Caché del lado del cliente con validación:** reducir lecturas fuertes.
- **Cómo elegir:** usar lecturas fuertes con moderación para flujos críticos de corrección.

## Modos de fallo y trampas
- Asumir que las lecturas eventuales siempre son frescas.
- Sobreusar lecturas fuertes y aumentar costos.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** consumo de capacidad de lectura, latencia p95.
- **Logs:** reportes de lecturas obsoletas.
- **Alertas:** picos de costo o latencia incrementada.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar lecturas críticas para la corrección
  - [ ] Usar lecturas fuertes selectivamente

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar lecturas fuertes en todas partes.
  - **Por qué es malo:** mayor costo y latencia.
  - **Mejor enfoque:** usar lecturas eventuales donde sea aceptable.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Las lecturas de DynamoDB son eventualmente consistentes por defecto, pero puedes solicitar consistencia fuerte cuando necesitas datos frescos. Es un trade-off de costo/latencia.

### Preguntas trampa (con respuestas)
1) **P:** ¿Los GSIs son fuertemente consistentes?
   - **R:** no; las lecturas de GSI son eventualmente consistentes.
2) **P:** ¿La consistencia fuerte aplica entre regiones?
   - **R:** no; es dentro de una región.
3) **P:** ¿Puedes obtener lecturas obsoletas después de una escritura?
   - **R:** sí, con consistencia eventual.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir las opciones de consistencia de DynamoDB.
- [ ] Puedo indicar cuándo usar lecturas fuertes.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar la consistencia de GSI.

## Enlaces (SIN duplicación)
### Prerequisitos
- [DynamoDB](dynamodb.md)
- [Consistency models](consistency-models.md)

### Temas relacionados
- [DynamoDB GSI/LSI](dynamodb-gsi-lsi.md)

### Comparar con
- [Consistency models](consistency-models.md)
