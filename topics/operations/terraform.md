---
id: terraform
title: "Terraform (Detallado)"
type: tool
status: learning
importance: 85
difficulty: medium
tags: [iac, automation, terraform, infrastructure, operations]
canonical: true
aliases: [tf, hashicorp-terraform]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# Terraform (Detallado)

## TL;DR (BLUF)
- Terraform es una herramienta declarativa de Infraestructura como Código (IaC) para gestionar recursos en la nube a través de múltiples proveedores (AWS, GCP, Azure).
- Úsala para infraestructura reproducible, configuraciones controladas por versión y despliegues multi-nube.
- Trade-off clave: potente abstracción y soporte multi-nube vs. complejidad de gestión de estado.

## Definición
**Qué es:** Una herramienta IaC de código abierto de HashiCorp que usa configuración declarativa (HCL -- HashiCorp Configuration Language) para aprovisionar y gestionar infraestructura a través de proveedores de nube.  
**Términos clave:** HCL (HashiCorp Configuration Language), provider, resource, module, archivo de estado, plan/apply/destroy, estado remoto, workspaces, detección de deriva.

## Por qué importa
- **Multi-nube:** Una sola herramienta para AWS, GCP, Azure, Kubernetes, etc. (1,000+ proveedores).
- **Declarativo:** Define el estado deseado; Terraform determina cómo alcanzarlo.
- **Reproducible:** La misma configuración produce infraestructura idéntica (sin clics manuales).
- **Controlado por versión:** Rastrea cambios de infraestructura en Git (revisión de código, reversión).
- **Colaboración:** El estado remoto permite la colaboración en equipo (bloqueo, estado compartido).
- **Relevancia en entrevistas:** Herramienta IaC estándar en discusiones de infraestructura en la nube.

## Alcance y no-objetivos
**Dentro del alcance:**
- Conceptos principales de Terraform (providers, resources, modules, state).
- Flujo de trabajo (init → plan → apply → destroy).
- Cuándo usar Terraform vs. alternativas.

**Fuera del alcance / NO resuelto por esto:**
- Detalles específicos del proveedor (las especificaciones de AWS pertenecen a la documentación de AWS).
- Funcionalidades avanzadas (providers personalizados, políticas Sentinel).
- Funcionalidades de Terraform Cloud/Enterprise.

## Modelo mental / Intuición
Piensa en Terraform como un **plano para tu infraestructura**:
- Escribes un plano (configuración HCL) describiendo el estado deseado: "Quiero 3 instancias EC2, 1 base de datos RDS."
- Terraform lee el plano y lo compara con el estado actual (archivo de estado).
- Calcula un plan: "Crear 2 instancias EC2 (1 ya existe), crear RDS."
- Apruebas, y Terraform ejecuta el plan (llamadas API al proveedor de nube).
- El archivo de estado se actualiza para coincidir con la realidad.

```hcl
# Blueprint (HCL)
resource "aws_instance" "web" {
  ami           = "ami-12345"
  instance_type = "t2.micro"
  count         = 3  # Desired state: 3 instances
}
```

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Gestionas infraestructura en la nube (AWS, GCP, Azure).
- Necesitas soporte multi-nube (evitar dependencia del proveedor).
- Quieres infraestructura controlada por versión (flujo de trabajo Git).
- Colaboras con un equipo (estado remoto, bloqueo).
- Necesitas entornos reproducibles (dev/staging/prod idénticos).

### Evítalo cuando
- La herramienta nativa del proveedor es más simple (por ejemplo, AWS CDK solo para AWS, abstracciones más simples).
- Se necesita gestión de configuración (usar Ansible; Terraform es para aprovisionamiento, no configuración).
- Infraestructura inmutable con orquestación de contenedores (el YAML de Kubernetes puede ser más simple).
- Infraestructura pequeña y estática (la gestión manual es aceptable).

