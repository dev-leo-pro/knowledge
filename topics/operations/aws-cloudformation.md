---
id: aws-cloudformation
title: "AWS CloudFormation (Detallado)"
type: tool
status: learning
importance: 80
difficulty: medium
tags: [iac, aws, cloudformation, infrastructure, operations]
canonical: true
aliases: [cfn, cloudformation]
created_at: 2026-01-20
updated_at: 2026-01-20
---

# AWS CloudFormation (Detallado)

## TL;DR (BLUF)
- CloudFormation es el servicio nativo de Infraestructura como Código (IaC) de AWS para aprovisionar y gestionar recursos AWS usando plantillas declarativas (JSON/YAML).
- Usar para infraestructura exclusivamente AWS, integración profunda con AWS y estado gestionado.
- Trade-off clave: conveniencia específica de AWS y estado gestionado gratuito vs. dependencia del proveedor.

## Definición
**Qué es:** El servicio nativo de IaC de AWS que usa plantillas declarativas (JSON o YAML) para aprovisionar y gestionar recursos AWS como una unidad única llamada "stack".  
**Términos clave:** stack, plantilla, recurso, parámetro, output, detección de drift, change set, stacks anidados, StackSets (multi-cuenta/región), rollback, estado gestionado por AWS.

## Por qué importa
- **Nativo de AWS:** Integración profunda con servicios AWS (soporte nativo para todos los recursos AWS).
- **Estado gestionado:** AWS gestiona el estado por ti (sin archivos de estado que asegurar o compartir).
- **Gratuito:** Sin coste adicional (solo pagas por los recursos AWS creados).
- **Declarativo:** Define el estado deseado; CloudFormation se encarga del aprovisionamiento.
- **Rollback:** Rollback automático ante fallos (si la creación del stack falla, elimina todos los recursos).
- **Relevancia en entrevistas:** IaC estándar para arquitecturas centradas en AWS.

## Alcance y no-objetivos
**Dentro del alcance:**
- Conceptos core de CloudFormation (stacks, plantillas, recursos, parámetros).
- Cuándo usar CloudFormation vs. alternativas.
- Detección de drift y change sets.

**Fuera del alcance / NO resuelto por esto:**
- Multi-cloud (CloudFormation es solo AWS; usar Terraform para multi-cloud).
- Funcionalidades avanzadas (recursos personalizados, macros, AWS CDK).
- Sintaxis específica de recursos (consultar la documentación de AWS para cada tipo de recurso).

## Modelo mental / Intuición
Piensa en CloudFormation como un **plano de construcción para AWS**:
- Escribes un plano (plantilla YAML) describiendo la infraestructura deseada: "Quiero 1 VPC, 2 subnets, 1 instancia EC2, 1 base de datos RDS."
- CloudFormation lee el plano y crea un "stack" (grupo de recursos).
- Aprovisiona recursos en el orden correcto (primero VPC, luego subnets, luego instancias).
- Si algo falla, CloudFormation elimina automáticamente todo (rollback).
- AWS rastrea el estado del stack internamente (no gestionas archivos de estado).

```yaml
# Plano (plantilla YAML)
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-12345
      InstanceType: t2.micro
```

## Reglas de decisión (Cuándo usar / Cuándo no usar)
### Úsalo cuando
- Infraestructura solo AWS (sin necesidades multi-cloud).
- Quieres estado gestionado (sin buckets S3 ni bloqueo que configurar).
- Necesitas integración profunda con AWS (soporte nativo para todos los servicios AWS).
- Una herramienta IaC gratuita es importante (CloudFormation es gratuito).
- Quieres rollback automático (si la creación del stack falla, todos los recursos se eliminan).

### Evítalo cuando
- Se necesita multi-cloud o neutralidad de proveedor (usar Terraform).
- Prefieres lenguajes de programación sobre YAML (usar AWS CDK o Pulumi).
- Necesitas abstracciones avanzadas (CDK proporciona constructos de nivel superior).
- Los límites de tamaño de plantilla son restrictivos (51,200 bytes para YAML; usar stacks anidados o Terraform).

