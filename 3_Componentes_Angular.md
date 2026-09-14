# 🧩 Unidad 3: Estructuración de interfaces de usuario con componentes


<style>
.code-card{margin:1.35rem 0;border:1px solid #d0d7de;border-radius:10px;overflow:hidden;background:#f6f8fa;box-shadow:0 2px 8px rgba(31,35,40,.08)}
.code-card .code-label{display:inline-block;padding:.35rem .8rem;background:#111;color:#fff;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;font-size:.72rem;font-weight:700;letter-spacing:.08em;text-transform:uppercase;border-bottom-right-radius:7px}
.code-card pre{margin:0;padding:1rem 1.15rem 1.15rem;overflow-x:auto;background:#f6f8fa;line-height:1.55;tab-size:2}
.code-card code{font-family:"Cascadia Code","JetBrains Mono",Consolas,"Courier New",monospace;font-size:.92rem;color:#24292f;background:transparent;white-space:pre}
.code-card[data-lang="typescript"] pre,.code-card[data-lang="javascript"] pre{background:#f7f9fc}
.code-card[data-lang="html"] pre{background:#fff8f5}
.code-card[data-lang="css"] pre{background:#f8fbff}
.code-card[data-lang="bash"] pre{background:#f7fbf7}
.code-card[data-lang="text"] pre{background:#fafafa}
:not(pre)>code{padding:.12em .38em;border-radius:4px;background:#eef1f4;color:#24292f;font-family:"Cascadia Code","JetBrains Mono",Consolas,monospace;font-size:.92em}
</style>


> **Referencia:** Angular 19  
> **Objetivo:** aprender a crear, componer y comunicar componentes Angular, manejar sus plantillas, controlar estilos, comprender la detección de cambios y utilizar los principales hooks del ciclo de vida.

---

## 🎯 Qué vas a aprender

- Crear componentes con Angular CLI.
- Comprender `@Component` y los componentes standalone.
- Insertar componentes hijos.
- Utilizar interpolación, property binding y event binding.
- Trabajar con `@if`, `@for`, `@empty` y `@switch`.
- Aplicar class binding y style binding.
- Comunicar componentes mediante `input()` y `output()`.
- Utilizar referencias locales de plantilla.
- Entender `ViewEncapsulation`.
- Aplicar `ChangeDetectionStrategy.OnPush`.
- Utilizar `ngOnInit`, `ngOnDestroy`, `ngOnChanges` y `ngAfterViewInit`.
- Acceder a componentes hijos mediante `viewChild()`.

---

## 📑 Contenidos

1. [Los componentes en Angular](#1-los-componentes-en-angular)
2. [Crear y cargar componentes](#3-crear-un-componente-con-angular-cli)
3. [Bindings y control de flujo](#5-interpolación)
4. [Comunicación entre componentes](#14-comunicación-entre-componentes)
5. [Encapsulamiento y detección de cambios](#18-encapsulamiento-css)
6. [Ciclo de vida del componente](#21-ciclo-de-vida-del-componente)
7. [Taller práctico](#30-taller-práctico-interfaces-con-componentes-angular)
8. [Reto integrador](#31-reto-integrador-catálogo-interactivo-de-productos)
9. [Diagnóstico y reflexión](#32-diagnóstico-de-errores-angular)

---

# 1. Los componentes en Angular

Los componentes son los bloques básicos de una aplicación Angular. Cada uno controla una parte de la interfaz, por ejemplo una lista de productos o un formulario de compra.

Se organizan jerárquicamente:

```text
AppComponent
└── ProductListComponent
    └── ProductDetailComponent
```

Un componente puede ser hijo de otro y, a la vez, padre de otros componentes.

---

# 2. Estructura de un componente

La clase del componente principal puede tener esta forma:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
&#8203;
@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'World';
}</code></pre>
</div>


`@Component` es un decorador Angular que configura una clase TypeScript como componente.

## Propiedades principales de `@Component`

| Propiedad | Función |
|---|---|
| `selector` | Selector CSS que identifica el componente |
| `imports` | Artefactos que necesita el componente standalone |
| `templateUrl` | Ruta de la plantilla HTML |
| `styleUrl` | Ruta de la hoja de estilos |

En Angular 19 los componentes standalone son la opción predeterminada.

---

# 3. Crear un componente con Angular CLI

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component product-list</code></pre>
</div>


Angular CLI genera:

```text
product-list/
├── product-list.component.css
├── product-list.component.html
├── product-list.component.spec.ts
└── product-list.component.ts
```

La clase inicial:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Component } from '@angular/core';
&#8203;
@Component({
  selector: 'app-product-list',
  imports: [],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent {
}</code></pre>
</div>


---

# 4. Cargar un componente hijo

En la plantilla del padre:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div class="content"&gt;
  &lt;app-product-list&gt;&lt;/app-product-list&gt;
&lt;/div&gt;</code></pre>
</div>


Si el padre no ha importado el componente, Angular mostrará:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">[ERROR] NG8001: 'app-product-list' is not a known element</code></pre>
</div>


Se corrige importando el componente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { ProductListComponent } from './product-list/product-list.component';
&#8203;
@Component({
  selector: 'app-root',
  imports: [RouterOutlet, ProductListComponent],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})</code></pre>
</div>


También puede utilizarse:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-list /&gt;</code></pre>
</div>


---

# 5. Interpolación

La interpolación muestra datos de la clase dentro de la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h1&gt;Hello, {{ title }}&lt;/h1&gt;</code></pre>
</div>


Angular evalúa `title`, lo convierte a texto y lo muestra.

---

# 6. Property binding

También podemos enlazar una propiedad del componente con una propiedad del DOM:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h1 [innerText]="title"&gt;&lt;/h1&gt;</code></pre>
</div>


- `innerText` es la propiedad destino.
- `title` es la expresión de plantilla.

Para atributos HTML:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p [attr.aria-label]="myText"&gt;&lt;/p&gt;</code></pre>
</div>


Para pasar un literal:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h1 [innerText]="'My title'"&gt;&lt;/h1&gt;</code></pre>
</div>


---

# 7. Control de flujo en plantillas

Angular moderno incorpora:

- `@if`
- `@for`
- `@empty`
- `@switch`

---

# 8. `@if`: representación condicional

Creamos una interfaz:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate interface product</code></pre>
</div>

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  title: string;
}</code></pre>
</div>


En el componente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class ProductListComponent {
  products: Product[] = [];
}</code></pre>
</div>


En la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (products.length &gt; 0) {
  &lt;h1&gt;Products ({{ products.length }})&lt;/h1&gt;
} @else {
  &lt;p&gt;No products found!&lt;/p&gt;
}</code></pre>
</div>


También puede utilizarse `@else if`.

> 💡 En versiones antiguas aparece `*ngIf`, pero Angular moderno recomienda `@if`.

---

# 9. `@for`: iterar colecciones

Datos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products: Product[] = [
  { id: 1, title: 'Keyboard' },
  { id: 2, title: 'Microphone' },
  { id: 3, title: 'Web camera' },
  { id: 4, title: 'Tablet' }
];</code></pre>
</div>


Plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;ul class="pill-group"&gt;
  @for (product of products; track product.id) {
    &lt;li class="pill"&gt;{{ product.title }}&lt;/li&gt;
  }
&lt;/ul&gt;</code></pre>
</div>


`track product.id` permite que Angular identifique cada elemento y sincronice eficientemente cambios del array con el DOM.

## `@empty`

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;ul class="pill-group"&gt;
  @for (product of products; track product.id) {
    &lt;li class="pill"&gt;{{ product.title }}&lt;/li&gt;
  } @empty {
    &lt;p&gt;No products found!&lt;/p&gt;
  }
&lt;/ul&gt;</code></pre>
</div>


## Variables especiales

| Variable | Significado |
|---|---|
| `$count` | Total de elementos |
| `$index` | Índice |
| `$first` / `$last` | Primero / último |
| `$even` / `$odd` | Índice par / impar |

Ejemplo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (product of products; track product.id; let i = $index) {
  &lt;li class="pill"&gt;{{ i + 1 }}. {{ product.title }}&lt;/li&gt;
}</code></pre>
</div>


---

# 10. `@switch`

Permite elegir entre varias plantillas:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (product of products; track product.id) {
  &lt;li class="pill"&gt;
    @switch (product.title) {
      @case ('Keyboard') { ⌨️ }
      @case ('Microphone') { 🎙️ }
      @default { 🏷️ }
    }
&#8203;
    {{ product.title }}
  &lt;/li&gt;
}</code></pre>
</div>


> 💡 En versiones anteriores pueden aparecer `*ngFor` y `[ngSwitch]`.

---

# 11. Class binding

Una clase CSS puede depender de una expresión:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p [class.star]="isLiked"&gt;&lt;/p&gt;</code></pre>
</div>


Varias clases:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p [class]="currentClasses"&gt;&lt;/p&gt;</code></pre>
</div>

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">currentClasses = {
  star: true,
  active: false
};</code></pre>
</div>


---

# 12. Style binding

Un estilo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p [style.color]="'greenyellow'"&gt;&lt;/p&gt;</code></pre>
</div>


Con unidad:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p [style.width.px]="100"&gt;&lt;/p&gt;</code></pre>
</div>


Varios estilos:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p [style]="currentStyles"&gt;&lt;/p&gt;</code></pre>
</div>

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">currentStyles = {
  color: 'greenyellow',
  width: '100px'
};</code></pre>
</div>


---

# 13. Event binding

Para enviar información de la plantilla a la clase escuchamos eventos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">selectedProduct: Product | undefined;</code></pre>
</div>

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (selectedProduct) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;{{ selectedProduct.title }}&lt;/strong&gt;
  &lt;/p&gt;
}</code></pre>
</div>

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (product of products; track product.id) {
  &lt;li
    class="pill"
    (click)="selectedProduct = product"
  &gt;
    {{ product.title }}
  &lt;/li&gt;
}</code></pre>
</div>


Resumen:

```text
{{ valor }}              interpolación
[propiedad]="expresión"  property binding
(evento)="acción"        event binding
```

---

# 14. Comunicación entre componentes

Angular expone una API pública basada en entradas y salidas.

## Padre → hijo: `input()`

Creamos el componente:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component product-detail</code></pre>
</div>

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Component, input } from '@angular/core';
import { Product } from '../product';
&#8203;
@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
export class ProductDetailComponent {
  product = input&lt;Product&gt;();
}</code></pre>
</div>


Plantilla del hijo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (product()) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;{{ product()!.title }}&lt;/strong&gt;
  &lt;/p&gt;
}</code></pre>
</div>


En el padre importamos `ProductDetailComponent` y enlazamos:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [product]="selectedProduct"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>


Un input obligatorio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product = input.required&lt;Product&gt;();</code></pre>
</div>


Si no se proporciona, Angular genera error de compilación.

---

# 15. Hijo → padre: `output()`

En el hijo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Component, input, output } from '@angular/core';
&#8203;
export class ProductDetailComponent {
  product = input&lt;Product&gt;();
  added = output();
&#8203;
  addToCart() {
    this.added.emit();
  }
}</code></pre>
</div>


Plantilla del hijo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (product()) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;{{ product()!.title }}&lt;/strong&gt;
  &lt;/p&gt;
&#8203;
  &lt;button (click)="addToCart()"&gt;Add to cart&lt;/button&gt;
}</code></pre>
</div>


En el padre:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">onAdded() {
  alert(`${this.selectedProduct?.title} added to the cart!`);
}</code></pre>
</div>

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [product]="selectedProduct"
  (added)="onAdded()"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>


---

# 16. Emitir datos con eventos personalizados

Tipamos el output:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">added = output&lt;Product&gt;();</code></pre>
</div>


Emitimos el producto:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addToCart() {
  this.added.emit(this.product()!);
}</code></pre>
</div>


El padre recibe el payload mediante `$event`:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [product]="selectedProduct"
  (added)="onAdded($event)"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">onAdded(product: Product) {
  alert(`${product.title} added to the cart!`);
}</code></pre>
</div>


---

# 17. Variables de referencia de plantilla

Se crean con `#`:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  #productDetail
  [product]="selectedProduct"
  (added)="onAdded()"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>


Después podemos acceder a miembros públicos:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;span&gt;{{ productDetail.product()!.title }}&lt;/span&gt;</code></pre>
</div>


---

# 18. Encapsulamiento CSS

Angular permite limitar el alcance de los estilos de cada componente.

Valores principales de `ViewEncapsulation`:

| Valor | Comportamiento |
|---|---|
| `Emulated` | Encapsulamiento simulado. Es el predeterminado |
| `Native` | Utiliza Shadow DOM nativo cuando está soportado |
| `None` | Sin encapsulamiento |

Ejemplo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  input,
  output,
  ViewEncapsulation
} from '@angular/core';
&#8203;
@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css',
  encapsulation: ViewEncapsulation.None
})
export class ProductDetailComponent {
}</code></pre>
</div>


Con:

<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">div {
  padding-inline: 0.75rem;
  padding-block: 0.375rem;
  border: 2px dashed;
}</code></pre>
</div>


si se usa `ViewEncapsulation.None`, el estilo puede afectar a otros `div` de la aplicación.

> ⚠️ Cambiar el encapsulamiento debe hacerse con cuidado.

---

# 19. Detección de cambios

La **change detection** permite que Angular detecte cambios en las propiedades y actualice la vista.

Puede activarse por:

- clics;
- peticiones asíncronas;
- `setTimeout`;
- `setInterval`.

Cada componente dispone de su propio detector de cambios.

---

# 20. `ChangeDetectionStrategy.OnPush`

Para evitar comprobaciones innecesarias:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  ChangeDetectionStrategy,
  Component,
  input,
  output
} from '@angular/core';
&#8203;
@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ProductDetailComponent {
}</code></pre>
</div>


El PDF muestra mediante Angular DevTools que, con la estrategia predeterminada, la detección puede ejecutarse sobre más componentes, mientras que `OnPush` evita determinadas comprobaciones cuando no han cambiado las referencias relevantes.

---

# 21. Ciclo de vida del componente

Hooks principales:

| Hook | Se ejecuta cuando... |
|---|---|
| `ngOnInit` | Se inicializa el componente |
| `ngOnDestroy` | Se destruye |
| `ngOnChanges` | Cambian inputs |
| `ngAfterViewInit` | Se inicializa la vista y sus hijos |

---

# 22. `ngOnInit`

Los inputs ya están preparados cuando se ejecuta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Component, input, OnInit } from '@angular/core';
&#8203;
export class ProductDetailComponent implements OnInit {
  product = input&lt;Product&gt;();
&#8203;
  constructor() {
    console.log('Product:', this.product());
  }
&#8203;
  ngOnInit(): void {
    console.log('Product:', this.product());
  }
}</code></pre>
</div>


El ejemplo del PDF muestra que el constructor puede recibir todavía `undefined`, mientras que `ngOnInit` ya dispone del input.

> ✅ Conviene mantener los constructores simples y evitar introducir en ellos lógica de negocio.

---

# 23. `ngOnDestroy`

Se utiliza para liberar recursos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  input,
  OnDestroy,
  output
} from '@angular/core';
&#8203;
export class ProductDetailComponent implements OnDestroy {
  product = input&lt;Product&gt;();
  added = output();
&#8203;
  ngOnDestroy(): void {
    // Limpiar recursos
  }
}</code></pre>
</div>


Casos habituales:

- cancelar timers;
- cancelar intervalos;
- anular suscripciones.

Un componente puede destruirse al desaparecer mediante `@if` o al navegar a otra vista.

---

# 24. `DestroyRef`

Alternativa moderna:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { DestroyRef } from '@angular/core';
&#8203;
constructor(destroyRef: DestroyRef) {
  destroyRef.onDestroy(() =&gt; {
    // Limpieza
  });
}</code></pre>
</div>


---

# 25. `ngOnChanges`

Permite detectar cambios en inputs:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  input,
  OnChanges,
  SimpleChanges
} from '@angular/core';
&#8203;
export class ProductDetailComponent implements OnChanges {
  product = input&lt;Product&gt;();
&#8203;
  ngOnChanges(changes: SimpleChanges): void {
    const product = changes['product'];
&#8203;
    const oldValue = product.previousValue;
    const newValue = product.currentValue;
&#8203;
    console.log('Old value', oldValue);
    console.log('New value', newValue);
  }
}</code></pre>
</div>


Podemos ignorar el primer cambio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnChanges(changes: SimpleChanges): void {
  const product = changes['product'];
&#8203;
  if (!product.isFirstChange()) {
    console.log('Old value', product.previousValue);
    console.log('New value', product.currentValue);
  }
}</code></pre>
</div>


---

# 26. `ngAfterViewInit` y `viewChild()`

`ngAfterViewInit` se ejecuta cuando la vista propia y las vistas hijas ya han sido inicializadas.

Importamos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  AfterViewInit,
  Component,
  viewChild
} from '@angular/core';</code></pre>
</div>


