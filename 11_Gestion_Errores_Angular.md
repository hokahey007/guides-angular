# 🚨 Unidad 11: Gestión de errores en aplicaciones Angular


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
> **Objetivo:** aprender a detectar, clasificar y gestionar errores de ejecución y errores del propio framework Angular, prestando especial atención a peticiones HTTP, gestión global de errores, interceptores y códigos `NG...`.


---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Diferenciar errores de ejecución y errores de desarrollo.
- Identificar errores originados en peticiones HTTP.
- Capturar errores HTTP con `catchError`.
- Trabajar con `HttpErrorResponse`.
- Diferenciar errores por código de estado HTTP.
- Utilizar `HttpStatusCode`.
- Reemitir errores con `throwError`.
- Reintentar peticiones con `retry`.
- Crear un manejador global mediante `ErrorHandler`.
- Registrar un manejador global personalizado.
- Gestionar de forma específica errores `401 Unauthorized`.
- Interrumpir un stream mediante `EMPTY`.
- Comprender el formato de errores `NG...` de Angular.
- Interpretar el error `NG0100: ExpressionChangedAfterItHasBeenCheckedError`.

---

## 📑 Contenidos

1. [Errores en tiempo de ejecución](#2-errores-en-tiempo-de-ejecución)
2. [Capturar y gestionar errores HTTP](#3-capturar-errores-http-con-catcherror)
3. [Reintentos y gestión por servicio](#10-reintentar-una-petición-con-retry)
4. [Manejador global con ErrorHandler](#12-errorhandler)
5. [Errores 401 e interceptores](#18-error-401-unauthorized)
6. [Errores del framework Angular](#22-errores-del-framework-angular)
7. [Estrategia y arquitectura de errores](#29-estrategia-general-de-tratamiento-de-errores)
8. [Taller práctico](#34-taller-práctico-gestión-de-errores-sobre-el-proyecto-real)
9. [Reto integrador](#35-reto-integrador-arquitectura-de-errores-de-la-tienda)
10. [Diagnóstico y reflexión](#36-diagnóstico-de-errores)

---

# 1. Los errores forman parte del ciclo de vida de una aplicación

Los errores pueden aparecer tanto:

- durante la ejecución;
- durante el desarrollo.

Un error de ejecución puede deberse, por ejemplo, a:

- una petición HTTP fallida;
- credenciales incorrectas;
- datos enviados con formato incorrecto;
- problemas de red;
- un formulario incompleto.

Un error de desarrollo suele aparecer cuando utilizamos incorrectamente:

- TypeScript;
- Angular;
- una API;
- una característica del framework.

> 🧠 **Idea clave**  
> Una aplicación robusta no pretende que nunca ocurra un error, sino que debe detectarlo, clasificarlo y reducir sus efectos sobre la experiencia del usuario.

---

# 2. Errores en tiempo de ejecución

Una de las fuentes más habituales de errores en Angular es la comunicación con una API HTTP.

Ejemplos:

```text
Usuario introduce credenciales
        │
        ▼
POST /login
        │
        ├── 200 → correcto
        │
        └── 401 → error
```

o:

```text
Angular
   │
   ▼
GET /products
   │
   ├── respuesta
   ├── error de servidor
   └── error de red
```

El material plantea tres niveles para gestionar errores HTTP:

1. directamente en una petición concreta;
2. globalmente mediante el manejador de errores de la aplicación;
3. centralmente mediante un interceptor HTTP.

---

# 3. Capturar errores HTTP con `catchError`

RxJS proporciona el operador:

`catchError`

Este operador puede utilizarse dentro de un `pipe()` para capturar errores emitidos por un observable.

En `ProductsService` importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  Observable,
  map,
  of,
  tap,
  catchError,
  throwError
} from 'rxjs';</code></pre>
</div>



También necesitamos `HttpErrorResponse`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpClient,
  HttpParams,
  HttpErrorResponse
} from '@angular/common/http';</code></pre>
</div>



---

# 4. Capturar el error en `getProducts()`

Ejemplo:



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
        }),
&#8203;
        catchError(
          (
            error:
              HttpErrorResponse
          ) =&gt; {
            console.error(
              error
            );
&#8203;
            return throwError(
              () =&gt; error
            );
          }
        )
      );
  }
&#8203;
  return of(
    this.products
  );
}</code></pre>
</div>



`catchError` recibe el objeto `HttpErrorResponse`.

Después de registrar el error, el ejemplo utiliza:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return throwError(
  () =&gt; error
);</code></pre>
</div>



para volver a emitirlo como error dentro de un observable.

> 💡 **Nota del material**  
> El PDF señala que también podría utilizarse `throw` en determinados casos y advierte de que `throwError` puede resultar excesivo si se utiliza sin necesidad. Debe elegirse según el escenario.

---

# 5. `HttpErrorResponse`

`HttpErrorResponse` contiene información sobre el fallo de una petición HTTP.

Entre los datos relevantes podemos encontrar:

- `status`;
- `statusText`;
- `message`;
- `url`;
- `error`;
- `headers`.

Conceptualmente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">HttpErrorResponse
├── status
├── statusText
├── message
├── url
├── error
└── headers</code></pre>
</div>



El código de estado nos permite clasificar errores.

---

# 6. `HttpStatusCode`

Angular proporciona una enumeración con códigos HTTP:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpClient,
  HttpParams,
  HttpErrorResponse,
  HttpStatusCode
} from '@angular/common/http';</code></pre>
</div>



Esto permite escribir código más expresivo que utilizar directamente números como `400` o `500`.

---

# 7. Crear un método `handleError`

Podemos centralizar dentro del servicio la clasificación:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private handleError(
  error: HttpErrorResponse
) {
  let message = '';
&#8203;
  switch (
    error.status
  ) {
    case
      HttpStatusCode
        .InternalServerError:
&#8203;
      message =
        'Server error';
&#8203;
      break;
&#8203;
    case
      HttpStatusCode
        .BadRequest:
&#8203;
      message =
        'Request error';
&#8203;
      break;
&#8203;
    default:
      message =
        'Unknown error';
  }
&#8203;
  console.error(
    message,
    error.error
  );
&#8203;
  return throwError(
    () =&gt; error
  );
}</code></pre>
</div>



Casos tratados:

| Código | Interpretación del ejemplo |
|---|---|
| `500` | Error de servidor |
| `400` | Error de petición |
| Otros | Error desconocido |

---

# 8. Reutilizar `handleError`

El método `getProducts()` queda más limpio:



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
        }),
