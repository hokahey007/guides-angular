# 🌐 Unidad 8: Comunicación con servicios de datos mediante HTTP

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

> **Referencia del PDF:** capítulo 8 de Angular 19.  
> **Objetivo:** aprender a comunicar una aplicación Angular con servicios backend mediante **HttpClient**, operaciones CRUD, autenticación, autorización e interceptores.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Comprender cómo Angular intercambia datos con APIs remotas.
- Configurar `provideHttpClient()`.
- Utilizar `HttpClient`.
- Realizar peticiones `GET`, `POST`, `PATCH` y `DELETE`.
- Enviar parámetros mediante `HttpParams`.
- Implementar operaciones CRUD.
- Trabajar con `Observable<T>` en peticiones HTTP.
- Mantener un pequeño caché local de productos.
- Crear componentes para alta y modificación de datos.
- Implementar autenticación mediante un servicio.
- Gestionar autorización utilizando Signals.
- Añadir cabeceras HTTP.
- Crear y registrar interceptores funcionales.
- Comprender el orden de ejecución de interceptores.

---

## 📑 Contenidos

1. [Comunicación HTTP en Angular](#13-comunicación-http-en-aplicaciones-angular)
2. [HttpClient y CRUD](#3-angular-httpclient)
3. [Productos y caché local](#6-configurar-productsservice-para-http)
4. [Crear, actualizar y eliminar productos](#11-crear-productos-con-post)
5. [Autenticación y autorización](#16-autenticación-mediante-http)
6. [Cabeceras e interceptores](#21-cabeceras-http)
7. [Taller práctico](#30-taller-práctico-comunicación-http-y-crud)

---

# 1. Comunicación con servicios de datos mediante HTTP

---

Las aplicaciones empresariales suelen necesitar intercambiar datos con APIs remotas.

Antes de estudiar `HttpClient`, podemos recordar la API nativa `fetch`:


<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">fetch(url)
  .then(response =&gt; {
    return response.ok
      ? response.text()
      : '';
  })
  .then(result =&gt; {
    if (result) {
      console.log(result);
    } else {
      console.error(
        'An error has occurred'
      );
    }
  });</code></pre>
</div>



`fetch` trabaja con promesas. El material destaca que la promesa no se rechaza automáticamente simplemente porque el servidor responda con un estado HTTP no exitoso; debemos comprobar `response.ok`.

---

# 2. Envolver `fetch` en un Observable

Podemos convertir la llamada en un stream:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const request$ =
  new Observable(observer =&gt; {
    fetch(url)
      .then(response =&gt; {
        return response.ok
          ? response.text()
          : '';
      })
      .then(result =&gt; {
        if (result) {
          observer.next(result);
          observer.complete();
        } else {
          observer.error(
            'An error has occurred'
          );
        }
      });
  });</code></pre>
</div>



Métodos del observer:

| Método       | Función                             |
| ------------ | ----------------------------------- |
| `next()`     | Emite datos                         |
| `complete()` | Informa de que no habrá más valores |
| `error()`    | Notifica un error                   |

Este ejemplo muestra cómo una API asíncrona puede adaptarse al modelo Observable.

---

# 3. Angular `HttpClient`

El cliente HTTP de Angular se encuentra dentro de:

`@angular/common/http`

Para activarlo importamos `provideHttpClient` en `app.config.ts`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  provideHttpClient
} from '@angular/common/http';
&#8203;
import {
  ApplicationConfig,
  provideZoneChangeDetection
} from '@angular/core';
&#8203;
import {
  provideRouter
} from '@angular/router';
&#8203;
import { routes }
  from './app.routes';
&#8203;
export const appConfig:
  ApplicationConfig = {
    providers: [
      provideZoneChangeDetection({
        eventCoalescing: true
      }),
      provideRouter(routes),
      provideHttpClient()
    ]
  };</code></pre>
</div>



Métodos CRUD principales de `HttpClient`:

| Método          | Operación  |
| --------------- | ---------- |
| `get`           | Leer datos |
| `post`          | Crear      |
| `put` / `patch` | Actualizar |
| `delete`        | Eliminar   |

Todos devuelven observables.

---

# 4. CRUD

CRUD significa:

```text
Create
Read
Update
Delete
```

En el material se utiliza **Fake Store API** como backend de pruebas:

<https://fakestoreapi.com>

Endpoints principales mencionados:

- `products`;
- `cart`;
- `user`;
- `login`.

> ⚠️ El material advierte que las operaciones de modificación de Fake Store API no persisten físicamente los cambios en una base de datos.

---

# 5. Configuración global de la API

Añadimos `APP_SETTINGS` en `app.config.ts`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  APP_SETTINGS,
  appSettings
} from './app.settings';
&#8203;
export const appConfig:
  ApplicationConfig = {
    providers: [
      provideZoneChangeDetection({
        eventCoalescing: true
      }),
      provideRouter(routes),
      provideHttpClient(),
      {
        provide: APP_SETTINGS,
        useValue: appSettings
      }
    ]
  };</code></pre>
</div>



En `app.settings.ts`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  InjectionToken
} from '@angular/core';
&#8203;
export interface AppSettings {
  title: string;
  version: string;
  apiUrl: string;
}
&#8203;
export const appSettings:
  AppSettings = {
    title: 'My e-shop',
    version: '1.0',
    apiUrl:
      'https://fakestoreapi.com'
  };
&#8203;
export const APP_SETTINGS =
  new InjectionToken&lt;AppSettings&gt;(
    'app.settings'
  );</code></pre>
</div>



El componente raíz puede consumir directamente:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;header&gt;
  {{ settings.title }}
&lt;/header&gt;</code></pre>
</div>



---

# 6. Configurar `ProductsService` para HTTP

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpClient
} from '@angular/common/http';
&#8203;
import {
  Injectable,
  inject
} from '@angular/core';
&#8203;
import { Product }
  from './product';
&#8203;
import {
  Observable,
  of
} from 'rxjs';
&#8203;
import {
  APP_SETTINGS
} from './app.settings';</code></pre>
</div>



Creamos la URL:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private productsUrl =
  inject(APP_SETTINGS).apiUrl
    + '/products';</code></pre>
</div>



Inyectamos `HttpClient`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private http: HttpClient
) {}</code></pre>
</div>



Y obtenemos los productos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts():
  Observable&lt;Product[]&gt; {
&#8203;
  return this.http.get&lt;Product[]&gt;(
    this.productsUrl
  );
}</code></pre>
</div>



El genérico `Product[]` indica el tipo esperado de la respuesta.

---

# 7. Parámetros de consulta con `HttpParams`

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpClient,
  HttpParams
} from '@angular/common/http';</code></pre>
</div>



Creamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const options =
  new HttpParams()
    .set('limit', 10);</code></pre>
</div>



Podemos encadenar parámetros:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const options =
  new HttpParams()
    .set('limit', 10)
    .set('page', 1);</code></pre>
</div>



Y pasarlos a la petición:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return this.http.get&lt;Product[]&gt;(
  this.productsUrl,
  {
    params: options
  }
);</code></pre>
</div>



> 🧠 `HttpParams` es inmutable. Cada operación devuelve una nueva instancia.

Este código no tendría el efecto esperado:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const options =
  new HttpParams();
&#8203;
options.set('limit', 10);</code></pre>
</div>



---

# 8. Adaptar el modelo `Product` al backend

El material cambia la propiedad `categories` por:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">category: string;</code></pre>
</div>



La plantilla puede elegir icono según la categoría:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@switch (product.category) {
  @case ('electronics') { 🖥️ }
  @case ('jewelery') { 💎 }
  @default { 🏷️ }
}</code></pre>
</div>



Y el detalle:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (product()) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;
      {{ product()!.title }}
    &lt;/strong&gt;
  &lt;/p&gt;
&#8203;
  &lt;p&gt;
    {{
      product()!.price
        | currency:'EUR'
    }}
  &lt;/p&gt;
&#8203;
  &lt;div class="pill-group"&gt;
    &lt;p class="pill"&gt;
      {{ product()!.category }}
    &lt;/p&gt;
  &lt;/div&gt;
&#8203;
  &lt;button (click)="addToCart()"&gt;
    Add to cart
  &lt;/button&gt;
}</code></pre>
</div>



