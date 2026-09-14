# 📝 Unidad 10: Recogida de datos de usuario con formularios en Angular


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
> **Objetivo:** aprender a recoger, estructurar y gestionar datos introducidos por el usuario mediante formularios Angular, utilizando tanto el enfoque **template-driven** como el enfoque **reactive forms**.



---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Explicar qué papel tienen los formularios en una aplicación web.
- Diferenciar entre formularios template-driven y formularios reactivos.
- Crear two-way binding mediante `[(ngModel)]`.
- Utilizar `FormsModule`.
- Gestionar el envío con `(ngSubmit)`.
- Crear formularios reactivos con `ReactiveFormsModule`.
- Utilizar `FormControl`, `FormGroup` y `FormArray`.
- Asociar controles del modelo con elementos HTML mediante `formControlName`.
- Crear jerarquías de formularios anidados.
- Construir formularios dinámicos.
- Utilizar `FormBuilder`.
- Comprender la diferencia entre `value` y `getRawValue()`.
- Identificar los estados automáticos de validación de Angular:
  `ng-valid`, `ng-invalid`, `ng-touched`, `ng-untouched`, `ng-dirty` y `ng-pristine`.

---

## 📑 Contenidos

1. [Introducción a los formularios web](#1-introducción-a-los-formularios-web)
2. [Formularios template-driven](#4-formularios-template-driven)
3. [Formularios reactivos](#11-formularios-reactivos)
4. [FormGroup y FormControl](#13-crear-un-formgroup)
5. [Formularios anidados y FormArray](#21-formularios-anidados)
6. [FormBuilder](#35-formbuilder)
7. [Validación](#41-introducción-a-la-validación)
8. [Taller práctico](#48-taller-práctico-formularios-angular-sobre-el-proyecto-real)

---

# 1. Introducción a los formularios web

Las aplicaciones web utilizan formularios para recoger información introducida por el usuario.

Ejemplos habituales:

- inicio de sesión;
- información de pago;
- reservas;
- búsquedas;
- alta o modificación de productos;
- datos de envío.

Los datos introducidos pueden:

- almacenarse localmente;
- enviarse a un backend;
- validarse antes de ser procesados.

Un formulario suele:

- definir distintos tipos de campos;
- aplicar reglas de validación;
- mostrar mensajes de error;
- decidir qué hacer cuando el formulario no es válido.

---

# 2. Dos enfoques de formularios en Angular

Angular proporciona dos estrategias principales:

| Enfoque | Características |
|---|---|
| **Template-driven forms** | Configuración principalmente en la plantilla |
| **Reactive forms** | Configuración programática en la clase TypeScript |

Ninguno es universalmente mejor.

La elección depende del escenario.

### Template-driven forms

Son adecuados para:

- formularios pequeños;
- configuraciones simples;
- casos donde queremos rapidez de desarrollo.

Características:

- se definen principalmente en la plantilla;
- utilizan directivas como `ngModel`;
- dependen más del mecanismo de change detection;
- resultan menos cómodos de probar unitariamente.

### Reactive forms

Son adecuados para:

- formularios complejos;
- aplicaciones grandes;
- escenarios con mucha validación;
- formularios dinámicos;
- aplicaciones con programación reactiva.

Características:

- el modelo del formulario vive en TypeScript;
- el estado se modifica explícitamente;
- son más sencillos de probar;
- mantienen un modelo intermedio de datos;
- encajan bien con Observables.

---

# 3. Estructura básica de un formulario HTML

Ejemplo de login:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form&gt;
  &lt;div&gt;
    &lt;input
      type="text"
      name="username"
      placeholder="Username"
    /&gt;
  &lt;/div&gt;
&#8203;
  &lt;div&gt;
    &lt;input
      type="password"
      name="password"
      placeholder="Password"
    /&gt;
  &lt;/div&gt;
&#8203;
  &lt;button type="submit"&gt;
    Login
  &lt;/button&gt;
&lt;/form&gt;</code></pre>
</div>



Aspectos importantes:

- un control debe estar dentro de `<form>` para formar parte del formulario;
- `type="password"` oculta visualmente el contenido;
- `type="submit"` permite enviar el formulario mediante botón o pulsando `Enter`.

---

# 4. Formularios template-driven

Los formularios template-driven permiten trabajar con two-way binding.

Recordemos:

```text
Component → Template
Template  → Component
```

Combinando ambos sentidos obtenemos:

```text
Component ⇄ Template
```

Angular proporciona la directiva `ngModel`.

---

# 5. Activar template-driven forms

En `ProductDetailComponent` importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  FormsModule
} from '@angular/forms';</code></pre>
</div>



Y lo añadimos a `imports`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Component({
  selector: 'app-product-detail',
  imports: [
    CommonModule,
    FormsModule
  ],
  templateUrl:
    './product-detail.component.html',
  styleUrl:
    './product-detail.component.css'
})</code></pre>
</div>



---

# 6. Two-way binding con `[(ngModel)]`

Podemos enlazar el precio del producto:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  placeholder="New price"
  type="number"
  name="price"
  [(ngModel)]="product.price"
/&gt;</code></pre>
</div>



La sintaxis:

`[(ngModel)]`

se conoce popularmente como:

**banana in a box**.

Se interpreta visualmente como:

```text
(ngModel)  → banana
[      ]   → box
[(ngModel)]
```

> 📌 El atributo `name` es necesario para que Angular pueda distinguir internamente el control dentro del formulario.

---

# 7. Envío del formulario

El botón:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button
  class="secondary"
  type="submit"
&gt;
  Change
&lt;/button&gt;</code></pre>
</div>



Ya no necesita un `(click)` específico.

El formulario captura el envío:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form
  (ngSubmit)="changePrice(product)"
&gt;
  &lt;input
    placeholder="New price"
    type="number"
    name="price"
    [(ngModel)]="product.price"
  /&gt;
&#8203;
  &lt;button
    class="secondary"
    type="submit"
  &gt;
    Change
  &lt;/button&gt;
&lt;/form&gt;</code></pre>
</div>



`ngSubmit` forma parte de `FormsModule` y se conecta con el evento nativo `submit`.

---

# 8. Actualizar el precio

El método puede quedar:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">changePrice(
  product: Product
) {
  this.productService
    .updateProduct(
      product.id,
      product.price
    )
    .subscribe(() =&gt;
      this.router.navigate([
        '/products'
      ])
    );
}</code></pre>
</div>



El problema de esta primera versión es que, mientras escribimos, `ngModel` modifica directamente `product.price`.

Eso provoca que el precio mostrado en pantalla cambie antes de confirmar.

---

# 9. Separar el precio editable del producto

Creamos una propiedad auxiliar:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">price:
  number | undefined;</code></pre>
</div>



La actualización utiliza esa propiedad:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">changePrice(
  product: Product
) {
  this.productService
    .updateProduct(
      product.id,
      this.price!
    )
    .subscribe(() =&gt;
      this.router.navigate([
        '/products'
      ])
    );
}</code></pre>
</div>



Y la plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  placeholder="New price"
  type="number"
  name="price"
  [(ngModel)]="price"
/&gt;</code></pre>
</div>



Ahora:

- el precio actual del producto permanece visible;
- el usuario introduce un nuevo valor aparte;
- la modificación solo se envía al confirmar.

---

# 10. ¿Cuándo usar template-driven forms?

Son especialmente útiles cuando:

- el formulario es pequeño;
- hay pocas reglas;
- no necesitamos estructuras dinámicas;
- la lógica de formulario es sencilla.

Para escenarios más avanzados, Angular proporciona **reactive forms**.

---

# 11. Formularios reactivos

Los formularios reactivos se construyen programáticamente.

Sus clases principales son:

| Clase | Función |
|---|---|
| `FormControl` | Representa un control individual |
| `FormGroup` | Agrupa varios controles |
| `FormArray` | Agrupa controles dinámicos |

Estas clases pertenecen a:

`@angular/forms`

---

# 12. `ReactiveFormsModule`

En `ProductCreateComponent`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  FormControl,
  FormGroup,
  ReactiveFormsModule
} from '@angular/forms';</code></pre>
</div>



Y:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Component({
  selector: 'app-product-create',
  imports: [
    ReactiveFormsModule
  ],
  templateUrl:
    './product-create.component.html',
  styleUrl:
    './product-create.component.css'
})</code></pre>
</div>



---

# 13. Crear un `FormGroup`

Definimos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productForm =
  new FormGroup({
    title:
      new FormControl(
        '',
        {
          nonNullable: true
        }
      ),
&#8203;
    price:
      new FormControl&lt;
        number | undefined
      &gt;(
        undefined,
        {
          nonNullable: true
        }
      ),
&#8203;
    category:
      new FormControl(
        '',
        {
          nonNullable: true
        }
      )
  });</code></pre>
</div>



El `FormGroup` representa el formulario completo.

Cada propiedad representa un control.

---

# 14. `nonNullable`

Por defecto, muchos controles Angular pueden contener `null`.

Con:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  nonNullable: true
}</code></pre>
</div>



indicamos que el control no debe aceptar `null`.

Esto mejora el tipado y simplifica el uso posterior del valor.

---

# 15. Asociar el `FormGroup` a la plantilla

El formulario HTML se conecta mediante:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form
  [formGroup]="productForm"
&gt;
  ...
&lt;/form&gt;</code></pre>
</div>



`formGroup` es una directiva exportada por `ReactiveFormsModule`.

---

# 16. Asociar controles con `formControlName`

Ejemplo:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div&gt;
  &lt;label for="title"&gt;
    Title
  &lt;/label&gt;
&#8203;
  &lt;input
    id="title"
    formControlName="title"
  /&gt;
&lt;/div&gt;
&#8203;
&lt;div&gt;
  &lt;label for="price"&gt;
    Price
  &lt;/label&gt;
&#8203;
  &lt;input
    id="price"
    formControlName="price"
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
    formControlName="category"
  &gt;
    &lt;option&gt;
      Select a category
    &lt;/option&gt;
&#8203;
    &lt;option value="electronics"&gt;
      Electronics
    &lt;/option&gt;
&#8203;
    &lt;option value="jewelery"&gt;
      Jewelery
    &lt;/option&gt;
&#8203;
    &lt;option&gt;
      Other
    &lt;/option&gt;
  &lt;/select&gt;
&lt;/div&gt;</code></pre>
</div>



Ahora ya no necesitamos variables de referencia como:

```text
#title
#price
#category
```

porque los valores pertenecen al modelo reactivo.

---

# 17. Leer el valor del formulario

Podemos utilizar:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">createProduct() {
  this.productsService
    .addProduct(
      this.productForm.value
    )
    .subscribe(() =&gt; {
      this.router.navigate([
        '/products'
      ]);
    });
}</code></pre>
</div>



Si el modelo del formulario no coincide exactamente con `Product`, podemos acceder a cada control:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">createProduct() {
  this.productsService
    .addProduct({
      title:
        this.productForm
          .controls
          .title
          .value,
&#8203;
      price:
        this.productForm
          .controls
          .price
          .value,
&#8203;
      category:
        this.productForm
          .controls
          .category
          .value
    })
    .subscribe(() =&gt; {
      this.router.navigate([
        '/products'
      ]);
    });
}</code></pre>
</div>



---

# 18. `value` frente a `getRawValue()`

`FormGroup.value` no incluye controles deshabilitados.

Si necesitamos todos los valores:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const data =
  this.productForm
    .getRawValue();</code></pre>
</div>



Comparativa:

| Método | Incluye controles deshabilitados |
|---|:---:|
| `.value` | ❌ |
| `.getRawValue()` | ✅ |

---

# 19. Enviar el formulario reactivo

Plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form
  [formGroup]="productForm"
  (ngSubmit)="createProduct()"
&gt;
  &lt;div&gt;
    &lt;label for="title"&gt;
      Title
    &lt;/label&gt;
&#8203;
    &lt;input
      id="title"
      formControlName="title"
    /&gt;
  &lt;/div&gt;
&#8203;
  &lt;div&gt;
    &lt;label for="price"&gt;
      Price
    &lt;/label&gt;
&#8203;
    &lt;input
      id="price"
      formControlName="price"
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
      formControlName="category"
    &gt;
      &lt;option&gt;
        Select a category
      &lt;/option&gt;
&#8203;
      &lt;option value="electronics"&gt;
        Electronics
      &lt;/option&gt;
&#8203;
      &lt;option value="jewelery"&gt;
        Jewelery
      &lt;/option&gt;
&#8203;
      &lt;option&gt;
        Other
      &lt;/option&gt;
    &lt;/select&gt;
  &lt;/div&gt;
&#8203;
  &lt;div&gt;
    &lt;button type="submit"&gt;
      Create
    &lt;/button&gt;
  &lt;/div&gt;
&lt;/form&gt;</code></pre>
</div>



---

# 20. Estilos globales para etiquetas

El material mueve los estilos de `label` a `styles.css`:



<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">label {
  margin-bottom: 4px;
  display: block;
}</code></pre>
</div>



