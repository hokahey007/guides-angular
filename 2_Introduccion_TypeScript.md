# 🧠 Unidad 2: Introducción a TypeScript

<style>
.code-card { margin: 1.35rem 0; border: 1px solid #d0d7de; border-radius: 10px; overflow: hidden; background: #f6f8fa; box-shadow: 0 2px 8px rgba(31,35,40,.08); }
.code-card .code-label { display: inline-block; margin: 0; padding: .35rem .8rem; background: #111; color: #fff; font-family: -apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif; font-size: .72rem; font-weight: 700; letter-spacing: .08em; text-transform: uppercase; border-bottom-right-radius: 7px; }
.code-card pre { margin: 0; padding: 1rem 1.15rem 1.15rem; overflow-x: auto; background: #f6f8fa; line-height: 1.55; tab-size: 2; }
.code-card code { font-family: "Cascadia Code","JetBrains Mono",Consolas,"Courier New",monospace; font-size: .92rem; color: #24292f; background: transparent; white-space: pre; }
.code-card[data-lang="typescript"] pre,.code-card[data-lang="javascript"] pre { background: #f7f9fc; }
.code-card[data-lang="html"] pre { background: #fff8f5; }
.code-card[data-lang="bash"] pre,.code-card[data-lang="shell"] pre { background: #f7fbf7; }
.code-card[data-lang="json"] pre,.code-card[data-lang="text"] pre { background: #fafafa; }
/* Las líneas visualmente vacías dentro de <pre><code> se representan
   mediante &#8203; para impedir que algunos motores Markdown cierren
   prematuramente el bloque HTML embebido. */

:not(pre) > code { padding: .12em .38em; border-radius: 4px; background: #eef1f4; color: #24292f; font-family: "Cascadia Code","JetBrains Mono",Consolas,monospace; font-size: .92em; }
</style>

> **Referencia del material:** capítulo 2 del PDF sobre Angular y TypeScript.  
> **Objetivo:** repasar características esenciales de JavaScript, comprender qué aporta TypeScript y aprender a trabajar con tipos, funciones, clases, interfaces, genéricos y tipos utilitarios.

---

## 🎯 Qué vas a aprender

Al finalizar esta unidad serás capaz de:

- Repasar características modernas de JavaScript muy utilizadas en Angular.
- Comprender qué es TypeScript y por qué se utiliza.
- Instalar el compilador de TypeScript.
- Transpilar código TypeScript a JavaScript.
- Trabajar con tipos primitivos y tipos personalizados.
- Definir parámetros y valores de retorno en funciones.
- Utilizar clases, herencia y modificadores de acceso.
- Crear interfaces.
- Utilizar genéricos.
- Trabajar con tipos utilitarios como `Partial`, `Pick` y `Record`.

---

# 1. JavaScript esencial

TypeScript es un **superconjunto sintáctico de JavaScript**. Esto significa que parte de JavaScript y añade nuevas características como tipos, interfaces y genéricos.

Antes de entrar en TypeScript conviene repasar algunas características de JavaScript que aparecen continuamente en el desarrollo Angular:

- declaración de variables;
- parámetros de funciones;
- funciones flecha;
- optional chaining;
- nullish coalescing;
- clases;
- módulos.

> 💡 Los ejemplos JavaScript de esta sección pueden ejecutarse tanto en la consola del navegador como desde un archivo `.js` usando Node.js.

---

# 2. Declaración de variables

Tradicionalmente JavaScript utilizó `var` para declarar variables.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">function myFunc() {
  var x = 0;
}</code></pre>
</div>

También puede utilizarse dentro de bucles:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">var x = 20;
&#8203;
for (var x = 0; x &lt; 10; x++) {
}</code></pre>
</div>

Para mejorar el control del ámbito, JavaScript introdujo `let`.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">function myFunc() {
  let x = 0;
  x = 10;
}</code></pre>
</div>

`let` permite cambiar posteriormente el valor de una variable.

## 2.1. Constantes con `const`

Cuando sabemos que una referencia no debe cambiar, utilizamos `const`.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const price = 100;
price = 50;</code></pre>
</div>

El código anterior provoca un error:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">TypeError: Assignment to constant variable.</code></pre>
</div>

Sin embargo, declarar un objeto como constante **no vuelve inmutables sus propiedades**.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const product = { price: 100 };
product.price = 50;</code></pre>
</div>

El código anterior sí es válido porque no estamos cambiando la referencia de `product`, sino una de sus propiedades.

En cambio:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const product = { price: 100 };
product = { price: 50 };</code></pre>
</div>

sí intenta sustituir la referencia completa y produce un error.

> 🧠 **Idea clave**  
> `const` impide reasignar la variable, pero no convierte automáticamente el contenido de un objeto en inmutable.

---

# 3. Spread operator

El operador **spread** utiliza tres puntos `...` para expandir valores.

## 3.1. Spread con arrays

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const category = 'Computing';
const categories = ['Gaming', 'Multimedia'];
const productCategories = [...categories, category];</code></pre>
</div>

El array original no cambia. Se crea uno nuevo con los elementos anteriores y el nuevo valor. Esta forma de trabajar favorece la **inmutabilidad**.

## 3.2. Spread con objetos

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const product = {
  name: 'Keyboard',
  price: 75
};
&#8203;
const newProduct = {
  ...product,
  price: 100,
  category: 'Computing'
};</code></pre>
</div>

El nuevo objeto será equivalente a:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">{
  name: 'Keyboard',
  price: 100,
  category: 'Computing'
}</code></pre>
</div>

Las propiedades que aparecen después del `spread` pueden sobrescribir valores anteriores.

> ⚠️ Un objeto no es realmente inmutable si sus propiedades pueden modificarse o contienen otros objetos modificables.

---

# 4. Parámetros de funciones

Las funciones procesan información de entrada y pueden producir resultados. JavaScript permite utilizar **parámetros por defecto**.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">function addtoCart(productId, quantity = 1) {
  const product = {
    id: productId,
    qty: quantity
  };
}</code></pre>
</div>

Si no se indica `quantity`, se utilizará `1`.

> 📌 Los parámetros con valor por defecto deben situarse después de los parámetros obligatorios.

## 4.1. Rest parameters

Los **rest parameters** permiten recibir un número variable de argumentos.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">function addProduct(name, ...categories) {
  const product = {
    name,
    categories: categories.join(',')
  };
}</code></pre>
</div>

Podemos invocar la función así:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">addProduct('Keyboard', 'Computing', 'Peripherals');</code></pre>
</div>

La sintaxis abreviada:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const product = {
  name
};</code></pre>
</div>

es equivalente a:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const product = {
  name: name
};</code></pre>
</div>

si el nombre de la propiedad coincide con el de la variable.

---

# 5. Funciones flecha

Las **arrow functions** simplifican la sintaxis de las funciones.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const discount = (price) =&gt; {
  return (price / 100) * 10;
};</code></pre>
</div>

Cuando hay un único parámetro y una única expresión podemos simplificar:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const discount = price =&gt; (price / 100) * 10;</code></pre>
</div>

Las funciones flecha también son importantes porque mantienen el contexto léxico de `this`.

## 5.1. Problema clásico con `this`

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">function createProduct(name) {
  this.name = name;
&#8203;
  this.getName = function() {
    setTimeout(function() {
      console.log('Product name is:', this.name);
    });
  };
}</code></pre>
</div>

Al ejecutar:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const product = new createProduct('Monitor');
product.getName();</code></pre>
</div>

el `this` utilizado dentro de `setTimeout` puede no referirse al objeto esperado.

La solución es usar una función flecha:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">setTimeout(() =&gt; {
  console.log('Product name is:', this.name);
});</code></pre>
</div>

---

# 6. Optional chaining

El operador de **encadenamiento opcional** `?.` permite acceder de forma segura a propiedades que podrían no existir.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const getOrder = () =&gt; {
  return {
    product: {
      name: 'Keyboard'
    }
  };
};</code></pre>
</div>

Sin optional chaining tendríamos que comprobar primero que existe el objeto:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const order = getOrder();
&#8203;
if (order !== undefined) {
  const product = order.product;
}</code></pre>
</div>

Con optional chaining:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const order = getOrder();
const product = order?.product;</code></pre>
</div>

Y en estructuras más profundas:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const name = order?.product?.name;</code></pre>
</div>

---

# 7. Nullish coalescing

El operador `??` permite proporcionar un valor por defecto cuando una variable es `null` o `undefined`.

Con operador ternario:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const quantity = qty ? qty : 1;</code></pre>
</div>

Con **nullish coalescing**:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">const quantity = qty ?? 1;</code></pre>
</div>

---

# 8. Clases en JavaScript

Las clases permiten estructurar código orientado a objetos. Pueden contener propiedades, constructor, métodos y accesores.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">class User {
  firstName = '';
  lastName = '';
  #isActive = false;
&#8203;
  constructor(firstName, lastName, isActive = true) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.#isActive = isActive;
  }
