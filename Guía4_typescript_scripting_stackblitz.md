<style>
:root {
  --text: #1f2937;
  --muted: #5b6472;
  --border: #d9dee7;
  --soft: #f7f9fc;
  --soft-blue: #eef6ff;
  --soft-green: #edf9f0;
  --soft-yellow: #fff8df;
  --soft-red: #fff0f0;
  --accent: #2563eb;
  --accent-dark: #1e3a8a;
  --code-bg: #f6f7f9;
}

body {
  color: var(--text);
  line-height: 1.65;
}

h1, h2, h3 {
  color: #172033;
}

h1 {
  border-bottom: 3px solid var(--accent);
  padding-bottom: .35rem;
}

h2 {
  margin-top: 2rem;
  border-bottom: 1px solid var(--border);
  padding-bottom: .25rem;
}

table {
  border-collapse: collapse;
  width: 100%;
  margin: 1rem 0;
}

th, td {
  border: 1px solid var(--border);
  padding: .55rem .7rem;
  vertical-align: top;
}

th {
  background: #f1f5f9;
}

.hero-card,
.concept-card,
.success-card,
.warning-card,
.danger-card {
  border-radius: 10px;
  padding: 1rem 1.15rem;
  margin: 1rem 0;
  border: 1px solid var(--border);
}