&#8203;
        catchError(
          this.handleError
        )
      );
  }
&#8203;
  return of(
    this.products
  );
}</code></pre>
</div>



---

# 9. Errores de cliente y código `0`

No todos los fallos HTTP proceden del servidor.

Puede producirse un error antes de que la petición llegue correctamente al backend.

Ejemplos:

- sin conexión;
- error de red;
- fallo en el cliente;
- excepción en una operación RxJS.

El material utiliza:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private handleError(
  error: HttpErrorResponse
) {
  let message = '';
&#8203;
  switch (
    error.status
  ) {
    case 0:
      message =
        'Client error';
&#8203;
      break;
&#8203;
    case
      HttpStatusCode
        .InternalServerError:
&#8203;
      message =
        'Server error';
&#8203;
      break;
&#8203;
    case
      HttpStatusCode
        .BadRequest:
&#8203;
      message =
        'Request error';
&#8203;
      break;
&#8203;
    default:
      message =
        'Unknown error';
  }
&#8203;
  console.error(
    message,
    error.error
  );
&#8203;
  return throwError(
    () =&gt; error
  );
}</code></pre>
</div>



En el ejemplo del capítulo, un `status` igual a `0` se trata como error del lado cliente.

---

# 10. Reintentar una petición con `retry`

Algunos errores pueden ser temporales.

RxJS ofrece:

`retry`

Ejemplo:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return this.http
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
    }),
&#8203;
    retry(2),
&#8203;
    catchError(
      this.handleError
    )
  );</code></pre>
</div>



`retry(2)` solicita volver a intentar la operación dos veces antes de pasar al tratamiento del error.

Conceptualmente:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Petición
   │
   ├── error
   │     │
   │     └── retry 1
   │             │
   │             ├── error
   │             │     │
   │             │     └── retry 2
   │             │
   │             └── éxito
   │
   └── catchError si persiste el error</code></pre>
</div>



---

# 11. Limitación de la gestión de errores por servicio

Crear un `handleError()` distinto en cada servicio no escala bien.

Una aplicación podría tener:

```text
ProductsService
OrdersService
CartService
AuthService
UsersService
...
```

Duplicar la misma lógica en todos ellos dificultaría el mantenimiento.

Angular ofrece `ErrorHandler` para centralizar la gestión.

---

# 12. `ErrorHandler`

Angular proporciona la clase:

`ErrorHandler`

La implementación por defecto registra errores en la consola.

Podemos sustituirla por una implementación propia.

Creamos:

`src/app/app-error-handler.ts`

Importaciones:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpErrorResponse,
  HttpStatusCode
} from '@angular/common/http';
&#8203;
import {
  ErrorHandler,
  Injectable
} from '@angular/core';</code></pre>
</div>



---

# 13. Crear `AppErrorHandler`

Declaramos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">@Injectable()
export class AppErrorHandler
  implements ErrorHandler {
}</code></pre>
</div>



`@Injectable()` es necesario porque registraremos esta clase mediante DI.

---

# 14. Implementar `handleError()`



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">handleError(
  error: any
): void {
&#8203;
  const err =
    error.rejection
    || error;
&#8203;
  let message = '';
&#8203;
  if (
    err instanceof
      HttpErrorResponse
  ) {
    switch (
      err.status
    ) {
      case 0:
        message =
          'Client error';
&#8203;
        break;
&#8203;
      case
        HttpStatusCode
          .InternalServerError:
&#8203;
        message =
          'Server error';
&#8203;
        break;
&#8203;
      case
        HttpStatusCode
          .BadRequest:
&#8203;
        message =
          'Request error';
&#8203;
        break;
&#8203;
      default:
        message =
          'Unknown error';
    }
  } else {
    message =
      'Application error';
  }
&#8203;
  console.error(
    message,
    err
  );
}</code></pre>
</div>



El código distingue:

- errores HTTP;
- errores generales de la aplicación.

---

# 15. `error.rejection`

El material explica que algunos errores procedentes del entorno de ejecución gestionado por Zone.js pueden encapsular el error real en:

`error.rejection`

Por eso se utiliza:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const err =
  error.rejection
  || error;</code></pre>
</div>



Primero intentamos recuperar el error encapsulado y, si no existe, utilizamos directamente el error original.

---