## Cómo lo usaría (práctico)
- **Contexto:** Aprovisionar infraestructura AWS para una app web (EC2, RDS, ALB).
- **Pasos:**
  1. **Escribir configuración Terraform:**
     ```hcl
     # main.tf
     terraform {
       required_providers {
         aws = {
           source  = "hashicorp/aws"
           version = "~> 5.0"
         }
       }
     }

     provider "aws" {
       region = "us-east-1"
     }

     resource "aws_instance" "web" {
       ami           = "ami-12345"
       instance_type = "t2.micro"
       tags = {
         Name = "WebServer"
       }
     }

     resource "aws_db_instance" "db" {
       allocated_storage = 20
       engine            = "postgres"
       instance_class    = "db.t3.micro"
       username          = "admin"
       password          = var.db_password  # From variables
     }
     ```
  2. **Inicializar Terraform:**
     ```bash
     terraform init  # Downloads AWS provider plugin
     ```
  3. **Planificar cambios:**
     ```bash
     terraform plan  # Shows what will be created
     # Output:
     # + aws_instance.web
     # + aws_db_instance.db
     ```
  4. **Aplicar cambios:**
     ```bash
     terraform apply  # Creates resources
     # Prompts for confirmation; type "yes"
     ```
  5. **Archivo de estado creado:**
     - `terraform.tfstate` rastrea los recursos creados.
     - Usa estado remoto (S3 + DynamoDB) para equipos.
  6. **Hacer cambios:**
     - Editar `main.tf` (por ejemplo, cambiar tipo de instancia a `t2.small`).
     - Ejecutar `terraform plan` → muestra diferencias.
     - Ejecutar `terraform apply` → actualiza la instancia.
  7. **Destruir cuando termines:**
     ```bash
     terraform destroy  # Deletes all resources
     ```
- **Cómo se ve el éxito:** Infraestructura reproducible; cambios rastreados en Git; el equipo usa estado compartido.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Multi-nube (AWS, GCP, Azure, Kubernetes, etc.).
  - Declarativo (sintaxis simple; enfoque en estado deseado).
  - Gran ecosistema (módulos, providers, comunidad).
  - Gestión de estado (conoce el estado actual vs deseado).
  - Controlado por versión (flujo de trabajo Git para infraestructura).
- **Desventajas / Riesgos:**
  - Complejidad del archivo de estado (debe gestionarse, asegurarse y compartirse).
  - Sin gestión de secretos integrada (contraseñas en texto plano; usa Vault).
  - Detección de deriva (manual; debe ejecutar plan para detectar cambios).
  - Bloqueo requerido para equipos (prevenir applies concurrentes).
  - Refactorizar es difícil (renombrar recursos requiere manipulación de estado).

### Alternativas
- **[AWS CloudFormation](aws-cloudformation.md):** Nativo de AWS; integración más profunda con AWS; gratuito (pero solo AWS).
- **Pulumi:** Usa lenguajes de programación reales (Python, Go); más expresivo (pero menos maduro).
- **AWS CDK:** Nativo de AWS; usa TypeScript/Python; genera CloudFormation (pero solo AWS).
- **Ansible:** Gestión de configuración; puede aprovisionar infraestructura (pero menos declarativo).

### Cómo elegir
- **Multi-nube:** Terraform.
- **Solo AWS, integración profunda:** CloudFormation o CDK.
- **Preferencia de lenguaje de programación:** Pulumi o CDK.
- **Gestión de configuración:** Ansible.

## Modos de fallo y trampas
- **Pérdida del archivo de estado:** Se elimina el archivo de estado; Terraform no sabe qué creó (recursos huérfanos).
- **Corrupción del archivo de estado:** Applies concurrentes sin bloqueo; conflictos en el archivo de estado.
- **Secretos en texto plano:** Contraseñas en archivos `.tf` o archivo de estado; commiteados a Git (riesgo de seguridad).
- **Deriva (cambios manuales):** Alguien edita un recurso en la consola de AWS; Terraform no lo sabe; el próximo apply revierte el cambio.
- **Problemas de dependencia:** El recurso A depende de B, pero no se declara; Terraform crea en orden incorrecto (falla).
- **Sin reversión:** Terraform no tiene reversión nativa; debe revertir el commit de Git y re-aplicar.

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tasa de éxito de ejecuciones de Terraform (% de applies exitosos).
  - Frecuencia de detección de deriva (qué tan seguido los recursos se desvían del estado).
  - Duración del apply (applies lentos indican dependencias complejas).
- **Logs:** Logs de Terraform (modo DEBUG para solución de problemas).
- **Alertas:** Bloqueo del archivo de estado retenido por >30 minutos (apply atascado), deriva detectada, apply fallido.
- **Detección de deriva:** Ejecutar `terraform plan` regularmente (trabajo CI); alertar si se detectan cambios (indica ediciones manuales).