&#8203;
  getFullname() {
    return `${this.firstName} ${this.lastName}`;
  }
&#8203;
  get active() {
    return this.#isActive;
  }
}</code></pre>
</div>

## Elementos de la clase

- **Propiedades:** `firstName`, `lastName` y `#isActive`.
- **Constructor:** se ejecuta al crear una instancia.
- **Métodos:** representan comportamiento de la clase.
- **Accesores:** permiten exponer propiedades mediante `get` y `set`.

---

# 9. Herencia

Una clase puede heredar de otra mediante `extends`.

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">class Customer extends User {
  taxNumber = '';
&#8203;
  constructor(firstName, lastName) {
    super(firstName, lastName);
  }
}</code></pre>
</div>

La llamada `super(...)` invoca el constructor de la clase padre.

---

# 10. Módulos

A medida que una aplicación crece, conviene separar las clases y funcionalidades en archivos diferentes. Esto favorece el **Single Responsibility Principle (SRP)**.

## Exportar una clase

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">export class User {
  // ...
}</code></pre>
</div>

## Importar una clase

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">import { User } from './user';</code></pre>
</div>

Si el módulo exporta más de un elemento:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">import { User, UserPreferences } from './user';</code></pre>
</div>

> 💡 Una buena señal para dividir un módulo es que comience a contener lógica de dominios distintos.

---

# 11. ¿Qué es TypeScript?

TypeScript fue creado por Microsoft para facilitar el desarrollo de aplicaciones JavaScript de gran tamaño. La versión 1.0 apareció en 2014.

Su principal aportación es el **tipado estático opcional**, que permite detectar errores antes de ejecutar el código.

Entre sus ventajas:

- tipado estático;
- detección temprana de errores;
- mejor mantenibilidad;
- mayor consistencia;
- interfaces;
- gran integración con editores;
- sintaxis familiar para desarrolladores de Java, C# o C++.

La web oficial es <https://www.typescriptlang.org>.

---

# 12. Instalación de TypeScript

TypeScript se distribuye como un paquete npm.

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">npm install -g typescript</code></pre>
</div>

Creamos un archivo `app.ts`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const title = 'Hello TypeScript!';</code></pre>
</div>

---

# 13. Transpilación

Los navegadores ejecutan JavaScript, no TypeScript directamente. Por eso necesitamos transformar TypeScript a JavaScript.

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">tsc app.ts</code></pre>
</div>

El proceso se denomina **transpilación**. El compilador `tsc` genera un archivo `app.js`.

Por ejemplo:

<div class="code-card" data-lang="javascript">
<div class="code-label">JAVASCRIPT</div>
<pre><code class="language-javascript">var title = 'Hello TypeScript!';</code></pre>
</div>

