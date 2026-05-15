---
title: Bazel Monorepo
---

# Bazel Monorepo

## Definición
Un Bazel monorepo es un repositorio único que usa Bazel como su sistema de compilación para gestionar, compilar y probar múltiples proyectos o servicios juntos.

## Por qué importa
Permite compilaciones reproducibles, límites fuertes de dependencias y CI/CD escalable para grandes bases de código.

## Cuándo usar / cuándo no usar
Úsalo para organizaciones grandes con muchos proyectos interdependientes. No es ideal para equipos pequeños o proyectos simples.

## Trade-offs
- + Compilaciones rápidas e incrementales
- + Experiencia de desarrollo consistente
- - Curva de aprendizaje pronunciada
- - Requiere mantenimiento cuidadoso de reglas

## Prerequisitos
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)

## Temas relacionados
- [GitHub Actions](github-actions.md)
- [Docker](../operations/docker.md)

## Preguntas trampa
1. ¿Cuál es un beneficio clave de usar Bazel en un monorepo?
   - Compilaciones rápidas y reproducibles entre proyectos.
2. ¿Cuál es un desafío común con Bazel?
   - Definiciones de reglas complejas y su mantenimiento.
3. ¿Cuándo deberías evitar un Bazel monorepo?
   - Para proyectos pequeños y simples donde la sobrecarga no se justifica.
