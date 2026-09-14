# 🧰 Unidad 5: Gestión de tareas complejas con servicios


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



> **Referencia:** Angular 19  
> **Objetivo:** aprender a separar la lógica de negocio de los componentes mediante servicios y comprender el sistema de **inyección de dependencias (DI)** de Angular, sus inyectores, proveedores y mecanismos de resolución.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Explicar qué es la **Dependency Injection (DI)**.
- Crear servicios Angular con `ng generate service`.
- Utilizar `@Injectable`.
- Inyectar servicios mediante constructor.
- Utilizar la función `inject()`.
- Comprender el papel de `providedIn`.
- Diferenciar entre **root injector** y **element injectors**.
- Compartir servicios en un árbol de componentes.
- Limitar el alcance de un servicio.
- Crear múltiples instancias de un servicio cuando sea necesario.
- Utilizar `@Host`, `@Optional`, `@Self` y `@SkipSelf`.
- Sobrescribir proveedores con `useClass`.
- Crear proveedores con `useFactory`.
- Proporcionar valores con `useValue`.
- Utilizar `InjectionToken` para valores que no son clases.
- Aplicar el principio de **Separation of Concerns (SoC)**.

---

## 📑 Contenidos

1. [Introducción a Dependency Injection](#2-introducción-a-dependency-injection)
2. [Crear e inyectar servicios](#3-crear-nuestro-primer-servicio-angular)
3. [Proveedores e inyectores](#8-proveedores-e-inyectores)
4. [Jerarquía y alcance de servicios](#11-jerarquía-de-inyectores)
5. [Providers avanzados](#24-sintaxis-completa-de-providers)
6. [InjectionToken y configuración](#27-proporcionar-valores-con-usevalue)
7. [Taller práctico](#34-taller-práctico-servicios-e-inyección-de-dependencias)
8. [Reto integrador](#35-reto-integrador-catálogo-con-servicios-y-scopes)
9. [Diagnóstico y reflexión](#36-diagnóstico-de-errores-de-di)

---

# 1. El problema: demasiada lógica dentro de los componentes

A medida que una aplicación crece, colocar toda la lógica dentro de los componentes hace que el código sea difícil de mantener.

En capítulos anteriores, la lista de productos estaba declarada directamente dentro de `ProductListComponent`:


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



Este enfoque presenta dos problemas:

- En aplicaciones reales, los datos suelen proceder de una API o de una fuente externa.
- El componente queda demasiado acoplado a la obtención de datos.

> 🧠 **Idea clave**  
> Un componente debería concentrarse principalmente en la **lógica de presentación**. La lógica de negocio y acceso a datos debe delegarse en servicios.

---

# 2. Introducción a Dependency Injection

La **inyección de dependencias** es un patrón de diseño utilizado también en otros lenguajes como Java o C#.

Una clase puede necesitar objetos externos para trabajar. Esos objetos son sus **dependencias**.

La DI separa:

```text
CONSUMIDOR
    │
    │ necesita
    ▼
DEPENDENCIA
    ▲
    │ creada y suministrada por
    │
INYECTOR
```

El consumidor:

- no necesita saber cómo crear la dependencia;
- solo necesita saber cómo utilizarla.

Angular incluye un sistema completo de DI para componentes, directivas, pipes y servicios.

---

# 3. Crear nuestro primer servicio Angular

Para generar un servicio:



<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng generate service products</code></pre>
</div>



Angular CLI crea:

```text
products.service.ts
products.service.spec.ts
```

Un servicio generado tiene una estructura similar a:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { Injectable } from '@angular/core';
&#8203;
@Injectable({
  providedIn: 'root'
})
export class ProductsService {
  constructor() {}
}</code></pre>
</div>



`@Injectable` marca la clase como un servicio que puede participar en el sistema de DI.

La propiedad:

`providedIn: 'root'`

indica que el servicio será proporcionado por el **inyector raíz** de la aplicación.

> 📌 Un servicio debería tener un contexto funcional claro.  
> Por ejemplo, productos en `ProductsService` y pedidos en un servicio diferente.

---

# 4. Añadir lógica a `ProductsService`

Importamos la interfaz:



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { Product } from './product';</code></pre>
</div>



Y creamos `getProducts()`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">getProducts(): Product[] {
  return [
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
  ];
}</code></pre>
</div>



Ahora la obtención de productos deja de ser responsabilidad directa del componente.

---

# 5. Primer intento: crear el servicio manualmente

Podríamos escribir en el componente:



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">private productService: ProductsService;
&#8203;
constructor() {
  this.productService = new ProductsService();
}</code></pre>
</div>



Y cargar los datos en `ngOnInit`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">ngOnInit(): void {
  this.products = this.productService.getProducts();
}</code></pre>
</div>



Aunque funciona, tiene varios inconvenientes:

- el componente sabe cómo crear el servicio;
- queda acoplado a su implementación;
- cada componente puede crear una nueva instancia;
- resulta más difícil sustituir o simular el servicio en pruebas;
- dificulta utilizar un servicio singleton.

La DI de Angular evita estos problemas.

---

# 6. Inyección mediante constructor

Angular permite solicitar el servicio directamente en el constructor:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class ProductListComponent implements OnInit {
  products: Product[] = [];
  selectedProduct: Product | undefined;
&#8203;
  constructor(
    private productService: ProductsService
  ) {}
&#8203;
  onAdded() {
    alert(
      `${this.selectedProduct?.title} added to the cart!`
    );
  }
&#8203;
  ngOnInit(): void {
    this.products =
      this.productService.getProducts();
  }
}</code></pre>
</div>



Angular se encarga de proporcionar la instancia.

> ✅ Es aconsejable declarar servicios inyectados como `readonly` cuando no deben reasignarse.



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">constructor(
  private readonly productService: ProductsService
) {}</code></pre>
</div>



---

# 7. Inyección mediante `inject()`

Angular también proporciona la función `inject()`.

Importamos:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import {
  Component,
  OnInit,
  inject
} from '@angular/core';</code></pre>
</div>



Y declaramos:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">private productService =
  inject(ProductsService);</code></pre>
</div>



En este caso ya no necesitamos constructor.

`inject()` es especialmente útil cuando:

- el constructor tendría demasiadas dependencias;
- trabajamos en contextos donde no existe constructor;
- trabajamos con funciones puras de routing o HTTP.

El PDF indica que Angular CLI dispone además de una migración para pasar a `inject()`.

---

# 8. Proveedores e inyectores

Angular utiliza **providers** para saber cómo crear una dependencia.

Un provider es una especie de receta que indica al inyector:

```text
token → cómo crear o proporcionar la dependencia
```

Cuando un componente solicita una dependencia:

1. el inyector comprueba si ya existe una instancia;
2. si no existe, la crea;
3. la devuelve;
4. la conserva para futuras peticiones.

---

# 9. `providedIn: 'root'`

Con:



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Injectable({
  providedIn: 'root'
})</code></pre>
</div>



el servicio se registra en el **root injector**.

Los servicios del root injector:

- están disponibles en toda la aplicación;
- suelen comportarse como singleton;
- pueden eliminarse del bundle si Angular detecta que no se utilizan.

Esto último está relacionado con **tree shaking**.

> 🌳 **Tree shaking**  
> Proceso mediante el cual se eliminan dependencias que no se utilizan para reducir el tamaño final del bundle.

---

# 10. Proveedores en `app.config.ts`

También podríamos registrar el servicio globalmente mediante `providers` en la configuración de aplicación.

Conceptualmente:



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export const appConfig: ApplicationConfig = {
  providers: [
    ProductsService
  ]
};</code></pre>
</div>



Sin embargo, el PDF recomienda preferir `providedIn: 'root'` porque permite un mejor tree shaking.

> ✅ Los servicios singleton deberían registrarse en el inyector raíz.

---

# 11. Jerarquía de inyectores

Angular utiliza una jerarquía de inyectores.

Conceptualmente:



<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Root Injector
      │
      ▼
Standalone App Injector
      │
      ▼
AppComponent Injector
      │
      ▼
ProductListComponent Injector</code></pre>
</div>



Cuando un componente solicita un servicio:

1. Angular busca en su inyector.
2. Si no lo encuentra, sube al inyector padre.
3. Continúa hasta llegar al root injector.
4. Si no encuentra proveedor, lanza un error.

El PDF muestra esta jerarquía utilizando **Angular DevTools → Components / Injector Tree**.

---

# 12. Inyectores de entorno e inyectores de elemento

Angular distingue principalmente:

## Environment injectors

Se configuran mediante:

- `providedIn`;
- `providers` de la configuración de aplicación.

## Element injectors

Cada componente dispone de uno.

Puede configurarse mediante:



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Component({
  providers: [
    ProductsService
  ]
})</code></pre>
</div>



Los element injectors permiten limitar el alcance del servicio a una parte concreta del árbol de componentes.

---

# 13. Compartir servicios con componentes hijos

Creamos un componente:



<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng generate component favorites</code></pre>
</div>



En `FavoritesComponent` inyectamos `ProductsService`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class FavoritesComponent
  implements OnInit {
&#8203;
  products: Product[] = [];
&#8203;
  constructor(
    private productService: ProductsService
  ) {}
&#8203;
  ngOnInit(): void {
    this.products =
      this.productService.getProducts();
  }
}</code></pre>
</div>



La plantilla muestra solo una parte de los productos:


<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;ul class="pill-group"&gt;
  @for (
    product of products | slice:1:3;
    track product.id
  ) {
    &lt;li class="pill"&gt;
      ⭐ {{ product.title }}
    &lt;/li&gt;
  }
&lt;/ul&gt;</code></pre>
</div>



Como utiliza `slice`, importa `CommonModule`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { CommonModule }
  from '@angular/common';
&#8203;
@Component({
  selector: 'app-favorites',
  imports: [CommonModule],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css'
})</code></pre>
</div>



---

# 14. Proporcionar `ProductsService` desde `ProductListComponent`

El padre puede registrar el servicio en su propio inyector:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Component({
  selector: 'app-product-list',
  imports: [
    ProductDetailComponent,
    SortPipe,
    FavoritesComponent
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css',
  providers: [
    ProductsService
  ]
})</code></pre>
</div>



En ese caso podemos retirar `providedIn` de `ProductsService`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Injectable()
export class ProductsService {
}</code></pre>
</div>



Y mostrar el hijo:


<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;h1&gt;Favorites&lt;/h1&gt;
&lt;app-favorites&gt;&lt;/app-favorites&gt;</code></pre>
</div>



`FavoritesComponent` puede utilizar `ProductsService` porque es hijo directo de `ProductListComponent`, que lo proporciona.

---

# 15. Root injector frente a component injector

Si el servicio está en root:

```text
ProductsService
      │
      └── instancia global compartida
```

Si lo proporciona un componente:

```text
ProductListComponent
      │
      ├── instancia local de ProductsService
      │
      └── disponible para sus descendientes
```

Esta técnica se denomina **service scope limiting**: limitar el alcance del servicio a una zona del árbol.

El PDF ilustra este caso en la figura de *Service scope limiting*.

---

# 16. Crear múltiples instancias: sandboxing

En algunos casos queremos una instancia independiente por cada componente renderizado.

Creamos:



<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng generate component product-view</code></pre>
</div>



El componente recibe un identificador:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import {
  Component,
  input
} from '@angular/core';
&#8203;
@Component({
  selector: 'app-product-view',
  imports: [],
  templateUrl: './product-view.component.html',
  styleUrl: './product-view.component.css'
})
export class ProductViewComponent {
  id = input&lt;number&gt;();
}</code></pre>
</div>



Después generamos un servicio específico:


<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">cd product-view
ng generate service product-view</code></pre>
</div>



Quitamos `providedIn` y hacemos que dependa de `ProductsService`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { Injectable } from '@angular/core';
import { ProductsService }
  from '../products.service';
&#8203;
@Injectable()
export class ProductViewService {
  constructor(
    private productService: ProductsService
  ) {}
}</code></pre>
</div>



Esto es un ejemplo de **service-in-a-service**.

---

# 17. Cache local en `ProductViewService`

Añadimos:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { Product } from '../product';
&#8203;
@Injectable()
export class ProductViewService {
  private product: Product | undefined;
&#8203;
  constructor(
    private productService: ProductsService
  ) {}
&#8203;
  getProduct(
    id: number
  ): Product | undefined {
    const products =
      this.productService.getProducts();
&#8203;
    if (!this.product) {
      this.product = products.find(
        product =&gt; product.id === id
      );
    }
&#8203;
    return this.product;
  }
}</code></pre>
</div>



El servicio guarda localmente el producto encontrado.

---

# 18. Proporcionar el servicio en cada `ProductViewComponent`

El componente registra su propia instancia:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Component({
  selector: 'app-product-view',
  imports: [],
  templateUrl: './product-view.component.html',
  styleUrl: './product-view.component.css',
  providers: [
    ProductViewService
  ]
})
export class ProductViewComponent
  implements OnInit {
&#8203;
  id = input&lt;number&gt;();
  product: Product | undefined;
&#8203;
  constructor(
    private productViewService:
      ProductViewService
  ) {}
&#8203;
  ngOnInit(): void {
    this.product =
      this.productViewService.getProduct(
        this.id()!
      );
  }
}</code></pre>
</div>



Cada instancia de `ProductViewComponent` tendrá su propia instancia de `ProductViewService`.

---

# 19. Mostrar el producto

Plantilla:


<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">@switch (product?.title) {
  @case ('Keyboard') { ⌨️ }
  @case ('Microphone') { 🎙️ }
  @default { 🏷️ }
}
&#8203;
{{ product?.title }}</code></pre>
</div>



En el listado:


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
      &lt;app-product-view
        [id]="product.id"
      &gt;&lt;/app-product-view&gt;
    &lt;/li&gt;
  } @empty {
    &lt;p&gt;No products found!&lt;/p&gt;
  }
&lt;/ul&gt;</code></pre>
</div>



---

# 20. ¿Qué ocurre si el servicio se proporciona demasiado arriba?

Si `ProductViewService` se registra en `ProductListComponent` en vez de en cada `ProductViewComponent`, todos los hijos compartirán la misma instancia.

Como `ProductViewService` guarda el primer producto encontrado en:



<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">private product:
  Product | undefined;</code></pre>
</div>



todos los componentes terminarán mostrando el mismo producto.

El PDF muestra este comportamiento con el mismo producto repetido varias veces.

> 🧠 El lugar donde registramos un provider afecta directamente al **alcance** y al **número de instancias** del servicio.

---

# 21. Restringir la búsqueda con `@Host`

Podemos limitar hasta dónde busca Angular una dependencia.

Ejemplo:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import {
  Component,
  Host,
  OnInit
} from '@angular/core';
&#8203;
export class FavoritesComponent
  implements OnInit {
&#8203;
  products: Product[] = [];
&#8203;
  constructor(
    @Host()
    private productService:
      ProductsService
  ) {}
&#8203;
  ngOnInit(): void {
    this.products =
      this.productService.getProducts();
  }
}</code></pre>
</div>



Si Angular no encuentra el servicio dentro del límite impuesto, puede lanzar:


<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Error: NG0201: No provider for _ProductsService found in NodeInjector.</code></pre>
</div>



---

# 22. `@Optional`

Podemos indicar que la dependencia puede no existir:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">constructor(
  @Optional()
  @Host()
  private productService:
    ProductsService
) {}</code></pre>
</div>



Sin embargo, si el servicio puede ser `null` o no encontrarse, la lógica debe tenerlo en cuenta antes de utilizarlo.

---

# 23. `@Self` y `@SkipSelf`

Angular también dispone de:

| Decorador | Comportamiento |
|---|---|
| `@Self()` | Busca solo en el inyector actual |
| `@SkipSelf()` | Ignora el inyector actual y comienza en el padre |
| `@Host()` | Limita la búsqueda al host |
| `@Optional()` | No obliga a que exista proveedor |

Estas opciones permiten controlar cómo se recorre la jerarquía de inyectores.

---

# 24. Sintaxis completa de providers

La forma abreviada:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">providers: [
  ProductsService
]</code></pre>
</div>



equivale a:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useClass: ProductsService
  }
]</code></pre>
</div>



Las propiedades principales son:

- `provide`: token que solicita el consumidor.
- `useClass`: clase concreta que debe suministrar Angular.

---

# 25. Sobrescribir una implementación con `useClass`

Creamos:


<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng generate service favorites</code></pre>
</div>



El nuevo servicio hereda de `ProductsService`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { Product } from './product';
import { ProductsService }
  from './products.service';
&#8203;
export class FavoritesService
  extends ProductsService {
&#8203;
  constructor() {
    super();
  }
&#8203;
  override getProducts(): Product[] {
    return super
      .getProducts()
      .slice(1, 3);
  }
}</code></pre>
</div>



En `FavoritesComponent`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Component({
  selector: 'app-favorites',
  imports: [],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css',
  providers: [
    {
      provide: ProductsService,
      useClass: FavoritesService
    }
  ]
})</code></pre>
</div>



Ahora, cuando `FavoritesComponent` solicita `ProductsService`, Angular entrega una instancia de `FavoritesService`.

> 🔁 El token solicitado sigue siendo `ProductsService`, pero la implementación cambia.

---

# 26. Proveedores condicionales con `useFactory`

También podemos decidir dinámicamente qué instancia crear mediante una función.

Ejemplo:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { FavoritesService }
  from './favorites.service';
import { ProductsService }
  from './products.service';
&#8203;
export function favoritesFactory(
  isFavorite: boolean
) {
  return () =&gt; {
    if (isFavorite) {
      return new FavoritesService();
    }
&#8203;
    return new ProductsService();
  };
}</code></pre>
</div>



Provider:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useFactory: favoritesFactory(true)
  }
]</code></pre>
</div>