.hero-card {
  background: linear-gradient(135deg, #eef6ff, #f8fbff);
  border-left: 5px solid var(--accent);
}

.concept-card {
  background: var(--soft-blue);
  border-left: 5px solid #3b82f6;
}

.success-card {
  background: var(--soft-green);
  border-left: 5px solid #22c55e;
}

.warning-card {
  background: var(--soft-yellow);
  border-left: 5px solid #eab308;
}

.danger-card {
  background: var(--soft-red);
  border-left: 5px solid #ef4444;
}

.code-card {
  margin: 1rem 0 1.25rem 0;
  border: 1px solid #d8dde5;
  border-radius: 9px;
  overflow: hidden;
  background: var(--code-bg);
}

.code-title {
  background: #111827;
  color: #ffffff;
  font-size: .78rem;
  font-weight: 700;
  letter-spacing: .06em;
  padding: .45rem .75rem;
}

.code-card pre {
  margin: 0;
  padding: .9rem 1rem;
  overflow-x: auto;
  background: var(--code-bg);
}

.code-card code {
  font-family: Consolas, "Courier New", monospace;
}

kbd {
  background: #eef2f7;
  border: 1px solid #cbd5e1;
  border-bottom-width: 2px;
  border-radius: 4px;
  padding: 0 .35rem;
  font-family: Consolas, monospace;
}
</style>

# 🟦 Guía ligera de TypeScript como scripting en StackBlitz

<div class="hero-card">

## 🎯 Objetivo

Trabajar con **TypeScript como lenguaje de programación de propósito general**, ejecutando scripts desde una terminal y **sin utilizar HTML, CSS, DOM, Angular, React ni ninguna interfaz web**.

El entorno de trabajo será **StackBlitz + Node.js + TypeScript**.

</div>

---

## 📑 Índice

1. [Modelo de trabajo](#1-modelo-de-trabajo)
2. [Crear el proyecto en StackBlitz](#2-crear-el-proyecto-en-stackblitz)
3. [Preparar TypeScript](#3-preparar-typescript)
4. [Primer script](#4-primer-script)
5. [Variables y tipos](#5-variables-y-tipos)
6. [Comprobar errores de tipos](#6-comprobar-errores-de-tipos)
7. [Operadores](#7-operadores)
8. [Condicionales](#8-condicionales)
9. [Bucles](#9-bucles)
10. [Arrays](#10-arrays)
11. [Funciones](#11-funciones)
12. [Objetos e interfaces](#12-objetos-e-interfaces)
13. [Clases](#13-clases)
14. [Separar el programa en módulos](#14-separar-el-programa-en-módulos)
15. [Scripts de npm](#15-scripts-de-npm)
16. [Flujo de trabajo recomendado](#16-flujo-de-trabajo-recomendado)
17. [Ejercicios](#17-ejercicios)
18. [Resumen](#18-resumen)

---

# 1. Modelo de trabajo

En esta primera aproximación utilizaremos TypeScript únicamente para escribir y ejecutar programas.

```text
┌───────────────────────────┐
│      Código TypeScript    │
│         index.ts          │
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│            tsx            │
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│          Node.js          │
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│          Terminal         │
└───────────────────────────┘
```

<div class="concept-card">

### 💡 Idea clave

Aunque StackBlitz se ejecuta dentro del navegador, **nuestro programa no será una aplicación web**.

Trabajaremos como si tuviéramos un pequeño entorno Node.js instalado en nuestro ordenador.

</div>

Por ahora no necesitaremos:

- HTML.
- CSS.
- DOM.
- Eventos del navegador.
- Angular.
- React.
- Servidores HTTP.
- Bases de datos.

---

# 2. Crear el proyecto en StackBlitz

Accede a StackBlitz y crea un proyecto basado en **Node.js**.

Una estructura inicial puede ser similar a:

```text
proyecto/
├── index.js
└── package.json
```

Para esta guía vamos a trabajar con TypeScript, por lo que sustituiremos `index.js` por:

```text
index.ts
```

La estructura quedará:

```text
proyecto/
├── index.ts
└── package.json
```

<div class="success-card">

### ✅ Objetivo del entorno

Todo el trabajo se realizará desde:

- el **editor de código** de StackBlitz;
- la **terminal** integrada;
- archivos con extensión `.ts`.

</div>

---

# 3. Preparar TypeScript

Abre la terminal de StackBlitz.

Instala TypeScript y `tsx`:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npm install --save-dev typescript tsx
```

</div>

Las dos herramientas tienen funciones diferentes:

| Herramienta | Función |
|---|---|
| `typescript` | Proporciona el compilador `tsc` y comprueba los tipos |
| `tsx` | Ejecuta directamente archivos TypeScript desde Node.js |

Podemos comprobar la versión instalada:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsc --version
```

</div>

---

# 4. Primer script

Crea el archivo:

```text
index.ts
```

Añade:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
console.log("Hola TypeScript");
```

</div>

Ejecuta el programa:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsx index.ts
```

</div>

Resultado:

```text
Hola TypeScript
```

<div class="concept-card">

### 💡 `console.log()`

`console.log()` permite mostrar información en la salida estándar.

En nuestro caso, esa salida aparecerá directamente en la **terminal de StackBlitz**.

</div>

---

# 5. Variables y tipos

TypeScript permite indicar explícitamente el tipo que puede almacenar una variable.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let nombre: string = "Ana";
let edad: number = 20;
let estudiante: boolean = true;

console.log(nombre);
console.log(edad);
console.log(estudiante);
```

</div>

Resultado:

```text
Ana
20
true
```

## 5.1. Tipos básicos

| Tipo | Ejemplo | Descripción |
|---|---|---|
| `string` | `"Ana"` | Cadenas de texto |
| `number` | `25` | Números enteros o decimales |
| `boolean` | `true` | Valores lógicos |
| `null` | `null` | Ausencia intencionada de valor |
| `undefined` | `undefined` | Valor no definido |

Ejemplo:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let nombre: string = "Carlos";
let edad: number = 21;
let nota: number = 8.5;
let aprobado: boolean = true;
```

</div>

TypeScript puede detectar errores como:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let edad: number = 20;

edad = "veinte";
```

</div>

<div class="warning-card">

### ⚠️ Importante

`tsx` está pensado principalmente para **ejecutar** el programa.

Para estudiar TypeScript resulta conveniente utilizar también `tsc` para realizar la **comprobación estática de tipos**.

</div>

---

# 6. Comprobar errores de tipos

Ejecuta:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsc --noEmit index.ts
```

</div>

La opción:

```text
--noEmit
```

indica a TypeScript:

> Comprueba el programa, pero no generes ningún archivo JavaScript.

Por tanto tendremos dos operaciones diferentes.

### Comprobar el código

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsc --noEmit index.ts
```

</div>

### Ejecutar el código

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsx index.ts
```

</div>

---

# 7. Operadores

Los operadores aritméticos funcionan de forma muy similar a otros lenguajes.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let a: number = 10;
let b: number = 3;

console.log(a + b);
console.log(a - b);
console.log(a * b);
console.log(a / b);
console.log(a % b);
```

</div>

Resultado:

```text
13
7
30
3.3333333333333335
1
```

| Operador | Operación |
|---|---|
| `+` | Suma |
| `-` | Resta |
| `*` | Multiplicación |
| `/` | División |
| `%` | Resto |

---

# 8. Condicionales

## 8.1. `if`

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let edad: number = 20;

if (edad >= 18) {
  console.log("Es mayor de edad");
} else {
  console.log("Es menor de edad");
}
```

</div>

Otro ejemplo:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let nota: number = 7;

if (nota >= 5) {
  console.log("Aprobado");
} else {
  console.log("Suspenso");
}
```

</div>

## 8.2. `switch`

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let opcion: number = 2;

switch (opcion) {
  case 1:
    console.log("Alta");
    break;

  case 2:
    console.log("Consulta");
    break;

  case 3:
    console.log("Salir");
    break;

  default:
    console.log("Opción incorrecta");
}
```

</div>

---

# 9. Bucles

## 9.1. Bucle `for`

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
for (let i = 1; i <= 5; i++) {
  console.log(i);
}
```

</div>

Resultado:

```text
1
2
3
4
5
```

## 9.2. Bucle `while`

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let contador: number = 1;

while (contador <= 5) {
  console.log(contador);
  contador++;
}
```

</div>

---

# 10. Arrays

Un array almacena una colección de elementos.

Podemos indicar el tipo que podrán contener sus posiciones:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let notas: number[] = [7, 8, 5, 9];

console.log(notas);
```

</div>

## 10.1. Recorrer un array

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let notas: number[] = [7, 8, 5, 9];

for (let nota of notas) {
  console.log(nota);
}
```

</div>

## 10.2. Acceder mediante posición

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
console.log(notas[0]);
console.log(notas[1]);
```

</div>

<div class="concept-card">

### 💡 Recuerda

La primera posición de un array es:

```text
0
```

Por tanto:

```text
notas[0]
```

representa el primer elemento.

</div>

---

# 11. Funciones

TypeScript permite especificar los tipos de los parámetros y el tipo devuelto.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
function sumar(a: number, b: number): number {
  return a + b;
}

let resultado: number = sumar(5, 3);

console.log(resultado);
```

</div>

Resultado:

```text
8
```

## 11.1. Funciones sin valor de retorno

Usamos:

```text
void
```

cuando la función no devuelve ningún valor.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
function saludar(nombre: string): void {
  console.log(`Hola ${nombre}`);
}

saludar("Ana");
```

</div>

---

# 12. Objetos e interfaces

## 12.1. Objetos

Podemos indicar directamente la estructura que debe tener un objeto.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let alumno: {
  nombre: string;
  edad: number;
  nota: number;
} = {
  nombre: "Laura",
  edad: 20,
  nota: 8.5
};

console.log(alumno.nombre);
console.log(alumno.nota);
```

</div>

## 12.2. Interfaces

Si vamos a utilizar la misma estructura varias veces, resulta más cómodo crear una `interface`.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
interface Alumno {
  nombre: string;
  edad: number;
  nota: number;
}

let alumno: Alumno = {
  nombre: "María",
  edad: 21,
  nota: 7.5
};

console.log(alumno);
```

</div>

Podemos crear varios objetos del mismo tipo:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let alumno1: Alumno = {
  nombre: "Ana",
  edad: 20,
  nota: 8
};

let alumno2: Alumno = {
  nombre: "Carlos",
  edad: 22,
  nota: 6
};
```

</div>

## 12.3. Arrays de objetos

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
interface Producto {
  nombre: string;
  precio: number;
}

let productos: Producto[] = [
  {
    nombre: "Teclado",
    precio: 25
  },
  {
    nombre: "Ratón",
    precio: 15
  },
  {
    nombre: "Monitor",
    precio: 180
  }
];

for (let producto of productos) {
  console.log(`${producto.nombre}: ${producto.precio} €`);
}
```

</div>

<div class="success-card">

### ✅ Punto importante

Las interfaces serán especialmente útiles más adelante cuando trabajemos con:

- DTO;
- entidades;
- servicios;
- objetos recuperados de una base de datos;
- APIs;
- TypeORM.

</div>

---

# 13. Clases

TypeScript también permite trabajar con programación orientada a objetos.

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
class Persona {

  constructor(
    public nombre: string,
    public edad: number
  ) {}

  saludar(): void {
    console.log(`Hola, soy ${this.nombre}`);
  }
}

const persona = new Persona("Lucía", 21);

persona.saludar();
```

</div>

Tenemos:

```text
Persona
  │
  ├── nombre
  ├── edad
  │
  └── saludar()
```

---

# 14. Separar el programa en módulos

Cuando el programa crece conviene dividirlo en varios archivos.

Por ejemplo:

```text
proyecto/
│
├── src/
│   ├── main.ts
│   └── matematicas.ts
│
└── package.json
```

## 14.1. Archivo `matematicas.ts`

<div class="code-card">
<div class="code-title">TYPESCRIPT · matematicas.ts</div>

```typescript
export function sumar(a: number, b: number): number {
  return a + b;
}

export function multiplicar(a: number, b: number): number {
  return a * b;
}
```

</div>

## 14.2. Archivo `main.ts`

<div class="code-card">
<div class="code-title">TYPESCRIPT · main.ts</div>

```typescript
import { sumar, multiplicar } from "./matematicas";

console.log(sumar(4, 5));
console.log(multiplicar(4, 5));
```

</div>

Ejecutamos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsx src/main.ts
```

</div>

---

# 15. Scripts de npm

Para evitar escribir repetidamente:

```bash
npx tsx index.ts
```

podemos crear scripts dentro de `package.json`.

<div class="code-card">
<div class="code-title">JSON · package.json</div>

```json
{
  "scripts": {
    "start": "tsx index.ts",
    "check": "tsc --noEmit index.ts"
  },
  "devDependencies": {
    "tsx": "latest",
    "typescript": "latest"
  }
}
```

</div>

A partir de ahora podremos ejecutar:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npm start
```

</div>

Para comprobar los tipos:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npm run check
```

</div>

---

# 16. Flujo de trabajo recomendado

El flujo de trabajo para los primeros ejercicios será:

```text
┌─────────────────────────────┐
│  1. Escribir código .ts     │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  2. Comprobar los tipos     │
│      npm run check          │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  3. Ejecutar el programa    │
│         npm start           │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  4. Analizar el resultado   │
└─────────────────────────────┘
```

<div class="success-card">

### ✅ Comandos esenciales

Comprobar:

```bash
npm run check
```

Ejecutar:

```bash
npm start
```

</div>

Para ejercicios rápidos también podremos utilizar directamente:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsx index.ts
```

</div>

---

# 17. Ejercicios

## 🧩 Ejercicio 1 · Presentación

Declara las variables:

```text
nombre
edad
ciclo
```

El programa deberá mostrar un resultado similar a:

```text
Me llamo Ana, tengo 20 años y estudio DAM.
```

---

## 🧩 Ejercicio 2 · Operaciones

Declara dos números y muestra:

- suma;
- resta;
- multiplicación;
- división;
- resto.

Ejemplo:

```text
a = 10
b = 3
```

---

## 🧩 Ejercicio 3 · Positivo, negativo o cero

Partimos de:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let numero: number = -5;
```

</div>

El programa deberá indicar si el número es:

- positivo;
- negativo;
- cero.

---

## 🧩 Ejercicio 4 · Mayor de dos números

Partimos de:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let a: number = 8;
let b: number = 12;
```

</div>

Muestra cuál de los dos números es mayor.

---

## 🧩 Ejercicio 5 · Tabla de multiplicar

Partimos de:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let numero: number = 7;
```

</div>

El programa deberá mostrar:

```text
7 x 1 = 7
7 x 2 = 14
7 x 3 = 21
...
7 x 10 = 70
```

---

## 🧩 Ejercicio 6 · Media de notas

Partimos de:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
let notas: number[] = [7, 5, 8, 9, 6];
```

</div>

Calcula y muestra la media de todas las notas.

---

## 🧩 Ejercicio 7 · Array de alumnos

Crea la siguiente interfaz:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
interface Alumno {
  nombre: string;
  nota: number;
}
```

</div>

Crea un array con al menos cinco alumnos y muestra únicamente aquellos cuya nota sea mayor o igual que `5`.

---

## 🧩 Ejercicio 8 · Función de cálculo

Implementa una función:

<div class="code-card">
<div class="code-title">TYPESCRIPT</div>

```typescript
function calcularMedia(notas: number[]): number {
  // ...
}
```

</div>

La función deberá devolver la media de las notas recibidas.

---

# 18. Resumen

El entorno que utilizaremos inicialmente es:

```text
┌───────────────────────────────┐
│          StackBlitz           │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│        Entorno Node.js        │
└───────────────┬───────────────┘
                │
         ┌──────┴──────┐
         ▼             ▼
   TypeScript          tsx
      tsc
         │             │
         └──────┬──────┘
                ▼
            Terminal
```

Los tres comandos fundamentales son:

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npm install --save-dev typescript tsx
```

</div>

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsc --noEmit index.ts
```

</div>

<div class="code-card">
<div class="code-title">TERMINAL / BASH</div>

```bash
npx tsx index.ts
```

</div>

<div class="danger-card">

### ⛔ En esta fase NO utilizaremos

- HTML.
- CSS.
- DOM.
- Angular.
- React.
- Navegación web.
- Formularios web.
- Servidores HTTP.

</div>

<div class="success-card">

### ✅ Nos centraremos en

- tipos;
- variables;
- operadores;
- estructuras condicionales;
- bucles;
- arrays;
- funciones;
- objetos;
- interfaces;
- clases;
- módulos.

</div>

---

## 🚀 Evolución posterior

Esta base nos permitirá avanzar progresivamente hacia:

```text
TypeScript
    │
    ▼
Objetos e interfaces
    │
    ▼
Módulos
    │
    ▼
Programación asíncrona
    │
    ▼
Node.js
    │
    ▼
NestJS
    │
    ▼
TypeORM
    │
    ▼
PostgreSQL
```

> **Idea final:** primero aprenderemos a programar correctamente con TypeScript. Después utilizaremos ese mismo lenguaje para construir aplicaciones de acceso a datos cada vez más estructuradas.
