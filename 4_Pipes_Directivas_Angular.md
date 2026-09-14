# 🧪 Unidad 4: Enriqueciendo aplicaciones con Pipes y Directivas


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
> **Objetivo:** aprender a transformar datos en las plantillas mediante pipes y a extender el comportamiento y la apariencia de elementos HTML mediante directivas Angular.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Utilizar pipes integrados de Angular.
- Combinar pipes con interpolación y property binding.
- Usar `currency`, `lowercase`, `keyvalue`, `slice`, `json`, `date`, `percent` y `async`.
- Crear pipes personalizados con `@Pipe` y `PipeTransform`.
- Pasar parámetros a un pipe.
- Comprender la diferencia entre pipes puros e impuros.
- Crear directivas de atributo.
- Manipular un elemento mediante `ElementRef`.
- Responder a eventos del elemento host con `@HostListener`.
- Modificar propiedades del host con `@HostBinding`.

---

# 1. ¿Qué es un pipe?

Los **pipes** permiten transformar el resultado de una expresión directamente en la vista.

Su sintaxis básica es:


<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">expression | pipe</code></pre>
</div>


Si el pipe acepta parámetros:


<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">expression | pipe:param</code></pre>
</div>


Los pipes pueden utilizarse tanto con interpolación como con property binding y también pueden encadenarse.

---

# 2. Pipes integrados en Angular

Angular incluye numerosos pipes de uso habitual:

| Pipe | Función |
|---|---|
| `uppercase` | Convierte un texto a mayúsculas |
| `lowercase` | Convierte un texto a minúsculas |
| `percent` | Formatea un número como porcentaje |
| `date` | Formatea fechas |
| `currency` | Formatea valores monetarios |
| `json` | Convierte un objeto a formato JSON |
| `keyvalue` | Convierte un objeto en pares clave-valor |
| `slice` | Obtiene una porción de una colección o cadena |
| `async` | Gestiona datos asíncronos en plantillas |

> 💡 `slice` devuelve una nueva colección y no modifica la original.

---

# 3. Uso de pipes en un componente

Para utilizar los pipes integrados podemos importar `CommonModule`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { CommonModule } from '@angular/common';
import { Component, input, output } from '@angular/core';
import { Product } from '../product';
&#8203;
@Component({
  selector: 'app-product-detail',
  imports: [CommonModule],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
export class ProductDetailComponent {
}</code></pre>
</div>


`CommonModule` exporta los pipes integrados de Angular.

---

# 4. Ampliar el modelo `Product`

El ejemplo del capítulo añade precio y categorías:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  title: string;
  price: number;
  categories: Record&lt;number, string&gt;;
}</code></pre>
</div>


`categories` es un objeto en el que:

- la clave representa el identificador de categoría;
- el valor representa su descripción.

Ejemplo de productos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products: Product[] = [
  {
    id: 1,
    title: 'Keyboard',
    price: 100,
    categories: {
      1: 'Computing',
      2: 'Peripherals'
    }
  },
  {
    id: 2,
    title: 'Microphone',
    price: 35,
    categories: {
      3: 'Multimedia'
    }
  },
  {
    id: 3,
    title: 'Web camera',
    price: 79,
    categories: {
      1: 'Computing',
      3: 'Multimedia'
    }
  },
  {
    id: 4,
    title: 'Tablet',
    price: 500,
    categories: {
      4: 'Entertainment'
    }
  }
];</code></pre>
</div>


---

# 5. Pipe `currency`

Para mostrar el precio en euros:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (product()) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;{{ product()!.title }}&lt;/strong&gt;
  &lt;/p&gt;
&#8203;
  &lt;p&gt;{{ product()!.price | currency:'EUR' }}&lt;/p&gt;
&#8203;
  &lt;button (click)="addToCart()"&gt;Add to cart&lt;/button&gt;
}</code></pre>
</div>


El pipe transforma el número en un valor monetario según la moneda indicada.

---

# 6. Pipes `keyvalue` y `lowercase`

La propiedad `categories` no es directamente iterable porque es un objeto.

`keyvalue` la transforma en una colección de pares clave-valor:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div class="pill-group"&gt;
  @for (
    cat of product()!.categories | keyvalue;
    track cat.key
  ) {
    &lt;p class="pill"&gt;{{ cat.value | lowercase }}&lt;/p&gt;
  }
&lt;/div&gt;</code></pre>
</div>


En este ejemplo:

- `cat.key` contiene el ID de la categoría;
- `cat.value` contiene su texto;
- `lowercase` transforma el texto a minúsculas.

---

# 7. Estilos para las categorías


