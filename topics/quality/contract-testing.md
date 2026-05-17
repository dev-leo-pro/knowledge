---
title: Pruebas de Contrato
---

# Pruebas de Contrato

## Definición
Las pruebas de contrato son una técnica para verificar que dos sistemas separados (como un servicio y su consumidor) pueden comunicarse e intercambiar datos según lo esperado, basándose en un contrato compartido (esquema de API, formato de mensaje, etc.).

## Por qué importa
Previene fallos de integración debidos a expectativas no coincidentes entre servicios, especialmente en arquitecturas de microservicios y basadas en eventos.

## Cuándo usar / cuándo no usar
Úsalas cuando tienes servicios o equipos que se despliegan independientemente, o cuando los cambios de API/esquema son frecuentes. No es necesario para monolitos o sistemas fuertemente acoplados.

## Trade-offs
- + Detección temprana de cambios que rompen compatibilidad
- + Permite despliegues independientes
- - Requiere mantenimiento de contratos e infraestructura de pruebas
- - Puede no detectar todos los problemas de integración (ej., autenticación, red)

## Prerequisitos
- [Fundamentos de diseño de API](../system-design/api-design-basics.md)
- [Fundamentos de pruebas](../quality/testing-fundamentals.md)

## Temas relacionados
- [Pruebas de integración](../quality/testing-fundamentals.md)
- [Versionado de APIs y Eventos](../system-design/versioning-apis-and-events.md)

## Preguntas trampa
1. ¿Cuál es la principal diferencia entre pruebas de contrato y pruebas de integración?
   - Las pruebas de contrato verifican el acuerdo entre servicios; las pruebas de integración verifican el sistema completo funcionando junto.
2. ¿Qué sucede si omites las pruebas de contrato en un entorno de microservicios?
   - Corres el riesgo de romper consumidores o productores cuando las APIs cambian.
3. ¿Las pruebas de contrato pueden reemplazar las pruebas de extremo a extremo?
   - No, complementan pero no reemplazan las pruebas E2E completas.
