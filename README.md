# NexaSupply AI

### Plataforma cloud-native de gestión empresarial con microservicios e inteligencia artificial

**Java 21 · Spring Boot 4.1.1 · Angular · PostgreSQL · Docker · Microsoft Azure · Microsoft Foundry**

NexaSupply AI es una plataforma empresarial diseñada para centralizar y optimizar la administración de inventarios, pedidos, proveedores, reportes y asistencia operativa mediante inteligencia artificial.

El sistema implementa una arquitectura de cinco microservicios independientes, desarrollados con Java y Spring Boot, integrados con una aplicación frontend Angular y preparados para ejecutarse en entornos locales y Microsoft Azure.

## 🌐 Aplicación desplegada en Microsoft Azure

### 🚀 Acceder a NexaSupply AI

**[Abrir NexaSupply AI — Aplicación Web](https://ca-nexa-portal-wp.delightfulsky-3ed4c3ce.canadacentral.azurecontainerapps.io/)**

**Proveedor:** Microsoft Azure  
**Servicio:** Azure Container Apps  
**Región:** Canada Central  
**Frontend:** Angular  
**Backend:** Cinco microservicios Java/Spring Boot  
**Base de datos:** PostgreSQL

> ⏳ **Aviso sobre el tiempo de carga**
>
> Al ingresar por primera vez, la aplicación puede tardar un poco en responder.
>
> Esto se debe a que los cinco microservicios del backend pueden necesitar tiempo para iniciar y completar su proceso de preparación, especialmente cuando sus contenedores han estado inactivos.
>
> Durante ese período, algunas funciones podrían no responder inmediatamente.
>
> **Si la aplicación tarda en cargar, espera unos momentos y vuelve a intentarlo.**
>
> Este comportamiento puede producirse durante el arranque de los servicios y no necesariamente indica una falla del sistema.

## 🏗️ Arquitectura del sistema

NexaSupply AI utiliza una arquitectura orientada a microservicios, basada en la separación de responsabilidades, el aislamiento de dominios y la comunicación mediante APIs REST.

```text
                 ┌──────────────────────┐
                 │     Angular Web      │
                 │      Frontend        │
                 └──────────┬───────────┘
                            │
                      REST / HTTPS
                            │
          ┌─────────────────┼──────────────────┐
          │                 │                  │
          ▼                 ▼                  ▼
 ┌────────────────┐ ┌────────────────┐ ┌────────────────┐
 │ Inventory      │ │ Order          │ │ Supplier       │
 │ Service        │ │ Service        │ │ Service        │
 │ Puerto 8081    │ │ Puerto 8082    │ │ Puerto 8083    │
 └────────────────┘ └────────────────┘ └────────────────┘
          ▲                 ▲                  ▲
          │                 │                  │
          └─────────────────┼──────────────────┘
                            │
                  ┌─────────┴──────────┐
                  │ Reporting Service  │
                  │ Puerto 8084        │
                  └─────────▲──────────┘
                            │
                  ┌────────────────────┐
                  │ AI Orchestrator    │
                  │ Puerto 8085        │
                  └─────────┬──────────┘
                            │
                  ┌────────────────────┐
                  │ Microsoft Foundry  │
                  │ Responses API      │
                  └────────────────────┘
```

### Principios arquitectónicos

- **Separación de dominios:** cada microservicio administra su propia responsabilidad funcional.
- **Database per Service:** cada servicio es propietario de su esquema PostgreSQL.
- **Comunicación REST:** los servicios intercambian información mediante DTOs tipados.
- **Identificadores UUID:** utilizados para las referencias entre dominios.
- **Seguridad:** autenticación OAuth2 y validación JWT mediante Microsoft Entra.
- **Trazabilidad:** propagación de `X-Correlation-ID`.
- **Despliegue en contenedores:** Docker y Azure Container Apps.
- **Integración con IA:** orquestación de herramientas mediante Microsoft Foundry Responses API.

## ⚙️ Microservicios

| Microservicio | Puerto | Esquema PostgreSQL | Responsabilidad |
|---|---|---|---|
| `inventory-service` | 8081 | `inventory` | Productos, almacenes, saldos, reservas y movimientos |
| `order-service` | 8082 | `ordering` | Pedidos, líneas, confirmación y cancelación |
| `supplier-service` | 8083 | `supplier` | Proveedores, órdenes de compra y recepciones |
| `reporting-service` | 8084 | `reporting` | Dashboard, snapshots y reportes |
| `ai-orchestrator` | 8085 | `ai` | Conversaciones, herramientas, auditoría e IA |

### Comunicación entre servicios

```text
order-service ─────────► inventory-service

supplier-service ──────► inventory-service

reporting-service ─────► inventory-service
                  ├────► order-service
                  └────► supplier-service

ai-orchestrator ───────► inventory-service
                ├──────► order-service
                ├──────► supplier-service
                ├──────► reporting-service
                └──────► Microsoft Foundry
```

Cada servicio administra exclusivamente sus entidades y operaciones de persistencia.

No se comparten entidades JPA ni se ejecutan consultas SQL directas entre los esquemas de otros dominios.

## 🛠️ Stack tecnológico

| Categoría | Tecnologías |
|---|---|
| Backend | Java 21, Spring Boot 4.1.1 |
| Frontend | Angular, TypeScript |
| Base de datos | PostgreSQL |
| Persistencia | Spring Data JPA, Hibernate |
| APIs | REST, JSON, OpenAPI |
| Seguridad | Spring Security, OAuth2 Resource Server, JWT |
| Identidad | Microsoft Entra ID |
| Inteligencia artificial | Microsoft Foundry Responses API |
| Contenedores | Docker, Docker Compose |
| Cloud | Microsoft Azure, Azure Container Apps |
| Almacenamiento de reportes | Azure Blob Storage |
| Autenticación cloud | Azure Managed Identity |
| Observabilidad | Spring Boot Actuator, logs ECS, Correlation ID |
| Pruebas | Maven, pruebas automatizadas, smoke tests |

## 🗄️ Base de datos

El sistema utiliza PostgreSQL como motor de persistencia.

**Base de datos:** `nexastock`

La estructura contempla cinco esquemas independientes y un total de 22 tablas.

```text
nexastock
├── inventory
├── ordering
├── supplier
├── reporting
└── ai
```

El archivo `nexasupplyai.sql` contiene la estructura SQL del proyecto.

## 💻 Ejecución local

### Requisitos

- Java 21
- Docker y Docker Compose
- Node.js compatible con la versión de Angular del proyecto
- npm
- Git

### 1. Preparar las variables de entorno

```bash
cp .env.example .env
```

Configurar `DB_PASSWORD` dentro del archivo `.env`.

### 2. Iniciar los microservicios

```bash
docker compose up --build
```

Este comando inicia PostgreSQL y los cinco servicios backend definidos en Docker Compose.

### 3. Iniciar el frontend

Abrir una segunda terminal:

```bash
cd nexasupply-frontend
npm ci
npm start
```

Acceder a:

**http://localhost:4200**

### 4. Ejecutar pruebas de integración

```bash
chmod +x scripts/smoke-test.sh
./scripts/smoke-test.sh
```

El script requiere `curl` y `jq`.

Para ejecutar nuevamente el flujo completo sobre una base limpia:

```bash
docker compose down -v
docker compose up --build
```

**Advertencia:** `docker compose down -v` elimina los volúmenes asociados al entorno, incluidos los datos persistidos de PostgreSQL.

## ☁️ Configuración de entornos

El sistema dispone de tres perfiles.

| Perfil | Descripción |
|---|---|
| `local` | Ejecución mediante Docker y PostgreSQL local |
| `azure` | Integración con servicios administrados de Microsoft Azure |
| `test` | Pruebas con H2 y dependencias externas simuladas |

### Perfil local

- Seguridad desactivada.
- Autenticación interservicio `none`.
- Almacenamiento local.
- Microsoft Foundry deshabilitado.
- PostgreSQL expuesto en `localhost:5433`.

### Perfil Azure

- PostgreSQL con autenticación passwordless.
- Microsoft Entra ID.
- Managed Identity.
- Azure Blob Storage.
- Microsoft Foundry.
- Validación JWT.
- Comunicación interservicio configurable.

### Perfil test

- Base de datos H2.
- Dependencias externas simuladas.
- Sin consumo de recursos Azure.

## 🔐 Seguridad

La arquitectura utiliza Spring Security y OAuth2 Resource Server.

Cuando `APP_SECURITY_ENABLED=true`, las APIs verifican:

- `AZURE_ENTRA_ISSUER_URI`
- `AZURE_ENTRA_AUDIENCE`
- Scopes `SCOPE_*`
- Roles `ROLE_*`

### Roles definidos

- `ADMIN`
- `INVENTORY_MANAGER`
- `PURCHASING`
- `SALES`
- `REPORT_VIEWER`
- `AI_USER`

### Autenticación interservicio

Se admiten tres modalidades:

```text
none
propagate
managed-identity
```

En el modo `managed-identity`, debe configurarse `AZURE_INTER_SERVICE_SCOPE`.

## 🤖 Inteligencia artificial

El microservicio `ai-orchestrator` integra Microsoft Foundry Responses API para habilitar capacidades de asistencia operativa.

La configuración incluye:

```text
FOUNDRY_PROJECT_ENDPOINT
FOUNDRY_MODEL
FOUNDRY_MAX_TOOL_ROUNDS
FOUNDRY_MAX_OUTPUT_TOKENS
FOUNDRY_MAX_HISTORY_MESSAGES
FOUNDRY_MAX_HISTORY_CHARACTERS
```

Estas variables permiten controlar las rondas de ejecución de herramientas, el tamaño de las respuestas y el historial utilizado.

Si `FOUNDRY_ENABLED=false`, el microservicio puede iniciar con normalidad, mientras que el endpoint de chat devuelve una respuesta controlada:

```http
HTTP 503 Service Unavailable
```

```text
FOUNDRY_NOT_CONFIGURED
```

## 📊 Observabilidad

Los cinco microservicios incorporan mecanismos para diagnóstico y seguimiento.

| Funcionalidad | Endpoint / Implementación |
|---|---|
| Health Check | `/actuator/health` |
| Métricas | `/actuator/metrics` |
| Información | `/actuator/info` |
| OpenAPI | `/v3/api-docs` |
| Swagger UI | `/swagger-ui.html` |
| Logs estructurados | Formato ECS |
| Trazabilidad | `X-Correlation-ID` |

Los errores REST mantienen una estructura consistente y evitan exponer stack traces en las respuestas.

## 🧪 Compilación y pruebas

Para ejecutar las pruebas de los cinco microservicios:

```bash
for service in \
  inventory-service \
  order-service \
  supplier-service \
  reporting-service \
  ai-orchestrator
do
  (
    cd "$service" &&
    ./mvnw clean test
  )
done
```

## 📁 Estructura del proyecto

```text
NexaSupply-AI/
│
├── inventory-service/
├── order-service/
├── supplier-service/
├── reporting-service/
├── ai-orchestrator/
│
├── nexasupply-frontend/
│
├── docs/
│   └── AZURE_READINESS.md
│
├── scripts/
│   └── smoke-test.sh
│
├── nexasupplyai.sql
├── docker-compose.yml
├── .env.example
└── README.md
```

## 📚 Documentación técnica

- [Inventory Service](./inventory-service/README.md)
- [Order Service](./order-service/README.md)
- [Supplier Service](./supplier-service/README.md)
- [Reporting Service](./reporting-service/README.md)
- [AI Orchestrator](./ai-orchestrator/README.md)
- [Preparación y recursos Azure](./docs/AZURE_READINESS.md)
- [Frontend Angular](./nexasupply-frontend/README.md)

## 📌 Estado del proyecto

NexaSupply AI cuenta con una implementación funcional del frontend y de los cinco microservicios backend.

La aplicación frontend dispone de una dirección pública en Microsoft Azure Container Apps.

El entorno local puede ejecutarse mediante Docker Compose y dispone de pruebas automatizadas y un smoke test de integración.

La disponibilidad de los servicios cloud depende del estado de sus contenedores, las configuraciones del entorno y las dependencias de Azure.

### Componentes no incluidos actualmente

- Plantillas de infraestructura Bicep.
- Pipelines CI/CD.
- Azure Data Factory.
- Azure Virtual Machines.
- Azure App Service.

---

**NexaSupply AI — Arquitectura de microservicios, servicios cloud e inteligencia artificial aplicada a operaciones empresariales.**

**[🌐 Explorar la aplicación en Microsoft Azure](https://ca-nexa-portal-wp.delightfulsky-3ed4c3ce.canadacentral.azurecontainerapps.io/)**