Consultamos el hijo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productDetail = viewChild(ProductDetailComponent);</code></pre>
</div>


Y accedemos a su API:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class ProductListComponent implements AfterViewInit {
  productDetail = viewChild(ProductDetailComponent);
&#8203;
  ngAfterViewInit(): void {
    console.log(this.productDetail()!.product());
  }
}</code></pre>
</div>


> 💡 En versiones antiguas puede aparecer `@ViewChild`.

---

# 27. Esquema de comunicación

```text
ProductListComponent (PADRE)
          │
          │ [product]="selectedProduct"
          ▼
ProductDetailComponent (HIJO)
          │
          │ (added)="onAdded($event)"
          ▼
ProductListComponent (PADRE)
```

```text
PADRE → HIJO : input()
HIJO → PADRE : output()
```

---

# 28. Resumen

En esta unidad hemos aprendido a:

- crear y configurar componentes;
- componer interfaces a partir de componentes hijos;
- enlazar propiedades y eventos;
- utilizar el nuevo control de flujo;
- aplicar estilos dinámicos;
- comunicar componentes mediante inputs y outputs;
- trabajar con referencias de plantilla;
- controlar el encapsulamiento CSS;
- optimizar la detección de cambios;
- ejecutar lógica en diferentes fases del ciclo de vida.

