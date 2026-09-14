# 🔄 Unidad 6: Patrones reactivos en Angular

<style>
.code-card{margin:1.35rem 0;border:1px solid #d0d7de;border-radius:10px;overflow:hidden;background:#f6f8fa;box-shadow:0 2px 8px rgba(31,35,40,.08)}
.code-card .code-label{display:inline-block;margin:0;padding:.35rem .8rem;background:#111;color:#fff;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;font-size:.72rem;font-weight:700;letter-spacing:.08em;text-transform:uppercase;border-bottom-right-radius:7px}
.code-card pre{margin:0;padding:1rem 1.15rem 1.15rem;overflow-x:auto;background:#f6f8fa;line-height:1.55;tab-size:2}
.code-card code{font-family:"Cascadia Code","JetBrains Mono",Consolas,"Courier New",monospace;font-size:.92rem;color:#24292f;background:transparent;white-space:pre}
.code-card[data-lang="typescript"] pre,.code-card[data-lang="javascript"] pre{background:#f7f9fc}
.code-card[data-lang="html"] pre{background:#fff8f5}
.code-card[data-lang="css"] pre{background:#f8fbff}
.code-card[data-lang="bash"] pre,.code-card[data-lang="shell"] pre{background:#f7fbf7}
.code-card[data-lang="json"] pre,.code-card[data-lang="text"] pre{background:#fafafa}
:not(pre)>code{padding:.12em .38em;border-radius:4px;background:#eef1f4;color:#24292f;font-family:"Cascadia Code","JetBrains Mono",Consolas,monospace;font-size:.92em}
</style>

> **Referencia:** Angular 19  
> **Objetivo:** comprender cómo manejar información asíncrona en Angular mediante callbacks, promesas, observables y RxJS, y aprender a gestionar correctamente las suscripciones.

---

## 🎯 Qué vas a aprender

- Estrategias para manejar información asíncrona.
- Diferencias entre callbacks, promesas y observables.
- Fundamentos de programación reactiva.
- Creación de observables con RxJS.
- Uso de `subscribe()`.
- Uso de `fromEvent`, `of` y `from`.
- Uso de `pipe`, `tap`, `map` y `filter`.
- Gestión de suscripciones y prevención de fugas de memoria.
- Uso de `takeUntilDestroyed`.
- Uso del pipe `async`.

---

## 📑 Contenidos

1. [Callbacks y promesas](#2-callbacks)
2. [Observables y programación reactiva](#6-observables)
3. [RxJS y sus operadores](#12-rxjs)
4. [Productos como observables](#19-convertir-productsservice-a-observables)
5. [Gestión de suscripciones](#21-cancelar-suscripciones)
6. [Pipe async](#23-pipe-async)
7. [Taller práctico](#30-taller-práctico-promesas-observables-y-rxjs)
8. [Reto integrador](#31-reto-integrador-panel-reactivo-de-productos)
9. [Diagnóstico y reflexión](#32-diagnóstico-de-errores-reactivos)

---

# 1. Información asíncrona en aplicaciones Angular

La información asíncrona aparece continuamente en aplicaciones reales:

- llamadas HTTP;
- autenticación;
- lectura de recursos remotos;
- interacción del usuario;
- eventos del navegador;
- tareas diferidas.

Aunque las conexiones sean rápidas, siempre existe un tiempo de respuesta. Por eso la aplicación debe poder continuar funcionando mientras espera datos.

---

# 2. Callbacks

Un callback es una función que se pasa como argumento a otra función y se ejecuta al finalizar una operación asíncrona.

En la plantilla mostramos el título:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;header&gt;{{ title }}&lt;/header&gt;
&#8203;
&lt;main class="main"&gt;
  &lt;div class="content"&gt;
    &lt;app-product-list&gt;&lt;/app-product-list&gt;
  &lt;/div&gt;
&lt;/main&gt;
&#8203;
&lt;footer appCopyright&gt;
  - v{{ settings.version }}
&lt;/footer&gt;
&#8203;
&lt;router-outlet /&gt;</code></pre>
</div>

En `AppComponent` definimos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private setTitle = () =&gt; {
  this.title = this.settings.title;
};</code></pre>
</div>

Creamos un método que ejecuta un callback después de dos segundos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private changeTitle(callback: Function) {
  setTimeout(() =&gt; {
    callback();
  }, 2000);
}</code></pre>
</div>

Y lo usamos desde el constructor:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  this.changeTitle(this.setTitle);
}</code></pre>
</div>

> 🧠 Cuando pasamos una función como callback pasamos su referencia, no la invocamos con `()`.

---

# 3. Callback hell

Cuando una operación depende de otra, los callbacks pueden anidarse:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getRootFolder(folder =&gt; {
  getAssetsFolder(folder, assets =&gt; {
    getPhotos(assets, photos =&gt; {
      // ...
    });
  });
});</code></pre>
</div>

Esta estructura se vuelve difícil de leer y mantener y se conoce como **callback hell**.

---

# 4. Promesas

Las promesas permiten representar una operación asíncrona que puede resolverse o rechazarse.

Creamos una promesa:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private onComplete() {
  return new Promise&lt;void&gt;(resolve =&gt; {
    setTimeout(() =&gt; {
      resolve();
    }, 2000);
  });
}</code></pre>
</div>

Y la consumimos con `then`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  this.onComplete()
    .then(this.setTitle);
}</code></pre>
</div>

Las operaciones pueden encadenarse:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getRootFolder()
  .then(getAssetsFolder)
  .then(getPhotos);</code></pre>
</div>

Las promesas mejoran mucho la legibilidad, pero siguen teniendo limitaciones.

---

# 5. Limitaciones de las promesas

Las principales limitaciones señaladas en el material son:

- no pueden cancelarse;
- se ejecutan inmediatamente al crearse;
- son operaciones de una sola resolución;
- producen un único valor;
- no ofrecen una forma sencilla de reintentar o continuar emitiendo.

Incluso si usamos `setInterval`, la promesa solo se resuelve una vez:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private onComplete() {
  return new Promise&lt;void&gt;(resolve =&gt; {
    setInterval(() =&gt; {
      resolve();
    }, 2000);
  });
}</code></pre>
</div>

Podemos modificar el título con una marca temporal:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private setTitle = () =&gt; {
  const timestamp = new Date();
  this.title =
    `${this.settings.title} (${timestamp})`;
};</code></pre>
</div>

El timestamp solo se establece una vez porque la promesa termina tras resolverse.

---

# 6. Observables

Un observable mantiene observadores o subscribers y emite cambios de estado o datos de forma asíncrona.



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Observable
    │
    │ emite eventos
    ▼
Observer / Subscriber
    │
    ▼
Reacción de la aplicación</code></pre>
</div>

Los observables pueden:

- emitir múltiples valores;
- permanecer activos;
- cancelarse;
- transformarse y filtrarse mediante operadores.

---

# 7. Crear un observable

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { Observable } from 'rxjs';</code></pre>
</div>

Creamos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ = new Observable(observer =&gt; {
  setInterval(() =&gt; {
    observer.next();
  }, 2000);
});</code></pre>
</div>

Y nos suscribimos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  this.title$.subscribe(this.setTitle);
}</code></pre>
</div>

> ⭐ Por convención, las variables observables suelen terminar en `$`.

> ⚠️ Un observable no ejecuta su lógica mientras nadie se suscriba.

---

# 8. Programación reactiva

La programación reactiva se basa en:

- observables;
- observers;
- una línea temporal;
- streams de eventos;
- operadores combinables.

Un ejemplo sencillo es el flujo de eventos de teclado:


<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">keyup ── keyup ───── keyup ──► tiempo
  A        n             g</code></pre>
</div>

La aplicación no controla cuándo pulsa el usuario; simplemente reacciona al stream.

---

# 9. Componente `KeyLogger`

Generamos:


<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">ng generate component key-logger</code></pre>
</div>

Plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;input
  type="text"
  #keyContainer
/&gt;
&#8203;
You pressed: {{ keys }}</code></pre>
</div>

En el componente importamos `ElementRef`, `OnInit` y `viewChild`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  ElementRef,
  OnInit,
  viewChild
} from '@angular/core';</code></pre>
</div>

Y definimos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">input =
  viewChild&lt;ElementRef&gt;('keyContainer');
&#8203;
keys = '';</code></pre>
</div>

`ElementRef` actúa como wrapper del elemento HTML nativo.

---

# 10. `fromEvent`

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { fromEvent } from 'rxjs';</code></pre>
</div>

Creamos un observable desde `keyup`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class KeyLoggerComponent
  implements OnInit {
&#8203;
  input =
    viewChild&lt;ElementRef&gt;('keyContainer');
&#8203;
  keys = '';
&#8203;
  ngOnInit(): void {
    const logger$ =
      fromEvent&lt;KeyboardEvent&gt;(
        this.input()!.nativeElement,
        'keyup'
      );
&#8203;
    logger$.subscribe(
      evt =&gt; this.keys += evt.key
    );
  }
}</code></pre>
</div>

`fromEvent` convierte eventos DOM en un observable.

---

# 11. Integrar `KeyLoggerComponent`

La plantilla raíz incluye:


<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-key-logger&gt;&lt;/app-key-logger&gt;</code></pre>
</div>

Al escribir, `keys` se actualiza con cada evento `keyup`.

---

# 12. RxJS

RxJS es la implementación JavaScript de ReactiveX y permite crear observables a partir de:

- eventos;
- promesas;
- callbacks;
- arrays;
- valores.

Además proporciona operadores para transformar, filtrar y combinar streams.

---

# 13. Crear observables con `of`



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { of } from 'rxjs';
&#8203;
const values = of(1, 2, 3);
&#8203;
values.subscribe(
  value =&gt; console.log(value)
);</code></pre>
</div>

Salida:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">1
2
3</code></pre>
</div>

---

# 14. Crear observables con `from`

`from` convierte arrays, promesas u otros valores compatibles en observables:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { from } from 'rxjs';
&#8203;
const values =
  from([1, 2, 3]);
&#8203;
values.subscribe(
  value =&gt; console.log(value)
);</code></pre>
</div>

También puede envolver una promesa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  const complete$ =
    from(this.onComplete());
&#8203;
  complete$.subscribe(
    this.setTitle
  );
}</code></pre>
</div>

> 💡 `from` resulta especialmente útil para migrar código basado en promesas.

---

# 15. `pipe` y `tap`

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  fromEvent,
  tap
} from 'rxjs';</code></pre>
</div>

Y encadenamos operadores:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  const logger$ =
    fromEvent&lt;KeyboardEvent&gt;(
      this.input()!.nativeElement,
      'keyup'
    );
&#8203;
  logger$.pipe(
    tap(
      evt =&gt; this.keys += evt.key
    )
  ).subscribe();
}</code></pre>
</div>

`tap` ejecuta un efecto sin transformar el valor emitido.

---

# 16. `map`

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  fromEvent,
  tap,
  map
} from 'rxjs';</code></pre>
</div>

Transformamos cada evento en el código Unicode:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">map(
  evt =&gt; evt.key.charCodeAt(0)
)</code></pre>
</div>

---

# 17. `filter`

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  fromEvent,
  tap,
  map,
  filter
} from 'rxjs';</code></pre>
</div>

Filtramos caracteres no numéricos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">filter(
  code =&gt;
    (
      code &gt; 31 &amp;&amp;
      (code &lt; 48 || code &gt; 57)
    ) === false
)</code></pre>
</div>

Y convertimos el código otra vez a carácter:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">tap(
  digit =&gt;
    this.keys +=
      String.fromCharCode(digit)
)</code></pre>
</div>

---

# 18. Activar el filtrado mediante `input()`

Añadimos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">numeric = input(false);</code></pre>
</div>

Y hacemos el filtro condicional:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">filter(code =&gt; {
  if (this.numeric()) {
    return (
      code &gt; 31 &amp;&amp;
      (code &lt; 48 || code &gt; 57)
    ) === false;
  }
&#8203;
  return true;
})</code></pre>
</div>

El método completo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  const logger$ =
    fromEvent&lt;KeyboardEvent&gt;(
      this.input()!.nativeElement,
      'keyup'
    );
&#8203;
  logger$.pipe(
    map(
      evt =&gt; evt.key.charCodeAt(0)
    ),
    filter(code =&gt; {
      if (this.numeric()) {
        return (
          code &gt; 31 &amp;&amp;
          (code &lt; 48 || code &gt; 57)
        ) === false;
      }
&#8203;
      return true;
    }),
    tap(
      digit =&gt;
        this.keys +=
          String.fromCharCode(digit)
    )
  ).subscribe();
}</code></pre>
</div>

Desde el padre:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-key-logger
  [numeric]="true"
&gt;&lt;/app-key-logger&gt;</code></pre>
</div>

---

# 19. Convertir `ProductsService` a observables

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Observable,
  of
} from 'rxjs';</code></pre>
</div>

Los productos pasan a una propiedad privada:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private products: Product[] = [
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

Y `getProducts()` devuelve un observable:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts():
  Observable&lt;Product[]&gt; {
&#8203;
  return of(this.products);
}</code></pre>
</div>

---

# 20. Suscribirse desde `ProductListComponent`



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.productService
    .getProducts()
    .subscribe(products =&gt; {
      this.products = products;
    });
}</code></pre>
</div>

En `ngOnInit`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnInit(): void {
  this.getProducts();
}</code></pre>
</div>

> ✅ El material recomienda mantener los hooks de ciclo de vida lo más claros y concisos posible.

---

# 21. Cancelar suscripciones

Una suscripción activa reserva recursos. Si el componente se destruye y la suscripción continúa viva, puede producirse una fuga de memoria.

## Cancelación manual

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Subscription
} from 'rxjs';</code></pre>
</div>

Guardamos la suscripción:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private productsSub:
  Subscription | undefined;</code></pre>
</div>

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.productsSub =
    this.productService
      .getProducts()
      .subscribe(products =&gt; {
        this.products = products;
      });
}</code></pre>
</div>

Y cancelamos en `ngOnDestroy`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnDestroy(): void {
  this.productsSub
    ?.unsubscribe();
}</code></pre>
</div>

---

# 22. `takeUntilDestroyed`

Podemos simplificar la gestión utilizando:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Component,
  DestroyRef,
  inject,
  OnInit
} from '@angular/core';
&#8203;
import {
  takeUntilDestroyed
} from '@angular/core/rxjs-interop';</code></pre>
</div>

