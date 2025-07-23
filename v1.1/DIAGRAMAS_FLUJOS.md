# Diagramas de Flujo y Casos de Uso - Sistema de Gestión de Envíos

## 1. Flujo Principal de Autenticación

### 1.1 Proceso de Login
```
[Usuario] → [Ingresa credenciales] → [Validación Backend] 
    ↓
[JWT Token generado] → [Token enviado al cliente] → [Usuario autenticado]
    ↓
[Verificación de permisos] → [Redirección según rol]
```

### 1.2 Flujo de Autorización
```
[Request del Usuario] → [Middleware Auth] → [Verificar JWT]
    ↓
[Token válido?] → No → [401 Unauthorized]
    ↓ Sí
[Extraer usuario y rol] → [Verificar permisos para ruta]
    ↓
[Permisos válidos?] → No → [403 Forbidden]
    ↓ Sí
[Continuar al controlador]
```

## 2. Flujo de Gestión de Órdenes

### 2.1 Creación de Orden
```
[Cliente/Operador] → [Seleccionar cliente] → [Agregar productos/servicios]
    ↓
[Calcular costos] → [Seleccionar forma de pago] → [Crear orden]
    ↓
[Estado: Pendiente] → [Notificación al cliente] → [Orden creada]
```

### 2.2 Estados de Orden
```
Pendiente → Confirmada → En Preparación → Lista para Envío → Enviada → Entregada
    ↓           ↓              ↓              ↓           ↓         ↓
    └─────→ Cancelada ←────────┴──────────────┴───────────┘         │
                                                                    ↓
                                                               [Orden Completada]
```

## 3. Flujo de Gestión de Envíos (Shipping)

### 3.1 Creación de Guía
```
[Orden Lista] → [Crear guía de envío] → [Generar número de tracking]
    ↓
[Asignar sucursal origen] → [Calcular ruta] → [Estado: Creada]
    ↓
[Imprimir guía/sticker] → [Listo para pickup]
```

### 3.2 Tracking de Envío
```
Creada → En Tránsito → En Distribución → Entregada
  ↓          ↓             ↓              ↓
  └→ Devuelta ←─────────────┴──────────────┘
  └→ Perdida (proceso especial)
```

### 3.3 Proceso de Seguimiento
```
[Cliente consulta tracking] → [Buscar por número] → [Mostrar estados]
    ↓
[Enviar notificaciones] → [SMS/Email] → [Estado actualizado]
```

## 4. Flujo de Manifiestos (Shipping Orders)

### 4.1 Creación de Manifiesto
```
[Operador] → [Seleccionar envíos pendientes] → [Agrupar por zona/ruta]
    ↓
[Asignar mensajero] → [Crear manifiesto] → [Generar hoja de ruta]
    ↓
[Imprimir manifiesto] → [Entregar a mensajero]
```

### 4.2 Gestión de Entregas
```
[Mensajero recibe manifiesto] → [Inicia ruta] → [Actualiza estados por envío]
    ↓
[Entrega exitosa] → [Captura firma/foto] → [Actualiza sistema]
    ↓
[Entrega fallida] → [Registra motivo] → [Reagenda entrega]
```

## 5. Casos de Uso Principales

### 5.1 Caso de Uso: Registro de Usuario
**Actor:** Administrador
**Flujo Principal:**
1. Admin accede al módulo de usuarios
2. Selecciona "Crear nuevo usuario"
3. Ingresa datos del usuario (nombre, email, teléfono)
4. Asigna rol y permisos
5. Sistema genera credenciales temporales
6. Se envía email de bienvenida con credenciales
7. Usuario debe cambiar contraseña en primer login

**Flujos Alternativos:**
- Email ya existe: Sistema muestra error
- Rol no válido: Sistema solicita rol válido

### 5.2 Caso de Uso: Crear Orden de Envío
**Actor:** Operador/Cliente
**Flujo Principal:**
1. Usuario autenticado accede a "Nueva Orden"
2. Selecciona cliente (crear si no existe)
3. Ingresa datos del envío:
   - Dirección origen/destino
   - Tipo de servicio
   - Productos/descripción
   - Peso/dimensiones
4. Sistema calcula costo automáticamente
5. Selecciona forma de pago
6. Confirma orden
7. Sistema genera número de orden
8. Se envía confirmación al cliente

**Precondiciones:**
- Usuario autenticado con permisos
- Cliente válido en sistema
- Tipo de servicio activo

### 5.3 Caso de Uso: Tracking de Envío
**Actor:** Cliente/Operador
**Flujo Principal:**
1. Usuario ingresa número de tracking
2. Sistema busca envío en base de datos
3. Muestra historial completo de estados
4. Muestra ubicación actual (si disponible)
5. Muestra tiempo estimado de entrega
6. Permite suscribirse a notificaciones

**Flujos Alternativos:**
- Número no válido: Muestra mensaje de error
- Envío no encontrado: Solicita verificar número