<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">.pill-group {
  display: flex;
  flex-direction: row;
  align-items: start;
  flex-wrap: wrap;
  gap: 1.25rem;
}
&#8203;
.pill {
  display: flex;
  align-items: center;
  --pill-accent: var(--gray-900);
  background: color-mix(
    in srgb,
    var(--pill-accent) 5%,
    transparent
  );
  color: var(--pill-accent);
  padding-inline: 0.75rem;
  padding-block: 0.375rem;
  border-radius: 2.75rem;
  border: 0;
  transition: background 0.3s ease;
  font-family: var(--inter-font);
  font-size: 0.875rem;
  font-style: normal;
  font-weight: 500;
  line-height: 1.4rem;
  letter-spacing: -0.00875rem;
  text-decoration: none;
}</code></pre>
</div>


---

# 8. Importar pipes individualmente

En lugar de `CommonModule`, podemos importar solo los pipes necesarios:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  CurrencyPipe,
  KeyValuePipe,
  LowerCasePipe
} from '@angular/common';
&#8203;
@Component({
  selector: 'app-product-detail',
  imports: [
    KeyValuePipe,
    CurrencyPipe,
    LowerCasePipe
  ],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
export class ProductDetailComponent {
}</code></pre>
</div>


---

# 9. `@let` en plantillas

Cuando una expresión aparece muchas veces podemos crear una variable local de plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let selectedProduct = product()!;</code></pre>
</div>


Y utilizarla después:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let selectedProduct = product()!;
&#8203;
@if (selectedProduct) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;{{ selectedProduct.title }}&lt;/strong&gt;
  &lt;/p&gt;
&#8203;
  &lt;p&gt;{{ selectedProduct.price | currency:'EUR' }}&lt;/p&gt;
&#8203;
  &lt;div class="pill-group"&gt;
    @for (
      cat of selectedProduct.categories | keyvalue;
      track cat.key
    ) {
      &lt;p class="pill"&gt;{{ cat.value | lowercase }}&lt;/p&gt;
    }
  &lt;/div&gt;
&#8203;
  &lt;button (click)="addToCart()"&gt;Add to cart&lt;/button&gt;
}</code></pre>
</div>


`@let` resulta útil con:

- operadores ternarios;
- propiedades anidadas;
- expresiones complejas;
- el pipe `async`.

---

# 10. Crear un pipe personalizado

Cuando los pipes integrados no son suficientes podemos crear uno propio.

Para crear `sort`:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate pipe sort</code></pre>
</div>


Angular CLI genera una clase similar a:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Pipe, PipeTransform } from '@angular/core';
&#8203;
@Pipe({
  name: 'sort'
})
export class SortPipe implements PipeTransform {
  transform(
    value: unknown,
    ...args: unknown[]
  ): unknown {
    return null;
  }
}</code></pre>
</div>


`@Pipe` define el nombre del pipe.

La clase implementa `PipeTransform`, cuyo método principal es `transform`.

---

# 11. Tipar el pipe `sort`

Como trabajaremos con productos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Pipe, PipeTransform } from '@angular/core';
import { Product } from './product';
&#8203;
@Pipe({
  name: 'sort'
})
export class SortPipe implements PipeTransform {
  transform(
    value: Product[],
    ...args: unknown[]
  ): Product[] {
    return [];
  }
}</code></pre>
</div>


---

# 12. Ordenar por título

Podemos utilizar `Array.sort`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">transform(
  value: Product[],
  ...args: unknown[]
): Product[] {
  if (value) {
    return value.sort((a: Product, b: Product) =&gt; {
      if (a.title &lt; b.title) {
        return -1;
      } else if (b.title &lt; a.title) {
        return 1;
      }
&#8203;
      return 0;
    });
  }
&#8203;
  return [];
}</code></pre>
</div>


Si no existe entrada, se devuelve un array vacío.

---

# 13. Importar y utilizar el pipe