Inyectamos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private destroyRef =
  inject(DestroyRef);</code></pre>
</div>

Y aplicamos el operador:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.productService
    .getProducts()
    .pipe(
      takeUntilDestroyed(
        this.destroyRef
      )
    )
    .subscribe(products =&gt; {
      this.products = products;
    });
}</code></pre>
</div>

`takeUntilDestroyed` cancela la suscripción cuando se destruye el componente.

---

# 23. Pipe `async`

El pipe `async`:

- se suscribe automáticamente;
- entrega el valor emitido;
- se desuscribe cuando el componente se destruye.

Importamos:


<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  AsyncPipe
} from '@angular/common';
&#8203;
import {
  Observable
} from 'rxjs';</code></pre>
</div>

Añadimos `AsyncPipe`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Component({
  selector: 'app-product-list',
  imports: [
    ProductDetailComponent,
    SortPipe,
    AsyncPipe
  ],
  templateUrl:
    './product-list.component.html',
  styleUrl:
    './product-list.component.css'
})</code></pre>
</div>

La propiedad pasa a ser:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products$:
  Observable&lt;Product[]&gt; | undefined;</code></pre>
</div>

El método queda:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts() {
  this.products$ =
    this.productService.getProducts();
}</code></pre>
</div>

Y en la plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let products =
  (products$ | async)!;</code></pre>