Si la factory necesita dependencias adicionales, se declaran en `deps`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useFactory: favoritesFactory(true),
    deps: [
      ProductViewService
    ]
  }
]</code></pre>
</div>



Y la factory puede recibirlas:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export function favoritesFactory(
  isFavorite: boolean
) {
  return (
    productViewService:
      ProductViewService
  ) =&gt; {
    if (isFavorite) {
      return new FavoritesService();
    }
&#8203;
    return new ProductsService();
  };
}</code></pre>
</div>



---

# 27. Proporcionar valores con `useValue`

No todas las dependencias son clases.

Podemos querer proporcionar configuración:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export interface AppSettings {
  title: string;
  version: string;
}
&#8203;
export const appSettings:
  AppSettings = {
    title: 'My e-shop',
    version: '1.0'
  };</code></pre>
</div>



Una interfaz TypeScript no existe en tiempo de ejecución, por lo que no puede utilizarse directamente como token de DI.

Necesitamos `InjectionToken`.

---

# 28. `InjectionToken`

Importamos:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import {
  InjectionToken
} from '@angular/core';</code></pre>
</div>



Creamos el token:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export const APP_SETTINGS =
  new InjectionToken&lt;AppSettings&gt;(
    'app.settings'
  );</code></pre>
</div>



Y lo registramos:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    ProductListComponent,
    CopyrightDirective
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css',
  providers: [
    {
      provide: APP_SETTINGS,
      useValue: appSettings
    }
  ]
})
export class AppComponent {
}</code></pre>
</div>