---

# 29. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Componente | Bloque básico de una interfaz Angular |
| `@Component` | Decorador de configuración |
| `selector` | Etiqueta para insertar el componente |
| `imports` | Dependencias de un standalone component |
| Interpolación | `{{ valor }}` |
| Property binding | `[propiedad]="expresión"` |
| Event binding | `(evento)="acción"` |
| `@if` | Condicional |
| `@for` | Iteración |
| `@empty` | Caso de colección vacía |
| `@switch` | Selección de plantilla |
| `input()` | Datos de padre a hijo |
| `output()` | Eventos de hijo a padre |
| `$event` | Datos emitidos por un evento |
| `#referencia` | Variable local de plantilla |
| `ViewEncapsulation` | Alcance de estilos |
| `OnPush` | Estrategia de change detection |
| `ngOnInit` | Inicialización |
| `ngOnDestroy` | Destrucción |
| `ngOnChanges` | Cambios en inputs |
| `ngAfterViewInit` | Vista inicializada |
| `viewChild()` | Acceso programático a hijos |

---

## 🔗 Recursos mencionados en el material

- Angular Components: <https://angular.dev/guide/components>
- Lifecycle: <https://angular.dev/guide/components/lifecycle>
- Inputs: <https://angular.dev/guide/components/inputs>
- Outputs: <https://angular.dev/guide/components/outputs>
- Queries: <https://angular.dev/guide/components/queries>
- Change detection: <https://angular.dev/best-practices/skipping-subtrees>
- Eventos DOM: <https://developer.mozilla.org/docs/Web/Events>
- Shadow DOM: <https://developer.mozilla.org/docs/Web/API/Web_components/Using_shadow_DOM>