## Cómo lo usaría (práctico)
- **Contexto:** Aprovisionando infraestructura AWS para una aplicación web (VPC, EC2, RDS).
- **Pasos:**
  1. **Escribir plantilla CloudFormation:**
     ```yaml
     # app-stack.yaml
     AWSTemplateFormatVersion: '2010-09-09'
     Description: Web app infrastructure

     Parameters:
       InstanceType:
         Type: String
         Default: t2.micro
         AllowedValues:
           - t2.micro
           - t2.small

     Resources:
       WebServer:
         Type: AWS::EC2::Instance
         Properties:
           ImageId: ami-12345
           InstanceType: !Ref InstanceType
           Tags:
             - Key: Name
               Value: WebServer

       Database:
         Type: AWS::RDS::DBInstance
         Properties:
           DBInstanceClass: db.t3.micro
           Engine: postgres
           AllocatedStorage: 20
           MasterUsername: admin
           MasterUserPassword: !Ref DBPassword

     Outputs:
       InstanceId:
         Value: !Ref WebServer
         Description: EC2 instance ID
     ```
  2. **Crear stack:**
     ```bash
     aws cloudformation create-stack \
       --stack-name my-app-stack \
       --template-body file://app-stack.yaml \
       --parameters ParameterKey=DBPassword,ParameterValue=secret123
     ```
  3. **Monitorear creación del stack:**
     ```bash
     aws cloudformation describe-stacks --stack-name my-app-stack
     # Status: CREATE_IN_PROGRESS → CREATE_COMPLETE
     ```
  4. **Actualizar stack:**
     - Editar plantilla (ej., cambiar tipo de instancia a `t2.small`).
     - Crear change set (previsualizar cambios):
       ```bash
       aws cloudformation create-change-set \
         --stack-name my-app-stack \
         --change-set-name update-instance-type \
         --template-body file://app-stack.yaml
       ```
     - Revisar change set, luego ejecutar:
       ```bash
       aws cloudformation execute-change-set \
         --change-set-name update-instance-type \
         --stack-name my-app-stack
       ```
  5. **Eliminar stack:**
     ```bash
     aws cloudformation delete-stack --stack-name my-app-stack
     # Elimina todos los recursos (EC2, RDS, etc.)
     ```
- **Cómo se ve el éxito:** Infraestructura reproducible; rollback automático ante fallos; sin archivos de estado que gestionar.

## Trade-offs y alternativas
### Trade-offs
- **Ventajas:**
  - Nativo de AWS (soporta todos los servicios AWS inmediatamente).
  - Estado gestionado (sin buckets S3, sin configuración de bloqueo).
  - Gratuito (sin coste adicional más allá de los recursos AWS).
  - Rollback automático (si la creación del stack falla, todos los recursos se eliminan).
  - Detección de drift (detectar cambios manuales via consola AWS).
  - Change sets (previsualizar cambios antes de aplicar).
- **Desventajas / Riesgos:**
  - Solo AWS (dependencia del proveedor; no puede gestionar GCP o Azure).
  - YAML/JSON verboso (menos expresivo que Terraform HCL o CDK).
  - Límites de tamaño de plantilla (51,200 bytes; infraestructura grande requiere stacks anidados).
  - Sin módulos (reutilización más difícil que los módulos de Terraform).
  - Actualizaciones más lentas (AWS añade nuevos servicios a CloudFormation con retraso).

### Alternativas
- **[Terraform](terraform.md):** Multi-cloud; sintaxis HCL; requiere gestión de estado (pero más flexible).
- **AWS CDK:** Usa lenguajes de programación (TypeScript, Python); genera CloudFormation (pero añade capa de abstracción).
- **Pulumi:** Multi-cloud; usa lenguajes de programación; más expresivo (pero requiere gestión de estado).
- **Ansible:** Gestión de configuración; puede aprovisionar infraestructura (pero menos declarativo).