En `ProductListComponent`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { SortPipe } from '../sort.pipe';
&#8203;
@Component({
  selector: 'app-product-list',
  imports: [
    ProductDetailComponent,
    SortPipe
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent {
}</code></pre>
</div>


En la plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;ul class="pill-group"&gt;
  @for (
    product of products | sort;
    track product.id
  ) {
    &lt;li
      class="pill"
      (click)="selectedProduct = product"
    &gt;
      {{ product.title }}
    &lt;/li&gt;
  } @empty {
    &lt;p&gt;No products found!&lt;/p&gt;
  }
&lt;/ul&gt;</code></pre>
</div>


El listado queda ordenado alfabéticamente por `title`.

---

# 14. Pasar parámetros a un pipe

Los parámetros se escriben después del pipe usando `:`.

Para permitir ordenar por cualquier propiedad de `Product`, podemos utilizar `keyof`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">transform(
  value: Product[],
  args: keyof Product
): Product[] {
  if (value) {
    return value.sort((a: Product, b: Product) =&gt; {
      if (a[args] &lt; b[args]) {
        return -1;
      } else if (b[args] &lt; a[args]) {
        return 1;
      }
&#8203;
      return 0;
    });
  }
&#8203;
  return [];
}</code></pre>
</div>


Podemos definir `title` como valor por defecto:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">transform(
  value: Product[],
  args: keyof Product = 'title'
): Product[]</code></pre>
</div>


Y ordenar por precio desde la plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products | sort:'price';
  track product.id
) {
  &lt;li
    class="pill"
    (click)="selectedProduct = product"
  &gt;
    {{ product.title }}
  &lt;/li&gt;
}</code></pre>
</div>


---

# 15. Pipes puros e impuros

Los pipes son **puros por defecto**.

Un pipe puro se ejecuta cuando cambia la referencia del valor de entrada.

Para declarar un pipe impuro:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Pipe({
  name: 'sort',
  pure: false
})</code></pre>
</div>


Un pipe impuro ejecuta `transform` en cada ciclo de detección de cambios, por lo que puede afectar negativamente al rendimiento.

---

# 16. Inmutabilidad y pipes puros

Si añadimos un producto con `push`, la referencia del array no cambia y un pipe puro puede no volver a ejecutarse.

Una alternativa es crear una nueva referencia:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.products = [
  ...this.products,
  {
    id: 5,
    title: 'Headphones',
    price: 55,
    categories: {
      3: 'Multimedia'
    }
  }
];</code></pre>
</div>


> 🧠 Trabajar de forma inmutable facilita que Angular detecte cambios de referencia.

El capítulo también menciona los **computed signals** como alternativa moderna en determinados escenarios, aunque se desarrollan más adelante.

---

# 17. ¿Qué es una directiva?

Las directivas son atributos HTML que extienden el comportamiento o la apariencia de elementos.

Angular distingue tres tipos:

| Tipo | Función |
|---|---|
| Componentes | Directivas con plantilla asociada |
| Estructurales | Añaden o eliminan elementos del DOM |
| De atributo | Modifican apariencia o comportamiento |

> 💡 Si necesitas una plantilla, crea un componente. Si solo necesitas modificar comportamiento o apariencia, una directiva suele ser una mejor opción.

---

# 18. Crear una directiva de atributo

Crearemos una directiva para mostrar copyright:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate directive copyright</code></pre>
</div>


Angular CLI genera:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Directive } from '@angular/core';
&#8203;
@Directive({
  selector: '[appCopyright]'
})
export class CopyrightDirective {
  constructor() {}
}</code></pre>
</div>


El selector es un selector CSS de atributo.

En HTML se utilizará así:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;footer appCopyright&gt;&lt;/footer&gt;</code></pre>
</div>


> 📌 Al utilizar la directiva en la plantilla no escribimos los corchetes del selector.

---

# 19. Estilos globales para la directiva

Las directivas no disponen de un archivo CSS propio como los componentes.

Añadimos los estilos a `styles.css`:


<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">.copyright {
  font-family:
    "Inter",
    -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    Roboto,
    Helvetica,
    Arial,
    sans-serif,
    "Apple Color Emoji",
    "Segoe UI Emoji",
    "Segoe UI Symbol";
&#8203;
  width: 100%;
  min-height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem;
  box-sizing: inherit;
  position: relative;
}</code></pre>
</div>


---

# 20. Manipular el elemento con `ElementRef`

Podemos acceder al elemento HTML asociado a la directiva:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Directive,
  ElementRef
} from '@angular/core';
&#8203;
@Directive({
  selector: '[appCopyright]'
})
export class CopyrightDirective {
  constructor(el: ElementRef) {
    const currentYear = new Date().getFullYear();
&#8203;
    const targetEl: HTMLElement =
      el.nativeElement;
&#8203;
    targetEl.classList.add('copyright');
&#8203;
    targetEl.textContent =
      `Copyright ©${currentYear} All Rights Reserved`;
  }
}</code></pre>
</div>


En este ejemplo:

- `ElementRef` da acceso al elemento;
- `nativeElement` contiene el elemento HTML nativo;
- `classList.add` añade una clase;
- `textContent` modifica el contenido textual.

---

# 21. Importar la directiva

En el componente raíz:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { CopyrightDirective }
  from './copyright.directive';