Podemos indicar una versión concreta de JavaScript como destino:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">tsc app.ts --target es2022</code></pre>
</div>

---

# 14. Configuración mediante `tsconfig.json`

<div class="code-card" data-lang="json">
<div class="code-label">JSON</div>
<pre><code class="language-json">{
  "compilerOptions": {
    "target": "ES2022"
  }
}</code></pre>
</div>

A partir de ese momento basta con ejecutar:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">tsc</code></pre>
</div>

> 🧠 Angular utiliza internamente el compilador de TypeScript para construir las aplicaciones.

---

# 15. Tipos en TypeScript

El tipado es una de las características fundamentales de TypeScript.

> ⚠️ Los tipos desaparecen durante la transpilación. No forman parte del JavaScript final.

## 15.1. `string`

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const product: string = 'Keyboard';</code></pre>
</div>

## 15.2. `boolean`

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const isActive: boolean = true;</code></pre>
</div>

## 15.3. `number`

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const price: number = 100;</code></pre>
</div>

## 15.4. Arrays

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const categories: string[] = ['Computing', 'Multimedia'];
const categories2: Array&lt;string&gt; = ['Computing', 'Multimedia'];</code></pre>
</div>

Conviene que todo el equipo utilice una convención coherente.

---

# 16. Inferencia de tipos

TypeScript suele ser capaz de deducir automáticamente el tipo.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const product = 'Keyboard';</code></pre>
</div>

TypeScript infiere que `product` es un `string`.

> 💡 Siempre que sea posible, es útil aprovechar la inferencia de tipos en lugar de anotar manualmente todo.

---

# 17. `any` y `unknown`

Cuando no se conoce el tipo, puede utilizarse `any`.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let order: any;
&#8203;
function setOrderNo() {
  order = '0001';
}</code></pre>
</div>

`any` reduce enormemente la comprobación estática y debe utilizarse con precaución.

TypeScript también dispone de `unknown`. Puede contener cualquier valor, pero obliga a comprobar el tipo antes de realizar operaciones sobre él.

> ✅ En muchos escenarios, `unknown` es preferible a `any`.

---

# 18. Tipos personalizados

Podemos crear nuestros propios tipos mediante `type`.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Categories = 'computing' | 'multimedia';</code></pre>
</div>

Ahora podemos declarar:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const category: Categories = 'computing';</code></pre>
</div>

## 18.1. `keyof`

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Category = {
  computing: string;
  multimedia: string;
};
&#8203;
type CategoryType = keyof Category;</code></pre>
</div>

El resultado equivale a:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type CategoryType = 'computing' | 'multimedia';</code></pre>
</div>

---

# 19. Funciones en TypeScript

Podemos indicar explícitamente el tipo de retorno.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function getProduct(): string {
  return 'Keyboard';
}</code></pre>
</div>

También podemos tipar parámetros:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function getFullname(firstName: string, lastName: string): string {
  return `${this.firstName} ${this.lastName}`;
}</code></pre>
</div>

Cuando una función no devuelve nada, usamos `void`.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function printFullname(firstName: string, lastName: string): void {
  console.log(`${this.firstName} ${this.lastName}`);
}</code></pre>
</div>

---

# 20. Parámetros opcionales

Un parámetro opcional se marca con `?`.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function addtoCart(productId: number, quantity?: number) {
  const product = {
    id: productId,
    qty: quantity ?? 1
  };
}</code></pre>
</div>

> 📌 Los parámetros opcionales deben colocarse al final de la firma.

---

# 21. Clases en TypeScript

Podemos convertir una clase JavaScript en TypeScript añadiendo tipos.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class User {
  firstName: string = '';
  lastName: string = '';
  private isActive: boolean = false;
&#8203;
  constructor(
    firstName: string,
    lastName: string,
    isActive: boolean = true
  ) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.isActive = isActive;
  }
&#8203;
  getFullname(): string {
    return `${this.firstName} ${this.lastName}`;
  }
&#8203;
  get active(): boolean {
    return this.isActive;
  }
}</code></pre>
</div>

TypeScript permite utilizar modificadores como `public`, `private` y `protected`.

## 21.1. Propiedades creadas desde el constructor

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">constructor(
  private firstName: string,
  private lastName: string,
  private isActive: boolean = true
) {}</code></pre>
</div>

Al declarar el modificador en el parámetro, TypeScript crea automáticamente las propiedades correspondientes.

---

# 22. Herencia y union types

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">class Customer extends User {
  taxNumber: number;
&#8203;
  constructor(firstName: string, lastName: string) {
    super(firstName, lastName);
  }
}</code></pre>
</div>

Una variable puede aceptar varios tipos mediante `|`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const account: User | Customer = undefined;</code></pre>
</div>

---

# 23. `instanceof`

Podemos comprobar el tipo real de una instancia.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">if (account instanceof Customer) {
  const taxNo = account.taxNumber;
} else {
  const name = account.getFullname();
}</code></pre>
</div>

TypeScript utiliza esta información para saber qué propiedades están disponibles en cada rama.

---

# 24. Interfaces

Una **interface** define un contrato o estructura que otros elementos deben cumplir.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Product {
  name: string;
  price: number;
  getCategories: () =&gt; string[];
}</code></pre>
</div>

Una interfaz puede contener propiedades, métodos y miembros opcionales.

> 💡 Las interfaces desaparecen durante la transpilación y no se incluyen en el JavaScript final.

---

# 25. Implementar una interfaz

Una clase utiliza `implements`.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">class Keyboard implements Product {
  name: string = 'Keyboard';
  price: number = 20;
&#8203;
  getCategories(): string[] {
    return ['Computing', 'Peripherals'];
  }
}</code></pre>
</div>

La clase debe implementar todos los miembros obligatorios de la interfaz.

