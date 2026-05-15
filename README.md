# Knowledge

Base de conocimiento estructurada para aprendizaje técnico a largo plazo y preparación de entrevistas. Construida como un **grafo de temas interconectados** — sin duplicación, solo referencias y enlaces.

El objetivo: ser capaz de explicar cualquier tema en "modo profesor" — qué es, por qué importa, cuándo usarlo, alternativas, trade-offs, ejemplos y trampas.

## Estructura del repositorio

```
knowledge/
├── topics/           # Base de conocimiento principal (223 temas)
├── assessments/      # Preguntas y problemas de práctica (33 ítems)
├── playbooks/        # Guías paso a paso para situaciones (19 playbooks)
├── prompts/          # Prompts de agente IA para creación de contenido
├── templates/        # Plantillas Markdown para contenido nuevo
├── docs/             # Documentación y convenciones del proyecto
└── AGENTS.md         # Instrucciones de agente para autoría asistida por IA
```

## topics/

El corazón del repositorio. Cada tema sigue una estructura uniforme: definición, por qué importa, cuándo usar / cuándo no, trade-offs, enlaces relacionados y preguntas trampa.

Organizado por categoría:

| Categoría | Ejemplos |
|---|---|
| **databases/** | PostgreSQL, DynamoDB, Redis, MongoDB, MySQL, SQLite, Cassandra + conceptos transversales (indexación, transacciones, modelado de datos, patrones NoSQL) |
| **system-design/** | Caché, backpressure, particionamiento, CAP/PACELC, diseño de APIs, rate limiting + 18 arquetipos de diseño de sistemas |
| **architecture/** | Event-driven, microservicios, DDD, CQRS, event sourcing, saga, outbox, Kafka, arquitectura hexagonal |
| **aws/** | Lambda, Step Functions, AppSync, SQS FIFO, IAM |
| **operations/** | Redes (DNS, TCP, TLS, HTTP), fiabilidad (circuit breaker, reintentos, bulkheads), observabilidad, SLI/SLO/SLA, Kubernetes, Docker, Terraform, estrategias de despliegue |
| **quality/** | Fundamentos de testing, SOLID, clean code, patrones de diseño, CI/CD, refactoring |

Índice completo: [`topics/_index.md`](topics/_index.md)

## assessments/

Material de práctica en tres formatos:

| Tipo | Descripción |
|---|---|
| **mcq/** | Preguntas de opción múltiple — 1 correcta, 1 absurda, 2 engañosas |
| **open-questions/** | Preguntas abiertas de diseño y razonamiento |
| **problem-solving/** | Problemas algorítmicos y técnicos paso a paso |

**Flujo de práctica**: elige un tema, haz 1 MCQ (4-5 min) + 1 pregunta abierta (8-12 min) + 1 problema (opcional, 10-20 min). Responde en formato BLUF y evalúa con el Prompt C.

Índice completo: [`assessments/_index.md`](assessments/_index.md)

## playbooks/

Guías procedimentales para manejar situaciones de ingeniería y liderazgo. A diferencia de los temas ("qué es X"), los playbooks describen "cómo manejo Y" con pasos, señales de éxito y encuadre para entrevistas (STAR).

| Categoría | Ejemplos |
|---|---|
| **behavioral/** | Gestión de conflictos, recibir feedback, influir sin autoridad, colaboración remota |
| **decision-making/** | Decisiones de trade-off, manejar ambigüedad, priorización, elegir bases de datos |
| **incident-response/** | Respuesta a incidentes en producción |
| **technical/** | Code reviews, depuración de rendimiento, deuda técnica, aprendizaje continuo |
| **leadership/** | Liderazgo sin autoridad, mentoría, asumir errores, entregar bajo presión |

Índice completo: [`playbooks/_index.md`](playbooks/_index.md)

## prompts/

Prompts de agente IA para crear y evaluar contenido:

| Prompt | Propósito |
|---|---|
| **A** — Topic Intake | Crear o actualizar temas de conocimiento |
| **B** — MCQ Generator | Generar preguntas de opción múltiple |
| **C** — Open Answer Evaluator | Evaluar respuestas abiertas |
| **D** — Practice Log Manager | Gestionar registros de sesiones de práctica |
| **E** — Playbook Intake | Crear playbooks de comportamiento/proceso |
| **F** — Problem Solving Agent | Generar y resolver problemas de entrevista de código |

## templates/

Plantillas Markdown que definen la estructura para contenido nuevo:

- `topic.template.md` — Tema de conocimiento
- `question.template.md` — Pregunta de evaluación
- `playbook.template.md` — Playbook situacional
- `case.template.md` — Caso de estudio

## docs/

Documentación y convenciones del proyecto:

| Documento | Contenido |
|---|---|
| `00-vision.md` | Visión y objetivos del proyecto |
| `01-how-to-use.md` | Guía de inicio rápido |
| `02-authoring-guide.md` | Convenciones de escritura |
| `03-graph-and-links.md` | Reglas de enlaces y navegación del grafo |
| `04-taxonomy.md` | Categorización de temas |
| `05-quality-bar.md` | Estándares de calidad |
| `06-prompts.md` | Guía de uso de prompts |

## Inicio rápido

1. Navega [`topics/_index.md`](topics/_index.md) para encontrar temas por categoría
2. Lee un tema y sigue los enlaces a prerequisitos
3. Practica con [assessments](assessments/_index.md) o las preguntas trampa del propio tema
4. Para añadir un tema nuevo, usa el [Prompt A](prompts/A-topic-intake.prompt.md)
