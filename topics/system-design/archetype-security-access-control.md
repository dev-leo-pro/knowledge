---
id: archetype-security-access-control
title: "Security & Access Control at Scale"
type: pattern
status: learning
importance: 95
difficulty: hard
tags: [system-design, security, access-control, rbac, abac, authorization, authentication]
canonical: true
aliases: [authentication-authorization, rbac-abac, policy-engines]
created_at: 2026-01-28
updated_at: 2026-01-28
---

# Seguridad y Control de Acceso a Escala

## TL;DR
- Controlar quién puede hacer qué, con auditabilidad y baja latencia a escala.
- Desafíos clave: reglas complejas (roles, recursos, contextos), rendimiento de verificaciones de autorización, requisitos de auditoría.
- Soluciones: política-como-datos (servicio central de políticas), RBAC para simple / ABAC para complejo, log de auditoría como almacenamiento de solo-append.

## Dónde duele (por qué duele)
1. **Reglas complejas (roles, recursos, contextos)**: "Admin puede ver nómina pero no editar contratos; manager puede ver solo su equipo"
   - **Solución**: RBAC (roles simples) o ABAC (basado en atributos para contextos complejos); motor de políticas (OPA, Zanzibar)
2. **Rendimiento de verificaciones de autorización**: Cada petición verifica permisos → sobrecarga de latencia
   - **Solución**: Cachear decisiones (con TTL), precomputar verificaciones comunes, decisiones de políticas como datos
3. **Requisitos de auditoría**: El cumplimiento requiere logs de "quién accedió a qué cuándo"
   - **Solución**: Log de auditoría de solo-append (inmutable), servicio de logging centralizado

## Reglas de decisión
- **Usar cuando**: SaaS empresarial, finanzas, salud (cumplimiento), multi-tenancy con permisos
- **Evitar cuando**: Usuario admin único (sin roles), API pública de solo lectura

## Trade-offs
- **RBAC (Basado en Roles)**: Simple, fácil de razonar; limitado (explosión de roles para reglas complejas)
- **ABAC (Basado en Atributos)**: Flexible, consciente del contexto (tiempo, ubicación, atributos del recurso); complejo de depurar
- **Elegir**: Roles simples → RBAC; contexto complejo → ABAC

## Ejemplo explícito
SaaS empresarial: Los usuarios tienen roles (Admin, Manager, Empleado). Admin puede ver todos los datos; Manager puede ver solo los datos de su equipo; Empleado puede ver solo los propios. Regla ABAC: `allow if (role == Manager AND resource.team_id == user.team_id) OR (role == Admin)`. La política se evalúa en cada petición; cacheada por 60s. Log de auditoría: cada acceso registrado con (usuario, recurso, acción, resultado, timestamp).

## Enlaces
**Parte de**: [Arquetipos de Diseño de Sistemas](system-design-archetypes.md)
**Relacionado**: [Multi-Tenancy](archetype-multi-tenancy.md), [Limitación de Tasa](archetype-rate-limiting-quotas.md)
**Tecnologías**: OPA (Open Policy Agent), Google Zanzibar, AWS IAM, RBAC, ABAC
