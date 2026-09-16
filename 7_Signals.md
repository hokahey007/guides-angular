# 🔄 Unidad 7: Seguimiento del estado de la aplicación con Signals


<style>
.code-card {
  margin: 1.35rem 0;
  border: 1px solid #d0d7de;
  border-radius: 10px;
  overflow: hidden;
  background: #f6f8fa;
  box-shadow: 0 2px 8px rgba(31, 35, 40, 0.08);
}
.code-card .code-label {
  display: inline-block;
  margin: 0;
  padding: 0.35rem 0.8rem;
  background: #111111;
  color: #ffffff;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  font-size: 0.72rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  border-bottom-right-radius: 7px;
}
.code-card pre {
  margin: 0;
  padding: 1rem 1.15rem 1.15rem;
  overflow-x: auto;
  background: #f6f8fa;
  line-height: 1.55;
  tab-size: 2;
}
.code-card code {
  font-family: "Cascadia Code", "JetBrains Mono", Consolas, "Courier New", monospace;
  font-size: 0.92rem;
  color: #24292f;
  background: transparent;
  white-space: pre;
}
.code-card[data-lang="typescript"] pre,
.code-card[data-lang="javascript"] pre {
  background: #f7f9fc;
}
.code-card[data-lang="html"] pre {
  background: #fff8f5;
}
.code-card[data-lang="css"] pre {
  background: #f8fbff;
}
.code-card[data-lang="bash"] pre,
.code-card[data-lang="shell"] pre {
  background: #f7fbf7;
}
.code-card[data-lang="json"] pre,
.code-card[data-lang="text"] pre {
  background: #fafafa;
}
:not(pre) > code {
  padding: 0.12em 0.38em;
  border-radius: 4px;
  background: #eef1f4;
  color: #24292f;
  font-family: "Cascadia Code", "JetBrains Mono", Consolas, monospace;
  font-size: 0.92em;
}
</style>



> **Referencia del PDF:** capítulo 7 de Angular 19.  
> **Objetivo:** comprender el modelo reactivo basado en **Signals**, aprender a crear y derivar estado reactivo y utilizar la interoperabilidad entre Signals y RxJS.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Explicar qué aporta la Signals API al mecanismo de detección de cambios.
- Crear y modificar writable signals.
- Leer valores de signals desde TypeScript y desde plantillas.
- Utilizar `set()` y `update()`.
- Crear computed signals.
- Comprender la memoización de valores derivados.
- Identificar situaciones donde Signals mejoran la gestión del estado.
- Comprender la relación entre Signals y la detección de cambios.
- Integrar Signals con RxJS mediante `toSignal()`.
- Sustituir determinados consumos con `AsyncPipe` por Signals cuando resulte conveniente.

---


---

## 📑 Contenidos

