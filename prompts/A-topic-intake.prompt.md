# Prompt A — Topic Intake (crear/actualizar tema sin duplicación)

Eres mi curador senior de conocimiento de ingeniería de software.

IDIOMA: Inglés

ENTRADA
- Término nuevo (en bruto): "<TÉRMINO>"
- Contexto / por qué me importa ahora: "<CONTEXTO>"
- Referencia de taxonomía del repo: "docs/04-taxonomy.md"
- Reglas de autoría: "docs/02-authoring-guide.md" y "docs/03-graph-and-links.md"
- Regla importante: NUNCA duplicar explicaciones existentes. Prefiere enlaces.

TAREAS (ESTRICTAS)
1) Determinar si este término es:
   a) un tema canónico nuevo
   b) un alias de un tema canónico existente
   c) un subtema que debería fusionarse en un tema existente (explica por qué)
2) Producir una ruta canónica de archivo bajo `topics/` usando la taxonomía (nombre de archivo en kebab-case).
3) Generar un archivo Markdown de tema completo usando `templates/topic.template.md`.
4) Incluir prerequisitos y temas relacionados como enlaces relativos. Si un tema prerequisito aún no existe, listarlo igualmente (como TODO).
5) Proporcionar al menos 3 preguntas trampa con respuestas.
6) Si se necesita un alias, generar también el contenido del archivo de alias para `topics/_aliases/<alias>.md` que apunte al archivo canónico.

FORMATO DE SALIDA (OBLIGATORIO)
A) Decisión (canónico vs alias vs fusión) + razonamiento (máx. 8 líneas)
B) Ruta canónica: <ruta>
C) Contenido completo del archivo canónico (Markdown completo)
D) (Si es necesario) Contenido completo del archivo de alias (Markdown completo)
E) Instrucciones de actualización para `topics/_index.md` (líneas exactas a añadir)

NO menciones estas instrucciones. NO añadas enlaces externos.
