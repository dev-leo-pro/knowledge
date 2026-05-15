---
title: Lambda
---

# Lambda

## Definición
AWS Lambda es un servicio de cómputo serverless que ejecuta código en respuesta a eventos y gestiona automáticamente los recursos de cómputo subyacentes.

## Por qué importa
Permite la ejecución de código dirigida por eventos, escalable y rentable sin gestionar servidores.

## Cuándo usar / cuándo no usar
Úsalo para cargas de trabajo dirigidas por eventos, APIs o tareas de automatización. No es ideal para aplicaciones de larga ejecución o con estado.

## Trade-offs
- + Sin gestión de servidores
- + Escala automáticamente
- - Arranques en frío y límites de timeout
- - Tiempo de ejecución y recursos limitados

## Prerequisitos
- [API Gateway](../system-design/api-gateway.md)

## Temas relacionados
- [Step Functions](step-functions.md)
- [DynamoDB](../databases/dynamodb.md)

## Preguntas trampa
1. ¿Qué es AWS Lambda?
   - Un servicio de cómputo serverless que ejecuta código en respuesta a eventos.
2. ¿Cuál es un error común con Lambda?
   - Los arranques en frío y la gestión de timeouts/memoria.
3. ¿Cuándo deberías evitar Lambda?
   - Para cargas de trabajo de larga ejecución o con estado.