# 16. Registrar el manejador global

En `app.config.ts` importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  ApplicationConfig,
  ErrorHandler,
  provideZoneChangeDetection
} from '@angular/core';
&#8203;
import {
  AppErrorHandler
} from './app-error-handler';</code></pre>
</div>



Y registramos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const appConfig:
  ApplicationConfig = {
    providers: [
      provideZoneChangeDetection({
        eventCoalescing: true
      }),
&#8203;
      provideRouter(
        routes
      ),
&#8203;
      provideHttpClient(),
&#8203;
      {
        provide:
          APP_SETTINGS,
        useValue:
          appSettings
      },
&#8203;
      {
        provide:
          ErrorHandler,
        useClass:
          AppErrorHandler
      }
    ]
  };</code></pre>
</div>



Angular utilizará ahora `AppErrorHandler` como manejador global.

---

# 17. Probar el manejador global

El capítulo propone un experimento sencillo:

1. iniciar la aplicación;
2. desconectar el equipo de Internet;
3. navegar a `http://localhost:4200`;
4. abrir DevTools;
5. observar la consola.

La captura de la **Figura 11.1** muestra un `HttpErrorResponse` con:

```text
status: 0
statusText: Unknown Error
message: Http failure response...
```

y el mensaje del manejador:

```text
Client error
```

Esto confirma que el error de red llega al manejador global.

---

# 18. Error `401 Unauthorized`

Uno de los errores HTTP habituales en aplicaciones empresariales es:

`401 Unauthorized`

Puede aparecer cuando:

- el usuario introduce credenciales incorrectas;
- el token de autenticación ha caducado.

El material considera que un lugar adecuado para gestionarlo es el interceptor de autenticación.

---

# 19. Capturar `401` en `authInterceptor`

Importaciones:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpErrorResponse,
  HttpInterceptorFn,
  HttpStatusCode
} from '@angular/common/http';
&#8203;
import {
  inject
} from '@angular/core';
&#8203;
import {
  AuthService
} from './auth.service';
&#8203;
import {
  catchError,
  EMPTY,
  throwError
} from 'rxjs';</code></pre>
</div>



Interceptor:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const authInterceptor:
  HttpInterceptorFn =
    (req, next) =&gt; {
&#8203;
      const authService =
        inject(
          AuthService
        );
&#8203;
      const authReq =
        req.clone({
          setHeaders: {
            Authorization:
              'myToken'
          }
        });
&#8203;
      return next(
        authReq
      )
      .pipe(
        catchError(
          (
            error:
              HttpErrorResponse
          ) =&gt; {
&#8203;
            if (
              error.status ===
              HttpStatusCode
                .Unauthorized
            ) {
              authService
                .logout();
&#8203;
              return EMPTY;
            }
&#8203;
            return throwError(
              () =&gt; error
            );
          }
        )
      );
    };</code></pre>
</div>



---

# 20. ¿Qué ocurre cuando llega un `401`?

El flujo es:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Respuesta HTTP 401
      │
      ▼
authInterceptor
      │
      ├── authService.logout()
      │
      └── return EMPTY
             │
             ▼
      el stream deja
      de emitir valores</code></pre>
</div>



`EMPTY` representa un observable que finaliza sin emitir datos.

---

# 21. ¿Qué ocurre con el resto de errores?

Para cualquier error distinto de `401`:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return throwError(
  () =&gt; error
);</code></pre>
</div>



El error continúa propagándose.

En la arquitectura planteada por el capítulo:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">HTTP error
   │
   ▼
authInterceptor
   │
   ├── 401 → logout + EMPTY
   │
   └── otros
          │
          ▼
      throwError
          │
          ▼
   AppErrorHandler</code></pre>
</div>



Así cada nivel trata únicamente los errores de los que es responsable.

---

# 22. Errores del framework Angular

No todos los errores proceden de HTTP.

También pueden aparecer por una interacción incorrecta entre nuestro código y Angular.

Angular utiliza un formato de error:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">NGWXYZ: {Error message}.&lt;Link&gt;</code></pre>
</div>



El capítulo descompone el código de la siguiente forma:

| Parte | Significado |
|---|---|
| `NG` | Error de Angular |
| `W` | Tipo de error |
| `X` | Categoría o área del framework |
| `YZ` | Código específico |
| `{Error message}` | Mensaje del error |
| `<Link>` | Documentación asociada |

---

# 23. Tipo de error `W`

Según el material:

- `0` representa un error de runtime;
- los valores `1` a `9` representan errores del compilador.

Angular utiliza estos códigos para que podamos identificar rápidamente el origen del problema.

---

# 24. Enlaces a la documentación del error

Los mensajes de Angular pueden incluir una URL hacia la documentación oficial.

Ejemplo:

```text
https://angular.dev/errors/NG0100
```

Esto permite consultar:

- explicación;
- causas habituales;
- recomendaciones de solución.

---

# 25. Ejemplo: `NG0100`

El capítulo utiliza uno de los errores más conocidos:

`ExpressionChangedAfterItHasBeenCheckedError`

Importamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  AfterViewInit,
  Component,
  inject
} from '@angular/core';</code></pre>
</div>



Implementamos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class AppComponent
  implements AfterViewInit {
}</code></pre>
</div>



Añadimos:



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title = '';</code></pre>
</div>



---

# 26. Modificar el valor en `ngAfterViewInit`