</div>

> ✅ Con `async` ya no es necesario gestionar manualmente `subscribe()` y `unsubscribe()`.

---

# 24. Comparativa

| Característica | Callback | Promise | Observable |
|---|---:|---:|---:|
| Múltiples valores | Depende | ❌ | ✅ |
| Cancelable | Manual | ❌ | ✅ |
| Lazy | Depende | ❌ | ✅ |
| Operadores de transformación | ❌ | Limitados | ✅ |
| Encadenamiento | Difícil | ✅ | ✅ |
| Integración con Angular | Baja | Media | Alta |

---

# 25. Esquema general de RxJS



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Fuente de datos
     │
     ▼
 Observable
     │
     ├── map
     ├── filter
     ├── tap
     └── otros operadores
     │
     ▼
 Subscriber
     │
     ▼
 Estado / interfaz</code></pre>
</div>

---

# 26. Resumen

En esta unidad hemos aprendido:

- cómo manejar asincronía;
- callbacks y callback hell;
- promesas y sus limitaciones;
- observables y programación reactiva;
- RxJS;
- `Observable`, `fromEvent`, `of` y `from`;
- `pipe`, `tap`, `map` y `filter`;
- suscripciones;
- cancelación manual;
- `takeUntilDestroyed`;
- pipe `async`.