Así pueden reutilizarse posteriormente en otros formularios, como el carrito.

---

# 21. Formularios anidados

Un `FormGroup` puede contener otro `FormGroup`.

Ejemplo:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productForm =
  new FormGroup({
    title:
      new FormControl(
        '',
        {
          nonNullable: true
        }
      ),
&#8203;
    price:
      new FormControl&lt;
        number | undefined
      &gt;(
        undefined,
        {
          nonNullable: true
        }
      ),
&#8203;
    category:
      new FormControl(
        '',
        {
          nonNullable: true
        }
      ),
&#8203;
    extra:
      new FormGroup({
        image:
          new FormControl(''),
&#8203;
        description:
          new FormControl('')
      })
  });</code></pre>
</div>



La estructura queda:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">productForm
├── title
├── price
├── category
└── extra
    ├── image
    └── description</code></pre>
</div>



El formulario principal es el padre y `extra` es un grupo hijo.

---

# 22. `formGroupName`

En la plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form
  [formGroup]="productForm"
  (ngSubmit)="createProduct()"
&gt;
  &lt;div&gt;
    &lt;label for="title"&gt;
      Title
    &lt;/label&gt;
&#8203;
    &lt;input
      id="title"
      formControlName="title"
    /&gt;
  &lt;/div&gt;
