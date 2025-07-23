# Módulos e Historias de Usuario para un MVP de TMS

Este documento define los módulos mínimos viables y un listado de historias de usuario de alto nivel para el desarrollo inicial del Sistema de Gestión de Transporte (TMS).

---

## Módulo 1: Gestión de Autenticación y Usuarios

**Objetivo:** Permitir el acceso seguro al sistema y la gestión de roles básicos.

*   **HU-01:** Como usuario, quiero poder iniciar sesión con mi correo y contraseña para acceder al sistema.
*   **HU-02:** Como usuario, quiero poder cerrar mi sesión para proteger mi cuenta.
*   **HU-03:** Como administrador, quiero poder crear nuevas cuentas de usuario (operadores) para darles acceso.
*   **HU-04:** Como administrador, quiero poder ver una lista de todos los usuarios del sistema.
*   **HU-05:** Como administrador, quiero poder asignar un rol (admin/operador) a un usuario para controlar sus permisos.

---

## Módulo 2: Gestión de Clientes (Mini-CRM)

**Objetivo:** Mantener un registro central de los clientes que solicitan envíos.

*   **HU-06:** Como operador, quiero poder registrar un nuevo cliente con su información de contacto y dirección.
*   **HU-07:** Como operador, quiero poder ver una lista de todos los clientes registrados.
*   **HU-08:** Como operador, quiero poder buscar un cliente por su nombre o número de identificación.
*   **HU-09:** Como operador, quiero poder ver los detalles de un cliente específico.
*   **HU-10:** Como operador, quiero poder editar la información de un cliente existente.

---

## Módulo 3: Gestión de Envíos (El Corazón del MVP)

**Objetivo:** Permitir la creación y el seguimiento del ciclo de vida completo de un envío.

*   **HU-11:** Como operador, quiero poder crear un nuevo envío, asociándolo a un cliente y especificando origen, destino y detalles del paquete.
*   **HU-12:** Como operador, quiero poder ver un listado de todos los envíos registrados en el sistema.
*   **HU-13:** Como operador, quiero poder filtrar el listado de envíos por su estado (ej. Creado, En Tránsito, Entregado).
*   **HU-14:** Como operador, quiero poder buscar un envío por su número de guía.
*   **HU-15:** Como operador, quiero poder ver todos los detalles de un envío específico.
*   **HU-16:** Como operador, quiero poder cambiar manualmente el estado de un envío (ej. de 'En Tránsito' a 'Entregado').
*   **HU-17:** Como operador, quiero poder registrar una nota o trazado simple en la historia del envío.
*   **HU-18:** Como operador, quiero poder asignar un envío a un transportista disponible.
*   **HU-19:** Como operador, quiero poder registrar una prueba de entrega básica (nombre de quien recibe y hora).

---

## Módulo 4: Gestión de Transportistas

**Objetivo:** Mantener un registro de los mensajeros o vehículos que realizan las entregas.

*   **HU-20:** Como administrador, quiero poder registrar un nuevo transportista (mensajero o vehículo) con su información básica.
*   **HU-21:** Como administrador, quiero poder ver una lista de todos los transportistas.
*   **HU-22:** Como administrador, quiero poder editar la información de un transportista.

---

## Módulo 5: Reportería Operacional Básica

**Objetivo:** Proveer visibilidad sobre la operación diaria para la toma de decisiones.

*   **HU-23:** Como administrador, quiero poder ver un dashboard con el número total de envíos creados hoy.
*   **HU-24:** Como administrador, quiero poder ver un conteo de cuántos envíos hay en cada estado (ej. 50 en tránsito, 120 entregados).
*   **HU-25:** Como operador, quiero poder exportar a un archivo CSV el listado de envíos que estoy viendo en pantalla.
