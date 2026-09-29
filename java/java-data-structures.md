# Java — Data Structures and Fundamentals for Interviews

> Guía de estudio y repaso rápido para ejercicios tipo Codility, LeetCode e entrevistas técnicas.
>
> Objetivo: entender **qué estructura usar, cuándo usarla, cómo funciona y cuál es su complejidad**.

---

# 1. Array

Un `array` en Java es una colección **ordenada y de tamaño fijo**.

```java
int[] numbers = {10, 20, 30, 40};

System.out.println(numbers[0]); // 10
System.out.println(numbers[2]); // 30
```

Los índices empiezan en `0`:

```text
index:   0   1   2   3
value:  10  20  30  40
```

## Operaciones importantes

```java
int[] numbers = {10, 20, 30};

numbers[0] = 99;
// Modifica el valor almacenado en una posición específica.

int value = numbers[1];
// Obtiene el valor almacenado en el índice indicado.

int length = numbers.length;
// Devuelve la cantidad de posiciones del array.
```

Importante:

```java
int[] numbers = new int[5];
```

crea un array de tamaño `5`.

Ese tamaño no puede cambiar después.

## Recorrer un Array

Con índice:

```java
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

Con enhanced for:

```java
for (int number : numbers) {
    System.out.println(number);
}
```

## Complejidad de las operaciones del Array

| Operación | Complejidad |
|---|---:|
| Acceso por índice `array[i]` | O(1) |
| Modificar `array[i]` | O(1) |
| Buscar un valor | O(n) |
| Recorrer todo el array | O(n) |

## Cuándo usarlo

Usa un array cuando:

- El tamaño es conocido.
- Necesitas acceso rápido por índice.
- Quieres almacenar valores del mismo tipo.
- No necesitas agregar o eliminar elementos dinámicamente.

---

# 2. ArrayList

`ArrayList` es una lista dinámica.

A diferencia de un array, su tamaño puede crecer o disminuir.

```java
import java.util.ArrayList;

ArrayList<Integer> numbers = new ArrayList<>();
```

También es común declarar usando la interfaz:

```java
List<Integer> numbers = new ArrayList<>();
```

Esto suele ser preferible porque desacopla el código de la implementación concreta.

## Operaciones importantes

```java
numbers.add(10);
// Agrega un elemento al final.

numbers.add(20);

numbers.add(0, 5);
// Inserta un elemento en una posición específica.

numbers.get(0);
// Devuelve el elemento almacenado en un índice.

numbers.set(0, 99);
// Reemplaza el valor de una posición.

numbers.remove(0);
// Elimina el elemento ubicado en el índice indicado.

numbers.contains(20);
// Devuelve true o false según si el valor existe.

numbers.size();
// Devuelve la cantidad de elementos.
```

## Recorrer un ArrayList

```java
for (int number : numbers) {
    System.out.println(number);
}
```

Con índice:

```java
for (int i = 0; i < numbers.size(); i++) {
    System.out.println(numbers.get(i));
}
```

## Complejidad de las operaciones de ArrayList

| Operación | Complejidad |
|---|---:|
| `get(index)` | O(1) |
| `set(index, value)` | O(1) |
| `add(value)` al final | O(1) amortizado |
| `add(0, value)` | O(n) |
| `remove(index)` | O(n) en general |
| `contains(value)` | O(n) |

## Array vs ArrayList

| Característica | Array | ArrayList |
|---|---|---|
| Tamaño | fijo | dinámico |
| Acceso por índice | O(1) | O(1) |
| Permite primitivos directamente | Sí | No |
| Puede crecer | No | Sí |
| Sintaxis | `int[]` | `ArrayList<Integer>` |

---

# 3. LinkedList

`LinkedList` representa una lista enlazada doble.

```java
import java.util.LinkedList;

LinkedList<Integer> numbers = new LinkedList<>();
```

También puede declararse como:

```java
List<Integer> numbers = new LinkedList<>();
```

## Operaciones importantes

```java
numbers.add(10);
// Agrega un elemento.

numbers.addFirst(5);
// Agrega un elemento al inicio.

numbers.addLast(20);
// Agrega un elemento al final.

numbers.removeFirst();
// Elimina y devuelve el primer elemento.

numbers.removeLast();
// Elimina y devuelve el último elemento.

numbers.getFirst();
// Obtiene el primer elemento.