&#8203;
@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    ProductListComponent,
    CopyrightDirective
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
}</code></pre>
</div>


Y en la plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;main class="main"&gt;
  &lt;div class="content"&gt;
    &lt;app-product-list&gt;&lt;/app-product-list&gt;
  &lt;/div&gt;
&lt;/main&gt;
&#8203;
&lt;footer appCopyright&gt;&lt;/footer&gt;
&#8203;
&lt;router-outlet /&gt;</code></pre>
</div>


---

# 22. `@HostBinding` y `@HostListener`

Las directivas de atributo también pueden responder a eventos del elemento host.

Angular proporciona:

- `@HostBinding`: enlaza una propiedad de la directiva con una propiedad del elemento host;
- `@HostListener`: escucha eventos del elemento host.

---

# 23. Directiva `numeric`

Creamos:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate directive numeric</code></pre>
</div>


Importamos los decoradores:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Directive,
  HostBinding,
  HostListener
} from '@angular/core';</code></pre>
</div>


Definimos una clase enlazada al host:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@HostBinding('class')
currentClass = '';</code></pre>
</div>


Y escuchamos `keypress`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@HostListener(
  'keypress',
  ['$event']
)
onKeyPress(event: KeyboardEvent) {
  const charCode =
    event.key.charCodeAt(0);
&#8203;
  if (
    charCode &gt; 31 &amp;&amp;
    (charCode &lt; 48 || charCode &gt; 57)
  ) {
    this.currentClass = 'invalid';
    event.preventDefault();
  } else {
    this.currentClass = 'valid';
  }
}</code></pre>
</div>


Cuando el usuario pulsa una tecla:

1. Angular llama a `onKeyPress`.
2. `$event` contiene el evento de teclado.
3. Se obtiene el código del carácter.
4. Si no es numérico:
   - se aplica la clase `invalid`;
   - se cancela la entrada con `preventDefault()`.
5. Si es numérico, se aplica `valid`.

---

# 24. Estilos de validación

En `styles.css`:


<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">input.valid {
  border: solid green;
}
&#8203;
input.invalid {
  border: solid red;
}</code></pre>
</div>


---

# 25. Aplicar `appNumeric`

En un input:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input appNumeric /&gt;</code></pre>
</div>


El componente que utilice la directiva debe importarla antes.

---

# 26. Esquema conceptual

```text
PIPE
dato → transformación → salida en plantilla

DIRECTIVA
elemento HTML → comportamiento/apariencia adicional
```

```text
Product
  │
  ├── currency
  ├── lowercase
  ├── keyvalue
  └── sort
       │
       ▼
Plantilla transformada
```

---

# 27. Resumen

En esta unidad hemos aprendido dos mecanismos fundamentales de Angular:

### Pipes

Permiten transformar datos en la plantilla:

- integrados;
- personalizados;
- parametrizables;
- puros o impuros.

### Directivas

Permiten extender el comportamiento de elementos:

- modificar contenido;
- modificar clases;
- responder a eventos;
- implementar comportamientos reutilizables.

También hemos trabajado con:

- `CommonModule`;
- `@let`;
- `keyof`;
- `ElementRef`;
- `@HostBinding`;
- `@HostListener`.

---

# 28. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Pipe | Transformación de datos en plantilla |
| `currency` | Formato monetario |
| `lowercase` | Texto en minúsculas |
| `keyvalue` | Objeto → colección clave-valor |
| `@let` | Variable local de plantilla |
| `@Pipe` | Decorador para pipes |
| `PipeTransform` | Interfaz de transformación |
| `transform()` | Método principal de un pipe |
| `keyof` | Restringe una clave a propiedades válidas |
| Pipe puro | Reacciona a cambios de referencia |
| Pipe impuro | Se ejecuta en cada change detection |
| Directiva | Extensión de comportamiento/apariencia |
| `@Directive` | Decorador de directivas |
| `ElementRef` | Acceso al elemento host |
| `nativeElement` | Elemento DOM nativo |
| `@HostBinding` | Binding hacia el host |
| `@HostListener` | Escucha eventos del host |
| `$event` | Evento actual |

---

## 🔗 Recursos mencionados en el material

- Angular API: <https://angular.dev/api>
- JavaScript `Array.sort`: <https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/sort>

---

> 🎓 **Siguiente paso**  
> Después de dominar pipes y directivas, el siguiente paso será estudiar cómo Angular utiliza **servicios e inyección de dependencias** para gestionar tareas complejas y compartir lógica entre componentes.

---

# 29. Taller práctico: Pipes y Directivas Angular 🧪

Los ejercicios de esta sección toman como referencia directa el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/04_pipes_y_directivas>