---

# 9. Obtener un único producto por ID

En `ProductsService`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProduct(
  id: number
): Observable&lt;Product&gt; {
&#8203;
  return this.http.get&lt;Product&gt;(
    `${this.productsUrl}/${id}`
  );
}</code></pre>
</div>



En `ProductDetailComponent` creamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">id = input&lt;number&gt;();
&#8203;
product$:
  Observable&lt;Product&gt; | undefined;</code></pre>
</div>



Inyectamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService:
    ProductsService
) {}</code></pre>
</div>



Y reaccionamos al cambio del input:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnChanges(): void {
  this.product$ =
    this.productService
      .getProduct(this.id()!);
}</code></pre>
</div>



La plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let product =
  (product$ | async);
&#8203;
@if (product) {
  &lt;p&gt;
    You selected:
    &lt;strong&gt;
      {{ product.title }}
    &lt;/strong&gt;
  &lt;/p&gt;
&#8203;
  &lt;p&gt;
    {{
      product.price
        | currency:'EUR'
    }}
  &lt;/p&gt;
&#8203;
  &lt;div class="pill-group"&gt;
    &lt;p class="pill"&gt;
      {{ product.category }}
    &lt;/p&gt;
  &lt;/div&gt;
&#8203;
  &lt;button (click)="addToCart()"&gt;
    Add to cart
  &lt;/button&gt;
}</code></pre>
</div>



Y el padre pasa solo el ID:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [id]="selectedProduct?.id"
  (added)="onAdded()"
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>



---

# 10. Caché local para productos

Como Fake Store API no persiste físicamente los cambios, el material mantiene un array local:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private products:
  Product[] = [];</code></pre>
</div>



Al obtener datos, guardamos el resultado mediante `map`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts():
  Observable&lt;Product[]&gt; {
&#8203;
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
        this.products = products;
        return products;
      })
    );
}</code></pre>
</div>



Para obtener el detalle se usa el caché:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProduct(
  id: number
): Observable&lt;Product&gt; {
&#8203;
  const product =
    this.products.find(
      p =&gt; p.id === id
    );
&#8203;
  return of(product!);
}</code></pre>
</div>



---

# 11. Crear productos con POST

Método:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addProduct(
  newProduct: Partial&lt;Product&gt;
): Observable&lt;Product&gt; {
&#8203;
  return this.http
    .post&lt;Product&gt;(
      this.productsUrl,
      newProduct
    )
    .pipe(
      map(product =&gt; {
        this.products.push(product);
        return product;
      })
    );
}</code></pre>
</div>