---

# 27. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Callback | Función ejecutada al completarse otra operación |
| Promise | Operación asíncrona de un único resultado |
| Observable | Stream que puede emitir múltiples valores |
| Observer | Consumidor de un observable |
| Subscription | Relación activa entre observable y observer |
| RxJS | Librería reactiva utilizada por Angular |
| `subscribe()` | Inicia el consumo del observable |
| `unsubscribe()` | Cancela la suscripción |
| `fromEvent()` | Convierte eventos DOM en observables |
| `of()` | Crea un observable desde valores |
| `from()` | Convierte arrays o promesas en observables |
| `pipe()` | Encadena operadores |
| `tap()` | Ejecuta efectos sin transformar |
| `map()` | Transforma valores |
| `filter()` | Filtra valores |
| `takeUntilDestroyed()` | Cancela al destruirse el componente |
| `async` | Pipe para suscripción automática |
| `$` | Convención habitual para variables observables |

---

## 🔗 Recursos relacionados con el contenido

- RxJS: <https://rxjs.dev>
- RxJS interop en Angular: <https://angular.dev/ecosystem/rxjs-interop>

---

> 🎓 **Siguiente paso**  
> El siguiente patrón reactivo que aborda el material es **Signals**, integrado directamente en Angular para gestionar estado reactivo de forma síncrona.

---

# 30. Taller práctico: Promesas, Observables y RxJS 🧪

Los ejercicios de esta sección toman como referencia el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/06_promesas_y_observables>

El estado actual del repositorio resulta especialmente útil para esta unidad porque ya contiene:

- un `AppComponent` con un `Observable` que emite cada dos segundos;
- un `ProductsService` que devuelve `Observable<Product[]>` mediante `of(...)`;
- un `ProductListComponent` que expone `products$`;
- uso de `AsyncPipe` en la plantilla;
- un `KeyLoggerComponent` construido con `fromEvent`;
- una cadena RxJS con `map`, `filter` y `tap`.

> 🎯 **Forma de trabajo recomendada**  
> Ejecuta cada ejercicio de forma incremental. Antes de modificar el código, intenta predecir qué valores emitirá cada observable, cuándo se ejecutará una suscripción y quién será responsable de cancelarla.

---

## Ejercicio 1. Identifica qué partes del proyecto son reactivas

### Objetivo

Reconocer observables, suscriptores y consumidores en el código real.

### Tareas

Abre:

- `app.component.ts`;
- `products.service.ts`;
- `product-list.component.ts`;
- `product-list.component.html`;
- `key-logger.component.ts`.

Completa una tabla como esta:

| Archivo                     | Observable                 | Qué emite | Quién lo consume |
| --------------------------- | -------------------------- | --------- | ---------------- |
| `app.component.ts`          | `title$`                   |           |                  |
| `products.service.ts`       | retorno de `getProducts()` |           |                  |
| `product-list.component.ts` | `products$`                |           |                  |
| `key-logger.component.ts`   | `logger$`                  |           |                  |

### Pregunta

¿Cuáles de estos observables emiten una sola vez y cuáles pueden emitir varias veces?

---

## Ejercicio 2. Observa el carácter *lazy* de un observable

### Objetivo

Comprobar que un observable no ejecuta su lógica hasta que existe una suscripción.

