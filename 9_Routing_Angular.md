# 🧭 Unidad 9: Navegación en aplicaciones Angular con Routing


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
> **Objetivo:** aprender a estructurar la navegación de una aplicación Angular mediante el **Angular Router**, rutas estáticas y dinámicas, parámetros, rutas hijas, guards, resolvers y lazy loading.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Explicar la diferencia entre navegación tradicional y una SPA.
- Configurar el Angular Router.
- Definir rutas en `app.routes.ts`.
- Utilizar `RouterLink`, `RouterLinkActive` y `RouterOutlet`.
- Configurar rutas por defecto y wildcard.
- Navegar de forma declarativa e imperativa.
- Trabajar con rutas relativas y absolutas.
- Pasar parámetros mediante la URL.
- Utilizar `ActivatedRoute`, `paramMap` y `snapshot`.
- Utilizar query parameters.
- Vincular parámetros de ruta directamente con `input()`.
- Crear rutas hijas.
- Proteger rutas con guards.
- Evitar abandonar una ruta con `CanDeactivate`.
- Precargar datos con resolvers.
- Aplicar lazy loading a rutas y componentes.
- Proteger rutas lazy con `CanMatch`.

---

## 📑 Contenidos

1. [Fundamentos del router](#1-por-qué-necesitamos-routing)
2. [Configurar rutas y RouterOutlet](#4-activar-el-router)
3. [Navegación declarativa e imperativa](#9-navegación-con-routerlink)
4. [Parámetros de ruta y consulta](#19-parámetros-de-ruta)
5. [Rutas hijas y providers](#26-rutas-hijas)
6. [Guards y resolvers](#33-guards)
7. [Lazy loading](#41-lazy-loading)
8. [Resumen](#48-resumen)

---

# 1. ¿Por qué necesitamos routing?

En aplicaciones web tradicionales, cambiar de vista implicaba solicitar una nueva página al servidor.

El flujo era aproximadamente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Navegador
    │
    │ GET /products
    ▼
Servidor
    │
    │ products.html
    ▼
Navegador
    │
    │ GET /product
    ▼
Servidor
    │
    │ product.html
    ▼
Navegador</code></pre>
</div>



Cada navegación podía provocar una recarga completa de la página.

En una **Single-Page Application (SPA)**, como una aplicación Angular, la situación es diferente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Primera carga
Navegador ─────► Servidor
           index.html
             ◄─────
&#8203;
Después
Navegador ─────► Angular Router
                  │
                  ├── /products
                  ├── /cart
                  └── /user
&#8203;</code></pre>
</div>



La aplicación se carga una vez y el router gestiona los cambios de URL en el cliente.

> 🧠 **Idea clave**  
> Angular Router permite cambiar de vista sin recargar completamente la página.

---

# 2. El paquete `@angular/router`

Angular proporciona el paquete:

`@angular/router`

Para implementar navegación.

Los pasos básicos son:

1. Definir una ruta base.
2. Activar el router.
3. Configurar las rutas.
4. Decidir dónde se renderizan los componentes navegados.

---

# 3. El elemento `<base>`

Angular necesita un elemento `<base>` en `index.html`:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;!doctype html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
  &lt;meta charset="utf-8"&gt;
  &lt;title&gt;MyApp&lt;/title&gt;
  &lt;base href="/"&gt;
  &lt;meta
    name="viewport"
    content="width=device-width,
             initial-scale=1"
  &gt;
  &lt;link
    rel="icon"
    type="image/x-icon"
    href="favicon.ico"
  &gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;app-root&gt;&lt;/app-root&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
</div>



El atributo `href="/"` indica la ruta base utilizada para cargar recursos y resolver navegación.

Angular CLI lo configura automáticamente al crear una aplicación.

---

# 4. Activar el router

En aplicaciones standalone modernas, el router se configura mediante `provideRouter()`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  ApplicationConfig,
  provideZoneChangeDetection
} from '@angular/core';
&#8203;
import {
  provideRouter
} from '@angular/router';
&#8203;
import {
  routes
} from './app.routes';
&#8203;
export const appConfig:
  ApplicationConfig = {
    providers: [
      provideZoneChangeDetection({
        eventCoalescing: true
      }),
      provideRouter(routes)
    ]
  };</code></pre>
</div>



`provideRouter(routes)` habilita los servicios y directivas necesarios para routing.

---

# 5. Configurar rutas

El archivo `app.routes.ts` contiene objetos `Route`.

Ejemplo:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Routes
} from '@angular/router';
&#8203;
export const routes: Routes = [
  {
    path: 'products',
    component:
      ProductListComponent
  },
  {
    path: '**',
    component:
      PageNotFoundComponent
  }
];</code></pre>
</div>



Cada ruta contiene:

- `path`: segmento de URL;
- `component`: componente que se activa.

> 📌 El valor de `path` no debe comenzar por `/`.

---

# 6. Ruta wildcard

La ruta:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '**',
  component:
    PageNotFoundComponent
}</code></pre>
</div>



actúa como fallback.

Se activa cuando ninguna ruta anterior coincide.

> ⚠️ Debe colocarse al final, porque Angular utiliza una estrategia **first-match-wins**.

---

# 7. `RouterOutlet`

La directiva:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;router-outlet /&gt;</code></pre>
</div>



marca el lugar donde Angular insertará el componente activado por routing.

Conceptualmente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">AppComponent
  │
  ├── header
  ├── router-outlet
  │      │
  │      └── componente activado
  └── footer</code></pre>
</div>



---

# 8. Rutas principales de la aplicación

Creamos un carrito:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component cart</code></pre>
</div>



Y configuramos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const routes:
  Routes = [
    {
      path: 'products',
      component:
        ProductListComponent
    },
    {
      path: 'cart',
      component:
        CartComponent
    }
  ];</code></pre>
</div>



---

# 9. Navegación con `routerLink`

En la plantilla raíz:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;header&gt;
  &lt;h2&gt;
    {{ settings.title }}
  &lt;/h2&gt;
&#8203;
  &lt;span class="spacer"&gt;
  &lt;/span&gt;
&#8203;
  &lt;div class="menu-links"&gt;
    &lt;a routerLink="/products"&gt;
      Products
    &lt;/a&gt;
&#8203;
    &lt;a routerLink="/cart"&gt;
      My Cart
    &lt;/a&gt;
  &lt;/div&gt;
&#8203;
  &lt;app-auth&gt;
  &lt;/app-auth&gt;
&lt;/header&gt;</code></pre>
</div>



Para utilizar `routerLink` importamos `RouterLink`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  RouterLink,
  RouterOutlet
} from '@angular/router';</code></pre>
</div>



Y lo añadimos al `imports` del componente.

> 💡 En `routerLink`, una ruta que comienza con `/` es una ruta absoluta.

---

# 10. Mover la navegación al `router-outlet`

El contenido principal puede quedar:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;main class="main"&gt;
  &lt;div class="content"&gt;
    &lt;router-outlet /&gt;
  &lt;/div&gt;
&lt;/main&gt;</code></pre>
</div>



De esta forma dejamos de renderizar directamente `ProductListComponent`.

Ahora el componente visible depende del router.

---

# 11. Estilos para el menú

Ejemplo de estilos:



<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">header {
  display: flex;
  flex-direction: row;
  gap: 0.73rem;
  justify-content: end;
  margin-top: 1.5rem;
}
&#8203;
.menu-links {
  display: flex;
  align-items: center;
  gap: 0.73rem;
}
&#8203;
.menu-links a {
  transition: fill 0.3s ease;
  color: var(--gray-400);
}
&#8203;
.menu-links a:hover {
  color: var(--gray-900);
}</code></pre>
</div>



Y globalmente:



<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">a {
  text-decoration: none;
}
&#8203;
.spacer {
  flex: 1 1 auto;
}</code></pre>
</div>



---

# 12. Organizar rutas de la aplicación

El formulario de creación de productos puede tener una ruta propia:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'products/new',
  component:
    ProductCreateComponent
}</code></pre>
</div>



Ya no necesitamos mostrar `ProductCreateComponent` dentro de `ProductListComponent`.

Esto separa mejor responsabilidades:

```text
/products
    → listado

/products/new
    → creación
```

---

# 13. Navegación relativa

Desde `ProductListComponent`, que ya está activado bajo `/products`, podemos escribir:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a routerLink="new"&gt;
  Nuevo producto
&lt;/a&gt;</code></pre>
</div>



El resultado será:

`/products/new`

No es necesario escribir:

`/products/new`

porque el router puede resolver la ruta relativa al contexto actual.

---

# 14. Navegación imperativa con `Router`

A veces necesitamos navegar después de una acción.

Ejemplo: tras crear un producto.

Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Router
} from '@angular/router';</code></pre>
</div>



Inyectamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productsService:
    ProductsService,
  private router:
    Router
) {}</code></pre>
</div>



Y navegamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">createProduct(
  title: string,
  price: string,
  category: string
) {
  this.productsService
    .addProduct({
      title,
      price: Number(price),
      category
    })
    .subscribe(() =&gt; {
      this.router.navigate([
        '/products'
      ]);
    });
}</code></pre>
</div>



`navigate()` recibe un array de segmentos de ruta.

---

# 15. Mantener el caché de productos

Para evitar volver siempre al backend:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts():
  Observable&lt;Product[]&gt; {
&#8203;
  if (
    this.products.length === 0
  ) {
    const options =
      new HttpParams()
        .set('limit', 10);
&#8203;
    return this.http
      .get&lt;Product[]&gt;(
        this.productsUrl,
        {
          params: options
        }
      )
      .pipe(
        map(products =&gt; {
          this.products =
            products;
&#8203;
          return products;
        })
      );
  }
&#8203;
  return of(this.products);
}</code></pre>
</div>



---

# 16. Ruta por defecto

Para redirigir desde `/` hacia `/products`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '',
  redirectTo: 'products',
  pathMatch: 'full'
}</code></pre>
</div>



`pathMatch: 'full'` exige que la URL coincida completamente con la ruta vacía.

---

# 17. Ruta wildcard con redirección

En vez de mostrar una página 404, podemos redirigir:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '**',
  redirectTo: 'products'
}</code></pre>
</div>



Orden recomendado:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const routes:
  Routes = [
    // rutas específicas primero
&#8203;
    {
      path: '',
      redirectTo:
        'products',
      pathMatch:
        'full'
    },
&#8203;
    {
      path: '**',
      redirectTo:
        'products'
    }
  ];</code></pre>
</div>



---

# 18. Resaltar el enlace activo

Angular proporciona `RouterLinkActive`.

Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  RouterLink,
  RouterLinkActive,
  RouterOutlet
} from '@angular/router';</code></pre>
</div>



Plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div class="menu-links"&gt;
  &lt;a
    routerLink="/products"
    routerLinkActive="active"
  &gt;
    Products
  &lt;/a&gt;
&#8203;
  &lt;a
    routerLink="/cart"
    routerLinkActive="active"
  &gt;
    My Cart
  &lt;/a&gt;
&lt;/div&gt;</code></pre>
</div>



CSS:



<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">.menu-links a.active {
  color:
    var(--electric-violet);
}</code></pre>
</div>



---

# 19. Parámetros de ruta

Podemos definir una ruta dinámica:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'products/:id',
  component:
    ProductDetailComponent
}</code></pre>
</div>



El carácter `:` indica que `id` es un parámetro dinámico.

Ejemplos:

```text
/products/1
/products/5
/products/27
```

---

# 20. Crear enlaces con parámetros

En el listado:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@for (
  product of products | sort;
  track product.id
) {
  &lt;li class="pill"&gt;
    @switch (
      product.category
    ) {
      @case ('electronics') {
        🖥️
      }
      @case ('jewelery') {
        💎
      }
      @default {
        🏷️
      }
    }
&#8203;
    &lt;a
      [routerLink]="[
        product.id
      ]"
    &gt;
      {{ product.title }}
    &lt;/a&gt;
  &lt;/li&gt;
} @empty {
  &lt;p&gt;
    No products found!
  &lt;/p&gt;
}</code></pre>
</div>



Como el componente ya está activado en `/products`, `[product.id]` genera una ruta relativa como:

`/products/3`

---

# 21. Obtener parámetros con `ActivatedRoute`

Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  ActivatedRoute,
  Router
} from '@angular/router';</code></pre>
</div>



Y también `switchMap`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Observable,
  switchMap
} from 'rxjs';</code></pre>
</div>



Inyectamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService:
    ProductsService,
  public authService:
    AuthService,
  private route:
    ActivatedRoute,
  private router:
    Router
) {}</code></pre>
</div>



---

# 22. `paramMap`

En `ngOnInit()`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.product$ =
    this.route.paramMap
      .pipe(
        switchMap(params =&gt; {
          return this
            .productService
            .getProduct(
              Number(
                params.get('id')
              )
            );
        })
      );
}</code></pre>
</div>



`paramMap` es un observable.

`params.get('id')` devuelve el parámetro.

> 📌 Los parámetros de ruta llegan como cadenas, por eso usamos `Number(...)`.

---

# 23. `switchMap`

`switchMap` permite tomar un valor de un observable y transformarlo en otro observable.

En este caso:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">paramMap
   │
   │ id
   ▼
switchMap
   │
   ▼
getProduct(id)
   │
   ▼
Observable&lt;Product&gt;</code></pre>
</div>



---

# 24. Navegar después de actualizar o borrar

Ejemplo:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">changePrice(
  product: Product,
  price: string
) {
  this.productService
    .updateProduct(
      product.id,
      Number(price)
    )
    .subscribe(() =&gt; {
      this.router.navigate([
        '/products'
      ]);
    });
}
&#8203;
remove(product: Product) {
  this.productService
    .deleteProduct(
      product.id
    )
    .subscribe(() =&gt; {
      this.router.navigate([
        '/products'
      ]);
    });
}</code></pre>
</div>



---

# 25. `snapshot`

Si el componente no se reutiliza, podemos evitar el observable:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  const id =
    this.route
      .snapshot
      .params['id'];
&#8203;
  this.product$ =
    this.productService
      .getProduct(id);
}</code></pre>
</div>



`route.snapshot` representa el estado actual de la ruta.

> ✅ Si sabes que el componente no se reutilizará durante navegaciones consecutivas, `snapshot` puede simplificar el código.

---

# 26. Rutas hijas

Una alternativa es mantener el listado visible y renderizar el detalle dentro de él.

Configuración:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const routes:
  Routes = [
    {
      path: 'products',
      component:
        ProductListComponent,
&#8203;
      children: [
        {
          path: 'new',
          component:
            ProductCreateComponent
        },
        {
          path: ':id',
          component:
            ProductDetailComponent
        }
      ]
    },
&#8203;
    {
      path: 'cart',
      component:
        CartComponent
    },
&#8203;
    {
      path: '',
      redirectTo:
        'products',
      pathMatch:
        'full'
    },
&#8203;
    {
      path: '**',
      redirectTo:
        'products'
    }
  ];</code></pre>
</div>



El componente padre debe incluir un `router-outlet` para los hijos.

Conceptualmente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ProductListComponent
 ├── listado
 └── router-outlet
       ├── ProductCreateComponent
       └── ProductDetailComponent</code></pre>
</div>



---

# 27. Providers en rutas

Una ruta puede proporcionar servicios a sus hijos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'products',
  component:
    ProductListComponent,
&#8203;
  children: [
    {
      path: 'new',
      component:
        ProductCreateComponent
    },
    {
      path: ':id',
      component:
        ProductDetailComponent
    }
  ],
&#8203;
  providers: [
    ProductsService
  ]
}</code></pre>
</div>



Angular crea un inyector específico asociado a esa rama de rutas.

Esto permite limitar el alcance de un servicio.

---

# 28. Query parameters

Los query parameters son parámetros opcionales en la URL.

Ejemplo:

```text
/products?limit=5
```

En `ProductsService`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts(
  limit?: number
): Observable&lt;Product[]&gt; {
&#8203;
  if (
    this.products.length === 0
  ) {
    const options =
      new HttpParams()
        .set(
          'limit',
          limit || 10
        );
&#8203;
    return this.http
      .get&lt;Product[]&gt;(
        this.productsUrl,
        {
          params: options
        }
      )
      .pipe(
        map(products =&gt; {
          this.products =
            products;
&#8203;
          return products;
        })
      );
  }
&#8203;
  return of(this.products);
}</code></pre>
</div>



---

# 29. `queryParamMap`

En `ProductListComponent`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService:
    ProductsService,
  private route:
    ActivatedRoute
) {}</code></pre>
</div>



Y:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.products$ =
    this.route
      .queryParamMap
      .pipe(
        switchMap(params =&gt; {
          return this
            .productService
            .getProducts(
              Number(
                params.get(
                  'limit'
                )
              )
            );
        })
      );
}</code></pre>
</div>



Así:

`/products?limit=5`

puede producir una lista limitada a cinco elementos.

---

# 30. Route params frente a query params

| Tipo | Ejemplo | Uso |
|---|---|---|
| Route param | `/products/3` | Identificador necesario para una ruta |
| Query param | `/products?limit=5` | Filtro u opción normalmente opcional |

---

# 31. Vincular parámetros directamente a inputs

Angular permite enlazar parámetros de ruta con inputs del componente.

Activamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  provideRouter,
  withComponentInputBinding
} from '@angular/router';</code></pre>
</div>



Configuramos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">provideRouter(
  routes,
  withComponentInputBinding()
)</code></pre>
</div>



En el componente:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">id = input&lt;string&gt;();</code></pre>
</div>



Y podemos utilizar:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.product$ =
    this.productService
      .getProduct(
        Number(
          this.id()!
        )
      );
}</code></pre>
</div>



Ventajas:

- clase más simple;
- menos lógica con `ActivatedRoute`;
- permite reutilizar componentes ya diseñados con inputs.

---

# 32. Funcionalidades avanzadas del router

Angular Router también permite:

- controlar acceso a rutas;
- impedir salir de una ruta;
- precargar datos;
- cargar partes de la aplicación bajo demanda.

Estas funcionalidades se implementan mediante:

- guards;
- resolvers;
- lazy loading.

---

# 33. Guards

Generamos un guard:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate guard auth</code></pre>
</div>



Tipos principales:

| Guard | Función |
|---|---|
| `CanActivate` | Controla si una ruta puede activarse |
| `CanActivateChild` | Controla rutas hijas |
| `CanDeactivate` | Controla si se puede abandonar |
| `CanMatch` | Controla si una ruta puede coincidir |

---

# 34. `CanActivate`

