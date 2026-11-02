# Tp-diseno-5

# Cubrí Seguros · Flujo de contratación digital de seguros

- Sitio publicado: https://tpdiseno5.netlify.app
- Repositorio: https://github.com/CardenasMilton/Tp-diseno-5

## Descripción

Front-end de un checkout para contratar un seguro de celular. El usuario elige el producto, cotiza, elige un plan, carga sus datos, paga (simulado) y recibe su número de póliza. No hay backend ni pagos reales: los planes salen de un JSON local y el pago es una función que simula la respuesta según el número de tarjeta.

## Tecnologías

- HTML5
- Tailwind CSS v4, cargado por CDN (`@tailwindcss/browser@4`)
- Basecoat UI v1.1.0, cargado por CDN (solo CSS, no se usa su JavaScript)
- Fuente Inter (Google Fonts)
- JavaScript vanilla con módulos (sin frameworks ni librerías de validación)

## Cómo correrlo

1. Clonar o descargar el repositorio.
2. Abrir la carpeta en Visual Studio Code y levantar el sitio con la extensión **Live Server** (clic derecho sobre `index.html` → *Open with Live Server*). También se puede usar `npx serve` dentro de la carpeta.
3. No abrir `index.html` con doble click: el `fetch` de `data/planes.json` no funciona con `file://`.

No hace falta instalar dependencias ni compilar. Tailwind y Basecoat se cargan por CDN, por lo que se necesita conexión a internet.

## Estructura del proyecto

```
index.html
css/tokens.css      colores y variables del diseño
data/planes.json    planes Básico, Plus y Premium
js/app.js           arranque, eventos y navegación
js/estado.js        objeto solicitud y funciones para modificarlo
js/render.js        dibuja cada paso a partir del estado
js/validaciones.js  validaciones de cada campo
js/cotizador.js     calcularCuota() y formato de moneda
js/pago.js          simularPago() y generarPoliza()
```

## Pantallas

1. Inicio / Catálogo (solo Celular habilitado; Hogar, Bicicleta y Viaje dicen "Próximamente")
2. Cotizar (Paso 1)
3. Elegir plan (Paso 2)
4. Datos personales (Paso 3)
5. Pago (Paso 4)
6. Éxito (número de póliza)
7. Error de pago

## Reglas de negocio

Cuota mensual = `Math.round(valor × tasaAnual / 12 × factorAntigüedad)`

| Plan | Tasa anual |
|------|-----------|
| Básico | 4% |
| Plus | 6% |
| Premium | 9% |

| Antigüedad | Factor |
|------------|--------|
| Menos de 1 año | 1,00 |
| 1 a 2 años | 1,10 |
| Más de 2 años | 1,25 |

Casos de prueba verificados:

| Valor | Antigüedad | Básico | Plus | Premium |
|-------|-----------|--------|------|---------|
| $ 600.000 | Menos de 1 año | $ 2.000 | $ 3.000 | $ 4.500 |
| $ 600.000 | 1 a 2 años | $ 2.200 | $ 3.300 | $ 4.950 |
| $ 1.200.000 | Más de 2 años | $ 5.000 | $ 7.500 | $ 11.250 |

Tarjetas de prueba (no se usan tarjetas reales):

| Número | Resultado |
|--------|-----------|
| 4111 1111 1111 1111 | Aprobado (pantalla de éxito) |
| 4000 0000 0000 0002 | Rechazado (pantalla de error) |
| Cualquier otro número válido | Aprobado |

El número de póliza tiene el formato `POL-AAAA-NNNNNN`.

## Decisiones tomadas

- Se usó la opción sin build (CDN) del enunciado, sin Vite ni npm.
- Toda la información del flujo vive en un único objeto (`solicitud`, en `estado.js`). Cada evento lo modifica y después se llama a `render()`.
- Cada paso es una `<section data-paso>` que se muestra u oculta con el atributo `hidden`. Los campos se completan desde el estado, por eso "Volver" no pierde los datos.
- Las validaciones están en un archivo aparte. Los errores se muestran al salir de cada campo y los botones "Ver planes", "Continuar al pago" y "Pagar y contratar" quedan deshabilitados hasta que todo sea válido, con un texto que indica qué falta.
- La cuota se calcula con una sola función (`calcularCuota()`), usada en las cards de plan y en el resumen.
- El resumen se genera una sola vez desde el estado (`renderResumen()`): barra fija en mobile y card lateral en desktop.
- Se trabajó mobile first (390 px) y se agregaron los cambios de escritorio (1440 px) con el prefijo `lg:` de Tailwind.
- El botón "Pagar y contratar" queda en "Procesando…" y deshabilitado durante 1,5 s, y se evita el doble click.
- El botón "Descargar póliza" abre la ventana de impresión del navegador.
- Se usaron los componentes de Basecoat que existen (botón, campo de formulario, card, badge y alert). El stepper se armó con utilidades de Tailwind y las cards de plan y las opciones de selección con unas pocas reglas propias en `css/tokens.css`.

## Capturas