## Notas de implementación
- **Lista de verificación**
  - [ ] Usar estado remoto (S3 + DynamoDB para bloqueo).
  - [ ] Nunca commitear archivo de estado a Git (agregar `*.tfstate` a `.gitignore`).
  - [ ] Usar variables para secretos (pasar via variables de entorno o Vault).
  - [ ] Organizar código (módulos para componentes reutilizables).
  - [ ] Usar workspaces para entornos (dev/staging/prod).
  - [ ] Habilitar bloqueo de estado (tabla DynamoDB para protección concurrente).
  - [ ] Ejecutar `terraform plan` antes de cada apply (revisar cambios).

- **Notas de seguridad / cumplimiento**
  - Almacenar archivo de estado de forma segura (S3 con cifrado, acceso restringido).
  - Nunca commitear secretos a Git (usar variables de entorno o Vault).
  - Usar roles IAM de privilegio mínimo (Terraform necesita solo los permisos necesarios).
  - Auditar applies de Terraform (rastrear quién aplicó qué, cuándo).

- **Notas de rendimiento**
  - Paralelizar creación de recursos (Terraform lo hace automáticamente; usar `-parallelism` para limitar).
  - Usar applies dirigidos para infraestructuras grandes (`terraform apply -target=resource`).

- **Notas operacionales**
  - Documentar ubicación del archivo de estado (el equipo debe saber dónde se almacena el estado).
  - Programar detección de deriva (trabajo CI diario ejecuta `terraform plan`).
  - Planificar para migración de estado (si se cambia el backend o se divide un monolito).

## Mini ejemplo (flujo de trabajo Terraform)
```hcl
# main.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"  # For locking
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

variable "aws_region" {
  default = "us-east-1"
}

variable "instance_count" {
  default = 2
}

resource "aws_instance" "app" {
  count         = var.instance_count
  ami           = "ami-12345"
  instance_type = "t2.micro"

  tags = {
    Name = "AppServer-${count.index}"
  }
}

output "instance_ids" {
  value = aws_instance.app[*].id
}

# Workflow:
# 1. terraform init          # Download provider, setup backend
# 2. terraform plan          # Preview changes
# 3. terraform apply         # Create resources
# 4. terraform output        # View outputs
# 5. terraform destroy       # Clean up
```

**Ejemplo de salida:**
```bash
$ terraform plan
Terraform will perform the following actions:

  # aws_instance.app[0] will be created
  + resource "aws_instance" "app" {
      + ami           = "ami-12345"
      + instance_type = "t2.micro"
      + tags          = { "Name" = "AppServer-0" }
    }

  # aws_instance.app[1] will be created
  + resource "aws_instance" "app" {
      + ami           = "ami-12345"
      + instance_type = "t2.micro"
      + tags          = { "Name" = "AppServer-1" }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

$ terraform apply
# ... creates resources ...

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:
instance_ids = ["i-abc123", "i-def456"]
```

## Anti-patrones comunes
- **Anti-patrón:** Commitear archivo de estado a Git.
  - **Por qué es malo:** El estado contiene datos sensibles (IDs de recursos, IPs); los conflictos de merge rompen el estado.
  - **Mejor enfoque:** Usar estado remoto (backend S3); agregar `*.tfstate` a `.gitignore`.

- **Anti-patrón:** Sin bloqueo de estado (múltiples applies a la vez).
  - **Por qué es malo:** Applies concurrentes corrompen el archivo de estado; recursos creados dos veces.
  - **Mejor enfoque:** Habilitar bloqueo (tabla DynamoDB para backend S3).

- **Anti-patrón:** Codificar secretos en archivos `.tf`.
  - **Por qué es malo:** Secretos commiteados a Git; archivo de estado contiene contraseñas en texto plano.
  - **Mejor enfoque:** Usar variables con variables de entorno (`TF_VAR_db_password`) o integración con Vault.

- **Anti-patrón:** Configuración monolítica (todos los recursos en un archivo).
  - **Por qué es malo:** Difícil de leer; applies lentos; conflictos del equipo en el mismo archivo.
  - **Mejor enfoque:** Usar módulos (separar VPC, cómputo, base de datos en módulos).

- **Anti-patrón:** No ejecutar `terraform plan` antes de apply.
  - **Por qué es malo:** Cambios inesperados (destruir recursos accidentalmente).
  - **Mejor enfoque:** Siempre planificar primero; revisar cambios; luego aplicar.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
