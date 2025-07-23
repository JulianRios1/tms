# Propuesta 2: Arquitectura Orientada a Servicios (El Crecimiento Ordenado)

**Filosofía:** Cuando el monolito modular comience a mostrar sus limitaciones (ej. cuellos de botella en el rendimiento, equipos que se estorban), es hora de dividirlo. Esta arquitectura se enfoca en separar los dominios de negocio más críticos en servicios independientes, sin llegar a la granularidad extrema de los microservicios.

**Basado en:** `ARQUITECTURA_DESARROLLO_V3.md` (versión simplificada)

---

## 1. Diagrama de Arquitectura

```
┌──────────────┐         ┌────────────────┐
│  Navegador   ├- (API) -►│  API Gateway   ├- (API) -┐
│ (React/Vue)  │         └────────────────┘         │
└──────────────┘                                      │
   ▲                                                  │
   │                                                  ▼
┌──┴───────────┐      ┌───────────────┐      ┌────────┴───────┐
│ Servicio de  │      │ Servicio de   │      │  Servicio de   │
│  Usuarios &  │      │   Órdenes &   │      │   Tracking &   │
│    Auth      │      │ Facturación   │      │  Geolocalización│
│ (Node.js)    │◄──┬──►│  (Node.js)    │◄──┬──►│   (Node.js)    │
└──────┬───────┘  │   └──────┬────────┘  │   └──────┬───────┘
       │ (DB)     │          │ (DB)      │          │ (DB)
┌──────▼───────┐  │   ┌──────▼────────┐  │   ┌──────▼───────┐
│ DB Usuarios  │  │   │  DB Órdenes   │  │   │  DB Tracking   │
│ (PostgreSQL) │  │   │ (PostgreSQL)  │  │   │ (PostgreSQL) │
└──────────────┘  │   └───────────────┘  │   └──────────────┘
                  │                      │
                  └────── (Eventos) ─────┘
                  ┌──────────────────────┐
                  │    Bus de Eventos    │
                  │ (RabbitMQ / Redis)   │
                  └──────────────────────┘
```

## 2. Características Clave

*   **Múltiples Servicios:** La aplicación se divide en 3-5 servicios principales, cada uno con su propio repositorio y ciclo de vida de despliegue.
*   **API Gateway:** Un único punto de entrada para todas las peticiones del frontend. Enruta las peticiones al servicio correspondiente. Esto simplifica la gestión de la autenticación, el rate limiting y el logging.
*   **Comunicación Síncrona y Asíncrona:**
    *   **Síncrona (API):** Para peticiones que necesitan una respuesta inmediata (ej. el servicio de órdenes consulta al servicio de usuarios para validar un cliente).
    *   **Asíncrona (Bus de Eventos):** Para notificar a otros servicios de que algo ha ocurrido sin esperar una respuesta (ej. el servicio de órdenes emite un evento `orden_creada` y el servicio de tracking reacciona para empezar el seguimiento).
*   **Base de Datos por Servicio:** Cada servicio es dueño exclusivo de sus datos. Ningún servicio puede acceder directamente a la base de datos de otro.

## 3. Pila Tecnológica Recomendada

*   **Orquestación de Contenedores:** Kubernetes o Docker Swarm para gestionar los despliegues de los diferentes servicios.
*   **API Gateway:** Kong, Traefik, o el propio de tu proveedor cloud (ej. AWS API Gateway).
*   **Bus de Eventos:** RabbitMQ (robusto y maduro) o Redis Streams (si ya usas Redis).
*   **Observabilidad:** Es crucial introducir herramientas de tracing distribuido (como Jaeger u OpenTelemetry) para seguir una petición a través de varios servicios.

## 4. Ventajas

*   **Escalabilidad Selectiva:** Puedes escalar independientemente los servicios que más carga reciben.
*   **Equipos Autónomos:** Diferentes equipos pueden trabajar en paralelo en distintos servicios con menos fricción.
*   **Flexibilidad Tecnológica Parcial:** Un nuevo servicio podría escribirse en un lenguaje diferente si hay una buena razón para ello.
*   **Resiliencia Mejorada:** Un fallo en el servicio de tracking no debería impedir que se puedan crear nuevas órdenes.

## 5. Desventajas

*   **Complejidad Operacional Aumentada:** Ahora hay que gestionar múltiples despliegues, redes entre servicios, y un sistema de eventos.
*   **Consistencia de Datos:** Mantener la consistencia de los datos entre diferentes bases de datos es un desafío complejo (se usan patrones como Sagas).
*   **Latencia de Red:** Las llamadas entre servicios son más lentas que las llamadas internas de un monolito.
*   **Requiere Más Experiencia:** El equipo necesita entender los patrones de los sistemas distribuidos.

## 6. Conclusión

Esta es la arquitectura a la que se debería migrar **cuando el monolito modular ya no sea suficiente**. Es el paso natural antes de saltar a la complejidad de la "Arquitectura 3". Permite un crecimiento controlado y aborda los principales problemas de escalabilidad y organización de equipos de un monolito grande.