Guard funcional:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  inject
} from '@angular/core';
&#8203;
import {
  CanActivateFn,
  Router
} from '@angular/router';
&#8203;
import {
  AuthService
} from './auth.service';
&#8203;
export const authGuard:
  CanActivateFn =
    () =&gt; {
&#8203;
      const authService =
        inject(AuthService);
&#8203;
      const router =
        inject(Router);
&#8203;
      if (
        authService
          .isLoggedIn()
      ) {
        return true;
      }
&#8203;
      return router.parseUrl(
        '/'
      );
    };</code></pre>
</div>



`parseUrl()` devuelve un `UrlTree`.

El router cancela la navegación actual y comienza otra hacia esa URL.

---

# 35. Aplicar un `CanActivate`

A la ruta del carrito:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'cart',
  component:
    CartComponent,
  canActivate: [
    authGuard
  ]
}</code></pre>
</div>



Solo usuarios autenticados pueden acceder.

---

# 36. `CanDeactivate`

Generamos:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate guard checkout</code></pre>
</div>



Ejemplo:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  CanDeactivateFn
} from '@angular/router';
&#8203;
import {
  CartComponent
} from './cart/cart.component';
&#8203;
export const checkoutGuard:
  CanDeactivateFn&lt;
    CartComponent
  &gt; = () =&gt; {
&#8203;
    const confirmation =
      confirm(
        'You have pending ' +
        'items in your cart. ' +
        'Do you want to continue?'
      );
&#8203;
    return confirmation;
  };</code></pre>
</div>



Registramos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'cart',
  component:
    CartComponent,
&#8203;
  canActivate: [
    authGuard
  ],
&#8203;
  canDeactivate: [
    checkoutGuard
  ]
}</code></pre>
</div>



También puede escribirse inline para casos sencillos.

---

# 37. Resolvers

Los resolvers permiten obtener datos **antes** de activar un componente.

Generamos:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate resolver products</code></pre>
</div>



El resolver puede devolver:

- valor;
- Promise;
- Observable.

Angular espera a que la operación finalice antes de continuar la navegación.

---

# 38. Resolver de productos

Ejemplo:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  inject
} from '@angular/core';
&#8203;
import {
  ResolveFn
} from '@angular/router';
&#8203;
import {
  Product
} from './product';
&#8203;
import {
  ProductsService
} from './products.service';
&#8203;
export const productsResolver:
  ResolveFn&lt;Product[]&gt; =
    (route, state) =&gt; {
&#8203;
      const productService =
        inject(
          ProductsService
        );
&#8203;
      const limit =
        Number(
          route
            .queryParamMap
            .get('limit')
        );
&#8203;
      return productService
        .getProducts(limit);
    };</code></pre>
</div>



---

# 39. Registrar un resolver

En la ruta:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'products',
  component:
    ProductListComponent,
&#8203;
  resolve: {
    products:
      productsResolver
  }
}</code></pre>
</div>



La clave `products` permite recuperar el dato resuelto.

---

# 40. Leer datos resueltos

En el componente:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.products$ =
    this.route.data.pipe(
      switchMap(data =&gt;
        of(
          data['products']
        )
      )
    );
}</code></pre>
</div>



Así el componente se activa con los datos ya disponibles.

---

# 41. Lazy loading

Lazy loading significa no cargar inicialmente partes de la aplicación que todavía no se necesitan.

Ventajas:

- menor bundle inicial;
- carga bajo demanda;
- mejor tiempo de arranque;
- escalabilidad en aplicaciones grandes.

---

# 42. Lazy loading de rutas

Creamos:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component user</code></pre>
</div>



Archivo `user.routes.ts`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  UserComponent
} from './user/user.component';
&#8203;
export default [
  {
    path: '',
    component:
      UserComponent
  }
];</code></pre>
</div>



En `app.routes.ts`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'user',
&#8203;
  loadChildren:
    () =&gt;
      import(
        './user.routes'
      )
}</code></pre>
</div>



El navegador cargará ese chunk cuando se navegue por primera vez a `/user`.

---

# 43. Enlace hacia una ruta lazy



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div class="menu-links"&gt;
  &lt;a
    routerLink="/products"
    routerLinkActive="active"
  &gt;
    Products
  &lt;/a&gt;
&#8203;
  &lt;a
    routerLink="/cart"
    routerLinkActive="active"
  &gt;
    My Cart
  &lt;/a&gt;
&#8203;
  &lt;a
    routerLink="/user"
    routerLinkActive="active"
  &gt;
    My Profile
  &lt;/a&gt;
&lt;/div&gt;</code></pre>
</div>



La primera vez que el usuario entra en `My Profile`, Angular solicita el chunk correspondiente.

Después queda disponible en memoria para navegaciones posteriores.

---

# 44. Lazy loading de un componente

También podemos usar `loadComponent`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'user',
&#8203;
  loadComponent:
    () =&gt;
      import(
        './user/user.component'
      )
      .then(c =&gt;
        c.UserComponent
      )
}</code></pre>
</div>



---

# 45. Proteger una ruta lazy con `CanMatch`

Extendemos el guard:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  CanActivateFn,
  CanMatchFn,
  Router
} from '@angular/router';
&#8203;
export const authGuard:
  CanActivateFn |
  CanMatchFn =
    () =&gt; {
&#8203;
      const authService =
        inject(AuthService);
&#8203;
      const router =
        inject(Router);
&#8203;
      if (
        authService
          .isLoggedIn()
      ) {
        return true;
      }
&#8203;
      return router.parseUrl(
        '/'
      );
    };</code></pre>
</div>



En la ruta:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'user',
&#8203;
  loadChildren:
    () =&gt;
      import(
        './user.routes'
      ),
&#8203;
  canMatch: [
    authGuard
  ]
}</code></pre>
</div>



`CanMatch` permite bloquear incluso el matching de la ruta.

---

# 46. Orden de evaluación de rutas

Angular Router utiliza **first-match-wins**.

Por tanto, el orden importa.

Ejemplo correcto:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const routes:
  Routes = [
    {
      path: 'products/new',
      component:
        ProductCreateComponent
    },
&#8203;
    {
      path: 'products/:id',
      component:
        ProductDetailComponent
    },
&#8203;
    {
      path: 'products',
      component:
        ProductListComponent
    },
&#8203;
    {
      path: '',
      redirectTo:
        'products',
      pathMatch:
        'full'
    },
&#8203;
    {
      path: '**',
      redirectTo:
        'products'
    }
  ];</code></pre>
