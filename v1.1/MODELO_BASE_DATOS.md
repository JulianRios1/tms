# Modelo de Base de Datos - Sistema de Gestión de Envíos

## 1. Diagrama Entidad-Relación (ERD)

### 1.1 Entidades Principales
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    Users    │    │    Roles    │    │ Permissions │
│             │    │             │    │             │
│ - id (PK)   │    │ - id (PK)   │    │ - id (PK)   │
│ - email     │    │ - name      │    │ - name      │
│ - password  │    │ - desc      │    │ - resource  │
│ - name      │    │ - active    │    │ - action    │
│ - phone     │    │             │    │             │
│ - active    │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
                    ┌─────────────┐
                    │ UserRoles   │
                    │             │
                    │ - user_id   │
                    │ - role_id   │
                    └─────────────┘
```

## 2. Esquemas de Tablas

### 2.1 Tabla: users
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    active BOOLEAN DEFAULT true,
    email_verified_at TIMESTAMP,
    last_login_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_active ON users(active);
```

### 2.2 Tabla: roles
```sql
CREATE TABLE roles (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO roles (name, description) VALUES
('super_admin', 'Super Administrador del Sistema'),
('admin', 'Administrador'),
('operator', 'Operador'),
('customer', 'Cliente'),
('courier', 'Mensajero');
```

### 2.3 Tabla: permissions
```sql
CREATE TABLE permissions (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL,
    resource VARCHAR(50) NOT NULL,
    action VARCHAR(50) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO permissions (name, resource, action, description) VALUES
('users.create', 'users', 'create', 'Crear usuarios'),
('users.read', 'users', 'read', 'Ver usuarios'),
('users.update', 'users', 'update', 'Actualizar usuarios'),
('users.delete', 'users', 'delete', 'Eliminar usuarios'),
('orders.create', 'orders', 'create', 'Crear órdenes'),
('orders.read', 'orders', 'read', 'Ver órdenes'),
('orders.update', 'orders', 'update', 'Actualizar órdenes'),
('shipments.create', 'shipments', 'create', 'Crear envíos'),
('shipments.read', 'shipments', 'read', 'Ver envíos'),
('shipments.update', 'shipments', 'update', 'Actualizar envíos');
```

### 2.4 Tablas de Relación
```sql
CREATE TABLE user_roles (
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    role_id INTEGER REFERENCES roles(id) ON DELETE CASCADE,
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    assigned_by INTEGER REFERENCES users(id),
    PRIMARY KEY (user_id, role_id)
);

CREATE TABLE role_permissions (
    role_id INTEGER REFERENCES roles(id) ON DELETE CASCADE,
    permission_id INTEGER REFERENCES permissions(id) ON DELETE CASCADE,
    PRIMARY KEY (role_id, permission_id)
);
```

### 2.5 Tabla: customers
```sql
CREATE TABLE customers (
    id SERIAL PRIMARY KEY,
    company_name VARCHAR(200),
    contact_name VARCHAR(200) NOT NULL,
    email VARCHAR(255),
    phone VARCHAR(20),
    tax_id VARCHAR(50),
    address TEXT,
    city_id INTEGER REFERENCES cities(id),
    active BOOLEAN DEFAULT true,
    created_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_customers_email ON customers(email);
CREATE INDEX idx_customers_active ON customers(active);
CREATE INDEX idx_customers_city ON customers(city_id);
```

### 2.6 Tabla: customer_users
```sql
CREATE TABLE customer_users (
    customer_id INTEGER REFERENCES customers(id) ON DELETE CASCADE,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    is_primary BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (customer_id, user_id)
);
```

### 2.7 Tablas de Catálogos
```sql
-- Ciudades
CREATE TABLE cities (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    state VARCHAR(100),
    country VARCHAR(100) DEFAULT 'Colombia',
    postal_code VARCHAR(20),
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Sucursales
CREATE TABLE branches (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    code VARCHAR(20) UNIQUE,
    address TEXT NOT NULL,
    city_id INTEGER REFERENCES cities(id),
    phone VARCHAR(20),
    email VARCHAR(255),
    manager_id INTEGER REFERENCES users(id),
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tipos de Servicio
CREATE TABLE service_types (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    base_price DECIMAL(10,2),
    price_per_kg DECIMAL(10,2),
    max_weight DECIMAL(8,2),
    estimated_days INTEGER,
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Formas de Pago
CREATE TABLE payment_methods (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    description TEXT,
    requires_approval BOOLEAN DEFAULT false,
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Productos
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    category VARCHAR(100),
    price DECIMAL(10,2),
    weight DECIMAL(8,2),
    dimensions VARCHAR(50),
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 2.8 Tabla: orders
```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    order_number VARCHAR(50) UNIQUE NOT NULL,
    customer_id INTEGER REFERENCES customers(id) NOT NULL,
    service_type_id INTEGER REFERENCES service_types(id) NOT NULL,
    payment_method_id INTEGER REFERENCES payment_methods(id) NOT NULL,
    
    -- Direcciones
    origin_address TEXT NOT NULL,
    origin_city_id INTEGER REFERENCES cities(id),
    origin_branch_id INTEGER REFERENCES branches(id),
    destination_address TEXT NOT NULL,
    destination_city_id INTEGER REFERENCES cities(id) NOT NULL,
    
    -- Detalles del envío
    recipient_name VARCHAR(200) NOT NULL,
    recipient_phone VARCHAR(20),
    weight DECIMAL(8,2),
    dimensions VARCHAR(50),
    declared_value DECIMAL(10,2),
    
    -- Costos
    base_cost DECIMAL(10,2),
    weight_cost DECIMAL(10,2),
    additional_costs DECIMAL(10,2),
    tax_amount DECIMAL(10,2),
    total_amount DECIMAL(10,2) NOT NULL,
    
    -- Estados y fechas
    status VARCHAR(50) DEFAULT 'pending',
    notes TEXT,
    special_instructions TEXT,
    
    created_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_orders_number ON orders(order_number);