### Cómo elegir
- **Solo AWS, gratuito, estado gestionado:** CloudFormation.
- **Multi-cloud o neutralidad de proveedor:** Terraform.
- **Preferencia por lenguaje de programación (solo AWS):** AWS CDK.
- **Preferencia por lenguaje de programación (multi-cloud):** Pulumi.

## Modos de fallo y trampas
- **Rollback ante fallo:** La creación del stack falla (ej., AMI inválida); CloudFormation elimina todos los recursos (puede perder progreso).
- **Dependencias circulares:** El recurso A depende de B, B depende de A; la creación del stack falla.
- **Cambios manuales (drift):** Alguien edita un recurso en la consola; CloudFormation no lo sabe; la próxima actualización puede revertir el cambio.
- **DeletionPolicy no configurada:** Eliminar stack; base de datos RDS eliminada (pérdida de datos).
- **Sin validación de parámetros:** Pasar parámetro inválido (ej., tipo de instancia incorrecto); la creación del stack falla.
- **Actualización requiere reemplazo:** Cambiar tipo de instancia; CloudFormation elimina la instancia antigua, crea una nueva (tiempo de inactividad).

## Observabilidad (Cómo detectar problemas)
- **Métricas:**
  - Tasa de éxito de creación de stacks (% de stacks que se completan con éxito).
  - Duración de actualización de stacks (actualizaciones lentas indican dependencias complejas).
  - Frecuencia de detección de drift (con qué frecuencia los recursos se desvían de la plantilla).
- **Logs:** Eventos de CloudFormation (eventos de creación, actualización, eliminación de recursos).
- **Alertas:** Creación de stack fallida, drift detectado, actualización de stack atascada (ROLLBACK_IN_PROGRESS).
- **Detección de drift:** Ejecutar `detect-drift` regularmente (job de CI); alertar si se encuentra drift (cambios manuales).

## Notas de implementación
- **Checklist**
  - [ ] Usar parámetros para valores específicos del entorno (tipo de instancia, contraseña de DB).
  - [ ] Configurar DeletionPolicy para recursos críticos (RDS, S3; prevenir eliminación accidental).
  - [ ] Usar change sets para actualizaciones (previsualizar cambios antes de ejecutar).
  - [ ] Habilitar protección de terminación para stacks de producción (prevenir eliminación accidental).
  - [ ] Etiquetar todos los recursos (asignación de costes, seguimiento de entorno).
  - [ ] Usar outputs para referencias entre stacks (exportar VPC ID, importar en otro stack).
  - [ ] Programar detección de drift (job diario de CI; alertar ante drift).

- **Notas de seguridad / cumplimiento**
  - Usar roles IAM para CloudFormation (mínimo privilegio; solo permisos necesarios).
  - Nunca codificar secretos en las plantillas (usar Secrets Manager o Parameter Store).
  - Habilitar política de stack (prevenir actualizaciones/eliminaciones accidentales de recursos críticos).
  - Auditar cambios de stack (CloudTrail registra todas las llamadas API de CloudFormation).

- **Notas de rendimiento**
  - Usar stacks anidados para infraestructuras grandes (evitar límites de tamaño de plantilla).
  - Paralelizar creación de recursos (CloudFormation hace esto automáticamente).

- **Notas operativas**
  - Documentar dependencias de stacks (stack de VPC antes del stack de aplicación).
  - Usar StackSets para despliegues multi-cuenta/multi-región.
  - Probar plantillas en entorno de desarrollo antes de producción.

## Mini ejemplo (plantilla CloudFormation)
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Simple web app infrastructure

Parameters:
  Environment:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - staging
      - prod

  InstanceType:
    Type: String
    Default: t2.micro
    Description: EC2 instance type