`Partial<Product>` resulta útil porque un producto nuevo puede no tener todavía ID.

Generamos componente:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component product-create</code></pre>
</div>



En el componente:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productsService:
    ProductsService
) {}
&#8203;
createProduct(
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
    .subscribe();
}</code></pre>
</div>



> 💡 El material señala que no es necesario cancelar manualmente las suscripciones a peticiones de `HttpClient`, porque estos observables completan tras la respuesta.

---

# 12. Plantilla de creación de producto



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h1&gt;Add new product&lt;/h1&gt;
&#8203;
&lt;div&gt;
  &lt;label for="title"&gt;
    Title
  &lt;/label&gt;
  &lt;input
    id="title"
    #title
  /&gt;
&lt;/div&gt;
&#8203;
&lt;div&gt;
  &lt;label for="price"&gt;
    Price
  &lt;/label&gt;
  &lt;input
    id="price"
    #price
    type="number"
  /&gt;
&lt;/div&gt;
&#8203;
&lt;div&gt;
  &lt;label for="category"&gt;
    Category
  &lt;/label&gt;
&#8203;
  &lt;select
    id="category"
    #category
  &gt;
    &lt;option&gt;
      Select a category
    &lt;/option&gt;
    &lt;option value="electronics"&gt;
      Electronics
    &lt;/option&gt;
    &lt;option value="jewelery"&gt;
      Jewelery
    &lt;/option&gt;
    &lt;option&gt;
      Other
    &lt;/option&gt;
  &lt;/select&gt;
&lt;/div&gt;
&#8203;
&lt;div&gt;
  &lt;button
    (click)="createProduct(
      title.value,
      price.value,
      category.value
    )"
  &gt;
    Create
  &lt;/button&gt;
&lt;/div&gt;</code></pre>
</div>



Estilos globales:


<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">input {
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 16px;
  border: 1px solid #BDBDBD;
}</code></pre>
</div>



Estilos del componente:


<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">input {
  width: 200px;
}
&#8203;
select {
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 16px;
  border: 1px solid #BDBDBD;
  width: 220px;
}
&#8203;
label {
  margin-bottom: 4px;
  display: block;
}</code></pre>
</div>



Finalmente:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-create&gt;
&lt;/app-product-create&gt;</code></pre>
</div>



---

# 13. Actualizar un producto con PATCH

Método:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">updateProduct(
  id: number,
  price: number
): Observable&lt;Product&gt; {
&#8203;
  return this.http
    .patch&lt;Product&gt;(
      `${this.productsUrl}/${id}`,
      {
        price
      }
    )
    .pipe(
      map(product =&gt; {
        const index =
          this.products.findIndex(
            p =&gt; p.id === id
          );
&#8203;
        this.products[index].price =
          price;
&#8203;
        return product;
      })
    );
}</code></pre>
</div>



El componente:


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
    .subscribe();
}</code></pre>
</div>



Plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  placeholder="New price"
  #price
  type="number"
/&gt;
&#8203;
&lt;button
  class="secondary"
  (click)="changePrice(
    product,
    price.value
  )"
&gt;
  Change
&lt;/button&gt;</code></pre>
</div>



> 📌 El material distingue `PATCH` y `PUT`: `PATCH` se usa cuando modificamos solo una parte del objeto; `PUT` puede representar la actualización completa.

---

# 14. Eliminar un producto con DELETE

Importamos `tap` y creamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">deleteProduct(
  id: number
): Observable&lt;void&gt; {
&#8203;
  return this.http
    .delete&lt;void&gt;(
      `${this.productsUrl}/${id}`
    )
    .pipe(
      tap(() =&gt; {
        const index =
          this.products.findIndex(
            p =&gt; p.id === id
          );
&#8203;
        this.products.splice(
          index,
          1
        );
      })
    );
}</code></pre>
</div>



Utilizamos `tap` porque queremos ejecutar el efecto sobre el caché sin transformar el valor emitido.

En el detalle:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">deleted = output();
&#8203;
remove(product: Product) {
  this.productService
    .deleteProduct(product.id)
    .subscribe(() =&gt; {
      this.deleted.emit();
    });
}</code></pre>
</div>



Plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div class="button-group"&gt;
  &lt;button (click)="addToCart()"&gt;
    Add to cart
  &lt;/button&gt;
&#8203;
  &lt;button
    class="delete"
    (click)="remove(product)"
  &gt;
    Delete
  &lt;/button&gt;
&lt;/div&gt;</code></pre>
</div>



CSS:


<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">button.delete {
  display: inline;
  margin-left: 5px;
  --button-accent:
    var(--hot-red);
}
&#8203;
.button-group {
  display: flex;
  flex-direction: row;
  align-items: start;
  flex-wrap: wrap;
}</code></pre>
</div>



El padre limpia la selección:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-product-detail
  [id]="selectedProduct?.id"
  (added)="onAdded()"
  (deleted)="
    selectedProduct = undefined
  "
&gt;&lt;/app-product-detail&gt;</code></pre>
</div>



---

# 15. Resumen CRUD

| Operación | HttpClient | Ejemplo |
|---|---|---|
| Create | `post()` | Añadir producto |
| Read | `get()` | Obtener lista o detalle |
| Update | `patch()` / `put()` | Cambiar precio |
| Delete | `delete()` | Eliminar producto |

---

# 16. Autenticación mediante HTTP

