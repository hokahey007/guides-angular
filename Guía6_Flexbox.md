# 📘 Control del layout moderno con CSS Flexbox en Angular

> 🎯 **Objetivo**
>
> Aprender a construir y controlar el **layout de una aplicación Angular** utilizando **CSS Flexbox**, organizando correctamente zonas como la cabecera, el menú lateral, el contenido principal y el pie de página.
>
> Al finalizar la guía serás capaz de diseñar una interfaz adaptable formada por varias secciones y comprender cómo se distribuye el espacio entre ellas.

---

## 📚 Índice

- [[#1. Introducción|1. Introducción]]
- [[#2. ¿Qué es Flexbox?|2. ¿Qué es Flexbox?]]
- [[#3. Contenedor Flex y elementos Flex|3. Contenedor Flex y elementos Flex]]
- [[#4. Los dos ejes de Flexbox|4. Los dos ejes de Flexbox]]
- [[#5. `flex-direction`|5. `flex-direction`]]
- [[#6. Layout principal de una aplicación Angular|6. Layout principal de una aplicación Angular]]
- [[#7. Primer nivel de Flexbox: estructura vertical|7. Primer nivel de Flexbox: estructura vertical]]
- [[#8. Segundo nivel de Flexbox: sidebar y contenido|8. Segundo nivel de Flexbox: sidebar y contenido]]
- [[#9. Hacer que el contenido ocupe el espacio disponible|9. Hacer que el contenido ocupe el espacio disponible]]
- [[#10. Hacer crecer la zona central verticalmente|10. Hacer crecer la zona central verticalmente]]
- [[#11. Ejemplo completo|11. Ejemplo completo]]
- [[#12. `justify-content`|12. `justify-content`]]
- [[#13. `align-items`|13. `align-items`]]
- [[#14. `gap`|14. `gap`]]
- [[#15. Evitar abusar de `margin`|15. Evitar abusar de `margin`]]
- [[#16. `flex-grow`|16. `flex-grow`]]
- [[#17. `flex-shrink`|17. `flex-shrink`]]
- [[#18. `flex-basis`|18. `flex-basis`]]
- [[#19. La propiedad abreviada `flex`|19. La propiedad abreviada `flex`]]
- [[#20. Flexbox dentro de Flexbox|20. Flexbox dentro de Flexbox]]
- [[#21. Flexbox en la cabecera|21. Flexbox en la cabecera]]
- [[#22. Tarjetas utilizando Flexbox|22. Tarjetas utilizando Flexbox]]
- [[#23. `flex-wrap`|23. `flex-wrap`]]
- [[#24. Control del ancho de las tarjetas|24. Control del ancho de las tarjetas]]
- [[#25. Diseño adaptable|25. Diseño adaptable]]
- [[#26. Ejemplo de layout moderno completo|26. Ejemplo de layout moderno completo]]
- [[#27. Anatomía del layout|27. Anatomía del layout]]
- [[#28. Estrategia para diseñar un layout con Flexbox|28. Estrategia para diseñar un layout con Flexbox]]
- [[#29. Error habitual: aplicar Flexbox al elemento equivocado|29. Error habitual: aplicar Flexbox al elemento equivocado]]
- [[#30. Error habitual: usar `position` para construir todo el layout|30. Error habitual: usar `position` para construir todo el layout]]
- [[#31. Error habitual: asignar anchuras a todos los elementos|31. Error habitual: asignar anchuras a todos los elementos]]
- [[#32. Error habitual: no controlar `box-sizing`|32. Error habitual: no controlar `box-sizing`]]
- [[#33. Flexbox y Angular|33. Flexbox y Angular]]
- [[#34. Separar el layout en componentes Angular|34. Separar el layout en componentes Angular]]
- [[#35. Resumen de propiedades fundamentales|35. Resumen de propiedades fundamentales]]
- [[#36. Propiedades que conviene dominar inicialmente|36. Propiedades que conviene dominar inicialmente]]
- [[#37. Modelo mental recomendado|37. Modelo mental recomendado]]
- [[#38. Regla práctica|38. Regla práctica]]
- [[#39. Actividad práctica|39. Actividad práctica]]
- [[#40. Ideas fundamentales|40. Ideas fundamentales]]

---

# 1. Introducción

Una aplicación web moderna suele dividir su interfaz en diferentes **zonas o secciones**.

Por ejemplo:

```text
┌───────────────────────────────────────────────┐
│                    HEADER                     │
├───────────────┬───────────────────────────────┤
│               │                               │
│    SIDEBAR    │            MAIN               │
│               │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                    FOOTER                     │
└───────────────────────────────────────────────┘
```

Este tipo de estructura aparece continuamente en:

- aplicaciones empresariales;
- paneles de administración;
- redes sociales;
- aplicaciones de gestión;
- plataformas educativas;
- dashboards;
- aplicaciones Angular.

Una de las herramientas más importantes de CSS para controlar este tipo de diseños es **Flexbox**.

> 💡 **Idea clave**
>
> Flexbox permite organizar elementos dentro de un contenedor y repartir automáticamente el espacio disponible.

---

# 2. ¿Qué es Flexbox?

**Flexbox**, o **Flexible Box Layout**, es un sistema de CSS diseñado para distribuir elementos dentro de un contenedor.

Permite controlar fácilmente:

- la dirección en la que se colocan los elementos;
- la separación entre ellos;
- su alineación;
- el espacio que ocupa cada elemento;
- su adaptación al tamaño disponible.

Para utilizar Flexbox hay que aplicar:

```css
display: flex;
```

sobre el elemento que actuará como **contenedor**.

Por ejemplo:

```css
.contenedor {
  display: flex;
}
```

A partir de ese momento, los elementos hijos pasan a ser **elementos flexibles**.

```html
<div class="contenedor">
  <div>Elemento 1</div>
  <div>Elemento 2</div>
  <div>Elemento 3</div>
</div>
```

---

# 3. Contenedor Flex y elementos Flex

Debemos distinguir entre dos conceptos.

## 3.1. Contenedor Flex

Es el elemento sobre el que aplicamos:

```css
display: flex;
```

Ejemplo:

```css
.contenedor {
  display: flex;
}
```

## 3.2. Elementos Flex

Son los **hijos directos** del contenedor.

```html
<div class="contenedor">
  <section class="izquierda"></section>
  <main class="contenido"></main>
</div>
```

En este caso:

```text
.contenedor
│
├── .izquierda
│
└── .contenido
```

`.contenedor` es el **Flex Container**.

`.izquierda` y `.contenido` son **Flex Items**.

> ⚠️ **Importante**
>
> Las propiedades de un contenedor Flex afectan principalmente a sus **hijos directos**, no a todos sus descendientes.

---

# 4. Los dos ejes de Flexbox

Flexbox trabaja utilizando dos ejes:

- **eje principal**;
- **eje secundario o transversal**.

```text
Eje principal
──────────────────────────────→

↑
│
│ Eje secundario
│
↓
```

El comportamiento de estos ejes depende especialmente de:

```css
flex-direction
```

> 📌 Si la dirección cambia, también cambia qué entendemos por eje principal y eje secundario.

---

# 5. `flex-direction`

La propiedad `flex-direction` establece la dirección en la que se distribuyen los elementos.

## 5.1. Distribución horizontal

```css
.contenedor {
  display: flex;
  flex-direction: row;
}
```

Resultado:

```text
┌─────────┬─────────┬─────────┐
│    A    │    B    │    C    │
└─────────┴─────────┴─────────┘
```

`row` es el comportamiento predeterminado.

## 5.2. Distribución vertical

```css
.contenedor {
  display: flex;
  flex-direction: column;
}
```

Resultado:

```text
┌─────────┐
│    A    │
├─────────┤
│    B    │
├─────────┤
│    C    │
└─────────┘
```

Esto resulta especialmente útil para construir la estructura general de una aplicación.

---

# 6. Layout principal de una aplicación Angular

Imaginemos la siguiente estructura:

```text
┌───────────────────────────────────────────┐
│                  HEADER                   │
├──────────────┬────────────────────────────┤
│              │                            │
│   SIDEBAR    │            MAIN            │
│              │                            │
├──────────────┴────────────────────────────┤
│                  FOOTER                   │
└───────────────────────────────────────────┘
```

Podemos implementarla en el componente raíz de Angular.

```html
<div class="app-layout">

  <header class="header">
    Mi aplicación
  </header>

  <div class="body">

    <aside class="sidebar">
      Menú lateral
    </aside>

    <main class="content">
      Contenido principal
    </main>

  </div>

  <footer class="footer">
    Pie de página
  </footer>

</div>
```

La estructura del DOM sería:

```text
app-layout
│
├── header
│
├── body
│   ├── sidebar
│   └── content
│
└── footer
```

Aquí tenemos **dos niveles diferentes de Flexbox**.

---

# 7. Primer nivel de Flexbox: estructura vertical

Queremos colocar:

```text
HEADER
BODY
FOOTER
```

verticalmente.

Por tanto:

```css
.app-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
```

La propiedad:

```css
min-height: 100vh;
```

hace que la aplicación tenga como mínimo la altura completa de la ventana del navegador.

`vh` significa:

```text
Viewport Height
```

Por tanto:

```text
100vh
```

equivale al **100 % de la altura visible del navegador**.

> 💡 Usar `min-height` en lugar de `height` permite que el documento siga creciendo cuando el contenido necesita más espacio.

---

# 8. Segundo nivel de Flexbox: sidebar y contenido

Dentro del cuerpo queremos colocar:

```text
SIDEBAR | CONTENIDO
```

horizontalmente.

```css
.body {
  display: flex;
  flex-direction: row;
}
```

Como `row` es el valor predeterminado, también podemos escribir simplemente:

```css
.body {
  display: flex;
}
```

Resultado:

```text
┌────────────┬─────────────────────────────┐
│            │                             │
│  SIDEBAR   │           CONTENT           │
│            │                             │
└────────────┴─────────────────────────────┘
```

---

# 9. Hacer que el contenido ocupe el espacio disponible

Una de las declaraciones más utilizadas en Flexbox es:

```css
flex: 1;
```

Por ejemplo:

```css
.content {
  flex: 1;
}
```

Esto permite que el contenido principal utilice el espacio disponible.

Si tenemos:

```css
.sidebar {
  width: 250px;
}

.content {
  flex: 1;
}
```

el navegador reparte el espacio de forma parecida a:

```text
ancho contenido =
ancho disponible - ancho sidebar
```

Visualmente:

```text
┌───────────┬──────────────────────────────────┐
│           │                                  │
│  250 px   │          flex: 1                 │
│           │                                  │
└───────────┴──────────────────────────────────┘
```

---

# 10. Hacer crecer la zona central verticalmente

También queremos que el cuerpo de la aplicación ocupe todo el espacio disponible entre la cabecera y el pie.

```css
.body {
  display: flex;
  flex: 1;
}
```

Tenemos entonces:

```css
.app-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.body {
  display: flex;
  flex: 1;
}
```

Ahora `.body` es a la vez:

- un **Flex Item** dentro de `.app-layout`;
- un **Flex Container** para `.sidebar` y `.content`.

---

# 11. Ejemplo completo

## 11.1. `app.component.html`

```html
<div class="app-layout">

  <header class="header">
    <h1>Mi aplicación Angular</h1>

    <nav>
      <a href="#">Inicio</a>
      <a href="#">Perfil</a>
      <a href="#">Contacto</a>
    </nav>
  </header>

  <div class="body">

    <aside class="sidebar">
      <h2>Navegación</h2>

      <ul>
        <li>Inicio</li>
        <li>Perfil</li>
        <li>Proyectos</li>
        <li>Experiencia</li>
      </ul>
    </aside>

    <main class="content">

      <h2>Contenido principal</h2>

      <p>
        Aquí aparecerá el contenido principal de la aplicación.
      </p>

    </main>

  </div>

  <footer class="footer">
    Aplicación Angular
  </footer>

</div>
```

## 11.2. `app.component.css`

```css
.app-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

/* CABECERA */

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  padding: 1rem 2rem;

  background-color: #1f2937;
  color: white;
}

/* CUERPO PRINCIPAL */

.body {
  display: flex;
  flex: 1;
}

/* MENÚ LATERAL */

.sidebar {
  width: 250px;
  padding: 1.5rem;
  background-color: #f3f4f6;
}

/* CONTENIDO */

.content {
  flex: 1;
  padding: 2rem;
}

/* PIE */

.footer {
  padding: 1rem;
  text-align: center;
  background-color: #1f2937;
  color: white;
}
```

---

# 12. `justify-content`

La propiedad:

```css
justify-content
```

controla la distribución de los elementos sobre el **eje principal**.

Por ejemplo:

```css
.header {
  display: flex;
  justify-content: space-between;
}
```

Si tenemos:

```html
<header>
  <h1>Mi aplicación</h1>

  <nav>
    ...
  </nav>
</header>
```

podemos obtener:

```text
Mi aplicación                         Inicio Perfil Salir
```

## 12.1. Valores habituales

### `flex-start`

```css
justify-content: flex-start;
```

```text
[A][B][C]
```

### `center`

```css
justify-content: center;
```

```text
          [A][B][C]
```

### `flex-end`

```css
justify-content: flex-end;
```

```text
                    [A][B][C]
```

### `space-between`

```css
justify-content: space-between;
```

```text
[A]              [B]              [C]
```

### `space-around`

```css
justify-content: space-around;
```

```text
   [A]        [B]        [C]
```

### `space-evenly`

```css
justify-content: space-evenly;
```

```text
     [A]       [B]       [C]
```

> 🎓 **Para recordar**
>
> `justify-content` trabaja sobre el **eje principal**.

---

# 13. `align-items`

La propiedad:

```css
align-items
```

controla la alineación sobre el **eje secundario**.

Uno de los usos más habituales aparece en las cabeceras.

```css
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

Esto permite alinear verticalmente elementos de diferente tamaño.

```text
┌─────────────────────────────────────────┐
│                                         │
│  Mi aplicación        Inicio  Perfil    │
│                                         │
└─────────────────────────────────────────┘
```

> 🎓 **Para recordar**
>
> `align-items` trabaja sobre el **eje transversal o secundario**.

---

# 14. `gap`

Flexbox permite establecer separación entre elementos mediante:

```css
gap
```

Por ejemplo:

```css
nav {
  display: flex;
  gap: 1rem;
}
```

En lugar de obtener:

```text
InicioPerfilContacto
```

obtenemos:

```text
Inicio    Perfil    Contacto
```

Podemos utilizar diferentes unidades:

```css
gap: 10px;
```

```css
gap: 1rem;
```

```css
gap: 2rem;
```

---

# 15. Evitar abusar de `margin`

Una ventaja importante de Flexbox es que muchas separaciones pueden resolverse mediante `gap`.

Menos recomendable:

```css
.enlace {
  margin-right: 20px;
}
```

Más sencillo:

```css
nav {
  display: flex;
  gap: 20px;
}
```

> 💡 `gap` expresa mejor que queremos una **separación entre los elementos del conjunto**.

---

# 16. `flex-grow`

La propiedad:

```css
flex-grow
```

determina cuánto puede crecer un elemento.

```css
.content {
  flex-grow: 1;
}
```

Esto indica que el contenido puede utilizar el espacio sobrante.

---

# 17. `flex-shrink`

La propiedad:

```css
flex-shrink
```

indica si un elemento puede reducir su tamaño cuando no existe suficiente espacio.

```css
.sidebar {
  width: 250px;
  flex-shrink: 0;
}
```

Esto impide que el sidebar reduzca automáticamente su anchura.

---

# 18. `flex-basis`

`flex-basis` establece el tamaño inicial de un elemento dentro del contenedor Flex.

```css
.sidebar {
  flex-basis: 250px;
}
```

Puede utilizarse en lugar de:

```css
width: 250px;
```

cuando se quiere expresar específicamente el tamaño inicial dentro del modelo Flexbox.

---

# 19. La propiedad abreviada `flex`

Las propiedades:

```text
flex-grow
flex-shrink
flex-basis
```

pueden escribirse juntas mediante:

```css
flex
```

Por ejemplo:

```css
.content {
  flex: 1;
}
```

También podemos encontrar:

```css
.sidebar {
  flex: 0 0 250px;
}
```

que podemos leer aproximadamente como:

```text
flex-grow:   0   → no crecer
flex-shrink: 0   → no reducirse
flex-basis:  250px → tamaño inicial
```

> ⭐ `flex: 0 0 250px` es muy útil para crear una columna lateral de tamaño estable.

---

# 20. Flexbox dentro de Flexbox

Una de las ideas más importantes al diseñar interfaces modernas es comprender que **un elemento puede ser al mismo tiempo un Flex Item y un Flex Container**.

```text
app-layout
│
├── header
│
├── body
│   ├── sidebar
│   └── content
│
└── footer
```

`.body` es:

- un elemento Flex dentro de `.app-layout`;
- y al mismo tiempo un contenedor Flex para `sidebar` y `content`.

```css
.body {
  display: flex;
  flex: 1;
}
```

Esta composición de distintos contenedores Flex permite construir interfaces complejas a partir de estructuras sencillas.

---

# 21. Flexbox en la cabecera

Nuestra cabecera podría contener:

```text
LOGO                         NAVEGACIÓN
```

HTML:

```html
<header class="header">

  <div class="logo">
    DevProfile
  </div>

  <nav class="navigation">
    <a href="#">Inicio</a>
    <a href="#">Perfil</a>
    <a href="#">Proyectos</a>
  </nav>

</header>
```

CSS:

```css
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.navigation {
  display: flex;
  gap: 1.5rem;
}
```

Tenemos nuevamente Flexbox dentro de Flexbox:

```text
HEADER
│
├── LOGO
│
└── NAVIGATION
     │
     ├── Inicio
     ├── Perfil
     └── Proyectos
```

---

# 22. Tarjetas utilizando Flexbox

Dentro del contenido podemos crear tarjetas.

```html
<section class="cards">

  <article class="card">
    Angular
  </article>

  <article class="card">
    TypeScript
  </article>

  <article class="card">
    CSS
  </article>

</section>
```

Podemos colocarlas horizontalmente:

```css
.cards {
  display: flex;
  gap: 1rem;
}
```

Resultado:

```text
┌──────────┐ ┌──────────┐ ┌──────────┐
│ Angular  │ │TypeScript│ │   CSS    │
└──────────┘ └──────────┘ └──────────┘
```

---

# 23. `flex-wrap`

¿Qué ocurre cuando no caben todos los elementos?

Por defecto, Flexbox intenta mantenerlos en una única línea.

Podemos permitir que salten a la siguiente mediante:

```css
flex-wrap: wrap;
```

Ejemplo:

```css
.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
```

Resultado:

```text
┌────────┐ ┌────────┐ ┌────────┐
│ Card 1 │ │ Card 2 │ │ Card 3 │
└────────┘ └────────┘ └────────┘

┌────────┐ ┌────────┐
│ Card 4 │ │ Card 5 │
└────────┘ └────────┘
```

---

# 24. Control del ancho de las tarjetas

Podemos combinar `flex` con un tamaño base.

```css
.card {
  flex: 1 1 250px;
}
```

Esto permite que cada tarjeta:

- pueda crecer;
- pueda reducirse;
- parta de un tamaño aproximado de `250px`.

Es una técnica muy útil para crear grupos de tarjetas adaptables.

---

# 25. Diseño adaptable

En pantallas grandes podemos utilizar:

```text
SIDEBAR | CONTENT
```

Pero en pantallas pequeñas posiblemente queramos:

```text
SIDEBAR
CONTENT
```

Para ello utilizamos **media queries**.

```css
@media (max-width: 768px) {

  .body {
    flex-direction: column;
  }

  .sidebar {
    width: auto;
  }

}
```

Cuando la pantalla tenga menos de `768px`, tendremos:

```text
┌───────────────────────┐
│        HEADER         │
├───────────────────────┤
│       SIDEBAR         │
├───────────────────────┤
│                       │
│        CONTENT        │
│                       │
├───────────────────────┤
│        FOOTER         │
└───────────────────────┘
```

> 📱 Cambiar `flex-direction` es una de las formas más sencillas de adaptar un layout de escritorio a una pantalla estrecha.

---

# 26. Ejemplo de layout moderno completo

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}

.app-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

/* HEADER */

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  padding: 1rem 2rem;

  background-color: #1f2937;
  color: white;
}

.navigation {
  display: flex;
  gap: 1.5rem;
}

.navigation a {
  color: white;
  text-decoration: none;
}

/* BODY */

.body {
  display: flex;
  flex: 1;
}

/* SIDEBAR */

.sidebar {
  flex: 0 0 250px;
  padding: 1.5rem;
  background-color: #f3f4f6;
}

/* CONTENT */

.content {
  flex: 1;
  padding: 2rem;
  background-color: white;
}

/* CARDS */

.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.card {
  flex: 1 1 250px;
  padding: 1.5rem;
  border: 1px solid #ddd;
  border-radius: 8px;
}

/* FOOTER */

.footer {
  padding: 1rem;
  text-align: center;
  background-color: #1f2937;
  color: white;
}

/* RESPONSIVE */

@media (max-width: 768px) {

  .header {
    flex-direction: column;
    gap: 1rem;
  }

  .body {
    flex-direction: column;
  }

  .sidebar {
    flex-basis: auto;
  }

}
```

---

# 27. Anatomía del layout

El resultado conceptual sería:

```text
app-layout
│
├── header
│   │
│   ├── logo
│   │
│   └── navigation
│       ├── enlace
│       ├── enlace
│       └── enlace
│
├── body
│   │
│   ├── sidebar
│   │
│   └── content
│       │
│       └── cards
│           ├── card
│           ├── card
│           └── card
│
└── footer
```

Cada nivel puede tener su propio:

```css
display: flex;
```

Esto permite construir el diseño **por capas**.

---

# 28. Estrategia para diseñar un layout con Flexbox

Cuando queremos construir una interfaz no conviene empezar aplicando propiedades CSS aleatoriamente.

Es mejor estudiar primero la estructura.

## 28.1. Paso 1. Identificar las grandes zonas

```text
HEADER
BODY
FOOTER
```

## 28.2. Paso 2. Determinar su dirección

Son verticales:

```css
.app-layout {
  display: flex;
  flex-direction: column;
}
```

## 28.3. Paso 3. Estudiar el `BODY`

Tenemos:

```text
SIDEBAR | CONTENT
```

Por tanto:

```css
.body {
  display: flex;
}
```

## 28.4. Paso 4. Repartir el espacio

Sidebar estable:

```css
.sidebar {
  flex: 0 0 250px;
}
```

Contenido flexible:

```css
.content {
  flex: 1;
}
```

## 28.5. Paso 5. Estudiar las secciones internas

Por ejemplo:

```text
LOGO | MENU
```

Podemos crear otro Flexbox:

```css
.header {
  display: flex;
  justify-content: space-between;
}
```

> 🎯 **Método recomendado**
>
> Diseña siempre el layout desde fuera hacia dentro: primero las zonas principales y después las secciones internas.

---

# 29. Error habitual: aplicar Flexbox al elemento equivocado

Supongamos:

```html
<div class="contenedor">
  <div class="uno"></div>
  <div class="dos"></div>
</div>
```

Si queremos colocar `.uno` y `.dos` horizontalmente, debemos aplicar:

```css
.contenedor {
  display: flex;
}
```

No:

```css
.uno {
  display: flex;
}
```

Flexbox organiza siempre los **hijos directos del contenedor**.

---

# 30. Error habitual: usar `position` para construir todo el layout

Es posible colocar elementos mediante:

```css
position: absolute;
```

pero normalmente no es la mejor herramienta para construir la estructura general de una aplicación.

Por ejemplo:

```css
.sidebar {
  position: absolute;
  left: 0;
}

.content {
  position: absolute;
  left: 250px;
}
```

genera un diseño más difícil de mantener.

Con Flexbox:

```css
.body {
  display: flex;
}

.sidebar {
  flex: 0 0 250px;
}

.content {
  flex: 1;
}
```

el navegador calcula automáticamente el espacio.

> ⚠️ `position: absolute` sigue siendo útil para casos concretos, pero no debería ser la herramienta principal para repartir las grandes zonas de una interfaz.

---

# 31. Error habitual: asignar anchuras a todos los elementos

No suele ser necesario hacer:

```css
.sidebar {
  width: 250px;
}

.content {
  width: 1000px;
}
```

El contenido puede ocupar el espacio restante:

```css
.sidebar {
  flex: 0 0 250px;
}

.content {
  flex: 1;
}
```

De esta forma el diseño es mucho más flexible.

---

# 32. Error habitual: no controlar `box-sizing`

CSS calcula normalmente el tamaño teniendo en cuenta:

```text
width + padding + border
```

Esto puede provocar resultados inesperados.

Una práctica habitual es:

```css
* {
  box-sizing: border-box;
}
```

Con `border-box`, el tamaño declarado incluye:

- contenido;
- `padding`;
- `border`.

---

# 33. Flexbox y Angular

Flexbox pertenece a **CSS**, no a Angular.

Angular se encarga principalmente de:

- componentes;
- datos;
- eventos;
- estado;
- lógica de aplicación;
- renderizado de plantillas.

CSS controla su presentación.

```text
Angular
   │
   ├── estructura
   ├── componentes
   ├── datos
   └── comportamiento

CSS
   │
   ├── layout
   ├── dimensiones
   ├── separación
   ├── colores
   └── presentación
```

> 💡 Angular no sustituye a CSS. Una aplicación Angular sigue necesitando HTML y CSS para construir su interfaz.

---

# 34. Separar el layout en componentes Angular

Cuando la aplicación crece, el layout puede dividirse en componentes.

```text
AppComponent
│
├── HeaderComponent
│
├── SidebarComponent
│
├── MainComponent
│
└── FooterComponent
```

El template raíz podría convertirse en:

```html
<div class="app-layout">

  <app-header />

  <div class="body">

    <app-sidebar />

    <main class="content">

      <!-- Contenido de la aplicación -->

    </main>

  </div>

  <app-footer />

</div>
```

El concepto de Flexbox sigue siendo exactamente el mismo.

> 🎓 La separación en componentes mejora la organización de Angular, mientras que Flexbox sigue controlando cómo se distribuyen visualmente esos componentes.

---

# 35. Resumen de propiedades fundamentales

| Propiedad | Función |
|---|---|
| `display: flex` | Activa Flexbox |
| `flex-direction` | Establece la dirección de distribución |
| `justify-content` | Distribuye elementos sobre el eje principal |
| `align-items` | Alinea elementos sobre el eje secundario |
| `gap` | Establece separación entre elementos |
| `flex-wrap` | Permite saltar elementos a otra línea |
| `flex-grow` | Permite crecer |
| `flex-shrink` | Permite reducirse |
| `flex-basis` | Define el tamaño inicial |
| `flex` | Forma abreviada de `grow`, `shrink` y `basis` |

---

# 36. Propiedades que conviene dominar inicialmente

Para comenzar a trabajar con Flexbox no es necesario memorizar todas las propiedades.

Las más importantes son:

```css
display: flex;
```

```css
flex-direction: row;
```

```css
flex-direction: column;
```

```css
justify-content: space-between;
```

```css
align-items: center;
```

```css
gap: 1rem;
```

```css
flex: 1;
```

```css
flex-wrap: wrap;
```

Con estas propiedades puede construirse una parte muy importante de los layouts utilizados en aplicaciones web modernas.

---

# 37. Modelo mental recomendado

Cuando observemos una interfaz deberíamos intentar verla como **cajas dentro de cajas**.

```text
┌────────────────────────────────────────────┐
│ HEADER                                     │
│ ┌─────────┐                  ┌───────────┐ │
│ │  LOGO   │                  │   MENU    │ │
│ └─────────┘                  └───────────┘ │
├────────────────────────────────────────────┤
│                                            │
│ ┌───────────┐ ┌──────────────────────────┐ │
│ │           │ │                          │ │
│ │ SIDEBAR   │ │          MAIN            │ │
│ │           │ │                          │ │
│ │           │ │ ┌──────┐ ┌──────┐       │ │
│ │           │ │ │ CARD │ │ CARD │       │ │
│ │           │ │ └──────┘ └──────┘       │ │
│ └───────────┘ └──────────────────────────┘ │
│                                            │
├────────────────────────────────────────────┤
│ FOOTER                                     │
└────────────────────────────────────────────┘
```

Para cada grupo debemos preguntarnos:

1. ¿Qué elementos quiero organizar?
2. ¿Cuál es su contenedor padre?
3. ¿Deben colocarse horizontal o verticalmente?
4. ¿Alguno debe ocupar el espacio restante?
5. ¿Necesito separación entre ellos?
6. ¿Cómo deben alinearse?

A partir de estas preguntas podemos elegir las propiedades Flexbox adecuadas.

---

# 38. Regla práctica

Una forma sencilla de recordar Flexbox es pensar:

```text
PADRE
│
│ display: flex
│
└── controla cómo se colocan sus HIJOS
```

Después decidimos:

```text
¿horizontal?

flex-direction: row
```

o:

```text
¿vertical?

flex-direction: column
```

Y finalmente ajustamos:

```text
espacio      → justify-content
alineación   → align-items
separación   → gap
crecimiento  → flex
salto línea  → flex-wrap
```

Esta estrategia permite construir layouts complejos combinando varios contenedores Flex sencillos.

---

# 39. Actividad práctica

> 🧪 **Actividad: perfil profesional tipo LinkedIn**

Crea un nuevo proyecto Angular y modifica el componente raíz para construir una página de **perfil profesional o currículum**.

El layout debe contener:

```text
┌───────────────────────────────────────────────┐
│                   CABECERA                    │
├───────────────┬───────────────────────────────┤
│               │                               │
│ INFORMACIÓN   │       PERFIL PROFESIONAL      │
│   PERSONAL    │                               │
│               │                               │
├───────────────┴───────────────────────────────┤
│                     PIE                       │
└───────────────────────────────────────────────┘
```

## 39.1. Requisitos

La página deberá contener:

- una cabecera con el nombre y profesión;
- una sección lateral izquierda;
- una zona principal;
- un pie de página.

En la sección lateral puedes mostrar:

- fotografía o avatar;
- datos de contacto;
- tecnologías conocidas;
- idiomas.

En la zona principal puedes mostrar:

- presentación personal;
- experiencia;
- formación;
- proyectos;
- habilidades.

Los datos deberán almacenarse inicialmente en propiedades del componente Angular.

Por ejemplo:

```typescript
export class AppComponent {

  nombre = 'Ana García';
  profesion = 'Desarrolladora Web';

  email = 'ana@example.com';

  tecnologias = [
    'HTML',
    'CSS',
    'TypeScript',
    'Angular'
  ];

}
```

## 39.2. Restricciones de layout

El layout principal deberá realizarse utilizando **Flexbox**.

Debes utilizar al menos:

```css
display: flex;
```

```css
flex-direction
```

```css
justify-content
```

```css
align-items
```

```css
gap
```

```css
flex
```

La zona principal deberá ocupar automáticamente el espacio disponible.

## 39.3. Mejora responsive

Añade una media query para que, en una pantalla estrecha, el layout:

```text
SIDEBAR | CONTENT
```

pase a mostrarse como:

```text
SIDEBAR
CONTENT
```

> ⭐ **Reto**
>
> Intenta que las secciones de experiencia, formación o proyectos se presenten también mediante tarjetas Flexbox que cambien automáticamente de línea con `flex-wrap`.

---

# 40. Ideas fundamentales

Al trabajar con Flexbox conviene recordar estas ideas:

- Flexbox organiza los **hijos directos** de un contenedor.
- Para activar Flexbox usamos `display: flex`.
- `flex-direction` determina la dirección del eje principal.
- `justify-content` distribuye elementos sobre el eje principal.
- `align-items` controla la alineación sobre el eje secundario.
- `gap` establece separación entre elementos.
- `flex: 1` permite que un elemento utilice el espacio disponible.
- `flex-wrap` permite distribuir elementos en varias líneas.
- Un elemento puede ser simultáneamente **Flex Item y Flex Container**.
- Los layouts complejos se construyen combinando varios contenedores Flex sencillos.
- Las media queries permiten modificar el layout para diferentes tamaños de pantalla.

> 📌 **Resumen conceptual**
>
> No pienses en una interfaz como una lista de elementos independientes. Piensa en ella como **cajas dentro de cajas**, identifica qué caja controla a cuáles y aplica Flexbox al contenedor adecuado.

---

## ✅ Resultado esperado

Al finalizar esta guía deberías ser capaz de interpretar una interfaz de este tipo:

```text
APP
│
├── HEADER
│   ├── LOGO
│   └── NAV
│
├── BODY
│   ├── SIDEBAR
│   └── MAIN
│       └── CARDS
│
└── FOOTER
```

y decidir de forma razonada dónde utilizar:

```css
display: flex;
```

qué dirección necesita cada contenedor y cómo repartir el espacio entre sus elementos.
