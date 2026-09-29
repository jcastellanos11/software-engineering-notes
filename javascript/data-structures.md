# JavaScript — Estructuras de Datos y Fundamentos para Entrevistas

> Guía de estudio y repaso rápido para ejercicios tipo Codility, LeetCode e entrevistas técnicas.
>
> Objetivo: entender **qué estructura usar, cuándo usarla, cómo funciona y cuál es su complejidad**.

---

## 1. Array

Un `Array` es una colección **ordenada** de elementos.

```js
const numbers = [10, 20, 30, 40];

console.log(numbers[0]); // 10
console.log(numbers[2]); // 30
```

Los índices empiezan en `0`:

```text
index:   0   1   2   3
value:  10  20  30  40
```

### Operaciones importantes

```js
const numbers = [1, 2, 3];

numbers.push(4);     
// Agrega un elemento al final del array.

numbers.pop();       
// Elimina y devuelve el último elemento del array.

numbers.unshift(0);  
// Agrega un elemento al inicio del array.

numbers.shift();     
// Elimina y devuelve el primer elemento del array.

numbers.includes(2); 
// Devuelve true o false según si el valor existe dentro del array.

numbers.indexOf(3);  
// Devuelve el índice de la primera aparición del valor; si no existe, devuelve -1.

numbers.length;      
// Devuelve la cantidad de elementos que contiene el array.
```

### Recorrer un Array

Con índice:

```js
for (let i = 0; i < numbers.length; i++) {
    console.log(numbers[i]);
}
```

Con `for...of`:

```js
for (const number of numbers) {
    console.log(number);
}
```

### Complejidad de las operaciones del Array

La siguiente tabla muestra el costo aproximado de cada operación en términos de Big-O.

| Operación | Complejidad |
|---|---:|
| Acceso por índice `arr[i]` | O(1) |
| `push()` | O(1) amortizado |
| `pop()` | O(1) |
| `shift()` | O(n) |
| `unshift()` | O(n) |
| `includes()` | O(n) |
| `indexOf()` | O(n) |
| Recorrer todo el array | O(n) |

### Cuándo usarlo

Usa `Array` cuando necesites:

- Mantener un orden.
- Acceder por posición.
- Recorrer una lista.
- Guardar una secuencia de valores.

---

## 2. Object

Un `Object` almacena pares:

```text
clave → valor
```

Ejemplo:

```js
const person = {
    name: "Janer",
    age: 31,
    role: "Developer"
};
```

Acceso:

```js
console.log(person.name);
console.log(person["name"]);
```

### Modificar propiedades

```js
person.age = 32;
person.country = "Colombia";
```

### Un Object también puede servir como contador

```js
const numbers = [1, 2, 2, 3, 3, 3];

const counter = {};

for (const number of numbers) {
    counter[number] = (counter[number] || 0) + 1;
}

console.log(counter);
```

Resultado:

```js
{
    1: 1,
    2: 2,
    3: 3
}
```

### Importante

En un objeto tradicional, las claves son principalmente `string` o `symbol`.

```js
const obj = {};

obj[1] = "uno";

console.log(obj);
// { "1": "uno" }
```

El número `1` terminó convertido en la clave string `"1"`.

### Cuándo usarlo

Usa `Object` cuando quieras representar una entidad con propiedades:

```js
const user = {
    id: 1,
    name: "Janer",
    active: true
};
```

---

## 3. Set

Un `Set` almacena valores **únicos**.

```js
const numbers = new Set();

numbers.add(5);
numbers.add(10);
numbers.add(5);

console.log(numbers);
// Set(2) { 5, 10 }
```

### Métodos principales

```js
const seen = new Set();

seen.add(10);

seen.has(10);    // true
seen.has(20);    // false

seen.delete(10);

seen.size;
```

### Idea mental

Cuando un problema diga:

> "¿Ya vi este elemento?"

piensa en:

```js
Set
```

Ejemplo:

```js
function hasDuplicate(numbers) {
    const seen = new Set();

    for (const number of numbers) {
        if (seen.has(number)) {
            return true;
        }

        seen.add(number);
    }

    return false;
}
```

### Complejidad promedio de las operaciones del Set

En un `Set`, las operaciones principales suelen ejecutarse en tiempo constante promedio.