## 25.1. Propiedades opcionales

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Product {
  name: string;
  price: number;
  getCategories: () =&gt; string[];
  description?: string;
}</code></pre>
</div>

> ✅ Las interfaces son especialmente útiles para tipar datos procedentes de APIs backend.

---

# 26. Type casting

Podemos indicar a TypeScript que trate un objeto como un tipo determinado.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const product = {
  name: 'Keyboard',
  price: 20
} as Product;</code></pre>
</div>

Esto se denomina **type casting**.

> ⚠️ Siempre que sea posible, es preferible definir el tipo correctamente en lugar de forzar un casting.

---

# 27. Genéricos

Los genéricos permiten crear código reutilizable para diferentes tipos.

Supongamos una función concreta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function save(data: Product) {
  localStorage.setItem('Product', JSON.stringify(data));
}</code></pre>
</div>

Si queremos aceptar más tipos podríamos escribir:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function save(data: Product | Keyboard) {
  localStorage.setItem('Product', JSON.stringify(data));
}</code></pre>
</div>

Pero esto no escala bien. La solución genérica es:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function save&lt;T&gt;(data: T) {
  localStorage.setItem('Product', JSON.stringify(data));
}</code></pre>
</div>

`T` representa un tipo que se decidirá cuando se utilice la función.

---

# 28. Utilizar un genérico

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">save&lt;Product&gt;({
  name: 'Microphone',
  price: 45,
  getCategories: () =&gt; ['Peripherals', 'Multimedia']
});</code></pre>
</div>

TypeScript comprueba que el objeto cumple con `Product`.

Esto no sería válido:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">save&lt;Product&gt;('Microphone');</code></pre>
</div>

porque hemos indicado que `T` es `Product`, pero estamos pasando un `string`.

## 28.1. Varios parámetros genéricos

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function save&lt;T, P&gt;(data: T, obj: P) {
  localStorage.setItem('Product', JSON.stringify(data));
}</code></pre>
</div>

---

# 29. Utility Types

Los **utility types** permiten crear nuevos tipos a partir de otros existentes.

## 29.1. `Partial`

Convierte todas las propiedades en opcionales.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const mic: Partial&lt;Product&gt; = {
  name: 'Microphone',
  price: 67
};</code></pre>
</div>

## 29.2. `Pick`

Permite seleccionar solo algunas propiedades de un tipo.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Microphone = Pick&lt;Product, 'name' | 'price'&gt;;
&#8203;
const microphone: Microphone = {
  name: 'Microphone',
  price: 67
};</code></pre>
</div>

## 29.3. `Record`

Permite representar estructuras clave-valor.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Order {
  products: Record&lt;string, number&gt;;
}</code></pre>
</div>

En este caso la clave es `string` y el valor es `number`.

---

# 30. Esquema general

<div class="code-card" data-lang="text">
<div class="code-label">ESQUEMA</div>
<pre><code class="language-text">JavaScript moderno
      │
      ├── let / const
      ├── spread / rest
      ├── arrow functions
      ├── optional chaining
      ├── nullish coalescing
      ├── clases
      └── módulos
              │
              ▼
         TypeScript
              │
      ├── tipos
      ├── funciones tipadas
      ├── clases
      ├── interfaces
      ├── genéricos
      └── utility types
              │
              ▼
           Angular</code></pre>
</div>

---

# 31. Actividad práctica 🧪

Crea la siguiente interfaz:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Student {
  id: number;
  name: string;
  active: boolean;
}</code></pre>
</div>

A continuación:

1. Crea un array de alumnos.
2. Añade una función que reciba un alumno y devuelva su nombre.
3. Crea un tipo con `Pick<Student, 'id' | 'name'>`.
4. Crea otro tipo usando `Partial<Student>`.
5. Implementa una función genérica que muestre cualquier objeto por consola.

Ejemplo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function printData&lt;T&gt;(data: T): void {
  console.log(data);
}</code></pre>
</div>

---

# 32. Preguntas de repaso

1. ¿Qué significa que TypeScript sea un superconjunto de JavaScript?
2. ¿Qué diferencia existe entre `let`, `const` y `var`?
3. ¿Para qué sirve el operador spread?
4. ¿Qué son los rest parameters?
5. ¿Qué ventaja tienen las arrow functions respecto a `this`?
6. ¿Qué hace `?.`?
7. ¿Qué hace `??`?
8. ¿Qué es la transpilación?
9. ¿Para qué sirve `tsconfig.json`?
10. ¿Qué diferencia existe entre `any` y `unknown`?
11. ¿Qué es un union type?
12. ¿Qué es una interface?
13. ¿Qué hace `implements`?
14. ¿Qué es un genérico?
15. ¿Para qué sirven `Partial`, `Pick` y `Record`?

---

# 33. Resumen

En esta unidad hemos repasado las características de JavaScript necesarias para comprender mejor TypeScript y Angular.

Hemos visto:

- declaración moderna de variables;
- spread y rest;
- funciones flecha;
- optional chaining;
- nullish coalescing;
- clases y módulos;
- instalación de TypeScript;
- transpilación;
- tipos básicos;
- tipos personalizados;
- funciones tipadas;
- clases;
- interfaces;
- genéricos;
- utility types.

TypeScript proporciona una capa de seguridad sobre JavaScript gracias al tipado estático y al análisis realizado antes de ejecutar el código.

---

# 34. Conceptos que debes recordar ⭐