&#8203;
  &lt;div&gt;
    &lt;label for="price"&gt;
      Price
    &lt;/label&gt;
&#8203;
    &lt;input
      id="price"
      formControlName="price"
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
      formControlName="category"
    &gt;
      &lt;option&gt;
        Select a category
      &lt;/option&gt;
&#8203;
      &lt;option value="electronics"&gt;
        Electronics
      &lt;/option&gt;
&#8203;
      &lt;option value="jewelery"&gt;
        Jewelery
      &lt;/option&gt;
&#8203;
      &lt;option&gt;
        Other
      &lt;/option&gt;
    &lt;/select&gt;
  &lt;/div&gt;
&#8203;
  &lt;h2&gt;
    Additional details
  &lt;/h2&gt;
&#8203;
  &lt;form formGroupName="extra"&gt;
    &lt;div&gt;
      &lt;label for="descr"&gt;
        Description
      &lt;/label&gt;
&#8203;
      &lt;input
        id="descr"
        formControlName="description"
      /&gt;
    &lt;/div&gt;
&#8203;
    &lt;div&gt;
      &lt;label for="photo"&gt;
        Photo URL
      &lt;/label&gt;
&#8203;
      &lt;input
        id="photo"
        formControlName="image"
      /&gt;
    &lt;/div&gt;
  &lt;/form&gt;
&#8203;
  &lt;div&gt;
    &lt;button type="submit"&gt;
      Create
    &lt;/button&gt;
  &lt;/div&gt;
&lt;/form&gt;</code></pre>
</div>



`formGroupName="extra"` vincula el grupo hijo.

El valor del grupo hijo forma parte del valor del padre.

---

# 23. Formularios dinámicos con `FormArray`

`FormArray` permite trabajar con un número variable de controles.

Ejemplo de uso:

- productos en un carrito;
- teléfonos de contacto;
- direcciones;
- líneas de factura;
- respuestas dinámicas.

---

# 24. Modelo `Cart`

Generamos:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate interface Cart</code></pre>
</div>



La interfaz:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Cart {
  id: number;
&#8203;
  products: {
    productId: number
  }[];
}</code></pre>
</div>



---

# 25. Crear `CartService`

Generamos:



<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate service cart</code></pre>
</div>



Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Injectable,
  inject
} from '@angular/core';
&#8203;
import {
  HttpClient
} from '@angular/common/http';
&#8203;
import {
  Observable,
  defer,
  map
} from 'rxjs';
&#8203;
import {
  Cart
} from './cart';
&#8203;
import {
  APP_SETTINGS
} from './app.settings';</code></pre>
</div>



Propiedades:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">cart:
  Cart | undefined;
&#8203;
private cartUrl =
  inject(APP_SETTINGS)
    .apiUrl
  + '/carts';</code></pre>
</div>



Inyectamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private http: HttpClient
) {}</code></pre>
</div>



---

# 26. Añadir productos al carrito

Método:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addProduct(
  id: number
): Observable&lt;Cart&gt; {
&#8203;
  const cartProduct = {
    productId: id,
    quantity: 1
  };
&#8203;
  return defer(() =&gt;
    !this.cart
&#8203;
      ? this.http.post&lt;Cart&gt;(
          this.cartUrl,
          {
            products: [
              cartProduct
            ]
          }
        )
&#8203;
      : this.http.put&lt;Cart&gt;(
          `${this.cartUrl}/${this.cart.id}`,
          {
            products: [
              ...this.cart.products,
              cartProduct
            ]
          }
        )
  )
  .pipe(
    map(cart =&gt;
      this.cart = cart
    )
  );
}</code></pre>
</div>



`defer` retrasa la decisión hasta el momento de la suscripción.

Conceptualmente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">¿Existe carrito?
   │
   ├── NO → POST /carts
   │
   └── SÍ → PUT /carts/:id</code></pre>
</div>



---

# 27. Conectar el carrito con el detalle de producto

Inyectamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productService:
    ProductsService,
&#8203;
  public authService:
    AuthService,
&#8203;
  private route:
    ActivatedRoute,
&#8203;
  private router:
    Router,
&#8203;
  private cartService:
    CartService
) {}</code></pre>
</div>



Método:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addToCart(
  id: number
) {
  this.cartService
    .addProduct(id)
    .subscribe();
}</code></pre>
</div>



Plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button
  (click)="addToCart(product.id)"
&gt;
  Add to cart
&lt;/button&gt;</code></pre>
</div>



---

# 28. Crear un carrito reactivo con `FormArray`

Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  FormArray,
  FormControl,
  FormGroup,
  ReactiveFormsModule
} from '@angular/forms';</code></pre>
</div>



Creamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">cartForm =
  new FormGroup({
    products:
      new FormArray&lt;
        FormControl&lt;number&gt;
      &gt;([])
  });
&#8203;
products:
  Product[] = [];</code></pre>
</div>



`FormArray` comienza vacío.

Los controles se añadirán en tiempo de ejecución según los productos del carrito.

---

# 29. Recuperar productos del carrito

Inyectamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private cartService:
    CartService,
&#8203;
  private productsService:
    ProductsService
) {}</code></pre>
</div>



Método:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.productsService
    .getProducts()
    .subscribe(products =&gt; {
&#8203;
      this.cartService
        .cart
        ?.products
        .forEach(item =&gt; {
&#8203;
          const product =
            products.find(
              p =&gt;
                p.id ===
                item.productId
            );
&#8203;
          if (product) {
            this.products.push(
              product
            );
          }
        });
    });
}</code></pre>
</div>



---

# 30. Construir controles dinámicamente

Método:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private buildForm() {
  this.products
    .forEach(() =&gt; {
&#8203;
      this.cartForm
        .controls
        .products
        .push(
          new FormControl(
            1,
            {
              nonNullable: true
            }
          )
        );
    });
}</code></pre>
</div>



Cada producto genera un control numérico cuyo valor inicial es `1`.

---

# 31. Inicialización del carrito