El proyecto de referencia ya contiene:

- una interfaz `Product` con `id`, `title`, `price` y `categories`;
- un `ProductListComponent` con cuatro productos;
- un `ProductDetailComponent` que utiliza `currency`, `keyvalue` y `lowercase`;
- un pipe personalizado `SortPipe`;
- una directiva `CopyrightDirective`;
- una directiva `NumericDirective`;
- el uso de `appCopyright` en el `<footer>` de la aplicación.

> 🎯 **Forma recomendada de trabajo**  
> Parte del código existente y realiza los ejercicios en orden. Antes de hacer cada cambio, intenta predecir qué ocurrirá en la plantilla o en el DOM.

---

## Ejercicio 1. Localiza todos los pipes del proyecto

### Objetivo

Identificar los pipes integrados y personalizados que ya utiliza la aplicación.

### Tareas

1. Abre `product-detail.component.html`.
2. Localiza los pipes utilizados.
3. Abre `product-list.component.html`.
4. Localiza el pipe personalizado.
5. Completa:

| Pipe | Tipo | Entrada | Resultado |
|---|---|---|---|
| `currency` | Integrado |  |  |
| `keyvalue` | Integrado |  |  |
| `lowercase` | Integrado |  |  |
| `sort` | Personalizado |  |  |

### Pregunta

¿Por qué `categories` necesita `keyvalue` antes de poder utilizarse cómodamente dentro de un `@for`?

---

## Ejercicio 2. Cambia el formato monetario

### Objetivo

Experimentar con parámetros de un pipe integrado.

En el proyecto actual aparece:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;{{ product()!.price | currency:'EUR' }}&lt;/p&gt;</code></pre>
</div>


### Tareas

1. Cambia temporalmente `EUR` por `USD`.
2. Prueba `GBP`.
3. Comprueba el resultado en el navegador.
4. Añade un segundo precio únicamente como prueba utilizando otra moneda.

### Reto

Investiga cómo mostrar el código de moneda además del símbolo y documenta el resultado en un comentario del ejercicio.

---

## Ejercicio 3. Encadena pipes

### Objetivo

Comprender que varios pipes pueden aplicarse de forma secuencial.

El proyecto transforma las categorías con:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">{{ cat.value | lowercase }}</code></pre>
</div>


Modifica temporalmente la expresión para experimentar con otro pipe de texto disponible.

Por ejemplo, compara:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">{{ cat.value | lowercase }}
{{ cat.value | uppercase }}</code></pre>
</div>


### Pregunta

¿Se modifica el valor original almacenado en `product.categories` o solamente su representación en la plantilla?

---

## Ejercicio 4. Utiliza el pipe `json` para depurar

### Objetivo

Visualizar rápidamente un objeto Angular durante el desarrollo.

Añade temporalmente en `product-detail.component.html`:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;pre&gt;{{ product() | json }}&lt;/pre&gt;</code></pre>
</div>


### Tareas

1. Selecciona varios productos.
2. Observa cómo cambia el JSON.
3. Compara esa información con el objeto original de `ProductListComponent`.
4. Elimina el bloque cuando termines.

### Reflexión

¿Por qué `json` es especialmente útil como herramienta de depuración y no como mecanismo habitual de presentación?

---

## Ejercicio 5. Usa `@let` para simplificar la plantilla

### Objetivo

Evitar repetir expresiones largas.

La plantilla actual utiliza varias veces:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">product()!</code></pre>
</div>


Refactoriza el archivo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let selectedProduct = product()!;
&#8203;
@if (selectedProduct) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;{{ selectedProduct.title }}&lt;/strong&gt;
  &lt;/p&gt;
&#8203;
  &lt;p&gt;{{ selectedProduct.price | currency:'EUR' }}&lt;/p&gt;
&#8203;
  &lt;div class="pill-group"&gt;
    @for (
      cat of selectedProduct.categories | keyvalue;
      track cat.key
    ) {
      &lt;p class="pill"&gt;{{ cat.value | lowercase }}&lt;/p&gt;
    }
  &lt;/div&gt;
&#8203;
  &lt;button (click)="addToCart()"&gt;Add to cart&lt;/button&gt;
}</code></pre>
</div>


### Pregunta

¿Qué mejora aporta `@let` en términos de legibilidad?

---

## Ejercicio 6. Comprende `SortPipe`

### Objetivo

Analizar el pipe personalizado existente antes de modificarlo.

El repositorio contiene un `SortPipe` cuya firma es:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">transform(
  value: Product[],
  args: keyof Product = 'title'
): Product[]</code></pre>
</div>