| Concepto | Significado |
|---|---|
| `let` | Variable reasignable con ámbito de bloque |
| `const` | Referencia no reasignable |
| Spread | Expande elementos con `...` |
| Rest | Agrupa parámetros con `...` |
| Arrow function | Función compacta con contexto léxico de `this` |
| Optional chaining | Acceso seguro mediante `?.` |
| Nullish coalescing | Valor por defecto mediante `??` |
| TypeScript | Superconjunto tipado de JavaScript |
| `tsc` | Compilador/transpilador de TypeScript |
| `string`, `number`, `boolean` | Tipos básicos |
| `any` | Tipo dinámico sin comprobación estricta |
| `unknown` | Tipo dinámico más seguro |
| Union type | Combinación de tipos con `|` |
| Interface | Contrato de estructura |
| Generic | Tipo parametrizable |
| `Partial` | Hace opcionales todas las propiedades |
| `Pick` | Selecciona propiedades |
| `Record` | Define pares clave-valor |

---

## 🔗 Recursos mencionados en el material

- TypeScript: <https://www.typescriptlang.org>
- `tsconfig`: <https://www.typescriptlang.org/tsconfig>
- Utility Types: <https://www.typescriptlang.org/docs/handbook/utility-types.html>
- Node.js: <https://nodejs.org>
- Git: <https://git-scm.com>
- Visual Studio Code: <https://code.visualstudio.com>

---

> 🎓 **Siguiente paso**  
> Una vez dominados estos fundamentos de TypeScript, el siguiente paso es aplicarlos dentro de Angular mediante **componentes**, plantillas y composición de interfaces de usuario.

---

# 35. Taller práctico de TypeScript 🧪

Los siguientes ejercicios están pensados para realizarse tomando como referencia la carpeta del repositorio:

<https://github.com/hokahey007/codebase-angular/tree/main/02_introduccion_a_typescript>

El objetivo no es introducir todavía conceptos nuevos de Angular, sino **practicar de forma progresiva el JavaScript moderno y el TypeScript que después utilizaremos dentro de los componentes, servicios y demás elementos de Angular**.

> 🎯 **Forma de trabajo recomendada**  
> Antes de modificar un ejemplo del repositorio, ejecútalo, predice qué resultado debería producir y crea una copia para tus pruebas. Intenta provocar también errores de compilación: aprender a interpretar los mensajes de TypeScript forma parte de la práctica.

---

## Ejercicio 1. Reconoce los ejemplos de la unidad

### Objetivo

Relacionar el contenido teórico con los ejemplos de código de la carpeta `02_introduccion_a_typescript`.

### Tareas

1. Clona el repositorio, si todavía no lo tienes.
2. Entra en `02_introduccion_a_typescript`.
3. Abre la carpeta con VS Code.
4. Localiza ejemplos relacionados con:
   - variables;
   - funciones;
   - clases;
   - módulos;
   - tipos;
   - interfaces;
   - genéricos;
   - utility types.
5. Para cada archivo, anota qué concepto de la unidad representa.

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">git clone https://github.com/hokahey007/aprendiendo-angular-con-codigo.git
cd aprendiendo-angular-con-codigo/02_introduccion_a_typescript
code .</code></pre>
</div>

### Comprueba tu entorno

Ejecuta:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">node -v
npm -v
tsc -v</code></pre>
</div>

Si `tsc` no está disponible, recuerda que TypeScript puede instalarse globalmente con:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">npm install -g typescript</code></pre>
</div>

---

## Ejercicio 2. `var`, `let` y `const`: predice antes de ejecutar

### Objetivo

Comprender el ámbito y la reasignación de variables.

Crea un archivo `variables.ts` con este código:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let price = 100;
price = 80;
const product = {
  name: 'Keyboard',
  price: 75
};
product.price = 60;
console.log(price);
console.log(product);</code></pre>
</div>

### Tareas

1. Predice la salida antes de ejecutar.
2. Transpila y ejecuta el archivo.
3. Intenta añadir:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">product = {
  name: 'Mouse',
  price: 25
};</code></pre>
</div>

4. Explica por qué TypeScript/JavaScript permite cambiar `product.price`, pero no sustituir la referencia completa de `product`.
5. Cambia `let price` por `const price` y comprueba el error.

### Pregunta

¿En qué casos utilizarías `let` y en cuáles `const`?

---

## Ejercicio 3. Inmutabilidad con spread

### Objetivo

Utilizar `...` para crear nuevos arrays y objetos sin modificar los originales.

Partiendo de:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const categories = ['Gaming', 'Multimedia'];
const product = {
  name: 'Keyboard',
  price: 75
};</code></pre>
</div>

crea:

1. Un nuevo array que añada `'Computing'`.
2. Un nuevo producto cuyo precio sea `100`.
3. Un nuevo producto que además tenga una propiedad `category`.
4. Mensajes por consola que demuestren que los originales siguen conservando sus valores.

Una posible comprobación es:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const productCategories = [...categories, 'Computing'];
const newProduct = {
  ...product,
  price: 100,
  category: 'Computing'
};
console.log(categories);
console.log(productCategories);
console.log(product);
console.log(newProduct);</code></pre>
</div>

### Reto

Cambia el orden de las propiedades:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const anotherProduct = {
  price: 100,
  ...product
};</code></pre>
</div>

¿Qué valor final tendrá `price`? Explica por qué.

---

## Ejercicio 4. Parámetros por defecto y rest parameters

### Objetivo

Practicar firmas de funciones flexibles.

Implementa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function addToCart(productId: number, quantity: number = 1) {
  return {
    id: productId,
    qty: quantity
  };
}
function addProduct(name: string, ...categories: string[]) {
  return {
    name,
    categories: categories.join(',')
  };
}</code></pre>
</div>

### Tareas

Prueba las siguientes llamadas:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">console.log(addToCart(10));
console.log(addToCart(10, 4));
console.log(addProduct('Keyboard'));
console.log(addProduct('Keyboard', 'Computing', 'Peripherals'));</code></pre>
</div>

Después:

1. Explica qué valor toma `quantity` cuando no se proporciona.
2. Explica qué tipo tiene `categories`.
3. Intenta colocar un parámetro obligatorio después de uno opcional o con una firma que TypeScript no permita.
4. Observa el diagnóstico del compilador.

