# AGENTS.md — Knowledge Gym (Markdown estático primero)

## Misión
Construir y mantener una **base de conocimiento estática** para aprendizaje a largo plazo y preparación de entrevistas. El repositorio es un **grafo de temas**: sin duplicación, solo referencias/enlaces.

## Principios fundamentales (innegociables)
1. **Sin explicaciones duplicadas.** Si el contenido ya existe en otro tema, enlázalo en lugar de reescribirlo.
2. **Un tema canónico por concepto.** Si un usuario añade un término que ya existe, crea una página de alias en `topics/_aliases/` que apunte al archivo canónico.
3. **Navegación basada en grafo.** Cada tema debe incluir:
   - prerequisitos (enlaces),
   - temas relacionados (enlaces),
   - relaciones "parte-de / se-compara-con" cuando sea relevante.

## Convenciones del repositorio
- Todos los documentos son Markdown.
- Directorio principal de temas: `topics/`
- Los nombres de archivo usan **kebab-case**: `gin-index.md`, `event-sourcing.md`
- La ubicación de los temas sigue la taxonomía en `docs/04-taxonomy.md`.
- Páginas de índice:
  - `topics/_index.md` debe listar los temas por categoría y actualizarse cuando se añadan nuevos temas.
  - `questions/_index.md` debe listar los conjuntos de preguntas si se añaden.
  - `practices/logs/_index.md` debe listar las sesiones de práctica y enlazar a sus registros.

## Estructura requerida de los temas
Usa `templates/topic.template.md`. No todas las secciones deben estar completas; mantén las secciones vacías con "N/A" solo si realmente no aplican.

Mínimo obligatorio para cada tema nuevo:
- Definición (Qué es)
- Por qué importa
- Cuándo usar / cuándo no usar
- Trade-offs (al menos 2)
- Enlaces: prerequisitos + relacionados
- Al menos 3 "preguntas trampa" con respuestas

## Reglas de enlace (evitar duplicación)
- Prefiere enlaces relativos: `../database/index.md`
- Enlaza la primera vez que aparece un término clave.
- Si mencionas un concepto que tiene archivo de tema, **siempre enlázalo**.

## Barra de calidad
Sigue `docs/05-quality-bar.md`. Prioriza corrección, reglas de decisión y modos de fallo.

## Prompts
- Para crear un tema nuevo a partir de un término en bruto, usa: `prompts/A-topic-intake.prompt.md`
- Para crear MCQs: `prompts/B-mcq-generator.prompt.md`
- Para evaluar respuestas abiertas: `prompts/C-open-answer-evaluator.prompt.md`
- Para actualizar el seguimiento de práctica: `prompts/D-practice-log-manager.prompt.md`

Usa siempre el prompt diseñado para la acción que estás realizando.

## Tareas típicas del agente
1. Añadir un tema nuevo:
   - Determinar nombre canónico + ruta del archivo (taxonomía)
   - Crear tema usando la plantilla
   - Actualizar `topics/_index.md`
   - Añadir archivo de alias si es necesario
   - Añadir enlaces a prerequisitos/temas relacionados
2. Mejorar un tema existente:
   - Añadir trade-offs, modos de fallo, ejemplos que falten
   - Añadir "preguntas trampa"
   - Asegurarse de que no haya explicaciones duplicadas; reemplazar con enlaces
3. Gestionar seguimiento de práctica:
   - Los registros se guardan en `practices/logs/YYYY-MM-DD.<nombre-sesion-kebab>.md`
   - Actualizar `practices/logs/_index.md`, `practices/next.md` y `practices/plan.md`
   - Los contextos de práctica pueden organizarse en subcarpetas bajo `practices/` (crear según sea necesario)

## Expectativas de salida del agente
- Siempre proponer ediciones de archivos como diffs unificados o contenido completo del archivo.
- Nunca introducir carpetas nuevas sin actualizar la documentación correspondiente.
- Nunca cambiar convenciones sin actualizar `docs/02-authoring-guide.md`.
