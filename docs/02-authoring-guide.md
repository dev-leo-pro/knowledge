# Guía de autoría

## Regla de oro: sin duplicación
- Si ya existe un tema para un término: enlázalo.
- Si no existe: crea un tema nuevo.
- Si alguien usa otro nombre para lo mismo: crea un alias en `topics/_aliases/`.

## Nomenclatura
- Archivo: kebab-case, sin acentos, sin espacios.
- Título legible dentro del archivo: puede usar capitalización normal.

## Estructura
- Usa `templates/topic.template.md` para temas.
- Usa la plantilla de evaluación correspondiente para MCQ, abierta o resolución de problemas (ver `assessments/`).
- Mantén las secciones y completa las que apliquen.
- Escribe en inglés. Términos técnicos en inglés (entre paréntesis) la primera vez.

## Calidad
- Evita definiciones vagas.
- Incluye reglas de decisión (cuándo sí / cuándo no).
- Incluye trade-offs (coste, complejidad, rendimiento, operabilidad).
- Incluye 3+ preguntas trampa con respuestas (para temas).

## Enlaces
- Usa enlaces relativos.
- Enlaza siempre prerequisitos y temas relacionados.

## Evaluaciones
Todas las evaluaciones (MCQ, abiertas, resolución de problemas) deben colocarse en la carpeta correspondiente bajo `assessments/`, no en `topics/`.