numbers.getLast();
// Obtiene el último elemento.
```

## Complejidad aproximada

| Operación | Complejidad |
|---|---:|
| `addFirst()` | O(1) |
| `addLast()` | O(1) |
| `removeFirst()` | O(1) |
| `removeLast()` | O(1) |
| `get(index)` | O(n) |
| Buscar valor | O(n) |

## Idea mental

```text
ArrayList  → acceso rápido por índice.

LinkedList → operaciones rápidas en los extremos.
```

Para entrevistas, `ArrayList` suele ser más común salvo que necesites explícitamente una estructura enlazada.

---

# 4. HashSet

`HashSet` almacena valores **únicos**.

```java
import java.util.HashSet;

HashSet<Integer> seen = new HashSet<>();
```

También:

```java
Set<Integer> seen = new HashSet<>();
```

## Operaciones importantes

```java
seen.add(10);
// Agrega un valor.

seen.contains(10);
// Verifica si el valor existe.

seen.remove(10);
// Elimina un valor.

seen.size();
// Devuelve la cantidad de elementos.

seen.isEmpty();
// Devuelve true si está vacío.
```

Los duplicados no se agregan:

```java
seen.add(5);
seen.add(5);
seen.add(5);

System.out.println(seen.size());
// 1
```

## Complejidad promedio de HashSet

| Operación | Complejidad promedio |
|---|---:|
| `add()` | O(1) |
| `contains()` | O(1) |
| `remove()` | O(1) |

## Idea mental

Si el problema dice:

> “¿Ya apareció este elemento?”

piensa en:

```text
HashSet
```

Ejemplo:

```java
public static boolean hasDuplicate(int[] numbers) {
    Set<Integer> seen = new HashSet<>();

    for (int number : numbers) {
        if (seen.contains(number)) {
            return true;
        }

        seen.add(number);
    }

    return false;
}
```

---

# 5. HashMap

`HashMap` almacena pares:

```text
clave → valor
```

Ejemplo:

```java
import java.util.HashMap;

HashMap<String, Integer> ages = new HashMap<>();
```

También:

```java
Map<String, Integer> ages = new HashMap<>();
```

## Operaciones importantes

```java
ages.put("Janer", 31);
// Agrega una nueva clave o actualiza su valor.

ages.get("Janer");
// Devuelve el valor asociado a una clave.

ages.getOrDefault("Aura", 0);
// Devuelve el valor si existe; de lo contrario, usa el valor por defecto.

ages.containsKey("Janer");
// Verifica si existe una clave.

ages.containsValue(31);
// Verifica si existe un valor.

ages.remove("Janer");
// Elimina una clave.

ages.size();
// Devuelve la cantidad de pares almacenados.
```

## Recorrer un HashMap

Solo claves:

```java
for (String key : ages.keySet()) {
    System.out.println(key);
}
```

Claves y valores:

```java
for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
}
```

## Contar frecuencias

```java
int[] numbers = {1, 2, 2, 3, 3, 3};

Map<Integer, Integer> counter = new HashMap<>();

for (int number : numbers) {
    counter.put(
        number,
        counter.getOrDefault(number, 0) + 1
    );
}
```

Resultado conceptual:

```text
1 → 1
2 → 2
3 → 3
```

## Complejidad promedio de HashMap

| Operación | Complejidad promedio |
|---|---:|
| `put()` | O(1) |
| `get()` | O(1) |
| `containsKey()` | O(1) |
| `remove()` | O(1) |

---

# 6. `getOrDefault()`

Este patrón aparece mucho en entrevistas:

```java
counter.getOrDefault(key, 0)
```

Significa:

> Obtén el valor asociado a la clave. Si no existe, devuelve `0`.

Ejemplo:

```java
Map<String, Integer> counter = new HashMap<>();

int value = counter.getOrDefault("Java", 0);

System.out.println(value);
// 0
```

Se usa mucho para frecuencias:

```java
counter.put(
    word,
    counter.getOrDefault(word, 0) + 1
);
```

---

# 7. HashSet vs HashMap

## HashSet

Pregunta:

> ¿Este valor existe?

```java
Set<Integer> seen = new HashSet<>();
```

Piensa:

```text
HashSet
```

## HashMap

Pregunta:

> ¿Qué información está asociada a esta clave?

```java
Map<String, Integer> ages = new HashMap<>();
```

Piensa:

```text
HashMap
```

Regla mental:

```text
HashSet → valores únicos.