---

## Ejercicio 5. Funciones flecha

### Objetivo

Simplificar funciones y comprender el comportamiento léxico de `this`.

Convierte esta función:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function discount(price: number): number {
  return (price / 100) * 10;
}</code></pre>
</div>

en una arrow function de una sola expresión.

### Resultado esperado

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const discount = (price: number): number =&gt; (price / 100) * 10;</code></pre>
</div>

### Segunda parte: `this`

Estudia el ejemplo de la unidad relacionado con `setTimeout`. Prueba primero una función tradicional y después una arrow function.

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">class ProductPrinter {
  constructor(private name: string) {}
  printLater(): void {
    setTimeout(() =&gt; {
      console.log('Product name is:', this.name);
    }, 500);
  }
}
const printer = new ProductPrinter('Monitor');
printer.printLater();</code></pre>
</div>

Explica por qué la arrow function puede acceder al `this` de `ProductPrinter`.

---

## Ejercicio 6. Optional chaining y nullish coalescing

### Objetivo

Trabajar de forma segura con valores que podrían no existir.

Define:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Order = {
  product?: {
    name?: string;
  };
};
const order: Order = {};
const name = order?.product?.name ?? 'Producto desconocido';
console.log(name);</code></pre>
</div>

### Tareas

Prueba tres casos:

1. `order` sin producto.
2. `order` con producto pero sin nombre.
3. `order` con `product.name = 'Keyboard'`.

### Explica

¿Qué parte resuelve `?.` y qué parte resuelve `??`?

### Reto conceptual

Compara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const quantityA = qty ? qty : 1;
const quantityB = qty ?? 1;</code></pre>
</div>

¿Qué ocurriría si `qty` fuese `0`?

---

## Ejercicio 7. Transpilación y `tsconfig.json`

### Objetivo

Observar qué hace realmente el compilador TypeScript.

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const title = 'Hello TypeScript!';
console.log(title);</code></pre>
</div>

y transpílalo:

<div class="code-card" data-lang="bash">
<div class="code-label">TERMINAL / BASH</div>
<pre><code class="language-bash">tsc app.ts</code></pre>
</div>

Después crea un `tsconfig.json`:

<div class="code-card" data-lang="json">
<div class="code-label">JSON</div>
<pre><code class="language-json">{
  "compilerOptions": {
    "target": "ES2022"
  }
}</code></pre>
</div>

### Tareas

1. Compara el `.ts` y el `.js` generado.
2. Ejecuta `tsc` sin indicar nombre de archivo.
3. Comprueba qué archivos transpila.
4. Cambia temporalmente el `target` y compara el JavaScript generado.
5. Comprueba que las anotaciones de tipos no aparecen en el `.js`.

> 🧠 La finalidad del ejercicio es distinguir claramente **código TypeScript de desarrollo** y **JavaScript ejecutable**.

---

## Ejercicio 8. Inferencia de tipos y errores de compilación

### Objetivo

Aprender cuándo TypeScript puede inferir un tipo sin necesidad de escribirlo.

Crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let productName = 'Keyboard';
let price = 100;
let active = true;</code></pre>
</div>

Sin añadir anotaciones explícitas:

1. Pasa el ratón sobre cada variable en VS Code.
2. Anota el tipo inferido.
3. Intenta después:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">productName = 200;
price = 'cien';
active = 1;</code></pre>
</div>

4. Explica los mensajes de error.

### Segunda parte

Compara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let dataAny: any = 'Keyboard';
dataAny = 100;
dataAny.toUpperCase();
let dataUnknown: unknown = 'Keyboard';</code></pre>
</div>

Intenta ejecutar directamente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">dataUnknown.toUpperCase();</code></pre>
</div>

y corrígelo comprobando primero el tipo:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">if (typeof dataUnknown === 'string') {
  console.log(dataUnknown.toUpperCase());
}</code></pre>
</div>

### Pregunta

¿Por qué `unknown` ofrece más seguridad que `any`?

---

## Ejercicio 9. Arrays tipados

### Objetivo

Comprobar cómo TypeScript protege el contenido de una colección.

Declara las dos formas vistas en la unidad:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const categoriesA: string[] = ['Computing', 'Multimedia'];
const categoriesB: Array&lt;string&gt; = ['Computing', 'Multimedia'];</code></pre>
</div>

### Tareas

1. Añade `'Peripherals'`.
2. Intenta añadir `25`.
3. Observa el error.
4. Crea un array de precios.
5. Crea un array que pueda contener `string` o `number`.

Para la última parte:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const values: Array&lt;string | number&gt; = ['Keyboard', 75];</code></pre>
</div>

---

## Ejercicio 10. Tipos personalizados y `keyof`

### Objetivo

Crear tipos que restrinjan los valores permitidos.

Define:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Categories = 'computing' | 'multimedia';
const category: Categories = 'computing';</code></pre>
</div>

### Tareas

1. Intenta asignar `'office'`.
2. Amplía el tipo para aceptar `'peripherals'`.
3. Construye ahora:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Category = {
  computing: string;
  multimedia: string;
  peripherals: string;
};
type CategoryType = keyof Category;</code></pre>
</div>

4. Crea una variable `CategoryType`.
5. Prueba valores válidos e inválidos.
6. Explica qué relación existe entre `keyof Category` y las claves del objeto.

---

## Ejercicio 11. Funciones tipadas y parámetros opcionales

### Objetivo

Definir tipos de parámetros y retorno.

Implementa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function getProduct(): string {
  return 'Keyboard';
}
function printFullname(firstName: string, lastName: string): void {
  console.log(`${firstName} ${lastName}`);
}
function addToCart(productId: number, quantity?: number) {
  return {
    id: productId,
    qty: quantity ?? 1
  };
}</code></pre>
</div>