Fake Store API proporciona un endpoint de autenticación.

El patrón utilizado es:

```text
AuthComponent
      │
      ▼
AuthService
      │
      ▼
POST /auth/login
      │
      ▼
token
```

Generamos:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate service auth</code></pre>
</div>



Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Injectable,
  computed,
  inject,
  signal
} from '@angular/core';
&#8203;
import {
  HttpClient
} from '@angular/common/http';
&#8203;
import {
  Observable,
  tap
} from 'rxjs';
&#8203;
import {
  APP_SETTINGS
} from './app.settings';</code></pre>
</div>



---

# 17. Estado de autenticación con Signals

En `AuthService`:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private accessToken =
  signal('');
&#8203;
private authUrl =
  inject(APP_SETTINGS).apiUrl
    + '/auth';
&#8203;
isLoggedIn =
  computed(
    () =&gt;
      this.accessToken() !== ''
  );</code></pre>
</div>



`accessToken` es un writable signal.

`isLoggedIn` es un computed signal que depende de `accessToken`.

---

# 18. Login y logout

Inyectamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private http: HttpClient
) {}</code></pre>
</div>



Login:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">login(
  username: string,
  password: string
): Observable&lt;string&gt; {
&#8203;
  return this.http
    .post&lt;string&gt;(
      this.authUrl + '/login',
      {
        username,
        password
      }
    )
    .pipe(
      tap(token =&gt;
        this.accessToken.set(token)
      )
    );
}</code></pre>
</div>



Logout:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">logout() {
  this.accessToken.set('');
}</code></pre>
</div>



> ⭐ Signals también pueden utilizarse dentro de servicios, no solo en componentes.

---

# 19. Componente de autenticación

Generamos:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component auth</code></pre>
</div>



Inyectamos de forma pública:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  public authService:
    AuthService
) {}</code></pre>
</div>



Métodos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">login() {
  this.authService
    .login(
      'david_r',
      '3478*#54'
    )
    .subscribe();
}
&#8203;
logout() {
  this.authService.logout();
}</code></pre>
</div>



Plantilla:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (
  !authService.isLoggedIn()
) {
  &lt;button (click)="login()"&gt;
    Login
  &lt;/button&gt;
} @else {
  &lt;button (click)="logout()"&gt;
    Logout
  &lt;/button&gt;
}</code></pre>
</div>



---

# 20. Autorización de funcionalidades

Podemos mostrar `Add to cart` solo si el usuario está autenticado:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (
  authService.isLoggedIn()
) {
  &lt;button (click)="addToCart()"&gt;
    Add to cart
  &lt;/button&gt;
}</code></pre>
</div>



Añadimos el componente de autenticación al encabezado:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;header&gt;
  {{ settings.title }}
&#8203;
  &lt;app-auth&gt;
  &lt;/app-auth&gt;
&lt;/header&gt;</code></pre>
</div>



El comportamiento esperado es:

1. Usuario invitado → botón `Login`.
2. `Add to cart` oculto.
3. Login correcto → botón cambia a `Logout`.
4. `Add to cart` aparece.

---

# 21. Cabeceras HTTP

Podemos enviar cabeceras mediante `HttpHeaders`.

Ejemplo conceptual:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts():
  Observable&lt;Product[]&gt; {
&#8203;
  const options = {
    params:
      new HttpParams()
        .set('limit', 10),
&#8203;
    headers:
      new HttpHeaders({
        Authorization:
          'myToken'
      })
  };
&#8203;
  return this.http
    .get&lt;Product[]&gt;(
      this.productsUrl,
      options
    )
    .pipe(
      map(products =&gt; {
        this.products =
          products;
&#8203;
        return products;
      })
    );
}</code></pre>
</div>



El problema aparece si debemos repetir esta cabecera en todas las peticiones. Para resolverlo utilizamos **interceptores**.

---

# 22. Interceptores HTTP

Un interceptor puede procesar peticiones y respuestas que pasan por `HttpClient`.

Casos de uso mencionados:

- añadir tokens de autenticación;
- mostrar indicadores de carga;
- registrar comunicaciones HTTP.

Generamos:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate interceptor auth</code></pre>
</div>



Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  provideHttpClient,
  withInterceptors
} from '@angular/common/http';
&#8203;
import {
  authInterceptor
} from './auth.interceptor';</code></pre>
</div>



Registramos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const appConfig:
  ApplicationConfig = {
    providers: [
      provideZoneChangeDetection({
        eventCoalescing: true
      }),
      provideRouter(routes),
&#8203;
      provideHttpClient(
        withInterceptors([
          authInterceptor
        ])
      ),
&#8203;
      {
        provide: APP_SETTINGS,
        useValue: appSettings
      }
    ]
  };</code></pre>
</div>



---

# 23. Orden de interceptores

La figura del PDF muestra que el orden de registro importa.

Podemos representarlo así:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">PETICIÓN
   │
   ▼
Authentication
   │
   ▼
Loading
   │
   ▼
Logger
   │
   ▼
HttpBackend
   │
   ▼
SERVIDOR
&#8203;
RESPUESTA
   ▲
   │
Authentication
   ▲
   │
Loading
   ▲
   │
Logger
   ▲
   │
HttpBackend</code></pre>
</div>



Las peticiones avanzan en el orden de los interceptores; las respuestas recorren la cadena en sentido inverso.

El último elemento antes de enviar la petición al servidor es el backend HTTP interno de Angular.

