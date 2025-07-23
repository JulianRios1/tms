# Estrategia de Normalización y Geocodificación de Direcciones en Colombia

Este documento detalla una arquitectura y estrategia robusta para manejar la normalización, validación y geocodificación de direcciones, un problema de alta complejidad en el contexto colombiano.

---

## Decisión Estratégica: Enfoque Híbrido (Construir + Comprar)

Construir un sistema de normalización desde cero es inviable. La estrategia recomendada es un **enfoque híbrido**: desarrollar un microservicio inteligente que orqueste y optimice el uso de un servicio externo de pago, añadiendo una capa de lógica de negocio y caché propia.

---

## Propuesta: Microservicio de Geo-Inteligencia

Se propone la creación de un servicio independiente con una única responsabilidad: ser el experto en todo lo relacionado con direcciones y geografía.

### 1. Lenguaje de Programación: Python

**Recomendación:** Python es la elección ideal para este servicio.
*   **Fortalezas:** Capacidades superiores para el procesamiento de texto (parsing, regex), un ecosistema maduro de librerías para ciencia de datos (Pandas) y una excelente integración con APIs de terceros.

### 2. Base de Datos: PostgreSQL + PostGIS

**Recomendación:** Utilizar la base de datos PostgreSQL con la extensión PostGIS activada.
*   **PostgreSQL:** Provee la base relacional robusta.
*   **PostGIS:** Es el componente clave. Transforma la base de datos para que "entienda" la geografía.
    *   **Almacenamiento Geoespacial:** Permite guardar coordenadas (latitud, longitud) en campos nativos de tipo `geography`.
    *   **Consultas Geoespaciales:** Habilita funcionalidades futuras críticas como optimización de rutas, geofencing (zonas de reparto) y cálculos de distancia.
*   **Diseño de Tabla Sugerido (`direcciones_normalizadas`):**
    *   `id` (PK)
    *   `direccion_raw` (TEXT): El texto original que ingresó el usuario.
    *   `direccion_display` (TEXT): La dirección canónica y formateada.
    *   `componentes` (JSONB): Todos los componentes estructurados (calle, número, barrio, etc.).
    *   `ciudad` (VARCHAR)
    *   `departamento` (VARCHAR)
    *   `codigo_postal` (VARCHAR)
    *   `coordenadas` (GEOGRAPHY(Point, 4326)): El punto geográfico exacto.
    *   `proveedor_place_id` (VARCHAR): El ID único del lugar según el proveedor (ej. Google Place ID). **Esencial para la caché y evitar duplicados.**
    *   `precision` (VARCHAR): El nivel de precisión devuelto por el proveedor (ROOFTOP, APPROXIMATE).
    *   `last_validated_at` (TIMESTAMPTZ)

### 3. Pipeline del Algoritmo de Normalización

El servicio ejecutará un proceso secuencial:

1.  **Cache Check:** Antes que nada, buscar si la `direccion_raw` ya existe en la tabla. Si es así, devolver el resultado guardado y terminar el proceso. **Esto es clave para el ahorro de costos.**
2.  **Pre-Limpieza:** Estandarizar el input del usuario (mayúsculas, reemplazo de abreviaturas como 'CL' por 'CALLE', etc.).
3.  **Llamada a API Externa:** Enviar la dirección pre-limpiada al servicio de pago.
4.  **Validación de Respuesta:** Analizar la respuesta del proveedor. ¿Fue exitosa? ¿El nivel de precisión es bueno (ej. a nivel de techo, `ROOFTOP`)?
5.  **Almacenamiento (Cache Write):** Si la respuesta es de alta calidad, guardar la dirección estructurada, sus coordenadas y su `place_id` en la tabla `direcciones_normalizadas`.
6.  **Devolver Resultado:** Devolver la dirección normalizada y geocodificada al servicio que la solicitó (ej. el módulo de creación de envíos).

### 4. Servicio Externo de Pago (Recomendación Principal)

**Recomendación:** **Google Maps Platform**.
*   **API Específica:** **Address Validation API**. Es superior al Geocoding API tradicional porque:
    *   Confirma si una dirección es real y **entregable**.
    *   Corrige errores y completa datos faltantes.
    *   Provee un `Place ID` canónico, ideal para la estrategia de caché.
*   **Modelo de Costos:** Pago por uso con un generoso nivel gratuito mensual, lo que lo hace ideal para empezar.
*   **Alternativas:** HERE Maps, Mapbox. Son competidores válidos, pero Google suele tener la ventaja en cobertura y precisión en Latinoamérica.

### 5. Planteamiento como Plataforma SaaS

Este microservicio está perfectamente posicionado para convertirse en una futura línea de negocio:

*   **API Externa:** Se puede crear un plan para que otros negocios consuman tu servicio de normalización a través de una API pública.
*   **Monetización:** Se pueden ofrecer diferentes niveles de suscripción basados en el volumen de consultas mensuales, generando ingresos adicionales al TMS principal.

---

## Conclusión

No intentes reinventar la rueda construyendo un normalizador desde cero. **Construye un microservicio de Geo-Inteligencia en Python, utilizando PostgreSQL con PostGIS para el almacenamiento y cacheo inteligente, y delega la validación final a un servicio robusto como la Address Validation API de Google.**

Este enfoque es escalable, costo-eficiente y te permite ofrecer una funcionalidad de clase mundial sin desviar el foco del desarrollo del resto de tu producto.
