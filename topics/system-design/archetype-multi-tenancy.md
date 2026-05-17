---
id: archetype-multi-tenancy
title: "Multi-Tenencia y Aislamiento"
type: pattern
status: learning
importance: 90
difficulty: hard
tags: [system-design, multi-tenancy, saas, noisy-neighbor, isolation]
canonical: true
aliases: [tenant-isolation, noisy-neighbor, per-tenant-quotas]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Multi-Tenencia y Aislamiento

## TL;DR
- Servir a muchos clientes en infraestructura compartida de forma segura sin que uno impacte a otros.
- Desafíos clave: el vecino ruidoso impacta a otros, aislamiento de datos y cumplimiento, escalado/facturación por tenant.
- Soluciones: cuotas / rate limiting por tenant, particionado por tenant ID, aislamiento de cargas de trabajo (colas, pools).

## Dónde duele (por qué duele)
1. **El vecino ruidoso impacta a otros**: Un tenant ejecuta una exportación de nómina masiva → ralentiza la API para todos
   - **Solución**: Rate limiting / cuotas por tenant; pools de workers separados para operaciones pesadas
2. **Aislamiento de datos y cumplimiento**: GDPR/cumplimiento requiere separación estricta de tenants
   - **Solución**: Tenant ID en cada clave + aplicación de autorización; BD-por-tenant para el aislamiento más estricto
3. **Escalado y facturación por tenant**: Necesidad de rastrear uso y costes por tenant
   - **Solución**: Instrumentación por tenant ID; cuotas + medición de uso

## Reglas de decisión
- **Usar cuando**: Plataformas SaaS, productos B2B, infraestructura compartida sirviendo a múltiples clientes
- **Evitar cuando**: Cliente único (sin multi-tenencia), B2C con usuarios uniformes (sin "tenants")

## Trade-offs
- **BD compartida**: Más barata, operaciones más fáciles; riesgo de vecino ruidoso
- **BD-por-tenant**: Aislamiento estricto, escalado independiente; carga operativa
- **Elegir**: La mayoría empieza con BD compartida + cuotas; pasar a BD-por-tenant para tenants grandes/regulados

## Ejemplo explícito
Plataforma SaaS de RRHH: El Tenant A ejecuta una exportación de nómina masiva (10k filas) → bloquea tablas de la BD → las llamadas API del Tenant B hacen timeout. Solución: Límites de tasa por tenant + cola de trabajos asíncrona con cuotas. El trabajo del Tenant A se encola por separado; no bloquea al Tenant B.

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)  
**Relacionado**: [Rate Limiting](archetype-rate-limiting-quotas.md), [Seguridad y Control de Acceso](archetype-security-access-control.md)