---

# 24. Interceptor funcional de autenticación

Ejemplo:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const authInterceptor:
  HttpInterceptorFn =
    (req, next) =&gt; {
&#8203;
      const authReq =
        req.clone({
          setHeaders: {
            Authorization:
              'myToken'
          }
        });
&#8203;
      return next(authReq);
    };</code></pre>
</div>



Puntos importantes:

- `req` representa la petición actual;
- las peticiones HTTP son inmutables;
- usamos `clone()` para crear una versión modificada;
- `setHeaders` actualiza las cabeceras;
- `next(authReq)` pasa la petición al siguiente interceptor.

---

# 25. Inyección dentro de un interceptor

Los interceptores funcionales también pueden utilizar DI:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  inject
} from '@angular/core';
&#8203;
import {
  HttpInterceptorFn
} from '@angular/common/http';
&#8203;
import {
  AuthService
} from './auth.service';
&#8203;
export const authInterceptor:
  HttpInterceptorFn =
    (req, next) =&gt; {
&#8203;
      const authService =
        inject(AuthService);
&#8203;
      const authReq =
        req.clone({
          setHeaders: {
            Authorization:
              'myToken'
          }
        });
&#8203;
      return next(authReq);
    };</code></pre>
</div>



El ejemplo conserva un token fijo por simplicidad, pero muestra cómo un interceptor puede obtener dependencias con `inject()`.

---

# 26. Interceptores en versiones antiguas

El material señala que en versiones anteriores de Angular los interceptores solían ser clases.

El registro se realizaba mediante un provider como:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  provide:
    HTTP_INTERCEPTORS,
  useClass:
    AuthInterceptor,
  multi: true
}</code></pre>
</div>



`multi: true` permite registrar múltiples valores para el mismo token.

---

# 27. Esquema general de comunicación HTTP



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Componente
    │
    ▼
Servicio Angular
    │
    ▼
HttpClient
    │
    ▼
Interceptors
    │
    ▼
Backend API
    │
    ▼
Observable
    │
    ▼
RxJS / async / subscriber
    │
    ▼
Componente / plantilla</code></pre>
</div>



---

---

# 28. Resumen de la unidad

En esta unidad hemos aprendido a comunicar una aplicación Angular con un backend mediante `HttpClient`.

Hemos trabajado con:

- `provideHttpClient()`;
- `HttpClient`;
- `HttpParams`;
- `HttpHeaders`;
- `GET`;
- `POST`;
- `PATCH`;
- `DELETE`;
- operaciones CRUD;
- observables HTTP;
- autenticación mediante `AuthService`;
- autorización de funcionalidades;
- cabeceras de autenticación;
- interceptores funcionales;
- `withInterceptors()`;
- `HttpInterceptorFn`;
- modificación inmutable de peticiones mediante `clone()`.

---

# 29. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| `HttpClient` | Cliente HTTP de Angular |
| `provideHttpClient()` | Registra los servicios HTTP |
| `HttpParams` | Parámetros de query |
| `HttpHeaders` | Cabeceras HTTP |
| `GET` | Lectura de datos |
| `POST` | Creación |
| `PATCH` | Actualización parcial |
| `PUT` | Actualización completa |
| `DELETE` | Eliminación |
| CRUD | Create, Read, Update, Delete |
| `Observable<T>` | Flujo asíncrono devuelto por HttpClient |
| `Partial<T>` | Tipo con propiedades opcionales |
| Interceptor | Procesa peticiones y respuestas HTTP |
| `withInterceptors()` | Registra interceptores funcionales |
| `HttpInterceptorFn` | Tipo para interceptor funcional |
| `clone()` | Crea una copia modificada de una petición |
| `AuthService` | Encapsula autenticación |
| Authorization | Control de acceso a funcionalidades |
| `InjectionToken` | Token DI para configuración |
| `tap()` | Ejecuta un efecto sin transformar el valor |
| `map()` | Transforma valores emitidos |

---

## 🔗 Recursos citados en el material

- Fetch API: <https://developer.mozilla.org/docs/Web/API/fetch>
- Fake Store API: <https://fakestoreapi.com>
- Angular HTTP: <https://angular.dev/guide/http>

---

> 🎓 **Siguiente paso**  
> Tras dominar el acceso a datos mediante HTTP, el siguiente paso natural es utilizar **Angular Router** para estructurar la navegación entre distintas vistas de la aplicación.

---

# 30. Taller práctico: comunicación HTTP y CRUD 🧪

Los ejercicios de esta sección toman como referencia el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/08_http>

El repositorio ya contiene una aplicación bastante completa para practicar HTTP:

- `provideHttpClient(withInterceptors(...))` está configurado en `app.config.ts`;
- `ProductsService` implementa `GET`, `POST`, `PATCH` y `DELETE`;
- `ProductListComponent` consume un `Observable<Product[]>` con `AsyncPipe`;
- `ProductCreateComponent` crea productos;
- `ProductDetailComponent` permite modificar y eliminar;
- `AuthService` mantiene el estado de autenticación mediante Signals;
- `authInterceptor` clona peticiones y añade una cabecera `Authorization`.

> 🎯 **Forma recomendada de trabajo**  
> Mantén abiertas las herramientas de desarrollo del navegador, especialmente la pestaña **Network**. En esta unidad es importante observar no solo la interfaz, sino también la petición HTTP real: método, URL, query params, body, cabeceras y respuesta.

---

