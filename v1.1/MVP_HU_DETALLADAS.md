# Historias de Usuario Detalladas para el MVP de TMS

Este documento profundiza en las historias de usuario del MVP, añadiendo Criterios de Aceptación (AC) para cada una, con el fin de guiar el desarrollo y las pruebas.

---

## Módulo 1: Gestión de Autenticación y Usuarios

*   **HU-01: Iniciar Sesión**
    *   AC-1: La página de login debe mostrar campos para correo electrónico y contraseña.
    *   AC-2: Debe existir un botón "Iniciar Sesión".
    *   AC-3: Si las credenciales son incorrectas, se debe mostrar un mensaje de error claro.
    *   AC-4: Si las credenciales son correctas, el usuario debe ser redirigido al dashboard principal.

*   **HU-02: Cerrar Sesión**
    *   AC-1: Debe haber un botón o enlace visible para "Cerrar Sesión" una vez que el usuario ha iniciado sesión.
    *   AC-2: Al hacer clic, la sesión del usuario debe invalidarse en el servidor.
    *   AC-3: El usuario debe ser redirigido a la página de inicio de sesión.

*   **HU-03: Crear Nuevos Usuarios**
    *   AC-1: Debe existir un formulario para crear usuarios con campos para nombre, correo, contraseña y rol.
    *   AC-2: Al guardar, se debe validar que el correo no esté ya registrado.
    *   AC-3: El nuevo usuario debe aparecer en la lista de usuarios.

*   **HU-04: Ver Lista de Usuarios**
    *   AC-1: Debe existir una página que muestre una tabla con todos los usuarios registrados.
    *   AC-2: La tabla debe mostrar al menos el nombre, correo y rol de cada usuario.

*   **HU-05: Asignar Rol a Usuario**
    *   AC-1: En la vista de edición de un usuario, debe haber un selector para cambiar su rol (Admin / Operador).
    *   AC-2: Al guardar, los permisos del usuario deben actualizarse de acuerdo al nuevo rol.

---

## Módulo 2: Gestión de Clientes (Mini-CRM)

*   **HU-06: Registrar Nuevo Cliente**
    *   AC-1: Debe existir un formulario con campos para nombre del cliente, ID/NIT, teléfono, correo y dirección principal.
    *   AC-2: Al guardar, el cliente debe aparecer en la lista general de clientes.

*   **HU-07: Ver Lista de Clientes**
    *   AC-1: Debe existir una página que muestre una tabla con todos los clientes.
    *   AC-2: La tabla debe mostrar al menos nombre, ID/NIT y teléfono.

*   **HU-08: Buscar Cliente**
    *   AC-1: En la página de listado de clientes, debe haber un campo de búsqueda.
    *   AC-2: Se debe poder filtrar la lista de clientes en tiempo real al escribir el nombre o ID/NIT.

*   **HU-09: Ver Detalles de Cliente**
    *   AC-1: Al hacer clic en un cliente de la lista, se debe redirigir a una página con su información completa.
    *   AC-2: Esta página debe mostrar también un historial de los envíos asociados a ese cliente.

*   **HU-10: Editar Cliente**
    *   AC-1: En la página de detalle del cliente, debe haber un botón para "Editar".
    *   AC-2: Al hacer clic, se debe mostrar un formulario con los datos del cliente precargados y listos para ser modificados.

---

## Módulo 3: Gestión de Envíos (Core)

*   **HU-11: Crear Nuevo Envío**
    *   AC-1: Debe existir un formulario para "Nuevo Envío".
    *   AC-2: El formulario debe tener un campo de búsqueda para seleccionar un cliente existente.
    *   AC-3: Al seleccionar un cliente, sus datos deben autocompletar la sección del remitente.
    *   AC-4: Debe haber campos para la información del destinatario (nombre, dirección, teléfono).
    *   AC-5: Al guardar, el sistema debe generar un número de guía único y visible.
    *   AC-6: El envío debe guardarse con el estado inicial "Creado".

*   **HU-12: Ver Lista de Envíos**
    *   AC-1: Debe existir una página que muestre una tabla con todos los envíos.
    *   AC-2: La tabla debe mostrar número de guía, cliente, destino, estado y fecha.

*   **HU-13: Filtrar Envíos por Estado**
    *   AC-1: En la lista de envíos, debe haber filtros (botones o un selector) para ver los envíos por su estado.
    *   AC-2: La lista debe actualizarse para mostrar solo los envíos que coinciden con el estado seleccionado.

*   **HU-14: Buscar Envío por Guía**
    *   AC-1: En la lista de envíos, debe haber un campo para buscar por número de guía.
    *   AC-2: La búsqueda debe llevar directamente a los detalles de ese envío si se encuentra.

*   **HU-15: Ver Detalles de Envío**
    *   AC-1: La página de detalle debe mostrar toda la información del envío (cliente, origen, destino, paquete).
    *   AC-2: Debe mostrar un historial cronológico de los cambios de estado y las notas registradas.

*   **HU-16: Cambiar Estado de Envío**
    *   AC-1: En la página de detalle, debe haber un selector con los posibles estados a los que puede pasar el envío.
    *   AC-2: Al cambiar el estado, se debe registrar automáticamente una nueva entrada en el historial del envío.

*   **HU-17: Registrar Nota en Envío**
    *   AC-1: En la página de detalle, debe haber un campo de texto y un botón para "Añadir Nota".
    *   AC-2: La nota guardada debe aparecer en el historial del envío, junto con la fecha y el usuario que la creó.

*   **HU-18: Asignar Transportista a Envío**
    *   AC-1: En la página de detalle, debe haber un selector para asignar un transportista de la lista de disponibles.
    *   AC-2: Una vez asignado, el nombre del transportista debe ser visible en los detalles del envío.

*   **HU-19: Registrar Prueba de Entrega**
    *   AC-1: Al cambiar el estado de un envío a "Entregado", deben aparecer campos para registrar el nombre y la cédula de quien recibe.
    *   AC-2: Esta información debe guardarse y ser visible en los detalles del envío.

---

## Módulo 4: Gestión de Transportistas

*   **HU-20: Registrar Nuevo Transportista**
    *   AC-1: Debe existir un formulario para registrar transportistas con campos para nombre, cédula, y placa del vehículo (si aplica).

*   **HU-21: Ver Lista de Transportistas**
    *   AC-1: Debe existir una página que muestre en una tabla a todos los transportistas registrados.

*   **HU-22: Editar Transportista**
    *   AC-1: Se debe poder hacer clic en un transportista de la lista para ir a un formulario de edición con sus datos.

---

## Módulo 5: Reportería Operacional Básica

*   **HU-23: Dashboard - Envíos Creados Hoy**
    *   AC-1: El dashboard principal debe tener una tarjeta o widget que muestre el número total de envíos creados en la fecha actual.

*   **HU-24: Dashboard - Conteo de Envíos por Estado**
    *   AC-1: El dashboard principal debe mostrar una lista o tabla resumiendo cuántos envíos hay actualmente en cada estado (ej. Creado: 15, En Tránsito: 32, Entregado: 150).

*   **HU-25: Exportar Listado a CSV**
    *   AC-1: En la página del listado de envíos, debe haber un botón "Exportar a CSV".
    *   AC-2: Al hacer clic, se debe descargar un archivo .csv que contenga los datos del listado que se está viendo en pantalla, respetando los filtros que el usuario haya aplicado.