| Operación | Complejidad promedio |
|---|---:|
| `add()` | O(1) |
| `has()` | O(1) |
| `delete()` | O(1) |

---

## 4. Set y objetos: comparación por referencia

Este punto es muy importante en JavaScript.

```js
const prueba = new Set();

prueba.add({ x: 1 });
prueba.add({ x: 1 });
prueba.add({ x: 1 });

console.log(prueba.size);
// 3
```

Aunque los objetos tienen el mismo contenido, **son objetos distintos**.

```js
console.log({ x: 1 } === { x: 1 });
// false
```

JavaScript compara objetos por **referencia**.

### Mismo contenido, distinta referencia

```js
const a = { x: 1 };
const b = { x: 1 };

console.log(a === b);
// false
```

### Misma referencia

```js
const a = { x: 1 };
const b = a;

console.log(a === b);
// true
```

Con `Set`:

```js
const set = new Set();

const obj = { x: 1 };

set.add(obj);
set.add(obj);
set.add(obj);

console.log(set.size);
// 1
```

### También ocurre con arrays

```js
console.log([1, 2] === [1, 2]);
// false
```

Pero:

```js
const a = [1, 2];
const b = a;

console.log(a === b);
// true
```

### Regla mental

```text
Primitivos → se comparan por valor.

Objetos / arrays / funciones → se comparan por referencia.
```

---

## 5. Map

Un `Map` almacena pares:

```text
clave → valor
```

Ejemplo:

```js
const ages = new Map();

ages.set("Janer", 31);
ages.set("Aura", 25);

console.log(ages.get("Janer"));
// 31
```

### Métodos principales

```js
map.set(key, value);

map.get(key);

map.has(key);

map.delete(key);

map.size;
```

### Contar frecuencias

Uno de los patrones más importantes en entrevistas:

```js
const numbers = [1, 2, 2, 3, 3, 3];

const counter = new Map();

for (const number of numbers) {
    counter.set(
        number,
        (counter.get(number) || 0) + 1
    );
}
```

Resultado conceptual:

```text
1 → 1
2 → 2
3 → 3
```

---

## 6. Operadores útiles: `||`, `??` y el operador ternario

### Operador OR lógico: `||`

`||` se llama **operador OR lógico** (*logical OR*).

Su comportamiento básico es:

```js
valorIzquierda || valorDerecha
```

JavaScript devuelve el valor de la izquierda si este es **truthy**.  
Si el valor de la izquierda es **falsy**, devuelve el valor de la derecha.

Ejemplos:

```js
const name = "" || "Sin nombre";
console.log(name);
// "Sin nombre"

const age = 31 || 18;
console.log(age);
// 31
```

Valores considerados **falsy** incluyen:

```js
false
0
""
null
undefined
NaN
```

Por eso `||` se usa frecuentemente para establecer valores por defecto:

```js
const username = inputName || "Invitado";
```

Sin embargo, hay que tener cuidado: si `0`, `false` o `""` son valores válidos, `||` los reemplazará igualmente.

---

### Operador de fusión nula: `??`

`??` se llama **nullish coalescing operator** u **operador de fusión nula**.

```js
valorIzquierda ?? valorPorDefecto
```

Solo usa el valor de la derecha cuando el valor de la izquierda es:

```js
null
undefined
```

Ejemplo:

```js
const score = 0 ?? 100;

console.log(score);
// 0
```

Con `||`:

```js
const score = 0 || 100;

console.log(score);
// 100
```

Regla práctica:

```text
|| → usa el valor de la derecha si la izquierda es falsy.

?? → usa el valor de la derecha solamente si la izquierda es null o undefined.
```

---

### Operador ternario: `? :`

El operador ternario **sí es otro operador diferente**.

Su sintaxis es:

```js
condicion ? valorSiTrue : valorSiFalse
```

Ejemplo:

```js
const age = 20;

const message = age >= 18
    ? "Mayor de edad"
    : "Menor de edad";
```

Es una forma compacta de escribir:

```js
let message;

if (age >= 18) {
    message = "Mayor de edad";
} else {
    message = "Menor de edad";
}
```

Regla mental:

```text
||  → OR lógico / valor alternativo si el primero es falsy
??  → valor alternativo si es null o undefined
?:  → elegir entre dos valores según una condición
```

---

## 7. Set vs Map

Esta diferencia es fundamental.

### Set

Usa `Set` cuando solamente necesitas saber:

> ¿Este valor existe o ya apareció?

```text
Set {
    5,
    10,
    20
}
```

Ejemplo:

```js
const seen = new Set();

seen.add(5);
seen.has(5); // true
```

### Map

Usa `Map` cuando necesitas guardar información asociada:

```text
5  → 3
10 → 2
20 → 1
```

Ejemplo:

```js
const frequency = new Map();

frequency.set(5, 3);
frequency.set(10, 2);
```

### Regla mental

```text
Set → valores únicos.

Map → clave + valor.
```

---

## 8. Stack — Pila

Un `Stack` sigue el principio:

```text
LIFO
Last In, First Out
```

El último elemento en entrar es el primero en salir.

En JavaScript normalmente se implementa usando un `Array`.

```js
const stack = [];

stack.push("A");
stack.push("B");
stack.push("C");

console.log(stack.pop());
// C
```

Operaciones principales:

```js
stack.push(value);
stack.pop();
```

### Usos comunes

- Validar paréntesis.
- Undo / redo.
- Recorridos de árboles.
- Expresiones matemáticas.
- Navegación.
- Algoritmos DFS.

Ejemplo conceptual:

```text
push A
push B
push C

Stack:
C
B
A

pop() → C
```

---

## 9. Queue — Cola

Una `Queue` sigue:

```text
FIFO
First In, First Out
```

El primero en entrar es el primero en salir.

Versión simple:

```js
const queue = [];

queue.push("Janer");
queue.push("Aura");
queue.push("Pedro");

console.log(queue.shift());
// Janer
```

Conceptualmente:

```js
queue.push(value); // agregar al final
queue.shift();     // sacar del inicio
```

### Importante

`shift()` normalmente cuesta:

```text
O(n)
```

porque los elementos restantes deben reajustar sus índices.

Para algoritmos grandes, puede ser mejor usar un índice:

```js
const queue = [];
let front = 0;

queue.push(10);
queue.push(20);
queue.push(30);

console.log(queue[front++]); // 10
console.log(queue[front++]); // 20
```

### Usos comunes

- BFS.
- Procesamiento de tareas.
- Sistemas de turnos.
- Colas de eventos.

---

## 10. String

Un `String` representa texto.

```js
const word = "javascript";
```

Acceso por posición:

```js
word[0]; // "j"
word[1]; // "a"
```

Recorrido:

```js
for (const letter of word) {
    console.log(letter);
}
```

### Métodos útiles

```js
word.length;

word.includes("script");

word.toLowerCase();

word.toUpperCase();

word.split("");
```

Invertir una cadena:

```js
const reversed = "hello"
    .split("")
    .reverse()
    .join("");

console.log(reversed);
// "olleh"
```

### Importante: los strings son inmutables

Esto no modifica el string:

```js
const name = "Janer";

name[0] = "P";

console.log(name);
// "Janer"
```

Necesitas crear otro string.

---

# 11. `let` vs `const`

## `let`

Permite reasignación.

```js
let age = 30;

age = 31;
```

Correcto.

---

## `const`

No permite reasignar la variable.

```js
const age = 30;

age = 31;
// TypeError
```

Pero `const` **no significa que un objeto sea inmutable**.

Esto es válido:

```js
const numbers = [1, 2, 3];

numbers.push(4);

console.log(numbers);
// [1, 2, 3, 4]
```

Porque no estás asignando un array nuevo a `numbers`.

Esto no es válido:

```js
const numbers = [1, 2, 3];

numbers = [4, 5, 6];
// Error
```

Con objetos ocurre lo mismo:

```js
const person = {
    name: "Janer"
};

person.name = "John"; // válido
```

Pero:

```js
person = {
    name: "Aura"
};
// Error
```

### Regla recomendada

```text
Usa const por defecto.

Usa let cuando necesites reasignar.
```

Ejemplo:

```js
const numbers = [1, 2, 3];
const seen = new Set();
const counter = new Map();

let total = 0;
let current = null;
```

---

# 12. Tabla comparativa