## Ejercicio 1. Sigue el recorrido de una petición GET

### Objetivo

Comprender todas las capas implicadas desde la plantilla hasta el backend.

Completa el recorrido:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ProductListComponent
        │
        ▼
ProductsService
        │
        ▼
HttpClient
        │
        ▼
authInterceptor
        │
        ▼
Fake Store API
        │
        ▼
Observable&lt;Product[]&gt;
        │
        ▼
AsyncPipe
        │
        ▼
Plantilla</code></pre>
</div>


### Tareas

1. Arranca la aplicación.
2. Abre **Network**.
3. Localiza la petición a `/products`.
4. Anota:
   - método HTTP;
   - URL;
   - query string;
   - código de respuesta;
   - cabecera `Authorization`.

---

## Ejercicio 2. Comprueba `provideHttpClient()`

### Objetivo

Entender por qué `HttpClient` puede inyectarse en los servicios.

En `app.config.ts` localiza la configuración HTTP.

### Experimento

Elimina temporalmente el registro de `provideHttpClient(...)` y ejecuta la aplicación.

### Pregunta

¿Qué ocurre cuando `ProductsService` intenta recibir `HttpClient`?

Restaura después la configuración.

---

## Ejercicio 3. Modifica el parámetro `limit`

### Objetivo

Practicar `HttpParams`.

En `getProducts()` el proyecto construye un parámetro `limit`.

Cámbialo a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const params =
  new HttpParams()
    .set('limit', 3);</code></pre>
</div>


### Tareas

1. Recarga.
2. Comprueba cuántos productos llegan.
3. Verifica en **Network** la URL generada.
4. Prueba `limit = 1`, `5` y `10`.

---

## Ejercicio 4. Demuestra que `HttpParams` es inmutable

### Objetivo

Evitar uno de los errores más habituales.

Prueba temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let params =
  new HttpParams();
&#8203;
params.set(
  'limit',
  3
);
&#8203;
console.log(
  params.toString()
);</code></pre>
</div>


Después corrígelo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const params =
  new HttpParams()
    .set('limit', 3);</code></pre>
</div>


### Explica

¿Por qué el primer código no conserva el parámetro?

---

## Ejercicio 5. Añade un segundo query parameter

### Objetivo

Construir opciones HTTP con más de un parámetro.

Añade de forma experimental:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const params =
  new HttpParams()
    .set('limit', 5)
    .set('sort', 'desc');</code></pre>
</div>


### Tareas

1. Observa la URL final.
2. Comprueba la respuesta real del backend.
3. Distingue entre lo que Angular envía y lo que la API decide soportar.

---

## Ejercicio 6. Analiza el caché local de `ProductsService`

### Objetivo

Comprender por qué `getProduct(id)` no realiza actualmente otra petición HTTP.

El servicio guarda:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private products:
  Product[] = [];</code></pre>
</div>


y `getProducts()` rellena ese array tras la petición.

### Tareas

1. Carga el listado.
2. Selecciona varios productos.
3. Observa **Network**.
4. Comprueba si cada selección genera una nueva petición a `/products/:id`.

### Pregunta

¿Qué ventaja y qué riesgo puede tener este pequeño caché?

---

## Ejercicio 7. Cambia temporalmente el detalle para hacer un GET real

### Objetivo

Comparar caché local y petición remota.

Implementa temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProduct(
  id: number
): Observable&lt;Product&gt; {
  return this.http.get&lt;Product&gt;(
    `${this.productsUrl}/${id}`
  );
}</code></pre>
</div>


### Tareas

1. Selecciona varios productos.
2. Observa **Network**.
3. Compara con el comportamiento anterior.
4. Decide qué estrategia realiza más peticiones.

---

## Ejercicio 8. Analiza `ProductCreateComponent`

### Objetivo

Seguir una operación `POST` completa.

El componente obtiene valores desde la plantilla y llama a `addProduct()`.

### Tareas

1. Crea un producto.
2. Localiza la petición `POST`.
3. Inspecciona:
   - URL;
   - body JSON;
   - respuesta.
4. Comprueba qué ocurre con el array local del servicio.

### Pregunta

¿Por qué el método acepta `Partial<Product>` en vez de exigir necesariamente un `Product` completo?

---

## Ejercicio 9. Evita productos con título vacío

### Objetivo

Añadir una validación sencilla antes de ejecutar HTTP.

Modifica:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">createProduct(
  title: string,
  price: string,
  category: string
): void {
  if (
    title.trim() === ''
  ) {
    return;
  }
&#8203;
  this.productsService
    .addProduct({
      title: title.trim(),
      price: Number(price),
      category
    })
    .subscribe();
}</code></pre>
</div>


### Reto

Añade también una comprobación para evitar precios menores o iguales que cero.

---

## Ejercicio 10. Observa una petición PATCH

### Objetivo

Relacionar una modificación parcial con el método HTTP apropiado.

Selecciona un producto y cambia su precio.

### Tareas

1. Localiza la petición `PATCH`.
2. Comprueba el body.
3. Verifica que solo se envía la propiedad modificada.
4. Observa cómo se actualiza el caché local.

### Pregunta

¿Por qué `PATCH` encaja mejor que `PUT` en este ejercicio?

---

## Ejercicio 11. Protege la actualización del caché

### Objetivo

Evitar acceder a una posición inexistente del array.

Mejora el método:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const index =
  this.products.findIndex(
    product =&gt;
      product.id === id
  );