HashMap → clave + valor.
```

---

# 8. Stack

En Java moderno, suele preferirse `Deque` sobre la clase antigua `Stack`.

Principio:

```text
LIFO
Last In, First Out
```

Ejemplo:

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.pop());
// 30
```

## Operaciones principales

```java
stack.push(value);
// Agrega un elemento arriba del stack.

stack.pop();
// Elimina y devuelve el elemento superior.

stack.peek();
// Devuelve el elemento superior sin eliminarlo.

stack.isEmpty();
// Verifica si está vacío.
```

## Usos comunes

- Validación de paréntesis.
- DFS.
- Undo / redo.
- Expresiones matemáticas.

---

# 9. Queue

Una cola sigue:

```text
FIFO
First In, First Out
```

Una implementación común:

```java
import java.util.ArrayDeque;
import java.util.Queue;

Queue<Integer> queue = new ArrayDeque<>();
```

## Operaciones importantes

```java
queue.offer(10);
// Agrega un elemento al final.

queue.offer(20);

queue.poll();
// Elimina y devuelve el primer elemento.
// Devuelve null si está vacía.

queue.peek();
// Devuelve el primer elemento sin eliminarlo.

queue.isEmpty();
// Verifica si está vacía.
```

## `offer()` vs `add()`

Ambos agregan elementos.

En estructuras con capacidad limitada:

```text
add()   → puede lanzar una excepción.
offer() → devuelve false si no puede agregar.
```

Para queues, `offer()` suele ser una opción clara.

## Usos comunes

- BFS.
- Sistemas de turnos.
- Procesamiento de tareas.
- Recorridos por niveles.

---

# 10. Deque

`Deque` significa:

```text
Double Ended Queue
```

Permite agregar y eliminar elementos por ambos extremos.

```java
Deque<Integer> deque = new ArrayDeque<>();
```

## Operaciones importantes

```java
deque.addFirst(10);
// Agrega al inicio.

deque.addLast(20);
// Agrega al final.

deque.removeFirst();
// Elimina el primero.

deque.removeLast();
// Elimina el último.

deque.peekFirst();
// Consulta el primero.

deque.peekLast();
// Consulta el último.
```

Puede servir tanto como:

```text
Stack
Queue
Deque
```

---

# 11. String

`String` representa texto y es **inmutable**.

```java
String word = "java";
```

Acceso:

```java
char first = word.charAt(0);

System.out.println(first);
// j
```

## Operaciones importantes

```java
word.length();
// Devuelve la cantidad de caracteres.

word.charAt(0);
// Devuelve el carácter de una posición.

word.contains("av");
// Verifica si contiene un substring.

word.toLowerCase();
// Convierte a minúsculas.

word.toUpperCase();
// Convierte a mayúsculas.

word.substring(1, 3);
// Obtiene una parte del string.

word.equals("java");
// Compara el contenido de dos strings.
```

## Muy importante: `equals()` vs `==`

Para comparar contenido:

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a.equals(b));
// true
```

Con:

```java
System.out.println(a == b);
// false
```

`==` compara referencias para objetos.

`equals()` compara contenido si la clase implementa correctamente ese método.

Regla mental:

```text
==       → identidad / referencia para objetos.

equals() → igualdad lógica / contenido.
```

---

# 12. StringBuilder

Como `String` es inmutable, concatenar muchas veces puede ser costoso.

Para construir strings dinámicamente, usa:

```java
StringBuilder builder = new StringBuilder();
```

Ejemplo:

```java
builder.append("Java");
builder.append(" ");
builder.append("Developer");

String result = builder.toString();
```

## Operaciones importantes

```java
builder.append("text");
// Agrega contenido al final.

builder.insert(0, "Hello ");
// Inserta contenido en una posición.

builder.deleteCharAt(0);
// Elimina un carácter.

builder.reverse();
// Invierte el contenido.

builder.toString();
// Convierte el resultado a String.
```

Muy útil en problemas de strings.

---

# 13. Primitive Types

Java distingue entre tipos primitivos y objetos.

Principales tipos primitivos:

```java
byte
short
int
long
float
double
char
boolean
```

Ejemplos:

```java
int age = 31;

double price = 10.5;

char letter = 'A';