| Estructura | Qué guarda | Mantiene orden | Duplicados | Acceso / búsqueda típica | Caso de uso |
|---|---|---:|---:|---|---|
| `Array` | Lista de valores | Sí | Sí | índice O(1), búsqueda O(n) | listas ordenadas |
| `Object` | clave → valor | No usar el orden como garantía lógica | claves únicas | propiedad ~O(1) promedio | entidades/configuración |
| `Set` | valores únicos | Iteración en orden de inserción | No para la misma identidad/valor | `has()` O(1) promedio | saber si algo existe |
| `Map` | clave → valor | Iteración en orden de inserción | claves únicas | `get()` / `has()` O(1) promedio | frecuencias, asociaciones |
| `Stack` | secuencia LIFO | Sí | Sí | `push/pop` O(1) | paréntesis, DFS, undo |
| `Queue` | secuencia FIFO | Sí | Sí | depende de implementación | BFS, turnos |
| `String` | texto | Sí | Sí | índice O(1) práctico | procesamiento de texto |

---

# 13. Object vs Map

Ambos permiten representar:

```text
clave → valor
```

Pero no son exactamente iguales.

| Característica | Object | Map |
|---|---|---|
| Claves | `string` / `symbol` principalmente | cualquier valor |
| `.size` | No | Sí |
| `.get()` / `.set()` | No | Sí |
| Iteración | posible, pero menos directa | directa |
| Frecuencias / algoritmos | válido | generalmente más claro |
| Modelar entidades | excelente | menos habitual |

Ejemplo ideal para `Object`:

```js
const user = {
    name: "Janer",
    age: 31,
    active: true
};
```

Ejemplo ideal para `Map`:

```js
const frequency = new Map();

frequency.set("a", 3);
frequency.set("b", 5);
```

---

# 14. Patrones importantes para entrevistas

## Patrón 1 — Detectar duplicados

Pregunta:

> ¿Algún valor ya apareció?

Piensa:

```text
Set
```

```js
function hasDuplicate(numbers) {
    const seen = new Set();

    for (const number of numbers) {
        if (seen.has(number)) {
            return true;
        }

        seen.add(number);
    }

    return false;
}
```

---

## Patrón 2 — Primer duplicado encontrado

```js
function firstDuplicate(numbers) {
    const seen = new Set();

    for (const number of numbers) {
        if (seen.has(number)) {
            return number;
        }

        seen.add(number);
    }

    return -1;
}
```

Ejemplo:

```js
firstDuplicate([2, 1, 3, 5, 3, 2]);
// 3
```

Complejidad:

```text
Tiempo: O(n)
Espacio: O(n)
```

---

## Patrón 3 — Contar frecuencias

Pregunta:

> ¿Cuántas veces aparece cada elemento?

Piensa:

```text
Map
```

```js
function countFrequency(numbers) {
    const frequency = new Map();

    for (const number of numbers) {
        frequency.set(
            number,
            (frequency.get(number) ?? 0) + 1
        );
    }

    return frequency;
}
```

---

## Patrón 4 — Elemento único cuando los demás aparecen dos veces

Una solución usando `Set`:

```js
function findUnique(numbers) {
    const seen = new Set();

    for (const number of numbers) {
        if (seen.has(number)) {
            seen.delete(number);
        } else {
            seen.add(number);
        }
    }

    return [...seen][0];
}
```

Ejemplo:

```js
findUnique([4, 1, 2, 1, 2]);
// 4
```

---

# 15. Evitar O(n²) innecesario

Una solución como esta:

```js
for (let i = 0; i < numbers.length; i++) {
    for (let j = i + 1; j < numbers.length; j++) {
        if (numbers[i] === numbers[j]) {
            // ...
        }
    }
}
```

puede costar:

```text
O(n²)
```

Si solamente necesitas saber si un elemento apareció anteriormente, frecuentemente puedes reemplazarlo por:

```js
const seen = new Set();

for (const number of numbers) {
    if (seen.has(number)) {
        // encontrado
    }

    seen.add(number);
}
```

que normalmente será:

```text
O(n)
```

---

# 16. Resumen rápido: ¿qué estructura debo elegir?