&#8203;
if (index &gt;= 0) {
  this.products[index].price =
    price;
}</code></pre>
</div>


### Pregunta

¿Qué podría ocurrir si `findIndex()` devolviera `-1` y usáramos directamente esa posición?

---

## Ejercicio 12. Observa una petición DELETE

### Objetivo

Seguir la eliminación de un producto.

### Tareas

1. Selecciona un producto.
2. Pulsa `Delete`.
3. Localiza la petición en **Network**.
4. Comprueba el método y la URL.
5. Explica qué hace el `tap()` del servicio.
6. Explica por qué el padre recibe el evento `deleted`.

---

## Ejercicio 13. Actualización inmutable del caché al borrar

### Objetivo

Comparar mutación con sustitución del array.

El repositorio utiliza una eliminación mediante `splice`.

Crea como alternativa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.products =
  this.products.filter(
    product =&gt;
      product.id !== id
  );</code></pre>
</div>


### Preguntas

1. ¿Qué versión modifica el array existente?
2. ¿Cuál crea una nueva referencia?
3. ¿Cuál encaja mejor con una estrategia de estado inmutable?

---

## Ejercicio 14. Diferencia `map()` y `tap()` en operaciones HTTP

### Objetivo

Relacionar operadores RxJS con su intención.

En el servicio aparecen ambos operadores.

Completa:

| Operador | ¿Transforma el valor? | Uso en el proyecto |
|---|:---:|---|
| `map()` |  | Guardar/retornar productos |
| `tap()` |  | Actualizar estado tras borrar |

### Reflexión

Si únicamente quieres ejecutar un efecto lateral sin cambiar la respuesta, ¿cuál es más expresivo?

---

## Ejercicio 15. Inspecciona el estado de autenticación

### Objetivo

Relacionar HTTP y Signals.

`AuthService` utiliza:

- un writable signal para el token;
- un computed signal para `isLoggedIn`.

### Tareas

1. Abre la aplicación sin hacer login.
2. Comprueba el valor visual de autenticación.
3. Pulsa `Login`.
4. Observa la petición HTTP.
5. Comprueba cómo cambia la interfaz.
6. Pulsa `Logout`.

### Pregunta

¿Por qué `isLoggedIn` encaja bien como computed signal?

---

## Ejercicio 16. Autorización visual de `Add to cart`

### Objetivo

Comprender que autenticación y autorización no son exactamente lo mismo.

En el detalle, el botón se muestra según:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (
  authService.isLoggedIn()
) {
  &lt;button (click)="addToCart()"&gt;
    Add to cart
  &lt;/button&gt;
}</code></pre>
</div>


### Tareas

1. Verifica que está oculto antes del login.
2. Inicia sesión.
3. Comprueba que aparece.
4. Cierra sesión.
5. Comprueba que vuelve a desaparecer.

### Reflexión

¿Ocultar un botón en la interfaz sería suficiente para proteger una operación sensible en un sistema real? Razona la respuesta.

---

## Ejercicio 17. Analiza `authInterceptor`

### Objetivo

Comprender por qué se clona la petición.

El interceptor crea una nueva petición y añade una cabecera.

### Tareas

1. Realiza una petición a productos.
2. Inspecciona sus Request Headers.
3. Localiza `Authorization`.
4. Cambia temporalmente el valor de la cabecera.
5. Comprueba el nuevo valor enviado.

### Pregunta

¿Por qué no se modifica directamente el objeto `req`?

---

## Ejercicio 18. Excluye el login del token fijo

### Objetivo

Añadir una condición a un interceptor funcional.

Como práctica, evita añadir la cabecera a la URL de login:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const authInterceptor:
  HttpInterceptorFn =
    (req, next) =&gt; {
&#8203;
      if (
        req.url.includes(
          '/auth/login'
        )
      ) {
        return next(req);
      }
&#8203;
      const authReq =
        req.clone({
          setHeaders: {
            Authorization:
              'myToken'
          }
        });
&#8203;
      return next(authReq);
    };</code></pre>
</div>


### Tareas

1. Haz login.
2. Consulta productos.
3. Compara las cabeceras de ambas peticiones.

---

## Ejercicio 19. Crea un interceptor de logging

### Objetivo

Practicar una cadena de interceptores.

Genera:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate interceptor logging</code></pre>
</div>


Implementa una versión sencilla:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const loggingInterceptor:
  HttpInterceptorFn =
    (req, next) =&gt; {
&#8203;
      console.log(
        req.method,
        req.url
      );
&#8203;
      return next(req);
    };</code></pre>
</div>


Registra ambos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">provideHttpClient(
  withInterceptors([
    authInterceptor,
    loggingInterceptor
  ])
)</code></pre>
</div>


### Reto

Invierte el orden y observa qué diferencias puedes detectar en los mensajes o en las peticiones procesadas.

---

## Ejercicio 20. Usa `APP_SETTINGS` para cambiar de backend

### Objetivo

Comprender por qué la URL de la API se obtiene mediante DI.

Crea una configuración alternativa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const localSettings:
  AppSettings = {
    title: 'My e-shop local',
    version: '1.0-local',
    apiUrl:
      'http://localhost:3000'
  };</code></pre>
</div>


No es necesario que tengas ese backend activo.

### Preguntas

1. ¿Qué archivos de los servicios tendrías que modificar si la URL estuviera escrita directamente en todos ellos?
2. ¿Qué ventaja proporciona centralizarla en `APP_SETTINGS`?

