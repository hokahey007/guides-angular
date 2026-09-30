# Guía de componentización de un layout Angular con Flexbox

## Índice

<!-- OBSIDIAN_TOC_START -->
- [[#1. Objetivo|1. Objetivo]]
- [[#2. Componentizar el layout|2. Componentizar el layout]]
- [[#3. Los componentes Angular también son etiquetas HTML|3. Los componentes Angular también son etiquetas HTML]]
- [[#4. La pseudoclase `:host`|4. La pseudoclase `:host`]]
- [[#5. `:host` y `display: flex`|5. `:host` y `display: flex`]]
- [[#6. Flexbox para la zona central|6. Flexbox para la zona central]]
- [[#7. Dar al lateral un 30 % del espacio|7. Dar al lateral un 30 % del espacio]]
- [[#8. Hacer que el contenido principal ocupe el espacio restante|8. Hacer que el contenido principal ocupe el espacio restante]]
- [[#9. ¿Por qué utilizar `min-width: 0`?|9. ¿Por qué utilizar `min-width: 0`?]]
- [[#10. Layout completo|10. Layout completo]]
- [[#11. AppComponent|11. AppComponent]]
- [[#12. MainLayoutComponent|12. MainLayoutComponent]]
- [[#13. AsideComponent|13. AsideComponent]]
- [[#14. MainComponent|14. MainComponent]]
- [[#15. Diferencia respecto al layout sin componentes|15. Diferencia respecto al layout sin componentes]]
- [[#16. Evitar contenedores innecesarios|16. Evitar contenedores innecesarios]]
- [[#17. Una regla importante|17. Una regla importante]]
- [[#18. Error habitual|18. Error habitual]]
- [[#19. Comprobación mediante las herramientas del navegador|19. Comprobación mediante las herramientas del navegador]]
- [[#20. Esquema final|20. Esquema final]]
- [[#21. Ideas clave|21. Ideas clave]]
<!-- OBSIDIAN_TOC_END -->

---
## 1. Objetivo

Cuando una aplicación Angular empieza a crecer, es recomendable dividir la interfaz en **componentes independientes**.

Partiremos de un layout sencillo como este:

```text
┌──────────────────────────────────────────────────────┐
│                      CABECERA                        │
├──────────────────┬───────────────────────────────────┤
│                  │                                   │
│      ASIDE       │               MAIN                │
│                  │                                   │
│       30 %       │         espacio restante          │
│                  │                                   │
├──────────────────┴───────────────────────────────────┤
│                        PIE                           │
└──────────────────────────────────────────────────────┘
```

Inicialmente podríamos tener toda la estructura dentro de `app.component.html`:

```html
<header>
  ...
</header>

<div class="main">
  <aside>
    ...
  </aside>

  <main>
    ...
  </main>
</div>

<footer>
  ...
</footer>
```

El objetivo será sustituir estas secciones por **componentes Angular** sin perder el comportamiento del layout realizado mediante Flexbox.

---

## 2. Componentizar el layout

Una posible estructura de componentes sería:

```text
AppComponent
│
├── HeaderComponent
│
├── MainLayoutComponent
│   │
│   ├── AsideComponent
│   └── MainComponent
│
└── FooterComponent
```

Visualmente:

```text
app-root
│
├── app-header
│
├── app-main-layout
│   ├── app-aside
│   └── app-main
│
└── app-footer
```

El HTML del componente raíz podría quedar reducido a:

```html
<app-header></app-header>

<app-main-layout></app-main-layout>

<app-footer></app-footer>
```

Y el componente encargado de la zona central:

```html
<app-aside></app-aside>

<app-main></app-main>
```

---

## 3. Los componentes Angular también son etiquetas HTML

Cuando Angular renderiza un componente como:

```html
<app-aside></app-aside>
```

`app-aside` es un **elemento real del DOM**.

Por tanto, si tenemos:

```html
<app-main-layout>
  <app-aside></app-aside>
  <app-main></app-main>
</app-main-layout>
```

los hijos directos de `app-main-layout` son:

```text
app-aside
app-main
```

Esto es especialmente importante cuando utilizamos Flexbox.

Si `app-main-layout` tiene:

```css
display: flex;
```

los **flex items** son precisamente:

```text
app-aside
app-main
```

No lo son los elementos `<aside>` o `<main>` que puedan existir en el interior de esos componentes.

---

## 4. La pseudoclase `:host`

Dentro de los estilos CSS de un componente Angular podemos utilizar:

```css
:host {
}
```

` :host ` representa la **etiqueta del propio componente**.

Por ejemplo, dentro de:

```text
aside.component.css
```

la regla:

```css
:host {
  background-color: #eeeeee;
}
```

se aplica al elemento:

```html
<app-aside></app-aside>
```

Conceptualmente:

```text
aside.component.css

:host
  ↓

<app-aside>
```

Esto permite aplicar estilos directamente al componente sin necesidad de añadir un `<div>` adicional.

---

## 5. `:host` y `display: flex`

Una de las aplicaciones más útiles de `:host` consiste en convertir directamente un componente Angular en un contenedor Flexbox.

Por ejemplo, en:

```text
app.component.css
```

podemos escribir:

```css
:host {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
```

Esto convierte:

```html
<app-root>
```

en un contenedor Flexbox.

Sus hijos:

```html
<app-header></app-header>
<app-main-layout></app-main-layout>
<app-footer></app-footer>
```

se organizarán verticalmente porque utilizamos:

```css
flex-direction: column;
```

El resultado conceptual es:

```text
app-root
│
│ display: flex
│ flex-direction: column
│
├── app-header
├── app-main-layout
└── app-footer
```

---

## 6. Flexbox para la zona central

La zona central contiene:

```text
ASIDE + MAIN
```

Por tanto, `MainLayoutComponent` debe actuar como contenedor Flexbox.

Su template puede ser:

```html
<app-aside></app-aside>

<app-main></app-main>
```

Y en:

```text
main-layout.component.css
```

podemos utilizar:

```css
:host {
  display: flex;
  flex: 1;
  width: 100%;
}
```

La propiedad:

```css
display: flex;
```

hace que los componentes:

```text
app-aside
app-main
```

se conviertan en elementos flexibles.

Visualmente:

```text
app-main-layout
│
│ display: flex
│
├───────────────┬────────────────────────────────────┐
│               │                                    │
│  app-aside    │              app-main              │
│               │                                    │
└───────────────┴────────────────────────────────────┘
```

---

## 7. Dar al lateral un 30 % del espacio

En lugar de utilizar:

```css
width: 30%;
```

podemos aprovechar directamente las propiedades de Flexbox.

Dentro de:

```text
aside.component.css
```

podemos indicar:

```css
:host {
  display: flex;
  flex-direction: column;

  flex: 0 0 30%;
}
```

La expresión:

```css
flex: 0 0 30%;
```

es una abreviatura de:

```css
flex-grow: 0;
flex-shrink: 0;
flex-basis: 30%;
```

Por tanto:

```text
flex-grow: 0
```

indica que el elemento **no debe crecer**.

```text
flex-shrink: 0
```

indica que el elemento **no debe reducir su tamaño**.

```text
flex-basis: 30%
```

indica que su tamaño base será el **30 % del contenedor Flexbox**.

Podemos interpretarlo como:

```text
app-aside
│
└── ocupa un 30 % del espacio
```

---

## 8. Hacer que el contenido principal ocupe el espacio restante

En:

```text
main.component.css
```

podemos utilizar:

```css
:host {
  display: flex;
  flex-direction: column;

  flex: 1;

  min-width: 0;
}
```

La regla:

```css
flex: 1;
```

indica que el componente debe utilizar el **espacio disponible restante**.

Por tanto:

```text
app-main-layout
│
├── app-aside → flex: 0 0 30%
│
└── app-main  → flex: 1
```

produce aproximadamente:

```text
┌──────────────────┬──────────────────────────────────┐
│                  │                                  │
│    app-aside     │            app-main              │
│                  │                                  │
│       30 %       │         espacio restante         │
│                  │                                  │
└──────────────────┴──────────────────────────────────┘
```

---

## 9. ¿Por qué utilizar `min-width: 0`?

Los elementos Flexbox pueden conservar un ancho mínimo relacionado con su contenido.

Esto puede provocar problemas cuando dentro de `app-main` aparecen:

- textos largos;
- URLs;
- imágenes;
- fragmentos de código;
- otros elementos que necesitan reducirse.

Por eso es frecuente añadir:

```css
min-width: 0;
```

al componente que debe utilizar el espacio restante:

```css
:host {
  flex: 1;
  min-width: 0;
}
```

Esto permite que el elemento se reduzca correctamente dentro del contenedor Flexbox.

---

## 10. Layout completo

Podemos resumir la estructura de Flexbox de la aplicación de esta forma:

```text
app-root
│
│ display: flex
│ flex-direction: column
│
├── app-header
│
├── app-main-layout
│   │
│   │ display: flex
│   │
│   ├── app-aside
│   │      │
│   │      └── flex: 0 0 30%
│   │
│   └── app-main
│          │
│          └── flex: 1
│
└── app-footer
```

Tenemos por tanto **dos niveles diferentes de Flexbox**.

### Nivel 1: distribución vertical

```text
app-root
```

controla:

```text
header
main-layout
footer
```

mediante:

```css
:host {
  display: flex;
  flex-direction: column;
}
```

### Nivel 2: distribución horizontal

```text
app-main-layout
```

controla:

```text
aside
main
```

mediante:

```css
:host {
  display: flex;
}
```

---

## 11. AppComponent

### `app.component.html`

```html
<app-header></app-header>

<app-main-layout></app-main-layout>

<app-footer></app-footer>
```

### `app.component.css`

```css
:host {
  display: flex;
  flex-direction: column;

  min-height: 100vh;
}
```

El componente raíz se convierte así en un Flexbox vertical.

---

## 12. MainLayoutComponent

### `main-layout.component.html`

```html
<app-aside></app-aside>

<app-main></app-main>
```

### `main-layout.component.css`

```css
:host {
  display: flex;

  flex: 1;
}
```

El propio:

```html
<app-main-layout>
```

es ahora el contenedor Flexbox de la zona central.

### ¿Es necesario `width: 100%`?

En este caso, no.

El componente raíz utiliza:

```css
:host {
  display: flex;
  flex-direction: column;
}
```

Por tanto, en `app-main-layout`:

```css
flex: 1;
```

hace que ocupe el **espacio vertical restante**, ya que el eje principal es vertical.

El ancho completo se obtiene normalmente de forma automática porque Flexbox utiliza por defecto:

```css
align-items: stretch;
```

Por eso, mientras no cambiemos ese comportamiento, `width: 100%` sería redundante.

Recuerda:

```text
flex: 1 en un contenedor column
→ reparte espacio vertical

flex: 1 en un contenedor row
→ reparte espacio horizontal
```

---

## 13. AsideComponent

### `aside.component.css`

```css
:host {
  display: flex;
  flex-direction: column;

  flex: 0 0 30%;

  padding: 1.5rem;

  box-sizing: border-box;
}
```

La regla fundamental es:

```css
flex: 0 0 30%;
```

De esta forma el propio componente:

```html
<app-aside>
```

ocupa el 30 % del layout central.

---

## 14. MainComponent

### `main.component.css`

```css
:host {
  display: flex;
  flex-direction: column;

  flex: 1;

  min-width: 0;

  padding: 1.5rem;

  box-sizing: border-box;
}
```

La regla fundamental es:

```css
flex: 1;
```

El componente:

```html
<app-main>
```

utilizará todo el espacio restante.

---

## 15. Diferencia respecto al layout sin componentes

Antes de componentizar podíamos tener:

```html
<div class="main">

  <aside>
  </aside>

  <main>
  </main>

</div>
```

con:

```css
.main {
  display: flex;
}

aside {
  flex: 0 0 30%;
}

main {
  flex: 1;
}
```

Después de componentizar tendremos:

```html
<app-main-layout>

  <app-aside></app-aside>

  <app-main></app-main>

</app-main-layout>
```

Ahora las reglas CSS se distribuyen entre los propios componentes.

### `main-layout.component.css`

```css
:host {
  display: flex;
}
```

### `aside.component.css`

```css
:host {
  flex: 0 0 30%;
}
```

### `main.component.css`

```css
:host {
  flex: 1;
}
```

La lógica de Flexbox es exactamente la misma.

Lo que cambia es **el elemento del DOM al que aplicamos las reglas**.

---

## 16. Evitar contenedores innecesarios

Sin utilizar `:host` podríamos caer en la tentación de escribir:

```html
<div class="aside-container">

  <aside>
    ...
  </aside>

</div>
```

y posteriormente:

```css
.aside-container {
  flex: 0 0 30%;
}
```

Pero Angular ya crea:

```html
<app-aside>
```

Por eso podemos utilizar directamente:

```css
:host {
  flex: 0 0 30%;
}
```

evitando introducir otro elemento únicamente para controlar el layout.

---

## 17. Una regla importante

Cuando utilices Flexbox debes preguntarte siempre:

> ¿Cuál es el elemento que tiene `display: flex`?

Y después:

> ¿Cuáles son sus hijos directos?

Por ejemplo:

```html
<app-main-layout>
  <app-aside></app-aside>
  <app-main></app-main>
</app-main-layout>
```

Si:

```css
app-main-layout {
  display: flex;
}
```

los elementos flexibles son exclusivamente:

```text
app-aside
app-main
```

Por eso las propiedades:

```css
flex: 0 0 30%;
```

y:

```css
flex: 1;
```

deben aplicarse a esos elementos.

---

## 18. Error habitual

Supongamos que `AsideComponent` tiene:

```html
<aside>
  ...
</aside>
```

y escribimos:

```css
aside {
  flex: 0 0 30%;
}
```

Podríamos esperar que el lateral ocupara un 30 %, pero el Flexbox superior contiene:

```html
<app-aside>
```

y no directamente:

```html
<aside>
```

Tenemos realmente:

```text
app-main-layout
│
├── app-aside
│      └── aside
│
└── app-main
       └── main
```

El hijo directo de `app-main-layout` es:

```text
app-aside
```

Por ello debemos aplicar:

```css
:host {
  flex: 0 0 30%;
}
```

en `aside.component.css`.

---

## 19. Comprobación mediante las herramientas del navegador

Podemos utilizar las herramientas de desarrollo del navegador para comprobar el layout.

Al inspeccionar el DOM deberíamos encontrar aproximadamente:

```html
<app-root>

  <app-header>
    ...
  </app-header>

  <app-main-layout>

    <app-aside>
      ...
    </app-aside>

    <app-main>
      ...
    </app-main>

  </app-main-layout>

  <app-footer>
    ...
  </app-footer>

</app-root>
```

Al seleccionar:

```html
<app-main-layout>
```

deberíamos comprobar que aparece:

```css
display: flex;
```

Al seleccionar:

```html
<app-aside>
```

deberíamos comprobar:

```css
flex: 0 0 30%;
```

Y al seleccionar:

```html
<app-main>
```

deberíamos comprobar:

```css
flex: 1 1 0%;
```

o una representación equivalente generada por el navegador a partir de:

```css
flex: 1;
```

---

## 20. Esquema final

La idea fundamental de la componentización puede resumirse así:

```text
ANTES
────────────────────────────────

div.main
│
│ display: flex
│
├── aside
│      flex: 0 0 30%
│
└── main
       flex: 1


DESPUÉS
────────────────────────────────

app-main-layout
│
│ :host {
│   display: flex;
│ }
│
├── app-aside
│      │
│      └── :host {
│            flex: 0 0 30%;
│          }
│
└── app-main
       │
       └── :host {
             flex: 1;
           }
```

---

## 21. Ideas clave

Recuerda:

```text
:host
```

representa la etiqueta del propio componente Angular.

```css
:host {
  display: flex;
}
```

convierte el componente en un **contenedor Flexbox**.

```css
:host {
  flex: 0 0 30%;
}
```

convierte el componente en un **elemento Flexbox que ocupa un 30 % fijo del espacio**.

```css
:host {
  flex: 1;
}
```

convierte el componente en un **elemento Flexbox que utiliza el espacio restante**.

Por tanto:

```text
:host + display: flex
```

se utiliza principalmente para controlar **cómo se distribuyen los hijos del componente**.

Mientras que:

```text
:host + flex
```

se utiliza para controlar **cómo participa ese componente dentro del Flexbox de su componente padre**.

Esta diferencia es fundamental para comprender correctamente la componentización de layouts con Angular y CSS Flexbox.