El PDF propone:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.getProducts();
  this.buildForm();
}</code></pre>
</div>



Este es el flujo presentado en la fuente:

```text
ngOnInit()
├── getProducts()
└── buildForm()
```

> 📌 El documento muestra literalmente esta secuencia. Al depender `getProducts()` de una suscripción asíncrona, en una aplicación real convendría revisar cuidadosamente el momento en que se ejecuta `buildForm()`. Esta observación no forma parte del código propuesto por el PDF; se incluye aquí únicamente como advertencia técnica para interpretar correctamente el ejemplo.

---

# 32. Renderizar un `FormArray`

Plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div
  [formGroup]="cartForm"
&gt;
  &lt;div
    formArrayName="products"
  &gt;
    @for (
      product of
        cartForm
          .controls
          .products
          .controls;
      track $index
    ) {
      &lt;label&gt;
        {{
          products[$index]
            .title
        }}
      &lt;/label&gt;
&#8203;
      &lt;input
        [formControlName]="$index"
        type="number"
      /&gt;
    }
  &lt;/div&gt;
&lt;/div&gt;</code></pre>
</div>



`$index` sirve como nombre dinámico del control dentro del array.

---

# 33. Estilos del carrito



<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">:host {
  width: 500px;
}
&#8203;
input {
  width: 50px;
}</code></pre>
</div>



---

# 34. Integrar el carrito con `CanDeactivate`

El guard puede comprobar si existe carrito:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  inject
} from '@angular/core';
&#8203;
import {
  CartService
} from './cart.service';
&#8203;
const cartService =
  inject(CartService);
&#8203;
if (
  cartService.cart
) {
  const confirmation =
    confirm(
      'You have pending items ' +
      'in your cart. ' +
      'Do you want to continue?'
    );
&#8203;
  return confirmation;
}
&#8203;
return true;</code></pre>
</div>



Ahora la confirmación solo aparece cuando realmente hay productos pendientes.

---

# 35. `FormBuilder`

Crear formularios complejos con `new FormGroup(...)` y `new FormControl(...)` puede resultar repetitivo.

Angular proporciona `FormBuilder`.

Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  OnInit
} from '@angular/core';
&#8203;
import {
  FormControl,
  FormGroup,
  ReactiveFormsModule,
  FormBuilder
} from '@angular/forms';</code></pre>
</div>



Inyectamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productsService:
    ProductsService,
&#8203;
  private router:
    Router,
&#8203;
  private builder:
    FormBuilder
) {}</code></pre>
</div>



---

# 36. Definir la estructura del formulario

El material define:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productForm:
  FormGroup&lt;{
    title:
      FormControl&lt;string&gt;;
&#8203;
    price:
      FormControl&lt;
        number | undefined
      &gt;;
&#8203;
    category:
      FormControl&lt;string&gt;;
  }&gt;
  | undefined;</code></pre>
</div>



En este punto solo declaramos la estructura.

La construcción real se delega a `FormBuilder`.

---

# 37. Construir con `FormBuilder`

Método:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private buildForm() {
  this.productForm =
    this.builder
      .nonNullable
      .group({
        title: [''],
&#8203;
        price:
          this.builder
            .nonNullable
            .control&lt;
              number | undefined
            &gt;(undefined),
&#8203;
        category: ['']
      });
}</code></pre>
</div>



Aspectos importantes:

- `builder.nonNullable.group()` crea el grupo;
- `title` y `category` usan una sintaxis abreviada;
- `price` necesita un control explícito por su tipo `number | undefined`.

---

# 38. Construir el formulario en `ngOnInit`



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.buildForm();
}</code></pre>
</div>



Al inicializarse el componente se crea el modelo reactivo.

---

# 39. Operador de aserción no nula

Como `productForm` puede ser `undefined` hasta que se construye:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">createProduct() {
  this.productsService
    .addProduct(
      this.productForm!.value
    )
    .subscribe(() =&gt; {
      this.router.navigate([
        '/products'
      ]);
    });
}</code></pre>
</div>



Y en plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form
  [formGroup]="productForm!"
  (ngSubmit)="createProduct()"
&gt;
  ...
&lt;/form&gt;</code></pre>
</div>



El operador `!` indica a TypeScript que asumimos que el valor ya existe.

---

# 40. Ventajas de `FormBuilder`

`FormBuilder` permite reducir código repetitivo.

En vez de escribir continuamente:

```text
new FormGroup(...)
new FormControl(...)
```

utilizamos métodos auxiliares:

```text
builder.group(...)
builder.control(...)
builder.nonNullable.group(...)
```

Internamente, Angular continúa creando `FormGroup` y `FormControl`.

---

# 41. Introducción a la validación

El PDF comienza la sección de validación indicando que un formulario debería:

- validar los datos;
- proporcionar feedback visual;
- guiar al usuario;
- evitar enviar información incorrecta.

El capítulo enumera cuatro estrategias:

1. validación global con CSS;
2. validación en la clase del componente;
3. validación en la plantilla;
4. validadores personalizados.

> 📌 El PDF adjunto solo desarrolla el inicio de la primera estrategia. Las siguientes aparecen mencionadas, pero no desarrolladas en las páginas proporcionadas.

---

# 42. Clases CSS de estado que Angular aplica automáticamente

Angular añade clases CSS automáticamente a formularios y controles.

### Interacción

| Clase | Significado |
|---|---|
| `ng-untouched` | El usuario todavía no ha interactuado |
| `ng-touched` | El usuario ya ha interactuado |
| `ng-pristine` | El control no se ha modificado |
| `ng-dirty` | El control ha sido modificado |

### Validez

| Clase | Significado |
|---|---|
| `ng-valid` | Valor válido |
| `ng-invalid` | Valor no válido |

Angular mantiene estas clases tanto en:

- formularios template-driven;
- formularios reactivos.

---

# 43. Propagación del estado

El estado de un formulario depende de sus controles.

Ejemplo:

```text
FormGroup
├── title       → valid
├── price       → invalid
└── category    → valid

Resultado:
FormGroup → invalid
```

Si un control hijo es inválido, el estado puede propagarse al grupo padre.

Lo mismo ocurre en jerarquías anidadas.

---

# 44. Comparativa final: Template-driven vs Reactive Forms

| Característica | Template-driven | Reactive |
|---|---|---|
| Configuración | Principalmente HTML | Principalmente TypeScript |
| Módulo | `FormsModule` | `ReactiveFormsModule` |
| Binding | `[(ngModel)]` | `formControlName` |
| Modelo explícito | Limitado | Sí |
| Testing | Más difícil | Más sencillo |
| Formularios complejos | Menos apropiado | Más apropiado |
| Controles dinámicos | Menos cómodo | `FormArray` |
| Reactividad | Basada en directivas | Integrada en el modelo |

---