---

# 29. Consumir la configuración

En `AppComponent`:


<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">settings =
  inject(APP_SETTINGS);</code></pre>
</div>



En la plantilla:


<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;footer appCopyright&gt;
  - v{{ settings.version }}
&lt;/footer&gt;</code></pre>
</div>



Salida esperada:


<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Copyright ©2024 All Rights Reserved - v1.0</code></pre>
</div>



`useValue` resulta especialmente útil para:

- configuración;
- constantes;
- objetos compartidos;
- pruebas unitarias.

---

# 30. Esquema general de DI en Angular



<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Componente / Servicio consumidor
            │
            │ solicita token
            ▼
        Injector local
            │
       ¿lo conoce?
      ┌─────┴─────┐
     sí           no
     │            │
 instancia     sube al padre
     │            │
     └───────► Root Injector
                    │
                    ▼
             proveedor encontrado
                    │
                    ▼
               dependencia</code></pre>
</div>



---

# 31. Comparativa de mecanismos de provider

| Mecanismo | Uso |
|---|---|
| `providedIn: 'root'` | Servicio singleton global |
| `providers: [Service]` | Servicio limitado a un componente y descendientes |
| `useClass` | Sustituir implementación |
| `useFactory` | Crear dependencia mediante lógica |
| `useValue` | Proporcionar valor u objeto |
| `InjectionToken` | Token para valores que no son clases |

