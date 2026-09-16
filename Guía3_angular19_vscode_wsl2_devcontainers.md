# 🅰️ Angular 19 con VS Code, WSL2 y Dev Containers 

> [!IMPORTANT]
> **Objetivo de la guía**
>
> Configurar un entorno profesional de desarrollo para **Angular 19** utilizando **Visual Studio Code**, **WSL2** y **Dev Containers**, de forma que todo el equipo trabaje con un entorno **homogéneo, aislado, reproducible y fácil de mantener**.
>
> La guía mantiene un enfoque pedagógico: no solo indica **qué configurar**, sino también **por qué se toma cada decisión**, qué ventajas aporta y qué problemas evita en un equipo de desarrollo.

---

## 📚 Índice

1. [Contexto profesional](#-1-contexto-profesional)
2. [Tecnologías utilizadas](#-2-tecnologías-utilizadas)
3. [Versiones del entorno](#-3-versiones-del-entorno)
4. [Arquitectura propuesta](#-4-arquitectura-propuesta)
5. [¿Por qué utilizar Dev Containers?](#-5-por-qué-utilizar-dev-containers)
6. [Eficiencia y rendimiento](#-6-eficiencia-y-rendimiento)
7. [Software necesario](#-7-software-necesario)
8. [Preparación de WSL2](#-8-preparación-de-wsl2)
9. [Ubicación de los proyectos](#-9-ubicación-de-los-proyectos)
10. [Creación de un proyecto Angular 19](#-10-creación-de-un-proyecto-angular-19)
11. [Configuración del Dev Container](#-11-configuración-del-dev-container)
12. [Apertura del proyecto en el contenedor](#-12-apertura-del-proyecto-en-el-contenedor)
13. [Comprobación del entorno](#-13-comprobación-del-entorno)
14. [Ejecución de Angular 19](#-14-ejecución-de-angular-19)
15. [Reenvío del puerto 4200](#-15-reenvío-del-puerto-4200)
16. [Extensiones recomendadas de VS Code](#-16-extensiones-recomendadas-de-vs-code)
17. [Flujo de trabajo profesional](#-17-flujo-de-trabajo-profesional)
18. [Gestión de dependencias](#-18-gestión-de-dependencias)
19. [Buenas prácticas de rendimiento](#-19-buenas-prácticas-de-rendimiento)
20. [Integración con Git y repositorio](#-20-integración-con-git-y-repositorio)
21. [Onboarding de nuevos desarrolladores](#-21-onboarding-de-nuevos-desarrolladores)
22. [Problemas habituales](#-22-problemas-habituales)
23. [Alternativa: WSL2 sin Dev Containers](#-23-alternativa-wsl2-sin-dev-containers)
24. [Evolución hacia Angular + NestJS + PostgreSQL](#-24-evolución-hacia-angular--nestjs--postgresql)
25. [Integración futura con CI/CD](#-25-integración-futura-con-cicd)
26. [Resumen final](#-26-resumen-final)

---

# 💼 1. Contexto profesional

En un equipo de desarrollo es frecuente que cada desarrollador disponga de:

- una versión diferente de Node.js;
- distintas versiones globales de Angular CLI;
- extensiones diferentes de VS Code;
- configuraciones distintas de Git;
- dependencias locales instaladas de forma diferente;
- sistemas operativos o configuraciones de sistema no idénticas.

Esto puede derivar en problemas difíciles de reproducir:

```text
"En mi equipo funciona"
```

```text
"Con mi versión de Node no falla"
```

```text
"Después de actualizar npm dejó de compilar"
```

```text
"El proyecto arranca en mi máquina pero no en la del resto del equipo"
```

El objetivo de esta configuración es reducir al mínimo esas diferencias.

---

## 🎯 Objetivos profesionales

La solución propuesta busca:

- garantizar un entorno homogéneo;
- reducir el tiempo de onboarding;
- minimizar problemas de versiones;
- aislar dependencias del sistema operativo;
- facilitar la reproducción de errores;
- documentar el entorno como código;
- simplificar actualizaciones futuras;
- preparar el proyecto para CI/CD;
- facilitar la colaboración entre distintos desarrolladores.

> [!NOTE]
> En un entorno profesional, el Dev Container debe entenderse como parte de la **infraestructura de desarrollo del proyecto**, no como una configuración personal del editor.

---

# 🧰 2. Tecnologías utilizadas

El entorno estará compuesto por:

| Tecnología             | Función                            |
| ---------------------- | ---------------------------------- |
| **Windows 11**         | Sistema operativo anfitrión        |
| **WSL2**               | Entorno Linux integrado en Windows |
| **Ubuntu**             | Distribución Linux de desarrollo   |
| **Docker Desktop**     | Motor de ejecución de contenedores |
| **Visual Studio Code** | Editor / IDE                       |
| **Dev Containers**     | Entorno de desarrollo reproducible |
| **Node.js 22**         | Runtime JavaScript                 |
| **npm**                | Gestor de paquetes                 |
| **Angular 19**         | Framework frontend                 |
| **TypeScript**         | Lenguaje principal                 |
| **Git**                | Control de versiones               |

La arquitectura general será:

```text
Windows 11
    │
    ▼
Visual Studio Code
    │
    ▼
WSL2 + Ubuntu
    │
    ▼
Dev Container
    │
    ├── Node.js 22
    ├── npm
    ├── Angular 19
    ├── TypeScript
    └── Git
```

---

# 🔢 3. Versiones del entorno

Para evitar comportamientos inconsistentes fijaremos las versiones principales.

| Componente | Versión recomendada |
|---|---|
| **Angular** | `19.2.x` |
| **Angular CLI** | `19.2.x` |
| **Node.js** | `22.x` |
| **TypeScript** | Gestionado por Angular |
| **npm** | Incluido con Node.js |

La combinación utilizada será:

```text
Angular 19.2.x
      │
      ▼
Node.js 22
      │
      ▼
npm
```

---

## 🧠 ¿Por qué fijar versiones?

En un equipo profesional debemos evitar depender de:

```text
latest
```

Por ejemplo, no es recomendable basar el entorno en:

```text
Node latest
Angular latest
npm latest
```

porque una actualización futura podría modificar el comportamiento del proyecto.

Es preferible:

```text
Node 22
Angular 19.2.x
```

---

## ⚠️ Angular 19 como versión fijada

Angular 19 es una versión anterior del framework.

En un proyecto profesional puede seguir utilizándose cuando:

- existe una base de código estable;
- la migración todavía no está planificada;
- se necesita mantener compatibilidad;
- el equipo debe conservar un entorno reproducible.

> [!TIP]
> El Dev Container ayuda especialmente en proyectos que permanecen durante un tiempo sobre una versión concreta del framework.

---

# 🏗️ 4. Arquitectura propuesta

La arquitectura recomendada será:

```text
┌───────────────────────────────────────────┐
│                WINDOWS 11                 │
│                                           │
│          Visual Studio Code               │
└───────────────────┬───────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────────┐
│                  WSL2                     │
│                 Ubuntu                    │
│                                           │
│       ~/workspace/angular19-app           │
└───────────────────┬───────────────────────┘
                    │
                    ▼
┌───────────────────────────────────────────┐
│              DEV CONTAINER                │
│                                           │
│  Ubuntu                                   │
│  Node.js 22                               │
│  npm                                      │
│  Angular 19                               │
│  TypeScript                               │
│  Git                                      │
│  Extensiones VS Code                      │
└───────────────────┬───────────────────────┘
                    │
                    │ puerto 4200
                    ▼
          http://localhost:4200
```

---

## 💡 Separación de responsabilidades

El equipo anfitrión proporciona:

```text
Windows
│
├── Visual Studio Code
├── WSL2
└── Docker Desktop
```

El proyecto proporciona:

```text
Dev Container
│
├── Node.js
├── npm
├── Angular
├── TypeScript
└── herramientas de desarrollo
```

La idea es separar:

```text
Sistema operativo del desarrollador
             │
             ▼
     Infraestructura local
             │
             ▼
    Entorno del proyecto
             │
             ▼
       Aplicación Angular
```

---

# 📦 5. ¿Por qué utilizar Dev Containers?

En un equipo sin entorno reproducible podemos encontrar:

```text
Desarrollador A
├── Node 20
├── npm 10
└── Angular CLI 19
```

```text
Desarrollador B
├── Node 22
├── npm 11
└── Angular CLI 20
```

```text
Desarrollador C
├── Node 24
├── npm
└── Angular CLI 22
```

Aunque todos trabajen con el mismo repositorio, el entorno no es idéntico.

Con Dev Containers:

```text
                    devcontainer.json
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        Desarrollador A Desarrollador B Desarrollador C
             │             │             │
             ▼             ▼             ▼
          Node 22       Node 22       Node 22
             │             │             │
             ▼             ▼             ▼
        Angular 19    Angular 19    Angular 19
```

---

## 📊 Comparación

| Característica | Instalación local | Dev Container |
|---|---:|---:|
| Configuración manual | Alta | Baja |
| Entornos homogéneos | ⚠️ | ✅ |
| Aislamiento | ❌ | ✅ |
| Reproducibilidad | ⚠️ | ✅ |
| Onboarding rápido | ⚠️ | ✅ |
| Configuración versionable | ❌ | ✅ |
| Reproducción de incidencias | Media | Alta |
| Adecuado para equipos | ✅ | ⭐⭐⭐⭐⭐ |

---

# ⚡ 6. Eficiencia y rendimiento

La arquitectura añade una capa adicional:

```text
Windows
   │
   ▼
WSL2
   │
   ▼
Docker
   │
   ▼
Dev Container
   │
   ▼
Angular 19
```

El coste de CPU suele ser pequeño.

El aspecto que más afecta al rendimiento es el **sistema de archivos**.

Angular y npm trabajan continuamente con:

```text
src/
node_modules/
.angular/
package.json
package-lock.json
```

y miles de archivos pequeños.

---

## 📊 Comparación orientativa

| Configuración              | Rendimiento |   RAM | Reproducibilidad |
| -------------------------- | ----------: | ----: | ---------------: |
| Windows + Node             |       ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |               ⭐⭐ |
| WSL2 + Node                |       ⭐⭐⭐⭐⭐ |  ⭐⭐⭐⭐ |             ⭐⭐⭐⭐ |
| **WSL2 + Dev Container**   |       ⭐⭐⭐⭐½ |   ⭐⭐⭐ |            ⭐⭐⭐⭐⭐ |
| Docker + proyecto en `C:\` |         ⭐⭐⭐ |   ⭐⭐⭐ |            ⭐⭐⭐⭐⭐ |

> [!IMPORTANT]
> Para mantener un rendimiento elevado, el repositorio debe almacenarse dentro del sistema de archivos Linux de WSL2.

---

# 🛠️ 7. Software necesario

En Windows instalaremos:

- Windows 11;
- WSL2;
- Ubuntu;
- Docker Desktop;
- Visual Studio Code.

No será necesario instalar directamente sobre Windows:

- Node.js;
- npm;
- Angular CLI.

---

## 🧩 Extensiones iniciales de VS Code

### WSL

```text
ms-vscode-remote.remote-wsl
```

### Dev Containers

```text
ms-vscode-remote.remote-containers
```

Estas extensiones permiten trabajar con:

```text
VS Code
   │
   ├── WSL2
   │
   └── Dev Container
```
![[Pasted image 20260916223209.png]]
---

# 🐧 8. Preparación de WSL2

Comprobamos el entorno:

```bash
uname -a
```

Consultamos la distribución:

```bash
cat /etc/os-release
```

Creamos un directorio de trabajo:

```bash
mkdir -p ~/workspace
```

Entramos:

```bash
cd ~/workspace
```

> [!NOTE]
> En un entorno profesional resulta habitual utilizar nombres como:
>
> ```text
> ~/workspace
> ~/projects
> ~/src
> ```
>
> Cualquiera de ellos es válido siempre que el repositorio esté almacenado dentro del filesystem Linux.

---

# 📁 9. Ubicación de los proyectos

## ✅ Ubicación recomendada

```text
~/workspace/angular19-app
```

o:

```text
/home/usuario/workspace/angular19-app
```

---

## ❌ Ubicación a evitar

```text
C:\Users\usuario\workspace
```

Desde WSL:

```text
/mnt/c/Users/usuario/workspace
```

---

## 🔍 ¿Por qué?

Angular y npm manejan una gran cantidad de archivos.

Trabajando en `/mnt/c`:

```text
Linux
  │
  ▼
WSL2
  │
  ▼
Windows
  │
  ▼
NTFS
```

Trabajando en `~/workspace`:

```text
Linux
  │
  ▼
Filesystem Linux
```

Esto afecta especialmente a:

- instalación de paquetes;
- lectura de `node_modules`;
- watchers;
- compilación;
- hot reload;
- operaciones Git;
- cachés de Angular.

> [!TIP]
> En equipos profesionales conviene documentar esta regla directamente en el `README.md` del repositorio.

---

# 🅰️ 10. Creación de un proyecto Angular 19

Para crear explícitamente un proyecto Angular 19:

```bash
npx -y @angular/cli@19.2 new angular19-app
```
![[Pasted image 20260916223800.png]]
Con una configuración habitual:

```bash
npx -y @angular/cli@19.2 new angular19-app \
  --routing \
  --style=scss \
  --standalone \
  --strict \
  --package-manager=npm
```

Entramos:

```bash
cd angular19-app
```

---

## 🔎 Comprobar versión

```bash
npx ng version
```

Resultado esperado:

```text
Angular CLI: 19.2.x
Node: 22.x.x
Package Manager: npm

Angular: 19.2.x
```
![[Pasted image 20260916223855.png]]
---

# 📦 11. Configuración del Dev Container

Creamos:

```text
.devcontainer/
```

y dentro:

```text
devcontainer.json
```

La estructura será:

```text
angular19-app/
│
├── .devcontainer/
│   └── devcontainer.json
│
├── src/
├── package.json
├── package-lock.json
├── angular.json
└── tsconfig.json
```

---

## 📝 `.devcontainer/devcontainer.json`

```jsonc
{
  "name": "Angular 19 - Node 22",

  "image": "mcr.microsoft.com/devcontainers/base:ubuntu-24.04",

  "features": {
    "ghcr.io/devcontainers/features/node:2": {
      "version": "22"
    }
  },

  "forwardPorts": [
    4200
  ],

  "portsAttributes": {
    "4200": {
      "label": "Angular 19 Development Server",
      "onAutoForward": "openBrowser"
    }
  },

  "postCreateCommand": "npm ci",

  "customizations": {
    "vscode": {

      "extensions": [
        "Angular.ng-template",
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "EditorConfig.EditorConfig"
      ],

      "settings": {

        "editor.formatOnSave": true,

        "editor.defaultFormatter": "esbenp.prettier-vscode",

        "[typescript]": {
          "editor.defaultFormatter": "esbenp.prettier-vscode"
        },

        "[html]": {
          "editor.defaultFormatter": "esbenp.prettier-vscode"
        },

        "[scss]": {
          "editor.defaultFormatter": "esbenp.prettier-vscode"
        },

        "terminal.integrated.defaultProfile.linux": "bash"
      }
    }
  },

  "remoteUser": "vscode"
}
```
![[Pasted image 20260916224449.png]]
---

## 🔎 ¿Qué estamos fijando?

### Node.js

```jsonc
"ghcr.io/devcontainers/features/node:2": {
  "version": "22"
}
```

### Puerto Angular

```json
"forwardPorts": [
  4200
]
```

### Dependencias

```json
"postCreateCommand": "npm ci"
```

### Extensiones del equipo

```jsonc
"extensions": [
  "Angular.ng-template",
  "dbaeumer.vscode-eslint",
  "esbenp.prettier-vscode",
  "EditorConfig.EditorConfig"
]
```

> [!NOTE]
> Esto permite tratar la configuración de VS Code como parte del proyecto y no como una preferencia individual.

---

# 🚀 12. Apertura del proyecto en el contenedor

Desde WSL:

```bash
cd ~/workspace/angular19-app
code .
```

Pulsamos:

```text
Ctrl + Shift + P
```

Seleccionamos:

```text
Dev Containers: Reopen in Container
```
![[Pasted image 20260916224634.png]]
---
![[Pasted image 20260916225145.png]]

![[Pasted image 20260916225810.png]]

![[Pasted image 20260916225925.png]]
## 🔄 Flujo automático

```text
devcontainer.json
       │
       ▼
Crear entorno Linux
       │
       ▼
Instalar Node 22
       │
       ▼
Instalar extensiones
       │
       ▼
npm ci
       │
       ▼
Abrir workspace
```

---

# 🔍 13. Comprobación del entorno

## Node.js

```bash
node --version
```

Resultado esperado:

```text
v22.x.x
```

## npm

```bash
npm --version
```

## Angular

```bash
npx ng version
```

Comprobamos:

```text
Angular CLI: 19.x
Angular: 19.x
Node: 22.x
```

---

# ▶️ 14. Ejecución de Angular 19

Ejecutamos:

```bash
npm start
```

En el Dev Container podemos utilizar:

```bash
npm start -- --host 0.0.0.0
```

o:

```bash
npx ng serve --host 0.0.0.0
```

---

## 🌐 Flujo

```text
Angular
   │
   │ :4200
   ▼
Dev Container
   │
   ▼
VS Code
Port Forwarding
   │
   ▼
Windows
   │
   ▼
Navegador
```

---

# 🌐 15. Reenvío del puerto 4200

La configuración:

```json
"forwardPorts": [
  4200
]
```

permite acceder desde Windows a:

```text
http://localhost:4200
```

---

# 🧩 16. Extensiones recomendadas de VS Code

## Angular Language Service

```text
Angular.ng-template
```

Aporta:

- autocompletado;
- validación de templates;
- navegación;
- información de tipos;
- detección de errores.

---

## Prettier

```text
esbenp.prettier-vscode
```

---

## ESLint

```text
dbaeumer.vscode-eslint
```

---

## EditorConfig

```text
EditorConfig.EditorConfig
```

> [!TIP]
> En un entorno profesional conviene decidir las extensiones mínimas obligatorias y evitar sobrecargar el Dev Container con herramientas no necesarias para todo el equipo.

---

# 🔄 17. Flujo de trabajo profesional

Un flujo habitual será:

```bash
cd ~/workspace
git clone URL_REPOSITORIO
cd angular19-app
code .
```

Después:

```text
Dev Containers: Reopen in Container
```

y:

```bash
npm start -- --host 0.0.0.0
```

---

## 🔁 Flujo completo

```text
git clone
    │
    ▼
code .
    │
    ▼
Reopen in Container
    │
    ▼
npm ci
    │
    ▼
npm start
    │
    ▼
desarrollo
    │
    ▼
git add / commit / push
```

---

# 📦 18. Gestión de dependencias

En proyectos profesionales debemos diferenciar:

```bash
npm install
```

de:

```bash
npm ci
```

---

## `npm install`

Se utilizará principalmente cuando:

- añadimos una dependencia;
- actualizamos una dependencia;
- modificamos `package.json`.

Ejemplo:

```bash
npm install rxjs
```

---

## `npm ci`

Se utilizará cuando queremos reconstruir exactamente el entorno:

```bash
npm ci
```

Es especialmente adecuado para:

- Dev Containers;
- CI/CD;
- servidores de integración;
- entornos limpios;
- onboarding.

---

## 🧠 Capas de reproducibilidad

```text
devcontainer.json
      │
      ▼
Node 22
```

```text
package.json
      │
      ▼
dependencias declaradas
```

```text
package-lock.json
      │
      ▼
resolución exacta
```

---

# ⚡ 19. Buenas prácticas de rendimiento

## ✅ Proyecto dentro de WSL2

```text
~/workspace/angular19-app
```

---

## ✅ No copiar `node_modules`

Reconstruir:

```bash
npm ci
```

---

## ✅ No versionar `node_modules`

`.gitignore`:

```gitignore
node_modules/
dist/
.angular/
```

---

## ✅ No depender de Angular CLI global

Utilizar:

```bash
npx ng
```

---

## ✅ Utilizar `package-lock.json`

Debe formar parte del repositorio.

---

## ✅ Evitar dependencias implícitas del equipo

No debemos depender de herramientas instaladas globalmente en Windows que no estén documentadas.

---

# 🌿 20. Integración con Git y repositorio

El Dev Container debe versionarse:

```text
.devcontainer/
└── devcontainer.json
```

También:

```text
package.json
package-lock.json
```

---

## 📂 Ejemplo de repositorio

```text
angular19-app/
│
├── .devcontainer/
│   └── devcontainer.json
│
├── .vscode/
│
├── src/
│
├── angular.json
├── package.json
├── package-lock.json
├── tsconfig.json
├── README.md
└── .gitignore
```

---

## 💡 Principio profesional

El repositorio no debe contener solamente el código.

También debe contener suficiente configuración para responder a:

```text
¿Qué necesita un nuevo desarrollador para empezar?
```

La respuesta ideal debería aproximarse a:

```text
1. Instalar VS Code
2. Instalar WSL2
3. Instalar Docker
4. Clonar
5. Reopen in Container
6. Empezar a trabajar
```

---

# 👥 21. Onboarding de nuevos desarrolladores

Una de las mayores ventajas del Dev Container es reducir el tiempo necesario para incorporar nuevos miembros al equipo.

Sin Dev Container:

```text
Instalar Node
   │
Configurar versión
   │
Instalar Angular CLI
   │
Configurar npm
   │
Instalar extensiones
   │
Resolver diferencias
   │
Ejecutar proyecto
```

Con Dev Container:

```text
Clonar
   │
   ▼
Abrir VS Code
   │
   ▼
Reopen in Container
   │
   ▼
npm ci
   │
   ▼
Trabajar
```

---

## 📌 Documentación mínima en `README.md`

El repositorio debería indicar al menos:

```markdown
## Requisitos

- Windows 11
- WSL2
- Docker Desktop
- Visual Studio Code
- Extensión Dev Containers

## Inicio

```bash
git clone URL_REPOSITORIO
cd proyecto
code .
```

Después:

Dev Containers: Reopen in Container
```

> [!TIP]
> Cuanto menos conocimiento implícito exista fuera del repositorio, más sencillo será mantener el proyecto.

---

# ⚠️ 22. Problemas habituales

## ❌ Angular utiliza otra versión

Comprobar:

```bash
npx ng version
```

---

## ❌ Node no es la versión esperada

```bash
node --version
```

Esperado:

```text
v22.x.x
```

---

## ❌ El proyecto funciona lentamente

```bash
pwd
```

Si aparece:

```text
/mnt/c/...
```

mover el repositorio a:

```text
~/workspace
```

---

## ❌ Dependencias inconsistentes

```bash
rm -rf node_modules
npm ci
```

---

## ❌ Cambio en `devcontainer.json`

Ejecutar:

```text
Dev Containers: Rebuild and Reopen in Container
```

---

## ❌ Funciona en local pero no en CI

Comprobar:

- versión de Node;
- uso de `npm ci`;
- variables de entorno;
- scripts definidos en `package.json`;
- dependencias no declaradas globalmente.

---

# 🪶 23. Alternativa: WSL2 sin Dev Containers

Podemos utilizar:

```text
Windows
   │
   ▼
VS Code
   │
   ▼
WSL2
   │
   ├── NVM
   ├── Node 22
   ├── npm
   └── Angular 19
```

---

## 📊 Comparación

| Característica            |  WSL2 | WSL2 + Dev Container |
| ------------------------- | ----: | -------------------: |
| Rendimiento               | ⭐⭐⭐⭐⭐ |                ⭐⭐⭐⭐½ |
| Consumo RAM               | ⭐⭐⭐⭐⭐ |                  ⭐⭐⭐ |
| Simplicidad inicial       | ⭐⭐⭐⭐⭐ |                 ⭐⭐⭐⭐ |
| Aislamiento               |   ⭐⭐⭐ |                ⭐⭐⭐⭐⭐ |
| Reproducibilidad          |  ⭐⭐⭐⭐ |                ⭐⭐⭐⭐⭐ |
| Onboarding                |   ⭐⭐⭐ |                ⭐⭐⭐⭐⭐ |
| Configuración versionable |     ❌ |                    ✅ |
| Adecuado para equipos     |  ⭐⭐⭐⭐ |                ⭐⭐⭐⭐⭐ |

---

## 🎯 Elección práctica

Para un único desarrollador:

```text
WSL2 + NVM + Node
```

puede ser suficiente.

Para un equipo:

```text
WSL2 + Dev Containers
```

aporta más valor por:

- homogeneidad;
- onboarding;
- reproducibilidad;
- mantenimiento;
- menor dependencia del equipo local.

---

# 🚀 24. Evolución hacia Angular + NestJS + PostgreSQL

Una arquitectura profesional puede evolucionar hacia:

```text
Angular 19
    │
    │ HTTP / REST
    ▼
NestJS
    │
    ▼
TypeORM
    │
    ▼
PostgreSQL
```

Podemos ejecutar Angular y NestJS dentro del mismo entorno Node:

```text
DEV CONTAINER
│
├── Angular 19
│      └── :4200
│
└── NestJS
       └── :3000
```

Y utilizar contenedores adicionales para infraestructura:

```text
Docker Compose
│
├── PostgreSQL
│      └── :5432
│
├── Redis
│
└── Mailpit
```

---

## 🏗️ Arquitectura completa

```text
┌──────────────────────────────────────────────┐
│                  WINDOWS 11                  │
└─────────────────────┬────────────────────────┘
                      │
                      ▼
                    WSL2
                      │
                      ▼
             ~/workspace/proyecto
                      │
                      ▼
                DEV CONTAINER
                      │
           ┌──────────┴──────────┐
           │                     │
           ▼                     ▼
      Angular 19               NestJS
        :4200                   :3000
                                   │
                                   ▼
                                TypeORM
                                   │
                                   ▼
                             PostgreSQL
                                :5432
```

---

# 🔁 25. Integración futura con CI/CD

Uno de los beneficios de este enfoque es que el entorno local puede aproximarse al entorno utilizado por CI.

Por ejemplo:

```text
Desarrollo local
      │
      ▼
Node 22
npm ci
npm run build
npm test
```

CI:

```text
Pipeline
   │
   ▼
Node 22
npm ci
npm run build
npm test
```

---

## 💡 Principio importante

Cuanto más similares sean:

```text
Entorno local
```

y:

```text
Entorno CI
```

menor será la probabilidad de errores específicos de entorno.

---

## Ejemplo conceptual

```text
             REPOSITORIO
                  │
        ┌─────────┴──────────┐
        │                    │
        ▼                    ▼
Desarrolladores           CI/CD
        │                    │
        ▼                    ▼
    Node 22               Node 22
        │                    │
        ▼                    ▼
     npm ci                 npm ci
        │                    │
        ▼                    ▼
     build                  build
```

---

# 📝 26. Resumen final

## 🏆 Configuración recomendada

```text
Windows 11
      +
Visual Studio Code
      +
WSL2
      +
Ubuntu
      +
Docker Desktop
      +
Dev Containers
      +
Node.js 22
      +
Angular 19.2.x
```

---

## 📁 Ubicación recomendada

```text
~/workspace/proyecto
```

Evitar:

```text
/mnt/c/Users/...
```

---

## 🔁 Flujo profesional

```text
git clone
   │
   ▼
code .
   │
   ▼
Reopen in Container
   │
   ▼
npm ci
   │
   ▼
npm start
   │
   ▼
desarrollo
   │
   ▼
test
   │
   ▼
commit
   │
   ▼
push
```

---

# 🎯 Resultado

Con esta arquitectura obtenemos un entorno:

- 🔒 **aislado**
- 🔁 **reproducible**
- ⚡ **eficiente**
- 📦 **portable**
- 👥 **homogéneo para todo el equipo**
- 🚀 **rápido de incorporar para nuevos desarrolladores**
- 🛠️ **fácil de mantener**
- 🔍 **adecuado para reproducir incidencias**
- 🔁 **preparado para CI/CD**
- 🗄️ **preparado para evolucionar hacia NestJS + TypeORM + PostgreSQL**

---

> [!IMPORTANT]
> **Principio final**
>
> El objetivo no es únicamente conseguir que Angular funcione.
>
> El objetivo es que cualquier desarrollador del equipo pueda obtener:
>
> ```text
> mismo repositorio
>       +
> mismo entorno
>       +
> mismas dependencias
>       =
> comportamiento reproducible
> ```
>
> Esa reproducibilidad es uno de los principales beneficios de utilizar **Dev Containers** en un entorno profesional.
