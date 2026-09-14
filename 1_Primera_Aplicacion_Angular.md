# 🚀 Unidad: Construyendo tu primera aplicación Angular
<style>
/* ===== Bloques de código ===== */
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

/* Variantes muy suaves por lenguaje */
.code-card[data-lang="typescript"] pre,
.code-card[data-lang="javascript"] pre {
  background: #f7f9fc;
}

.code-card[data-lang="html"] pre {
  background: #fff8f5;
}

.code-card[data-lang="bash"] pre,
.code-card[data-lang="shell"] pre {
  background: #f7fbf7;
}

.code-card[data-lang="text"] pre,
.code-card[data-lang="editorconfig"] pre {
  background: #fafafa;
}

/* Las líneas visualmente vacías dentro de <pre><code> se representan
   con &#8203; para evitar que ciertos motores Markdown cierren el bloque HTML. */

/* Código inline */
:not(pre) > code {
  padding: 0.12em 0.38em;
  border-radius: 4px;
  background: #eef1f4;
  color: #24292f;
  font-family: "Cascadia Code", "JetBrains Mono", Consolas, monospace;
  font-size: 0.92em;
}
</style>

> **Versión de referencia:** Angular 19  
> **Objetivo:** comprender qué es Angular, por qué utilizarlo, cómo preparar el entorno de desarrollo, crear una primera aplicación con Angular CLI y conocer las principales herramientas del ecosistema.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Explicar qué es Angular y cuáles son sus principales ventajas.
- Reconocer la evolución histórica desde AngularJS hasta Angular moderno.
- Preparar un entorno de desarrollo para Angular.
- Instalar y utilizar **Angular CLI**.
- Crear y ejecutar una primera aplicación.
- Identificar la estructura básica de un proyecto Angular.
- Comprender el papel de los **componentes**, el **bootstrapping** y la **interpolación**.
- Utilizar herramientas como **Angular DevTools** y el **depurador de VS Code**.
- Configurar un perfil de VS Code orientado al desarrollo con Angular.

---

## 📑 Contenidos

