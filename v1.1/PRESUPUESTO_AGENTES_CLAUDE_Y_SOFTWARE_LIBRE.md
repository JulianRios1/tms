# Presupuesto: TMS para Pymes usando Agentes Claude + Software Libre

> Este documento evalúa reemplazar el equipo humano de desarrollo (propuesta original: 7-8 personas, $250k-$350k, 26 semanas — ver `HISTORIA_PROYECTO.md`) por agentes de Claude Code dirigidos por una sola persona, y analiza si conviene partir de software libre existente en vez de construir desde cero.

---

## 1. Resumen ejecutivo

| | Opción A: Adaptar Fleetbase (recomendada) | Opción B: Construir desde cero con agentes |
|---|---|---|
| **Base** | Fleetbase (open source, AGPL-3.0) | Stack nuevo (Node/Postgres o similar) |
| **Costo inicial (una vez)** | **$500 – $1,500 USD** | **$1,000 – $3,000 USD** |
| **Costo recurrente mensual** | $80 – $200 USD/mes | $40 – $100 USD/mes |
| **Tiempo estimado** | 3-5 semanas | 6-10 semanas |
| **Riesgo técnico** | Bajo-medio (aprender codebase ajeno) | Medio (todo se construye y prueba desde cero) |
| **Riesgo legal** | AGPL — ver sección 4 | Ninguno, código 100% propio |

Ambos escenarios asumen **una sola persona** (tú) dirigiendo agentes de Claude Code, sin equipo de desarrolladores. Esto es 100-300x más barato que la propuesta original de $250k-$350k porque se elimina el costo de nómina — pero no elimina la necesidad de: (a) tu tiempo tomando decisiones de producto, (b) una revisión humana de seguridad antes de cobrar pagos o manejar datos sensibles, y (c) pruebas reales con los mensajeros/operadores de la pyme.

---

## 2. ¿Existe software libre que sirva de base?

Sí. Investigué alternativas para no partir de cero:

### Fleetbase — la opción más directa (recomendada)