### Tareas

1. Cambia temporalmente `getProduct()` para devolver un número.
2. Observa el error.
3. Llama a `printFullname` con valores que no sean `string`.
4. Ejecuta `addToCart(10)` y `addToCart(10, 5)`.
5. Explica por qué `quantity` puede ser omitido.

---

## Ejercicio 12. Clase `User`

### Objetivo

Aplicar tipado a propiedades, constructor, métodos y accesores.

Implementa una versión TypeScript de `User`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class User {
  firstName: string = '';
  lastName: string = '';
  private isActive: boolean = false;
  constructor(
    firstName: string,
    lastName: string,
    isActive: boolean = true
  ) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.isActive = isActive;
  }
  getFullname(): string {
    return `${this.firstName} ${this.lastName}`;
  }
  get active(): boolean {
    return this.isActive;
  }
}</code></pre>
</div>

### Tareas

1. Crea dos usuarios.
2. Muestra su nombre completo.
3. Consulta `active`.
4. Intenta acceder directamente a `isActive`.
5. Explica el error.
6. Reescribe la clase usando propiedades creadas directamente desde el constructor.

### Versión abreviada a investigar

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export class User {
  constructor(
    public firstName: string,
    public lastName: string,
    private isActive: boolean = true
  ) {}
  getFullname(): string {
    return `${this.firstName} ${this.lastName}`;
  }
  get active(): boolean {
    return this.isActive;
  }
}</code></pre>
</div>

Compara ambas implementaciones: ¿qué código evita escribir TypeScript en la segunda?

---

## Ejercicio 13. Herencia e `instanceof`

### Objetivo

Comprender el estrechamiento de tipos (*type narrowing*) mediante `instanceof`.

Crea `Customer`:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">import { User } from './user';
export class Customer extends User {
  constructor(
    firstName: string,
    lastName: string,
    public taxNumber: number
  ) {
    super(firstName, lastName);
  }
}</code></pre>
</div>

Después declara:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let account: User | Customer;
account = new Customer('Ada', 'Lovelace', 12345);
if (account instanceof Customer) {
  console.log(account.taxNumber);
} else {
  console.log(account.getFullname());
}</code></pre>
</div>

### Tareas

1. Ejecuta el código con un `Customer`.
2. Ejecuta el mismo flujo con un `User`.
3. Intenta acceder a `taxNumber` sin comprobar primero el tipo.
4. Explica cómo `instanceof` ayuda al compilador.

---

## Ejercicio 14. Módulos: `export` e `import`

### Objetivo

Separar responsabilidades en archivos.

Organiza el ejercicio anterior así:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">src/
├── user.ts
├── customer.ts
└── main.ts</code></pre>
</div>

En `user.ts`, exporta `User`.  
En `customer.ts`, importa `User` y exporta `Customer`.  
En `main.ts`, importa ambas clases.

### Preguntas

1. ¿Qué ocurre si eliminas `export` de `User`?
2. ¿Qué ocurre si escribes mal la ruta de importación?
3. ¿Por qué separar `User` y `Customer` mejora la organización frente a colocar todo en un único archivo?

---

## Ejercicio 15. Interfaces como contrato

### Objetivo

Comprobar que una clase debe respetar la estructura de una interfaz.

Define:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Product {
  name: string;
  price: number;
  getCategories: () =&gt; string[];
  description?: string;
}</code></pre>
</div>

Implementa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">class Keyboard implements Product {
  name = 'Keyboard';
  price = 20;
  getCategories(): string[] {
    return ['Computing', 'Peripherals'];
  }
}</code></pre>
</div>

### Tareas

1. Elimina temporalmente `price`.
2. Observa el error.
3. Elimina `getCategories`.
4. Observa el error.
5. No declares `description`.
6. Explica por qué en este caso no aparece error.
7. Crea una clase `Monitor` que también implemente `Product`.

> ✅ Relaciona este ejercicio con un caso habitual en Angular: tipar los objetos recibidos desde una API.

---

## Ejercicio 16. Type casting: útil, pero peligroso

### Objetivo

Entender que `as` no crea automáticamente las propiedades que faltan.

Prueba:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Product {
  name: string;
  price: number;
  getCategories: () =&gt; string[];
}
const product = {
  name: 'Keyboard',
  price: 20
} as Product;</code></pre>
</div>

### Tareas

1. Comprueba que TypeScript acepta el casting.
2. Ejecuta:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">console.log(product.getCategories());</code></pre>
</div>

3. Analiza qué ocurre en tiempo de ejecución.
4. Reescribe el código evitando el casting y haciendo que el objeto cumpla realmente la interfaz.

### Reflexión

¿Por qué un `as Product` no significa que el objeto se haya transformado realmente en un `Product`?

---

## Ejercicio 17. Funciones genéricas

### Objetivo

Crear una operación independiente del tipo concreto.

Implementa:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function save&lt;T&gt;(data: T): void {
  localStorage.setItem('data', JSON.stringify(data));
}</code></pre>
</div>

Si ejecutas el ejemplo fuera del navegador, sustituye temporalmente `localStorage` por un `console.log`.

### Tareas

1. Invoca la función con un `Product`.
2. Invócala con un `User`.
3. Invócala con un `string`.
4. Fuerza explícitamente:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">save&lt;Product&gt;('Microphone');</code></pre>
</div>

5. Explica el error.

### Reto

Crea una función:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function first&lt;T&gt;(items: T[]): T | undefined {
  return items[0];
}</code></pre>
</div>

y pruébala con arrays de `string`, `number` y `Product`.

---

## Ejercicio 18. `Partial`, `Pick` y `Record`

### Objetivo

Derivar tipos nuevos sin duplicar definiciones.