---

## Ejercicio 21. Añade estado de carga al listado

### Objetivo

Mejorar la experiencia del usuario durante una petición.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">loading = true;</code></pre>
</div>


y modifica la obtención:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts(): void {
  this.loading = true;
&#8203;
  this.products$ =
    this.productService
      .getProducts()
      .pipe(
        tap(() =&gt; {
          this.loading = false;
        })
      );
}</code></pre>
</div>


En la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (loading) {
  &lt;p&gt;
    Cargando productos...
  &lt;/p&gt;
}</code></pre>
</div>


### Reflexión

¿Qué ocurriría con `loading` si la petición fallase antes de emitir correctamente? Anótalo como mejora pendiente para una unidad posterior sobre gestión de errores.

---

# 31. Reto integrador: cliente CRUD con autenticación 🚀

### Objetivo

Integrar todos los conceptos de la unidad sobre el proyecto del repositorio.

La aplicación deberá permitir:

1. cargar productos mediante `GET`;
2. limitar la consulta con `HttpParams`;
3. seleccionar un producto;
4. crear un producto con `POST`;
5. modificar su precio con `PATCH`;
6. eliminarlo con `DELETE`;
7. autenticar al usuario;
8. mostrar `Add to cart` solo cuando esté autenticado;
9. añadir la cabecera mediante interceptor;
10. registrar las peticiones con un segundo interceptor.

### Requisitos de arquitectura

- [ ] `HttpClient` debe utilizarse desde servicios, no directamente desde las plantillas.
- [ ] `provideHttpClient()` debe estar configurado en `app.config.ts`.
- [ ] La URL base debe proceder de `APP_SETTINGS`.
- [ ] Los métodos del servicio deben estar tipados con `Observable<T>`.
- [ ] Debe utilizarse `HttpParams`.
- [ ] Debe existir al menos un `POST`.
- [ ] Debe existir al menos un `PATCH`.
- [ ] Debe existir al menos un `DELETE`.
- [ ] La autenticación debe encapsularse en `AuthService`.
- [ ] El estado de login debe derivarse mediante Signal/computed.
- [ ] Debe existir al menos un interceptor funcional.
- [ ] Las peticiones deben comprobarse en **Network**.

---

# 32. Diagnóstico de errores HTTP 🔎

## Caso A. `HttpClient` sin proveedor

Un servicio inyecta `HttpClient`, pero `app.config.ts` no registra `provideHttpClient()`.

**Pregunta:** ¿qué problema esperas durante la resolución de dependencias?

---

## Caso B. `HttpParams` sin reasignar

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const params =
  new HttpParams();
&#8203;
params.set(
  'limit',
  5
);</code></pre>
</div>


**Pregunta:** ¿por qué el parámetro puede no enviarse?

---

## Caso C. Tipo de respuesta incorrecto

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.http.get&lt;Product&gt;(
  this.productsUrl
);</code></pre>
</div>


pero el endpoint devuelve una colección.

**Pregunta:** ¿qué tipo debería declararse?

---

## Caso D. `PATCH` con ID equivocado

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">`${this.productsUrl}/undefined`</code></pre>
</div>


**Pregunta:** ¿qué dato del componente debe revisarse antes de ejecutar la petición?

---

## Caso E. Mutar una petición en un interceptor

Se intenta modificar directamente `req.headers`.

**Pregunta:** ¿qué técnica utiliza Angular para modificar una petición inmutable?

---

## Caso F. Login correcto pero UI sin actualizar

`accessToken` se guarda en una propiedad normal y `isLoggedIn` no es reactivo.

**Pregunta:** ¿qué combinación utilizada por el repositorio evita ese problema?

---

# 33. Cuestiones finales de reflexión 📝

1. ¿Qué registra `provideHttpClient()`?
2. ¿Por qué los métodos de `HttpClient` encajan bien con RxJS?
3. ¿Qué diferencia existe entre query params y body?
4. ¿Qué característica importante tiene `HttpParams`?
5. ¿Qué método HTTP se utiliza para leer?
6. ¿Qué método se utiliza habitualmente para crear?
7. ¿Qué diferencia conceptual existe entre `PATCH` y `PUT`?
8. ¿Qué método se utiliza para borrar?
9. ¿Por qué `Partial<Product>` resulta útil al crear?
10. ¿Qué objetivo tiene el caché local de `ProductsService`?
11. ¿Qué diferencia hay entre `map()` y `tap()` en el servicio?
12. ¿Qué responsabilidad tiene `ProductCreateComponent`?
13. ¿Por qué el detalle recibe un ID en lugar del objeto completo?
14. ¿Qué representa `isLoggedIn`?
15. ¿Qué diferencia existe entre autenticación y autorización?
16. ¿Para qué sirve un interceptor HTTP?
17. ¿Por qué las peticiones se clonan dentro de un interceptor?
18. ¿Por qué importa el orden de los interceptores?
19. ¿Qué ventaja aporta `APP_SETTINGS` para configurar la URL de la API?
20. ¿Qué información comprobarías en la pestaña **Network** cuando una petición no funciona?

---

> ✅ **Meta de aprendizaje**  
> Si puedes seguir una operación CRUD desde el componente hasta la petición de red, explicar qué papel desempeñan servicio, Observable e interceptor y verificar la petición en DevTools, ya dominas los fundamentos de comunicación HTTP en Angular.