# 45. Esquema general de formularios Angular



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ANGULAR FORMS
│
├── Template-driven
│   ├── FormsModule
│   ├── ngModel
│   └── ngSubmit
│
└── Reactive Forms
    ├── ReactiveFormsModule
    ├── FormControl
    ├── FormGroup
    ├── FormArray
    ├── formControlName
    ├── formGroupName
    └── FormBuilder</code></pre>
</div>



---

# 46. Resumen de la unidad

En esta unidad hemos aprendido los fundamentos de formularios en Angular.

Hemos trabajado con:

- formularios HTML;
- template-driven forms;
- two-way binding;
- `[(ngModel)]`;
- `FormsModule`;
- `(ngSubmit)`;
- reactive forms;
- `ReactiveFormsModule`;
- `FormControl`;
- `FormGroup`;
- `FormArray`;
- `formControlName`;
- `formGroupName`;
- formularios anidados;
- formularios dinámicos;
- `FormBuilder`;
- `value`;
- `getRawValue()`;
- clases CSS automáticas de estado.

---

# 47. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Formulario | Estructura para recoger datos |
| Template-driven | Formulario configurado principalmente en HTML |
| Reactive form | Formulario gestionado desde TypeScript |
| `FormsModule` | Habilita template-driven forms |
| `ReactiveFormsModule` | Habilita reactive forms |
| `ngModel` | Binding del control |
| `[(ngModel)]` | Two-way binding |
| `ngSubmit` | Evento Angular de envío |
| `FormControl` | Control individual |
| `FormGroup` | Grupo de controles |
| `FormArray` | Colección dinámica de controles |
| `formGroup` | Vincula un grupo a `<form>` |
| `formControlName` | Vincula control y elemento |
| `formGroupName` | Vincula grupo hijo |
| `nonNullable` | Impide valores `null` |
| `.value` | Valor activo del formulario |
| `getRawValue()` | Incluye controles deshabilitados |
| `FormBuilder` | Servicio auxiliar para crear formularios |
| `ng-touched` | El usuario interactuó |
| `ng-untouched` | Sin interacción |
| `ng-dirty` | Modificado |
| `ng-pristine` | Sin modificar |
| `ng-valid` | Válido |
| `ng-invalid` | Inválido |

---

## 🔗 Recursos citados en el material

- Angular Forms: <https://angular.dev/guide/forms>
- Template-driven forms: <https://angular.dev/guide/forms/template-driven-forms>
- Reactive forms: <https://angular.dev/guide/forms/reactive-forms>

---

> 🎓 **Siguiente paso**  
> El PDF termina al comenzar la validación global mediante clases CSS. El siguiente bloque lógico del capítulo sería profundizar en validadores, mensajes de error y manipulación del estado del formulario, pero ese contenido no está incluido en el documento adjunto.

---

# 48. Taller práctico: formularios Angular sobre el proyecto real 🧪

Los ejercicios de esta sección toman como referencia directa el repositorio:

<https://github.com/hokahey007/codebase-angular/tree/main/10_formularios>

El estado actual del proyecto permite trabajar con varios conceptos de la unidad en un mismo código:

- `ProductDetailComponent` utiliza un formulario **template-driven** con `FormsModule`, `ngModel` y `ngForm`;
- el precio del detalle se valida con atributos HTML y con una directiva personalizada `appPriceMaximum`;
- `ProductCreateComponent` utiliza **Reactive Forms** con `FormGroup`, `FormControl` y `Validators`;
- el formulario de creación incluye un validador personalizado `priceMaximumValidator`;
- `CartComponent` utiliza un `FormArray<FormControl<number>>`;
- `CartService` utiliza `defer()` para decidir entre crear o actualizar el carrito;
- los estilos globales reaccionan a clases como `ng-dirty`, `ng-valid` y `ng-invalid`.

> 🎯 **Forma recomendada de trabajo**  
> Realiza los ejercicios en orden. Inspecciona también el DOM con DevTools para observar las clases que Angular añade automáticamente a cada control.

---

## Ejercicio 1. Localiza los dos enfoques de formularios

### Objetivo

Distinguir claramente template-driven forms y reactive forms en el repositorio.

Abre:

- `product-detail.component.ts`;
- `product-detail.component.html`;
- `product-create.component.ts`;
- `product-create.component.html`.

Completa:

| Componente | Tipo de formulario | Módulo Angular | Directivas principales |
|---|---|---|---|
| `ProductDetailComponent` |  |  |  |
| `ProductCreateComponent` |  |  |  |

### Preguntas

1. ¿En qué componente el modelo del formulario está definido principalmente en HTML?
2. ¿En cuál está definido en TypeScript?
3. ¿Cuál utiliza `[(ngModel)]`?
4. ¿Cuál utiliza `[formGroup]` y `formControlName`?

---

## Ejercicio 2. Sigue el two-way binding del precio

### Objetivo

Comprender qué ocurre en el formulario template-driven del detalle.

El repositorio utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  placeholder="New price"
  type="number"
  name="price"
  required
  min="1"
  appPriceMaximum
  threshold="500"
  #priceCtrl="ngModel"
  [(ngModel)]="price"
/&gt;</code></pre>
</div>


### Tareas

1. Selecciona un producto.
2. Escribe un nuevo precio sin enviar el formulario.
3. Comprueba el valor de la propiedad `price` desde el componente o mediante un `console.log`.
4. Explica el flujo:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Input HTML
   ⇅
[(ngModel)]
   ⇅
price
&#8203;</code></pre>
</div>


### Pregunta

¿Por qué el repositorio enlaza el input con `price` y no directamente con `product.price`?

---

## Ejercicio 3. Comprueba el papel de `name`

### Objetivo

Entender por qué `name="price"` es importante en template-driven forms.

Elimina temporalmente:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">name="price" </code></pre>
</div>


del input asociado a `ngModel`.

### Tareas

1. Compila y ejecuta.
2. Observa el error o comportamiento producido.
3. Restaura el atributo.

### Explica

¿Por qué Angular necesita un nombre para registrar un control dentro de `ngForm`?

---

## Ejercicio 4. Analiza `#priceForm="ngForm"`

### Objetivo

Comprender la referencia de plantilla al formulario Angular.

El repositorio declara:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form
  (ngSubmit)="changePrice(product)"
  #priceForm="ngForm"
&gt;
&#8203;</code></pre>
</div>


y deshabilita el botón mediante:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">[disabled]="priceForm.invalid" </code></pre>
</div>


### Tareas

Muestra temporalmente:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;pre&gt;
valid: {{ priceForm.valid }}
invalid: {{ priceForm.invalid }}
dirty: {{ priceForm.dirty }}
pristine: {{ priceForm.pristine }}
touched: {{ priceForm.touched }}
&lt;/pre&gt;</code></pre>
</div>


Escribe y borra valores y observa cómo cambia cada estado.

