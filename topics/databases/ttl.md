---
id: ttl
title: "TTL (Time-to-Live)"
type: concept
status: learning
importance: 45
difficulty: easy
tags: [database, caching]
canonical: true
aliases: ["time-to-live"]
created_at: 2026-01-19
updated_at: 2026-01-19
---

# TTL (Time-to-Live)

## TL;DR (BLUF)
- TTL define cuándo los datos expiran y pueden ser eliminados automáticamente.
- Úsalo para entradas de caché o registros efímeros.
- Trade-off: la expiración es frecuentemente de mejor esfuerzo, no exacta.

## Definición
**Qué es:** Un mecanismo para expirar datos después de un período de tiempo especificado.
**Términos clave:** expiración, desalojo, eliminación de mejor esfuerzo.

## Por qué importa
- Previene el crecimiento del almacenamiento y la acumulación de datos obsoletos.
- Depender de un timing preciso de TTL puede causar problemas de corrección.

## Alcance y no-objetivos
**Dentro del alcance:** TTL como mecanismo de ciclo de vida de datos.
**Fuera del alcance / NO resuelto por esto:** garantías de programación precisa.

## Modelo mental / Intuición
- Piensa en TTL como un trabajo de limpieza en segundo plano.

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Los datos son efímeros y pueden expirar de forma segura.
### Evítalo cuando
- Necesitas timing estricto de eliminación por seguridad o cumplimiento.

## Cómo lo usaría (práctico)
- **Contexto:** Tokens de sesión o flags temporales.
- **Pasos:** configurar TTL al escribir → aplicar expiración en lecturas → monitorear limpieza.
- **Cómo se ve el éxito:** el almacenamiento se mantiene acotado y los datos obsoletos se eliminan.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:** limpieza automática y almacenamiento acotado.
- **Desventajas / Riesgos:** el timing de eliminación es aproximado.
### Alternativas
- **Trabajos programados:** control preciso de expiración.
- **Cómo elegir:** usa TTL para limpieza de mejor esfuerzo, trabajos para timing estricto.

## Modos de fallo y trampas
- Usar TTL como frontera de seguridad.

## Observabilidad (Cómo detectar problemas)
- **Métricas:** crecimiento de almacenamiento, tasa de eliminación.
- **Logs:** intentos de acceso a elementos expirados.
- **Alertas:** almacenamiento que no decrece como se esperaba.

## Notas de implementación (si aplica)
- **Checklist**
  - [ ] No depender de TTL para eliminación inmediata
  - [ ] Aplicar expiración en lógica de aplicación cuando sea necesario

## Mini ejemplo (si aplica)
N/A

## Anti-patrones comunes
- **Anti-patrón:** Asumir que TTL es exacto.
  - **Por qué es malo:** eliminaciones retrasadas pueden exponer datos obsoletos.
  - **Mejor enfoque:** validar expiración en tiempo de lectura.

## Preparación para entrevistas
### "Explícalo como si estuviera enseñando"
- TTL marca datos para expirar automáticamente, lo cual es genial para caché y registros temporales. Pero la mayoría de sistemas eliminan elementos eventualmente, no exactamente en la fecha límite.

### Preguntas trampa (con respuestas)
1) **P:** ¿TTL garantiza eliminación inmediata?
   - **R:** no; usualmente es de mejor esfuerzo.
2) **P:** ¿TTL puede reemplazar verificaciones de autenticación?
   - **R:** no; aplica expiración en la lógica de aplicación.
3) **P:** ¿TTL es solo para cachés?
   - **R:** no; puede usarse para cualquier dato efímero.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo definir TTL con precisión.
- [ ] Puedo decir cuándo usarlo.
- [ ] Puedo nombrar un trade-off.
- [ ] Puedo describir una trampa.
- [ ] Puedo explicar la eliminación de mejor esfuerzo.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de caché](../system-design/caching-fundamentals.md)

### Temas relacionados
- [Redis](redis.md)
- [DynamoDB TTL](dynamodb-ttl.md)

### Comparar con
- [Trabajos de limpieza programados](../operations/scheduled-cleanup-jobs.md)
