---
id: consistency-models
title: "Modelos de Consistencia"
type: concept
status: learning
importance: 55
difficulty: medium
tags: [database, distributed]
canonical: true
aliases: []
created_at: 2026-01-19
updated_at: 2026-01-19
---

# Modelos de Consistencia

## TL;DR (BLUF)
- Los modelos de consistencia definen qué tan frescas son las lecturas en relación con las escrituras.
- Úsalos para razonar sobre la corrección en sistemas distribuidos.
- Trade-off: una consistencia más fuerte puede reducir la disponibilidad o la latencia.

## Definición
**Qué es:** Garantías sobre la visibilidad de lectura entre réplicas y a lo largo del tiempo.
**Términos clave:** consistencia fuerte, consistencia eventual, leer-tus-escrituras.

## Por qué importa
- Determina si los usuarios pueden ver datos obsoletos.
- Influye en las decisiones de diseño y el manejo de fallos.

## Alcance y no-objetivos
**Dentro del alcance:** garantías de consistencia a alto nivel y trade-offs.
**Fuera del alcance / NO resuelto por esto:** pruebas formales o algoritmos de consenso.

## Modelo mental / Intuición
- Piensa en la consistencia como qué tan "sincronizadas" están las copias de los datos.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Necesitas garantías de corrección para flujos de usuario.
### Evítalo cuando
- Una ligera obsolescencia es aceptable y la disponibilidad importa más.

## Cómo lo usaría (práctico)
- **Contexto:** Un usuario actualiza su perfil y luego lo lee.
- **Pasos:** requerir lecturas fuertes para flujos críticos → permitir eventual para datos de feed.
- **Cómo se ve el éxito:** UX correcta con latencia aceptable.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** mayor corrección con consistencia fuerte.
- **Desventajas / Riesgos:** mayor latencia o disponibilidad reducida.
### Alternativas
- **Caché del lado del cliente:** para rendimiento con validación.
- **Cómo elegir:** usar la consistencia más débil que cumpla las necesidades de corrección.

## Modos de fallo y trampas
- Asumir que la consistencia eventual siempre es fresca.
- Mezclar niveles de consistencia sin reglas claras.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** reportes de lecturas obsoletas, latencia p95.
- **Logs:** errores relacionados con consistencia.
- **Alertas:** picos en quejas de lecturas obsoletas.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] Identificar lecturas críticas para la corrección
  - [ ] Documentar las expectativas de consistencia

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Usar consistencia fuerte en todas partes.
  - **Por qué es malo:** mayor latencia y costo.
  - **Mejor enfoque:** usar lecturas fuertes solo donde se requiera.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- Los modelos de consistencia describen qué tan rápido las lecturas reflejan las escrituras. La consistencia fuerte da datos frescos pero puede ser más lenta; la consistencia eventual es más rápida pero puede ser obsoleta.

### Preguntas trampa (con respuestas)
1) **P:** ¿Consistencia eventual significa "siempre obsoleto"?
   - **R:** no; solo permite obsolescencia en algunos casos.
2) **P:** ¿La consistencia fuerte siempre está disponible?
   - **R:** no; puede reducir la disponibilidad durante particiones.
3) **P:** ¿Se pueden mezclar niveles de consistencia?
   - **R:** sí, pero debes ser explícito sobre las expectativas.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir modelos de consistencia.
- [ ] Puedo explicar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo nombrar una regla de decisión.
- [ ] Puedo relacionarlo con CAP.

## Enlaces (SIN duplicación)
### Prerequisitos
- [CAP theorem (practical)](../system-design/cap-theorem.md)

### Temas relacionados
- [DynamoDB consistency](dynamodb-consistency.md)

### Comparar con
- [Isolation levels (general)](isolation-levels.md)