---

## Ejercicio 5. Inspecciona las clases CSS automáticas

### Objetivo

Relacionar el estado de Angular Forms con el DOM real.

El `styles.css` del repositorio contiene reglas equivalentes a:

<div class="code-card" data-lang="css">
<div class="code-label">CSS</div>
<pre><code class="language-css">input.ng-dirty.ng-valid {
  border: solid green;
}
&#8203;
input.ng-dirty.ng-invalid {
  border: solid red;
}</code></pre>
</div>


### Tareas

1. Abre DevTools.
2. Selecciona el input del precio.
3. Antes de escribir, anota sus clases.
4. Escribe un precio válido.
5. Escribe uno inválido.
6. Sal del campo.
7. Anota cuándo aparecen:
   - `ng-pristine`;
   - `ng-dirty`;
   - `ng-touched`;
   - `ng-valid`;
   - `ng-invalid`.

---

## Ejercicio 6. Valida el mínimo del precio

### Objetivo

Comprobar la validación HTML integrada con Angular Forms.

El input contiene:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">required
min="1" </code></pre>
</div>


Prueba:

```text
vacío
0
-10
1
25
```

### Tareas

Para cada valor indica:

- `priceCtrl.valid`;
- `priceCtrl.hasError('required')`;
- `priceCtrl.hasError('min')`;
- estado del botón `Change`.

---

## Ejercicio 7. Estudia el validador personalizado `priceMaximumValidator`

### Objetivo

Comprender cómo se construye un `ValidatorFn`.

El repositorio define un validador equivalente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export function priceMaximumValidator(
  price: number
): ValidatorFn {
  return (
    control: AbstractControl
  ): ValidationErrors | null =&gt; {
    const isMax =
      control.value &lt;= price;
&#8203;
    return isMax
      ? null
      : {
          priceMaximum: true
        };
  };
}</code></pre>
</div>


### Preguntas

1. ¿Por qué la función exterior recibe el máximo?
2. ¿Qué recibe la función interior?
3. ¿Qué representa `null`?
4. ¿Qué representa `{ priceMaximum: true }`?
5. ¿Qué nombre debe consultar después `hasError(...)`?

---

## Ejercicio 8. Cambia el máximo del formulario reactivo

### Objetivo

Practicar la parametrización de un validador personalizado.

En `ProductCreateComponent` el precio utiliza un máximo de `1000`.

Cámbialo temporalmente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">priceMaximumValidator(250)</code></pre>
</div>


### Tareas

Prueba los valores:

```text
249
250
251
1000
```

Comprueba el estado del formulario y el mensaje mostrado.

Después restaura el valor inicial.

---

## Ejercicio 9. Analiza la directiva `PriceMaximumDirective`

### Objetivo

Comprender cómo se reutiliza el mismo validador en template-driven forms.

La directiva del repositorio:

- implementa `Validator`;
- se registra mediante `NG_VALIDATORS`;
- utiliza `multi: true`;
- recibe el máximo mediante un input con alias `threshold`;
- transforma el atributo con `numberAttribute`.

En la plantilla se usa así:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  appPriceMaximum
  threshold="500"
/&gt;</code></pre>
</div>


### Preguntas

1. ¿Por qué el límite del detalle puede ser `500` y el de creación `1000`?
2. ¿Qué ventaja tiene reutilizar `priceMaximumValidator()` dentro de la directiva?
3. ¿Para qué sirve `multi: true`?

---

## Ejercicio 10. Haz configurable el límite desde el componente

### Objetivo

Evitar un valor fijo en la plantilla.

En `ProductDetailComponent` añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">maximumPrice = 750;</code></pre>
</div>


Y cambia:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  appPriceMaximum
  [threshold]="maximumPrice"
  ...
/&gt;</code></pre>
</div>


### Tareas

1. Prueba `500`, `750` y `1000`.
2. Comprueba que la directiva reacciona al valor recibido.
3. Muestra el máximo dinámicamente en el texto de error.

---

## Ejercicio 11. Analiza el `FormGroup` de creación

### Objetivo

Interpretar el modelo reactivo actual.

El proyecto contiene un grupo equivalente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productForm =
  new FormGroup({
    title:
      new FormControl(
        '',
        {
          nonNullable: true,
          validators:
            Validators.required
        }
      ),
&#8203;
    price:
      new FormControl&lt;
        number | undefined
      &gt;(
        undefined,
        {
          nonNullable: true,
          validators: [
            Validators.required,
            Validators.min(1),
            priceMaximumValidator(
              1000
            )
          ]
        }
      ),
&#8203;
    category:
      new FormControl(
        '',
        {
          nonNullable: true
        }
      )
  });</code></pre>
</div>


### Tareas

Para cada control indica:

- tipo TypeScript;
- valor inicial;
- si acepta `null`;
- validadores.

---

## Ejercicio 12. Haz obligatoria la categoría

### Objetivo

Añadir una regla de validación al formulario reactivo.

Modifica:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">category:
  new FormControl(
    '',
    {
      nonNullable: true,
      validators:
        Validators.required
    }
  )</code></pre>
</div>


Asegúrate también de que la opción inicial tenga valor vacío:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;option value=""&gt;
  Select a category
&lt;/option&gt;</code></pre>
</div>


Añade un mensaje de error análogo al del título.

---

## Ejercicio 13. Analiza los mensajes de error de `ProductCreateComponent`

### Objetivo

Distinguir diferentes errores de un mismo control.

El precio puede tener:

- `required`;
- `min`;
- `priceMaximum`.

### Tareas

Construye una tabla:

| Valor | `required` | `min` | `priceMaximum` |
|---:|:---:|:---:|:---:|
| vacío |  |  |  |
| 0 |  |  |  |
| 1 |  |  |  |
| 1000 |  |  |  |
| 1001 |  |  |  |

Después comprueba la tabla ejecutando la aplicación.

---

## Ejercicio 14. Impide el submit de un formulario inválido

### Objetivo

Relacionar la validez del grupo con el botón.

El repositorio ya utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button
  type="submit"
  [disabled]="productForm.invalid"
&gt;
  Create
&lt;/button&gt;</code></pre>
</div>


### Tareas

1. Abre el formulario vacío.
2. Observa el botón.
3. Completa solo el título.
4. Completa un precio inválido.
5. Introduce finalmente datos válidos.

### Explica

¿Por qué `productForm.invalid` refleja los errores de sus controles hijos?

---

## Ejercicio 15. Añade información de depuración del formulario

### Objetivo

Visualizar el modelo reactivo mientras se trabaja.

Añade temporalmente:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;pre&gt;
{{ productForm.value | json }}
&lt;/pre&gt;</code></pre>
</div>


Para poder usar `json`, importa `JsonPipe` o `CommonModule` según el diseño que prefieras.

### Tareas