<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">ngAfterViewInit(): void {
  this.title =
    this.settings.title;
}</code></pre>
</div>



Y la plantilla:



<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h2&gt;
  {{ title }}
&lt;/h2&gt;</code></pre>
</div>



Visualmente parece funcionar, pero DevTools muestra el error.

---

# 27. Mensaje `NG0100`

El ejemplo del PDF muestra un mensaje similar a:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">Application error RuntimeError: NG0100:
ExpressionChangedAfterItHasBeenCheckedError:
Expression has changed after it was checked.
Previous value: ''.
Current value: 'My e-shop'.
Expression location:
_AppComponent component.
&#8203;
Find more at:
https://angular.dev/errors/NG0100</code></pre>
</div>



El problema aparece porque el valor observado por Angular cambia después de haber sido comprobado dentro del ciclo correspondiente.

---

# 28. Interpretar el error en lugar de ignorarlo

El objetivo del capítulo no es memorizar todos los códigos de Angular.

La estrategia es:

1. leer el código;
2. leer el mensaje;
3. identificar el componente o expresión;
4. seguir el enlace oficial;
5. comprender qué regla del framework estamos incumpliendo;
6. corregir la causa.

> ⭐ Los códigos `NG...` están diseñados para ayudar a diagnosticar errores del framework de una forma sistemática.

---

# 29. Estrategia general de tratamiento de errores

Podemos resumir el enfoque del capítulo así:



<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">ERROR
 │
 ├── HTTP
 │    │
 │    ├── error específico
 │    │      └── catchError
 │    │
 │    ├── autenticación
 │    │      └── interceptor
 │    │
 │    └── error no tratado
 │           └── ErrorHandler
 │
 └── aplicación / Angular
      │
      ├── ErrorHandler
      │
      └── código NG...
           └── documentación Angular</code></pre>
</div>



---

# 30. Comparativa de herramientas

| Herramienta | Uso principal |
|---|---|
| `catchError()` | Capturar errores en un stream RxJS |
| `throwError()` | Emitir/repropagar un error como observable |
| `retry()` | Reintentar una operación |
| `HttpErrorResponse` | Representar errores HTTP |
| `HttpStatusCode` | Enumeración de códigos HTTP |
| `ErrorHandler` | Gestión global |
| `EMPTY` | Observable que completa sin emitir |
| Interceptor | Tratamiento transversal de HTTP |
| `NG...` | Código identificador de error Angular |

---

# 31. Buenas decisiones arquitectónicas

El material conduce a varias ideas de diseño importantes:

### No duplicar la misma lógica

Si muchos servicios necesitan la misma gestión de errores, conviene pensar en un mecanismo central.

### Gestionar cerca de la responsabilidad adecuada

Ejemplo:

- `401` → interceptor de autenticación;
- error HTTP específico → servicio u operación concreta;
- error general → `ErrorHandler`.

### No ocultar errores importantes

Capturar un error no significa simplemente ignorarlo.

Debemos decidir si:

- mostrar información;
- registrar;
- reintentar;
- cerrar sesión;
- propagar;
- transformar.

---

# 32. Resumen de la unidad

En esta unidad hemos aprendido a gestionar errores de runtime y errores producidos por Angular.

Hemos trabajado con:

- `catchError`;
- `HttpErrorResponse`;
- `HttpStatusCode`;
- `throwError`;
- `retry`;
- `ErrorHandler`;
- manejadores globales personalizados;
- `401 Unauthorized`;
- `EMPTY`;
- interceptores;
- códigos de error `NG...`;
- `NG0100`;
- `ExpressionChangedAfterItHasBeenCheckedError`.

Una buena gestión de errores permite que una aplicación sea más:

- robusta;
- mantenible;
- diagnósticable;
- predecible para el usuario.

---

# 33. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Runtime error | Error durante la ejecución |
| Development error | Error derivado del desarrollo o uso incorrecto |
| `catchError()` | Captura un error RxJS |
| `HttpErrorResponse` | Error producido en una operación HTTP |
| `HttpStatusCode` | Enum de códigos HTTP |
| `throwError()` | Devuelve un observable que emite error |
| `retry()` | Reintenta una operación observable |
| `ErrorHandler` | API global de gestión de errores |
| `AppErrorHandler` | Implementación personalizada del capítulo |
| `status === 0` | Tratado en el ejemplo como error cliente/red |
| `401 Unauthorized` | Acceso no autorizado |
| `EMPTY` | Observable que completa sin emitir |
| HTTP interceptor | Procesa peticiones/respuestas transversalmente |
| `NG...` | Formato de código de error Angular |
| `NG0100` | Expression changed after checked |
| `AfterViewInit` | Hook posterior a inicialización de vista |
| `error.rejection` | Posible encapsulado del error real |

---

## 🔗 Recursos mencionados en el material

- Angular Error Encyclopedia: <https://angular.dev/errors>
- Angular HTTP: <https://angular.dev/guide/http>
- RxJS `catchError`: <https://rxjs.dev/api/operators/catchError>
- RxJS `retry`: <https://rxjs.dev/api/operators/retry>

---

> 🎓 **Siguiente paso**  
> Después de aprender a gestionar errores, el material indica que el siguiente capítulo aborda **Angular Material**, utilizando componentes y estilos preparados para mejorar la interfaz visual de la aplicación.

---

# 34. Taller práctico: gestión de errores sobre el proyecto real 🧪