Terraform es una herramienta de Infraestructura como Código que te permite definir recursos en la nube en un archivo de configuración usando HCL (HashiCorp Configuration Language). Escribes código como "Quiero 3 instancias EC2 y 1 base de datos RDS," y Terraform hace llamadas API a AWS para crearlos. El flujo de trabajo es: (1) escribir configuración, (2) `terraform init` para descargar plugins del provider, (3) `terraform plan` para previsualizar cambios, (4) `terraform apply` para crear recursos. Terraform rastrea lo que creó en un "archivo de estado," para saber el estado actual vs deseado. Si luego cambias la configuración (por ejemplo, aumentar instancias a 5), Terraform calcula la diferencia y actualiza solo lo que cambió. Los principales beneficios son soporte multi-nube (funciona con AWS, GCP, Azure), control de versión (rastrear cambios de infraestructura en Git) y reproducibilidad (misma configuración = infraestructura idéntica). Las desventajas son gestión del archivo de estado (debe asegurarse y compartirse) y sin manejo nativo de secretos.

### Preguntas trampa (con respuestas)
1) **P:** ¿Qué es el archivo de estado de Terraform y por qué es importante?
   - **R:** El archivo de estado (`terraform.tfstate`) es un archivo JSON que mapea la configuración de Terraform a recursos del mundo real. Almacena IDs de recursos, atributos y dependencias. Es crítico porque Terraform lo usa para saber qué existe actualmente, así puede calcular diferencias (qué crear/actualizar/eliminar). Sin estado, Terraform no puede gestionar infraestructura. Mejor práctica: usar estado remoto (S3) y habilitar bloqueo (DynamoDB) para prevenir conflictos.

2) **P:** ¿Cómo maneja Terraform las dependencias entre recursos?
   - **R:** Terraform construye un grafo de dependencias. Puedes declarar dependencias explícitas (`depends_on`), pero Terraform también infiere dependencias implícitas (por ejemplo, ID de grupo de seguridad referenciado en configuración de instancia). Crea recursos en el orden correcto (grupo de seguridad primero, luego instancia). Si la creación falla, Terraform se detiene y deja estado parcial (puedes reintentar o destruir).

3) **P:** ¿Qué pasa si alguien cambia manualmente un recurso en la consola de AWS?
   - **R:** Terraform no sabe del cambio (el archivo de estado está obsoleto). Esto se llama "deriva." La próxima vez que ejecutes `terraform plan`, Terraform detecta la diferencia (estado real vs configuración) y propone revertir el cambio manual. Para detectar deriva regularmente, ejecuta `terraform plan` en CI (por ejemplo, trabajo nocturno) y alerta si se detectan cambios.

4) **P:** ¿Cómo gestionas secretos en Terraform?
   - **R:** No codifiques secretos en archivos `.tf` (estarían en Git y el archivo de estado). En su lugar: (1) usa variables y pasa secretos via variables de entorno (`TF_VAR_db_password=secret terraform apply`), (2) integra con HashiCorp Vault o AWS Secrets Manager, (3) usa sensitive=true para variables (oculta la salida), (4) cifra el archivo de estado (backend S3 con cifrado).

5) **P:** ¿Cuál es la diferencia entre Terraform y CloudFormation?
   - **R:** 
     - **Terraform:** Multi-nube (AWS, GCP, Azure); sintaxis HCL; código abierto; requiere gestión de estado.
     - **CloudFormation:** Solo AWS; JSON/YAML; gratuito; estado gestionado por AWS (sin archivo de estado que gestionar).
     - Elige Terraform para multi-nube o neutralidad de proveedor; elige CloudFormation para solo AWS con integración más profunda.

### Auto-verificación rápida (5 elementos)
- [ ] Puedo explicar el flujo de trabajo de Terraform (init → plan → apply → destroy).
- [ ] Puedo describir el rol del archivo de estado.
- [ ] Puedo articular cuándo usar Terraform vs. CloudFormation.
- [ ] Puedo explicar cómo gestionar secretos de forma segura en Terraform.
- [ ] Puedo identificar la deriva y cómo detectarla.

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de Infraestructura como Código](infrastructure-as-code.md)

### Temas relacionados
- [AWS CloudFormation](aws-cloudformation.md)
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)

### Comparar con
- [AWS CloudFormation](aws-cloudformation.md) — CloudFormation es solo AWS; Terraform es multi-nube.

## Notas / Bandeja de entrada
- Agregar ejemplos de proyectos reales: módulos de Terraform para infraestructura de Heimdall.
- Considerar agregar sección sobre workspaces de Terraform (dev/staging/prod).
- Vincular a mejores prácticas de módulos de Terraform cuando se creen.