Resources:
  # VPC
  AppVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      Tags:
        - Key: Name
          Value: !Sub '${Environment}-vpc'

  # EC2 Instance
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-12345
      InstanceType: !Ref InstanceType
      SubnetId: !Ref AppSubnet
      Tags:
        - Key: Name
          Value: !Sub '${Environment}-web-server'

  # Subnet (depends on VPC)
  AppSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref AppVPC
      CidrBlock: 10.0.1.0/24
      Tags:
        - Key: Name
          Value: !Sub '${Environment}-subnet'

  # RDS (with DeletionPolicy to prevent accidental deletion)
  Database:
    Type: AWS::RDS::DBInstance
    DeletionPolicy: Snapshot  # Creates snapshot before deletion
    Properties:
      DBInstanceClass: db.t3.micro
      Engine: postgres
      AllocatedStorage: 20
      MasterUsername: admin
      MasterUserPassword: '{{resolve:secretsmanager:MyDBPassword}}'
      Tags:
        - Key: Name
          Value: !Sub '${Environment}-database'

Outputs:
  VPCId:
    Value: !Ref AppVPC
    Description: VPC ID
    Export:
      Name: !Sub '${Environment}-vpc-id'  # Can import in other stacks

  InstanceId:
    Value: !Ref WebServer
    Description: EC2 instance ID

# Create stack:
# aws cloudformation create-stack \
#   --stack-name dev-app-stack \
#   --template-body file://template.yaml \
#   --parameters ParameterKey=Environment,ParameterValue=dev

# Update stack (with change set):
# aws cloudformation create-change-set \
#   --stack-name dev-app-stack \
#   --change-set-name update-1 \
#   --template-body file://template.yaml
# aws cloudformation execute-change-set \
#   --change-set-name update-1 \
#   --stack-name dev-app-stack