---

> 🎓 **Siguiente paso**  
> Después de dominar los componentes y su ciclo de vida, el siguiente paso será profundizar en **directivas y pipes**, que permiten enriquecer y reutilizar comportamiento dentro de las plantillas Angular.

---

# 30. Taller práctico: interfaces con componentes Angular 🧪

Los ejercicios de esta sección toman como referencia directa el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/03_ui_con_componentes>

El proyecto utiliza Angular 19 y contiene, entre otros, estos elementos:

- `AppComponent`, que importa y muestra `ProductListComponent`;
- la interfaz `Product`;
- `ProductListComponent`, con cuatro productos y una propiedad `selectedProduct`;
- `ProductDetailComponent`, que recibe el producto mediante `input()` y emite un evento mediante `output()`;
- plantillas que utilizan `@if`, `@for`, `@empty`, `@switch`, property binding y event binding.

> 🎯 **Forma recomendada de trabajo**  
> Ejecuta primero el proyecto tal como está. Después realiza cada cambio de manera incremental y comprueba el resultado en el navegador y en Angular DevTools.

---

## Ejercicio 1. Reconoce el árbol de componentes

### Objetivo

Comprender la jerarquía real del proyecto antes de modificarlo.

En el proyecto de referencia, `AppComponent` importa `ProductListComponent`, y este importa `ProductDetailComponent`.