### 5.4 Caso de Uso: Asignar Mensajero a Manifiesto
**Actor:** Operador
**Flujo Principal:**
1. Operador accede a "Gestión de Manifiestos"
2. Selecciona envíos pendientes por zona
3. Sistema sugiere agrupación óptima
4. Selecciona mensajero disponible
5. Sistema genera ruta optimizada
6. Crea manifiesto con envíos asignados
7. Notifica al mensajero
8. Imprime hoja de ruta y guías

## 6. Diagramas de Secuencia

### 6.1 Secuencia: Autenticación JWT
```
Cliente    Frontend    Backend    Database
  |          |          |          |
  |--login-->|          |          |
  |          |--POST--->|          |
  |          |          |--query-->|
  |          |          |<--user---|
  |          |          |          |
  |          |<--JWT----|          |
  |<--token--|          |          |
  |          |          |          |
```

### 6.2 Secuencia: Creación de Orden
```
Operador   Frontend   Backend   Database   EmailService
   |         |         |         |            |
   |--crear->|         |         |            |
   |         |--POST-->|         |            |
   |         |         |--save-->|            |
   |         |         |<--id----|            |
   |         |         |--notify------------>|
   |         |<--orden-|         |            |
   |<--conf--|         |         |            |
```

## 7. Diagramas de Estados

### 7.1 Estados de Usuario
```
[Creado] → [Activo] ⇄ [Suspendido]
    ↓         ↓           ↓
    └→ [Eliminado] ←──────┘
```

### 7.2 Estados de Orden
```
[Pendiente] → [Confirmada] → [En Preparación] → [Lista] → [Enviada] → [Entregada]
      ↓            ↓              ↓             ↓          ↓
      └─────→ [Cancelada] ←────────┴─────────────┴──────────┘
```

### 7.3 Estados de Envío
```
[Creada] → [Pickup] → [En Tránsito] → [En Distribución] → [Entregada]
    ↓         ↓           ↓               ↓
    └→ [Cancelada] ←──────┴───────────────┘
    └→ [Devuelta] ←──────────────────────┘
    └→ [Perdida] (requiere investigación)
```

## 8. Matriz de Permisos por Rol

### 8.1 Permisos del Sistema
```
Recurso/Acción    | Super | Admin | Operador | Cliente | Mensajero
                  | Admin |       |          |         |
------------------|-------|-------|----------|---------|----------
Users (Create)    |   ✓   |   ✓   |    ✗     |    ✗    |    ✗
Users (Read)      |   ✓   |   ✓   |    ✓     |    ✗    |    ✗
Users (Update)    |   ✓   |   ✓   |    ✗     |    ✗    |    ✗
Users (Delete)    |   ✓   |   ✗   |    ✗     |    ✗    |    ✗
Orders (Create)   |   ✓   |   ✓   |    ✓     |    ✓    |    ✗
Orders (Read)     |   ✓   |   ✓   |    ✓     |    ✓*   |    ✗
Orders (Update)   |   ✓   |   ✓   |    ✓     |    ✗    |    ✗
Shipments (Read)  |   ✓   |   ✓   |    ✓     |    ✓*   |    ✓*
Shipments (Update)|   ✓   |   ✓   |    ✓     |    ✗    |    ✓*
Manifests (Create)|   ✓   |   ✓   |    ✓     |    ✗    |    ✗
Manifests (Read)  |   ✓   |   ✓   |    ✓     |    ✗    |    ✓*
Reports (Read)    |   ✓   |   ✓   |    ✓     |    ✗    |    ✗
Settings (Manage) |   ✓   |   ✓   |    ✗     |    ✗    |    ✗

* Solo sus propios registros
```

## 9. Flujos de Error y Excepciones

### 9.1 Manejo de Errores de Autenticación
```
[Token expirado] → [Intentar refresh] → [Éxito] → [Continuar]
                                    → [Fallo] → [Logout] → [Login]
```

### 9.2 Manejo de Errores de Orden
```
[Error en pago] → [Notificar usuario] → [Marcar orden pendiente pago]
[Error de stock] → [Notificar operador] → [Actualizar disponibilidad]
[Error de dirección] → [Validar con cliente] → [Corregir datos]
```

### 9.3 Manejo de Errores de Envío
```
[Envío perdido] → [Crear ticket] → [Investigar] → [Resolver]
[Dirección incorrecta] → [Contactar cliente] → [Actualizar] → [Reenviar]
[Mensajero no disponible] → [Reasignar] → [Notificar cliente]
```

## 10. Integraciones Externas

### 10.1 Flujo de Integración con APIs de Tracking
```
[Actualización interna] → [Webhook a API externa] → [Confirmación]
[Consulta externa] → [API Gateway] → [Respuesta JSON] → [Update UI]
```

### 10.2 Flujo de Notificaciones
```
[Evento del sistema] → [Queue de notificaciones] → [Procesamiento]
    ↓
[Email Service] → [Envío email]
[SMS Service] → [Envío SMS]
[Push Service] → [Notificación push]
```

### 10.3 Flujo de Pagos (Futuro)
```
[Orden creada] → [Gateway de pago] → [Procesamiento] → [Confirmación]
    ↓
[Pago exitoso] → [Actualizar orden] → [Proceder con envío]
[Pago fallido] → [Notificar cliente] → [Orden pendiente]
```
