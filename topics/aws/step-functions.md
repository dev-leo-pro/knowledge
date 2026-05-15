---
title: Step Functions
---

# Step Functions

## Definición
Step Functions es un servicio de AWS para orquestar flujos de trabajo serverless, permitiéndote coordinar múltiples servicios de AWS en aplicaciones críticas para el negocio usando máquinas de estado.

## Por qué importa
Permite la orquestación confiable, visual y auditable de flujos de trabajo complejos con manejo de errores, reintentos y lógica de compensación.

## Cuándo usar / cuándo no usar
Úsalo para flujos de trabajo de múltiples pasos, procesos de larga ejecución o cuando necesitas orquestación entre servicios de AWS. No es necesario para tareas simples de un solo paso.

## Trade-offs
- + Diseño visual de flujos de trabajo y monitoreo
- + Manejo de errores y reintentos incorporados
- - Puede volverse complejo para flujos de trabajo grandes
- - Costo y límites de máquinas de estado

## Prerequisitos
- [AWS Lambda](lambda.md)

## Temas relacionados
- [API Gateway](../system-design/api-gateway.md)
- [DynamoDB](../databases/dynamodb.md)

## Preguntas trampa
1. ¿Qué es una Step Function?
   - Servicio de AWS para orquestar flujos de trabajo serverless.
2. ¿Cuál es un error común con Step Functions?
   - Mezclar orquestación con lógica de negocio central.
3. ¿Cuándo deberías evitar Step Functions?
   - Para tareas simples de un solo paso o cuando la orquestación no es necesaria.