### Tareas

1. Abre:
   - `app.component.ts`
   - `app.component.html`
   - `product-list.component.ts`
   - `product-list.component.html`
   - `product-detail.component.ts`
2. Completa el árbol:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">AppComponent
└── ______________________
    └── ______________________</code></pre>
</div>


3. Identifica qué selector utiliza cada componente.
4. Localiza dónde se inserta cada selector dentro de la plantilla de su padre.
5. Explica por qué `ProductListComponent` debe aparecer en `imports` de `AppComponent`.

### Comprueba

En el proyecto, el componente raíz importa explícitamente `ProductListComponent`, y la plantilla contiene su selector.

---

## Ejercicio 2. Provoca el error «not a known element»

### Objetivo

Comprender la relación entre `imports` y el uso de un componente standalone.

En `app.component.ts`, elimina temporalmente `ProductListComponent` de:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">imports: [RouterOutlet, ProductListComponent]</code></pre>
</div>


dejándolo así:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">imports: [RouterOutlet]</code></pre>
</div>


### Tareas

1. Guarda el archivo.
2. Observa el error de compilación relacionado con `<app-product-list>`.
3. Restablece la importación.
4. Explica por qué el selector por sí solo no es suficiente.

### Reflexión

¿Qué función cumple la propiedad `imports` en un componente standalone?

---

## Ejercicio 3. Amplía la interfaz `Product`

### Objetivo

Recordar la relación entre TypeScript y las plantillas Angular.

La interfaz del repositorio contiene actualmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  title: string;
}</code></pre>
</div>


Añade dos propiedades:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  title: string;
  price: number;
  stock: number;
}</code></pre>
</div>


### Tareas

1. Actualiza los cuatro productos de `ProductListComponent`.
2. Asigna precios y stock diferentes.
3. Comprueba los errores que aparecen mientras los objetos no cumplan todavía la interfaz.
4. Muestra el precio junto al título en la plantilla.

Por ejemplo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;li class="pill" (click)="onSelect(product)"&gt;
  {{ product.title }} - {{ product.price }} €
&lt;/li&gt;</code></pre>
</div>


### Pregunta

¿Qué ventaja aporta definir la estructura de los productos mediante una interfaz?

---

## Ejercicio 4. `@if`: productos disponibles y agotados

### Objetivo

Utilizar renderizado condicional sobre propiedades del modelo.

Con la propiedad `stock` añadida en el ejercicio anterior, modifica la plantilla para mostrar:

- `Disponible` si `stock > 0`;
- `Agotado` si `stock === 0`.

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (product of products; track product.id) {
  &lt;li class="pill" (click)="onSelect(product)"&gt;
    {{ product.title }}
&#8203;
    @if (product.stock &gt; 0) {
      &lt;span&gt;Disponible&lt;/span&gt;
    } @else {
      &lt;span&gt;Agotado&lt;/span&gt;
    }
  &lt;/li&gt;
}</code></pre>
</div>


### Tareas

1. Pon el stock de uno de los productos a `0`.
2. Comprueba el resultado.
3. Añade un tercer estado:
   - `stock === 0` → Agotado
   - `stock < 5` → Últimas unidades
   - resto → Disponible

Utiliza `@else if`.

---

## Ejercicio 5. Explora las variables de `@for`

### Objetivo

Practicar `$index`, `$first`, `$last`, `$even` y `$odd`.

Modifica temporalmente el bucle:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products;
  track product.id;
  let i = $index;
  let first = $first;
  let last = $last
) {
  &lt;li class="pill"&gt;
    {{ i + 1 }}. {{ product.title }}
&#8203;
    @if (first) {
      &lt;strong&gt;PRIMERO&lt;/strong&gt;
    }
&#8203;
    @if (last) {
      &lt;strong&gt;ÚLTIMO&lt;/strong&gt;
    }
  &lt;/li&gt;
}</code></pre>
</div>


### Tareas

1. Numera los productos.
2. Marca el primero.
3. Marca el último.
4. Añade `$even` y `$odd` y muestra `PAR` o `IMPAR`.

### Pregunta

¿Por qué `track product.id` es mejor que usar el índice cuando la colección puede cambiar de orden?

---

## Ejercicio 6. Comprueba `@empty`

### Objetivo

Entender el comportamiento del bloque `@empty`.

El proyecto ya utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (product of products; track product.id) {
  ...
} @empty {
  &lt;p&gt;No products found!&lt;/p&gt;
}</code></pre>
</div>


### Tareas

1. Sustituye temporalmente el array por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products: Product[] = [];</code></pre>
</div>


2. Comprueba qué se muestra.
3. Devuelve los productos al array.
4. Explica por qué `@empty` puede ser más cómodo que duplicar una condición con `@if`.

---

## Ejercicio 7. Amplía el `@switch`

### Objetivo

Comprender cómo se seleccionan fragmentos de plantilla según un valor.