---

# 32. Resumen

En esta unidad hemos aprendido que Angular DI es uno de los pilares del framework.

Los servicios permiten:

- separar lógica de negocio de la presentación;
- reutilizar funcionalidades;
- compartir estado o comportamiento;
- facilitar pruebas;
- controlar el ciclo de vida de las dependencias.

También hemos visto:

- inyección por constructor;
- `inject()`;
- root injector;
- element injectors;
- providers en componentes;
- jerarquía de resolución;
- `@Host`, `@Optional`, `@Self`, `@SkipSelf`;
- `useClass`;
- `useFactory`;
- `useValue`;
- `InjectionToken`.

---

# 33. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| DI | Inyección de dependencias |
| Dependency | Objeto requerido por otro |
| Injector | Entidad que crea y proporciona dependencias |
| Service | Clase que encapsula lógica reutilizable |
| `@Injectable` | Marca una clase como servicio Angular |
| `providedIn: 'root'` | Proveedor global |
| Root injector | Inyector principal |
| Element injector | Inyector asociado a componente |
| Provider | Receta de creación/resolución |
| `inject()` | Función para solicitar dependencias |
| `providers` | Registro local de dependencias |
| `@Host` | Limita búsqueda |
| `@Optional` | Dependencia no obligatoria |
| `@Self` | Solo inyector actual |
| `@SkipSelf` | Empieza a buscar en el padre |
| `useClass` | Sustitución por otra clase |
| `useFactory` | Creación mediante función |
| `useValue` | Provisión de un valor |
| `InjectionToken` | Token para valores no representados por clases |
| Tree shaking | Eliminación de código no utilizado |
| Service scope limiting | Limitación del alcance de un servicio |
| Service-in-a-service | Servicio inyectado dentro de otro |