Los ejercicios de esta sección toman como referencia directa el proyecto:

<https://github.com/hokahey007/codebase-angular/tree/main/11_errores>

En el estado actual del repositorio encontramos varios puntos especialmente útiles para practicar:

- `ProductsService` ya captura errores de `getProducts()` mediante `catchError(this.handleError)`;
- `handleError()` distingue `status === 0`, `500`, `400` y el resto de estados;
- `AppErrorHandler` implementa `ErrorHandler` y diferencia errores HTTP de errores generales;
- `app.config.ts` registra `AppErrorHandler` como manejador global;
- `productsResolver` obtiene la lista de productos antes de activar `/products`;
- `ProductListComponent` consume esos datos desde `ActivatedRoute.data`;
- `AuthService` mantiene el estado de autenticación y permite ejecutar `login()` y `logout()`.

> 🎯 **Forma recomendada de trabajo**  
> Para estos ejercicios utiliza DevTools, especialmente **Console** y **Network**. El objetivo no es solo “hacer desaparecer” el error, sino entender dónde se origina, quién lo captura y hasta dónde se propaga.

---

## Ejercicio 1. Localiza la cadena de tratamiento de errores actual

### Objetivo

Comprender qué piezas del repositorio ya participan en la gestión de errores.

Dibuja el flujo actual:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">GET /products
      │
      ▼
ProductsService.getProducts()
      │
      ▼
catchError(this.handleError)
      │
      ├── console.error(...)
      │
      └── throwError(...)
             │
             ▼
AppErrorHandler
             │
             ▼
console.error(...)
&#8203;</code></pre>
</div>


### Tareas

1. Abre `products.service.ts`.
2. Localiza `catchError(this.handleError)`.
3. Abre `app-error-handler.ts`.
4. Localiza `handleError(error: any)`.
5. Abre `app.config.ts`.
6. Localiza el provider de `ErrorHandler`.

### Pregunta

¿Por qué podemos afirmar que el proyecto combina gestión **local** y gestión **global** de errores?

---

## Ejercicio 2. Fuerza un error de red

### Objetivo

Comprobar el caso `status === 0`.

### Tareas

1. Arranca la aplicación.
2. Abre `/products`.
3. Desactiva temporalmente la conexión de red o cambia la URL de la API por una dirección inválida.
4. Recarga.
5. Observa la consola.
6. Inspecciona el objeto `HttpErrorResponse`.

### Debes localizar

- `status`;
- `statusText`;
- `message`;
- `url`;
- `error`.

### Pregunta

¿Por qué el repositorio clasifica `status === 0` como `Client error`?

---

## Ejercicio 3. Fuerza un `404`

### Objetivo

Observar cómo trata el código actual un estado no contemplado expresamente.

Cambia temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">private productsUrl =
  inject(APP_SETTINGS).apiUrl
  + '/products-no-existe';</code></pre>
</div>


### Tareas

1. Recarga `/products`.
2. Observa **Network**.
3. Comprueba el código HTTP.
4. Observa qué mensaje genera `handleError()`.

### Pregunta

¿Por qué un `404` termina actualmente en el caso:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">default:
  message =
    'Unknown error';</code></pre>
</div>


?

---

## Ejercicio 4. Añade soporte explícito para `404`

### Objetivo

Ampliar la clasificación de errores HTTP.

Modifica `handleError()`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">case HttpStatusCode.NotFound:
  message =
    'Resource not found';
&#8203;
  break;</code></pre>
</div>


### Tareas

1. Mantén temporalmente la URL incorrecta.
2. Recarga la aplicación.
3. Comprueba el nuevo mensaje.
4. Restaura después la URL correcta.

---

## Ejercicio 5. Añade soporte para `401 Unauthorized`

### Objetivo

Completar la tabla de errores frecuentes.

Añade:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">case HttpStatusCode.Unauthorized:
  message =
    'Unauthorized';
&#8203;
  break;</code></pre>
</div>


### Preguntas

1. ¿Debería limitarse el proyecto a registrar este error?
2. ¿Qué otra acción tendría sentido si el usuario había iniciado sesión previamente?

No implementes todavía esa lógica; la trabajaremos en el interceptor.

---

## Ejercicio 6. Inspecciona `HttpStatusCode`

### Objetivo

Evitar números mágicos.

Busca en el editor algunos miembros de:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">HttpStatusCode</code></pre>
</div>


y completa:

| Estado | Enum |
|---:|---|
| 400 |  |
| 401 |  |
| 403 |  |
| 404 |  |
| 500 |  |

### Reflexión

¿Por qué resulta más legible escribir:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">HttpStatusCode.BadRequest</code></pre>
</div>


que simplemente `400`?

---

## Ejercicio 7. Añade `retry(2)`

### Objetivo

Practicar reintentos antes de capturar definitivamente el error.

Importa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  retry
} from 'rxjs';</code></pre>
</div>


y modifica:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return this.http
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
    }),
&#8203;
    retry(2),
&#8203;
    catchError(
      this.handleError
    )
  );</code></pre>
</div>


### Tareas

1. Fuerza temporalmente un error.
2. Observa Network.
3. Cuenta las peticiones.
4. Explica por qué `retry` debe situarse antes de `catchError`.

---

## Ejercicio 8. Experimenta con el orden de operadores

### Objetivo

Comprender que el orden dentro de `pipe()` importa.

Prueba temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">pipe(
  catchError(
    this.handleError
  ),
  retry(2)
)</code></pre>
</div>