El repositorio muestra iconos diferentes para `Keyboard` y `Microphone`.

Amplía el bloque para cubrir los cuatro productos:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@switch (product.title) {
  @case ('Keyboard') { ⌨️ }
  @case ('Microphone') { 🎙️ }
  @case ('Web camera') { 📷 }
  @case ('Tablet') { 📱 }
  @default { 🏷️ }
}</code></pre>
</div>


### Reto

Añade un quinto producto denominado `Mouse` sin añadir un nuevo `@case`.

¿Qué icono se mostrará y por qué?

---

## Ejercicio 8. Extrae lógica del template a un método

### Objetivo

Comparar expresiones directas en la plantilla con métodos del componente.

El repositorio ya utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;li class="pill" (click)="onSelect(product)"&gt;</code></pre>
</div>


y el método:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">onSelect(product: Product) {
  this.selectedProduct = product;
  console.log(`Selected product: ${product.title}`);
}</code></pre>
</div>


### Tareas

1. Añade una propiedad:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">selectionCount = 0;</code></pre>
</div>


2. Modifica `onSelect`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">onSelect(product: Product) {
  this.selectedProduct = product;
  this.selectionCount++;
  console.log(`Selected product: ${product.title}`);
}</code></pre>
</div>


3. Muestra el contador mediante interpolación.
4. Comprueba que aumenta con cada clic.

### Reflexión

¿Qué ventajas tiene encapsular la lógica en `onSelect()` frente a escribir varias asignaciones directamente en `(click)`?

---

## Ejercicio 9. Class binding para resaltar el producto seleccionado

### Objetivo

Aplicar dinámicamente una clase CSS.

Añade una clase CSS:

<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">.selected {
  font-weight: 700;
  outline: 2px solid currentColor;
}</code></pre>
</div>


Y modifica cada producto:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;li
  class="pill"
  [class.selected]="selectedProduct?.id === product.id"
  (click)="onSelect(product)"
&gt;
  {{ product.title }}
&lt;/li&gt;</code></pre>
</div>


### Tareas

1. Selecciona varios productos.
2. Comprueba que solo el actual mantiene la clase.
3. Inspecciona el elemento con DevTools y observa cuándo Angular añade o elimina `selected`.

---

## Ejercicio 10. Style binding según el stock

### Objetivo

Aplicar estilos calculados mediante property binding.

Utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;span
  [style.font-weight]="product.stock === 0 ? '700' : '400'"
&gt;
  Stock: {{ product.stock }}
&lt;/span&gt;</code></pre>
</div>


Después añade:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;span
  [style.opacity]="product.stock === 0 ? 0.5 : 1"
&gt;
  {{ product.title }}
&lt;/span&gt;</code></pre>
</div>


### Reflexión

¿Qué diferencias observas entre class binding y style binding? ¿Cuál usarías para un estilo reutilizable?

---

## Ejercicio 11. Haz que `product` sea un input obligatorio

### Objetivo

Entender la diferencia entre input opcional y requerido.

En el repositorio actual, `ProductDetailComponent` utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product = input&lt;Product | undefined&gt;();</code></pre>
</div>


Cámbialo temporalmente por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product = input.required&lt;Product&gt;();</code></pre>
</div>


### Tareas

1. Observa qué ocurre con el binding desde el padre.
2. Comprueba si `selectedProduct` puede seguir siendo `undefined`.
3. Asigna un producto inicial en el padre:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">selectedProduct: Product = this.products[0];</code></pre>
</div>


4. Ajusta los tipos necesarios.
5. Explica qué garantía adicional proporciona `input.required()`.

---

## Ejercicio 12. Envía el producto mediante `output<Product>()`

### Objetivo

Convertir el evento `added` en un evento con payload tipado.

El proyecto actual emite:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">added = output();
&#8203;
addToCart() {
  this.added.emit();
}</code></pre>
</div>


Modifícalo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">added = output&lt;Product&gt;();
&#8203;
addToCart() {
  this.added.emit(this.product()!);
}</code></pre>
</div>


En el padre:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [product]="selectedProduct"
  (added)="onAdded($event)"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>


Y:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">onAdded(product: Product) {
  alert(`${product.title} added to the cart!`);
}</code></pre>
</div>


### Preguntas

1. ¿Qué representa `$event`?
2. ¿Qué gana el código al declarar `output<Product>()`?
3. ¿Por qué el padre ya no necesita leer necesariamente `selectedProduct` dentro de `onAdded`?

---

## Ejercicio 13. Construye un carrito mínimo

### Objetivo

Aplicar comunicación hijo → padre en un caso real.

Añade a `ProductListComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">cart: Product[] = [];
&#8203;
onAdded(product: Product) {
  this.cart = [...this.cart, product];
}</code></pre>
</div>


Muestra el número de productos:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;Productos en carrito: {{ cart.length }}&lt;/p&gt;</code></pre>
</div>


### Tareas

1. Añade varios productos.
2. Comprueba que el contador aumenta.
3. Muestra los títulos con otro `@for`.
4. Utiliza `@empty` para mostrar `Carrito vacío`.

### Reto

Evita añadir el mismo producto dos veces.

---

## Ejercicio 14. Utiliza una referencia local de plantilla

### Objetivo

Acceder desde la plantilla del padre a la API pública del hijo.

Añade:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  #detail
  [product]="selectedProduct"
  (added)="onAdded($event)"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>


Después:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (detail.product()) {
  &lt;p&gt;
    Producto mostrado por el hijo:
    {{ detail.product()!.title }}
  &lt;/p&gt;
}</code></pre>
</div>


### Reflexión

¿Qué diferencia existe entre acceder al producto mediante `selectedProduct` y hacerlo mediante `#detail`?