boolean active = true;
```

Los primitivos almacenan directamente su valor.

---

# 14. Wrapper Classes

Las colecciones de Java trabajan con objetos, no directamente con tipos primitivos.

Por eso existen wrappers:

| Primitivo | Wrapper |
|---|---|
| `int` | `Integer` |
| `long` | `Long` |
| `double` | `Double` |
| `float` | `Float` |
| `char` | `Character` |
| `boolean` | `Boolean` |

Ejemplo:

```java
List<Integer> numbers = new ArrayList<>();
```

No:

```java
List<int> numbers;
```

Esto no es válido.

---

# 15. Autoboxing y Unboxing

Java convierte automáticamente entre primitivos y wrappers en muchos casos.

## Autoboxing

```java
Integer number = 10;
```

Conceptualmente:

```text
int → Integer
```

## Unboxing

```java
Integer number = 10;

int value = number;
```

Conceptualmente:

```text
Integer → int
```

---

# 16. Mutable vs Immutable

## Mutables

Ejemplos:

```text
ArrayList
HashMap
HashSet
StringBuilder
arrays
```

Pueden cambiar después de ser creados.

## Inmutables

Ejemplos frecuentes:

```text
String
Integer
Long
Double
Boolean
```

Ejemplo:

```java
String name = "Janer";

name.toUpperCase();
```

Esto no cambia `name`.

Debes reasignar:

```java
name = name.toUpperCase();
```

---

# 17. Referencias de objetos

Las variables de objetos almacenan referencias.

Ejemplo:

```java
List<Integer> a = new ArrayList<>();

a.add(10);

List<Integer> b = a;

b.add(20);

System.out.println(a);
// [10, 20]
```

`a` y `b` apuntan al mismo objeto.

Conceptualmente:

```text
a ──┐
    ├──> ArrayList [10, 20]
b ──┘
```

---

# 18. `==` vs `.equals()`

Este concepto es muy importante en Java.

## Primitivos

Con primitivos:

```java
int a = 10;
int b = 10;

System.out.println(a == b);
// true
```

`==` compara valores.

## Objetos

Con objetos:

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);
// false

System.out.println(a.equals(b));
// true
```

Regla mental:

```text
Primitivos:
== → compara valor.

Objetos:
== → compara referencia.
equals() → compara igualdad lógica.
```

---

# 19. Arrays Utility Class

Java tiene:

```java
java.util.Arrays
```

Métodos útiles:

```java
import java.util.Arrays;

int[] numbers = {4, 2, 8, 1};

Arrays.sort(numbers);
// Ordena el array.

Arrays.toString(numbers);
// Convierte el array a una representación legible.

Arrays.equals(a, b);
// Compara contenido de dos arrays.

Arrays.fill(numbers, 0);
// Llena el array con un valor.
```

También:

```java
int index = Arrays.binarySearch(numbers, 8);
```

Importante:

```text
binarySearch requiere que el array esté ordenado.
```

---

# 20. Collections Utility Class

Para colecciones existe:

```java
java.util.Collections
```

Ejemplo:

```java
List<Integer> numbers = new ArrayList<>();

numbers.add(4);
numbers.add(2);
numbers.add(8);

Collections.sort(numbers);
```

Otros métodos:

```java
Collections.reverse(numbers);
// Invierte el orden.

Collections.max(numbers);
// Obtiene el máximo.

Collections.min(numbers);
// Obtiene el mínimo.

Collections.frequency(numbers, 2);
// Cuenta cuántas veces aparece un valor.
```

---

# 21. Tabla comparativa

| Estructura | Orden | Duplicados | Acceso típico | Caso de uso |
|---|---:|---:|---|---|
| `Array` | Sí | Sí | índice O(1) | tamaño fijo |
| `ArrayList` | Sí | Sí | índice O(1) | lista dinámica |
| `LinkedList` | Sí | Sí | índice O(n) | extremos / lista enlazada |
| `HashSet` | Sin orden lógico garantizado | No | búsqueda O(1) promedio | existencia / duplicados |
| `HashMap` | Sin orden lógico garantizado | claves únicas | clave O(1) promedio | asociaciones / frecuencias |
| `ArrayDeque` | Sí | Sí | extremos O(1) | stack / queue / deque |
| `String` | Sí | Sí | `charAt()` O(1) | texto |
| `StringBuilder` | Sí | Sí | mutable | construcción de texto |

---

# 22. Patrones importantes para entrevistas

## Detectar duplicados