CREATE INDEX idx_orders_customer ON orders(customer_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at);
```

### 2.9 Tabla: order_items
```sql
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id) ON DELETE CASCADE,
    product_id INTEGER REFERENCES products(id),
    description TEXT NOT NULL,
    quantity INTEGER DEFAULT 1,
    unit_price DECIMAL(10,2),
    total_price DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 2.10 Tabla: shipments
```sql
CREATE TABLE shipments (
    id SERIAL PRIMARY KEY,
    tracking_number VARCHAR(50) UNIQUE NOT NULL,
    order_id INTEGER REFERENCES orders(id) NOT NULL,
    
    -- Detalles del envío
    weight DECIMAL(8,2),
    dimensions VARCHAR(50),
    package_type VARCHAR(50),
    
    -- Rutas y fechas
    pickup_date DATE,
    estimated_delivery DATE,
    actual_delivery TIMESTAMP,
    
    -- Estado actual
    status VARCHAR(50) DEFAULT 'created',
    current_location TEXT,
    
    -- Asignaciones
    assigned_courier_id INTEGER REFERENCES users(id),
    origin_branch_id INTEGER REFERENCES branches(id),
    destination_branch_id INTEGER REFERENCES branches(id),
    
    -- Metadata
    notes TEXT,
    created_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_shipments_tracking ON shipments(tracking_number);
CREATE INDEX idx_shipments_order ON shipments(order_id);
CREATE INDEX idx_shipments_status ON shipments(status);
CREATE INDEX idx_shipments_courier ON shipments(assigned_courier_id);
```

### 2.11 Tabla: manifests
```sql
CREATE TABLE manifests (
    id SERIAL PRIMARY KEY,
    manifest_number VARCHAR(50) UNIQUE NOT NULL,
    courier_id INTEGER REFERENCES users(id) NOT NULL,
    branch_id INTEGER REFERENCES branches(id),
    
    -- Detalles de la ruta
    route_description TEXT,
    estimated_duration INTEGER, -- en minutos
    total_packages INTEGER,
    
    -- Estados y fechas
    status VARCHAR(50) DEFAULT 'created',
    created_date DATE DEFAULT CURRENT_DATE,
    start_time TIMESTAMP,
    end_time TIMESTAMP,
    
    notes TEXT,
    created_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_manifests_number ON manifests(manifest_number);
CREATE INDEX idx_manifests_courier ON manifests(courier_id);
CREATE INDEX idx_manifests_status ON manifests(status);
CREATE INDEX idx_manifests_date ON manifests(created_date);
```

### 2.12 Tabla: manifest_shipments
```sql
CREATE TABLE manifest_shipments (
    manifest_id INTEGER REFERENCES manifests(id) ON DELETE CASCADE,
    shipment_id INTEGER REFERENCES shipments(id) ON DELETE CASCADE,
    sequence_order INTEGER,
    estimated_time TIME,
    actual_delivery_time TIMESTAMP,
    delivery_status VARCHAR(50),
    delivery_notes TEXT,
    recipient_signature TEXT, -- Base64 de imagen de firma
    proof_photo TEXT, -- URL o Base64 de foto de entrega
    PRIMARY KEY (manifest_id, shipment_id)
);
```

