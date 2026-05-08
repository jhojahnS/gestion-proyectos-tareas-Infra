# Infraestructura como Código (IaC) - Gestión de Proyectos y Tareas

Este repositorio contiene la **infraestructura como código (IaC)** necesaria para desplegar en AWS los recursos cloud del proyecto **Gestión de Proyectos y Tareas**.

El proyecto forma parte del **Trabajo de Fin de Grado (TFG)** y está dividido en varios repositorios:

- Aplicación cliente desarrollada en **.NET MAUI**
- API backend desarrollada con **FastAPI**
- Infraestructura cloud definida mediante **AWS CloudFormation**

**Este repositorio NO contiene la lógica de negocio del backend ni el código de la aplicación cliente.**  
Aquí se define la infraestructura necesaria para ejecutar la API, almacenar los datos y monitorizar los servicios en AWS.

---

## Descripción del proyecto

Este repositorio permite definir y desplegar de forma automatizada la infraestructura cloud necesaria para el funcionamiento del sistema.

La infraestructura incluye, de forma progresiva, servicios como:

- Red y configuración de conectividad
- Computación para ejecutar la API FastAPI
- Base de datos
- Monitorización mediante CloudWatch
- Gestión de secretos
- Alarmas y control de costes

El objetivo principal es:

- Automatizar el despliegue de infraestructura
- Garantizar reproducibilidad entre entornos
- Separar la infraestructura del código de aplicación
- Facilitar el mantenimiento del sistema
- Aplicar buenas prácticas DevOps
- Controlar el consumo y los costes en AWS

---

## Tecnologías utilizadas

| Componente | Tecnología |
|---|---|
| Infraestructura como código | AWS CloudFormation |
| Cloud Provider | AWS |
| Backend | FastAPI |
| Cliente | .NET MAUI |
| Base de datos | PostgreSQL en Amazon RDS |
| CI/CD | GitHub Actions |
| Monitorización | Amazon CloudWatch |
| Control de versiones | Git |
| Repositorio | GitHub |

---

## Arquitectura del sistema

La arquitectura general del proyecto se basa en una aplicación cliente que consume una API desplegada en AWS.

```text
App .NET MAUI
      ↓
API FastAPI en AWS
      ↓
PostgreSQL en Amazon RDS
```

La infraestructura cloud se compone de los siguientes bloques:

- **Network:** configuración de red, subredes y grupos de seguridad.
- **Compute:** recursos donde se ejecutará la API FastAPI.
- **Database:** almacenamiento persistente de los datos de la aplicación.
- **Monitoring:** logs, métricas y alarmas mediante CloudWatch.
- **Security:** permisos, roles IAM y gestión de secretos.

---

## Estructura del repositorio

```
gestion-proyectos-tareas-Infra
│
├── .github
│   └── workflows
│       └── infra-ci.yml
│
├── infra
│   └── templates
│       ├── compute
│       ├── database
│       ├── monitoring
│       └── network
│
├── .gitignore
└── README.md
```

---

## Requisitos

Para trabajar con este repositorio es necesario disponer de:

- Cuenta de AWS
- AWS CLI instalado y configurado
- Git
- Cuenta de GitHub
- Permisos adecuados para desplegar recursos en AWS

---

## Configuración de credenciales

Para trabajar en local se puede configurar AWS CLI mediante:

```bash
aws configure
```

También se pueden usar variables de entorno:

```bash
export AWS_ACCESS_KEY_ID=xxx
export AWS_SECRET_ACCESS_KEY=xxx
export AWS_REGION=eu-west-1
```

En el despliegue mediante GitHub Actions se recomienda utilizar credenciales seguras mediante GitHub Secrets u OIDC, evitando subir claves al repositorio.

---

## Despliegue de la infraestructura

### Validar una plantilla CloudFormation

```bash
aws cloudformation validate-template \
  --template-body file://infra/templates/network/vpc.yml
```

### Desplegar un stack

```bash
aws cloudformation deploy \
  --template-file infra/templates/network/vpc.yml \
  --stack-name develop-vpc \
  --region eu-west-1
```

### Eliminar un stack

```bash
aws cloudformation delete-stack \
  --stack-name develop-vpc \
  --region eu-west-1
```

---

## Funcionalidades de la infraestructura

### Network

Recursos relacionados con la red del proyecto:

- VPC
- Subnets
- Internet Gateway
- Tablas de rutas
- Security Groups

### Compute

Recursos destinados a ejecutar la API desarrollada con FastAPI.

Según la evolución del proyecto, esta parte podrá implementarse mediante:

- EC2
- ECS Fargate
- Lambda
- Elastic Beanstalk