1. Cambia cada control.
2. Observa cómo cambia el objeto.
3. Compara con `productForm.getRawValue()` desde TypeScript.

---

## Ejercicio 16. Practica `setValue()` y `patchValue()`

### Objetivo

Modificar el modelo del formulario desde el componente.

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">loadExample(): void {
  this.productForm.setValue({
    title: 'Mechanical Keyboard',
    price: 120,
    category: 'electronics'
  });
}</code></pre>
</div>


Y otro método:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">applyDiscountPrice(): void {
  this.productForm.patchValue({
    price: 99
  });
}</code></pre>
</div>


### Pregunta

¿Qué diferencia existe entre `setValue()` y `patchValue()` cuando no suministramos todas las propiedades?

---

## Ejercicio 17. Reinicia el formulario tras una operación

### Objetivo

Utilizar el estado del formulario programáticamente.

Como variante de práctica, elimina temporalmente la navegación tras crear y ejecuta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.productForm.reset({
  title: '',
  price: undefined,
  category: ''
});</code></pre>
</div>


### Tareas

1. Crea un producto.
2. Observa el formulario.
3. Comprueba sus estados `pristine` y `untouched` después del reset.

---

## Ejercicio 18. Refactoriza `ProductCreateComponent` con `FormBuilder`

### Objetivo

Aplicar el contenido de la unidad sobre el código real del repositorio.

Inyecta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private productsService:
    ProductsService,
  private router:
    Router,
  private builder:
    FormBuilder
) {}</code></pre>
</div>


Y construye una versión equivalente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productForm =
  this.builder.nonNullable.group({
    title: [
      '',
      Validators.required
    ],
&#8203;
    price:
      this.builder.control&lt;
        number | undefined
      &gt;(
        undefined,
        {
          validators: [
            Validators.required,
            Validators.min(1),
            priceMaximumValidator(
              1000
            )
          ]
        }
      ),
&#8203;
    category: ['']
  });</code></pre>
</div>


### Tareas

1. Mantén exactamente las mismas validaciones.
2. Comprueba que la plantilla no necesita cambios.
3. Compara legibilidad y cantidad de código.

---

## Ejercicio 19. Añade un `FormGroup` anidado `extra`

### Objetivo

Practicar formularios jerárquicos.

Amplía el formulario de producto con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">extra:
  new FormGroup({
    description:
      new FormControl(''),
&#8203;
    image:
      new FormControl('')
  })</code></pre>
</div>


En plantilla utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;div formGroupName="extra"&gt;
  &lt;label for="description"&gt;
    Description
  &lt;/label&gt;
&#8203;
  &lt;input
    id="description"
    formControlName="description"
  /&gt;
&#8203;
  &lt;label for="image"&gt;
    Image URL
  &lt;/label&gt;
&#8203;
  &lt;input
    id="image"
    formControlName="image"
  /&gt;
&lt;/div&gt;</code></pre>
</div>


### Tarea

Muestra `productForm.getRawValue()` y comprueba la estructura anidada generada.

---

## Ejercicio 20. Analiza el `FormArray` del carrito

### Objetivo

Comprender el formulario dinámico existente.

`CartComponent` define:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">cartForm =
  new FormGroup({
    products:
      new FormArray&lt;
        FormControl&lt;number&gt;
      &gt;([])
  });</code></pre>
</div>


La plantilla itera sobre los controles y usa `$index` como `formControlName`.

### Preguntas

1. ¿Por qué `FormArray` es más apropiado que un `FormGroup` para este caso?
2. ¿Qué representa cada `FormControl<number>`?
3. ¿Por qué los nombres de los controles son índices?

---

## Ejercicio 21. Investiga la inicialización del `FormArray`

### Objetivo

Analizar la secuencia asíncrona real del repositorio.

Actualmente `ngOnInit()` ejecuta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.getProducts();
  this.buildForm();
}</code></pre>
</div>


y `getProducts()` obtiene los datos dentro de una suscripción.

### Tareas

1. Añade `console.log` al inicio y final de `getProducts()`.
2. Añade otro al inicio de `buildForm()`.
3. Prueba con datos ya cacheados.
4. Prueba después de una recarga completa.
5. Comprueba cuántos controles se generan.

### Reto de refactorización

Haz que `buildForm()` se ejecute cuando `products` ya esté preparado:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts(): void {
  this.productsService
    .getProducts()
    .subscribe(products =&gt; {
      this.products = [];
&#8203;
      this.cartService
        .cart
        ?.products
        .forEach(item =&gt; {
          const product =
            products.find(
              p =&gt;
                p.id ===
                item.productId
            );
&#8203;
          if (product) {
            this.products.push(
              product
            );
          }
        });
&#8203;
      this.buildForm();
    });
}</code></pre>
</div>


Después elimina la llamada independiente a `buildForm()` de `ngOnInit()`.

---

## Ejercicio 22. Añade y elimina controles dinámicamente

### Objetivo

Practicar las operaciones de `FormArray`.

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">addQuantityControl(): void {
  this.cartForm
    .controls
    .products
    .push(
      new FormControl(
        1,
        {
          nonNullable: true
        }
      )
    );
}
&#8203;
removeQuantityControl(
  index: number
): void {
  this.cartForm
    .controls
    .products
    .removeAt(index);
}</code></pre>
</div>


Añade botones temporales para probar ambos métodos.

### Pregunta

¿Qué diferencia fundamental hay entre un `FormArray` y un grupo con número fijo de controles?

---

## Ejercicio 23. Valida las cantidades del carrito

### Objetivo

Aplicar validadores a controles creados dinámicamente.

Cambia la creación del control:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">new FormControl(
  1,
  {
    nonNullable: true,
    validators: [
      Validators.required,
      Validators.min(1),
      Validators.max(10)
    ]
  }
)</code></pre>
</div>


### Tareas

1. Importa `Validators`.
2. Introduce `0`.
3. Introduce `11`.
4. Comprueba el estado del `FormArray`.
5. Comprueba también `cartForm.invalid`.

---

## Ejercicio 24. Calcula el total del carrito a partir del formulario

### Objetivo

Relacionar el valor de los controles dinámicos con los productos.

Crea un método:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getTotal(): number {
  return this.products.reduce(
    (
      total,
      product,
      index
    ) =&gt; {
      const quantity =
        this.cartForm
          .controls
          .products
          .at(index)
          .value;
&#8203;
      return total +
        product.price *
        quantity;
    },
    0
  );
}</code></pre>
</div>


Muestra:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;p&gt;
  Total:
  {{ getTotal() | currency:'EUR' }}
&lt;/p&gt;</code></pre>
</div>


### Reto

Evita recalcular innecesariamente el total utilizando un enfoque reactivo basado en `valueChanges`.

---

## Ejercicio 25. Analiza `CartService` y `defer()`

