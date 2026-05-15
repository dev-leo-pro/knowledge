# Knowledge

A structured knowledge base for long-term technical learning and interview readiness. Built as a **graph of interconnected topics** — no duplication, only references and links.

The goal: be able to explain any topic in "teacher mode" — what it is, why it matters, when to use it, alternatives, trade-offs, examples, and traps.

## Repository structure

```
knowledge/
├── topics/           # Core knowledge base (223 topics)
├── assessments/      # Practice questions and problems (33 items)
├── playbooks/        # Step-by-step guides for situations (19 playbooks)
├── prompts/          # AI agent prompts for content creation
├── templates/        # Markdown templates for new content
├── docs/             # Project documentation and conventions
└── AGENTS.md         # Agent instructions for AI-assisted authoring
```

## topics/

The heart of the repo. Each topic follows a uniform structure: definition, why it matters, when to use / when not, trade-offs, related links, and trap questions.

Organized by category:

| Category | Examples |
|---|---|
| **databases/** | PostgreSQL, DynamoDB, Redis, MongoDB, MySQL, SQLite, Cassandra + cross-cutting concepts (indexing, transactions, data modeling, NoSQL patterns) |
| **system-design/** | Caching, backpressure, partitioning, CAP/PACELC, API design, rate limiting + 18 system design archetypes |
| **architecture/** | Event-driven, microservices, DDD, CQRS, event sourcing, saga, outbox, Kafka, hexagonal architecture |
| **aws/** | Lambda, Step Functions, AppSync, SQS FIFO, IAM |
| **operations/** | Networking (DNS, TCP, TLS, HTTP), reliability (circuit breaker, retries, bulkheads), observability, SLI/SLO/SLA, Kubernetes, Docker, Terraform, deployment strategies |
| **quality/** | Testing fundamentals, SOLID, clean code, design patterns, CI/CD, refactoring |

Full index: [`topics/_index.md`](topics/_index.md)

## assessments/

Practice material in three formats:

| Type | Description |
|---|---|
| **mcq/** | Multiple-choice questions — 1 correct, 1 absurd, 2 deceptive answers |
| **open-questions/** | Open-ended design and reasoning questions |
| **problem-solving/** | Step-by-step algorithmic and technical problems |

**Practice flow**: pick a topic, do 1 MCQ (4-5 min) + 1 open question (8-12 min) + 1 problem (optional, 10-20 min). Answer in BLUF format, then evaluate with Prompt C.

Full index: [`assessments/_index.md`](assessments/_index.md)

## playbooks/

Procedural guides for handling engineering and leadership situations. Unlike topics ("what is X"), playbooks describe "how I handle Y" with steps, success signals, and interview framing (STAR).

| Category | Examples |
|---|---|
| **behavioral/** | Handling conflict, receiving feedback, influencing without authority, remote collaboration |
| **decision-making/** | Trade-off decisions, handling ambiguity, prioritization, choosing databases |
| **incident-response/** | Production incident response |
| **technical/** | Code reviews, debugging performance, technical debt, continuous learning |
| **leadership/** | Leadership without authority, mentorship, owning mistakes, delivering under pressure |

Full index: [`playbooks/_index.md`](playbooks/_index.md)

## prompts/

AI agent prompts for creating and evaluating content:

| Prompt | Purpose |
|---|---|
| **A** — Topic Intake | Create or update knowledge topics |
| **B** — MCQ Generator | Generate multiple-choice questions |
| **C** — Open Answer Evaluator | Evaluate open-ended answers |
| **D** — Practice Log Manager | Manage practice session logs |
| **E** — Playbook Intake | Create behavioral/process playbooks |
| **F** — Problem Solving Agent | Generate and solve coding interview problems |

## templates/

Markdown templates that define the structure for new content:

- `topic.template.md` — Knowledge topic
- `question.template.md` — Assessment question
- `playbook.template.md` — Situational playbook
- `case.template.md` — Case study

## docs/

Project documentation and conventions:

| Document | Content |
|---|---|
| `00-vision.md` | Project vision and goals |
| `01-how-to-use.md` | Quick-start guide |
| `02-authoring-guide.md` | Writing conventions |
| `03-graph-and-links.md` | Linking rules and graph navigation |
| `04-taxonomy.md` | Topic categorization |
| `05-quality-bar.md` | Quality standards |
| `06-prompts.md` | Prompt usage guide |

## Quick start

1. Browse [`topics/_index.md`](topics/_index.md) to find topics by category
2. Read a topic and follow links to prerequisites
3. Practice with [assessments](assessments/_index.md) or the topic's own trap questions
4. To add a new topic, use [Prompt A](prompts/A-topic-intake.prompt.md)