</div>



Las rutas más específicas deben ir antes de las más generales.

---

# 47. Esquema general de Angular Router



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">URL / routerLink
      │
      ▼
Route configuration
      │
      ├── guards
      ├── resolvers
      ├── params
      ├── query params
      └── lazy loading
      │
      ▼
RouterOutlet
      │
      ▼
Componente activado</code></pre>
</div>



---

# 48. Resumen

En esta unidad hemos aprendido a convertir una aplicación Angular con múltiples componentes visibles simultáneamente en una aplicación organizada mediante rutas.

Hemos trabajado con:

- `provideRouter`;
- `Routes`;
- `RouterOutlet`;
- `RouterLink`;
- `RouterLinkActive`;
- rutas absolutas y relativas;
- navegación imperativa con `Router`;
- rutas por defecto;
- wildcard routes;
- parámetros dinámicos;
- `ActivatedRoute`;
- `paramMap`;
- `snapshot`;
- `queryParamMap`;
- `withComponentInputBinding`;
- rutas hijas;
- providers en rutas;
- guards;
- `CanActivate`;
- `CanDeactivate`;
- resolvers;
- lazy loading;
- `loadChildren`;
- `loadComponent`;
- `CanMatch`.

---

# 49. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| SPA | Aplicación de una sola página |
| Router | Gestiona navegación |
| Route | Asociación entre URL y componente |
| `provideRouter()` | Activa/configura router |
| `RouterOutlet` | Punto de renderizado |
| `RouterLink` | Navegación declarativa |
| `RouterLinkActive` | Clase según ruta activa |
| `Router` | Navegación programática |
| `ActivatedRoute` | Información de la ruta activa |
| `paramMap` | Observable de parámetros |
| `snapshot` | Estado actual de parámetros |
| Query parameter | Parámetro opcional en URL |
| `queryParamMap` | Observable de query params |
| `withComponentInputBinding()` | Ruta → input |
| Child route | Ruta hija |
| Guard | Control de navegación |
| `CanActivate` | Permite/bloquea entrada |
| `CanDeactivate` | Permite/bloquea salida |
| `CanMatch` | Controla matching |
| Resolver | Precarga datos |
| Lazy loading | Carga bajo demanda |
| `loadChildren` | Lazy loading de rutas |
| `loadComponent` | Lazy loading de componente |
| `UrlTree` | Representación de una URL de router |

---

## 🔗 Recursos citados en el material

- Angular Router: <https://angular.dev/guide/routing>
- MDN `pushState`: <https://developer.mozilla.org/docs/Web/API/History/pushState>
- MDN Falsy: <https://developer.mozilla.org/docs/Glossary/Falsy>

---

> 🎓 **Siguiente paso**  
> Una vez dominada la navegación, el siguiente paso será trabajar con **formularios Angular** para recoger y validar datos introducidos por el usuario.

---

# 50. Taller práctico: Routing en Angular 🧪

Los ejercicios de esta sección toman como referencia directa el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/09_rutas>

El estado actual del repositorio es especialmente adecuado para estudiar routing porque ya contiene:

- rutas para listado, alta y detalle de productos;
- una ruta protegida para el carrito;
- una ruta lazy para el perfil;
- `RouterLink`, `RouterLinkActive` y `RouterOutlet`;
- navegación imperativa mediante `Router`;
- parámetros de ruta mediante `ActivatedRoute.paramMap`;
- un `CanActivate`/`CanMatch` guard;
- un `CanDeactivate` guard;
- un resolver de productos;
- recuperación de datos resueltos mediante `ActivatedRoute.data`.

> 🎯 **Forma recomendada de trabajo**  
> Ejecuta los ejercicios en orden y mantén abiertas tanto la consola como la pestaña **Network** de las herramientas de desarrollo. Antes de cambiar una ruta, intenta predecir qué componente se activará y qué URL aparecerá en el navegador.

---

## Ejercicio 1. Dibuja el mapa de rutas real

### Objetivo

Comprender la configuración inicial del repositorio antes de modificarla.

