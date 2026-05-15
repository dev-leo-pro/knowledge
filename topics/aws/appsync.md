---
title: AppSync
---

# AppSync

## Definición
AppSync es un servicio administrado de AWS para construir APIs GraphQL con datos en tiempo real, autorización e integración con otros servicios de AWS.

## Por qué importa
Simplifica la creación de APIs GraphQL escalables y seguras, y soporta suscripciones en tiempo real, acceso offline y agregación de datos.

## Cuándo usar / cuándo no usar
Úsalo para construir APIs modernas que requieran consultas flexibles, actualizaciones en tiempo real o integración con fuentes de datos de AWS. No es necesario para APIs REST simples o cuando GraphQL no es requerido.

## Trade-offs
- + API GraphQL administrada y escalable
- + Soporte en tiempo real y offline
- - Complejidad en la lógica de resolvers
- - Costo y curva de aprendizaje

## Prerequisitos
- [GraphQL](../system-design/graphql.md)

## Temas relacionados
- [DynamoDB](../databases/dynamodb.md)
- [API Gateway](../system-design/api-gateway.md)

## Preguntas trampa
1. ¿Qué es AppSync?
   - Servicio administrado de AWS para APIs GraphQL.
2. ¿Cuál es un error común con AppSync?
   - Lógica de resolvers compleja y gestión de costos.
3. ¿Cuándo deberías evitar AppSync?
   - Para APIs simples o cuando GraphQL no es necesario.