### Tareas

1. Identifica el valor de entrada.
2. Identifica el parámetro.
3. Identifica el valor por defecto.
4. Explica qué restringe `keyof Product`.
5. Comprueba dónde se importa el pipe.
6. Comprueba dónde se utiliza.

### Pregunta

¿Por qué el pipe puede recibir `'title'` o `'price'`, pero no una propiedad inexistente como `'description'`?

---

## Ejercicio 7. Ordena por precio

### Objetivo

Pasar argumentos a un pipe.

La plantilla actual utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products | sort;
  track product.id
) {
  ...
}</code></pre>
</div>


Modifícala:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products | sort:'price';
  track product.id
) {
  ...
}</code></pre>
</div>


### Tareas

1. Predice el orden antes de guardar.
2. Comprueba el resultado.
3. Vuelve a ordenar por `title`.
4. Explica qué valor recibe `args` dentro de `transform()`.

---

## Ejercicio 8. Detecta un efecto secundario de `Array.sort`

### Objetivo

Comprender que `Array.sort()` modifica el array original.

El pipe actual contiene una operación equivalente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return value.sort((a, b) =&gt; {
  // comparación
});</code></pre>
</div>


### Tareas

1. Añade un `console.log(this.products)` antes y después de que la plantilla utilice el pipe.
2. Comprueba si el array original conserva su orden inicial.
3. Explica el motivo.

### Mejora

Evita modificar la referencia recibida:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return [...value].sort((a: Product, b: Product) =&gt; {
  if (a[args] &lt; b[args]) {
    return -1;
  }
&#8203;
  if (b[args] &lt; a[args]) {
    return 1;
  }
&#8203;
  return 0;
});</code></pre>
</div>


### Reflexión

¿Por qué esta versión es más coherente con una estrategia inmutable?

---

## Ejercicio 9. Añade orden ascendente y descendente

### Objetivo

Crear un pipe con más de un parámetro.

Modifica la firma:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">transform(
  value: Product[],
  property: keyof Product = 'title',
  direction: 'asc' | 'desc' = 'asc'
): Product[]</code></pre>
</div>


Una posible implementación del factor de orden:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const factor = direction === 'asc' ? 1 : -1;</code></pre>
</div>


y dentro del comparador:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">if (a[property] &lt; b[property]) {
  return -1 * factor;
}
&#8203;
if (b[property] &lt; a[property]) {
  return 1 * factor;
}
&#8203;
return 0;</code></pre>
</div>


Utilízalo así:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products | sort:'price':'desc';
  track product.id
) {
  ...
}</code></pre>
</div>


### Comprobación

El producto más caro debe aparecer antes que los demás.

---

## Ejercicio 10. Añade un nuevo producto de forma inmutable

### Objetivo

Relacionar pipes puros con cambios de referencia.

Añade un método:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addHeadphones(): void {
  this.products = [
    ...this.products,
    {
      id: 5,
      title: 'Headphones',
      price: 55,
      categories: {
        3: 'Multimedia'
      }
    }
  ];
}</code></pre>
</div>


Y un botón:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button (click)="addHeadphones()"&gt;
  Añadir Headphones
&lt;/button&gt;</code></pre>
</div>


### Tareas

1. Comprueba que el producto aparece.
2. Comprueba que el pipe vuelve a ordenar.
3. Sustituye temporalmente la asignación por `this.products.push(...)`.
4. Compara el comportamiento.

### Pregunta

¿Qué relación existe entre un pipe puro y el cambio de referencia del array?

---

## Ejercicio 11. Crea un pipe `discount`

### Objetivo

Crear un pipe desde cero.

Genera:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate pipe discount</code></pre>
</div>


El pipe recibirá un precio y un porcentaje de descuento:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Pipe, PipeTransform } from '@angular/core';
&#8203;
@Pipe({
  name: 'discount'
})
export class DiscountPipe implements PipeTransform {
  transform(
    price: number,
    percentage: number = 0
  ): number {
    return price - (price * percentage / 100);
  }
}</code></pre>
</div>


Importa el pipe en el componente correspondiente y úsalo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Oferta:
  {{ product()!.price | discount:10 | currency:'EUR' }}
&lt;/p&gt;</code></pre>
</div>


### Pregunta

¿En qué orden se ejecutan `discount` y `currency`?

---

## Ejercicio 12. Crea un pipe `categoryCount`

### Objetivo

Transformar un objeto en un valor derivado.

Crea un pipe que reciba:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">Record&lt;number, string&gt;</code></pre>
</div>


y devuelva el número de categorías.