### Pregunta

¿Se comporta igual que:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">pipe(
  retry(2),
  catchError(
    this.handleError
  )
)</code></pre>
</div>


?

Documenta lo observado y restaura la versión correcta.

---

## Ejercicio 9. Añade contexto al log de `handleError()`

### Objetivo

Mejorar la capacidad de diagnóstico.

Cambia:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">console.error(
  message,
  error.error
);</code></pre>
</div>


por una versión más informativa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">console.error(
  '[ProductsService]',
  {
    message,
    status:
      error.status,
    url:
      error.url,
    detail:
      error.error
  }
);</code></pre>
</div>


### Reflexión

¿Por qué en una aplicación real conviene registrar contexto además del mensaje genérico?

---

## Ejercicio 10. Devuelve un valor alternativo en un error

### Objetivo

Comprobar que `catchError` también puede recuperar el flujo.

Prueba temporalmente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">catchError(
  (
    error:
      HttpErrorResponse
  ) =&gt; {
    console.error(error);
&#8203;
    return of([]);
  }
)</code></pre>
</div>


### Tareas

1. Fuerza un fallo en `/products`.
2. Observa si la navegación sigue completándose.
3. Comprueba qué muestra el listado.

### Pregunta

¿Qué diferencia conceptual existe entre:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return of([]);</code></pre>
</div>


y:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return throwError(
  () =&gt; error
);</code></pre>
</div>


?

---

## Ejercicio 11. Analiza la propagación a través del resolver

### Objetivo

Relacionar la gestión de errores con Angular Router.

`productsResolver` devuelve directamente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">return productService
  .getProducts(limit);</code></pre>
</div>


### Tareas

1. Fuerza un error en `getProducts()`.
2. Navega a `/products`.
3. Observa si `ProductListComponent` llega a recibir datos.
4. Observa la consola y la navegación.

### Pregunta

¿Qué efecto tiene propagar un error desde un observable utilizado por un resolver?

---

## Ejercicio 12. Recupera el resolver con una lista vacía

### Objetivo

Decidir dónde debe manejarse un error según la UX deseada.

Como experimento, modifica el resolver:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export const productsResolver:
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
        .getProducts(limit)
        .pipe(
          catchError(
            () =&gt; of([])
          )
        );
    };</code></pre>
</div>


### Tareas

1. Fuerza un error.
2. Comprueba si se activa el componente.
3. Decide si esta experiencia es mejor o peor que bloquear la navegación.

### Reflexión

No existe una única estrategia correcta: depende del significado que tenga el fallo para la aplicación.

---

## Ejercicio 13. Comprueba el manejador global actual

### Objetivo

Verificar que `AppErrorHandler` está realmente registrado.

En `app.config.ts` existe:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  provide:
    ErrorHandler,
  useClass:
    AppErrorHandler
}</code></pre>
</div>


### Experimento

Comenta temporalmente ese provider.

Provoca después un error de aplicación.

Compara:

- salida con el handler personalizado;
- salida con la implementación por defecto de Angular.

---

## Ejercicio 14. Genera un error de aplicación no HTTP

### Objetivo

Comprobar la rama:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">else {
  message =
    'Application error';
}</code></pre>
</div>


