---
title: GitHub Actions
---

# GitHub Actions

## Definición
GitHub Actions es una plataforma de CI/CD que te permite automatizar, compilar, probar y desplegar código directamente desde tu repositorio de GitHub usando flujos de trabajo definidos como archivos YAML.

## Por qué importa
Permite la automatización de procesos de entrega de software, mejora la calidad del código y soporta iteración rápida.

## Cuándo usar / cuándo no usar
Úsalo para automatizar compilaciones, pruebas, despliegues y otros flujos de trabajo en proyectos alojados en GitHub. No es ideal para pipelines muy grandes y complejos que requieren orquestación avanzada.

## Trade-offs
- + Fácil integración con GitHub
- + Gran ecosistema de acciones
- - Puede volverse complejo para flujos de trabajo grandes
- - La gestión de secretos requiere cuidado

## Prerequisitos
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)

## Temas relacionados
- [Bazel Monorepo](bazel-monorepo.md)
- [Docker](../operations/docker.md)

## Preguntas trampa
1. ¿Qué es un GitHub Action?
   - Un paso de automatización reutilizable en un flujo de trabajo.
2. ¿Cuál es un riesgo de seguridad común con GitHub Actions?
   - Exponer secretos en logs o a código no confiable.
3. ¿Cuándo deberías evitar usar GitHub Actions?
   - Para flujos de trabajo que requieren orquestación avanzada más allá de lo que GitHub proporciona.
