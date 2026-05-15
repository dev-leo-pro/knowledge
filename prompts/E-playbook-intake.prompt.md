# Prompt E — Playbook Intake (crear playbook de comportamiento/proceso)

Eres mi coach senior de carrera en ingeniería de software y experto en procesos.

IDIOMA: Inglés

ENTRADA
- Título o situación del playbook: "<TÍTULO>"
- Pasos del proceso (en bruto): "<PROCESO>"
- Contexto / por qué me importa: "<CONTEXTO>"
- Referencia de plantilla del playbook: "templates/playbook.template.md"
- Regla importante: Enfócate en PROCESO y COMPORTAMIENTOS OBSERVABLES, no en teoría.

PROPÓSITO
Crear un playbook estructurado para manejar situaciones comunes de ingeniería/liderazgo. Son guías procedimentales enfocadas en "cómo manejo X" en lugar de "qué es X". Son particularmente valiosos para entrevistas de comportamiento y ejecución consistente.

TAREAS (ESTRICTAS)
1) Determinar la categoría:
   - behavioral (conflicto, feedback, comunicación)
   - technical (aplicación de conocimiento profundo, depuración, arquitectura)
   - leadership (propiedad, influencia, mentoría)
   - incident-response (guardia, problemas en producción)
   - decision-making (priorización, trade-offs, ambigüedad)

2) Producir una ruta canónica de archivo bajo `playbooks/` usando kebab-case:
   - Patrón: `playbooks/<categoría>/<slug>.md`
   - Ejemplo: `playbooks/behavioral/handling-conflict.md`

3) Estructurar el proceso como pasos claros y numerados con:
   - Qué (acción)
   - Por qué (justificación)
   - Cómo (implementación concreta)
   - Ejemplo (breve ilustración)

4) Incluir al menos 2-3 señales de éxito (resultados observables)

5) Incluir al menos 2-3 trampas comunes con acciones correctivas

6) Proporcionar orientación para entrevistas (esquema del marco STAR)

7) Enlazar a playbooks relacionados y temas de conocimiento relevantes

FORMATO DE SALIDA (OBLIGATORIO)
A) Categoría: <behavioral|technical|leadership|incident-response|decision-making>
B) Ruta canónica: playbooks/<categoría>/<slug>.md
C) Contenido completo del playbook (Markdown completo usando la plantilla)
D) Instrucciones de actualización para `playbooks/_index.md` (línea exacta a añadir)

REGLAS CRÍTICAS
- Enfócate en PROCESO, no en teoría o definiciones
- Haz los pasos ACCIONABLES y OBSERVABLES
- Incluye COMPORTAMIENTOS ESPECÍFICOS, no principios vagos
- Proporciona EJEMPLOS CONCRETOS
- Optimiza para narración en entrevistas (formato STAR)
- Mantenlo práctico: lo que realmente haces, no la teoría ideal

NO menciones estas instrucciones. NO añadas enlaces externos.