En `AppComponent` el repositorio contiene un observable equivalente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ = new Observable(observer =&gt; {
  setInterval(() =&gt; {
    observer.next();
  }, 2000);
});</code></pre>
</div>


### Tareas

1. Añade un `console.log` dentro del constructor del observable:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ = new Observable(observer =&gt; {
  console.log('Observable activado');
&#8203;
  setInterval(() =&gt; {
    observer.next();
  }, 2000);
});</code></pre>
</div>


2. Comenta temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.title$.subscribe(this.setTitle);</code></pre>
</div>


3. Recarga la aplicación.
4. Comprueba si aparece el mensaje.
5. Restaura la suscripción y vuelve a probar.

### Explica

¿Por qué decimos que los observables son **lazy**?

---

## Ejercicio 3. Cuenta las emisiones de `title$`

### Objetivo

Comprender que un observable puede generar una secuencia de valores.

Modifica `title$` para emitir un contador:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ = new Observable&lt;number&gt;(observer =&gt; {
  let counter = 0;
&#8203;
  setInterval(() =&gt; {
    counter++;
    observer.next(counter);
  }, 2000);
});</code></pre>
</div>


Suscríbete:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  this.title$.subscribe(value =&gt; {
    console.log('Emisión:', value);
  });
}</code></pre>
</div>


### Tareas

1. Espera varias emisiones.
2. Anota la secuencia.
3. Comprueba que no es una operación de un único resultado.
4. Compara este comportamiento con una `Promise<number>`.

---

## Ejercicio 4. De observable a actualización de título

### Objetivo

Utilizar el valor emitido, en vez de emitir únicamente una notificación.

Haz que el propio observable emita el texto que debe mostrarse:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ = new Observable&lt;string&gt;(observer =&gt; {
  setInterval(() =&gt; {
    const timestamp = new Date();
&#8203;
    observer.next(
      `${this.settings.title} (${timestamp})`
    );
  }, 2000);
});</code></pre>
</div>


Después:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor() {
  this.title$.subscribe(title =&gt; {
    this.title = title;
  });
}</code></pre>
</div>


### Reflexión

¿Qué diseño te parece más expresivo?

- emitir una señal vacía y calcular el título en el suscriptor;
- emitir directamente el nuevo título.

Justifica tu respuesta.

---

## Ejercicio 5. Convierte una promesa en observable con `from`

### Objetivo

Practicar la migración desde promesas.

Crea temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private onComplete(): Promise&lt;string&gt; {
  return new Promise(resolve =&gt; {
    setTimeout(() =&gt; {
      resolve('Operación completada');
    }, 1500);
  });
}</code></pre>
</div>


Convierte la promesa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { from } from 'rxjs';
&#8203;
const complete$ = from(this.onComplete());
&#8203;
complete$.subscribe(message =&gt; {
  console.log(message);
});</code></pre>
</div>


### Tareas

1. Ejecuta el código.
2. Comprueba cuántas veces emite.
3. Explica por qué `from(Promise)` sigue teniendo comportamiento de un solo resultado aunque ahora sea un observable.

---

## Ejercicio 6. Compara `of` y `from`

### Objetivo

Entender una diferencia fundamental entre ambos creadores de observables.

Prueba:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { of, from } from 'rxjs';
&#8203;
of([1, 2, 3]).subscribe(value =&gt; {
  console.log('of:', value);
});
&#8203;
from([1, 2, 3]).subscribe(value =&gt; {
  console.log('from:', value);
});</code></pre>
</div>


### Tareas

1. Predice la salida antes de ejecutar.
2. Ejecuta.
3. Explica por qué `of([1, 2, 3])` y `from([1, 2, 3])` no generan la misma secuencia.

### Relación con el repositorio

`ProductsService` utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return of(this.products);</code></pre>
</div>


¿Por qué esta elección es adecuada si queremos emitir el array completo como una sola unidad?

---

## Ejercicio 7. Añade retraso conceptual al servicio

### Objetivo

Simular que los productos proceden de una fuente asíncrona.

Sin añadir todavía operadores nuevos, crea un observable manual:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts(): Observable&lt;Product[]&gt; {
  return new Observable(observer =&gt; {
    setTimeout(() =&gt; {
      observer.next(this.products);
      observer.complete();
    }, 1500);
  });
}</code></pre>
</div>


### Tareas

1. Observa la interfaz al cargar.
2. Comprueba cuándo aparece el listado.
3. Añade mensajes:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">console.log('Petición iniciada');
console.log('Productos emitidos');</code></pre>
</div>


4. Determina en qué orden se muestran.

### Pregunta

¿Qué similitud tiene esta simulación con una futura petición HTTP?

---

## Ejercicio 8. Analiza el uso de `AsyncPipe`

### Objetivo

Comprender la relación entre el observable del componente y la plantilla.

En el repositorio existe:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products$:
  Observable&lt;Product[]&gt; | undefined;</code></pre>
</div>


y la plantilla contiene:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@let products =
  (products$ | async)!;</code></pre>
</div>


### Tareas