Una implementación posible:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">transform(
  categories: Record&lt;number, string&gt;
): number {
  return Object.keys(categories).length;
}</code></pre>
</div>


Utilízalo:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Categorías:
  {{ product()!.categories | categoryCount }}
&lt;/p&gt;</code></pre>
</div>


---

## Ejercicio 13. Analiza `CopyrightDirective`

### Objetivo

Comprender qué elemento modifica una directiva de atributo.

El repositorio contiene:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;footer appCopyright&gt;&lt;/footer&gt;</code></pre>
</div>


y la directiva modifica:

- la clase CSS del elemento;
- su contenido textual;
- el año mostrado.

### Tareas

1. Abre `copyright.directive.ts`.
2. Identifica `ElementRef`.
3. Identifica `nativeElement`.
4. Cambia temporalmente el texto.
5. Aplica `appCopyright` sobre un `<p>` en vez de un `<footer>`.

### Pregunta

¿Por qué la directiva puede reutilizarse sobre distintos elementos HTML?

---

## Ejercicio 14. Amplía `CopyrightDirective`

### Objetivo

Modificar más de una propiedad del elemento host.

Añade, por ejemplo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">targetEl.setAttribute(
  'aria-label',
  'Información de copyright'
);</code></pre>
</div>


### Tareas

1. Inspecciona el elemento con DevTools.
2. Comprueba el nuevo atributo.
3. Añade también un atributo `title`.
4. Explica qué diferencia existe entre `textContent`, `classList` y `setAttribute`.

---

## Ejercicio 15. Comprueba `NumericDirective`

### Objetivo

Poner en uso una directiva que ya existe en el repositorio pero no se está utilizando en la plantilla principal.

Importa `NumericDirective` en `ProductListComponent` o en otro componente que vaya a utilizarla.

Después añade:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;label&gt;
  Cantidad:
  &lt;input appNumeric /&gt;
&lt;/label&gt;</code></pre>
</div>


### Tareas

1. Escribe números.
2. Escribe letras.
3. Observa las clases `valid` e `invalid` en DevTools.
4. Comprueba el comportamiento de `preventDefault()`.

### Pregunta

¿Qué parte realiza `@HostBinding` y qué parte realiza `@HostListener`?

---

## Ejercicio 16. Mejora la validación numérica

### Objetivo

Analizar las limitaciones de una directiva basada únicamente en `keypress`.

Prueba:

- pegar texto con el ratón;
- pegar con teclado;
- introducir caracteres desde distintos métodos de entrada.

### Reflexión

¿La directiva actual controla todos esos casos?

### Propuesta

Investiga cómo podría escucharse también el evento `input` o `paste` y documenta una posible mejora sin necesidad de sustituir todavía la implementación.

---

## Ejercicio 17. Crea una directiva `highlight`

### Objetivo

Crear una directiva visual reutilizable.

Genera:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate directive highlight</code></pre>
</div>


Una primera versión puede utilizar `ElementRef`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Directive,
  ElementRef
} from '@angular/core';
&#8203;
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(el: ElementRef&lt;HTMLElement&gt;) {
    el.nativeElement.style.fontWeight = '700';
  }
}</code></pre>
</div>


Úsala:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p appHighlight&gt;
  Texto destacado
&lt;/p&gt;</code></pre>
</div>


### Reto

Haz que la directiva también responda a `mouseenter` y `mouseleave` mediante `@HostListener`.

---

## Ejercicio 18. Directiva para indicar poco stock

### Objetivo

Relacionar una directiva con un caso de negocio.

Amplía temporalmente `Product` con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">stock: number;</code></pre>
</div>


Crea una directiva `LowStockDirective` y aplícala sobre el elemento que muestra el stock.

La idea es que añada una clase:

<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">.low-stock {
  font-weight: 700;
  text-decoration: underline;
}</code></pre>
</div>


cuando corresponda.

> 💡 Este ejercicio puede ampliarse posteriormente cuando se estudien inputs en directivas.

---

# 30. Reto integrador: catálogo enriquecido 🚀

### Objetivo

Combinar pipes y directivas en una evolución del catálogo del repositorio.

## Parte 1. Modelo

Amplía `Product`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  title: string;
  price: number;
  categories: Record&lt;number, string&gt;;
  stock: number;
}</code></pre>
</div>


## Parte 2. Pipes

La aplicación deberá incluir:

- `currency` para precios;
- `keyvalue` para categorías;
- `lowercase` o `uppercase`;
- `SortPipe`;
- un pipe personalizado `DiscountPipe`.

El listado deberá poder mostrarse ordenado:

- por título;
- por precio ascendente;
- por precio descendente.

