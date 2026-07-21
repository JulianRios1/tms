# Plan de Adaptación: Fleetbase para Pyme (Opción A)

> Continuación de `PRESUPUESTO_AGENTES_CLAUDE_Y_SOFTWARE_LIBRE.md`. Ese documento comparó adaptar Fleetbase vs. construir desde cero y recomendó Fleetbase. Este documento detalla el plan semana por semana para ejecutar esa opción, dirigido por una sola persona con agentes de Claude Code (sin equipo de desarrollo).

---

## Semana 0 (previa, 1-2 días): Validar que Fleetbase encaja antes de comprometerse

No tiene sentido planear 5 semanas sobre una base que no calza. Antes de arrancar la Semana 1:

1. **Levantar Fleetbase localmente** con su instalador Docker (`docker compose up` según su repo) — sin personalizar nada todavía.
2. **Recorrer el flujo real de la pyme** contra la consola de Fleetbase tal cual viene: crear una orden, asignar un mensajero/conductor, marcar estados, ver el tracking público. Comparar contra el flujo que hoy la pyme hace en Excel/WhatsApp.
3. **Checklist go/no-go:**
   - [ ] ¿El modelo de "orden → despacho → conductor → estados" de Fleetbase cubre al menos el 70% del flujo actual sin rediseñar la lógica de negocio?
   - [ ] ¿La consola (Ember.js) es suficientemente simple de navegar para un operador no técnico, o requiere demasiada curva de aprendizaje?
   - [ ] ¿El modelo de tarifas/zonas de Fleetbase se puede mapear a cómo la pyme cobra hoy (por zona, por peso, tarifa plana, etc.)?
   - [ ] ¿La instalación corre sin errores graves en un VPS de gama baja (2-4 GB RAM)?
4. **Si 3 de 4 casillas se cumplen:** seguir con el plan de abajo. **Si no:** volver a evaluar Opción B (construir desde cero) antes de invertir más tiempo — es mejor descubrir esto en 2 días que en la Semana 3.

---

## Semana 1: Entorno, mapeo de codebase y alcance real

**Objetivo:** tener Fleetbase corriendo en un entorno de desarrollo estable y saber exactamente qué módulos se usan, cuáles se ocultan, y qué falta.

- **Días 1-2:** Instalación de desarrollo (no la del instalador rápido) — clonar `fleetbase/fleetbase` y `fleetbase/fleetops`, entender la estructura del monorepo (API Laravel, consola Ember, extensiones).
- **Días 2-3:** Agente(s) de Claude Code leen el codebase y producen un mapa: qué controladores/modelos corresponden a órdenes, despacho, conductores, zonas, tarifas, tracking público. Esto reemplaza el "diseño de arquitectura desde cero" de los documentos v1.0 — aquí es documentar lo que ya existe.
- **Días 3-4:** Definir alcance mínimo real con la pyme: qué módulos de Fleetbase se necesitan (ej. órdenes + despacho + tracking) y cuáles se ocultan de la UI (ej. gestión de flotas complejas, rutas multi-parada si la pyme no las usa).
- **Día 5:** Documentar decisiones de la semana (`DECISIONES_ADAPTACION.md`) — qué se mantiene, qué se oculta, qué se modifica. Esto evita retrabajo cuando se personalice.

**Entregable:** Fleetbase corriendo en entorno de desarrollo + documento de alcance mínimo confirmado con la pyme.

---

## Semana 2: Personalización del flujo core

**Objetivo:** que el flujo de "crear orden → asignar mensajero → marcar entregado" funcione tal como lo necesita la pyme, sin fricción de campos/pasos que no usa.

- Ocultar/eliminar del menú los módulos fuera de alcance (definidos en Semana 1).
- Simplificar el formulario de creación de orden a los campos que la pyme realmente llena hoy (evitar los 15+ campos genéricos de un TMS enterprise).
- Ajustar el modelo de tarifas/zonas al esquema real de cobro de la pyme.
- Traducir/ajustar textos de la consola que queden en inglés o con terminología que no coincide con cómo la pyme llama a las cosas (ej. "guía" en vez de "waybill", "mensajero" en vez de "driver" si aplica).
- Importar datos semilla: clientes y catálogo de zonas/tarifas desde el Excel actual de la pyme (script de importación puntual, no un módulo reutilizable).

**Entregable:** demo interna del flujo completo de principio a fin con datos reales (o representativos) de la pyme.

---

## Semana 3: Integraciones específicas y tracking

**Objetivo:** cerrar las piezas que Fleetbase no trae de fábrica pero la pyme sí necesita.

- **Notificaciones WhatsApp:** conectar creación de orden / cambio de estado a la API de WhatsApp Business (Meta Cloud API) para avisar al cliente final — esto normalmente no viene resuelto de forma simple en Fleetbase para el caso de una pyme chica en Latinoamérica.
- **Tracking público:** confirmar que la página de tracking (ya incluida en Fleetbase) se ve bien en móvil y con la marca de la pyme (logo, colores básicos).
- **Roles y permisos:** ajustar qué puede ver/hacer cada rol (operador, mensajero, admin) según cómo trabaja la pyme hoy, no según el default de Fleetbase.
- Pruebas manuales de extremo a extremo del flujo completo, incluyendo casos borde (orden cancelada, reasignación de mensajero, entrega fallida).

**Entregable:** sistema funcionalmente completo para uso interno, con notificaciones automáticas funcionando.

---

## Semana 4: Pruebas reales, seguridad y despliegue

**Objetivo:** pasar de "funciona en mi máquina" a "la pyme lo puede usar en producción".

- **Pruebas con usuarios reales:** el operador y al menos un mensajero de la pyme usan el sistema con órdenes reales (o un piloto controlado) durante unos días. Recoger fricción real, no supuesta.
- **Revisión de seguridad** (partida presupuestada en el documento anterior): autenticación, exposición de datos de clientes, HTTPS, backups de base de datos.
- **Despliegue a producción:** VPS definitivo, dominio, SSL (Let's Encrypt), variables de entorno y credenciales fuera del repo.
- Documentación mínima de uso para el operador y el mensajero (una guía corta, no un manual extenso).

**Entregable:** sistema en producción, con al menos un ciclo de uso real validado.

---

## Semana 5 (buffer, opcional)

Reservada para lo que casi siempre aparece tarde: ajustes de UX que surgen al usar el sistema en producción, bugs de casos borde no cubiertos en pruebas, o retrasos de la Semana 4 si la pyme no pudo probar a tiempo. Si no se necesita, el proyecto se cierra en la Semana 4.

---

## Resumen de duración

| Semana | Foco | Riesgo si se salta |
|---|---|---|
| 0 | Validar que Fleetbase encaja | Alto — se descubre demasiado tarde que no calza |
| 1 | Entorno + alcance mínimo | Medio — se personaliza sin saber qué hace falta |
| 2 | Flujo core personalizado | — |
| 3 | Integraciones (WhatsApp, tracking, roles) | — |
| 4 | Pruebas reales + seguridad + despliegue | Alto — lanzar sin validar con usuarios reales |
| 5 | Buffer | Bajo — opcional |

Esto confirma el rango de 3-5 semanas estimado en `PRESUPUESTO_AGENTES_CLAUDE_Y_SOFTWARE_LIBRE.md`: 4 semanas es el escenario realista, la 5ª es colchón.

---

## Próximo paso

Ejecutar la Semana 0 (validación) antes de comprometer las semanas siguientes. Si el checklist go/no-go pasa, continuar directo con la Semana 1.
