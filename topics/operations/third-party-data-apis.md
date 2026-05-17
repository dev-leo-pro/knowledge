---
title: APIs de datos de terceros
---

# APIs de datos de terceros

## Definición
Las APIs de datos de terceros son interfaces externas proporcionadas por proveedores o plataformas para acceder a datos o servicios, como email, CRM o analíticas.

## Por qué importa
Permiten una integración rápida de capacidades externas, pero introducen riesgos en torno a fiabilidad, privacidad y cumplimiento.

## Cuándo usar / cuándo no usar
Úsalas cuando necesites enriquecer tu aplicación con datos o servicios externos. No es necesario para sistemas puramente internos.

## Trade-offs
- + Acceso rápido a capacidades externas
- + Reduce tiempo de desarrollo
- - Dependencia del proveedor y APIs cambiantes
- - Riesgos de privacidad, cumplimiento y fiabilidad

## Prerequisitos
- [Webhooks](webhooks.md)

## Temas relacionados
- [Nylas](nylas.md)
- [Gong](gong.md)

## Preguntas trampa
1. ¿Qué es una API de datos de terceros?
   - Una interfaz externa para acceder a datos o servicios de otro proveedor.
2. ¿Cuál es un riesgo común con APIs de terceros?
   - Cambios incompatibles o interrupciones fuera de tu control.
3. ¿Cómo puedes mitigar los riesgos con APIs de terceros?
   - Usa reintentos, monitoreo y contratos claros.