Partiendo de `Product`, crea:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const draft: Partial&lt;Product&gt; = {
  name: 'Microphone'
};
type ProductSummary = Pick&lt;Product, 'name' | 'price'&gt;;
const summary: ProductSummary = {
  name: 'Keyboard',
  price: 20
};
interface Order {
  products: Record&lt;string, number&gt;;
}</code></pre>
</div>

### Tareas

1. Añade nuevas propiedades a `draft`.
2. Intenta añadir a `summary` una propiedad que no pertenezca al tipo seleccionado.
3. Crea un pedido:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const order: Order = {
  products: {
    Keyboard: 2,
    Mouse: 1,
    Monitor: 3
  }
};</code></pre>
</div>

4. Intenta utilizar un texto como cantidad.
5. Explica qué restricción impone `Record<string, number>`.

---

# 36. Reto integrador: catálogo tipado de productos 🚀

### Objetivo

Integrar los conceptos esenciales de la unidad en un pequeño programa TypeScript independiente de Angular.

Crea esta estructura:

<div class="code-card" data-lang="text">
<div class="code-label">SALIDA / TEXTO</div>
<pre><code class="language-text">catalogo/
├── models/
│   ├── product.ts
│   └── category.ts
├── services/
│   └── catalog.ts
├── app.ts
└── tsconfig.json</code></pre>
</div>

## Requisitos

### 1. Categorías

Define un tipo restringido:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export type Category =
  | 'computing'
  | 'multimedia'
  | 'peripherals';</code></pre>
</div>

### 2. Producto

Crea una interfaz:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">export interface Product {
  id: number;
  name: string;
  price: number;
  category: Category;
  description?: string;
}</code></pre>
</div>

### 3. Catálogo

Crea una clase que mantenga internamente un array de productos y tenga, al menos:

- `add(product: Product): void`
- `findById(id: number): Product | undefined`
- `findByCategory(category: Category): Product[]`
- `update(id: number, changes: Partial<Product>): Product | undefined`

### 4. Implementa `update` sin modificar directamente el producto original

Utiliza spread:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const updatedProduct = {
  ...product,
  ...changes
};</code></pre>
</div>

### 5. Añade una función genérica

Crea una función reutilizable:

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function printData&lt;T&gt;(data: T): void {
  console.log(JSON.stringify(data, null, 2));
}</code></pre>
</div>

### 6. Programa principal

Crea varios productos y comprueba:

- alta de productos;
- búsqueda por identificador;
- filtrado por categoría;
- actualización mediante `Partial<Product>`;
- impresión mediante la función genérica.

## Condiciones

- [ ] No utilizar `any`.
- [ ] Utilizar inferencia cuando el tipo sea evidente.
- [ ] Utilizar una interfaz para `Product`.
- [ ] Utilizar un tipo personalizado para `Category`.
- [ ] Utilizar al menos un union type.
- [ ] Utilizar `Partial`.
- [ ] Utilizar spread.
- [ ] Separar el código en módulos.
- [ ] Compilar sin errores con `tsc`.
- [ ] Ejecutar el JavaScript generado con Node.js cuando sea aplicable.

---

# 37. Ejercicios de diagnóstico de errores 🔎

En los siguientes fragmentos **no corrijas inmediatamente el código**. Primero escribe qué error esperas que detecte TypeScript.

## Caso A

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">const price: number = '100';</code></pre>
</div>

## Caso B

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">interface Product {
  name: string;
  price: number;
}
const product: Product = {
  name: 'Keyboard'
};</code></pre>
</div>

## Caso C

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">function calculate(price: number): number {
  console.log(price);
}</code></pre>
</div>

## Caso D

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">type Category = 'computing' | 'multimedia';
const category: Category = 'office';</code></pre>
</div>

## Caso E

<div class="code-card" data-lang="typescript">
<div class="code-label">TYPESCRIPT</div>
<pre><code class="language-typescript">let value: unknown = 'Angular';
console.log(value.toUpperCase());</code></pre>
</div>

Para cada caso indica:

1. Qué regla de tipos se incumple.
2. Si el problema se detecta en compilación o solo en ejecución.
3. Cómo lo corregirías sin recurrir a `any` ni a un casting innecesario.

---

# 38. Cuestiones finales de reflexión 📝

1. ¿Por qué TypeScript puede detectar errores que JavaScript aceptaría hasta tiempo de ejecución?
2. ¿Qué significa realmente que los tipos desaparezcan durante la transpilación?
3. ¿Cuándo es preferible dejar que TypeScript infiera un tipo?
4. ¿Por qué abusar de `any` reduce una de las principales ventajas de TypeScript?
5. ¿Qué diferencia conceptual existe entre `any` y `unknown`?
6. Explica con un ejemplo la diferencia entre spread y rest.
7. ¿Qué ventaja aporta `?.` cuando trabajamos con datos externos?
8. ¿Qué diferencia existe entre `||` y `??` cuando un valor válido puede ser `0`?
9. ¿Qué diferencia existe entre una clase y una interfaz en TypeScript?
10. ¿Qué aporta `implements`?
11. ¿Qué información obtiene TypeScript después de una comprobación con `instanceof`?
12. ¿Por qué los módulos favorecen el principio de responsabilidad única?
13. ¿Qué problema puede ocultar un type casting incorrecto?
14. ¿Qué resuelven los genéricos frente a declarar una larga lista de union types?
15. Pon un ejemplo real en el que `Partial<Product>` sea útil.
16. ¿Para qué utilizarías `Pick`?
17. ¿Qué estructura representa bien `Record<string, number>`?
18. ¿Qué conceptos de esta unidad crees que aparecerán con más frecuencia cuando empecemos a programar componentes Angular?

---

> ✅ **Meta de aprendizaje**  
> Si puedes resolver el reto integrador, explicar los errores de la sección de diagnóstico y justificar por qué eliges un tipo, interfaz, clase o genérico concreto, estás preparado para utilizar TypeScript como base del desarrollo con Angular.