### 2.13 Tablas de Historial
```sql
-- Historial de estados de orden
CREATE TABLE order_status_history (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id) ON DELETE CASCADE,
    previous_status VARCHAR(50),
    new_status VARCHAR(50) NOT NULL,
    reason TEXT,
    changed_by INTEGER REFERENCES users(id),
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Historial de estados de envío
CREATE TABLE shipment_status_history (
    id SERIAL PRIMARY KEY,
    shipment_id INTEGER REFERENCES shipments(id) ON DELETE CASCADE,
    previous_status VARCHAR(50),
    new_status VARCHAR(50) NOT NULL,
    location TEXT,
    reason TEXT,
    changed_by INTEGER REFERENCES users(id),
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_order_history_order ON order_status_history(order_id);
CREATE INDEX idx_order_history_date ON order_status_history(changed_at);
CREATE INDEX idx_shipment_history_shipment ON shipment_status_history(shipment_id);
CREATE INDEX idx_shipment_history_date ON shipment_status_history(changed_at);
```

### 2.14 Tablas de Sistema
```sql
-- Tokens de autenticación
CREATE TABLE auth_tokens (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    token_hash VARCHAR(255) NOT NULL,
    token_type VARCHAR(50) DEFAULT 'refresh',
    expires_at TIMESTAMP NOT NULL,
    revoked BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Lista negra de tokens JWT
CREATE TABLE token_blacklist (
    id SERIAL PRIMARY KEY,
    jti VARCHAR(255) UNIQUE NOT NULL, -- JWT ID
    user_id INTEGER REFERENCES users(id),
    revoked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL
);

-- Configuraciones del sistema
CREATE TABLE system_settings (
    id SERIAL PRIMARY KEY,
    key VARCHAR(100) UNIQUE NOT NULL,
    value TEXT,
    description TEXT,
    category VARCHAR(50),
    updated_by INTEGER REFERENCES users(id),
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Notificaciones
CREATE TABLE notifications (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    type VARCHAR(50) NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT,
    data JSONB,
    read_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_notifications_user ON notifications(user_id);
CREATE INDEX idx_notifications_read ON notifications(read_at);
```

## 3. Triggers y Funciones

### 3.1 Trigger para actualizar timestamps
```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Aplicar a todas las tablas con updated_at
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_customers_updated_at BEFORE UPDATE ON customers
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_orders_updated_at BEFORE UPDATE ON orders
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_shipments_updated_at BEFORE UPDATE ON shipments
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

### 3.2 Trigger para generar números automáticos
```sql
CREATE OR REPLACE FUNCTION generate_order_number()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.order_number IS NULL THEN
        NEW.order_number := 'ORD-' || TO_CHAR(CURRENT_DATE, 'YYYYMMDD') || '-' || 
                           LPAD(nextval('order_number_seq')::text, 4, '0');
    END IF;
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE SEQUENCE order_number_seq START 1;

CREATE TRIGGER generate_order_number_trigger BEFORE INSERT ON orders
    FOR EACH ROW EXECUTE FUNCTION generate_order_number();
```

### 3.3 Trigger para tracking numbers
```sql
CREATE OR REPLACE FUNCTION generate_tracking_number()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.tracking_number IS NULL THEN
        NEW.tracking_number := 'TRK' || TO_CHAR(CURRENT_TIMESTAMP, 'YYYYMMDDHH24MISS') || 
                              LPAD((RANDOM() * 9999)::int::text, 4, '0');
    END IF;
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER generate_tracking_number_trigger BEFORE INSERT ON shipments
    FOR EACH ROW EXECUTE FUNCTION generate_tracking_number();
```

### 3.4 Trigger para historial de estados
```sql
CREATE OR REPLACE FUNCTION log_order_status_change()
RETURNS TRIGGER AS $$
BEGIN
    IF OLD.status IS DISTINCT FROM NEW.status THEN
        INSERT INTO order_status_history (order_id, previous_status, new_status, changed_by)
        VALUES (NEW.id, OLD.status, NEW.status, NEW.updated_by);
    END IF;
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER log_order_status_trigger AFTER UPDATE OF status ON orders
    FOR EACH ROW EXECUTE FUNCTION log_order_status_change();
```

## 4. Vistas Útiles

### 4.1 Vista de órdenes con detalles
```sql
CREATE VIEW order_details AS
SELECT 
    o.id,
    o.order_number,
    o.status,
    c.company_name,
    c.contact_name,
    st.name as service_type,
    pm.name as payment_method,
    o.total_amount,
    o.created_at,
    COALESCE(u.first_name || ' ' || u.last_name, 'Sistema') as created_by_name
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.id
LEFT JOIN service_types st ON o.service_type_id = st.id
LEFT JOIN payment_methods pm ON o.payment_method_id = pm.id
LEFT JOIN users u ON o.created_by = u.id;
```

### 4.2 Vista de envíos con tracking
```sql
CREATE VIEW shipment_tracking AS
SELECT 
    s.id,
    s.tracking_number,
    s.status,
    o.order_number,
    c.contact_name as customer_name,
    s.current_location,
    s.estimated_delivery,
    COALESCE(courier.first_name || ' ' || courier.last_name, 'No asignado') as courier_name