```java
public static boolean hasDuplicate(int[] numbers) {
    Set<Integer> seen = new HashSet<>();

    for (int number : numbers) {
        if (seen.contains(number)) {
            return true;
        }

        seen.add(number);
    }

    return false;
}
```

Tiempo:

```text
O(n)
```

Espacio:

```text
O(n)
```

---

## Primer duplicado

```java
public static int firstDuplicate(int[] numbers) {
    Set<Integer> seen = new HashSet<>();

    for (int number : numbers) {
        if (seen.contains(number)) {
            return number;
        }

        seen.add(number);
    }

    return -1;
}
```

Ejemplo:

```java
firstDuplicate(new int[]{2, 1, 3, 5, 3, 2});
// 3
```

---

## Contar frecuencias

```java
public static Map<Integer, Integer> countFrequency(int[] numbers) {
    Map<Integer, Integer> frequency = new HashMap<>();

    for (int number : numbers) {
        frequency.put(
            number,
            frequency.getOrDefault(number, 0) + 1
        );
    }

    return frequency;
}
```

---

## Elemento único

```java
public static int findUnique(int[] numbers) {
    Set<Integer> seen = new HashSet<>();

    for (int number : numbers) {
        if (seen.contains(number)) {
            seen.remove(number);
        } else {
            seen.add(number);
        }
    }

    return seen.iterator().next();
}
```

---

# 23. Evitar O(n²) innecesario

Esto:

```java
for (int i = 0; i < numbers.length; i++) {
    for (int j = i + 1; j < numbers.length; j++) {
        if (numbers[i] == numbers[j]) {
            // ...
        }
    }
}
```

puede costar:

```text
O(n²)
```

Si solo quieres saber si un valor apareció anteriormente:

```java
Set<Integer> seen = new HashSet<>();

for (int number : numbers) {
    if (seen.contains(number)) {
        // encontrado
    }

    seen.add(number);
}
```

normalmente cuesta:

```text
O(n)
```

---

# 24. Sorting

## Array

```java
Arrays.sort(numbers);
```

Complejidad típica para arrays de primitivos:

```text
O(n log n)
```

## List

```java
Collections.sort(numbers);
```

También:

```java
numbers.sort(null);
```

## Orden descendente

```java
numbers.sort(Collections.reverseOrder());
```

Esto funciona con objetos como `Integer`, no con `int[]` directamente.

---

# 25. Comparator

Para definir orden personalizado:

```java
List<String> names = new ArrayList<>();

names.add("John");
names.add("Alexander");
names.add("Ana");

names.sort(
    (a, b) -> Integer.compare(a.length(), b.length())
);
```

Esto ordena por longitud.

---

# 26. Lambda Expressions

Una lambda es una función compacta.

```java
(a, b) -> a + b
```

Ejemplo con sorting:

```java
numbers.sort((a, b) -> b - a);
```

Pero para evitar problemas de overflow, es mejor:

```java
numbers.sort((a, b) -> Integer.compare(b, a));
```

---

# 27. For-each

Forma recomendada cuando no necesitas el índice:

```java
for (int number : numbers) {
    System.out.println(number);
}
```

Si necesitas índice:

```java
for (int i = 0; i < numbers.length; i++) {
    System.out.println(i);
    System.out.println(numbers[i]);
}
```

---

# 28. `null`

`null` representa ausencia de referencia.

```java
String name = null;
```

Comprobación:

```java
if (name == null) {
    System.out.println("No value");
}
```

Cuidado:

```java
name.length();
```

si `name` es `null`, produce:

```text
NullPointerException
```

---

# 29. Ternary Operator

Java tiene operador ternario:

```java
condition ? valueIfTrue : valueIfFalse
```

Ejemplo:

```java
int age = 20;

String status = age >= 18
    ? "Adult"
    : "Minor";
```

Equivale a:

```java
String status;

if (age >= 18) {
    status = "Adult";
} else {
    status = "Minor";
}
```

---

# 30. Logical Operators

## AND

```java
&&
```

Ejemplo:

```java
if (age >= 18 && active) {
    // ...
}
```

## OR

```java
||
```

Ejemplo:

```java
if (role.equals("ADMIN") || role.equals("OWNER")) {
    // ...
}
```

## NOT

```java
!
```

Ejemplo:

```java
if (!active) {
    // ...
}
```

---

# 31. Resumen rápido: qué estructura elegir