¿En qué casos puede ser útil una referencia de plantilla?

---

## Ejercicio 15. Comprueba el encapsulamiento CSS

### Objetivo

Observar de manera práctica el efecto de `ViewEncapsulation`.

Añade temporalmente en `product-detail.component.css`:

<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">div {
  border: 3px dashed;
}</code></pre>
</div>


Si la plantilla del detalle no tiene `div`, envuelve su contenido con uno.

### Primera prueba

Mantén el encapsulamiento predeterminado.

Comprueba si otros `div` de la aplicación reciben ese borde.

### Segunda prueba

Configura:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">encapsulation: ViewEncapsulation.None</code></pre>
</div>


### Tareas

1. Observa el cambio.
2. Inspecciona los elementos desde DevTools.
3. Restablece después el comportamiento predeterminado.

### Pregunta

¿Por qué `ViewEncapsulation.None` puede provocar efectos inesperados en una aplicación grande?

---

## Ejercicio 16. Experimenta con `ChangeDetectionStrategy.OnPush`

### Objetivo

Comprender cuándo Angular puede omitir comprobaciones.

Añade al detalle:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">changeDetection: ChangeDetectionStrategy.OnPush</code></pre>
</div>


### Tareas

1. Abre Angular DevTools.
2. Selecciona **Profiler**.
3. Graba una interacción seleccionando productos.
4. Graba otra pulsando `Add to cart`.
5. Compara los componentes sobre los que se ejecuta change detection.

### Segunda parte

En lugar de sustituir un producto completo, prueba a modificar directamente una propiedad del objeto seleccionado.

Analiza la diferencia entre:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.selectedProduct = {
  ...product,
  title: 'Modified'
};</code></pre>
</div>


y:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product.title = 'Modified';</code></pre>
</div>


> 🔎 El objetivo es observar el papel de las referencias cuando se trabaja con `OnPush`.

---

## Ejercicio 17. Constructor frente a `ngOnInit`

### Objetivo

Comprender cuándo están disponibles los inputs.

Implementa temporalmente en `ProductDetailComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  console.log('Constructor:', this.product());
}
&#8203;
ngOnInit(): void {
  console.log('ngOnInit:', this.product());
}</code></pre>
</div>


### Tareas

1. Implementa `OnInit`.
2. Selecciona un producto inicial en el padre.
3. Recarga la aplicación.
4. Compara los mensajes.
5. Explica por qué no conviene depender de inputs dentro del constructor.

---

## Ejercicio 18. Comprueba `ngOnChanges`

### Objetivo

Observar los cambios de un input.

Haz que `ProductDetailComponent` implemente `OnChanges`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnChanges(changes: SimpleChanges): void {
  const productChange = changes['product'];
&#8203;
  if (!productChange.isFirstChange()) {
    console.log('Anterior:', productChange.previousValue);
    console.log('Nuevo:', productChange.currentValue);
  }
}</code></pre>
</div>


### Tareas

1. Selecciona Keyboard.
2. Selecciona Microphone.
3. Selecciona Tablet.
4. Observa la consola.
5. Explica qué representa `previousValue` y `currentValue`.

---

## Ejercicio 19. Destrucción del componente

### Objetivo

Provocar explícitamente `ngOnDestroy`.

Añade al componente padre:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">showDetail = true;</code></pre>
</div>


Controla la existencia del hijo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (showDetail) {
  &lt;app-product-detail
    [product]="selectedProduct"
    (added)="onAdded($event)"
  /&gt;
}
&#8203;
&lt;button (click)="showDetail = !showDetail"&gt;
  Mostrar / ocultar detalle
&lt;/button&gt;</code></pre>
</div>


En el hijo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnDestroy(): void {
  console.log('ProductDetailComponent destruido');
}</code></pre>
</div>


### Tareas

1. Oculta el componente.
2. Comprueba el mensaje.
3. Vuelve a mostrarlo.
4. Explica por qué ocultar visualmente con CSS no equivale necesariamente a destruir el componente.

---

## Ejercicio 20. Accede al hijo con `viewChild()`

### Objetivo

Consultar un componente hijo desde la clase del padre.

En `ProductListComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productDetail = viewChild(ProductDetailComponent);</code></pre>
</div>


Implementa `AfterViewInit`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngAfterViewInit(): void {
  console.log('Detalle:', this.productDetail());
}</code></pre>
</div>


### Tareas

1. Comprueba cuándo aparece el valor en consola.
2. Intenta acceder al hijo desde el constructor.
3. Compara ambos momentos.
4. Accede después a una propiedad pública del hijo.

---

# 31. Reto integrador: catálogo interactivo de productos 🚀

### Objetivo

Aplicar de forma conjunta los conceptos principales de la unidad sobre el proyecto real del repositorio.

Amplía la aplicación para convertirla en un pequeño catálogo interactivo.

## Modelo