1. Explica qué hace `async`.
2. Explica qué valor termina almacenado en la variable local `products`.
3. Identifica por qué ya no existe un `subscribe()` en `ProductListComponent`.
4. Explica quién cancela la suscripción cuando el componente se destruye.

---

## Ejercicio 9. Vuelve temporalmente a una suscripción imperativa

### Objetivo

Comparar suscripción manual y `AsyncPipe`.

Sustituye temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products$:
  Observable&lt;Product[]&gt; | undefined;</code></pre>
</div>


por:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products: Product[] = [];</code></pre>
</div>


y cambia `getProducts()`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private getProducts(): void {
  this.productService
    .getProducts()
    .subscribe(products =&gt; {
      this.products = products;
    });
}</code></pre>
</div>


Quita el `async` de la plantilla.

### Tareas

1. Comprueba que la aplicación sigue funcionando.
2. Compara la cantidad de código.
3. Anota quién gestiona ahora la suscripción.
4. Restaura después la versión con `AsyncPipe`.

### Conclusión

¿Cuál de las dos versiones resulta más declarativa?

---

## Ejercicio 10. Suscripción manual y `unsubscribe()`

### Objetivo

Practicar la cancelación explícita de una suscripción.

Partiendo temporalmente de la versión imperativa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private productsSub:
  Subscription | undefined;</code></pre>
</div>


Guarda la suscripción:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.productsSub =
  this.productService
    .getProducts()
    .subscribe(products =&gt; {
      this.products = products;
    });</code></pre>
</div>


Y cancela en:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngOnDestroy(): void {
  this.productsSub?.unsubscribe();
}</code></pre>
</div>


### Preguntas

1. ¿Qué objeto devuelve `subscribe()`?
2. ¿Para qué sirve `unsubscribe()`?
3. ¿Por qué esta estrategia puede resultar incómoda cuando existen muchas suscripciones?

---

## Ejercicio 11. Sustituye el `unsubscribe()` manual por `takeUntilDestroyed`

### Objetivo

Aplicar el mecanismo moderno explicado en la unidad.

Importa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  DestroyRef,
  inject
} from '@angular/core';
&#8203;
import {
  takeUntilDestroyed
} from '@angular/core/rxjs-interop';</code></pre>
</div>


Declara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private readonly destroyRef =
  inject(DestroyRef);</code></pre>
</div>


Y utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.productService
  .getProducts()
  .pipe(
    takeUntilDestroyed(
      this.destroyRef
    )
  )
  .subscribe(products =&gt; {
    this.products = products;
  });</code></pre>
</div>


### Tareas

1. Elimina la propiedad `Subscription`.
2. Elimina `ngOnDestroy`.
3. Comprueba que el código compila.
4. Explica qué evento provoca la cancelación.

---

## Ejercicio 12. Integra `KeyLoggerComponent` en la aplicación

### Objetivo

Poner en uso un componente que ya existe en el repositorio.

El repositorio contiene `KeyLoggerComponent`, pero el `AppComponent` actual no lo muestra en su plantilla.

### Tareas

1. Importa `KeyLoggerComponent` en `AppComponent`.
2. Añádelo al array `imports`.
3. Añade:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-key-logger&gt;&lt;/app-key-logger&gt;</code></pre>
</div>


4. Escribe texto.
5. Comprueba el valor acumulado de `keys`.

### Pregunta

¿Qué evento DOM se ha convertido en observable?

---

## Ejercicio 13. Activa el modo numérico del key logger

### Objetivo

Practicar un `input()` que modifica la lógica del stream.

Utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;app-key-logger
  [numeric]="true"
&gt;&lt;/app-key-logger&gt;</code></pre>
</div>


### Tareas

Prueba escribir:

```text
Angular 19
```

Comprueba qué caracteres aparecen.

Después cambia:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">[numeric]="false" </code></pre>
</div>


y vuelve a probar.

### Explica

¿Por qué el componente no necesita reconstruir el observable para decidir si acepta letras?

---

## Ejercicio 14. Sigue el tipo de dato por el pipeline

### Objetivo

Comprender cómo `map` transforma el tipo emitido.

En el repositorio:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">fromEvent&lt;KeyboardEvent&gt;(
  this.input()!.nativeElement,
  'keyup'
)
.pipe(
  map(
    evt =&gt; evt.key.charCodeAt(0)
  ),
  filter(code =&gt; {
    // ...
  }),
  tap(digit =&gt; {
    // ...
  })
);</code></pre>
</div>


Completa:

| Punto del pipeline | Tipo del valor |
|---|---|
| Antes de `map` | `KeyboardEvent` |
| Después de `map` |  |
| Después de `filter` |  |
| Dentro de `tap` |  |

### Pregunta

¿`filter` transforma el tipo de dato o solo decide si un valor continúa por el stream?

---

## Ejercicio 15. Investiga `tap`

### Objetivo

Distinguir transformación y efecto lateral.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">tap(value =&gt; {
  console.log(
    'Valor que atraviesa tap:',
    value
  );
})</code></pre>
</div>


### Tareas

1. Comprueba el valor recibido.
2. Mueve el `tap` antes de `map`.
3. Comprueba qué tipo de dato recibe ahora.
4. Explica por qué `tap` no sustituye a `map`.