FROM shipments s
LEFT JOIN orders o ON s.order_id = o.id
LEFT JOIN customers c ON o.customer_id = c.id
LEFT JOIN users courier ON s.assigned_courier_id = courier.id;
```

### 4.3 Vista de manifiestos activos
```sql
CREATE VIEW active_manifests AS
SELECT 
    m.id,
    m.manifest_number,
    m.status,
    courier.first_name || ' ' || courier.last_name as courier_name,
    b.name as branch_name,
    COUNT(ms.shipment_id) as total_shipments,
    m.created_date
FROM manifests m
LEFT JOIN users courier ON m.courier_id = courier.id
LEFT JOIN branches b ON m.branch_id = b.id
LEFT JOIN manifest_shipments ms ON m.id = ms.manifest_id
WHERE m.status != 'completed'
GROUP BY m.id, courier.first_name, courier.last_name, b.name;
```

## 5. Índices de Performance

### 5.1 Índices compuestos importantes
```sql
-- Para búsquedas de órdenes por cliente y fecha
CREATE INDEX idx_orders_customer_date ON orders(customer_id, created_at DESC);

-- Para búsquedas de envíos por estado y fecha
CREATE INDEX idx_shipments_status_date ON shipments(status, created_at DESC);

-- Para el dashboard de mensajeros
CREATE INDEX idx_manifests_courier_status ON manifests(courier_id, status);

-- Para reportes de envíos por sucursal
CREATE INDEX idx_shipments_branch_date ON shipments(origin_branch_id, created_at DESC);
```

### 5.2 Índices para búsquedas de texto
```sql
-- Para búsqueda de clientes
CREATE INDEX idx_customers_search ON customers USING gin(to_tsvector('spanish', 
    coalesce(company_name, '') || ' ' || coalesce(contact_name, '') || ' ' || coalesce(email, '')));

-- Para búsqueda de órdenes
CREATE INDEX idx_orders_search ON orders USING gin(to_tsvector('spanish',
    order_number || ' ' || coalesce(recipient_name, '') || ' ' || coalesce(destination_address, '')));
```

## 6. Políticas de Seguridad Row Level Security (RLS)

### 6.1 Política para usuarios
```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- Los clientes solo pueden ver sus propias órdenes
CREATE POLICY orders_customer_policy ON orders
    FOR SELECT TO customer_role
    USING (customer_id IN (
        SELECT customer_id FROM customer_users 
        WHERE user_id = current_user_id()
    ));

-- Los operadores pueden ver todas las órdenes
CREATE POLICY orders_operator_policy ON orders
    FOR ALL TO operator_role, admin_role
    USING (true);
```

## 7. Mantenimiento y Optimización

### 7.1 Particionado de tablas históricas
```sql
-- Particionar tabla de historial por fecha
CREATE TABLE order_status_history_template (
    LIKE order_status_history INCLUDING ALL
) PARTITION BY RANGE (changed_at);

-- Crear particiones mensuales
CREATE TABLE order_status_history_2025_01 
    PARTITION OF order_status_history_template
    FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');
```

### 7.2 Archivado automático
```sql
-- Función para archivar órdenes antiguas
CREATE OR REPLACE FUNCTION archive_old_orders()
RETURNS void AS $$
BEGIN
    -- Mover órdenes completadas de más de 2 años a tabla histórica
    INSERT INTO orders_archive 
    SELECT * FROM orders 
    WHERE status = 'completed' 
    AND created_at < CURRENT_DATE - INTERVAL '2 years';
    
    DELETE FROM orders 
    WHERE status = 'completed' 
    AND created_at < CURRENT_DATE - INTERVAL '2 years';
END;
$$ LANGUAGE plpgsql;
```

## 8. Backup y Recovery

### 8.1 Estrategia de respaldos
```sql
-- Script de backup diario
pg_dump --format=custom --no-owner --no-privileges gestorenvio > backup_$(date +%Y%m%d).backup

-- Backup incremental con WAL
archive_command = 'cp %p /backup/wal/%f'
```

### 8.2 Verificación de integridad
```sql
-- Función para verificar integridad referencial
CREATE OR REPLACE FUNCTION check_data_integrity()
RETURNS TABLE(table_name text, issue_count bigint) AS $$
BEGIN
    -- Verificar órdenes sin cliente
    RETURN QUERY
    SELECT 'orders_without_customer'::text, count(*)
    FROM orders o
    LEFT JOIN customers c ON o.customer_id = c.id
    WHERE c.id IS NULL;
    
    -- Verificar envíos sin orden
    RETURN QUERY
    SELECT 'shipments_without_order'::text, count(*)
    FROM shipments s
    LEFT JOIN orders o ON s.order_id = o.id
    WHERE o.id IS NULL;
END;
$$ LANGUAGE plpgsql;
```