---

## 🔗 Recursos mencionados en el material

- Angular DI: <https://angular.dev/guide/di>
- Hierarchical DI: <https://angular.dev/guide/di/hierarchical-dependency-injection>
- Migración a `inject()`: <https://angular.dev/reference/migrations/inject-function>

---

> 🎓 **Siguiente paso**  
> Tras comprender servicios e inyección de dependencias, el siguiente paso será introducir **programación reactiva y observables**, que permitirán trabajar con flujos de datos asíncronos dentro de Angular.

---

# 34. Taller práctico: servicios e inyección de dependencias 🧪

Los ejercicios de esta sección toman como referencia el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/05_servicios>

El punto de partida del repositorio ya contiene un `ProductsService` registrado con `providedIn: 'root'`, un `ProductListComponent` que lo recibe por constructor y varios artefactos que permiten experimentar con la jerarquía de inyectores: `FavoritesComponent`, `FavoritesService`, `ProductViewComponent`, `ProductViewService` y `APP_SETTINGS`.

> 🎯 **Forma de trabajo recomendada**  
> Realiza los ejercicios en orden. Antes de mover un provider o cambiar la forma de inyección, predice cuántas instancias del servicio existirán y qué componentes podrán acceder a ellas.

---

## Ejercicio 1. Sigue el recorrido de los productos

### Objetivo

Comprender la separación entre presentación y lógica de negocio.

En el repositorio, `ProductsService` contiene `getProducts()` y `ProductListComponent` carga esos datos en `ngOnInit()`.

### Tareas

1. Abre `products.service.ts`.
2. Localiza `getProducts()`.
3. Abre `product-list.component.ts`.
4. Localiza la inyección de `ProductsService`.
5. Localiza la llamada realizada desde `ngOnInit()`.
6. Dibuja el flujo:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ProductsService
      │
      │ getProducts()
      ▼
ProductListComponent
      │
      │ products
      ▼
product-list.component.html</code></pre>
</div>


### Preguntas

1. ¿Qué responsabilidad ha dejado de tener `ProductListComponent`?
2. ¿Qué parte sigue siendo responsabilidad del componente?
3. ¿Por qué esta separación facilita sustituir en el futuro los datos estáticos por una API?

---

## Ejercicio 2. Rompe deliberadamente la separación de responsabilidades

### Objetivo

Comparar el diseño con servicio frente a un componente acoplado.

Copia temporalmente el array de productos de `ProductsService` dentro de `ProductListComponent` y elimina la llamada al servicio.

### Tareas

1. Comprueba que la aplicación sigue mostrando los productos.
2. Compara ambos diseños.
3. Anota al menos tres inconvenientes de mantener el array dentro del componente.
4. Restaura después la versión con servicio.

### Reflexión

Que una solución funcione no significa necesariamente que tenga una arquitectura adecuada.

---

## Ejercicio 3. Constructor injection frente a `new`

### Objetivo

Comprobar por qué no debemos instanciar manualmente un servicio gestionado por Angular.

Sustituye temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService: ProductsService
) {}</code></pre>
</div>


por una creación manual:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private productService: ProductsService;
&#8203;
constructor() {
  this.productService = new ProductsService();
}</code></pre>
</div>


### Tareas

1. Comprueba que la aplicación puede seguir funcionando.
2. Explica qué responsabilidad adicional ha asumido ahora el componente.
3. Piensa qué ocurriría si `ProductsService` necesitase otra dependencia en su constructor.
4. Restaura la inyección Angular.

### Pregunta clave

¿Quién debería decidir cómo se construye `ProductsService`: el componente o el inyector?

---

## Ejercicio 4. Inyección mediante `inject()`

### Objetivo

Practicar la segunda forma habitual de solicitar dependencias.

Refactoriza `ProductListComponent`.

Importa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  OnInit,
  inject
} from '@angular/core';</code></pre>
</div>


Declara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private readonly productService =
  inject(ProductsService);</code></pre>
</div>


y elimina la inyección del constructor.

### Tareas

1. Comprueba que la aplicación sigue funcionando.
2. Confirma que `ngOnInit()` no necesita cambiar.
3. Explica qué valor devuelve `inject(ProductsService)`.
4. Vuelve a la inyección por constructor y decide cuál de las dos te resulta más legible en este componente.

---

## Ejercicio 5. Comprueba el singleton del root injector

### Objetivo

Demostrar que dos consumidores pueden recibir la misma instancia de un servicio raíz.

Añade temporalmente al servicio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">instanceId = Math.random();</code></pre>
</div>


