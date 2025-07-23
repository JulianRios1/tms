# Arquitectura del Sistema - Gestor de Envíos MVP

## 1. Visión General de la Arquitectura

### 1.1 Arquitectura de Alto Nivel
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │    Backend      │    │   Base de       │
│   (React/Vue)   │◄──►│  (Node.js/API)  │◄──►│   Datos         │
│                 │    │                 │    │  (PostgreSQL)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │   Servicios     │
                    │   Externos      │
                    │ (Email, SMS,    │
                    │  Tracking APIs) │
                    └─────────────────┘
```

### 1.2 Patrón de Arquitectura
- **Arquitectura**: Microservicios/Monolito Modular
- **Patrón**: MVC (Model-View-Controller)
- **Comunicación**: RESTful APIs
- **Autenticación**: JWT (JSON Web Tokens)
- **Autorización**: RBAC (Role-Based Access Control)

## 2. Módulos del Sistema

### 2.1 Módulo de Autenticación y Autorización
**Funcionalidades:**
- Registro de usuarios
- Login/Logout
- Gestión de sesiones con JWT
- Middleware de autenticación
- Recuperación de contraseñas

**Endpoints:**
```
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
POST /api/auth/refresh-token
POST /api/auth/forgot-password
POST /api/auth/reset-password
```

### 2.2 Módulo de Gestión de Perfiles y Permisos
**Funcionalidades:**
- CRUD de perfiles de usuario
- Gestión de roles y permisos
- Middleware de autorización
- Asignación de permisos por rutas

**Roles Principales:**
- Super Admin
- Admin
- Operador
- Cliente
- Mensajero

**Endpoints:**
```
GET /api/users/profile
PUT /api/users/profile
GET /api/roles
POST /api/roles
GET /api/permissions
POST /api/users/:id/assign-role
```

### 2.3 Módulo de Gestión de Clientes
**Funcionalidades:**
- CRUD de clientes
- Relación con usuarios del sistema
- Historial de envíos por cliente
- Gestión de direcciones

**Endpoints:**
```
GET /api/customers
POST /api/customers
GET /api/customers/:id
PUT /api/customers/:id
DELETE /api/customers/:id
GET /api/customers/:id/users
POST /api/customers/:id/users
```

### 2.4 Módulos CRUD (Catálogos)
**Incluye:**
- Productos
- Tipos de servicio
- Formas de pago
- Sucursales
- Ciudades

**Endpoints Genéricos:**
```
GET /api/products
POST /api/products
GET /api/service-types
GET /api/payment-methods
GET /api/branches
GET /api/cities
```

### 2.5 Módulo de Órdenes
**Funcionalidades:**
- Creación de órdenes
- Gestión de estados
- Relación con clientes y productos
- Cálculo de costos

**Estados de Orden:**
- Pendiente
- Confirmada
- En Preparación
- Lista para Envío
- Enviada
- Entregada
- Cancelada

**Endpoints:**
```
GET /api/orders
POST /api/orders
GET /api/orders/:id
PUT /api/orders/:id
GET /api/orders/:id/status-history
POST /api/orders/:id/update-status
```

### 2.6 Módulo de Guías (Shipping)
**Funcionalidades:**
- Generación de guías de envío
- Impresión de guías y stickers
- Sistema de tracking
- Gestión de estados de envío

**Estados de Envío:**
- Creada
- En Tránsito
- En Distribución
- Entregada
- Devuelta
- Perdida

**Endpoints:**
```
GET /api/shipments
POST /api/shipments
GET /api/shipments/:id
PUT /api/shipments/:id/status
GET /api/shipments/:id/track
GET /api/shipments/:id/print-guide
GET /api/shipments/:id/print-sticker
```

### 2.7 Módulo de Órdenes de Servicio (Manifests)
**Funcionalidades:**
- Creación de manifiestos
- Asignación de mensajeros
- Agrupación de envíos
- Seguimiento de rutas

**Endpoints:**
```
GET /api/manifests
POST /api/manifests
GET /api/manifests/:id
PUT /api/manifests/:id/assign-courier
GET /api/manifests/:id/shipments
POST /api/manifests/:id/add-shipment
```

## 3. Modelo de Base de Datos

### 3.1 Entidades Principales
- Users
- Roles
- Permissions
- Customers
- Products
- ServiceTypes
- PaymentMethods
- Branches
- Cities
- Orders
- Shipments
- Manifests
- OrderStatusHistory
- ShipmentStatusHistory

### 3.2 Relaciones Clave
- Users ↔ Roles (Many-to-Many)
- Customers ↔ Users (One-to-Many)
- Orders ↔ Customers (Many-to-One)
- Shipments ↔ Orders (One-to-One)
- Manifests ↔ Shipments (One-to-Many)

## 4. Seguridad

### 4.1 Autenticación JWT
- Access Token (15 minutos)
- Refresh Token (7 días)
- Blacklist de tokens revocados

### 4.2 Autorización RBAC
- Middleware de verificación de permisos
- Rutas protegidas por roles
- Permisos granulares por recurso

### 4.3 Validaciones
- Validación de entrada de datos
- Sanitización de inputs
- Rate limiting
- CORS configurado

## 5. Tecnologías

### 5.1 Backend
- **Runtime**: Node.js 18+
- **Framework**: Express.js
- **ORM**: Sequelize/Prisma
- **Validación**: Joi/Yup
- **Testing**: Jest

### 5.2 Frontend
- **Framework**: React 18+ / Vue.js 3+
- **State Management**: Redux/Zustand
- **UI Library**: Material-UI / Tailwind CSS
- **HTTP Client**: Axios

### 5.3 Base de Datos
- **Principal**: PostgreSQL 14+
- **Cache**: Redis
- **Files**: AWS S3 / GCP Storage

### 5.4 DevOps
- **Containerización**: Docker
- **Orquestación**: Docker Compose / Kubernetes
- **CI/CD**: GitHub Actions / GitLab CI
- **Monitoring**: CloudWatch / Stackdriver

## 6. Consideraciones de Escalabilidad

### 6.1 Horizontal Scaling
- Load balancer para múltiples instancias
- Separación de servicios por dominio
- Microservicios independientes

### 6.2 Performance
- Índices de base de datos optimizados
- Cache de consultas frecuentes
- CDN para archivos estáticos
- Lazy loading en frontend

### 6.3 Disponibilidad
- Health checks
- Circuit breakers
- Fallback strategies
- Backup y recovery procedures