### Objetivo

Comprender la lógica observable del carrito.

El servicio decide entre:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">sin carrito
    → POST /carts
&#8203;
con carrito
    → PUT /carts/:id
&#8203;</code></pre>
</div>


### Tareas

1. Añade el primer producto.
2. Observa la petición en Network.
3. Añade un segundo producto.
4. Compara método y URL.
5. Explica por qué la decisión se coloca dentro de `defer()`.

---

## Ejercicio 26. Relaciona formulario y `checkoutGuard`

### Objetivo

Comprender cómo el estado de negocio afecta a la navegación.

El guard del repositorio consulta `CartService.cart`.

### Tareas

1. Entra al carrito sin haber añadido productos.
2. Intenta salir.
3. Añade un producto.
4. Entra al carrito.
5. Intenta salir otra vez.

### Pregunta

¿El guard inspecciona directamente `cartForm` o el estado del servicio? ¿Por qué puede ser conveniente mantener la lógica de negocio fuera del formulario?

---

# 49. Reto integrador: formulario completo de producto y carrito 🚀

### Objetivo

Aplicar template-driven forms, reactive forms, validación y formularios dinámicos sobre el proyecto real.

## Parte 1. Detalle de producto

Mantén el formulario template-driven para cambiar el precio.

Debe incluir:

- `[(ngModel)]`;
- `required`;
- `min`;
- `appPriceMaximum`;
- referencia `ngModel`;
- mensajes de error;
- botón deshabilitado si el formulario es inválido.

## Parte 2. Alta de producto

El formulario reactivo debe incluir:

- título obligatorio;
- precio obligatorio;
- mínimo `1`;
- máximo personalizado `1000`;
- categoría obligatoria;
- grupo anidado `extra`;
- descripción;
- URL de imagen.

## Parte 3. Carrito

Cada producto tendrá un control de cantidad dinámico dentro de `FormArray`.

Las cantidades deberán cumplir:

```text
mínimo: 1
máximo: 10
```

El carrito mostrará:

- nombre del producto;
- precio unitario;
- cantidad;
- subtotal;
- total general.

## Parte 4. Estados visuales

Utiliza las clases automáticas de Angular para mostrar:

- borde rojo si el control modificado es inválido;
- borde verde si es válido;
- textos de ayuda solo después de interacción.

## Checklist

- [ ] Existe un formulario template-driven funcional.
- [ ] Existe un formulario reactivo funcional.
- [ ] Se utiliza `FormsModule`.
- [ ] Se utiliza `ReactiveFormsModule`.
- [ ] Se utiliza `ngModel`.
- [ ] Se utiliza `FormGroup`.
- [ ] Se utiliza `FormControl`.
- [ ] Se utiliza `FormArray`.
- [ ] Existe un `FormGroup` anidado.
- [ ] Se utilizan validadores integrados.
- [ ] Se utiliza `priceMaximumValidator`.
- [ ] Se utiliza `PriceMaximumDirective`.
- [ ] El submit se bloquea cuando corresponde.
- [ ] Se muestran errores contextualizados.
- [ ] Se comprueban estados `dirty`, `touched`, `valid` e `invalid`.
- [ ] El proyecto compila sin errores.

---

# 50. Diagnóstico de errores de formularios 🔎

## Caso A. `ngModel` sin `FormsModule`

La plantilla contiene:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  [(ngModel)]="price"
/&gt;</code></pre>
</div>


pero el componente standalone no importa `FormsModule`.

**Pregunta:** ¿qué debe corregirse?

---

## Caso B. `ngModel` dentro de formulario sin `name`

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;form&gt;
  &lt;input
    [(ngModel)]="price"
  /&gt;
&lt;/form&gt;</code></pre>
</div>


**Pregunta:** ¿qué información necesita Angular para registrar el control?

---

## Caso C. `formControlName` inexistente

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  formControlName="description"
/&gt;</code></pre>
</div>


pero `productForm` no contiene `description`.

**Pregunta:** ¿por qué la plantilla y el modelo deben coincidir?

---

## Caso D. Error consultado con nombre incorrecto

El validador devuelve:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  priceMaximum: true
}</code></pre>
</div>


pero la plantilla consulta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">hasError(
  'maximumPrice'
)</code></pre>
</div>


**Pregunta:** ¿por qué nunca se mostrará el mensaje correcto?

---

## Caso E. `FormArray` construido antes de disponer de productos

`buildForm()` se ejecuta cuando `products` todavía está vacío.

**Pregunta:** ¿cuántos controles se crearán y cómo puede reorganizarse el flujo?

---

## Caso F. Botón siempre habilitado

El formulario tiene validadores, pero el botón no utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">[disabled]="productForm.invalid" </code></pre>
</div>


**Pregunta:** ¿significa eso que Angular deja de validar o únicamente que la interfaz permite intentar enviar?

---

# 51. Cuestiones finales de reflexión 📝

1. ¿Qué diferencia principal existe entre template-driven forms y reactive forms?
2. ¿Para qué sirve `FormsModule`?
3. ¿Qué representa `[(ngModel)]`?
4. ¿Por qué `ngModel` suele necesitar `name` dentro de un formulario?
5. ¿Qué información ofrece `ngForm`?
6. ¿Qué diferencia existe entre `dirty` y `touched`?
7. ¿Qué diferencia existe entre `pristine` y `untouched`?
8. ¿Qué representa un `FormControl`?
9. ¿Qué representa un `FormGroup`?
10. ¿Cuándo utilizarías `FormArray`?
11. ¿Qué aporta `nonNullable`?
12. ¿Qué diferencia existe entre `.value` y `.getRawValue()`?
13. ¿Qué ventaja aporta `FormBuilder`?
14. ¿Qué hace `Validators.required`?
15. ¿Qué hace `Validators.min(1)`?
16. ¿Cómo se crea un validador parametrizable?
17. ¿Por qué un validador válido devuelve `null`?
18. ¿Qué función tiene `NG_VALIDATORS` en una directiva de validación?
19. ¿Por qué `multi: true` es importante al registrar validadores?
20. ¿Qué ventaja tiene reutilizar la misma función validadora en reactive y template-driven forms?
21. ¿Por qué el estado inválido de un control afecta al `FormGroup`?
22. ¿Qué ventaja tiene un `FormArray` para representar las cantidades de un carrito?
23. ¿Qué problema potencial existe si construimos controles dinámicos antes de recibir los datos?
24. ¿Qué responsabilidades deberían permanecer en el formulario y cuáles deberían estar en servicios?

---

> ✅ **Meta de aprendizaje**  
> Si puedes construir y validar tanto un formulario template-driven como uno reactivo, crear validadores reutilizables y manejar controles dinámicos mediante `FormArray`, ya dominas los fundamentos principales de formularios Angular tratados por esta unidad y su repositorio.
