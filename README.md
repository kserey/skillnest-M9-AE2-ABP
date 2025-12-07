# skillnest-M9-AE2-ABP
# Informe Final de Proyecto: Rediseño sitio web Compad e-Commerce

---

## 0. Contexto y Problemática Inicial

El proyecto nace de la necesidad urgente de modernizar la infraestructura digital de **Compad**, la cual sufría de una deuda técnica significativa que afectaba tanto su operatividad como su imagen de marca.

### Obsolescencia Tecnológica
La plataforma original estaba construida sobre una instancia de **WordPress** antigua y mal optimizada. Esto resultaba en una interfaz visualmente obsoleta y tiempos de carga excesivos, lo que impactaba negativamente en la experiencia de usuario y en la percepción de modernidad que una empresa de tecnología debe proyectar.

### Fragmentación de Identidad y DNS
El problema más crítico, sin embargo, era estructural. Debido a una gestión deficiente de los registros DNS en el hosting anterior, la empresa no lograba unificar sus servicios bajo un mismo dominio.
* **El sitio web** operaba bajo `compad.cl`.
* **Los correos corporativos**, por incapacidad técnica de configurar los registros MX correctamente en la plataforma antigua, funcionaban bajo un dominio secundario: `@compad-it.cl`.

Esta disparidad generaba confusión en los clientes y restaba profesionalismo a las comunicaciones.

### Solución Implementada
Al reconstruir el sitio web desde cero utilizando una arquitectura moderna (HTML/JS estático) y migrar el hosting a **Netlify**, recuperamos el control total sobre la zona DNS. Esto permitió:
1.  **Optimizar el rendimiento:** Pasando de un CMS pesado a un sitio estático de carga instantánea.
2.  **Unificar la marca:** Se configuraron correctamente los registros DNS en Netlify, permitiendo que tanto la web como los correos corporativos operen finalmente bajo el dominio principal **`@compad.cl`**, consolidando definitivamente la identidad corporativa.

## 1. Revisión del Producto

### ✅ Funcionalidades implementadas con éxito

1.  **Arquitectura Modular (ES6):** Se logró desacoplar la lógica del sitio en módulos reutilizables. Archivos como `carrusel-generator.js` y `services-renderer.js` permiten generar componentes dinámicos pasándoles objetos de configuración, manteniendo el HTML limpio y separando la vista de los datos.

2.  **Motor de Precios Dinámico:** El archivo `tienda.js` implementa exitosamente una lógica matricial compleja. La función `generarProductosCompletos` cruza las variables de *Dispositivos* (1-25) vs. *Años* (1-3) para calcular el precio exacto mapeando los arrays de datos crudos definidos en `productos-data.js`.

3.  **Interfaz Adaptativa (Responsive Logic):** Se implementó lógica en JavaScript para adaptar la Experiencia de Usuario (UX) según el dispositivo. En `services-renderer.js`, el script detecta el ancho de la ventana (`window.innerWidth`) y decide renderizar pestañas verticales para escritorio o un acordeón colapsable para móviles.

4.  **Gestión de Estado Asíncrona:** El carrito de compras (`carrito.js`) maneja la persistencia de datos con `localStorage` y utiliza `async/await` en la función `enviarNotificacion` para procesar el pedido mediante la API de *Formsubmit* sin recargar la página, proporcionando feedback visual inmediato.

### ⚠️ Áreas de mejora detectadas

* **Manejo de Datos Sensibles en Frontend:** En `tienda.js`, la función `renderizarConfirmacion` inyecta los datos bancarios (Cuenta corriente, RUT, Banco) directamente en el HTML mediante un string ("hardcoded"). Esto dificulta el mantenimiento si la cuenta bancaria cambia.

* **Performance en Resize:** En `script.js`, se utiliza un listener de `resize` con un temporizador (`setTimeout`) para redibujar la sección de servicios. Aunque funcional, redibujar todo el DOM puede ser costoso en dispositivos de gama baja.

* **Dependencia de Parámetros URL:** La tienda depende estrictamente del parámetro `?marca=` en la URL. Si un usuario intenta acceder directamente a `detalle-producto.html` sin navegar desde la home, se muestra un mensaje de error genérico en lugar de un catálogo por defecto.

---

## 2. Depuración y Feedback

Para cumplir con el requisito de retroalimentación, se simuló una revisión técnica (Code Review) identificando puntos críticos y las soluciones aplicadas.

**Feedback Recibido:**
1.  *"La documentación en el README original estaba desactualizada; hablaba de un sitio corporativo simple y no mencionaba el sistema de e-commerce, que es la parte más compleja del código."*
2.  *"El formulario de checkout no tiene validaciones robustas para el teléfono, confiando solo en el input type HTML."*
3.  *"Al confirmar la compra, el modal se cierra y reemplaza todo el contenido del `main`. Si el usuario recarga la página por accidente, pierde la confirmación de su pedido."*

**Ajustes Finales Implementados:**
* **Actualización de Documentación:** Se reescribió el `README.md` detallando la nueva estructura de carpetas `JS/` (incluyendo `data.js`, `productos-data.js`) y explicando el flujo de "Orden de Compra" y la lógica de precios.
* **Mejora en Notificaciones:** Se refactorizó la función `enviarNotificacion` en `carrito.js` para construir un correo detallado que incluye el resumen de productos, total y datos del cliente, enviándolo en segundo plano mediante `fetch` para asegurar que el usuario reciba la información aunque cierre la web.
* **Limpieza de Código:** Se organizaron las importaciones al inicio de `tienda.js` y `script.js` para cumplir con los estándares de módulos ES6 y asegurar que las dependencias como `catalogo` y `Cart` estén disponibles antes de la ejecución.

---

## 3. Descripción Final del Producto

El proyecto **Compad Web** es una aplicación web híbrida que fusiona un sitio corporativo de presentación con un módulo de ventas B2B/B2C para software.

**Tecnologías:** HTML5, CSS3, JavaScript (Vanilla ES6), Bootstrap 5.3.

**Características Clave:**
* **Sitio Corporativo:** SPA (Single Page Application) simulada con navegación fluida. Renderizado dinámico de secciones como "Servicios" y "Casos de Éxito" basado en archivos JSON (`data.js`), lo que permite actualizar textos e imágenes sin tocar el código HTML.

* **Tienda de Licencias:** Sistema de cotización en tiempo real. El usuario selecciona un producto (ej. Antivirus Panda), configura la cantidad de dispositivos y la duración, y el sistema muestra instantáneamente el precio y una tabla comparativa de características técnicas (`tienda.js`).

* **Checkout sin Backend:** Implementación de un flujo de "Orden de Compra" que genera un ID único, envía correos transaccionales vía API externa y presenta instrucciones de pago offline para transferencia bancaria.

---

## 4. Reflexión Personal

El desarrollo de este proyecto marcó una transición importante desde la maquetación estática hacia la programación lógica de aplicaciones web.

El mayor desafío técnico fue la manipulación de datos en `tienda.js` y `productos-data.js`. Tuve que diseñar una lógica que transformara arrays de precios planos (que solo eran listas de números) en objetos de producto utilizables con propiedades como `id`, `precio` y `nombre`, mapeándolos dinámicamente según la elección del usuario en los selectores.

Aprendí a valorar profundamente la **modularización**: separar el generador de carruseles en `carrusel-generator.js` no solo limpió el código principal, sino que me permitió reutilizar la misma lógica para los carruseles de "Partners" y "Casos de Éxito" sin duplicar funciones. Este proyecto demuestra cómo JavaScript nativo, bien estructurado, puede manejar lógicas de negocio complejas sin depender necesariamente de frameworks pesados.