Muestra el valor desde `ProductListComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  console.log(
    'ProductList:',
    this.productService.instanceId
  );
&#8203;
  this.products =
    this.productService.getProducts();
}</code></pre>
</div>


Haz lo mismo desde `FavoritesComponent`.

### Tareas

1. Arranca la aplicación.
2. Comprueba los identificadores.
3. Si ambos reciben el servicio desde el root injector, compara los valores.
4. Recarga la página y observa cuándo cambia el identificador.

### Conclusión

Explica con tus palabras qué significa que el servicio raíz sea un singleton dentro de una ejecución de la aplicación.

---

## Ejercicio 6. Inspecciona la inyección con Angular DevTools

### Objetivo

Relacionar el código con la jerarquía real de inyectores.

1. Ejecuta la aplicación.
2. Abre Angular DevTools.
3. Selecciona `app-product-list`.
4. Busca la sección **Injected Services**.
5. Localiza `ProductsService`.
6. Abre **Injector Tree**.

### Tareas

Representa con un esquema simplificado la ruta que sigue Angular para resolver `ProductsService`.

Por ejemplo:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ProductListComponent
       │
       ▼
AppComponent
       │
       ▼
Environment / Root Injector
       │
       ▼
ProductsService</code></pre>
</div>


### Pregunta

¿En qué inyector está registrado inicialmente `ProductsService` en el repositorio?

---

## Ejercicio 7. Lleva `ProductsService` al inyector del componente

### Objetivo

Limitar el alcance de un servicio.

En `ProductListComponent`, añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Component({
  // ...
  providers: [
    ProductsService
  ]
})</code></pre>
</div>


Para realizar la prueba de forma coherente, elimina temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providedIn: 'root'</code></pre>
</div>


de `ProductsService`.

### Tareas

1. Comprueba que `ProductListComponent` continúa funcionando.
2. Inspecciona de nuevo el árbol de inyectores.
3. Explica qué parte del árbol puede ahora utilizar esa instancia.
4. Intenta inyectar el mismo servicio desde un componente que no sea descendiente de `ProductListComponent`.

### Reflexión

¿Qué diferencia hay entre **disponibilidad global** y **alcance limitado al subárbol de un componente**?

---

## Ejercicio 8. Conecta `FavoritesComponent`

### Objetivo

Compartir una dependencia del padre con un hijo.

El repositorio ya contiene `FavoritesComponent`, que inyecta `ProductsService`.

Importa el componente en `ProductListComponent` y añade:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h1&gt;Favorites&lt;/h1&gt;
&lt;app-favorites&gt;&lt;/app-favorites&gt;</code></pre>
</div>


### Tareas

1. Comprueba que aparecen los dos productos seleccionados por `slice:1:3`.
2. No añadas `ProductsService` a los providers de `FavoritesComponent`.
3. Comprueba que el hijo puede obtenerlo del padre.
4. Usa Angular DevTools para verificar la ruta de resolución.

### Pregunta

¿Por qué `FavoritesComponent` puede recibir el servicio sin proporcionarlo él mismo?

---

## Ejercicio 9. Demuestra que padre e hijo comparten instancia

### Objetivo

Comprobar el comportamiento de un provider situado en el padre.

Mantén el `instanceId` del ejercicio 5.

Muestra desde:

- `ProductListComponent`
- `FavoritesComponent`

el identificador de `ProductsService`.

### Resultado esperado

Si ambos resuelven el servicio proporcionado por `ProductListComponent`, deben obtener la misma instancia.

### Reto

Añade `ProductsService` también a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  ProductsService
]</code></pre>
</div>


en `FavoritesComponent`.

¿Qué ocurre ahora con los identificadores? Explica por qué.

---

## Ejercicio 10. Observa el efecto de una instancia local por componente

### Objetivo

Comprender el concepto de *sandboxing*.

El repositorio contiene `ProductViewComponent` con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  ProductViewService
]</code></pre>
</div>


y `ProductViewService` almacena:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private product:
  Product | undefined;</code></pre>
</div>


### Tareas

1. Importa `ProductViewComponent` en `ProductListComponent`.
2. Sustituye el contenido de cada `<li>` por:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-view
  [id]="product.id"
&gt;&lt;/app-product-view&gt;</code></pre>
</div>


3. Comprueba que cada fila muestra su producto.
4. Añade un `instanceId = Math.random()` a `ProductViewService`.
5. Muestra el identificador por consola desde cada `ProductViewComponent`.

### Conclusión

¿Cuántas instancias de `ProductViewService` se crean si hay cuatro componentes `app-product-view`?

---

## Ejercicio 11. Proporciona `ProductViewService` demasiado arriba

### Objetivo

Comprobar cómo el lugar del provider modifica el comportamiento.

1. Elimina `providers: [ProductViewService]` de `ProductViewComponent`.
2. Añade el servicio a los providers de `ProductListComponent`.
3. Ejecuta la aplicación.

### Observa

El método del servicio conserva el primer producto encontrado:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">if (!this.product) {
  this.product = products.find(
    product =&gt; product.id === id
  );
}</code></pre>
</div>


### Preguntas

1. ¿Cuántas instancias del servicio hay ahora?
2. ¿Por qué varios `ProductViewComponent` muestran el mismo producto?
3. ¿Qué demuestra este experimento sobre el alcance de los providers?

Restablece después el provider en `ProductViewComponent`.

---

## Ejercicio 12. Servicio dentro de servicio

### Objetivo

Reconocer una cadena de dependencias.

En el repositorio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService:
    ProductsService
) {}</code></pre>
</div>


aparece dentro de `ProductViewService`.

### Tareas

Representa la cadena:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ProductViewComponent
        │
        ▼
ProductViewService
        │
        ▼
ProductsService
        │
        ▼
Product[]</code></pre>
</div>


Después responde:

1. ¿Quién crea `ProductViewService`?
2. ¿Quién proporciona `ProductsService`?
3. ¿Tiene `ProductViewComponent` que saber que `ProductViewService` depende de otro servicio?

---

## Ejercicio 13. `@Host`: limita la búsqueda

### Objetivo

Provocar y comprender un error de resolución de dependencias.

Modifica temporalmente `FavoritesComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  @Host()
  private productService:
    ProductsService
) {}</code></pre>
</div>


Importa `Host` desde `@angular/core`.

### Tareas

1. Prueba distintas ubicaciones del provider.
2. Fuerza una situación donde el servicio no esté dentro del límite de búsqueda.
3. Observa el error `NG0201`.
4. Documenta qué provider faltaba y dónde esperaba encontrarlo Angular.

---

## Ejercicio 14. `@Optional`: una dependencia puede faltar