[Fleetbase](https://github.com/fleetbase/fleetbase) es un "sistema operativo de logística" open source: dispatch de órdenes, gestión de conductores/mensajeros, mapa en vivo, zonas de servicio, tracking público, y una extensión específica de TMS/flotas ([FleetOps](https://github.com/fleetbase/fleetops)). Es prácticamente el mismo alcance que la propuesta original del repo (`HISTORIA_PROYECTO.md`: autenticación, clientes, órdenes, envíos, manifiestos) pero **ya construido y en producción** (+8,000 operaciones logísticas lo usan según su sitio).

- Se instala self-hosted vía Docker con su propio CLI.
- Stack: Laravel (backend) + Ember.js (consola web) + MySQL + Redis.
- Licencia: **AGPL-3.0** (ver sección 4 — importante si vas a revenderlo como SaaS a varias pymes).
- Trabajo de agentes Claude aquí: no es "programar un TMS", es **personalizar** — quitar módulos que la pyme no usa, traducir/simplificar el flujo a su operación real, conectar WhatsApp para notificaciones, importar su Excel actual como datos semilla.

### ERPNext / Frappe — si la pyme quiere ERP completo, no solo logística

[ERPNext](https://frappe.io/erpnext) es 100% open source (GPLv3, sin límite de usuarios) e incluye notas de entrega, "delivery trips", transportistas — pero está pensado como ERP general (inventario, contabilidad, ventas), no como TMS especializado. Tiene sentido si la pyme además quiere reemplazar su Excel de facturación/inventario, no solo el de envíos. Requiere más esfuerzo de configuración que Fleetbase para el caso específico de despacho/mensajería.

### Traccar — complemento, no reemplazo

[Traccar](https://github.com/traccar/traccar) es open source (Apache 2.0) y resuelve solo el tracking GPS en tiempo real de vehículos/mensajeros. Útil si Fleetbase resulta demasiado pesado y solo necesitas "dónde está el mensajero" combinado con una app ligera de pedidos hecha a la medida.

### Proyectos pequeños en GitHub (no recomendados como base)

Encontré varios repos tipo "Last-Mile-Delivery-Management-System" (proyectos universitarios/personales) — no están en producción, no tienen mantenimiento activo, y adaptarlos probablemente toma más esfuerzo que construir desde cero. Los descarto como base.

**Mi recomendación:** empezar con Fleetbase. Reduce el trabajo de agentes de "construir un TMS" a "configurar y personalizar uno", que es donde los agentes de Claude Code son más efectivos y rápidos (leer código existente, adaptar UI/flujos, escribir integraciones) en vez de diseñar arquitectura desde cero.

---

## 3. Desglose del presupuesto

### Costos de "desarrollo" (agentes Claude, en vez de nómina)

Claude Code se factura de dos formas — usa la que te convenga:

| Modalidad | Costo | Cuándo conviene |
|---|---|---|
| **Suscripción Claude Max** (5x o 20x uso de Pro) | $100/mes o $200/mes | Uso interactivo diario dirigiendo agentes tú mismo — es lo más predecible y probablemente lo más barato para este proyecto |
| **API por uso** (Claude Sonnet 5 / Opus 4.8) | Sonnet 5: ~$3 / $15 por millón de tokens (input/output); Opus 4.8: ~$5 / $25 | Si corres agentes de forma más autónoma/paralela (varias tareas a la vez, ejecución desatendida) — el costo escala con cuánto código se lee/escribe, no con "horas" |

Para un proyecto de este tamaño (adaptar o construir un MVP de logística para una pyme), estimo:

- **Opción A (adaptar Fleetbase):** 1-2 meses de suscripción Max → **$100 – $400 USD**
- **Opción B (construir desde cero):** 2-3 meses de suscripción Max, o uso de API más intensivo si necesitas paralelizar → **$400 – $1,200 USD**

### Infraestructura y costos operativos

| Concepto | Opción A (Fleetbase) | Opción B (a medida) |
|---|---|---|
| Hosting (VPS: DigitalOcean/Hetzner/similar) | $40 – $80/mes (Laravel+Ember+MySQL+Redis+búsqueda) | $15 – $40/mes (app + Postgres) |
| Dominio + SSL | ~$15/año (SSL gratis con Let's Encrypt) | igual |
| Notificaciones WhatsApp (Meta Cloud API o Twilio) | $20 – $50/mes según volumen | igual |
| Pasarela de pagos (si se cobra en línea) | Comisión por transacción (no costo fijo) | igual |
| **Revisión de seguridad humana (recomendado, una vez)** | $300 – $800 | $300 – $800 |

### Total estimado

| | Inicial (una vez) | Recurrente mensual |
|---|---|---|
| **Opción A: Fleetbase** | $500 – $1,500 | $80 – $200 |
| **Opción B: Desde cero** | $1,000 – $3,000 | $40 – $100 |

Compárese con la propuesta original del repo: **$250,000 – $350,000** y 26 semanas con 7-8 personas. La diferencia no es magia — es que estás reemplazando nómina de un equipo profesional por tu propio tiempo + una suscripción de IA, y aceptando un alcance mucho más chico y enfocado (lo que la pyme necesita hoy, no una plataforma SaaS "líder de mercado" como planteaba `PROPUESTA_V2_TMS_LIDER.md`).

---

## 4. Riesgos y advertencias importantes

1. **Licencia AGPL de Fleetbase.** Si usas Fleetbase solo para *una* pyme (uso interno, no lo revendes), no hay problema. Si tu plan es ofrecerlo como SaaS a *muchas* pymes (multi-tenant, cobrando suscripción), AGPL obliga a publicar el código fuente de tus modificaciones a quien lo use como servicio de red. Fleetbase ofrece una licencia comercial (Fleetbase Commercial License) para evitar esa obligación — su costo no está publicado, hay que contactarlos directamente. Esto es una decisión de negocio, no técnica: decide el modelo (una implementación por cliente vs. SaaS multi-tenant) antes de elegir esta ruta.
2. **Los agentes no reemplazan juicio de negocio ni pruebas reales.** Necesitas tú (o alguien de confianza) validando que el flujo de pedidos/tarifas/roles realmente coincide con cómo trabaja la pyme, y que los mensajeros/operadores puedan usar la interfaz sin fricción.
3. **Seguridad en pagos y autenticación.** Si vas a cobrar en línea o manejar datos de clientes, vale la pena una revisión de seguridad por un humano antes de lanzar a producción — está presupuestado arriba como partida opcional pero recomendada.
4. **El presupuesto de IA es una estimación, no una cotización.** El costo real depende de cuánto iteres, cuántas veces cambies de dirección, y qué tan grande termine siendo el alcance. Los rangos de arriba son conservadores para un MVP enfocado.

---

## 5. Próximo paso sugerido

Si estás de acuerdo con el enfoque (Opción A: adaptar Fleetbase), el siguiente paso sería:
1. Levantar Fleetbase localmente con Docker para explorar qué tan cerca está de lo que la pyme necesita.
2. Definir el alcance mínimo real (qué módulos de Fleetbase usar, cuáles ocultar).
3. Empezar la personalización con Claude Code sobre ese codebase.

¿Quieres que detalle el plan de las 3-5 semanas semana por semana, o prefieres primero que exploremos Fleetbase para confirmar que encaja antes de comprometernos a esa ruta?