| Si el problema dice... | Piensa en... |
|---|---|
| "tamaño fijo" | `Array` |
| "lista dinámica" | `ArrayList` |
| "acceso por índice" | `Array` / `ArrayList` |
| "ya apareció" | `HashSet` |
| "eliminar duplicados" | `HashSet` |
| "cuántas veces aparece" | `HashMap` |
| "clave → información" | `HashMap` |
| "último en entrar, primero en salir" | `Deque` como stack |
| "primero en entrar, primero en salir" | `Queue` / `ArrayDeque` |
| "texto" | `String` |
| "construir texto eficientemente" | `StringBuilder` |

---

# 32. Chuleta rápida de sintaxis

## Array

```java
int[] numbers = {1, 2, 3};

numbers[0];
numbers.length;
```

## ArrayList

```java
List<Integer> numbers = new ArrayList<>();

numbers.add(value);
numbers.get(index);
numbers.set(index, value);
numbers.remove(index);
numbers.contains(value);
numbers.size();
```

## HashSet

```java
Set<Integer> seen = new HashSet<>();

seen.add(value);
seen.contains(value);
seen.remove(value);
seen.size();
```

## HashMap

```java
Map<String, Integer> map = new HashMap<>();

map.put(key, value);
map.get(key);
map.getOrDefault(key, defaultValue);
map.containsKey(key);
map.remove(key);
map.size();
```

## Stack

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(value);
stack.pop();
stack.peek();
```

## Queue

```java
Queue<Integer> queue = new ArrayDeque<>();

queue.offer(value);
queue.poll();
queue.peek();
```

---

# 33. Big-O que debes reconocer

| Complejidad | Interpretación |
|---|---|
| O(1) | tiempo constante |
| O(log n) | crecimiento lento |
| O(n) | recorrido lineal |
| O(n log n) | sorting eficiente |
| O(n²) | normalmente loops anidados |

Ejemplo O(1):

```java
numbers[0];
```

Ejemplo O(n):

```java
for (int number : numbers) {
    System.out.println(number);
}
```

Ejemplo O(n²):

```java
for (int a : numbers) {
    for (int b : numbers) {
        System.out.println(a + " " + b);
    }
}
```

---

# 34. Reglas mentales para Codility / entrevistas

1. Identifica qué información necesitas guardar.
2. Si el tamaño es fijo y necesitas índices: `Array`.
3. Si necesitas una lista dinámica: `ArrayList`.
4. Si solo necesitas saber si un valor apareció: `HashSet`.
5. Si necesitas asociar una clave con un valor: `HashMap`.
6. Para stacks y queues, piensa en `ArrayDeque`.
7. Para frecuencias recuerda:
   ```java
   map.put(
       value,
       map.getOrDefault(value, 0) + 1
   );
   ```
8. Con Strings recuerda usar `.equals()` para comparar contenido.
9. Si ves dos loops anidados, pregúntate si `HashSet` o `HashMap` pueden mejorar la solución.
10. Revisa casos límite:
    - array vacío;
    - un solo elemento;
    - todos iguales;
    - ningún duplicado;
    - negativos;
    - cero;
    - strings vacíos;
    - `null`.
11. Después de resolver, calcula:
    - complejidad temporal;
    - complejidad espacial.

---

# 35. Qué estudiar después

1. Arrays y Strings.
2. ArrayList, HashSet y HashMap.
3. Big-O.
4. Stack, Queue y Deque.
5. Sorting y Comparator.
6. Two Pointers.
7. Sliding Window.
8. Binary Search.
9. Recursion.
10. Linked Lists.
11. Trees.
12. Graphs.
13. BFS y DFS.
14. PriorityQueue / Heap.
15. Dynamic Programming.

---

# 36. Resumen final

Las estructuras que debes dominar primero en Java son:

```text
Array       → secuencia de tamaño fijo
ArrayList   → lista dinámica
HashSet     → valores únicos / "¿ya apareció?"
HashMap     → clave → valor / frecuencias
ArrayDeque  → stack / queue eficiente
String      → texto inmutable
StringBuilder → construcción eficiente de texto
```

La habilidad importante en una entrevista no es memorizar APIs completas, sino poder responder rápidamente:

```text
¿Qué información necesito guardar?
¿Qué estructura de datos encaja mejor?
¿Cuál es la complejidad de mi solución?
```
