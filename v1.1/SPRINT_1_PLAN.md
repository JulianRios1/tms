# Plan del Sprint 1: El Esqueleto Funcional

Este documento detalla el plan para el primer sprint de desarrollo del TMS, con una duración de 2 semanas y un equipo de 2 desarrolladores junior.

---

## Objetivo Principal del Sprint (Sprint Goal)

**Construir el flujo de creación de envíos de principio a fin.** Al finalizar el sprint, un usuario debe poder iniciar sesión, registrar un cliente, crear un envío para ese cliente y ver el envío creado en una lista. Esto validará que la arquitectura base, la base de datos y los componentes principales del frontend y backend están funcionando juntos.

---

## Historias de Usuario Seleccionadas para el Sprint 1

Se ha seleccionado un subconjunto del backlog que conforma el "camino feliz" del flujo principal.

1.  **HU-01:** Como usuario, quiero poder iniciar sesión con mi correo y contraseña.
2.  **HU-02:** Como usuario, quiero poder cerrar mi sesión.
3.  **HU-06:** Como operador, quiero poder registrar un nuevo cliente con su información.
4.  **HU-07:** Como operador, quiero poder ver una lista de todos los clientes registrados.
5.  **HU-11:** Como operador, quiero poder crear un nuevo envío, asociándolo a un cliente.
6.  **HU-12:** Como operador, quiero poder ver un listado de todos los envíos registrados.
7.  **HU-15:** Como operador, quiero poder ver todos los detalles de un envío específico.

---

## Desglose de Tareas Técnicas por Historia de Usuario

Aquí se detallan las tareas técnicas de alto nivel. El equipo deberá refinarlas en tareas más pequeñas.

### Semana 1: Foco en Backend, Base de Datos y Autenticación

*   **Tareas Generales:**
    *   Configurar el repositorio (Git) y el proyecto base (Node.js/Express o NestJS).
    *   Establecer la conexión a la base de datos (DBaaS).
    *   Configurar la estructura de la aplicación monolítica modular.

*   **HU-01 (Iniciar Sesión):**
    *   **Backend:** Crear la tabla `usuarios` en la base de datos.
    *   **Backend:** Crear el endpoint `POST /api/auth/login` que valida credenciales y genera un JWT.
    *   **Frontend:** Diseñar y construir el componente de la página de inicio de sesión.

*   **HU-06 (Registrar Nuevo Cliente):**
    *   **Backend:** Crear la tabla `clientes` en la base de datos.
    *   **Backend:** Crear el endpoint `POST /api/clientes` para guardar un nuevo cliente.
    *   **Frontend:** Diseñar y construir el formulario para registrar un nuevo cliente.

*   **HU-07 (Ver Lista de Clientes):**
    *   **Backend:** Crear el endpoint `GET /api/clientes` para devolver la lista de clientes.
    *   **Frontend:** Diseñar y construir la página que muestra la lista de clientes en una tabla.

### Semana 2: Foco en el Core (Envíos) e Integración Frontend

*   **HU-11 (Crear Nuevo Envío):**
    *   **Backend:** Crear las tablas `envios` y `trazas` en la base de datos.
    *   **Backend:** Crear el endpoint `POST /api/envios` que recibe los datos del envío, lo guarda y genera el número de guía.
    *   **Frontend:** Diseñar y construir el formulario de "Nuevo Envío", incluyendo la lógica para buscar y seleccionar un cliente existente.

*   **HU-12 (Ver Lista de Envíos):**
    *   **Backend:** Crear el endpoint `GET /api/envios`.
    *   **Frontend:** Diseñar y construir la página que muestra la lista de envíos en una tabla.

*   **HU-15 (Ver Detalles de Envío):**
    *   **Backend:** Crear el endpoint `GET /api/envios/:id` que devuelve la información completa de un envío, incluyendo su historial de trazas.
    *   **Frontend:** Diseñar y construir la página de detalle del envío.

*   **HU-02 (Cerrar Sesión):**
    *   **Frontend:** Implementar el botón de "Cerrar Sesión" que elimina el token local y redirige al login.

---

## ¿Qué se entregará al final del Sprint?

Al finalizar las 2 semanas, el equipo deberá realizar una demostración (Sprint Review) donde se muestre a un usuario realizando las siguientes acciones en la aplicación en vivo:

1.  Acceder a la página de login e iniciar sesión.
2.  Navegar a la sección de "Clientes" y registrar un nuevo cliente.
3.  Verificar que el nuevo cliente aparece en la lista.
4.  Navegar a la sección de "Envíos" y crear un nuevo envío, seleccionando al cliente recién creado.
5.  Verificar que el nuevo envío aparece en la lista de envíos con su número de guía.
6.  Hacer clic en el envío para ver su página de detalles.
7.  Cerrar la sesión y volver a la página de login.