1. [[#1. ¿Por qué Signals?|¿Por qué Signals?]]
2. [[#2. Crear y leer un writable signal|Crear y leer un writable signal]]
3. [[#5. Computed Signals|Computed Signals]]
4. [[#7. Signals y RxJS|Signals y RxJS]]
5. [[#9. Convertir productos de Observable a Signal|Convertir productos de Observable a Signal]]
6. [[#13. Resumen de la unidad|Resumen de la unidad]]
7. [[#15. Taller práctico: estado reactivo con Signals 🧪|Taller práctico: estado reactivo con Signals 🧪]]
8. [[#16. Reto integrador: panel reactivo de catálogo 🚀|Reto integrador: panel reactivo de catálogo 🚀]]
9. [[#17. Diagnóstico de errores con Signals 🔎|Diagnóstico de errores con Signals 🔎]]

---

# 1. ¿Por qué Signals?

En aplicaciones Angular tradicionales, **Zone.js** participa activamente en la detección de cambios. Cuando se produce determinados eventos, Angular puede iniciar un ciclo de *change detection* y revisar bindings de los componentes.

El problema es que Angular no sabe de forma automática dónde se ha producido exactamente un cambio. Para limitar comprobaciones innecesarias, históricamente se han utilizado técnicas como:

- `ChangeDetectionStrategy.OnPush`;
- interacción manual mediante `ChangeDetectorRef`.

**Signals** proporcionan un modelo reactivo más preciso: permiten seguir los cambios de estado y notificar al framework qué consumidores dependen del valor modificado.

> 🧠 **Idea clave**  
> Un signal actúa como un contenedor reactivo. Cuando cambia su valor, Angular conoce que debe actualizar a quienes lo consumen.

Un signal puede cambiar:

- directamente, mediante un **writable signal**;
- indirectamente, mediante un **computed signal**.

---

# 2. Crear y leer un writable signal

Importamos `signal`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  inject,
  signal
} from '@angular/core';</code></pre>
</div>



Creamos un signal con la fecha actual:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">currentDate = signal(new Date());</code></pre>
</div>



Para escribir un nuevo valor utilizamos `set`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.currentDate.set(
  new Date()
);</code></pre>
</div>



Para leerlo, invocamos el signal como si fuera una función:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.title =
  `${this.settings.title} (${this.currentDate()})`;</code></pre>
</div>



La llamada:

`this.currentDate()`

devuelve el valor actual almacenado en el signal.

---

# 3. Casos adecuados para utilizar Signals

El material propone Signals especialmente para situaciones donde la aplicación necesita reaccionar con frecuencia a cambios de estado.

Ejemplos:

- dashboards con widgets y datos en tiempo real;
- aplicaciones bursátiles;
- componentes que muestran solo determinadas partes de objetos grandes o complejos.

Supongamos un objeto de pedido:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const order = {
  no: '1',
  date: new Date(),
  products: [
    {
      id: 1,
      title: 'Keyboard',
      price: 100
    },
    {
      id: 2,
      title: 'Microphone',
      price: 35
    }
  ],
  customerCode: '0002',
  isCompleted: false
};</code></pre>
</div>



Podemos conservar en un signal únicamente las propiedades que interesan a la vista:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const orderDetails = signal({
  no: '1',
  customerCode: '0002',
  isCompleted: false
});</code></pre>
</div>



Así evitamos implicar todo el objeto original en el estado reactivo que necesitamos observar.

---

# 4. Modificar un signal con `update()`

Además de `set`, podemos utilizar `update` cuando el nuevo valor depende del valor actual:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.currentDate.update(d =&gt; {
  return new Date(
    d.getFullYear(),
    d.getMonth(),
    d.getDate(),
    0,
    0
  );
});</code></pre>
</div>



En este caso:

1. `d` contiene el valor actual del signal.
2. La función devuelve el nuevo valor.
3. Angular recibe la notificación de que el signal ha cambiado.

### `set()` frente a `update()`

| Método | Uso |
|---|---|
| `set(valor)` | Sustituye directamente el valor |
| `update(fn)` | Calcula el nuevo valor a partir del anterior |

---

# 5. Computed Signals

Un **computed signal** depende de otros signals.

Características:

- es de solo lectura;
- no se modifica con `set`;
- no se modifica con `update`;
- cambia indirectamente cuando cambian sus dependencias.

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  inject,
  Signal,
  computed,
  signal
} from '@angular/core';</code></pre>
</div>



Declaramos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title: Signal&lt;string&gt; =
  signal('');</code></pre>
</div>



Y calculamos su valor:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  this.title$.subscribe(
    this.setTitle
  );
&#8203;
  this.title = computed(() =&gt; {
    return `${this.settings.title} ` +
      `(${this.currentDate()})`;
  });
}</code></pre>
</div>



La plantilla accede al valor mediante:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;header&gt;
  {{ title() }}
&lt;/header&gt;</code></pre>
</div>



El valor de `title` depende de `currentDate`. Cuando cambia `currentDate`, el valor derivado puede recalcularse.

---

# 6. Ventajas de los computed signals

El PDF destaca tres características de rendimiento:

- la función `computed` se ejecuta cuando el valor se lee por primera vez;
- se recalcula únicamente cuando cambian los signals de los que depende;
- utiliza un mecanismo de caché o **memoización**.

Conceptualmente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">currentDate (writable signal)
        │
        │ cambia
        ▼
title (computed signal)
        │
        ▼
Plantilla</code></pre>
</div>



---

# 7. Signals y RxJS

Signals y RxJS aportan reactividad, pero no son exactamente lo mismo.

Según el material, Signals ofrece:

- reactividad más granular;
- un enfoque más imperativo;
- mejor integración con la detección de cambios.

RxJS continúa siendo especialmente importante porque diferentes APIs de Angular siguen utilizando observables, como:

- HTTP;
- router.

> 📌 Signals no se presenta como un sustituto directo de RxJS, sino como otro mecanismo reactivo que puede colaborar con él.

---

# 8. `@angular/core/rxjs-interop`

Angular proporciona utilidades específicas de interoperabilidad.

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  inject
} from '@angular/core';
&#8203;
import {
  toSignal
} from '@angular/core/rxjs-interop';</code></pre>
</div>



`toSignal` permite convertir un observable en un signal.

---

# 9. Convertir productos de Observable a Signal

Si `ProductsService.getProducts()` devuelve un observable:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products = toSignal(
  inject(ProductsService)
    .getProducts(),
  {
    initialValue: []
  }
);</code></pre>
</div>



Se proporcionan:

1. el observable origen;
2. opcionalmente un valor inicial.

En este caso, el valor inicial es un array vacío.

---

# 10. Consumir el signal en la plantilla

Una vez convertido, ya no necesitamos `AsyncPipe`.

Ejemplo:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (products().length &gt; 0) {
  &lt;h1&gt;
    Products ({{ products().length }})
  &lt;/h1&gt;
}
&#8203;
&lt;ul class="pill-group"&gt;
  @for (
    product of products() | sort;
    track product.id
  ) {
    &lt;li
      class="pill"
      (click)="selectedProduct = product"
    &gt;
      @switch (product.title) {
        @case ('Keyboard') { ⌨️ }
        @case ('Microphone') { 🎙️ }
        @default { 🏷️ }
      }
&#8203;
      {{ product.title }}
    &lt;/li&gt;
  } @empty {
    &lt;p&gt;No products found!&lt;/p&gt;
  }
&lt;/ul&gt;
&#8203;
&lt;app-product-detail
  [product]="selectedProduct"
  (added)="onAdded()"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>



El signal se lee mediante:

`products()`

en lugar de utilizar:

`products$ | async`

---

# 11. Componente simplificado con `toSignal`

El componente puede reducirse a:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  inject
} from '@angular/core';
&#8203;
import {
  toSignal
} from '@angular/core/rxjs-interop';
&#8203;
import { Product }
  from '../product';
&#8203;
import {
  ProductDetailComponent
} from '../product-detail/product-detail.component';
&#8203;
import { SortPipe }
  from '../sort.pipe';
&#8203;
import {
  ProductsService
} from '../products.service';
&#8203;
@Component({
  selector: 'app-product-list',
  imports: [
    ProductDetailComponent,
    SortPipe
  ],
  templateUrl:
    './product-list.component.html',
  styleUrl:
    './product-list.component.css'
})
export class ProductListComponent {
  selectedProduct:
    Product | undefined;
&#8203;
  products = toSignal(
    inject(ProductsService)
      .getProducts(),
    {
      initialValue: []
    }
  );
&#8203;
  onAdded() {
    alert(
      `${this.selectedProduct?.title} ` +
      `added to the cart!`
    );
  }
}</code></pre>
</div>



> ✅ El resultado es un componente más compacto: desaparecen `AsyncPipe`, la propiedad observable y la suscripción manual.

---

# 12. Resumen de Signals

```text
signal()
   │
   ├── set()
   ├── update()
   └── lectura con ()
         │
         ▼
     consumidores

computed()
   │
   └── depende de otros signals

Observable
   │
   └── toSignal()
         │
         ▼
       Signal
```

---

---

# 13. Resumen de la unidad

En esta unidad hemos aprendido a utilizar **Signals** como mecanismo reactivo integrado en Angular.

Hemos trabajado con:

- `signal()`;
- lectura de signals mediante `()`;
- `set()`;
- `update()`;
- `computed()`;
- memoización;
- relaciones entre writable signals y computed signals;
- interoperabilidad entre RxJS y Signals;
- `toSignal()`.

Los Signals permiten representar estado reactivo de forma sencilla y granular, facilitando que Angular conozca qué partes de la aplicación dependen de un valor y deben actualizarse cuando este cambia.

---

# 14. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Signal | Contenedor reactivo de estado |
| Writable signal | Signal cuyo valor puede modificarse |
| `signal()` | Crea un writable signal |
| `set()` | Sustituye directamente el valor |
| `update()` | Calcula el nuevo valor a partir del actual |
| `computed()` | Crea un signal derivado y de solo lectura |
| Memoización | Reutilización de un valor calculado mientras no cambien sus dependencias |
| Consumidor | Código que lee un signal |
| Reactividad granular | Actualización centrada en los consumidores afectados |
| `toSignal()` | Convierte un Observable en un Signal |
| RxJS interop | Utilidades para integrar Observables y Signals |

---

## 🔗 Recursos citados en el material

- Angular Signals: <https://angular.dev/guide/signals>
- Angular RxJS interop: <https://angular.dev/ecosystem/rxjs-interop>

---

> 🎓 **Siguiente paso**  
> Una vez dominada la gestión reactiva del estado con Signals, la siguiente unidad aborda la **comunicación con servicios de datos mediante HTTP**.

---

# 15. Taller práctico: estado reactivo con Signals 🧪

Los ejercicios de esta sección toman como referencia el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/07_se%C3%B1ales>

El repositorio ya contiene dos escenarios especialmente útiles para practicar:

- `AppComponent` combina un `Observable` periódico con un writable signal `currentDate` y un computed signal `title`;
- `ProductListComponent` convierte el `Observable<Product[]>` de `ProductsService` en un signal mediante `toSignal()`.

> 🎯 **Forma recomendada de trabajo**  
> Realiza los ejercicios en orden. Antes de modificar un signal, intenta predecir qué consumidores se actualizarán y si el cambio requiere `set()`, `update()` o un `computed()`.

---

## Ejercicio 1. Localiza los Signals del proyecto

### Objetivo

Reconocer qué valores del repositorio ya utilizan Signals.

### Tareas

Abre:

- `app.component.ts`;
- `app.component.html`;
- `product-list.component.ts`;
- `product-list.component.html`.

Completa:

| Elemento | Tipo | Cómo se lee |
|---|---|---|
| `currentDate` | Writable signal |  |
| `title` | Computed signal |  |
| `products` | Signal obtenido desde RxJS |  |

### Preguntas

1. ¿Por qué en la plantilla se escribe `title()`?
2. ¿Por qué el listado utiliza `products()` en lugar de `products$ | async`?
3. ¿Qué dependencia utiliza `title` para calcular su valor?

---

## Ejercicio 2. Practica `set()`

### Objetivo

Modificar directamente el valor de un writable signal.

Añade temporalmente a `AppComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">resetDate(): void {
  this.currentDate.set(
    new Date(2000, 0, 1)
  );
}</code></pre>
</div>


Y en la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button (click)="resetDate()"&gt;
  Reiniciar fecha
&lt;/button&gt;</code></pre>
</div>


### Tareas

1. Espera a que el título se actualice varias veces.
2. Pulsa el botón.
3. Comprueba el valor mostrado.
4. Explica por qué también cambia el computed signal `title`.

---

## Ejercicio 3. Practica `update()`

### Objetivo

Calcular el nuevo valor a partir del valor actual.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addOneDay(): void {
  this.currentDate.update(date =&gt; {
    const next = new Date(date);
    next.setDate(
      next.getDate() + 1
    );
    return next;
  });
}</code></pre>
</div>


### Pregunta

¿Por qué `update()` expresa mejor la intención que `set()` cuando necesitamos conocer el valor anterior?

---

## Ejercicio 4. `set()` frente a `update()`

### Objetivo

Distinguir correctamente ambos mecanismos.

Indica cuál usarías en cada caso:

1. Sustituir el nombre del usuario por `"Ana"`.
2. Incrementar un contador.
3. Vaciar una cesta de compra.
4. Añadir uno al número de notificaciones.
5. Establecer una fecha recibida desde un formulario.

Después implementa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">counter = signal(0);
&#8203;
increment(): void {
  this.counter.update(
    value =&gt; value + 1
  );
}
&#8203;
resetCounter(): void {
  this.counter.set(0);
}</code></pre>
</div>


---

## Ejercicio 5. Comprueba cuándo se recalcula un `computed()`

### Objetivo

Observar la relación entre dependencias y valores calculados.

Modifica temporalmente el computed del título:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.title = computed(() =&gt; {
  console.log(
    'Calculando title'
  );
&#8203;
  return `${this.settings.title} ` +
    `(${this.currentDate()})`;
});</code></pre>
</div>


### Tareas

1. Recarga la aplicación.
2. Observa la consola.
3. Cambia `currentDate`.
4. Observa cuándo vuelve a ejecutarse el cálculo.
5. Añade una propiedad normal que no sea signal y modifícala.
6. Comprueba si eso, por sí solo, se convierte en una dependencia del computed.

### Reflexión

¿Qué significa que las dependencias de un computed se rastrean de forma reactiva?

---

## Ejercicio 6. Crea un segundo writable signal

### Objetivo

Hacer que un computed dependa de más de un signal.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">prefix = signal('Tienda');
&#8203;
title = computed(() =&gt; {
  return `${this.prefix()} - ` +
    `${this.settings.title} ` +
    `(${this.currentDate()})`;
});</code></pre>
</div>


Crea un botón que cambie `prefix`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">changePrefix(): void {
  this.prefix.set(
    'Catálogo'
  );
}</code></pre>
</div>


### Pregunta

¿Cuántas dependencias reactivas tiene ahora `title`?

---

## Ejercicio 7. Computed signal de número de productos

### Objetivo

Derivar información a partir del signal `products`.

En `ProductListComponent`, añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productCount = computed(
  () =&gt; this.products().length
);</code></pre>
</div>


Recuerda importar `computed`.

Muestra:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Total:
  {{ productCount() }}
&lt;/p&gt;</code></pre>
</div>


### Pregunta

¿Por qué no necesitamos almacenar manualmente el número de productos en otra propiedad?

---

## Ejercicio 8. Calcula el precio total mediante `computed()`

### Objetivo

Crear estado derivado de una colección.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">totalPrice = computed(() =&gt; {
  return this.products()
    .reduce(
      (total, product) =&gt;
        total + product.price,
      0
    );
});</code></pre>
</div>


Y muestra:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Valor del catálogo:
  {{ totalPrice() }} €
&lt;/p&gt;</code></pre>
</div>


### Tareas

1. Comprueba el resultado.
2. Cambia temporalmente un precio en `ProductsService`.
3. Recarga la aplicación.
4. Verifica que no has escrito código adicional para recalcular el total.

---

## Ejercicio 9. Crea un filtro reactivo de productos

### Objetivo

Combinar un writable signal con un computed signal.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">searchText = signal('');
&#8203;
filteredProducts = computed(() =&gt; {
  const text =
    this.searchText()
      .trim()
      .toLowerCase();
&#8203;
  return this.products()
    .filter(product =&gt;
      product.title
        .toLowerCase()
        .includes(text)
    );
});</code></pre>
</div>


En la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  #search
  placeholder="Buscar producto"
/&gt;
&#8203;
&lt;button
  (click)="searchText.set(
    search.value
  )"
&gt;
  Buscar
&lt;/button&gt;</code></pre>
</div>


Sustituye en el `@for`:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of filteredProducts() | sort;
  track product.id
) {
  ...
}</code></pre>
</div>


### Pregunta

¿Qué valores provocan ahora que `filteredProducts` pueda cambiar?

---

## Ejercicio 10. Limpia el filtro

### Objetivo

Comprobar que el estado reactivo puede reiniciarse fácilmente.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">clearSearch(): void {
  this.searchText.set('');
}</code></pre>
</div>


Y un botón:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button (click)="clearSearch()"&gt;
  Mostrar todos
&lt;/button&gt;</code></pre>
</div>


Comprueba que no necesitas reconstruir manualmente el array original.

---

## Ejercicio 11. Analiza `toSignal()`

### Objetivo

Comprender la interoperabilidad entre RxJS y Signals.

El repositorio contiene una expresión equivalente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products = toSignal(
  inject(ProductsService)
    .getProducts(),
  {
    initialValue: []
  }
);</code></pre>
</div>


### Responde

1. ¿Qué recibe `toSignal()`?
2. ¿Qué devuelve?
3. ¿Para qué sirve `initialValue`?
4. ¿Qué ocurriría en la plantilla antes de la primera emisión si no existiera un valor inicial?
5. ¿Por qué ya no necesitamos `AsyncPipe` en este componente?

---

## Ejercicio 12. Experimenta sin `initialValue`

### Objetivo

Observar cómo cambia el tipo cuando el signal no dispone de valor inicial.

Elimina temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  initialValue: []
}</code></pre>
</div>


### Tareas

1. Observa el tipo inferido por VS Code.
2. Revisa los errores que puedan aparecer en la plantilla.
3. Explica por qué Angular/TypeScript debe contemplar `undefined`.
4. Restaura `initialValue: []`.

---

## Ejercicio 13. Compara Observable y Signal en la plantilla

### Objetivo

Relacionar esta unidad con la unidad anterior.

Escribe una comparación breve entre:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let products =
  (products$ | async)!;</code></pre>
</div>


y:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products();
  track product.id
) {
  ...
}</code></pre>
</div>


### Cuestiones

- ¿Cuál necesita `AsyncPipe`?
- ¿Cuál se lee invocando una función?
- ¿Cuál nace de un Observable convertido con `toSignal()`?
- ¿Qué forma te resulta más directa para estado utilizado repetidamente en una plantilla?

---

## Ejercicio 14. Selección de producto mediante signal

### Objetivo

Convertir un estado local sencillo en signal.

Cambia:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">selectedProduct:
  Product | undefined;</code></pre>
</div>


por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">selectedProduct =
  signal&lt;Product | undefined&gt;(
    undefined
  );</code></pre>
</div>


En el clic:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">(click)="
  selectedProduct.set(product)
" </code></pre>
</div>


Y en el detalle:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [product]="selectedProduct()"
  (added)="onAdded()"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>


Actualiza también `onAdded()` para leer el signal.

### Reflexión

¿Era obligatorio convertir esta propiedad en signal? ¿Qué ventaja conceptual puede aportar si otros valores derivados empiezan a depender de la selección?

---

## Ejercicio 15. Deriva el título del producto seleccionado

### Objetivo

Crear un computed dependiente de un signal opcional.

Partiendo del ejercicio anterior:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">selectedTitle = computed(() =&gt; {
  return this.selectedProduct()
    ?.title
    ?? 'Ningún producto';
});</code></pre>
</div>


Muestra:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Selección:
  {{ selectedTitle() }}
&lt;/p&gt;</code></pre>
</div>


Comprueba que cambia automáticamente al seleccionar productos.

---

# 16. Reto integrador: panel reactivo de catálogo 🚀

### Objetivo

Aplicar writable signals, computed signals y `toSignal()` sobre el código real del repositorio.

Amplía `ProductListComponent` con:

- `products`, obtenido mediante `toSignal()`;
- `searchText`, writable signal;
- `selectedProduct`, writable signal;
- `filteredProducts`, computed;
- `productCount`, computed;
- `totalPrice`, computed;
- `selectedTitle`, computed.

La interfaz deberá mostrar:

1. una caja de búsqueda;
2. el número de productos visibles;
3. el valor económico de los productos visibles;
4. el listado filtrado;
5. el producto seleccionado.

### Condiciones

- [ ] No duplicar manualmente datos derivados.
- [ ] Utilizar `set()` al menos una vez.
- [ ] Utilizar `update()` al menos una vez.
- [ ] Utilizar al menos tres `computed()`.
- [ ] Mantener `ProductsService.getProducts()` como Observable.
- [ ] Convertir el Observable mediante `toSignal()`.
- [ ] No utilizar `AsyncPipe` en `ProductListComponent`.
- [ ] Leer los signals con `()`.
- [ ] El proyecto debe compilar sin errores.

---

# 17. Diagnóstico de errores con Signals 🔎

## Caso A

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">currentDate =
  signal(new Date());
&#8203;
console.log(currentDate);</code></pre>
</div>


**Pregunta:** ¿estamos mostrando la fecha o el signal?

---

## Caso B

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title = computed(() =&gt; {
  return this.currentDate;
});</code></pre>
</div>


**Pregunta:** ¿qué falta para leer el valor del signal?

---

## Caso C

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title = computed(() =&gt; {
  return 'Angular';
});
&#8203;
this.title.set('Otro título');</code></pre>
</div>


**Pregunta:** ¿por qué no puede utilizarse `set()` sobre un computed signal?

---

## Caso D

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">{{ products.length }}</code></pre>
</div>


si `products` es un signal.

**Pregunta:** ¿qué sintaxis correcta debe utilizar la plantilla?

---

## Caso E

Se elimina `initialValue` de `toSignal()` y la plantilla asume que siempre existe un array.

**Pregunta:** ¿qué posibilidad adicional debe contemplar el tipo?

---

# 18. Cuestiones finales de reflexión 📝

1. ¿Qué es un signal?
2. ¿Qué diferencia existe entre writable signal y computed signal?
3. ¿Cómo se lee un signal?
4. ¿Qué diferencia existe entre `set()` y `update()`?
5. ¿Por qué un computed signal es de solo lectura?
6. ¿Cómo descubre Angular las dependencias de un computed?
7. ¿Qué significa memoización en este contexto?
8. ¿Qué ventaja aporta no almacenar manualmente datos derivados?
9. ¿Para qué sirve `toSignal()`?
10. ¿Qué función cumple `initialValue`?
11. ¿Signals sustituye completamente a RxJS? Razona la respuesta según lo trabajado en la unidad.
12. ¿Qué diferencia existe entre `products()` y `products$ | async`?
13. ¿Cuándo puede ser útil representar la selección actual como signal?
14. ¿Por qué un filtro reactivo encaja bien con un computed signal?
15. ¿Qué estados de tu propia aplicación representarías con Signals?

---

> ✅ **Meta de aprendizaje**  
> Si puedes distinguir estado fuente de estado derivado, elegir correctamente entre `set()`, `update()` y `computed()`, y convertir un Observable con `toSignal()`, tienes una base sólida para trabajar con Signals en Angular.
