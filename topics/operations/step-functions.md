---
title: Step Functions
---

# Step Functions

## Definición
Step Functions es un servicio de AWS para orquestar flujos de trabajo serverless, permitiendo coordinar múltiples servicios de AWS en aplicaciones críticas de negocio usando máquinas de estado.

## Por qué importa
Permite la orquestación fiable, visual y auditable de flujos de trabajo complejos con manejo de errores, reintentos y lógica de compensación.

## Cuándo usar / cuándo no usar
Úsalo para flujos de trabajo de múltiples pasos, procesos de larga ejecución o cuando necesitas orquestación entre servicios de AWS. No es necesario para tareas simples de un solo paso.

## Trade-offs
- + Diseño y monitoreo visual de flujos de trabajo
- + Manejo de errores y reintentos integrados
- - Puede volverse complejo para flujos de trabajo grandes
- - Costo y límites de máquinas de estado

## Prerequisitos
- [AWS Lambda](lambda.md) (TODO si falta)

## Temas relacionados
- [API Gateway](../system-design/api-gateway.md)
- [DynamoDB](../databases/dynamodb.md)

## Preguntas trampa
1. ¿Qué es una Step Function?
   - Un servicio de AWS para orquestar flujos de trabajo serverless.
2. ¿Cuál es un error común con Step Functions?
   - Mezclar orquestación con lógica de negocio principal.
3. ¿Cuándo deberías evitar Step Functions?
   - Para tareas simples de un solo paso o cuando no se necesita orquestación.