---

## Ejercicio 16. Filtra únicamente números pares

### Objetivo

Practicar `filter` con una secuencia controlada.

Crea en un método de prueba:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  from,
  filter
} from 'rxjs';
&#8203;
const numbers$ =
  from([1, 2, 3, 4, 5, 6]);
&#8203;
numbers$
  .pipe(
    filter(
      value =&gt; value % 2 === 0
    )
  )
  .subscribe(value =&gt; {
    console.log(value);
  });</code></pre>
</div>


### Resultado esperado

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">2
4
6</code></pre>
</div>


Después modifica el filtro para conservar únicamente valores mayores que `3`.

---

## Ejercicio 17. Transforma números con `map`

### Objetivo

Practicar la transformación de un stream.

Partiendo del ejercicio anterior:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">numbers$
  .pipe(
    map(value =&gt; value * 10)
  )
  .subscribe(value =&gt; {
    console.log(value);
  });</code></pre>
</div>


### Tareas

1. Predice la salida.
2. Ejecuta.
3. Combina `filter` y `map`.
4. Comprueba cómo influye el orden:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">filter → map
map → filter</code></pre>
</div>


### Reflexión

¿Por qué el orden de los operadores puede cambiar el resultado?

---

## Ejercicio 18. Construye un pipeline completo

### Objetivo

Aplicar `map`, `filter` y `tap` juntos.

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">from([1, 2, 3, 4, 5, 6])
  .pipe(
    filter(value =&gt; value % 2 === 0),
    map(value =&gt; value * 100),
    tap(value =&gt;
      console.log(
        'Procesado:',
        value
      )
    )
  )
  .subscribe();</code></pre>
</div>


### Tareas

1. Describe cada fase.
2. Indica qué operador modifica los datos.
3. Indica cuál descarta valores.
4. Indica cuál solo observa.

---

## Ejercicio 19. Dos suscripciones al mismo observable

### Objetivo

Observar qué sucede cuando un observable tiene varios subscribers.

Sobre `title$` añade dos suscripciones:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.title$.subscribe(value =&gt; {
  console.log(
    'Suscriptor A:',
    value
  );
});
&#8203;
this.title$.subscribe(value =&gt; {
  console.log(
    'Suscriptor B:',
    value
  );
});</code></pre>
</div>


### Tareas

1. Observa la consola.
2. Añade un `console.log('Inicio observable')` dentro de la función que crea el observable.
3. Comprueba cuántas veces se ejecuta esa lógica.

### Pregunta

¿Qué deduces sobre la ejecución del observable utilizado en este ejemplo?

---

## Ejercicio 20. Añade teardown al observable manual

### Objetivo

Comprender que un observable puede liberar los recursos que crea.

El `title$` del repositorio utiliza `setInterval`.

Refactorízalo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ = new Observable&lt;void&gt;(
  observer =&gt; {
    const timer = setInterval(() =&gt; {
      observer.next();
    }, 2000);
&#8203;
    return () =&gt; {
      clearInterval(timer);
&#8203;
      console.log(
        'Intervalo eliminado'
      );
    };
  }
);</code></pre>
</div>


### Tareas

1. Guarda la `Subscription`.
2. Cancélala después de unos segundos.
3. Comprueba que dejan de producirse emisiones.
4. Comprueba que se ejecuta la función de teardown.

### Idea clave

Una suscripción no solo recibe datos: también puede representar recursos que deben liberarse.

---

## Ejercicio 21. Convierte `title$` en una propiedad consumida con `AsyncPipe`

### Objetivo

Aplicar en `AppComponent` el mismo enfoque declarativo utilizado por `ProductListComponent`.

Haz que el observable emita directamente textos:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title$ =
  new Observable&lt;string&gt;(
    observer =&gt; {
      setInterval(() =&gt; {
        observer.next(
          `${this.settings.title} ` +
          `(${new Date()})`
        );
      }, 2000);
    }
  );</code></pre>
</div>


Importa `AsyncPipe` en `AppComponent` y utiliza:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;header&gt;
  {{ title$ | async }}
&lt;/header&gt;</code></pre>
</div>


### Tareas

1. Elimina la suscripción manual del constructor.
2. Comprueba que el título sigue actualizándose.
3. Explica quién gestiona ahora la suscripción.

---

# 31. Reto integrador: panel reactivo de productos 🚀

### Objetivo

Aplicar de forma conjunta los conceptos de la unidad sobre el código real del repositorio.

Construye una pequeña evolución del proyecto con los siguientes requisitos.

## Parte 1. Productos como observable

Mantén:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">getProducts():
  Observable&lt;Product[]&gt;</code></pre>
</div>


en `ProductsService`.

El componente debe trabajar preferentemente con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products$:
  Observable&lt;Product[]&gt; | undefined;</code></pre>
</div>


y `AsyncPipe`.

## Parte 2. Estado de carga

Añade una propiedad:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">loading = true;</code></pre>
</div>


y simula que los datos tardan en llegar mediante un observable con `setTimeout`.

Cuando los productos se reciban, actualiza el estado.

