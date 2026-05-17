---
title: Pruebas de Integración
---

# Pruebas de Integración

## Definición
Las pruebas de integración verifican que múltiples componentes o sistemas funcionen juntos según lo previsto, enfocándose en sus interacciones y flujo de datos.

## Por qué importa
Detecta problemas que las pruebas unitarias no capturan, como dependencias mal configuradas, contratos rotos o problemas con datos del mundo real.

## Cuándo usar / cuándo no usar
Úsalas cuando tienes múltiples componentes/servicios que interactúan. No es necesario para funciones puras aisladas.

## Trade-offs
- + Detecta problemas reales de integración
- + Aumenta la confianza en el comportamiento del sistema
- - Más lentas y complejas que las pruebas unitarias
- - Pueden ser inestables si las dependencias externas son inestables

## Prerequisitos
- [Fundamentos de pruebas](../quality/testing-fundamentals.md)

## Temas relacionados
- [Pruebas de Contrato](contract-testing.md)
- [Pruebas Unitarias](../quality/testing-fundamentals.md)

## Preguntas trampa
1. ¿Por qué las pruebas de integración suelen ser más lentas que las pruebas unitarias?
   - Involucran dependencias reales (BD, red, etc.), no solo lógica en memoria.
2. ¿Cuál es un error común de las pruebas de integración?
   - Pruebas inestables debido a sistemas externos inestables.
3. ¿Deberías simular dependencias en las pruebas de integración?
   - No, usa dependencias reales o realistas para probar la integración real.