# Delete stack:
# aws cloudformation delete-stack --stack-name dev-app-stack
```

## Anti-patrones comunes
- **Anti-patrón:** Codificar secretos en la plantilla.
  - **Por qué es malo:** Los secretos son visibles en la consola de CloudFormation y las llamadas API; riesgo de seguridad.
  - **Mejor enfoque:** Usar Secrets Manager o Parameter Store; referenciar via `{{resolve:secretsmanager:...}}`.

- **Anti-patrón:** Sin DeletionPolicy para recursos críticos.
  - **Por qué es malo:** Eliminar stack -> base de datos RDS eliminada -> pérdida de datos.
  - **Mejor enfoque:** Configurar `DeletionPolicy: Snapshot` para RDS, `DeletionPolicy: Retain` para S3.

- **Anti-patrón:** Sin change sets para actualizaciones.
  - **Por qué es malo:** Cambios inesperados (instancia reemplazada en lugar de actualizada).
  - **Mejor enfoque:** Siempre crear change set; revisar; luego ejecutar.

- **Anti-patrón:** Plantilla monolítica (todos los recursos en una plantilla).
  - **Por qué es malo:** Difícil de mantener; límites de tamaño de plantilla; conflictos de equipo.
  - **Mejor enfoque:** Usar stacks anidados o stacks separados por capa (stack de VPC, stack de app, stack de base de datos).

- **Anti-patrón:** Sin protección de terminación para stacks de producción.
  - **Por qué es malo:** Un `delete-stack` accidental elimina toda la infraestructura de producción.
  - **Mejor enfoque:** Habilitar protección de terminación; requerir deshabilitación manual antes de la eliminación.

## Preparación para entrevistas
### Explícalo como si estuviera enseñando
CloudFormation es el servicio de Infraestructura como Código de AWS. Escribes una plantilla YAML o JSON describiendo recursos AWS (EC2, RDS, VPC, etc.), y CloudFormation los aprovisiona como un "stack". El flujo de trabajo es: (1) escribir plantilla, (2) crear stack (CloudFormation aprovisiona recursos en el orden correcto), (3) actualizar stack (CloudFormation calcula el diff y actualiza solo lo que cambió), (4) eliminar stack (CloudFormation elimina todos los recursos). El beneficio clave es que AWS gestiona el estado por ti — sin archivos de estado que asegurar o compartir, a diferencia de Terraform. CloudFormation también tiene rollback automático: si la creación del stack falla, elimina todos los recursos automáticamente. La principal desventaja es la dependencia del proveedor (solo AWS; no puede gestionar GCP o Azure) y la sintaxis YAML verbosa (menos expresiva que Terraform HCL o AWS CDK).

### Preguntas trampa (con respuestas)
1) **P:** ¿Cómo gestiona CloudFormation el estado?
   - **R:** CloudFormation gestiona el estado internamente en AWS. No gestionas archivos de estado (a diferencia de Terraform). AWS rastrea los recursos del stack, sus IDs y dependencias. Puedes ver el estado del stack via la consola AWS o la API `describe-stacks`. Esto simplifica la colaboración (sin buckets S3 ni bloqueo) pero te ata a AWS.

2) **P:** ¿Qué es un change set y por qué es importante?
   - **R:** Un change set es una previsualización de lo que CloudFormation cambiará cuando actualices un stack. Muestra qué recursos se crearán, actualizarán o eliminarán. Es crítico porque algunas actualizaciones reemplazan recursos (ej., cambiar tipo de instancia elimina la instancia antigua, crea una nueva — tiempo de inactividad). Siempre revisa los change sets antes de ejecutar para evitar sorpresas.

3) **P:** ¿Qué pasa si falla la creación de un recurso durante la creación del stack?
   - **R:** CloudFormation hace rollback automáticamente: elimina todos los recursos creados hasta el momento y establece el estado del stack como `ROLLBACK_COMPLETE`. Esto previene stacks parciales (infraestructura a medio crear). Puedes deshabilitar el rollback (`--disable-rollback`) para depuración, pero esto deja recursos huérfanos. Mejor: corregir el problema y reintentar.

4) **P:** ¿Cómo prevenir la eliminación accidental de recursos críticos?
   - **R:** Usar dos mecanismos: (1) Configurar `DeletionPolicy: Retain` o `DeletionPolicy: Snapshot` en recursos críticos (RDS, S3); cuando se elimina el stack, el recurso se retiene o se crea snapshot. (2) Habilitar protección de terminación en el stack; debe deshabilitarse manualmente antes de la eliminación.

5) **P:** ¿Cuál es la diferencia entre CloudFormation y Terraform?
   - **R:** 
     - **CloudFormation:** Solo AWS; JSON/YAML; estado gestionado por AWS; gratuito; rollback automático.
     - **Terraform:** Multi-cloud; HCL; estado autogestionado (S3 + DynamoDB); código abierto; sin rollback automático.
     - Elegir CloudFormation para solo AWS con estado gestionado; elegir Terraform para multi-cloud o neutralidad de proveedor.

### Auto-verificación rápida (5 ítems)
- [ ] Puedo explicar cómo CloudFormation gestiona el estado (gestionado por AWS, sin archivos de estado).
- [ ] Puedo describir el rol de los change sets (previsualizar actualizaciones antes de ejecutar).
- [ ] Puedo articular cuándo usar CloudFormation vs. Terraform.
- [ ] Puedo explicar DeletionPolicy y protección de terminación.
- [ ] Puedo identificar modos de fallo (rollback ante error, drift por cambios manuales).

## Enlaces (SIN duplicación)
### Prerequisitos
- [Fundamentos de Infraestructura como Código](infrastructure-as-code.md)

### Temas relacionados
- [Terraform](terraform.md)
- [Fundamentos de CI/CD](../quality/ci-cd-basics.md)

### Comparar con
- [Terraform](terraform.md) — Terraform es multi-cloud; CloudFormation es solo AWS.

## Notas / Bandeja de entrada
- Añadir ejemplos de proyectos reales: plantillas CloudFormation para infraestructura AWS de Heimdall.
- Considerar añadir sección sobre stacks anidados y StackSets.
- Enlazar a AWS CDK cuando se cree (abstracción de nivel superior sobre CloudFormation).