### Objetivo

Evitar que la resolución falle inmediatamente cuando el provider es opcional.

Declara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  @Optional()
  @Host()
  private productService:
    ProductsService | null
) {}</code></pre>
</div>


Ajusta `ngOnInit()`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  if (this.productService) {
    this.products =
      this.productService.getProducts();
  } else {
    this.products = [];
  }
}</code></pre>
</div>


### Tareas

1. Prueba con provider disponible.
2. Prueba sin provider.
3. Comprueba que ahora el componente puede decidir qué hacer.
4. Explica la diferencia entre **dependencia inexistente** y **dependencia opcional**.

---

## Ejercicio 15. Experimenta con `@Self` y `@SkipSelf`

### Objetivo

Comprender de dónde debe obtenerse una dependencia.

Realiza dos pruebas independientes.

### Prueba A

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  @Self()
  private productService:
    ProductsService
) {}</code></pre>
</div>


### Prueba B

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  @SkipSelf()
  private productService:
    ProductsService
) {}</code></pre>
</div>


### Tareas

Para cada prueba:

1. Anota dónde está registrado el servicio.
2. Predice si Angular lo encontrará.
3. Ejecuta.
4. Explica el resultado.

### Tabla a completar

| Decorador | Busca localmente | Puede buscar en padre |
|---|:---:|:---:|
| `@Self()` |  |  |
| `@SkipSelf()` |  |  |
| `@Host()` |  |  |

---

## Ejercicio 16. Sobrescribe `ProductsService` con `FavoritesService`

### Objetivo

Comprender el proveedor `useClass`.

El repositorio contiene `FavoritesService`, que hereda de `ProductsService` y redefine `getProducts()`.