La plantilla debe mostrar:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (loading) {
  &lt;p&gt;Cargando productos...&lt;/p&gt;
} @else {
  &lt;!-- listado --&gt;
}</code></pre>
</div>


## Parte 3. Key logger

Integra `KeyLoggerComponent` y permite alternar entre:

- entrada libre;
- solo números.

## Parte 4. Pipeline RxJS

Crea un observable de prueba que:

1. parta de `[1, 2, 3, 4, 5, 6]`;
2. descarte impares;
3. multiplique los pares por `10`;
4. muestre cada valor con `tap`.

## Parte 5. Gestión de suscripciones

La solución debe demostrar al menos dos enfoques:

- una suscripción gestionada automáticamente con `AsyncPipe`;
- una suscripción imperativa protegida mediante `takeUntilDestroyed` o cancelada explícitamente.

## Checklist

- [ ] Se utiliza al menos un `Observable`.
- [ ] Se utiliza `of()` o `from()`.
- [ ] Se utiliza `fromEvent()`.
- [ ] Se utiliza `pipe()`.
- [ ] Se utiliza `map()`.
- [ ] Se utiliza `filter()`.
- [ ] Se utiliza `tap()`.
- [ ] Existe al menos una suscripción explícita.
- [ ] La suscripción explícita se limpia correctamente.
- [ ] Existe al menos un uso de `AsyncPipe`.
- [ ] Las variables observables terminan en `$`.
- [ ] Se distingue correctamente entre `Product[]` y `Observable<Product[]>`.
- [ ] El proyecto compila y funciona sin errores.

---

# 32. Diagnóstico de errores reactivos 🔎

Analiza cada caso antes de corregirlo.

## Caso A. Observable sin suscripción

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const values$ =
  new Observable(observer =&gt; {
    console.log('Ejecutando');
    observer.next(1);
  });</code></pre>
</div>


No existe `subscribe()` ni `async`.

**Pregunta:** ¿se ejecutará el `console.log`?

---

## Caso B. Confundir observable y valor

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">products:
  Product[] =
    this.productService
      .getProducts();</code></pre>
</div>


`getProducts()` devuelve `Observable<Product[]>`.

**Pregunta:** ¿por qué los tipos no son compatibles?

---

## Caso C. `subscribe()` olvidado

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.productService
  .getProducts()
  .pipe(
    tap(products =&gt;
      console.log(products)
    )
  );</code></pre>
</div>


**Pregunta:** ¿por qué no se imprime nada?

---

## Caso D. Suscripción sin limpiar

Un componente se suscribe a un stream infinito creado con `setInterval`, pero nunca ejecuta `unsubscribe()`.

**Pregunta:** ¿qué problema puede aparecer al destruir y volver a crear el componente?

---

## Caso E. Usar `map` para un efecto lateral

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">map(value =&gt; {
  console.log(value);
  return value;
})</code></pre>
</div>


**Pregunta:** ¿qué operador expresa mejor que solo queremos observar el dato sin transformarlo?

---

## Caso F. `filter` que nunca deja pasar valores

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">filter(() =&gt; false)</code></pre>
</div>


**Pregunta:** ¿se ejecutará el `subscribe` para algún valor?

---

## Caso G. `AsyncPipe` no importado

La plantilla usa:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">{{ products$ | async }}</code></pre>
</div>


pero el componente standalone no incluye `AsyncPipe` en `imports`.

**Pregunta:** ¿qué debe corregirse?

---

# 33. Cuestiones finales de reflexión 📝

1. ¿Qué diferencia fundamental existe entre una promesa y un observable?
2. ¿Qué significa que un observable sea *lazy*?
3. ¿Qué diferencia existe entre `of([1,2,3])` y `from([1,2,3])`?
4. ¿Qué papel desempeña `subscribe()`?
5. ¿Qué devuelve una llamada a `subscribe()`?
6. ¿Qué representa una `Subscription`?
7. ¿Por qué un observable puede necesitar lógica de teardown?
8. ¿Qué ventaja ofrece `AsyncPipe`?
9. ¿Qué resuelve `takeUntilDestroyed`?
10. ¿Cuándo utilizarías una suscripción explícita en vez de `AsyncPipe`?
11. ¿Qué convierte `fromEvent()` en observable?
12. ¿Qué diferencia hay entre `map` y `tap`?
13. ¿Qué hace `filter`?
14. ¿Por qué importa el orden de los operadores dentro de `pipe()`?
15. ¿Qué tipo emite el `KeyLoggerComponent` antes de aplicar `map`?
16. ¿Qué tipo emite después?
17. ¿Por qué conviene terminar los nombres de observables con `$`?
18. ¿Qué ventaja ofrece que `ProductsService` devuelva observables pensando en futuras peticiones HTTP?
19. ¿Quién debe responsabilizarse de cancelar una suscripción manual?
20. ¿Qué estilo te parece más declarativo: `subscribe()` en el componente o `async` en la plantilla? Justifica tu respuesta.

---

> ✅ **Meta de aprendizaje**  
> Si puedes explicar qué valores atraviesan un pipeline RxJS, cuándo se ejecuta un observable, quién mantiene una suscripción y cómo se libera, ya tienes una base sólida para trabajar con programación reactiva en Angular.
