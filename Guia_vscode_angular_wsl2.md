# 🧩 Guía de instalación de plugins en VS Code para Angular con WSL2

> **Entorno recomendado:** Windows 11 + WSL2 + Ubuntu + Node.js + Visual Studio Code  
> **Objetivo:** configurar un entorno de desarrollo moderno, homogéneo y adecuado para trabajar con Angular y Node.js desde Windows utilizando Linux mediante WSL2.

---

## 📑 Índice

1. [¿Por qué utilizar WSL2?](#1-por-qué-utilizar-wsl2)
2. [Arquitectura recomendada](#2-arquitectura-recomendada)
3. [Comprobar Node.js dentro de WSL](#3-comprobar-nodejs-dentro-de-wsl)
4. [Instalar Visual Studio Code](#4-instalar-visual-studio-code)
5. [Instalar la extensión WSL](#5-instalar-la-extensión-wsl)
6. [Abrir VS Code desde WSL](#6-abrir-vs-code-desde-wsl)
7. [¿Dónde se instalan las extensiones?](#7-dónde-se-instalan-las-extensiones)
8. [Extensiones básicas recomendadas](#8-extensiones-básicas-recomendadas)
9. [Angular Language Service](#9-angular-language-service)
10. [ESLint](#10-eslint)
11. [Prettier](#11-prettier)
12. [EditorConfig](#12-editorconfig)
13. [Instalación conjunta de extensiones](#13-instalación-conjunta-de-extensiones)
14. [Configurar Prettier y ESLint](#14-configurar-prettier-y-eslint)
15. [Crear y ejecutar un proyecto Angular](#15-crear-y-ejecutar-un-proyecto-angular)
16. [Comprobar el entorno](#16-comprobar-el-entorno)
17. [Extensiones adicionales](#17-extensiones-adicionales)
18. [Recomendaciones para el aula](#18-recomendaciones-para-el-aula)
19. [Configuración final recomendada](#19-configuración-final-recomendada)

---

# 1. 🐧 ¿Por qué utilizar WSL2?

Aunque **Node.js**, **npm** y **Angular CLI** pueden instalarse directamente en Windows, para desarrollo web es muy recomendable trabajar mediante **WSL2 (Windows Subsystem for Linux)**.

WSL2 permite disponer de un sistema Linux completamente integrado con Windows.

### ✅ Ventajas principales

- entorno Linux real;
- mejor compatibilidad con herramientas de desarrollo web;
- uso natural de `bash`, `ssh`, `git`, `npm`, `npx`, etc.;
- comportamiento similar al de servidores Linux;
- menos problemas de rutas y permisos;
- excelente integración con Visual Studio Code;
- buena compatibilidad con Docker y herramientas de backend.

> [!TIP]
> Para proyectos Angular y Node.js en Windows, una configuración especialmente cómoda consiste en ejecutar **VS Code en Windows** y mantener **Node.js, npm, Angular CLI y el proyecto dentro de WSL2**.

---

# 2. 🏗️ Arquitectura recomendada

La organización recomendada es la siguiente:

```text
┌──────────────────────────────────────────────┐
│                  WINDOWS 11                  │
│                                              │
│   Visual Studio Code                         │
│            │                                 │
│            │ Extensión WSL                   │
│            ▼                                 │
│   ┌──────────────────────────────────────┐   │
│   │                 WSL2                 │   │
│   │                Ubuntu                │   │
│   │                                      │   │
│   │   NVM                                │   │
│   │   Node.js                            │   │
│   │   npm                                │   │
│   │   Angular CLI                        │   │
│   │   Git                                │   │
│   │   Proyecto Angular                   │   │
│   │                                      │   │
│   └──────────────────────────────────────┘   │
└──────────────────────────────────────────────┘
```

De esta forma:

- **VS Code** se ejecuta en Windows.
- **Node.js** se ejecuta dentro de Ubuntu/WSL2.
- `npm`, `npx` y `ng` se ejecutan en Linux.
- el código fuente se guarda preferentemente en el sistema de archivos de WSL.
- las extensiones de desarrollo pueden ejecutarse en el contexto Linux.

---

## 📂 Organización recomendada de proyectos

Podemos crear una carpeta general:

```bash
mkdir -p ~/proyectos
cd ~/proyectos
```

Por ejemplo:

```text
/home/alumno/proyectos/
├── angular01/
├── angular02/
└── proyecto-final/
```

> [!IMPORTANT]
> Siempre que sea posible, evita guardar proyectos Node.js dentro de:
>
> ```text
> /mnt/c/
> ```
>
> Es preferible utilizar:
>
> ```text
> /home/usuario/proyectos/
> ```
>
> Esto suele mejorar el rendimiento de `node_modules`, Vite, Angular CLI y los *watchers* de archivos.

---

# 3. 🔍 Comprobar Node.js dentro de WSL

Abrimos Ubuntu y ejecutamos:

```bash
node --version
```

Por ejemplo:

```text
v24.x.x
```

Comprobamos npm:

```bash
npm --version
```

Y, si utilizamos NVM:

```bash
nvm --version
```

Para listar las versiones instaladas:

```bash
nvm ls
```

Seleccionar una versión:

```bash
nvm use 24
```

Configurarla como predeterminada:

```bash
nvm alias default 24
```

> [!WARNING]
> Estos comandos deben ejecutarse **dentro de WSL**, no desde PowerShell ni desde CMD.

---

# 4. 💻 Instalar Visual Studio Code

Visual Studio Code debe instalarse normalmente en **Windows**.

Al trabajar con WSL tendremos dos piezas:

```text
VS Code en Windows
        +
VS Code Server dentro de WSL
```

El servidor de VS Code se instala automáticamente dentro de Linux la primera vez que conectamos el editor con WSL.

---

# 5. 🔌 Instalar la extensión WSL

La primera extensión imprescindible es:

## WSL

**Desarrollador:** Microsoft

**Identificador:**

```text
ms-vscode-remote.remote-wsl
```

Su función es permitir que Visual Studio Code abra carpetas y ejecute terminales, extensiones y herramientas dentro de WSL.

### Instalación desde terminal

Desde Windows:

```powershell
code --install-extension ms-vscode-remote.remote-wsl
```

También puede instalarse desde la vista **Extensions** de VS Code.

> [!NOTE]
> Esta extensión se instala en el **VS Code de Windows**, ya que es la encargada de establecer la conexión con WSL.

---

# 6. 🚀 Abrir VS Code desde WSL

Desde Ubuntu:

```bash
cd ~/proyectos
```

Ejecutamos:

```bash
code .
```

Visual Studio Code se abrirá conectado a WSL.

En la esquina inferior izquierda debería aparecer algo similar a:

```text
WSL: Ubuntu
```

Esto indica que VS Code está trabajando en el entorno Linux.

---

# 7. 🧠 ¿Dónde se instalan las extensiones?

Cuando utilizamos WSL existen dos contextos:

```text
VS Code local
    │
    ├── extensiones de interfaz
    │
    └── WSL
         └── extensiones de desarrollo
```

Una distribución adecuada sería:

| Extensión | Ubicación recomendada |
|---|---|
| WSL | Windows |
| Angular Language Service | WSL |
| ESLint | WSL |
| Prettier | WSL |
| EditorConfig | WSL |
| GitLens | WSL |
| Tailwind CSS IntelliSense | WSL |

Cuando estemos conectados a WSL, VS Code puede mostrar:

```text
Install in WSL: Ubuntu
```

En las extensiones relacionadas con el proyecto utilizaremos esta opción.

---

# 8. 🧩 Extensiones básicas recomendadas

Para comenzar con Angular no es necesario instalar una gran cantidad de extensiones.

Una configuración sencilla y eficaz es:

| Extensión | Función |
|---|---|
| **WSL** | Integración entre VS Code y Linux |
| **Angular Language Service** | Autocompletado y análisis de plantillas Angular |
| **EditorConfig** | Reglas comunes de edición |
| **ESLint** | Análisis estático del código |
| **Prettier** | Formateo automático |

```text
VS Code
│
├── WSL
├── Angular Language Service
├── EditorConfig
├── ESLint
└── Prettier
```

---

# 9. 🅰️ Angular Language Service

## Angular Language Service

**Desarrollador:** Angular

**Identificador:**

```text
Angular.ng-template
```

Esta extensión proporciona soporte específico para Angular.

### Funciones principales

- autocompletado en templates;
- detección de errores;
- navegación entre componentes y plantillas;
- comprobación de propiedades;
- comprobación de eventos;
- soporte de directivas;
- soporte de pipes;
- información de tipos;
- análisis del template.

Ejemplo:

```html
<h1>{{ usuario.nombre }}</h1>
```

Angular Language Service puede comprobar que la propiedad:

```typescript
usuario.nombre
```

existe realmente.

También reconoce la sintaxis de control moderna de Angular:

```html
@if (usuario) {
  <p>{{ usuario.nombre }}</p>
}
```

```html
@for (producto of productos; track producto.id) {
  <p>{{ producto.nombre }}</p>
}
```

Y bloques diferidos:

```html
@defer {
  <app-productos />
}
```

### Instalación

```bash
code --install-extension Angular.ng-template
```

---

# 10. 🔎 ESLint

## ESLint

**Identificador:**

```text
dbaeumer.vscode-eslint
```

ESLint analiza el código JavaScript y TypeScript en busca de:

- errores;
- malas prácticas;
- problemas de calidad;
- incumplimiento de reglas del proyecto;
- patrones no recomendados.

### Ejemplo

```typescript
let nombre = "Ana";
```

Dependiendo de las reglas del proyecto, ESLint podría recomendar:

```typescript
const nombre = 'Ana';
```

### Instalación

```bash
code --install-extension dbaeumer.vscode-eslint
```

> [!IMPORTANT]
> La extensión de VS Code no sustituye al paquete ESLint instalado en el proyecto.
>
> Habitualmente tendremos dependencias de desarrollo como:
>
> ```bash
> npm install -D eslint
> ```

En proyectos Angular puede utilizarse además:

```text
angular-eslint
```

---

# 11. 🎨 Prettier

## Prettier - Code formatter

**Identificador:**

```text
esbenp.prettier-vscode
```

Prettier se ocupa fundamentalmente del **formato del código**.

Ejemplo:

```typescript
const persona={nombre:"Ana",edad:25};
```

Puede convertirse en:

```typescript
const persona = {
  nombre: 'Ana',
  edad: 25,
};
```

Prettier resulta especialmente útil para:

```text
TypeScript
JavaScript
HTML
CSS
SCSS
JSON
Markdown
```

### Instalación

```bash
code --install-extension esbenp.prettier-vscode
```

---

## 🔄 ESLint frente a Prettier

| Herramienta | Responsabilidad principal |
|---|---|
| **ESLint** | Calidad y corrección del código |
| **Prettier** | Formato |
| **Angular Language Service** | Análisis de Angular y templates |

Ejemplo:

```typescript
let x=4
```

Prettier puede transformarlo en:

```typescript
let x = 4;
```

ESLint, además, podría recomendar:

```typescript
const x = 4;
```

si la variable no cambia.

---

# 12. ⚙️ EditorConfig

## EditorConfig for VS Code

**Identificador:**

```text
EditorConfig.EditorConfig
```

EditorConfig permite que todos los integrantes del equipo compartan reglas básicas de edición.

Un proyecto puede incluir:

```text
.editorconfig
```

con:

```ini
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true
```

Esto permite unificar aspectos como:

- codificación UTF-8;
- tamaño de indentación;
- espacios frente a tabuladores;
- salto de línea final;
- eliminación de espacios sobrantes.

### Instalación

```bash
code --install-extension EditorConfig.EditorConfig
```

Angular CLI suele crear un `.editorconfig` al generar el proyecto.

---

# 13. 📦 Instalación conjunta de extensiones

Una vez conectados a WSL podemos instalar las extensiones principales desde terminal:

```bash
code --install-extension Angular.ng-template
code --install-extension EditorConfig.EditorConfig
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
```

Nuestra instalación mínima quedaría:

```text
✅ WSL
✅ Angular Language Service
✅ EditorConfig
✅ ESLint
✅ Prettier
```

---

# 14. 🛠️ Configurar Prettier y ESLint

Abrimos la configuración de VS Code:

```text
File
  → Preferences
      → Settings
```

Buscamos:

```text
Default Formatter
```

Y seleccionamos:

```text
Prettier - Code formatter
```

Activamos además:

```text
Format On Save
```

---

## 🧾 Configuración mediante `settings.json`

Abrimos la paleta:

```text
Ctrl + Shift + P
```

Seleccionamos:

```text
Preferences: Open User Settings (JSON)
```

Podemos utilizar una configuración como:

```json
{
  "editor.formatOnSave": true,

  "editor.defaultFormatter": "esbenp.prettier-vscode",

  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },

  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "[css]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "[scss]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

El flujo resultante será:

```text
Guardar archivo
      │
      ├── Prettier → formatea
      │
      └── ESLint   → aplica correcciones
```

---

# 15. 🅰️ Crear y ejecutar un proyecto Angular

Dentro de WSL:

```bash
cd ~/proyectos
```

Comprobamos Angular CLI:

```bash
ng version
```

Creamos un nuevo proyecto:

```bash
ng new mi-angular
```

Entramos en él:

```bash
cd mi-angular
```

Abrimos VS Code:

```bash
code .
```

---

## ▶️ Ejecutar el proyecto

Podemos utilizar:

```bash
npm start
```

o:

```bash
ng serve
```

Angular iniciará normalmente el servidor en:

```text
http://localhost:4200/
```

Aunque Angular se esté ejecutando dentro de WSL2, podremos abrir la aplicación desde Edge, Chrome o Firefox en Windows.

---

# 16. 🧪 Comprobar el entorno

Desde el terminal integrado de VS Code:

```bash
which node
```

Debería devolver una ruta similar a:

```text
/home/usuario/.nvm/versions/node/v24.x.x/bin/node
```

Comprobamos npm:

```bash
which npm
```

Resultado esperado:

```text
/home/usuario/.nvm/versions/node/v24.x.x/bin/npm
```

También podemos comprobar:

```bash
node --version
npm --version
ng version
git --version
```

> [!WARNING]
> Si aparece una ruta de Windows como:
>
> ```text
> C:\Program Files\nodejs
> ```
>
> debemos revisar la configuración porque el proyecto debería estar utilizando el Node.js instalado dentro de WSL.

---

# 17. ➕ Extensiones adicionales

Las siguientes extensiones pueden resultar útiles, aunque no son imprescindibles.

---

## 🧬 GitLens

**Identificador:**

```text
eamodio.gitlens
```

Amplía las herramientas Git de VS Code.

Permite consultar:

- autor de una línea;
- commit asociado;
- historial;
- ramas;
- cambios;
- *blame*.

---

## 📦 npm Intellisense

**Identificador:**

```text
christian-kohler.npm-intellisense
```

Facilita el autocompletado de módulos npm.

---

## 📁 Path Intellisense

**Identificador:**

```text
christian-kohler.path-intellisense
```

Ayuda a completar rutas:

```typescript
import { Producto } from './
```

---

## 🎨 Tailwind CSS IntelliSense

Si utilizamos Tailwind CSS:

```text
bradlc.vscode-tailwindcss
```

Proporciona autocompletado para clases como:

```html
<div class="flex items-center justify-between p-4">
```

---

## ⚠️ No instalar extensiones innecesarias

Una cantidad excesiva de extensiones puede:

- aumentar el consumo de memoria;
- ralentizar VS Code;
- duplicar funcionalidades;
- introducir conflictos;
- activar analizadores que no necesitamos.

> [!TIP]
> Para comenzar, es preferible instalar únicamente:
>
> ```text
> WSL
> Angular Language Service
> EditorConfig
> ESLint
> Prettier
> ```

---

# 18. 🎓 Recomendaciones para el aula

Para conseguir un entorno homogéneo entre todos los alumnos puede resultar útil incluir las extensiones recomendadas dentro del propio proyecto.

Creamos:

```text
.vscode/extensions.json
```

Contenido:

```json
{
  "recommendations": [
    "Angular.ng-template",
    "EditorConfig.EditorConfig",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode"
  ]
}
```

Podemos ampliarlo:

```json
{
  "recommendations": [
    "Angular.ng-template",
    "EditorConfig.EditorConfig",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "christian-kohler.path-intellisense",
    "christian-kohler.npm-intellisense"
  ]
}
```

Cuando un alumno clone el repositorio, VS Code podrá indicarle que existen extensiones recomendadas para ese espacio de trabajo.

---

## 🗂️ Organización propuesta

```text
Windows 11
│
├── Visual Studio Code
├── Navegador
│
└── WSL2
    │
    └── Ubuntu
        │
        ├── Git
        ├── NVM
        ├── Node.js
        ├── npm
        ├── Angular CLI
        │
        └── ~/proyectos
            │
            └── angular
                │
                ├── proyecto01
                ├── proyecto02
                └── proyecto03
```

---

## 🔁 Flujo de trabajo diario

Abrimos WSL:

```bash
wsl
```

Nos situamos en la carpeta de trabajo:

```bash
cd ~/proyectos
```

Entramos en el proyecto:

```bash
cd mi-angular
```

Abrimos VS Code:

```bash
code .
```

Instalamos dependencias si fuese necesario:

```bash
npm install
```

Ejecutamos:

```bash
npm start
```

Y accedemos desde el navegador:

```text
http://localhost:4200
```

---

# 19. ✅ Configuración final recomendada

| Componente | Ubicación |
|---|---|
| Windows 11 | Sistema anfitrión |
| WSL2 | Windows |
| Ubuntu | WSL2 |
| Visual Studio Code | Windows |
| Extensión WSL | VS Code Windows |
| Git | WSL |
| NVM | WSL |
| Node.js | WSL |
| npm | WSL |
| Angular CLI | WSL |
| Proyecto Angular | `/home/usuario/proyectos` |
| Angular Language Service | VS Code / WSL |
| ESLint | VS Code / WSL |
| Prettier | VS Code / WSL |
| EditorConfig | VS Code / WSL |

La idea principal puede resumirse así:

```text
             WINDOWS
                │
       Visual Studio Code
                │
                │ WSL Extension
                ▼
               WSL2
                │
              Ubuntu
                │
      ┌─────────┴─────────┐
      │                   │
   Node.js             Angular
   npm                 Proyecto
   Angular CLI
```

> [!IMPORTANT]
> Para desarrollo con **Angular + Node.js en Windows**, utiliza **VS Code como interfaz en Windows** y mantén las herramientas de desarrollo y los proyectos dentro de **WSL2**.
>
> Guarda preferentemente los proyectos en:
>
> ```bash
> ~/proyectos
> ```
>
> Esta configuración proporciona un entorno limpio, rápido, reproducible y muy próximo al que se utilizará posteriormente con servidores Linux, Docker y sistemas de despliegue.

---

## 🧠 Ideas clave

> **WSL2 no sustituye a Windows:** lo complementa proporcionando un entorno Linux integrado.

> **VS Code se instala en Windows:** el código y las herramientas de Node.js pueden residir en Linux.

> **Node.js debe ejecutarse dentro de WSL:** evita mezclar instalaciones Windows/Linux dentro del mismo proyecto.

> **Las extensiones deben instalarse en el contexto adecuado:** WSL para las herramientas relacionadas con el código.

> **Menos extensiones suele ser mejor:** instala únicamente las que aporten una función clara al flujo de trabajo.

---

<div align="center">

### 🚀 Windows + WSL2 + VS Code + Angular

**Un entorno moderno, reproducible y preparado para desarrollo profesional**

</div>
