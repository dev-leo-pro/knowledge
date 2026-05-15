---
title: IAM (AWS)
---

# IAM (AWS)

## Definición
IAM (Identity and Access Management) es un servicio de AWS para gestionar el acceso a recursos de AWS de forma segura mediante usuarios, grupos, roles y políticas.

## Por qué importa
Aplica el acceso con mínimos privilegios, protege operaciones sensibles y permite el cumplimiento de estándares de seguridad.

## Cuándo usar / cuándo no usar
Úsalo en todos los entornos de AWS para controlar el acceso. No es necesario para recursos que no son de AWS.

## Trade-offs
- + Control de acceso granular
- + Auditable y gestionable
- - Puede ser complejo de configurar
- - Permisos demasiado amplios son un riesgo

## Prerequisitos
- [Acuerdo de Nivel de Servicio (SLA)](../operations/service-level-agreement.md)

## Temas relacionados
- [Controles de uso de datos](../operations/data-use-controls.md)
- [Lambda](lambda.md)

## Preguntas trampa
1. ¿Qué es IAM en AWS?
   - Gestión de identidad y acceso para controlar el acceso a recursos de AWS.
2. ¿Cuál es un error común con IAM?
   - Usar comodines o permisos demasiado amplios.
3. ¿Cuándo deberías evitar IAM?
   - Solo para recursos que no son de AWS; siempre úsalo para AWS.