1. [[#1. ¿Qué es Angular?|¿Qué es Angular?]]
2. [[#3. Preparación del entorno de desarrollo|Preparación del entorno de desarrollo]]
3. [[#5. Instalación de Angular CLI|Instalación de Angular CLI]]
4. [[#7. Crear un nuevo proyecto Angular|Crear un nuevo proyecto Angular]]
5. [[#8. Estructura inicial del proyecto|Estructura inicial del proyecto]]
6. [[#16. Sintaxis de plantillas|Sintaxis de plantillas]]
7. [[#17. Herramientas para trabajar con Angular|Herramientas para trabajar con Angular]]
8. [[#26. Primera práctica guiada|Primera práctica guiada]]
9. [[#31. Taller práctico: comprende tu primera aplicación Angular 🧪|Taller práctico: comprende tu primera aplicación Angular 🧪]]
10. [[#32. Reto integrador: transforma la página inicial 🚀|Reto integrador: transforma la página inicial 🚀]]
11. [[#33. Cuestiones de reflexión para entregar 📝|Cuestiones de reflexión para entregar 📝]]

---

# 1. ¿Qué es Angular?

**Angular** es un framework web escrito en **TypeScript**. Incluye, además del propio framework:

- Una interfaz de línea de comandos: **Angular CLI**.
- Un servicio de lenguaje para el editor.
- Herramientas de depuración.
- Una amplia colección de bibliotecas oficiales.

Angular permite construir aplicaciones web escalables utilizando **TypeScript**, que es un superconjunto tipado de JavaScript.

> 💡 **Idea clave**  
> Las bibliotecas incluidas directamente por el equipo de Angular se suelen denominar **first-party libraries** o bibliotecas oficiales de primera parte.

La documentación oficial de Angular se encuentra en:

<https://angular.dev>

---

## 1.1. Breve historia de Angular

Angular fue creado por **Google**.

### AngularJS

La primera versión, **AngularJS 1.0**, apareció en 2012.

Las aplicaciones se escribían en **JavaScript** y la arquitectura del framework era muy diferente de la actual.

### Angular 2

En 2016, el equipo de Angular decidió rediseñar profundamente el framework.

Para ello:

- colaboró con el equipo de TypeScript de Microsoft;
- adoptó **TypeScript** como lenguaje principal;
- cambió la arquitectura;
- abandonó el nombre **AngularJS** y pasó a denominarse simplemente **Angular**.

### El renacimiento de Angular

A partir de 2022 comenzó una nueva etapa que el propio ecosistema ha denominado **Angular Renaissance**.

Durante esta etapa Angular introdujo mejoras importantes centradas en:

- una forma más sencilla y moderna de crear aplicaciones;
- nuevos patrones de reactividad;
- mejores mecanismos de gestión del estado;
- integración mejorada de **Server-Side Rendering (SSR)**;
- optimización del rendimiento;
- mejora de la experiencia del desarrollador o **Developer Experience (DX)**.

Uno de los hitos más importantes fue **Angular 17**, versión en la que el framework recibió una nueva identidad visual y un nuevo logotipo.

> ⚠️ **AngularJS ya no debe utilizarse en nuevos proyectos.**  
> AngularJS llegó al final de su vida útil en 2022 y ya no recibe mantenimiento oficial.

---

# 2. ¿Por qué elegir Angular?

La potencia de Angular se apoya en varios pilares:

- 🌍 Multiplataforma.
- 🧰 Excelente conjunto de herramientas.
- 🚪 Entrada relativamente sencilla al ecosistema.
- 👥 Gran comunidad.
- 🏢 Uso intensivo en productos reales y proyectos de gran tamaño.

---

## 2.1. Multiplataforma

Angular está pensado principalmente para aplicaciones web, pero puede participar en distintos tipos de proyectos.

### Web

Es su entorno natural.

### Servidor

Mediante **Angular SSR** es posible renderizar aplicaciones Angular en el servidor.

### Aplicaciones progresivas

Con el **Angular Service Worker** pueden crearse **Progressive Web Apps (PWA)**.

### Aplicaciones móviles

Herramientas como:

- **Ionic**
- **NativeScript**

permiten crear aplicaciones móviles utilizando Angular.

---

## 2.2. Herramientas del ecosistema

El equipo de Angular mantiene herramientas muy importantes.

### Angular CLI

Permite:

- crear proyectos;
- generar componentes;
- compilar;
- ejecutar pruebas;
- actualizar versiones;
- servir la aplicación;
- preparar builds de producción.

### Angular DevTools

Es una extensión del navegador que permite:

- inspeccionar componentes;
- analizar el estado de la aplicación;
- perfilar el rendimiento;
- visualizar el árbol de inyección de dependencias.

---

## 2.3. Incorporación rápida al desarrollo

Cuando creamos una nueva aplicación Angular obtenemos acceso a numerosas bibliotecas oficiales.

Entre las más habituales se encuentran:

- **HttpClient**, para realizar peticiones HTTP.
- **Angular Forms**, para trabajar con formularios.
- **Angular Router**, para navegar entre vistas o componentes.

> 🧠 Que estas bibliotecas estén disponibles no significa que siempre se utilicen automáticamente.  
> Deben importarse o configurarse cuando sean necesarias.

---

## 2.4. Uso de Angular a nivel mundial

Angular se utiliza en miles de proyectos.

Google lo emplea internamente en numerosos productos, lo que hace que nuevas versiones del framework se prueben en escenarios reales antes de llegar al público.

La comunidad también participa activamente:

- probando nuevas funcionalidades;
- informando de errores;
- proponiendo mejoras;
- compartiendo documentación;
- creando librerías;
- organizando eventos y comunidades.

Algunas comunidades destacadas son:

- **Tech Stack Nation**
- **Angular Community Discord**
- **Angular.love**

---

# 3. Preparación del entorno de desarrollo

Crear un proyecto moderno implica manejar muchas tareas:

- estructura inicial;
- compilación;
- dependencias;
- ejecución;
- pruebas;
- configuración;
- empaquetado.

Angular CLI automatiza gran parte de este trabajo mediante **schematics**.

---

# 4. Requisitos previos

Antes de trabajar con Angular necesitamos preparar varias herramientas.

---

## 4.1. Node.js

Node.js es un entorno de ejecución de JavaScript basado en el motor **V8**.

Angular necesita una versión compatible de Node.js, normalmente una versión **LTS activa o en mantenimiento**.

Para comprobar la versión instalada:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">node -v</code></pre>
</div>

> 💡 Si necesitas trabajar con varias versiones de Node.js, una solución habitual es utilizar **nvm**.

---

## 4.2. npm

**npm** es el gestor de paquetes que se instala junto con Node.js.

Para comprobar su versión:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">npm -v</code></pre>
</div>

Una aplicación Angular utiliza múltiples paquetes descargados desde el registro de npm.

npm se encarga de:

- descargar dependencias;
- instalarlas;
- mantener sus versiones;
- ejecutar scripts definidos en `package.json`.

---

## 4.3. Git

Git es un sistema de control de versiones distribuido.

Permite trabajar con plataformas como:

- GitHub;
- GitLab;
- Bitbucket.

Para Angular CLI no es estrictamente obligatorio, pero en un proyecto real es prácticamente imprescindible.

---

# 5. Instalación de Angular CLI

Angular CLI se distribuye como un paquete npm.

Para instalarlo globalmente:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">npm install -g @angular/cli</code></pre>
</div>

### Significado de los parámetros

| Elemento | Significado |
|---|---|
| `npm` | Ejecuta el gestor de paquetes |
| `install` o `i` | Instala un paquete |
| `-g` o `--global` | Instala el paquete globalmente |
| `@angular/cli` | Nombre del paquete |

En determinados sistemas pueden ser necesarios permisos administrativos.

### Linux/macOS

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">sudo npm install -g @angular/cli</code></pre>
</div>

> ⚠️ En entornos profesionales suele ser preferible utilizar un gestor de versiones como **nvm** para evitar instalar paquetes con permisos administrativos.

---

## 5.1. Instalar Angular CLI 19

La versión de Angular CLI suele corresponder con la versión del framework.

Para comprobar la versión instalada:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng version</code></pre>
</div>

También puede utilizarse:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng v</code></pre>
</div>

Si necesitas instalar específicamente Angular CLI 19:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">npm install -g @angular/cli@19</code></pre>
</div>

---

# 6. Comandos básicos de Angular CLI

La sintaxis general es:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng [comando] [opciones]</code></pre>
</div>

Para consultar la ayuda:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng help</code></pre>
</div>

## Comandos más utilizados

| Comando | Alias | Función |
|---|---:|---|
| `ng new` | `ng n` | Crea un nuevo workspace Angular |
| `ng build` | `ng b` | Compila la aplicación |
| `ng generate` | `ng g` | Genera artefactos Angular |
| `ng serve` | — | Compila y ejecuta la aplicación en desarrollo |
| `ng test` | `ng t` | Ejecuta pruebas unitarias |
| `ng add` | — | Añade una biblioteca Angular |
| `ng update` | — | Actualiza Angular y sus dependencias |

> 🔄 Mantener un proyecto actualizado es una tarea importante. Las nuevas versiones suelen incorporar mejoras de rendimiento, nuevas características y correcciones de errores.

---

# 7. Crear un nuevo proyecto Angular

Vamos a crear una aplicación llamada `my-app`.

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng new my-app</code></pre>
</div>

Angular CLI realizará varias preguntas.

---

## 7.1. Telemetría de Angular

La primera vez puede aparecer una pregunta parecida a esta:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Would you like to share pseudonymous usage data about this project
with the Angular Team at Google under Google's Privacy Policy at
https://policies.google.com/privacy. For more details and how to
change this setting, see https://angular.dev/cli/analytics. (y/N)</code></pre>
</div>

Esta opción controla si deseas compartir datos de uso anónimos con el equipo de Angular.

---

## 7.2. Formato de estilos

Angular CLI preguntará:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Which stylesheet format would you like to use?</code></pre>
</div>

Podemos elegir entre:

- CSS
- SCSS
- Sass
- Less

Para un proyecto introductorio podemos utilizar **CSS**.

---

## 7.3. SSR y SSG

También puede aparecer:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Do you want to enable Server-Side Rendering (SSR) and Static Site
Generation (SSG/Prerendering)? (y/N)</code></pre>
</div>

### SSR

**Server-Side Rendering** renderiza inicialmente la aplicación en el servidor.

### SSG

**Static Site Generation** genera contenido HTML estático previamente.

Para una primera aplicación sencilla podemos responder **No**.

---

# 8. Estructura inicial del proyecto

Una vez finalizada la creación, se genera una carpeta llamada:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">my-app</code></pre>
</div>

Dentro encontraremos los archivos del workspace.

## Elementos principales

| Archivo o carpeta | Función |
|---|---|
| `.vscode` | Configuración específica de VS Code |
| `node_modules` | Dependencias instaladas |
| `public` | Recursos estáticos |
| `src` | Código fuente |
| `.editorconfig` | Reglas de formato |
| `.gitignore` | Archivos ignorados por Git |
| `angular.json` | Configuración principal de Angular CLI |
| `package.json` | Dependencias y scripts |
| `package-lock.json` | Versiones exactas instaladas |
| `README.md` | Información general del proyecto |
| `tsconfig.app.json` | Configuración TypeScript de la aplicación |
| `tsconfig.json` | Configuración TypeScript general |
| `tsconfig.spec.json` | Configuración TypeScript para pruebas |

---

# 9. Ejecutar la aplicación

Entramos en la carpeta del proyecto:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">cd my-app</code></pre>
</div>

Y ejecutamos:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng serve</code></pre>
</div>

Angular CLI:

1. compila el proyecto;
2. inicia un servidor web de desarrollo;
3. observa cambios en los archivos;
4. recompila automáticamente cuando detecta modificaciones.

La aplicación puede abrirse normalmente en:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">http://localhost:4200</code></pre>
</div>

> 📌 Los comandos de Angular CLI relacionados con una aplicación deben ejecutarse dentro del workspace correspondiente.

---

# 10. Estructura de `src`

La carpeta `src` contiene el código principal de la aplicación.

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">src/
├── app/
├── index.html
├── main.ts
└── styles.css</code></pre>
</div>

## Archivos principales

### `app/`

Contiene los componentes y demás artefactos de Angular.

### `index.html`

Documento HTML principal de la aplicación.

### `main.ts`

Punto de entrada de la aplicación Angular.

### `styles.css`

Estilos globales.

---

# 11. Estructura inicial de `app`

En una aplicación creada por Angular CLI podemos encontrar:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">src/app/
├── app.component.css
├── app.component.html
├── app.component.spec.ts
├── app.component.ts
├── app.config.ts
└── app.routes.ts</code></pre>
</div>

---

# 12. Componentes

Los archivos cuyo nombre empieza por:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">app.component</code></pre>
</div>

forman el componente principal.

Un **componente Angular** controla una parte de la interfaz de usuario.

Combina principalmente:

- lógica de presentación;
- una plantilla HTML;
- estilos;
- opcionalmente pruebas.

---

## 12.1. El elemento raíz

En `index.html` aparece un elemento similar a:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;body&gt;
  &lt;app-root&gt;&lt;/app-root&gt;
&lt;/body&gt;</code></pre>
</div>

`<app-root>` identifica el componente principal.

Angular renderiza dentro de este elemento la plantilla del componente raíz.

> 🌳 Una aplicación Angular puede entenderse como un **árbol de componentes**.

---

# 13. Bootstrapping de la aplicación

El proceso mediante el cual Angular arranca la aplicación se denomina **bootstrapping**.

El punto de entrada se encuentra en:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">src/main.ts</code></pre>
</div>

Código:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { bootstrapApplication } from '@angular/platform-browser';
import { appConfig } from './app/app.config';
import { AppComponent } from './app/app.component';</code></pre>
</div>

A continuación se inicia la aplicación:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">bootstrapApplication(AppComponent, appConfig)
  .catch((err) =&gt; console.error(err));</code></pre>
</div>

### ¿Qué ocurre aquí?

- `AppComponent` es el componente raíz.
- `appConfig` contiene la configuración global.
- `bootstrapApplication(...)` inicia Angular.

---

# 14. Configuración de la aplicación

El archivo:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">app.config.ts</code></pre>
</div>

contiene la configuración principal.

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';
&#8203;
export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes)
  ]
};</code></pre>
</div>

El objeto `appConfig` contiene una propiedad:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">providers</code></pre>
</div>

que permite registrar servicios disponibles en la aplicación.

---

# 15. Configuración del router

Angular puede proporcionar navegación interna mediante **Angular Router**.

En `app.config.ts` se activa mediante:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">provideRouter(routes)</code></pre>
</div>

La configuración de rutas está en:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">app.routes.ts</code></pre>
</div>

Código inicial:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">import { Routes } from '@angular/router';
&#8203;
export const routes: Routes = [];</code></pre>
</div>

El array está vacío porque todavía no se han definido rutas.

---

# 16. Sintaxis de plantillas

Una de las características más importantes de Angular es que amplía HTML con una sintaxis propia.

Vamos a ver un ejemplo de **interpolación**.

---

## 16.1. Propiedad del componente

Archivo:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">app.component.ts</code></pre>
</div>

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
  title = 'my-app';
}</code></pre>
</div>

La propiedad:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">title = 'my-app';</code></pre>
</div>

forma parte del estado del componente.

---

## 16.2. Interpolación

En la plantilla:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;h1&gt;Hello, {{ title }}&lt;/h1&gt;</code></pre>
</div>

La expresión:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">{{ title }}</code></pre>
</div>

se denomina **interpolación**.

Angular toma el valor de la propiedad `title`, lo convierte a texto y lo muestra en el HTML.

### Si cambiamos el valor

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'World';
}</code></pre>
</div>

la vista mostrará:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Hello, World</code></pre>
</div>

> 🔁 Durante `ng serve`, Angular recompila la aplicación automáticamente cuando guardamos cambios.

---

# 17. Herramientas para trabajar con Angular

Angular cuenta con un ecosistema de herramientas muy potente.

Entre las principales:

- Angular DevTools
- VS Code Debugger
- VS Code Profiles

---

# 18. Angular DevTools

**Angular DevTools** es una extensión oficial del navegador.

Permite inspeccionar aplicaciones Angular directamente desde las herramientas de desarrollo.

Sus pestañas principales son:

### Components

Muestra el árbol de componentes de la aplicación.

### Profiler

Permite analizar la ejecución y rendimiento.

### Injector Tree

Permite visualizar los servicios registrados mediante el sistema de inyección de dependencias.

---

## 18.1. Inspeccionar componentes

Desde la pestaña **Components** podemos:

- seleccionar un componente;
- visualizar sus propiedades;
- consultar sus metadatos;
- localizar el elemento HTML relacionado;
- navegar al código fuente;
- modificar temporalmente propiedades.

Por ejemplo, si tenemos:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">title = 'World';</code></pre>
</div>

podemos modificar su valor desde Angular DevTools por:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Angular World</code></pre>
</div>

y ver inmediatamente el cambio en la interfaz.

---

# 19. Depuración con VS Code

Aunque puede utilizarse:

<div class="code-card" data-lang="javascript">
  <div class="code-label">JAVASCRIPT</div>
  <pre><code class="language-javascript">console.log(...)</code></pre>
</div>

para inspeccionar valores, en proyectos grandes abusar de mensajes de consola puede dificultar la lectura del código.

Una alternativa más potente es utilizar **breakpoints**.

---

## 19.1. ¿Qué es un breakpoint?

Un breakpoint es un punto en el código donde el programa se detiene temporalmente.

Cuando se alcanza podemos:

- inspeccionar variables;
- comprobar el estado del componente;
- revisar la pila de llamadas;
- avanzar paso a paso;
- continuar la ejecución.

---

## 19.2. Depurar una aplicación Angular

1. Abre `app.component.ts`.
2. Haz clic en el margen izquierdo de una línea para crear un breakpoint.
3. Abre **Run and Debug** en VS Code.
4. Inicia la configuración de depuración.
5. VS Code abrirá la aplicación.
6. Cuando se alcance el breakpoint, la ejecución quedará pausada.

Esto permite investigar el comportamiento real de la aplicación sin modificar el código con numerosos `console.log`.

---

# 20. VS Code Profiles

Los **Profiles** de VS Code permiten mantener diferentes configuraciones según el tipo de proyecto.

Un perfil puede incluir:

- configuración;
- atajos de teclado;
- snippets;
- tareas;
- extensiones.

Esto resulta especialmente útil cuando trabaja todo un equipo con herramientas similares.

---

## 20.1. Crear un perfil Angular

En VS Code:

1. Abre **Manage**.
2. Selecciona **Profiles**.
3. Crea un nuevo perfil desde una plantilla.
4. Selecciona **Angular**.
5. Personaliza el icono si lo deseas.
6. Crea el perfil.

VS Code aplicará automáticamente la nueva configuración.

---

# 21. Angular Language Service

La extensión **Angular Language Service** está mantenida por el equipo de Angular.

Aporta al editor:

- autocompletado;
- navegación a definiciones;
- información rápida;
- diagnóstico de errores;
- soporte específico para plantillas Angular.

---

## 21.1. Ejemplo de autocompletado

Supongamos que añadimos una propiedad al componente:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'my-app';
  description = 'Hello World';
}</code></pre>
</div>

Podemos utilizarla en la plantilla:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;span&gt;{{ description }}&lt;/span&gt;</code></pre>
</div>

Angular Language Service detectará `description` y podrá sugerirla durante la escritura.

---

## 21.2. Propiedades públicas y privadas

La propiedad:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">description = 'Hello World';</code></pre>
</div>

es pública por defecto.

También podría escribirse como:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">public description = 'Hello World';</code></pre>
</div>

Sin embargo, una propiedad privada:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">private description = 'Hello World';</code></pre>
</div>

no debe utilizarse desde la plantilla.

> ⚠️ Las plantillas Angular deben trabajar con miembros accesibles públicamente.

---

# 22. Diagnóstico de errores

Angular Language Service también detecta errores en las plantillas.

Por ejemplo:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;span&gt;{{ descr }}&lt;/span&gt;</code></pre>
</div>

si no existe ninguna propiedad llamada `descr`, el editor puede marcarla como error antes de ejecutar la aplicación.

Esto mejora mucho la experiencia de desarrollo.

---

# 23. Material Icon Theme

VS Code incluye iconos básicos, pero extensiones como **Material Icon Theme** permiten identificar rápidamente los tipos de archivos.

En un proyecto Angular ayuda a distinguir visualmente:

- componentes;
- rutas;
- configuración;
- TypeScript;
- HTML;
- CSS;
- pruebas.

Esto es especialmente útil en proyectos grandes.

---

# 24. EditorConfig

Angular CLI genera normalmente un archivo:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">.editorconfig</code></pre>
</div>

que permite mantener reglas de estilo comunes.

Ejemplo:

<div class="code-card" data-lang="editorconfig">
  <div class="code-label">EDITORCONFIG</div>
  <pre><code class="language-editorconfig"># Editor configuration, see https://editorconfig.org
root = true
&#8203;
[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true
&#8203;
[*.ts]
quote_type = single
ij_typescript_use_double_quotes = false
&#8203;
[*.md]
max_line_length = off
trim_trailing_whitespace = false</code></pre>
</div>

### Ventaja principal

Todo el equipo puede utilizar reglas similares de:

- indentación;
- codificación;
- comillas;
- finales de línea;
- espacios.

---

# 25. Esquema general de funcionamiento

Podemos resumir el arranque de una aplicación Angular así:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">index.html
    │
    ▼
&lt;app-root&gt;
    │
    ▼
main.ts
    │
    ▼
bootstrapApplication(...)
    │
    ├── AppComponent
    │
    └── appConfig
            │
            └── providers / router / servicios</code></pre>
</div>

> 🧠 **Idea clave**  
> Angular arranca desde `main.ts`, carga el componente raíz y su configuración, y renderiza la aplicación dentro del elemento raíz definido en `index.html`.

---

# 26. Primera práctica guiada

## Paso 1. Crear el proyecto

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng new my-app</code></pre>
</div>

## Paso 2. Entrar en la carpeta

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">cd my-app</code></pre>
</div>

## Paso 3. Ejecutar la aplicación

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng serve</code></pre>
</div>

## Paso 4. Abrir en el navegador

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">http://localhost:4200</code></pre>
</div>

## Paso 5. Modificar `app.component.ts`

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'Angular World';
}</code></pre>
</div>

## Paso 6. Mostrar el valor en la plantilla

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;h1&gt;Hello, {{ title }}&lt;/h1&gt;</code></pre>
</div>

## Paso 7. Observar la actualización

Guarda el archivo y comprueba cómo Angular recompila automáticamente la aplicación.

---

# 27. Actividad propuesta 🧪

Modifica la aplicación para que el componente tenga estas propiedades:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'Mi primera aplicación Angular';
  description = 'Aplicación creada en clase';
  author = 'Tu nombre';
}</code></pre>
</div>

Muestra los valores en la plantilla:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;h1&gt;{{ title }}&lt;/h1&gt;
&#8203;
&lt;p&gt;{{ description }}&lt;/p&gt;
&#8203;
&lt;p&gt;Autor: {{ author }}&lt;/p&gt;</code></pre>
</div>

### Objetivo

Comprobar cómo Angular conecta:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Clase TypeScript
        │
        ▼
Propiedades
        │
        ▼
Interpolación
        │
        ▼
Plantilla HTML</code></pre>
</div>

---

# 28. Preguntas de repaso

1. ¿Qué lenguaje utiliza Angular principalmente?
2. ¿Cuál es la función de Angular CLI?
3. ¿Qué comando crea una nueva aplicación?
4. ¿Qué comando ejecuta el servidor de desarrollo?
5. ¿Cuál es la función de `main.ts`?
6. ¿Qué representa `<app-root>`?
7. ¿Qué significa bootstrapping?
8. ¿Para qué sirve `app.config.ts`?
9. ¿Qué función tiene `app.routes.ts`?
10. ¿Qué es la interpolación?
11. ¿Para qué sirve Angular DevTools?
12. ¿Qué ventajas aporta Angular Language Service?
13. ¿Qué es un breakpoint?
14. ¿Para qué sirve `.editorconfig`?

---

# 29. Resumen

En esta unidad hemos visto los fundamentos necesarios para comenzar a trabajar con Angular.

Hemos aprendido que Angular:

- es un framework basado en TypeScript;
- dispone de un ecosistema completo de herramientas;
- puede utilizarse para crear aplicaciones web modernas y escalables;
- integra un CLI que automatiza tareas repetitivas;
- organiza la interfaz en componentes;
- arranca mediante un proceso de bootstrapping;
- extiende HTML mediante una sintaxis de plantillas;
- ofrece herramientas profesionales de depuración y análisis.

También hemos creado una primera aplicación y hemos estudiado el flujo:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">Angular CLI
    ↓
Workspace
    ↓
src/
    ↓
main.ts
    ↓
AppComponent
    ↓
Template HTML
    ↓
Navegador</code></pre>
</div>

---

# 30. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| Angular | Framework web basado en TypeScript |
| Angular CLI | Herramienta de línea de comandos |
| Componente | Unidad básica de interfaz |
| Template | Plantilla HTML del componente |
| Interpolación | Inserción de valores con `{{ ... }}` |
| Bootstrapping | Proceso de arranque |
| Router | Sistema de navegación |
| Provider | Configuración para proporcionar servicios |
| Angular DevTools | Herramienta de inspección y profiling |
| Angular Language Service | Soporte inteligente para plantillas |
| Breakpoint | Punto de parada de depuración |
| EditorConfig | Reglas comunes de formato |

---

## 🔗 Recursos mencionados en el material

- Angular: <https://angular.dev>
- Angular CLI: <https://angular.dev/cli>
- Angular DevTools: <https://angular.dev/tools/devtools>
- Node.js: <https://nodejs.org>
- Git: <https://git-scm.com>
- Visual Studio Code: <https://code.visualstudio.com>

---

> 🎓 **Siguiente paso**  
> Tras dominar la estructura básica de Angular, el siguiente paso natural es profundizar en **TypeScript**, ya que es el lenguaje sobre el que se construye el desarrollo moderno con Angular.

---

# 31. Taller práctico: comprende tu primera aplicación Angular 🧪

Los siguientes ejercicios están pensados para realizarse sobre el código de ejemplo de la unidad:

<https://github.com/hokahey007/codebase-angular/tree/main/01_tu_primera_aplicacion_angular>

El proyecto utiliza **Angular 19** y parte de la aplicación inicial generada con Angular CLI. No se pretende introducir todavía conceptos avanzados: el objetivo es **comprender y manipular las piezas que ya hemos estudiado en esta unidad**.

> 🎯 **Dinámica recomendada**  
> Realiza los ejercicios en orden. Antes de modificar un archivo, intenta explicar qué función cumple y qué esperas que ocurra después del cambio.

---

## Ejercicio 1. Pon en marcha el proyecto y reconoce el workspace

### Objetivo

Relacionar los comandos de Angular CLI con la estructura real de una aplicación Angular.

### Tareas

1. Descarga o clona el repositorio.
2. Entra en la carpeta `01_tu_primera_aplicacion_angular`.
3. Instala las dependencias.
4. Arranca el servidor de desarrollo.
5. Abre la aplicación en el navegador.
6. Localiza en VS Code los siguientes elementos:
   - `src/`
   - `src/app/`
   - `src/index.html`
   - `src/main.ts`
   - `src/styles.css`
   - `angular.json`
   - `package.json`
   - `.editorconfig`

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">git clone https://github.com/hokahey007/aprendiendo-angular-con-codigo.git
cd aprendiendo-angular-con-codigo/01_tu_primera_aplicacion_angular
npm install
npm start</code></pre>
</div>


### Comprueba

- ¿Qué URL utiliza el servidor de desarrollo?
- ¿Qué sucede si modificas un archivo y lo guardas?
- ¿Cuál de las carpetas contiene el código que vas a modificar habitualmente?

> 💡 En este proyecto, el script `npm start` ejecuta internamente `ng serve`.

---

## Ejercicio 2. Investiga `package.json`

### Objetivo

Comprender la relación entre **npm**, **Angular CLI** y las dependencias del proyecto.

Abre `package.json` y localiza la sección `scripts`.

### Tareas

Completa una tabla como la siguiente:

| Script npm | Comando que ejecuta | ¿Para qué sirve? |
|---|---|---|
| `npm start` |  |  |
| `npm run build` |  |  |
| `npm test` |  |  |
| `npm run watch` |  |  |

Después responde:

1. ¿Qué versión principal de Angular utiliza el proyecto?
2. ¿En qué sección aparece `@angular/core`?
3. ¿En qué sección aparece `@angular/cli`?
4. ¿Qué diferencia conceptual observas entre `dependencies` y `devDependencies`?
5. ¿Qué versión de TypeScript utiliza este ejemplo?

### Reto breve

Ejecuta:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng version</code></pre>
</div>


y compara la información mostrada con la que aparece en `package.json`.

---

## Ejercicio 3. Sigue el arranque de Angular paso a paso

### Objetivo

Comprender el proceso de **bootstrapping**.

Sin modificar todavía el código, abre estos archivos en este orden:

1. `src/index.html`
2. `src/main.ts`
3. `src/app/app.component.ts`
4. `src/app/app.component.html`

### Tareas

Completa mentalmente y después por escrito el siguiente flujo:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">index.html
    │
    │ encuentra...
    ▼
&lt;____________&gt;
    │
    │ Angular arranca desde...
    ▼
____________.ts
    │
    │ ejecuta...
    ▼
bootstrapApplication(____________, ____________)
    │
    ▼
AppComponent
    │
    ▼
____________________________</code></pre>
</div>


### Preguntas

1. ¿Qué etiqueta no estándar aparece dentro de `<body>`?
2. ¿Qué propiedad del decorador `@Component` relaciona esa etiqueta con `AppComponent`?
3. ¿Qué archivo inicia realmente la aplicación?
4. ¿Qué dos argumentos recibe `bootstrapApplication(...)`?
5. ¿Qué archivo contiene el HTML que finalmente se muestra en el navegador?

> 🧠 **No memorices el código:** intenta entender la cadena de relaciones entre los archivos.

---

## Ejercicio 4. Experimenta con la interpolación

### Objetivo

Comprobar cómo una propiedad TypeScript se refleja en la plantilla HTML.

En el proyecto de ejemplo, `AppComponent` contiene una propiedad `title`.

Modifícala varias veces:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'DAM 2 - Acceso a Datos';
}</code></pre>
</div>


Localiza en `app.component.html` la expresión que muestra dicha propiedad:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;h1&gt;Hello, {{ title }}&lt;/h1&gt;</code></pre>
</div>


### Tareas

Prueba, una a una, estas situaciones:

1. Cambia `title` por tu nombre.
2. Cámbialo por el nombre del módulo.
3. Sustituye `{{ title }}` en la plantilla por un texto fijo.
4. Restaura después `{{ title }}`.
5. Cambia el nombre de la propiedad TypeScript de `title` a `courseTitle` y actualiza también la plantilla.

### Reflexiona

- ¿Qué ocurre si cambias el nombre en TypeScript pero no en HTML?
- ¿Quién detecta el error mientras escribes?
- ¿Qué ventaja aporta esto frente a escribir HTML y JavaScript sin herramientas específicas?

---

## Ejercicio 5. Amplía el estado del componente

### Objetivo

Entender que una plantilla puede utilizar varias propiedades públicas del componente.

Añade a `AppComponent` estas propiedades:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'Mi primera aplicación Angular';
  module = 'Acceso a Datos';
  course = '2º DAM';
  author = 'Tu nombre';
}</code></pre>
</div>


Ahora modifica una pequeña parte de `app.component.html` para mostrarlas mediante interpolación:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;h1&gt;{{ title }}&lt;/h1&gt;
&lt;p&gt;Módulo: {{ module }}&lt;/p&gt;
&lt;p&gt;Curso: {{ course }}&lt;/p&gt;
&lt;p&gt;Autor: {{ author }}&lt;/p&gt;</code></pre>
</div>


### Tareas

1. Comprueba que todas las propiedades aparecen.
2. Cambia sus valores con `ng serve` activo.
3. Observa cuándo se actualiza el navegador.
4. Introduce intencionadamente un error escribiendo `{{ autor }}` en lugar de `{{ author }}`.

### Explica con tus palabras

¿Qué relación existe entre:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">AppComponent
     │
     ├── propiedades TypeScript
     │
     ▼
interpolación {{ ... }}
     │
     ▼
plantilla HTML
     │
     ▼
navegador</code></pre>
</div>


---

## Ejercicio 6. `app-root`: conecta `index.html` con el componente raíz

### Objetivo

Comprender el papel del **selector**.

En `index.html` aparece:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;body&gt;
  &lt;app-root&gt;&lt;/app-root&gt;
&lt;/body&gt;</code></pre>
</div>


Y en `app.component.ts`:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})</code></pre>
</div>


### Tareas

1. Cambia temporalmente el selector a:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">selector: 'app-principal'</code></pre>
</div>


2. Guarda el archivo sin cambiar `index.html`.
3. Observa qué ocurre.
4. Cambia ahora la etiqueta de `index.html` para que coincida:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;body&gt;
  &lt;app-principal&gt;&lt;/app-principal&gt;
&lt;/body&gt;</code></pre>
</div>


5. Comprueba de nuevo el resultado.
6. Devuelve finalmente ambos archivos a `app-root`.

### Pregunta clave

¿Por qué deben coincidir el `selector` del componente y la etiqueta utilizada en `index.html`?

---

## Ejercicio 7. Investiga la configuración y el router

### Objetivo

Identificar cómo la configuración participa en el arranque aunque todavía no definamos rutas.

Abre `app.config.ts` y `app.routes.ts`.

En el ejemplo, la configuración proporciona el router y el array de rutas está inicialmente vacío.

### Tareas

Completa este esquema:

<div class="code-card" data-lang="text">
  <div class="code-label">SALIDA / TEXTO</div>
  <pre><code class="language-text">main.ts
   │
   └── bootstrapApplication(AppComponent, appConfig)
                                      │
                                      ▼
                                app.config.ts
                                      │
                                      └── provideRouter(________)
                                                           │
                                                           ▼
                                                     app.routes.ts
                                                           │
                                                           ▼
                                                   const routes = ____</code></pre>
</div>


Después responde:

1. ¿Por qué la aplicación puede arrancar aunque `routes` esté vacío?
2. ¿Dónde se registra el router?
3. ¿Dónde se declararían las rutas cuando las necesitemos?
4. ¿Qué ventaja tiene separar `app.config.ts` y `app.routes.ts`?

> 📌 En este ejercicio no es necesario crear rutas. Solo debes comprender dónde se encuentran y cómo se conectan.

---

## Ejercicio 8. Usa Angular Language Service para encontrar errores

### Objetivo

Comprobar cómo las herramientas del editor ayudan a detectar problemas antes de ejecutar la aplicación.

Partiendo del ejercicio 5, escribe temporalmente:

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;p&gt;{{ description }}&lt;/p&gt;</code></pre>
</div>


sin haber creado una propiedad llamada `description`.

### Tareas

1. Observa el aviso de VS Code.
2. Sitúa el cursor sobre el error.
3. Lee el mensaje de diagnóstico.
4. Corrige el problema añadiendo la propiedad:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">description = 'Aprendiendo cómo funciona una aplicación Angular';</code></pre>
</div>


5. Vuelve a la plantilla y empieza a escribir `{{ descr...`.
6. Comprueba si Angular Language Service ofrece autocompletado.

### Reflexiona

¿Por qué es útil detectar este tipo de error **antes** de abrir la aplicación en el navegador?

---

## Ejercicio 9. Inspecciona el componente con Angular DevTools

### Objetivo

Relacionar el código fuente con el estado real del componente en ejecución.

Con la aplicación ejecutándose:

1. Abre las herramientas de desarrollo del navegador.
2. Entra en la pestaña **Angular**.
3. Selecciona **Components**.
4. Localiza `app-root`.
5. Selecciona el componente.

### Tareas

Localiza las propiedades que hayas creado, por ejemplo:

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">title = 'Mi primera aplicación Angular';
module = 'Acceso a Datos';
course = '2º DAM';</code></pre>
</div>


Después:

1. Modifica temporalmente `title` desde Angular DevTools.
2. Observa la página.
3. Recarga el navegador.

### Preguntas

- ¿El cambio realizado desde DevTools modifica el archivo `.ts`?
- ¿Qué ocurre con ese cambio después de recargar?
- ¿Qué diferencia hay entre **modificar el estado en ejecución** y **modificar el código fuente**?

---

## Ejercicio 10. Depura el arranque con VS Code

### Objetivo

Observar cuándo Angular crea el componente raíz.

1. Abre `app.component.ts`.
2. Coloca un breakpoint en la línea donde se inicializa `title`.
3. Inicia la aplicación utilizando **Run and Debug**.
4. Cuando la ejecución se detenga, inspecciona el componente.

### Comprueba

- El valor de `title`.
- El valor de las demás propiedades que añadiste.
- La pila de llamadas disponible en el depurador.
- Qué ocurre al pulsar **Continue**.

### Después

Cambia el valor de `title`, vuelve a ejecutar la sesión de depuración y comprueba que el breakpoint se alcanza de nuevo.

> 🔎 La finalidad no es memorizar el depurador, sino entender que podemos detener la aplicación y estudiar su estado durante la ejecución.

---

## Ejercicio 11. Experimenta con `.editorconfig`

### Objetivo

Relacionar la configuración del proyecto con el estilo uniforme del código.

Abre `.editorconfig`.

### Tareas

Localiza las reglas que indican:

- codificación UTF-8;
- indentación con espacios;
- tamaño de indentación;
- comillas simples para TypeScript;
- eliminación de espacios finales.

Crea temporalmente un pequeño fragmento mal indentado en `app.component.ts` y utiliza el formateador de VS Code.

### Pregunta

¿Qué ventaja aporta mantener estas reglas dentro del propio repositorio en lugar de depender únicamente de la configuración personal de cada programador?

---

## Ejercicio 12. Diferencia desarrollo y compilación

### Objetivo

Distinguir `ng serve` de `ng build`.

Primero ejecuta:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng serve</code></pre>
</div>


Después detén el servidor y ejecuta:

<div class="code-card" data-lang="bash">
  <div class="code-label">TERMINAL / BASH</div>
  <pre><code class="language-bash">ng build</code></pre>
</div>


### Tareas

1. Explica qué hace cada comando.
2. Localiza la carpeta generada por la compilación.
3. Comprueba que `ng build` no deja un servidor esperando peticiones.
4. Identifica en `package.json` qué script npm permite ejecutar la compilación.

### Completa

| Acción | `ng serve` | `ng build` |
|---|:---:|:---:|
| Compila la aplicación | ✅ | ✅ |
| Inicia servidor de desarrollo |  |  |
| Vigila cambios mientras desarrollas |  |  |
| Genera artefactos de compilación |  |  |

---

# 32. Reto integrador: transforma la página inicial 🚀

### Objetivo

Aplicar en una única práctica los conceptos principales de la unidad sin introducir todavía componentes adicionales.

Partiendo del repositorio de ejemplo, transforma la página inicial para convertirla en una **portada del módulo Acceso a Datos**.

## Requisitos

Debes mantener:

- `AppComponent` como componente raíz;
- el proceso de bootstrapping existente;
- el selector `app-root`;
- `app.config.ts`;
- `app.routes.ts`.

Debes modificar:

- `app.component.ts`;
- `app.component.html`;
- opcionalmente `app.component.css`;
- el `<title>` de `index.html`.

### Estado mínimo del componente

<div class="code-card" data-lang="typescript">
  <div class="code-label">TYPESCRIPT</div>
  <pre><code class="language-typescript">export class AppComponent {
  title = 'Acceso a Datos';
  course = '2º DAM';
  unit = 'Unidad 1 - Tu primera aplicación Angular';
  author = 'Tu nombre';
}</code></pre>
</div>


### La plantilla debe mostrar como mínimo

- el título;
- el curso;
- la unidad;
- el autor;
- un texto breve indicando que la aplicación está funcionando.

Todos los datos anteriores deben obtenerse mediante **interpolación**, no escribiéndolos directamente como texto fijo cuando ya existen como propiedades del componente.

### Ejemplo de estructura mínima

<div class="code-card" data-lang="html">
  <div class="code-label">HTML</div>
  <pre><code class="language-html">&lt;main&gt;
  &lt;h1&gt;{{ title }}&lt;/h1&gt;
  &lt;h2&gt;{{ unit }}&lt;/h2&gt;
&#8203;
  &lt;p&gt;Curso: {{ course }}&lt;/p&gt;
  &lt;p&gt;Autor: {{ author }}&lt;/p&gt;
&#8203;
  &lt;p&gt;Mi primera aplicación Angular está funcionando.&lt;/p&gt;
&lt;/main&gt;</code></pre>
</div>


## Comprobaciones finales

Antes de considerar terminada la práctica:

- [ ] La aplicación arranca sin errores.
- [ ] `ng serve` recompila al guardar.
- [ ] El selector de `AppComponent` coincide con `index.html`.
- [ ] Todas las interpolaciones corresponden a propiedades existentes.
- [ ] Angular Language Service no muestra errores.
- [ ] Puedes localizar `AppComponent` con Angular DevTools.
- [ ] Has probado al menos un breakpoint con VS Code.
- [ ] `ng build` finaliza correctamente.

---

# 33. Cuestiones de reflexión para entregar 📝

Responde brevemente, sin copiar literalmente los apuntes:

1. Describe el recorrido desde que el navegador carga `index.html` hasta que se muestra la plantilla de `AppComponent`.
2. Explica qué relación existe entre `selector`, `<app-root>` y `AppComponent`.
3. ¿Qué diferencia existe entre `main.ts`, `app.config.ts` y `app.component.ts`?
4. ¿Por qué `app.routes.ts` puede estar vacío y la aplicación seguir funcionando?
5. ¿Qué aporta la interpolación frente a escribir directamente el texto en HTML?
6. ¿Qué diferencia existe entre cambiar una propiedad desde Angular DevTools y cambiarla en el archivo TypeScript?
7. ¿Cuándo utilizarías `ng serve` y cuándo `ng build`?
8. ¿Cómo te puede ayudar Angular Language Service durante el desarrollo?
9. ¿Qué utilidad tiene un breakpoint frente a utilizar repetidamente `console.log(...)`?
10. Explica con tus palabras qué parte de la aplicación modificarías si quisieras cambiar:
    - el título de la pestaña del navegador;
    - el texto mostrado en la página;
    - el valor de una propiedad del componente;
    - la configuración global de Angular.

---

> ✅ **Meta de aprendizaje**  
> Si puedes realizar el reto integrador y explicar las preguntas anteriores sin limitarte a copiar código, ya tienes una buena comprensión de la estructura y del arranque de una aplicación Angular básica.