| Si el problema dice... | Piensa en... |
|---|---|
| "recorre una lista" | `Array` |
| "posición / índice" | `Array` |
| "ya apareció" | `Set` |
| "eliminar duplicados" | `Set` |
| "cuántas veces aparece" | `Map` |
| "clave → información" | `Map` |
| "propiedades de una entidad" | `Object` |
| "último en entrar, primero en salir" | `Stack` |
| "primero en entrar, primero en salir" | `Queue` |
| "letras / palabras / texto" | `String` |

---

# 17. Chuleta rápida de sintaxis

## Array

```js
const arr = [];

arr.push(value);
arr.pop();

arr.includes(value);
arr.length;
```

## Set

```js
const set = new Set();

set.add(value);
set.has(value);
set.delete(value);
set.size;
```

## Map

```js
const map = new Map();

map.set(key, value);
map.get(key);
map.has(key);
map.delete(key);
map.size;
```

## Object

```js
const obj = {};

obj.name = "Janer";
obj["age"] = 31;
```

## Stack

```js
const stack = [];

stack.push(value);
stack.pop();
```

## Queue simple

```js
const queue = [];

queue.push(value);
queue.shift();
```

---

# 18. Big-O que debes reconocer

No necesitas dominar toda la teoría matemática al principio. Para entrevistas, empieza por estas:

| Complejidad | Interpretación |
|---|---|
| O(1) | tiempo constante |
| O(log n) | crece lentamente |
| O(n) | recorre los datos una vez |
| O(n log n) | común en algoritmos eficientes de ordenamiento |
| O(n²) | normalmente dos bucles anidados |

Ejemplos:

```js
numbers[0];
```

```text
O(1)
```

```js
for (const number of numbers) {
    console.log(number);
}
```

```text
O(n)
```

```js
for (let i = 0; i < numbers.length; i++) {
    for (let j = 0; j < numbers.length; j++) {
        // ...
    }
}
```

```text
O(n²)
```

---

# 19. Reglas mentales para Codility / entrevistas

1. Antes de escribir código, identifica qué necesitas guardar.
2. Si solamente necesitas saber si algo apareció: `Set`.
3. Si necesitas relacionar una clave con un dato: `Map`.
4. Si necesitas orden e índices: `Array`.
5. Si modelas una entidad: `Object`.
6. Revisa si tienes dos loops anidados y pregúntate si puedes usar `Set` o `Map`.
7. Piensa en casos límite:
   - array vacío;
   - un solo elemento;
   - todos iguales;
   - ningún duplicado;
   - números negativos;
   - valores `0`;
   - strings vacíos.
8. Después de resolver, calcula:
   - complejidad temporal;
   - complejidad espacial.

---

# 20. Mini preguntas de repaso

### Pregunta 1

Quieres saber si un número ya apareció anteriormente.

**Respuesta:** `Set`.

### Pregunta 2

Quieres saber cuántas veces aparece cada número.

**Respuesta:** `Map`.

### Pregunta 3

Quieres representar:

```text
nombre
edad
cargo
```

de una persona.

**Respuesta:** `Object`.

### Pregunta 4

Necesitas acceder al tercer elemento de una lista.

**Respuesta:** `Array`.

### Pregunta 5

Necesitas validar correctamente aperturas y cierres como:

```text
([]{})
```

**Respuesta:** `Stack`.

### Pregunta 6

Necesitas procesar personas en el mismo orden en que llegaron.

**Respuesta:** `Queue`.

---

# 21. Qué estudiar después

Orden recomendado:

1. Arrays y Strings.
2. Set y Map.
3. Big-O.
4. Stack y Queue.
5. Two Pointers.
6. Sliding Window.
7. Sorting.
8. Binary Search.
9. Recursion.
10. Trees y Graphs.
11. BFS y DFS.
12. Dynamic Programming.

---

## Resumen final

Las cuatro estructuras que debes dominar primero son:

```text
Array  → lista ordenada
Object → entidad con propiedades
Set    → valores únicos / "¿ya apareció?"
Map    → clave → valor / frecuencias
```

Y para algoritmos:

```text
Stack → LIFO
Queue → FIFO
```

La habilidad importante en una entrevista no es memorizar métodos: es poder leer un problema y decidir rápidamente **qué estructura de datos encaja mejor**.