Amplía `Product`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  title: string;
  price: number;
  stock: number;
  category: 'peripherals' | 'multimedia' | 'mobile';
}</code></pre>
</div>


## Requisitos del listado

`ProductListComponent` deberá:

- contener al menos seis productos;
- mostrar el número total;
- utilizar `@for`;
- utilizar `track product.id`;
- mostrar el estado del stock con `@if`;
- mostrar un icono con `@switch`;
- resaltar el producto seleccionado con class binding;
- mantener un carrito;
- mostrar `@empty` cuando corresponda.

## Requisitos del detalle

`ProductDetailComponent` deberá:

- recibir un `Product` mediante `input()`;
- mostrar título, precio, stock y categoría;
- emitir el producto mediante `output<Product>()`;
- deshabilitar o esconder `Add to cart` si no hay stock.

Ejemplo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (product()) {
  &lt;section&gt;
    &lt;h2&gt;{{ product()!.title }}&lt;/h2&gt;
    &lt;p&gt;Precio: {{ product()!.price }} €&lt;/p&gt;
    &lt;p&gt;Stock: {{ product()!.stock }}&lt;/p&gt;
&#8203;
    @if (product()!.stock &gt; 0) {
      &lt;button (click)="addToCart()"&gt;
        Add to cart
      &lt;/button&gt;
    } @else {
      &lt;strong&gt;Producto agotado&lt;/strong&gt;
    }
  &lt;/section&gt;
}</code></pre>
</div>


## Requisitos de comunicación

La comunicación debe respetar:

```text
ProductListComponent
       │
       │ [product]
       ▼
ProductDetailComponent
       │
       │ (added)
       ▼
ProductListComponent
```

## Comprobaciones finales

- [ ] El proyecto compila sin errores.
- [ ] Los componentes están correctamente importados.
- [ ] El listado utiliza `@for`.
- [ ] Existe al menos un `@if`.
- [ ] Existe un `@switch`.
- [ ] Se usa `input()`.
- [ ] Se usa `output<Product>()`.
- [ ] Se utiliza `$event`.
- [ ] Existe class binding.
- [ ] Existe style binding o una justificación razonada de por qué no es necesario.
- [ ] El carrito se actualiza al recibir el evento.
- [ ] Los estilos del detalle no se filtran involuntariamente a otros componentes.
- [ ] Se ha inspeccionado la aplicación con Angular DevTools.

---

# 32. Diagnóstico de errores Angular 🔎

Analiza cada caso antes de corregirlo.

## Caso A: componente desconocido

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [product]="selectedProduct"
/&gt;</code></pre>
</div>


pero `ProductDetailComponent` no aparece en `imports`.

**Pregunta:** ¿qué tipo de error esperas?

---

## Caso B: input inexistente

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [item]="selectedProduct"
/&gt;</code></pre>
</div>


pero el hijo declara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product = input&lt;Product&gt;();</code></pre>
</div>


**Pregunta:** ¿por qué Angular no puede resolver `[item]`?

---

## Caso C: evento inexistente

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  (saved)="onAdded($event)"
/&gt;</code></pre>
</div>


pero el hijo declara `added`.

**Pregunta:** ¿qué nombre debe coincidir?

---

## Caso D: propiedad no existente

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">{{ product.description }}</code></pre>
</div>


si `Product` no declara `description`.

**Pregunta:** ¿qué herramienta de VS Code debería ayudarte a detectar el problema?

---

## Caso E: `input.required()`

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product = input.required&lt;Product&gt;();</code></pre>
</div>


y en el padre:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail /&gt;</code></pre>
</div>


**Pregunta:** ¿por qué este problema puede detectarse durante la compilación?

---

# 33. Cuestiones finales de reflexión 📝

1. ¿Qué diferencia hay entre `selector` e `imports`?
2. ¿Qué función desempeña `track` dentro de `@for`?
3. ¿Cuándo utilizarías `@empty`?
4. ¿Qué diferencia existe entre interpolación y property binding?
5. ¿Qué diferencia existe entre property binding y event binding?
6. ¿Por qué `[product]="selectedProduct"` representa comunicación padre → hijo?
7. ¿Por qué `(added)="onAdded($event)"` representa comunicación hijo → padre?
8. ¿Qué contiene `$event` cuando el output está definido como `output<Product>()`?
9. ¿Qué ventaja tiene un `input.required()`?
10. ¿Cuándo usarías una referencia de plantilla `#detail`?
11. ¿Qué problema puede provocar `ViewEncapsulation.None`?
12. ¿Qué objetivo tiene `ChangeDetectionStrategy.OnPush`?
13. ¿Por qué no conviene realizar lógica dependiente de inputs en el constructor?
14. ¿Cuándo se ejecuta `ngOnDestroy`?
15. ¿Qué información proporciona `SimpleChanges`?
16. ¿Qué diferencia existe entre una referencia de plantilla y `viewChild()`?
17. ¿Por qué dividir una interfaz en `ProductListComponent` y `ProductDetailComponent` puede mejorar la mantenibilidad?

---

> ✅ **Meta de aprendizaje**  
> Si puedes completar el reto integrador y explicar el flujo de datos entre `ProductListComponent` y `ProductDetailComponent`, ya dominas los fundamentos esenciales para construir interfaces Angular mediante componentes.