Añade temporalmente a `AppComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">throwTestError(): void {
  throw new Error(
    'Error de prueba'
  );
}</code></pre>
</div>


Y en plantilla:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;button
  (click)="throwTestError()"
&gt;
  Lanzar error
&lt;/button&gt;</code></pre>
</div>


### Tareas

1. Pulsa el botón.
2. Observa la consola.
3. Comprueba que no se clasifica como `HttpErrorResponse`.

---

## Ejercicio 15. Mejora el tipado del handler

### Objetivo

Evitar `any` cuando sea posible.

El repositorio utiliza:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">handleError(
  error: any
): void</code></pre>
</div>


Como ejercicio de análisis, prueba:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">handleError(
  error: unknown
): void {
  const wrapped =
    error as {
      rejection?: unknown
    };
&#8203;
  const err =
    wrapped.rejection
    ?? error;
&#8203;
  // resto de lógica
}</code></pre>
</div>


### Pregunta

¿Por qué `unknown` obliga a comprobar o acotar el tipo antes de utilizar sus propiedades?

---

## Ejercicio 16. Evita duplicar la clasificación de códigos

### Objetivo

Detectar una repetición de lógica existente.

Actualmente `ProductsService.handleError()` y `AppErrorHandler.handleError()` contienen lógica similar de clasificación.

Extrae una función pura:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export function getHttpErrorMessage(
  status: number
): string {
  switch (status) {
    case 0:
      return 'Client error';
&#8203;
    case HttpStatusCode
      .BadRequest:
      return 'Request error';
&#8203;
    case HttpStatusCode
      .Unauthorized:
      return 'Unauthorized';
&#8203;
    case HttpStatusCode
      .NotFound:
      return 'Resource not found';
&#8203;
    case HttpStatusCode
      .InternalServerError:
      return 'Server error';
&#8203;
    default:
      return 'Unknown error';
  }
}</code></pre>
</div>


### Tareas

1. Utilízala desde `ProductsService`.
2. Utilízala desde `AppErrorHandler`.
3. Comprueba que ambas piezas mantienen el mismo criterio.

---

## Ejercicio 17. Inspecciona el login actual

### Objetivo

Preparar el tratamiento específico de autenticación.

`AuthComponent` llama actualmente a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">this.authService
  .login(
    'david_r',
    '3478*#54'
  )
  .subscribe();</code></pre>
</div>


y `AuthService.login()` realiza un `POST`.

### Tareas

1. Ejecuta un login correcto.
2. Observa Network.
3. Cambia temporalmente la contraseña por un valor incorrecto.
4. Observa el error devuelto por la API.

### Pregunta

¿Dónde sería mejor centralizar un `401` relacionado con autenticación: en todos los componentes o en una capa transversal?

---

## Ejercicio 18. Crea un interceptor de autenticación con tratamiento de `401`

### Objetivo

Implementar el patrón descrito en la unidad sobre el repositorio.

Crea un archivo:

```text
auth.interceptor.ts
```

con:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  HttpErrorResponse,
  HttpInterceptorFn,
  HttpStatusCode
} from '@angular/common/http';
&#8203;
import {
  inject
} from '@angular/core';
&#8203;
import {
  catchError,
  EMPTY,
  throwError
} from 'rxjs';
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
        inject(
          AuthService
        );
&#8203;
      return next(req)
        .pipe(
          catchError(
            (
              error:
                HttpErrorResponse
            ) =&gt; {
&#8203;
              if (
                error.status ===
                HttpStatusCode
                  .Unauthorized
              ) {
                authService
                  .logout();
&#8203;
                return EMPTY;
              }
&#8203;
              return throwError(
                () =&gt; error
              );
            }
          )
        );
    };</code></pre>
</div>


### Nota

En esta rama del repositorio no aparece ese interceptor en la ruta esperada, por lo que este ejercicio lo añade como evolución del proyecto.

---

## Ejercicio 19. Registra el interceptor

### Objetivo

Integrar el tratamiento transversal en `HttpClient`.

Modifica `app.config.ts`:

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


Y:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">provideHttpClient(
  withInterceptors([
    authInterceptor
  ])
)</code></pre>
</div>


### Tareas

1. Fuerza un login incorrecto.
2. Comprueba que se llama a `logout()`.
3. Comprueba el valor de `isLoggedIn()`.
4. Observa que `EMPTY` finaliza el stream sin propagar ese `401`.

---

## Ejercicio 20. Compara `EMPTY` y `throwError`

### Objetivo

Comprender dos decisiones distintas frente a un error.

Completa:

| Estrategia | Emite valor | Emite error | Completa |
|---|:---:|:---:|:---:|
| `EMPTY` |  |  |  |
| `throwError(() => error)` |  |  |  |

### Pregunta

¿Por qué puede tener sentido consumir un `401` con `EMPTY`, pero propagar otros errores?

---

## Ejercicio 21. Provoca `NG0100`

### Objetivo

Reproducir un error del framework Angular.

Modifica temporalmente `AppComponent`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import {
  AfterViewInit,
  Component,
  inject
} from '@angular/core';
&#8203;
export class AppComponent
  implements AfterViewInit {
&#8203;
  settings =
    inject(APP_SETTINGS);
&#8203;
  title = '';
&#8203;
  ngAfterViewInit(): void {
    this.title =
      this.settings.title;
  }
}</code></pre>
</div>


Y cambia:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h2&gt;
  {{ title }}
&lt;/h2&gt;</code></pre>
</div>


### Tareas

1. Ejecuta en modo desarrollo.
2. Abre la consola.
3. Localiza `NG0100`.
4. Identifica:
   - valor anterior;
   - valor nuevo;
   - componente implicado;
   - enlace a la documentación.

---

## Ejercicio 22. Corrige el origen de `NG0100`

### Objetivo

No limitarse a ocultar el error.

Mueve la inicialización a una fase adecuada, por ejemplo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">title =
  this.settings.title;</code></pre>
</div>


o mantén directamente la expresión original del repositorio:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">&lt;h2&gt;
  {{ settings.title }}
&lt;/h2&gt;</code></pre>
</div>


### Reflexión

¿Por qué es mejor corregir el momento en el que cambia el estado que intentar silenciar el error?

---

## Ejercicio 23. Descompón un código `NG...`

### Objetivo

Practicar la lectura sistemática de errores Angular.

Partiendo de:

```text
NG0100
```

completa:

| Parte | Valor | Interpretación |
|---|---|---|
| Prefijo | `NG` |  |
| Tipo | `0` |  |
| Categoría | `1` |  |
| Código | `00` |  |

Después sigue el enlace de documentación que aparece en el propio error.

---

## Ejercicio 24. Diseña una política de errores

### Objetivo

Pasar de código aislado a una estrategia de arquitectura.

Clasifica dónde gestionarías cada caso:

| Error | Servicio | Interceptor | ErrorHandler | UI |
|---|:---:|:---:|:---:|:---:|
| `400 Bad Request` al crear producto |  |  |  |  |
| `401 Unauthorized` |  |  |  |  |
| servidor sin conexión |  |  |  |  |
| excepción inesperada en componente |  |  |  |  |
| `404` al buscar un producto |  |  |  |  |

No existe necesariamente una única respuesta válida: justifica cada decisión.

---

# 35. Reto integrador: arquitectura de errores de la tienda 🚀

### Objetivo

Aplicar una estrategia coherente de gestión de errores sobre el proyecto `11_errores`.

La solución deberá incluir:

### 1. Gestión en `ProductsService`

- `retry(2)` en la carga de productos;
- clasificación mediante `HttpStatusCode`;
- reutilización de una función común de mensajes;
- propagación de errores no recuperables.

### 2. Gestión global

`AppErrorHandler` deberá registrar como mínimo:

- tipo de error;
- mensaje;
- status HTTP cuando exista;
- URL cuando exista.

### 3. Autenticación

Añade un interceptor que:

- detecte `401`;
- ejecute `AuthService.logout()`;
- no propague el `401`;
- propague el resto de errores.

### 4. UX

Añade una representación simple de error para el usuario.

Por ejemplo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">errorMessage = '';</code></pre>
</div>


y:

<div class="code-card" data-lang="html">
<div class="code-label">HTML</div>
<pre><code class="language-html">@if (errorMessage) {
  &lt;p class="error"&gt;
    {{ errorMessage }}
  &lt;/p&gt;
}</code></pre>
</div>


No muestres al usuario objetos técnicos completos como `HttpErrorResponse`.

### 5. Pruebas manuales

Debes provocar al menos:

- error de red;
- `404`;
- credenciales incorrectas;
- error general de aplicación;
- `NG0100`.

### Checklist

- [ ] Se utiliza `catchError()`.
- [ ] Se utiliza `HttpErrorResponse`.
- [ ] Se utiliza `HttpStatusCode`.
- [ ] Se utiliza `retry()`.
- [ ] Existe un `ErrorHandler` personalizado.
- [ ] Está registrado mediante DI.
- [ ] Existe un tratamiento específico de `401`.
- [ ] Se utiliza `EMPTY` en un caso justificado.
- [ ] Los demás errores pueden propagarse.
- [ ] Se ha reproducido y comprendido `NG0100`.
- [ ] Los mensajes para usuario no exponen detalles internos.
- [ ] El proyecto sigue compilando correctamente.

---

# 36. Diagnóstico de errores 🔎

## Caso A. `catchError` no devuelve observable

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">catchError(error =&gt; {
  console.error(error);
})</code></pre>
</div>


**Pregunta:** ¿qué debe devolver la función de `catchError`?

---

## Caso B. `retry` colocado después de una recuperación

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">pipe(
  catchError(
    () =&gt; of([])
  ),
  retry(2)
)</code></pre>
</div>


**Pregunta:** si `catchError` ya convierte el fallo en `of([])`, ¿queda un error que `retry` pueda reintentar?

---

## Caso C. Error global no registrado

Existe `AppErrorHandler`, pero falta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">{
  provide:
    ErrorHandler,
  useClass:
    AppErrorHandler
}</code></pre>
</div>


**Pregunta:** ¿qué implementación utilizará Angular?

---

## Caso D. `401` reemitido después de logout

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">if (
  error.status ===
    HttpStatusCode.Unauthorized
) {
  authService.logout();
&#8203;
  return throwError(
    () =&gt; error
  );
}</code></pre>
</div>


**Pregunta:** ¿en qué se diferencia esta decisión de devolver `EMPTY`?

---

## Caso E. `NG0100` ignorado

El desarrollador ve la interfaz correcta y decide ignorar la consola.

**Pregunta:** ¿por qué la ausencia de un fallo visual inmediato no significa que el código sea correcto?

---

# 37. Cuestiones finales de reflexión 📝

1. ¿Qué diferencia existe entre un error de runtime y uno de desarrollo?
2. ¿Para qué sirve `catchError()`?
3. ¿Qué contiene `HttpErrorResponse`?
4. ¿Qué representa `status === 0` en los ejemplos de la unidad?
5. ¿Qué ventaja ofrece `HttpStatusCode`?
6. ¿Qué diferencia existe entre recuperar un stream con `of(...)` y reemitir el error?
7. ¿Qué hace `retry(2)`?
8. ¿Por qué el orden `retry → catchError` es importante?
9. ¿Qué problema tiene duplicar `handleError()` en todos los servicios?
10. ¿Qué responsabilidad tiene `ErrorHandler`?
11. ¿Cómo se sustituye el `ErrorHandler` por defecto?
12. ¿Por qué `AppErrorHandler` comprueba `HttpErrorResponse`?
13. ¿Para qué se examina `error.rejection`?
14. ¿Por qué un `401` encaja bien en un interceptor de autenticación?
15. ¿Qué hace `EMPTY`?
16. ¿Qué diferencia existe entre `EMPTY` y `throwError()`?
17. ¿Qué significa el prefijo `NG` en un error Angular?
18. ¿Qué tipo de información aporta un error como `NG0100`?
19. ¿Por qué conviene seguir el enlace oficial incluido en los errores Angular?
20. ¿Qué errores deberían mostrarse al usuario y cuáles deberían registrarse solo para diagnóstico?
21. ¿Qué información nunca deberíamos exponer directamente en un mensaje de error al usuario?
22. ¿Cómo afecta un error no tratado dentro de un resolver a la navegación?
23. ¿Cuándo tendría sentido devolver un valor alternativo como `of([])`?
24. ¿Qué estrategia utilizarías para monitorizar errores en una aplicación real?

---

> ✅ **Meta de aprendizaje**  
> Si puedes decidir dónde debe tratarse cada tipo de error, comprender cómo se propaga por RxJS y Angular, y diagnosticar un código `NG...` sin limitarte a ocultarlo, ya tienes una base sólida de gestión de errores en Angular.