Abre `app.routes.ts` y representa las rutas existentes mediante un árbol:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">/
└── redirect → /products
&#8203;
/products
/products/new
/products/:id
/cart
/user
/**
&#8203;</code></pre>
</div>


Completa una tabla:

| Ruta | Componente / carga | Protección | Datos previos |
|---|---|---|---|
| `/products` |  |  |  |
| `/products/new` |  |  |  |
| `/products/:id` |  |  |  |
| `/cart` |  |  |  |
| `/user` |  |  |  |
| `/**` |  |  |  |

### Pregunta

¿Qué rutas se cargan directamente mediante `component` y cuál utiliza `loadChildren`?

---

## Ejercicio 2. Comprueba el `RouterOutlet` principal

### Objetivo

Relacionar la URL activa con el lugar donde se renderiza el componente.

El `AppComponent` contiene un único:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;router-outlet /&gt;</code></pre>
</div>


### Tareas

1. Navega a `/products`.
2. Inspecciona el DOM.
3. Navega a `/cart`.
4. Comprueba qué componente aparece junto al `router-outlet`.
5. Navega a `/user`.

### Reflexión

¿Por qué `AppComponent` no necesita importar directamente `ProductListComponent`, `CartComponent` o `UserComponent` para mostrarlos mediante routing?

---

## Ejercicio 3. Analiza el menú principal

### Objetivo

Distinguir navegación declarativa de enlaces HTML tradicionales.

El repositorio utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a
  routerLink="/products"
  routerLinkActive="active"
&gt;
  Products
&lt;/a&gt;
&#8203;
&lt;a
  routerLink="/cart"
  routerLinkActive="active"
&gt;
  My Cart
&lt;/a&gt;
&#8203;
&lt;a
  routerLink="/user"
  routerLinkActive="active"
&gt;
  My Profile
&lt;/a&gt;</code></pre>
</div>


### Tareas

1. Navega utilizando cada enlace.
2. Comprueba que no se produce una recarga completa.
3. Observa qué enlace recibe la clase `active`.
4. Inspecciona el historial del navegador con Atrás/Adelante.

### Pregunta

¿Qué aporta `RouterLink` respecto a un enlace que fuerza una navegación tradicional?

---

## Ejercicio 4. Añade una ruta nueva

### Objetivo

Practicar una ruta estática sencilla.

Genera:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component about</code></pre>
</div>


Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'about',
  component: AboutComponent
}</code></pre>
</div>


Y crea un enlace:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a
  routerLink="/about"
  routerLinkActive="active"
&gt;
  About
&lt;/a&gt;</code></pre>
</div>


### Comprobación

La URL debe ser `/about` y el componente debe aparecer en el `RouterOutlet` principal.

---

## Ejercicio 5. Experimenta con la ruta por defecto

### Objetivo

Comprender `redirectTo` y `pathMatch`.

El repositorio contiene:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '',
  redirectTo: 'products',
  pathMatch: 'full'
}</code></pre>
</div>


### Tareas

1. Navega a `http://localhost:4200/`.
2. Comprueba la redirección.
3. Cambia temporalmente `products` por `cart`.
4. Recarga la raíz.
5. Restaura la configuración.

### Pregunta

¿Qué función cumple `pathMatch: 'full'` en esta ruta vacía?

---

## Ejercicio 6. Comprueba la wildcard route

### Objetivo

Entender el fallback del router.

El repositorio finaliza con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '**',
  redirectTo: 'products'
}</code></pre>
</div>


### Tareas

Navega a:

```text
/no-existe
/productos
/xyz/123
```

Comprueba el resultado.

### Reto

Genera un `PageNotFoundComponent` y sustituye temporalmente la redirección por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '**',
  component:
    PageNotFoundComponent
}</code></pre>
</div>


---

## Ejercicio 7. Rompe deliberadamente el orden de rutas

### Objetivo

Comprobar por qué la wildcard debe estar al final.

Mueve temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: '**',
  redirectTo: 'products'
}</code></pre>
</div>


al principio del array.

### Tareas

1. Intenta entrar en `/cart`.
2. Intenta entrar en `/user`.
3. Explica qué ocurre.
4. Restaura la wildcard al final.

### Concepto

Angular evalúa las rutas con una estrategia **first-match-wins**.

---

## Ejercicio 8. Analiza la navegación relativa a `products/new`

### Objetivo

Comprender por qué el repositorio utiliza `routerLink="new"`.

En `product-list.component.html` existe:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a routerLink="new"&gt;
  ...
&lt;/a&gt;</code></pre>
</div>


### Tareas

1. Entra en `/products`.
2. Pulsa el enlace.
3. Comprueba que navega a `/products/new`.
4. Sustituye temporalmente por:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a routerLink="/new"&gt;
  Nuevo
&lt;/a&gt;</code></pre>
</div>


5. Compara ambas URLs.

### Pregunta

¿Qué diferencia existe entre navegación relativa y absoluta?

---

## Ejercicio 9. Navegación imperativa después de crear

### Objetivo

Analizar el uso real del servicio `Router`.

`ProductCreateComponent` ejecuta una navegación después de crear el producto.

### Tareas

1. Localiza `Router` en el constructor.
2. Localiza `router.navigate(...)`.
3. Crea un producto.
4. Comprueba que vuelves a `/products`.

### Modificación

Cambia temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.router.navigate([
  '/products'
]);</code></pre>
</div>


por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.router.navigate([
  '/cart'
]);</code></pre>
</div>


Comprueba la diferencia y restaura después el código.

---

## Ejercicio 10. Parámetros dinámicos de producto

### Objetivo

Comprender la ruta `/products/:id`.

La configuración contiene:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'products/:id',
  component:
    ProductDetailComponent
}</code></pre>
</div>


y el listado genera enlaces con:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a
  [routerLink]="[
    product.id
  ]"
&gt;
  {{ product.title }}
&lt;/a&gt;</code></pre>
</div>


### Tareas

1. Pulsa varios productos.
2. Anota las URLs.
3. Identifica qué parte permanece fija.
4. Identifica qué parte es el parámetro.

---

## Ejercicio 11. Lee el parámetro con `paramMap`

### Objetivo

Seguir el flujo real de `id` hasta `ProductsService`.

El detalle utiliza `ActivatedRoute.paramMap` y `switchMap`.

Representa:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">URL /products/7
      │
      ▼
ActivatedRoute.paramMap
      │
      │ get('id')
      ▼
"7"
      │
      │ Number(...)
      ▼
7
      │
      ▼
ProductsService.getProduct(7)
      │
      ▼
Observable&lt;Product&gt;</code></pre>
</div>


### Preguntas

1. ¿Por qué `params.get('id')` debe convertirse a número?
2. ¿Qué función cumple `switchMap`?
3. ¿Qué ocurriría si el parámetro no existiese?

---

## Ejercicio 12. Usa `snapshot` como alternativa

### Objetivo

Comparar lectura reactiva y lectura puntual de parámetros.

Sustituye temporalmente `paramMap` por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  const id =
    this.route
      .snapshot
      .params['id'];
&#8203;
  this.product$ =
    this.productService
      .getProduct(
        Number(id)
      );
}</code></pre>
</div>


### Tareas

1. Comprueba que el detalle sigue funcionando.
2. Explica qué información representa `snapshot`.
3. Restaura `paramMap`.

### Reflexión

¿Cuándo preferirías `snapshot` y cuándo un observable de parámetros?

---

## Ejercicio 13. Activa `withComponentInputBinding`

### Objetivo

Probar una tercera forma de recibir parámetros de ruta.

Actualmente `app.config.ts` registra:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">provideRouter(routes)</code></pre>
</div>


Modifícalo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  provideRouter,
  withComponentInputBinding
} from '@angular/router';
&#8203;
provideRouter(
  routes,
  withComponentInputBinding()
)</code></pre>
</div>


En `ProductDetailComponent`, cambia:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">id = input&lt;string&gt;();</code></pre>
</div>


y utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.product$ =
    this.productService
      .getProduct(
        Number(
          this.id()!
        )
      );
}</code></pre>
</div>


### Tareas

1. Elimina temporalmente `ActivatedRoute` del detalle.
2. Comprueba el funcionamiento.
3. Compara la cantidad de código con `paramMap`.

---

## Ejercicio 14. Query parameter `limit`

### Objetivo

Relacionar parámetros de navegación con parámetros HTTP.

El resolver del repositorio lee:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const limit =
  Number(
    route.queryParamMap
      .get('limit')
  );</code></pre>
</div>


### Tareas

Prueba:

```text
/products?limit=2
/products?limit=5
/products?limit=8
```

Recarga la aplicación en cada caso.

### Preguntas

1. ¿Qué recibe el resolver?
2. ¿A qué método se envía `limit`?
3. ¿Cómo termina convertido en un parámetro HTTP?

---

## Ejercicio 15. Genera enlaces con query params

### Objetivo

Evitar escribir parámetros manualmente en la barra del navegador.

Añade al listado:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a
  routerLink="/products"
  [queryParams]="{
    limit: 5
  }"
&gt;
  Ver 5 productos
&lt;/a&gt;
&#8203;
&lt;a
  routerLink="/products"
  [queryParams]="{
    limit: 10
  }"
&gt;
  Ver 10 productos
&lt;/a&gt;</code></pre>
</div>


### Tareas

1. Comprueba la URL.
2. Observa qué valor recibe el resolver.
3. Comprueba el número de productos devueltos.

---

## Ejercicio 16. Analiza el resolver actual

### Objetivo

Comprender por qué el listado obtiene sus datos desde `route.data`.

El repositorio registra:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">resolve: {
  products:
    productsResolver
}</code></pre>
</div>


y `ProductListComponent` consume:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.products$ =
  this.route.data.pipe(
    switchMap(data =&gt;
      of(
        data['products']
      )
    )
  );</code></pre>
</div>


### Tareas

1. Añade un `console.log` dentro de `productsResolver`.
2. Añade otro en `ProductListComponent.ngOnInit`.
3. Recarga `/products`.
4. Comprueba cuál se ejecuta primero.

### Conclusión

¿Por qué el resolver resulta útil cuando el componente necesita datos antes de mostrarse?

---

## Ejercicio 17. Simplifica el consumo de `route.data`

### Objetivo

Analizar si `switchMap(... of(...))` es necesario para un dato ya disponible.

Prueba como ejercicio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.products$ =
  this.route.data.pipe(
    map(data =&gt;
      data['products']
        as Product[]
    )
  );</code></pre>
</div>


Importa `map` desde RxJS.

### Pregunta

¿Qué diferencia conceptual existe entre `map` y `switchMap` en este caso?

---

## Ejercicio 18. Modifica el resolver para aplicar un límite por defecto

### Objetivo

Gestionar correctamente un query param ausente.

Implementa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const rawLimit =
  Number(
    route.queryParamMap
      .get('limit')
  );
&#8203;
const limit =
  rawLimit &gt; 0
    ? rawLimit
    : 10;
&#8203;
return productService
  .getProducts(limit);</code></pre>
</div>


### Tareas

Prueba:

```text
/products
/products?limit=0
/products?limit=5
```

Explica el resultado esperado en cada caso.

---

## Ejercicio 19. Analiza `authGuard`

### Objetivo

Comprender un guard que sirve tanto para `CanActivate` como para `CanMatch`.

El repositorio declara el guard con dos tipos y comprueba `AuthService.isLoggedIn()`.

### Tareas

1. Intenta entrar en `/cart` sin autenticarte.
2. Inicia sesión.
3. Vuelve a entrar.
4. Cierra sesión.
5. Intenta entrar en `/user`.

### Preguntas

1. ¿En qué ruta se utiliza como `canActivate`?
2. ¿En cuál se utiliza como `canMatch`?
3. ¿Qué devuelve cuando el usuario no está autenticado?

---

## Ejercicio 20. Cambia la redirección del guard

### Objetivo

Experimentar con `UrlTree`.

Modifica temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return router.parseUrl(
  '/'
);</code></pre>
</div>


por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return router.parseUrl(
  '/products'
);</code></pre>
</div>


Comprueba el comportamiento al intentar entrar en `/cart` sin autenticación.

### Reflexión

¿Por qué un guard puede devolver un `UrlTree` en lugar de llamar directamente a `navigate()`?

---

## Ejercicio 21. Analiza `checkoutGuard`

### Objetivo

Comprender `CanDeactivate`.

Entra en el carrito después de autenticarte e intenta:

- pulsar `Products`;
- pulsar `My Profile`;
- usar el botón Atrás del navegador.

### Tareas

1. Pulsa **Cancel** en el diálogo.
2. Repite y pulsa **OK**.
3. Explica qué controla el guard.

### Reto

Cambia temporalmente el mensaje de confirmación e incluye el destino conceptual de la operación.

---

## Ejercicio 22. Haz que `CanDeactivate` dependa del estado del carrito

### Objetivo

Evitar pedir confirmación cuando no existe nada pendiente.

Añade temporalmente una propiedad a `CartComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">hasPendingItems = false;</code></pre>
</div>


Modifica el guard:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const checkoutGuard:
  CanDeactivateFn&lt;CartComponent&gt; =
    component =&gt; {
&#8203;
      if (
        !component.hasPendingItems
      ) {
        return true;
      }
&#8203;
      return confirm(
        'Hay productos pendientes. ' +
        '¿Deseas salir?'
      );
    };</code></pre>
</div>


### Tareas

Prueba con `true` y `false`.

---

## Ejercicio 23. Analiza el lazy loading del perfil

### Objetivo

Comprobar que `/user` se carga bajo demanda.

La configuración utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'user',
  loadChildren:
    () =&gt;
      import(
        './user.routes'
      ),
  canMatch: [
    authGuard
  ]
}</code></pre>
</div>


### Tareas

1. Inicia la aplicación sin visitar `/user`.
2. Abre **Network**.
3. Autentícate.
4. Pulsa `My Profile`.
5. Localiza el chunk cargado dinámicamente.
6. Sal de la ruta y vuelve a entrar.

### Pregunta

¿Por qué normalmente no se solicita de nuevo el mismo chunk en la segunda navegación?

---

## Ejercicio 24. Lazy loading de componente en lugar de rutas

### Objetivo

Comparar `loadChildren` y `loadComponent`.

Sustituye temporalmente la ruta por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'user',
&#8203;
  loadComponent:
    () =&gt;
      import(
        './user/user.component'
      )
      .then(
        module =&gt;
          module.UserComponent
      ),
&#8203;
  canMatch: [
    authGuard
  ]
}</code></pre>
</div>


### Tareas

1. Comprueba que sigue funcionando.
2. Inspecciona Network.
3. Explica la diferencia entre cargar un componente y cargar una configuración de rutas.

---

## Ejercicio 25. Refactoriza productos con rutas hijas

### Objetivo

Experimentar con una estructura master-detail.

El repositorio actual utiliza rutas hermanas:

```text
/products
/products/new
/products/:id
```

Refactoriza temporalmente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  path: 'products',
  component:
    ProductListComponent,
&#8203;
  children: [
    {
      path: 'new',
      component:
        ProductCreateComponent
    },
    {
      path: ':id',
      component:
        ProductDetailComponent
    }
  ],
&#8203;
  resolve: {
    products:
      productsResolver
  }
}</code></pre>
</div>


Añade en la plantilla de `ProductListComponent`:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;router-outlet /&gt;</code></pre>
</div>


e importa `RouterOutlet`.

### Tareas

1. Navega a un producto.
2. Comprueba que el listado permanece visible.
3. Navega directamente de un producto a otro.
4. Observa el comportamiento de `paramMap`.

### Reflexión

¿Por qué este diseño favorece la reutilización de `ProductDetailComponent` durante navegaciones consecutivas?

---

# 51. Reto integrador: navegación completa de la tienda 🚀

### Objetivo

Construir una navegación coherente sobre el proyecto del repositorio.

La solución deberá incluir:

- página de productos;
- página de alta;
- detalle mediante parámetro;
- query parameter `limit`;
- carrito protegido;
- confirmación de salida del carrito;
- resolver de productos;
- perfil lazy;
- protección del perfil mediante `CanMatch`;
- menú con enlace activo.

### Requisitos

- [ ] Existe una ruta por defecto.
- [ ] Existe una wildcard route al final.
- [ ] Se utiliza `RouterLink`.
- [ ] Se utiliza `RouterLinkActive`.
- [ ] Existe navegación imperativa con `Router`.
- [ ] Existe al menos un route parameter.
- [ ] Existe al menos un query parameter.
- [ ] Se utiliza `ActivatedRoute` o `withComponentInputBinding`.
- [ ] Existe un `CanActivate`.
- [ ] Existe un `CanDeactivate`.
- [ ] Existe un `CanMatch`.
- [ ] Existe un resolver.
- [ ] Existe una ruta lazy.
- [ ] Se comprueba el chunk lazy en DevTools.
- [ ] El proyecto compila sin errores.

### Ampliación

Crea además una ruta:

```text
/products?limit=5
```

accesible desde un enlace `Top 5`, y otra `Todos` que vuelva al límite por defecto.

---

# 52. Diagnóstico de errores de routing 🔎

## Caso A. Wildcard antes de rutas específicas

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const routes:
  Routes = [
    {
      path: '**',
      redirectTo:
        'products'
    },
    {
      path: 'cart',
      component:
        CartComponent
    }
  ];</code></pre>
</div>


**Pregunta:** ¿por qué `/cart` no llegará a activarse como esperamos?

---

## Caso B. Falta `RouterLink` en imports

La plantilla utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;a routerLink="/products"&gt;
  Products
&lt;/a&gt;</code></pre>
</div>


pero el componente standalone no importa `RouterLink`.

**Pregunta:** ¿qué debe corregirse?

---

## Caso C. Parámetro con nombre incorrecto

La ruta declara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">path: 'products/:id'</code></pre>
</div>


pero el componente intenta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">params.get(
  'productId'
)</code></pre>
</div>


**Pregunta:** ¿qué valor obtendrá?

---

## Caso D. Query param inválido

La URL contiene:

```text
/products?limit=abc
```

y se ejecuta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">Number(
  route.queryParamMap
    .get('limit')
)</code></pre>
</div>


**Pregunta:** ¿qué valor numérico obtendremos y cómo convendría validarlo?

---

## Caso E. Ruta lazy sin autenticación

Se elimina:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">canMatch: [
  authGuard
]</code></pre>
</div>


de `/user`.

**Pregunta:** ¿qué cambia desde el punto de vista de acceso a la ruta?

---

## Caso F. Resolver que no completa

Un resolver devuelve un observable que nunca completa ni produce el valor esperado.

**Pregunta:** ¿qué efecto puede tener sobre la navegación?

---

# 53. Cuestiones finales de reflexión 📝

1. ¿Qué diferencia existe entre una aplicación tradicional y una SPA?
2. ¿Qué función desempeña `RouterOutlet`?
3. ¿Qué diferencia existe entre `RouterLink` y `Router.navigate()`?
4. ¿Qué diferencia hay entre una ruta absoluta y una relativa?
5. ¿Para qué sirve `RouterLinkActive`?
6. ¿Qué significa `:id` en una ruta?
7. ¿Por qué los parámetros de ruta llegan como cadenas?
8. ¿Qué diferencia existe entre `paramMap` y `snapshot`?
9. ¿Para qué sirve `switchMap` al trabajar con parámetros?
10. ¿Qué diferencia existe entre route params y query params?
11. ¿Qué aporta `withComponentInputBinding()`?
12. ¿Cuándo resultan útiles las rutas hijas?
13. ¿Qué significa *first-match-wins*?
14. ¿Por qué la wildcard debe estar al final?
15. ¿Qué controla `CanActivate`?
16. ¿Qué controla `CanDeactivate`?
17. ¿Qué controla `CanMatch`?
18. ¿Qué ventaja aporta devolver un `UrlTree` desde un guard?
19. ¿Qué problema resuelve un resolver?
20. ¿Qué es lazy loading?
21. ¿Qué diferencia existe entre `loadChildren` y `loadComponent`?
22. ¿Por qué una ruta lazy puede mejorar el bundle inicial?
23. ¿Qué combinación de routing utilizarías para una pantalla master-detail?
24. ¿Qué información revisarías en DevTools para comprobar que una ruta realmente se está cargando de forma lazy?

---

> ✅ **Meta de aprendizaje**  
> Si puedes diseñar el mapa de rutas de una aplicación, transportar parámetros, proteger navegación, precargar datos y decidir qué partes cargar bajo demanda, ya dominas los fundamentos y varias técnicas avanzadas del Angular Router.