## Parte 3. Directivas

La aplicación deberá utilizar:

- `appCopyright`;
- `appNumeric`;
- una directiva personalizada adicional.

## Parte 4. Cantidad

Añade al detalle:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;label&gt;
  Cantidad:
  &lt;input appNumeric /&gt;
&lt;/label&gt;</code></pre>
</div>


## Parte 5. Producto en oferta

Muestra:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Precio:
  {{ selectedProduct.price | currency:'EUR' }}
&lt;/p&gt;
&#8203;
&lt;p&gt;
  Precio con 10 % de descuento:
  {{
    selectedProduct.price
      | discount:10
      | currency:'EUR'
  }}
&lt;/p&gt;</code></pre>
</div>


## Parte 6. Comprobaciones

- [ ] Los productos siguen tipados mediante `Product`.
- [ ] El listado utiliza el pipe `sort`.
- [ ] `sort` no modifica el array original.
- [ ] El pipe admite al menos un parámetro.
- [ ] Se utilizan al menos tres pipes integrados.
- [ ] Existe al menos un pipe personalizado adicional.
- [ ] `appCopyright` sigue funcionando.
- [ ] `appNumeric` está realmente aplicado en un input.
- [ ] Existe una nueva directiva de atributo.
- [ ] El proyecto compila sin errores.
- [ ] Los bloques de plantilla siguen siendo legibles y no contienen lógica innecesariamente compleja.

---

# 31. Diagnóstico de errores 🔎

Antes de corregir cada fragmento, explica qué esperas que ocurra.

## Caso A: pipe no importado

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">{{ product.price | currency:'EUR' }}</code></pre>
</div>


pero el componente no importa `CommonModule` ni `CurrencyPipe`.

**Pregunta:** ¿qué problema debe aparecer?

---

## Caso B: pipe personalizado no importado

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products | sort;
  track product.id
) {
  ...
}</code></pre>
</div>


pero `SortPipe` no aparece en `imports`.

**Pregunta:** ¿por qué Angular no reconoce `sort`?

---

## Caso C: parámetro inválido de `SortPipe`

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">products | sort:'description'</code></pre>
</div>


si `description` no pertenece a `Product`.

**Pregunta:** ¿qué papel juega `keyof Product`?

---

## Caso D: directiva no importada

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;footer appCopyright&gt;&lt;/footer&gt;</code></pre>
</div>


pero `CopyrightDirective` no aparece en `imports`.

**Pregunta:** ¿por qué el simple atributo HTML no es suficiente para activar la directiva Angular?

---

## Caso E: mutación silenciosa

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return value.sort(...);</code></pre>
</div>


**Pregunta:** ¿qué efecto secundario tiene sobre `value`?

---

## Caso F: pipe impuro innecesario

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Pipe({
  name: 'sort',
  pure: false
})</code></pre>
</div>


**Pregunta:** ¿qué coste potencial tiene ejecutar un pipe impuro en cada ciclo de detección de cambios?

---

# 32. Cuestiones finales de reflexión 📝

1. ¿Qué diferencia fundamental existe entre un pipe y una directiva?
2. ¿Cuándo utilizarías `currency`?
3. ¿Por qué `keyvalue` es útil con `Record<number, string>`?
4. ¿Qué ventaja aporta `@let` en una plantilla compleja?
5. ¿Qué función tiene `transform()`?
6. ¿Qué aporta `keyof Product` al pipe `sort`?
7. ¿Por qué conviene evitar modificar directamente el array dentro de un pipe?
8. ¿Qué diferencia existe entre un pipe puro y uno impuro?
9. ¿Por qué una actualización inmutable favorece a un pipe puro?
10. ¿Qué diferencia existe entre un componente y una directiva?
11. ¿Qué representa el selector `[appCopyright]`?
12. ¿Qué proporciona `ElementRef`?
13. ¿Qué es `nativeElement`?
14. ¿Qué función cumple `@HostBinding`?
15. ¿Qué función cumple `@HostListener`?
16. ¿Qué contiene `$event` en `onKeyPress`?
17. ¿Qué hace `preventDefault()` en `NumericDirective`?
18. ¿Qué limitaciones puede tener una validación basada solo en `keypress`?
19. ¿Cuándo crearías una directiva en vez de un componente?
20. ¿Qué parte de esta unidad consideras más reutilizable entre diferentes aplicaciones Angular?

---

> ✅ **Meta de aprendizaje**  
> Si puedes crear un pipe parametrizable, explicar por qué conviene mantenerlo puro y construir una directiva que modifique o responda al elemento host, ya dominas los fundamentos de pipes y directivas en Angular.
