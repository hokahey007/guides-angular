# ⚙️ Guía de ficheros de configuración de un proyecto Angular

> [!NOTE]
> Esta guía explica los principales ficheros de configuración generados por **Angular CLI** en un proyecto Angular:
>
> - `package.json`
> - `package-lock.json`
> - `tsconfig.json`
> - `tsconfig.app.json`
> - `tsconfig.spec.json`
> - `angular.json`

> [!TIP]
> La idea fundamental es sencilla:
>
> ```text
> package*.json   → npm
> tsconfig*.json  → TypeScript
> angular.json    → Angular CLI
> ```

---

## 📚 Índice

- [[#1. Visión general|1. Visión general]]
- [[#2. `package.json`|2. package.json]]
- [[#3. Scripts npm|3. Scripts npm]]
- [[#4. `dependencies`|4. dependencies]]
- [[#5. `devDependencies`|5. devDependencies]]
- [[#6. Rangos de versiones|6. Rangos de versiones]]
- [[#7. `package-lock.json`|7. package-lock.json]]
- [[#8. ¿Debemos modificar `package-lock.json` manualmente?|8. ¿Debemos modificar package-lock.json manualmente?]]
- [[#9. ¿Se debe subir `package-lock.json` a Git?|9. ¿Se debe subir package-lock.json a Git?]]
- [[#10. `npm install` frente a `npm ci`|10. npm install frente a npm ci]]
- [[#11. Los tres ficheros `tsconfig`|11. Los tres ficheros tsconfig]]
- [[#12. `tsconfig.json`|12. tsconfig.json]]
- [[#13. `compilerOptions`|13. compilerOptions]]
- [[#14. `strict`|14. strict]]
- [[#15. `angularCompilerOptions`|15. angularCompilerOptions]]
- [[#16. `tsconfig.app.json`|16. tsconfig.app.json]]
- [[#17. Herencia mediante `extends`|17. Herencia mediante extends]]
- [[#18. `files`, `include` y `exclude`|18. files, include y exclude]]
- [[#19. `tsconfig.spec.json`|19. tsconfig.spec.json]]
- [[#20. Los ficheros `.spec.ts`|20. Los ficheros .spec.ts]]
- [[#21. Resumen de los tres `tsconfig`|21. Resumen de los tres tsconfig]]
- [[#22. `angular.json`|22. angular.json]]
- [[#23. `projects`|23. projects]]
- [[#24. `projectType`|24. projectType]]
- [[#25. `sourceRoot`|25. sourceRoot]]
- [[#26. `prefix`|26. prefix]]
- [[#27. `architect`|27. architect]]
- [[#28. `build`|28. build]]
- [[#29. `styles`|29. styles]]
- [[#30. `assets`|30. assets]]
- [[#31. `serve`|31. serve]]
- [[#32. `test`|32. test]]
- [[#33. Configuraciones de desarrollo y producción|33. Configuraciones de desarrollo y producción]]
- [[#34. Relación con los comandos Angular CLI|34. Relación con los comandos Angular CLI]]
- [[#35. Modificar `angular.json` desde Angular CLI|35. Modificar angular.json desde Angular CLI]]
- [[#36. Relación entre todos los ficheros|36. Relación entre todos los ficheros]]
- [[#37. ¿Qué ocurre al ejecutar `npm start`?|37. ¿Qué ocurre al ejecutar npm start?]]
- [[#38. ¿Qué ocurre al ejecutar `ng build`?|38. ¿Qué ocurre al ejecutar ng build?]]
- [[#39. ¿Qué fichero debemos modificar?|39. ¿Qué fichero debemos modificar?]]
- [[#40. Ficheros que normalmente modificaremos|40. Ficheros que normalmente modificaremos]]
- [[#41. Una regla sencilla para recordarlo|41. Una regla sencilla para recordarlo]]
- [[#42. Ideas fundamentales|42. Ideas fundamentales]]

---

## 🎯 Objetivo

Cuando Angular CLI crea un proyecto mediante:

```bash
ng new mi-aplicacion
```

no genera únicamente el código de nuestra aplicación. También crea una serie de **ficheros de configuración** que determinan cómo se instalan las dependencias, cómo se compila TypeScript, cómo se ejecutan las pruebas y cómo Angular CLI construye o sirve la aplicación.

Una estructura habitual será:

```text
mi-aplicacion/
│
├── angular.json
├── package.json
├── package-lock.json
│
├── tsconfig.json
├── tsconfig.app.json
├── tsconfig.spec.json
│
├── public/
│
├── src/
│   ├── app/
│   ├── index.html
│   ├── main.ts
│   └── styles.css
│
└── node_modules/
```

Los seis ficheros que estudiaremos pueden dividirse en tres grandes grupos:

| Ficheros | Responsabilidad |
|---|---|
| `package.json` / `package-lock.json` | Gestión de **Node.js y npm** |
| `tsconfig.json` / `tsconfig.app.json` / `tsconfig.spec.json` | Configuración de **TypeScript** |
| `angular.json` | Configuración de **Angular CLI** |

---

## 1. Visión general

Podemos entender la relación entre estos ficheros de la siguiente forma:

```text
                     PROYECTO ANGULAR
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
         ▼                 ▼                 ▼
       npm             TypeScript        Angular CLI
         │                 │                 │
         ▼                 ▼                 ▼
   package.json      tsconfig.json       angular.json
         │             /       \
         ▼            /         \
package-lock.json    ▼           ▼
              tsconfig.app   tsconfig.spec
```

Cada tecnología se ocupa de una parte diferente del proyecto:

- **npm** administra paquetes y dependencias.
- **TypeScript** decide cómo se transforma nuestro código TypeScript en JavaScript.
- **Angular CLI** coordina tareas como desarrollo, compilación, pruebas, recursos, estilos y configuraciones de producción.

> [!IMPORTANT]
> Estos ficheros **no funcionan de manera aislada**. Angular CLI, TypeScript y npm colaboran entre sí para construir y ejecutar la aplicación.

---

## 2. `package.json`

El fichero:

```text
package.json
```

es uno de los ficheros fundamentales de cualquier proyecto basado en Node.js.

> [!NOTE]
> `package.json` **no es específico de Angular**. Forma parte del ecosistema Node.js/npm.

Su función principal es **describir el proyecto y declarar las dependencias que necesita**.

Un `package.json` simplificado podría tener esta estructura:

```json
{
  "name": "mi-aplicacion",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  },
  "dependencies": {
    "@angular/common": "^19.0.0",
    "@angular/compiler": "^19.0.0",
    "@angular/core": "^19.0.0",
    "@angular/forms": "^19.0.0",
    "@angular/platform-browser": "^19.0.0",
    "@angular/router": "^19.0.0",
    "rxjs": "~7.8.0",
    "zone.js": "~0.15.0"
  },
  "devDependencies": {
    "@angular/cli": "^19.0.0",
    "@angular/compiler-cli": "^19.0.0",
    "typescript": "~5.6.0"
  }
}
```

> [!WARNING]
> Las versiones anteriores son únicamente un ejemplo. Las versiones concretas dependen de la versión de Angular con la que se haya creado el proyecto.

### 2.1. `name`

Identifica el proyecto:

```json
"name": "mi-aplicacion"
```

Normalmente coincide con el nombre indicado al ejecutar:

```bash
ng new mi-aplicacion
```

### 2.2. `version`

Indica la versión del proyecto:

```json
"version": "0.0.0"
```

Puede modificarse siguiendo las reglas habituales de **Semantic Versioning**:

```text
MAJOR.MINOR.PATCH
```

Por ejemplo:

```text
1.4.2
```

---

## 3. Scripts npm

La sección:

```json
"scripts": {
  "ng": "ng",
  "start": "ng serve",
  "build": "ng build",
  "watch": "ng build --watch --configuration development",
  "test": "ng test"
}
```

define comandos que podemos ejecutar mediante npm.

Por ejemplo:

```bash
npm start
```

ejecutará:

```bash
ng serve
```

De la misma forma:

```bash
npm run build
```

ejecutará:

```bash
ng build
```

y:

```bash
npm test
```

ejecutará:

```bash
ng test
```

> [!TIP]
> Los scripts permiten que todos los desarrolladores utilicen los mismos comandos sin necesidad de recordar todos los parámetros internos.

---

## 4. `dependencies`

La sección:

```json
"dependencies": {
  "@angular/core": "...",
  "@angular/forms": "...",
  "@angular/router": "...",
  "rxjs": "..."
}
```

contiene paquetes de los que **depende la propia aplicación**.

Por ejemplo:

```json
"@angular/core": "^19.0.0"
```

indica que el proyecto utiliza el núcleo de Angular.

Otros paquetes habituales son:

```text
@angular/common
@angular/forms
@angular/router
rxjs
```

---

## 5. `devDependencies`

La sección:

```json
"devDependencies": {
  "@angular/cli": "...",
  "@angular/compiler-cli": "...",
  "typescript": "..."
}
```

contiene herramientas necesarias principalmente durante el **desarrollo, compilación o pruebas**.

Ejemplos:

```text
@angular/cli
```

proporciona Angular CLI.

```text
typescript
```

proporciona el compilador TypeScript.

Podemos resumirlo así:

| Sección | Uso principal |
|---|---|
| `dependencies` | Paquetes necesarios por la aplicación |
| `devDependencies` | Herramientas utilizadas durante el desarrollo |

---

## 6. Rangos de versiones

En `package.json` podemos encontrar:

```json
"@angular/core": "^19.0.0"
```

o:

```json
"rxjs": "~7.8.0"
```

Estos caracteres tienen significado.

### `^`

Por ejemplo:

```text
^19.0.0
```

permite normalmente actualizar dentro de la misma versión mayor:

```text
19.x.x
```

### `~`

Por ejemplo:

```text
~7.8.0
```

es más restrictivo y permite normalmente actualizaciones de parche:

```text
7.8.x
```

Por tanto, `package.json` puede indicar un **rango de versiones permitido**, no necesariamente una versión exacta.

Aquí entra en juego `package-lock.json`.

---

## 7. `package-lock.json`

Cuando ejecutamos:

```bash
npm install
```

npm genera o actualiza:

```text
package-lock.json
```

Este fichero contiene el **árbol concreto de dependencias que npm ha resuelto**.

Mientras `package.json` puede decir:

```json
"@angular/core": "^19.0.0"
```

`package-lock.json` registra la versión exacta que npm ha instalado.

Conceptualmente:

```text
package.json
      │
      │ permite
      ▼
   ^19.0.0
      │
      │ npm resuelve
      ▼
package-lock.json
      │
      ▼
 versión exacta
```

> [!IMPORTANT]
> `package.json` expresa **qué versiones se permiten**.
>
> `package-lock.json` registra **qué versiones concretas se han resuelto**.

---

## 8. ¿Debemos modificar `package-lock.json` manualmente?

Normalmente:

> [!WARNING]
> **No debemos editar `package-lock.json` manualmente.**

Debe ser administrado por npm.

Comandos como:

```bash
npm install
```

```bash
npm install bootstrap
```

```bash
npm uninstall bootstrap
```

```bash
npm update
```

pueden modificar automáticamente:

```text
package.json
package-lock.json
```

Modificar manualmente `package-lock.json` puede dejar el árbol de dependencias en un estado incoherente.

---

## 9. ¿Se debe subir `package-lock.json` a Git?

Sí.

Lo habitual es versionar:

```text
package.json
package-lock.json
```

y **no versionar**:

```text
node_modules/
```

Por ejemplo:

```text
proyecto/
├── package.json          ✔ Git
├── package-lock.json     ✔ Git
└── node_modules/         ✘ Git
```

Esto permite descargar un proyecto desde Git y reconstruir sus dependencias sin almacenar miles de archivos de `node_modules`.

---

## 10. `npm install` frente a `npm ci`

Durante el desarrollo utilizaremos normalmente:

```bash
npm install
```

Sin embargo, cuando queremos reproducir una instalación basada estrictamente en `package-lock.json`, especialmente en integración continua, podemos utilizar:

```bash
npm ci
```

### Diferencias principales

| `npm install` | `npm ci` |
|---|---|
| Uso habitual durante desarrollo | Especialmente útil en CI/CD |
| Puede modificar `package-lock.json` | No modifica el lockfile |
| Tolera determinados cambios | Exige coherencia con `package-lock.json` |
| Puede conservar `node_modules` | Parte de una instalación limpia |

`npm ci` es especialmente habitual en:

```text
GitHub Actions
GitLab CI
Jenkins
Docker
servidores de construcción
```

---

## 11. Los tres ficheros `tsconfig`

TypeScript utiliza ficheros:

```text
tsconfig*.json
```

para determinar **cómo debe compilarse el código TypeScript**.

En una aplicación Angular estándar encontraremos normalmente:

```text
tsconfig.json
tsconfig.app.json
tsconfig.spec.json
```

Su relación es:

```text
                  tsconfig.json
               configuración base
                      /    \
                     /      \
                    ▼        ▼
         tsconfig.app.json  tsconfig.spec.json
             aplicación          tests
```

---

## 12. `tsconfig.json`

`tsconfig.json` contiene la **configuración general de TypeScript**.

Un ejemplo simplificado:

```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "outDir": "./dist/out-tsc",
    "strict": true,
    "noImplicitOverride": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "sourceMap": true,
    "declaration": false,
    "experimentalDecorators": true,
    "moduleResolution": "bundler",
    "target": "ES2022",
    "module": "ES2022"
  },
  "angularCompilerOptions": {
    "strictInjectionParameters": true,
    "strictInputAccessModifiers": true,
    "strictTemplates": true
  }
}
```

> [!NOTE]
> El contenido exacto puede variar entre versiones de Angular y TypeScript.

---

## 13. `compilerOptions`

La sección:

```json
"compilerOptions": {
}
```

pertenece a TypeScript.

Algunas propiedades importantes son:

| Propiedad | Función |
|---|---|
| `target` | Versión de JavaScript objetivo |
| `module` | Formato de módulos generado |
| `moduleResolution` | Estrategia para localizar módulos |
| `strict` | Activa comprobaciones estrictas de tipos |
| `sourceMap` | Genera información útil para depuración |
| `outDir` | Directorio de salida |
| `noImplicitReturns` | Detecta funciones con retornos incompletos |
| `noFallthroughCasesInSwitch` | Detecta determinados errores en `switch` |
| `noImplicitOverride` | Exige indicar explícitamente determinadas sobrescrituras |

---

## 14. `strict`

Una de las opciones más importantes es:

```json
"strict": true
```

Activa un conjunto de comprobaciones estrictas de TypeScript.

Por ejemplo:

```typescript
let edad: number;

edad = "veinte";
```

TypeScript detectará el problema:

```text
Type 'string' is not assignable to type 'number'
```

El objetivo es localizar determinados errores **durante el desarrollo**, antes de que lleguen a producirse durante la ejecución.

---

## 15. `angularCompilerOptions`

Además de las opciones de TypeScript, Angular incorpora sus propias opciones:

```json
"angularCompilerOptions": {
  "strictInjectionParameters": true,
  "strictInputAccessModifiers": true,
  "strictTemplates": true
}
```

Estas opciones afectan especialmente al compilador y al sistema de plantillas de Angular.

Por ejemplo:

```json
"strictTemplates": true
```

permite realizar comprobaciones de tipos más estrictas dentro de las plantillas HTML.

---

## 16. `tsconfig.app.json`

El fichero:

```text
tsconfig.app.json
```

contiene la configuración TypeScript específica de **la aplicación**.

Normalmente comienza heredando de:

```text
tsconfig.json
```

mediante:

```json
{
  "extends": "./tsconfig.json"
}
```

Un ejemplo simplificado sería:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": []
  },
  "files": [
    "src/main.ts"
  ],
  "include": [
    "src/**/*.d.ts"
  ]
}
```

La configuración efectiva será la combinación:

```text
tsconfig.json
     +
tsconfig.app.json
```

---

## 17. Herencia mediante `extends`

Supongamos:

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022"
  }
}
```

en `tsconfig.json`.

Y:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app"
  }
}
```

en `tsconfig.app.json`.

La configuración resultante será conceptualmente:

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "outDir": "./out-tsc/app"
  }
}
```

> [!TIP]
> `extends` permite definir una configuración común una sola vez y especializarla después para cada contexto.

---

## 18. `files`, `include` y `exclude`

TypeScript permite determinar qué archivos forman parte de una compilación.

### `files`

```json
"files": [
  "src/main.ts"
]
```

indica explícitamente determinados archivos.

### `include`

```json
"include": [
  "src/**/*.d.ts"
]
```

permite seleccionar archivos mediante patrones.

Por ejemplo:

```text
src/**/*.ts
```

representaría los ficheros TypeScript situados dentro de `src`.

### `exclude`

También puede aparecer:

```json
"exclude": [
  "node_modules"
]
```

para excluir determinados patrones del proceso de búsqueda realizado por `include`.

---

## 19. `tsconfig.spec.json`

El tercer fichero TypeScript habitual es:

```text
tsconfig.spec.json
```

Su finalidad es configurar la compilación de los **tests**.

También hereda normalmente de:

```text
tsconfig.json
```

Por ejemplo:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/spec"
  },
  "include": [
    "src/**/*.spec.ts",
    "src/**/*.d.ts"
  ]
}
```

La diferencia fundamental es:

```text
tsconfig.app.json
        │
        ▼
 código de la aplicación


tsconfig.spec.json
        │
        ▼
 código de pruebas
```

---

## 20. Los ficheros `.spec.ts`

Cuando Angular genera determinados elementos, puede crear pruebas como:

```text
app.component.spec.ts
```

o:

```text
producto.service.spec.ts
```

Estos archivos corresponden al código de pruebas.

Por ejemplo:

```text
src/app/app.component.ts
```

corresponde al código de la aplicación.

Mientras:

```text
src/app/app.component.spec.ts
```

corresponde al código utilizado para comprobar su funcionamiento.

---

## 21. Resumen de los tres `tsconfig`

| Fichero | Función |
|---|---|
| `tsconfig.json` | Configuración TypeScript común |
| `tsconfig.app.json` | Configuración para compilar la aplicación |
| `tsconfig.spec.json` | Configuración para compilar las pruebas |

La relación fundamental es:

```text
                 tsconfig.json
              configuración base
                /           \
               /             \
              ▼               ▼
  tsconfig.app.json     tsconfig.spec.json
       aplicación              tests
```

> [!IMPORTANT]
> Los ficheros especializados heredan normalmente de `tsconfig.json`, evitando duplicar configuraciones.

---

## 22. `angular.json`

El fichero:

```text
angular.json
```

es uno de los ficheros de configuración más importantes **específicos de Angular CLI**.

Se encuentra en la raíz del workspace y contiene opciones globales y configuraciones específicas para uno o varios proyectos.

Una estructura simplificada sería:

```json
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "mi-aplicacion": {
      "projectType": "application",
      "root": "",
      "sourceRoot": "src",
      "prefix": "app",
      "architect": {
        "build": {
        },
        "serve": {
        },
        "test": {
        }
      }
    }
  }
}
```

---

## 23. `projects`

Una de las secciones fundamentales es:

```json
"projects": {
}
```

Un workspace Angular puede contener **uno o varios proyectos**.

Por ejemplo:

```json
"projects": {
  "tienda": {
  }
}
```

Pero también podría contener:

```json
"projects": {
  "tienda": {
  },
  "administracion": {
  },
  "componentes": {
  }
}
```

Esto permite trabajar con workspaces más complejos que contienen aplicaciones y bibliotecas.

---

## 24. `projectType`

Dentro de cada proyecto podemos encontrar:

```json
"projectType": "application"
```

Los valores principales son:

```text
application
library
```

Una **aplicación** puede ejecutarse directamente.

Una **biblioteca** contiene código reutilizable que puede ser empleado por otras aplicaciones.

---

## 25. `sourceRoot`

La propiedad:

```json
"sourceRoot": "src"
```

indica dónde se encuentra el código fuente principal del proyecto.

```text
sourceRoot
    │
    ▼
   src/
```

---

## 26. `prefix`

La propiedad:

```json
"prefix": "app"
```

establece el prefijo utilizado normalmente al generar selectores.

Por ejemplo:

```typescript
selector: 'app-producto'
```

donde:

```text
app
```

es el prefijo.

---

## 27. `architect`

Una de las partes más importantes de `angular.json` es:

```json
"architect": {
}
```

Aquí se configuran diferentes **tareas o targets** que Angular CLI puede realizar.

Conceptualmente:

```text
architect
│
├── build
│
├── serve
│
├── test
└── extract-i18n
```

Estas tareas están relacionadas con comandos de Angular CLI.

---

## 28. `build`

La sección:

```json
"build": {
}
```

configura la construcción de la aplicación.

Está relacionada con:

```bash
ng build
```

Puede contener opciones como:

```json
"options": {
  "outputPath": "dist/mi-aplicacion",
  "index": "src/index.html",
  "browser": "src/main.ts",
  "tsConfig": "tsconfig.app.json",
  "assets": [
    "public"
  ],
  "styles": [
    "src/styles.css"
  ]
}
```

Aquí aparece una conexión muy importante:

```json
"tsConfig": "tsconfig.app.json"
```

Angular CLI indica qué configuración TypeScript debe utilizar para construir la aplicación.

```text
ng build
   │
   ▼
angular.json
   │
   ▼
tsconfig.app.json
   │
   ▼
tsconfig.json
```

---

## 29. `styles`

En:

```json
"styles": [
  "src/styles.css"
]
```

indicamos las hojas de estilo globales.

Si instalamos una biblioteca CSS, también podríamos añadir sus estilos:

```json
"styles": [
  "src/styles.css",
  "node_modules/alguna-libreria/estilos.css"
]
```

> [!NOTE]
> Los estilos definidos aquí son **globales para la aplicación**.

---

## 30. `assets`

Los recursos estáticos se configuran mediante opciones como:

```json
"assets": [
  "public"
]
```

Estos recursos no necesitan ser compilados como TypeScript.

Pueden ser, por ejemplo:

- imágenes;
- fuentes;
- iconos;
- ficheros JSON;
- documentos.

Angular puede copiarlos al resultado final durante el proceso de construcción.

---

## 31. `serve`

La sección:

```json
"serve": {
}
```

está relacionada con:

```bash
ng serve
```

El comando inicia el servidor de desarrollo de Angular.

Por ejemplo:

```bash
ng serve
```

o:

```bash
ng serve -o
```

La opción:

```text
-o
```

equivale a:

```text
--open
```

y abre automáticamente la aplicación en el navegador.

---

## 32. `test`

La configuración:

```json
"test": {
}
```

está relacionada con:

```bash
ng test
```

Angular CLI utiliza este target para ejecutar las pruebas configuradas para el proyecto.

La relación conceptual es:

```text
ng test
    │
    ▼
angular.json
    │
    ▼
configuración test
    │
    ▼
tsconfig.spec.json
```

---

## 33. Configuraciones de desarrollo y producción

Dentro de determinados targets podemos encontrar:

```json
"configurations": {
  "production": {
  },
  "development": {
  }
}
```

Esto permite establecer comportamientos distintos según el entorno.

Por ejemplo:

```bash
ng build --configuration production
```

utilizará la configuración:

```text
production
```

Mientras:

```bash
ng build --configuration development
```

utilizará:

```text
development
```

> [!TIP]
> Las configuraciones permiten sobrescribir determinados valores definidos en las opciones generales de un target.

---

## 34. Relación con los comandos Angular CLI

Podemos simplificar la relación de la siguiente forma:

| Comando | Configuración principal |
|---|---|
| `ng build` | `architect.build` |
| `ng serve` | `architect.serve` |
| `ng test` | `architect.test` |
| `ng extract-i18n` | `architect.extract-i18n` |

Cuando ejecutamos:

```bash
ng build
```

Angular CLI consulta la configuración del workspace para determinar **cómo debe realizar esa tarea**.

---

## 35. Modificar `angular.json` desde Angular CLI

Podemos editar directamente:

```text
angular.json
```

pero Angular CLI también proporciona:

```bash
ng config
```

Por ejemplo, podemos consultar determinados valores mediante:

```bash
ng config projects
```

> [!TIP]
> Cuando exista una opción equivalente en Angular CLI, suele ser preferible utilizarla antes que modificar configuraciones complejas manualmente.

---

## 36. Relación entre todos los ficheros

El proceso completo puede representarse así:

```text
                    package.json
                         │
                         │ npm install
                         ▼
                 package-lock.json
                         │
                         ▼
                    node_modules
                         │
                         ▼
                    Angular CLI
                         │
                         ▼
                    angular.json
                    /     |      \
                   /      |       \
                  ▼       ▼        ▼
              build      serve    test
                │                   │
                ▼                   ▼
       tsconfig.app.json    tsconfig.spec.json
                \                   /
                 \                 /
                  ▼               ▼
                    tsconfig.json
                         │
                         ▼
                     TypeScript
```

Cada fichero tiene una responsabilidad diferente, pero todos forman parte del mismo proceso de construcción y ejecución.

---

## 37. ¿Qué ocurre al ejecutar `npm start`?

Supongamos que `package.json` contiene:

```json
"scripts": {
  "start": "ng serve"
}
```

Cuando ejecutamos:

```bash
npm start
```

ocurre conceptualmente:

```text
npm start
    │
    ▼
package.json
    │
    ▼
"start": "ng serve"
    │
    ▼
Angular CLI
    │
    ▼
angular.json
    │
    ▼
architect → serve
    │
    ▼
construcción de la aplicación
    │
    ▼
tsconfig.app.json
    │
    ▼
tsconfig.json
    │
    ▼
TypeScript
    │
    ▼
aplicación en el navegador
```

> [!IMPORTANT]
> `npm start` no ejecuta Angular por sí mismo. npm consulta `package.json`, encuentra el script correspondiente y desde ahí se inicia Angular CLI.

---

## 38. ¿Qué ocurre al ejecutar `ng build`?

El proceso simplificado sería:

```text
ng build
   │
   ▼
angular.json
   │
   ▼
architect.build
   │
   ├── main.ts
   ├── index.html
   ├── styles
   ├── assets
   │
   └── tsconfig.app.json
             │
             ▼
        tsconfig.json
             │
             ▼
         compilación
             │
             ▼
           dist/
```

Como resultado obtenemos una aplicación preparada para ser desplegada.

---

## 39. ¿Qué fichero debemos modificar?

Ante un cambio en el proyecto conviene preguntarse primero **qué queremos configurar**.

| Necesidad | Fichero |
|---|---|
| Añadir una dependencia | `package.json`, normalmente mediante npm |
| Conocer versiones exactas instaladas | `package-lock.json` |
| Crear un script npm | `package.json` |
| Configurar TypeScript globalmente | `tsconfig.json` |
| Configurar la compilación TS de la aplicación | `tsconfig.app.json` |
| Configurar TypeScript para los tests | `tsconfig.spec.json` |
| Añadir estilos globales | `angular.json` |
| Configurar assets | `angular.json` |
| Configurar `ng build` | `angular.json` |
| Configurar `ng serve` | `angular.json` |
| Configurar diferentes entornos de build | `angular.json` |

---

## 40. Ficheros que normalmente modificaremos

No todos los ficheros tienen el mismo grado de manipulación manual.

### `package.json`

Podemos modificarlo, aunque para añadir o eliminar paquetes es preferible utilizar:

```bash
npm install paquete
npm uninstall paquete
```

### `package-lock.json`

> [!WARNING]
> No deberíamos editarlo manualmente.

### `tsconfig.json`

Puede modificarse cuando sea necesario cambiar opciones generales de TypeScript.

### `tsconfig.app.json`

Solo suele modificarse ante necesidades específicas de compilación de la aplicación.

### `tsconfig.spec.json`

Normalmente solo se modifica cuando necesitamos adaptar el entorno de pruebas.

### `angular.json`

Puede modificarse para configurar:

- compilaciones;
- recursos;
- estilos;
- targets;
- configuraciones de desarrollo y producción;
- comportamiento de diferentes herramientas de Angular CLI.

---

## 41. Una regla sencilla para recordarlo

Podemos memorizar los ficheros mediante tres preguntas:

```text
¿QUÉ PAQUETES NECESITO?
        │
        ▼
   package.json
        +
 package-lock.json


¿CÓMO COMPILO TYPESCRIPT?
        │
        ▼
    tsconfig.json
     /         \
tsconfig.app  tsconfig.spec


¿CÓMO FUNCIONA MI PROYECTO ANGULAR?
        │
        ▼
    angular.json
```

O todavía más resumido:

```text
package*.json   → npm
tsconfig*.json  → TypeScript
angular.json    → Angular CLI
```

---

## 42. Ideas fundamentales

> [!IMPORTANT]
> ### ✅ `package.json`
>
> Describe el proyecto Node.js, contiene scripts y declara las dependencias.

> [!IMPORTANT]
> ### ✅ `package-lock.json`
>
> Registra las versiones concretas de las dependencias resueltas por npm y permite reproducir instalaciones de forma consistente.

> [!IMPORTANT]
> ### ✅ `tsconfig.json`
>
> Contiene la configuración TypeScript común del proyecto.

> [!IMPORTANT]
> ### ✅ `tsconfig.app.json`
>
> Especializa la configuración TypeScript para compilar la aplicación.

> [!IMPORTANT]
> ### ✅ `tsconfig.spec.json`
>
> Especializa la configuración TypeScript para compilar y ejecutar las pruebas.

> [!IMPORTANT]
> ### ✅ `angular.json`
>
> Configura el workspace de Angular CLI y determina cómo se realizan operaciones como `ng build`, `ng serve` y `ng test`.

La relación final que debemos conservar es:

```text
                  PROYECTO ANGULAR
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
       npm          TypeScript       Angular CLI
        │               │                │
        ▼               ▼                ▼
 package.json      tsconfig.json      angular.json
        │            /      \
        ▼           ▼        ▼
package-lock   tsconfig.app  tsconfig.spec
```

> [!TIP]
> Comprender estos ficheros permite dejar de verlos como archivos generados automáticamente y empezar a entender **cómo npm, TypeScript y Angular CLI colaboran para construir, ejecutar y probar una aplicación Angular**.