Configura en `FavoritesComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useClass: FavoritesService
  }
]</code></pre>
</div>


Después elimina el `slice` de la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products;
  track product.id
) {
  &lt;li class="pill"&gt;
    ⭐ {{ product.title }}
  &lt;/li&gt;
}</code></pre>
</div>


### Preguntas

1. ¿Qué tipo solicita el constructor?
2. ¿Qué clase crea realmente Angular?
3. ¿Por qué el componente no necesita cambiar su constructor?
4. ¿Qué ventaja tiene esta sustitución desde el punto de vista del desacoplamiento?

---

## Ejercicio 17. Compara la sintaxis abreviada y explícita

### Objetivo

Comprender qué representa realmente un provider.

Compara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  ProductsService
]</code></pre>
</div>


con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useClass: ProductsService
  }
]</code></pre>
</div>


### Tarea

Explica con tus palabras:

- qué es el **token**;
- qué es la **implementación**;
- por qué ambas configuraciones son equivalentes.

---

## Ejercicio 18. Proveedor condicional con `useFactory`

### Objetivo

Elegir una implementación mediante una factory.

El repositorio incluye `favoritesFactory`.

Configura:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useFactory:
      favoritesFactory(true)
  }
]</code></pre>
</div>


### Tareas

1. Ejecuta con `true`.
2. Ejecuta con `false`.
3. Comprueba qué productos se muestran en cada caso.
4. Explica quién decide ahora la implementación concreta.

### Reto

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const useFavorites = false;</code></pre>
</div>


y utiliza esa constante para controlar la factory.

---

## Ejercicio 19. Añade una dependencia a una factory

### Objetivo

Comprender la propiedad `deps`.

Supón que una factory necesita `ProductViewService`.

Configura:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: ProductsService,
    useFactory:
      favoritesFactory(true),
    deps: [
      ProductViewService
    ]
  }
]</code></pre>
</div>


### Preguntas

1. ¿Qué información proporciona `deps` al inyector?
2. ¿Quién construye esas dependencias?
3. ¿Por qué una factory puede necesitar DI igual que un servicio?

---

## Ejercicio 20. Analiza `APP_SETTINGS`

### Objetivo

Comprender por qué una interfaz no puede utilizarse como token en tiempo de ejecución.

El repositorio define:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface AppSettings {
  title: string;
  version: string;
}
&#8203;
export const appSettings:
  AppSettings = {
    title: 'My e-shop',
    version: '1.0'
  };</code></pre>
</div>


y:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const APP_SETTINGS =
  new InjectionToken&lt;AppSettings&gt;(
    'app.settings'
  );</code></pre>
</div>


### Preguntas

1. ¿Por qué no podemos usar `AppSettings` directamente como token?
2. ¿Qué ocurre con las interfaces TypeScript durante la compilación?
3. ¿Qué conserva `InjectionToken` en tiempo de ejecución?

---

## Ejercicio 21. Proporciona configuración con `useValue`

### Objetivo

Trabajar con una dependencia que no es un servicio de clase.

En `AppComponent`, el repositorio utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: APP_SETTINGS,
    useValue: appSettings
  }
]</code></pre>
</div>


y:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">settings =
  inject(APP_SETTINGS);</code></pre>
</div>


### Tareas

1. Cambia `title` a `Tienda DAM`.
2. Cambia `version` a `1.1`.
3. Muestra ambos valores en la plantilla.
4. Comprueba el footer.
5. Explica por qué `useValue` no necesita construir ninguna clase.

---

## Ejercicio 22. Configuración alternativa para desarrollo

### Objetivo

Comprobar la facilidad para sustituir valores inyectados.

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const devSettings:
  AppSettings = {
    title: 'Tienda DAM DEV',
    version: 'dev'
  };</code></pre>
</div>


Cambia temporalmente el provider:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: APP_SETTINGS,
    useValue: devSettings
  }
]</code></pre>
</div>


### Reflexión

¿Por qué esta técnica puede ser útil para:

- tests;
- desarrollo;
- configuraciones diferentes?

---

# 35. Reto integrador: catálogo con servicios y scopes 🚀

### Objetivo

Aplicar de forma conjunta servicios, jerarquía de inyectores y providers sobre la aplicación del repositorio.

## Requisitos

### 1. `ProductsService`

Debe continuar siendo la fuente principal de productos.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProductById(
  id: number
): Product | undefined {
  return this.getProducts().find(
    product =&gt; product.id === id
  );
}</code></pre>
</div>


### 2. `ProductListComponent`

Debe:

- obtener los productos mediante DI;
- no crear servicios mediante `new`;
- utilizar `inject()` o constructor injection;
- limitarse a lógica de presentación.

### 3. `FavoritesComponent`

Debe recibir `ProductsService` como token, pero utilizar una implementación específica mediante:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  provide: ProductsService,
  useClass: FavoritesService
}</code></pre>
</div>


### 4. `ProductViewComponent`

Cada instancia debe tener su propio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  ProductViewService
]</code></pre>
</div>


### 5. Configuración

Mantén:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">APP_SETTINGS</code></pre>
</div>


mediante `InjectionToken` y `useValue`.

La plantilla raíz debe mostrar:

- título de aplicación;
- versión;
- listado de productos.

### 6. Verificación con DevTools

Documenta mediante un esquema la resolución de:

- `ProductsService` en `ProductListComponent`;
- `ProductsService` en `FavoritesComponent`;
- `ProductViewService` en dos instancias distintas;
- `APP_SETTINGS` en `AppComponent`.

### Checklist

- [ ] No se instancia `ProductsService` mediante `new` desde componentes.
- [ ] Existe al menos una inyección mediante constructor.
- [ ] Existe al menos una inyección mediante `inject()`.
- [ ] Se utiliza `providedIn: 'root'` o se justifica por qué se ha cambiado.
- [ ] Se utiliza un provider local.
- [ ] Se utiliza `useClass`.
- [ ] Se utiliza `useValue`.
- [ ] Se utiliza `InjectionToken`.
- [ ] Dos `ProductViewComponent` no comparten su `ProductViewService`.
- [ ] La jerarquía se ha inspeccionado con Angular DevTools.
- [ ] El proyecto compila sin errores.

---

# 36. Diagnóstico de errores de DI 🔎

Analiza cada caso antes de ejecutarlo.

## Caso A: servicio sin provider

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Injectable()
export class ProductsService {
}</code></pre>
</div>


y ningún injector registra `ProductsService`.

El componente intenta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService:
    ProductsService
) {}</code></pre>
</div>


**Pregunta:** ¿qué tipo de error esperas?

---

## Caso B: dependencia creada manualmente

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.productService =
  new ProductsService();</code></pre>
</div>


**Pregunta:** aunque compile, ¿qué ventajas de DI estamos perdiendo?

---

## Caso C: provider demasiado alto

`ProductViewService` contiene un cache local y se proporciona en `ProductListComponent`.

**Pregunta:** ¿por qué todos los `ProductViewComponent` pueden compartir el mismo producto cacheado?

---

## Caso D: `@Self()` sin provider local

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  @Self()
  private productService:
    ProductsService
) {}</code></pre>
</div>


pero el servicio solo existe en root.

**Pregunta:** ¿por qué Angular no debe subir hasta root?

---

## Caso E: interfaz como token

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">providers: [
  {
    provide: AppSettings,
    useValue: appSettings
  }
]</code></pre>
</div>


**Pregunta:** ¿por qué `AppSettings` no puede actuar como token real?

---

## Caso F: `useClass` con implementación incorrecta

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  provide: ProductsService,
  useClass: OtraClase
}</code></pre>
</div>


si `OtraClase` no ofrece el contrato esperado por los consumidores.

**Pregunta:** ¿qué problemas de diseño o ejecución podrían aparecer?

---

# 37. Cuestiones finales de reflexión 📝

1. ¿Qué problema arquitectónico resuelven los servicios?
2. ¿Qué diferencia existe entre crear una dependencia con `new` y solicitarla al inyector?
3. ¿Qué significa `providedIn: 'root'`?
4. ¿Qué es un provider?
5. ¿Qué es un token de inyección?
6. ¿Qué diferencia existe entre root injector y element injector?
7. ¿Cómo busca Angular una dependencia cuando no está en el inyector actual?
8. ¿Qué ventaja aporta `inject()` frente al constructor en determinados contextos?
9. ¿Por qué puede interesar declarar una dependencia inyectada como `readonly`?
10. ¿Qué significa limitar el scope de un servicio?
11. ¿Por qué un servicio proporcionado por un componente puede compartirse con sus hijos?
12. ¿Cuándo interesa crear una instancia de servicio por cada componente?
13. ¿Qué significa *service-in-a-service*?
14. ¿Qué diferencia existe entre `@Self()` y `@SkipSelf()`?
15. ¿Para qué sirve `@Optional()`?
16. ¿Qué hace `@Host()` sobre la búsqueda de providers?
17. ¿Qué diferencia existe entre `provide` y `useClass`?
18. ¿Cuándo usarías `useFactory`?
19. ¿Cuándo usarías `useValue`?
20. ¿Por qué necesitamos `InjectionToken` para ciertas dependencias?
21. ¿Qué relación existe entre DI y testing?
22. ¿Cómo ayuda DI a cumplir el principio de separación de responsabilidades?

---

> ✅ **Meta de aprendizaje**  
> Si puedes predecir qué instancia recibirá cada componente según la posición de sus providers y puedes sustituir implementaciones sin modificar el consumidor, ya comprendes los fundamentos del sistema de inyección de dependencias de Angular.