### Database

Recursos destinados al almacenamiento persistente de la aplicación.

La base de datos seleccionada para el proyecto será **PostgreSQL**, desplegada en AWS mediante **Amazon RDS**.

Esta sección incluirá recursos como:

- Instancia RDS PostgreSQL
- Grupo de subredes para la base de datos
- Security Group específico para la base de datos
- Configuración de backups
- Parámetros de conexión
- Integración con Secrets Manager para credenciales

### Monitoring

Recursos de observabilidad y control:

- CloudWatch Log Groups
- Métricas
- Alarmas
- Notificaciones
- Control de costes

### Security

Recursos relacionados con seguridad:

- Roles IAM
- Policies
- Secrets Manager
- Permisos para despliegues desde GitHub Actions

---

## Flujo de trabajo del proyecto

El proyecto utiliza un flujo basado en Git Flow simplificado.

### Ramas principales

- `main` → versión estable
- `develop` → desarrollo

### Ramas de trabajo

- `feature/*`
- `fix/*`
- `refactor/*`
- `docs/*`

Ejemplos:

```
feature/vpc
feature/cloudwatch-logs
feature/database
fix/security-group
docs/readme
```

### Flujo de desarrollo

```bash
git checkout develop
git pull origin develop
git checkout -b feature/nueva-infra
```

Después de realizar cambios:

```bash
git add .
git commit -m "feat(infra): añadir plantilla inicial de red"
git push origin feature/nueva-infra
```

---

## Convención de commits

Formato recomendado:

```
tipo(ambito): descripción
```

Ejemplos:

```
feat(network): crear vpc base
feat(database): añadir plantilla inicial de base de datos
feat(monitoring): crear log group de cloudwatch
fix(iam): corregir permisos del rol de despliegue
docs(readme): actualizar documentación inicial
```

Tipos habituales:

| Tipo | Uso |
|---|---|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección |
| `docs` | Documentación |
| `refactor` | Reorganización de código |
| `ci` | Cambios en pipelines |
| `chore` | Tareas menores |

---

## Integración continua y despliegue

El proyecto utiliza GitHub Actions para automatizar validaciones y despliegues de infraestructura.

### Objetivo del pipeline

Garantizar que:

- Las plantillas CloudFormation son válidas
- No existen errores de sintaxis
- La infraestructura puede desplegarse de forma controlada
- Los cambios quedan versionados y trazables

### Etapas previstas

1. Checkout del repositorio
2. Validación de plantillas CloudFormation
3. Linting con `cfn-lint`
4. Despliegue del stack en AWS
5. Comprobación del estado del despliegue

```bash
# Validación
aws cloudformation validate-template

# Linting
cfn-lint infra/templates/**/*.yml

# Despliegue
aws cloudformation deploy
```

---

## Gestión de secretos

Este repositorio **no debe contener** credenciales, claves privadas ni contraseñas.

Se utilizarán mecanismos como:

- **GitHub Secrets** para variables necesarias en los workflows.
- **AWS Secrets Manager** para credenciales de base de datos u otros secretos de la aplicación.
- **IAM** para controlar permisos de acceso.

> ⚠️ Nunca se deben subir credenciales al repositorio.

---

## Monitorización y control de costes

Dado que el proyecto se despliega en una cuenta de AWS, se incluirán medidas para controlar el gasto:

- Alarmas de facturación
- Notificaciones por email
- Métricas en CloudWatch
- Logs centralizados de la API
- Posibilidad de detener servicios no utilizados

> Este apartado es especialmente importante al tratarse de un entorno académico y de pruebas.

---

## Documentación

La documentación técnica del proyecto podrá incluir:

- Arquitectura cloud
- Diagramas de infraestructura
- Explicación de las plantillas CloudFormation
- Decisiones técnicas
- Capturas de AWS
- Capturas de GitHub Actions
- Buenas prácticas aplicadas

En fases posteriores se podrá añadir una carpeta `docs/`.

---

## Estado del proyecto

> 🚧 Proyecto en fase inicial de definición de infraestructura.

### Primera fase prevista:

- [ ] Crear estructura base del repositorio.
- [ ] Definir plantillas iniciales de CloudFormation.
- [ ] Añadir validación con GitHub Actions.
- [ ] Crear recursos básicos de monitorización.
- [ ] Desplegar la base de datos PostgreSQL en Amazon RDS.
- [ ] Desplegar la API FastAPI.
- [ ] Conectar la app .NET MAUI con la API desplegada.

---

## Autor

**Jhojahn Sebastian Ramirez Marin**
**Irene Cañada Gomez**
