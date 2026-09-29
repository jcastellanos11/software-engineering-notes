# Python — Data Structures and Fundamentals for Interviews

> Guía de estudio y repaso rápido para ejercicios tipo Codility, LeetCode e entrevistas técnicas.
>
> Objetivo: entender **qué estructura usar, cuándo usarla, cómo funciona y cuál es su complejidad**.

---

# 1. List

Una `list` es una colección **ordenada y mutable** de elementos.

```python
numbers = [10, 20, 30, 40]

print(numbers[0])  # 10
print(numbers[2])  # 30
```

Los índices empiezan en `0`:

```text
index:   0   1   2   3
value:  10  20  30  40
```

## Operaciones importantes

```python
numbers = [1, 2, 3]

numbers.append(4)
# Agrega un elemento al final de la lista.

numbers.pop()
# Elimina y devuelve el último elemento de la lista.

numbers.insert(0, 0)
# Inserta un elemento en una posición específica.

numbers.pop(0)
# Elimina y devuelve el elemento ubicado en el índice indicado.

2 in numbers
# Devuelve True o False según si el valor existe dentro de la lista.

numbers.index(3)
# Devuelve el índice de la primera aparición del valor.
# Si no existe, lanza ValueError.

len(numbers)
# Devuelve la cantidad de elementos de la lista.
```

## Recorrer una lista

```python
for number in numbers:
    print(number)
```

Con índice:

```python
for i in range(len(numbers)):
    print(numbers[i])
```

Con índice y valor:

```python
for i, number in enumerate(numbers):
    print(i, number)
```

## Complejidad de las operaciones de List

| Operación | Complejidad |
|---|---:|
| Acceso por índice `lst[i]` | O(1) |
| `append()` | O(1) amortizado |
| `pop()` al final | O(1) |
| `insert(0, value)` | O(n) |
| `pop(0)` | O(n) |
| `value in lst` | O(n) |
| `index()` | O(n) |
| Recorrer toda la lista | O(n) |

## Cuándo usarla

Usa `list` cuando necesites:

- Mantener orden.
- Acceder por posición.
- Recorrer una secuencia.
- Agregar o modificar elementos.
- Permitir valores repetidos.

---

# 2. Tuple

Una `tuple` es una colección **ordenada e inmutable**.

```python
point = (10, 20)

print(point[0])  # 10
```

Esto no es válido:

```python
point[0] = 5
# TypeError
```

## Cuándo usarla

Usa `tuple` cuando:

- Los datos no deberían cambiar.
- Quieras representar coordenadas o datos fijos.
- Necesites una colección inmutable.
- Quieras usarla como clave de un `dict`, si sus elementos también son hashables.

Ejemplo:

```python
locations = {
    (4, 5): "Point A",
    (8, 2): "Point B"
}
```

## List vs Tuple

| Característica | List | Tuple |
|---|---|---|
| Ordenada | Sí | Sí |
| Mutable | Sí | No |
| Duplicados | Sí | Sí |
| Sintaxis | `[1, 2]` | `(1, 2)` |
| Uso típico | datos que cambian | datos fijos |

---

# 3. Dictionary

Un `dict` almacena pares:

```text
clave → valor
```

Ejemplo:

```python
person = {
    "name": "Janer",
    "age": 31,
    "role": "Developer"
}
```

Acceso:

```python
print(person["name"])
# Janer
```

También:

```python
print(person.get("name"))
# Janer
```

## Operaciones importantes

```python
person["age"] = 32
# Modifica el valor asociado a una clave existente.

person["country"] = "Colombia"
# Agrega una nueva clave con su valor.

person.get("name")
# Devuelve el valor de una clave.
# Si no existe, devuelve None.

person.get("city", "Unknown")
# Devuelve un valor por defecto si la clave no existe.

"name" in person
# Verifica si una clave existe.

person.keys()
# Devuelve las claves.

person.values()
# Devuelve los valores.

person.items()
# Devuelve pares (clave, valor).

person.pop("age")
# Elimina una clave y devuelve su valor.
```

## Recorrer un Dictionary

```python
for key in person:
    print(key)
```

Clave y valor:

```python
for key, value in person.items():
    print(key, value)
```

## Contar frecuencias

```python
numbers = [1, 2, 2, 3, 3, 3]

counter = {}

for number in numbers:
    counter[number] = counter.get(number, 0) + 1
```

Resultado:

```python
{
    1: 1,
    2: 2,
    3: 3
}
```

## Complejidad promedio de Dictionary

| Operación | Complejidad promedio |
|---|---:|
| Leer `dictionary[key]` | O(1) |
| Insertar / actualizar | O(1) |
| `key in dictionary` | O(1) |
| `get()` | O(1) |
| `pop()` | O(1) |

---

# 4. Set

Un `set` almacena valores **únicos**.

```python
numbers = set()

numbers.add(5)
numbers.add(10)
numbers.add(5)

print(numbers)
```

Resultado conceptual:

```python
{5, 10}
```

## Operaciones importantes

```python
seen = set()

seen.add(10)
# Agrega un valor.

10 in seen
# Verifica si el valor existe.

seen.remove(10)
# Elimina un valor.
# Lanza KeyError si no existe.

seen.discard(10)
# Elimina un valor si existe.
# No falla si no existe.

len(seen)
# Devuelve la cantidad de elementos.
```

## Complejidad promedio de las operaciones de Set

| Operación | Complejidad promedio |
|---|---:|
| `add()` | O(1) |
| `value in set` | O(1) |
| `remove()` | O(1) |
| `discard()` | O(1) |

## Idea mental

Si un problema dice:

> “¿Ya vi este elemento?”

piensa en:

```text
set
```

Ejemplo:

```python
def has_duplicate(numbers):
    seen = set()

    for number in numbers:
        if number in seen:
            return True

        seen.add(number)

    return False
```

---

# 5. Hashability

En Python, las claves de un `dict` y los valores de un `set` deben ser **hashable**.

Normalmente son hashables:

```python
int
float
str
bool
tuple
```

Normalmente no son hashables:

```python
list
dict
set
```

Ejemplo válido:

```python
data = {
    (1, 2): "coordinate"
}
```

Ejemplo inválido:

```python
data = {
    [1, 2]: "coordinate"
}
# TypeError: unhashable type: 'list'
```

Regla mental:

```text
Mutable → normalmente no hashable.
Inmutable → puede ser hashable.
```

---

# 6. Operadores útiles: `or`, `and` y expresión condicional

## `or`

`or` es el operador OR lógico de Python.

```python
left_value or right_value
```

Devuelve el valor de la izquierda si es truthy. Si es falsy, devuelve el de la derecha.

```python
name = "" or "Guest"

print(name)
# Guest
```

Valores falsy comunes:

```python
False
0
0.0
""
[]
{}
set()
None
```

---

## `and`

`and` devuelve el primer valor falsy; si todos son truthy, devuelve el último.

```python
result = True and "Hello"

print(result)
# Hello
```

---

## Expresión condicional

El equivalente conceptual al operador ternario de JavaScript es:

```python
value_if_true if condition else value_if_false
```

Ejemplo:

```python
age = 20

status = "Adult" if age >= 18 else "Minor"
```

Equivale a:

```python
if age >= 18:
    status = "Adult"
else:
    status = "Minor"
```

Regla mental:

```text
or  → valor alternativo si el primero es falsy
and → continúa si el primero es truthy
A if condition else B → elegir entre dos valores
```

---

# 7. Stack

Una `list` funciona bien como stack.

Principio:

```text
LIFO
Last In, First Out
```

```python
stack = []

stack.append("A")
stack.append("B")
stack.append("C")

print(stack.pop())
# C
```

Operaciones principales:

```python
stack.append(value)
# Agrega un elemento al final.

stack.pop()
# Elimina y devuelve el último elemento.
```

Usos comunes:

- Validar paréntesis.
- DFS.
- Undo / redo.
- Expresiones matemáticas.

---

# 8. Queue

Una queue sigue:

```text
FIFO
First In, First Out
```

Para colas eficientes se recomienda `deque`:

```python
from collections import deque

queue = deque()

queue.append("Janer")
queue.append("Aura")
queue.append("Pedro")

print(queue.popleft())
# Janer
```

Operaciones:

```python
queue.append(value)
# Agrega al final.

queue.popleft()
# Elimina y devuelve el primer elemento.
```

## ¿Por qué no usar `list.pop(0)`?

```python
list.pop(0)
```

cuesta:

```text
O(n)
```

En cambio:

```python
deque.popleft()
```

cuesta:

```text
O(1)
```

Usos comunes:

- BFS.
- Sistemas de turnos.
- Procesamiento de tareas.

---

# 9. String

Un `str` representa texto y es **inmutable**.

```python
word = "python"
```

Acceso:

```python
print(word[0])
# p
```

Recorrido:

```python
for letter in word:
    print(letter)
```

## Operaciones comunes

```python
len(word)
# Longitud.

"th" in word
# Verifica si existe un substring.

word.lower()
# Minúsculas.

word.upper()
# Mayúsculas.

word.split()
# Divide un string.

word.replace("p", "P")
# Reemplaza texto.

"".join(["a", "b", "c"])
# Une elementos en un string.
```

Los strings son inmutables:

```python
name = "Janer"

name[0] = "P"
# TypeError
```

---

# 10. List Comprehension

Permite crear listas de manera compacta.

Versión tradicional:

```python
squares = []

for number in range(5):
    squares.append(number * number)
```

Con list comprehension:

```python
squares = [
    number * number
    for number in range(5)
]
```

Resultado:

```python
[0, 1, 4, 9, 16]
```

Con condición:

```python
even_numbers = [
    number
    for number in range(10)
    if number % 2 == 0
]
```

Resultado:

```python
[0, 2, 4, 6, 8]
```

---

# 11. Set Comprehension

```python
numbers = [1, 2, 2, 3, 3]

unique_squares = {
    number * number
    for number in numbers
}
```

Resultado:

```python
{1, 4, 9}
```

---

# 12. Dictionary Comprehension

```python
numbers = [1, 2, 3]

squares = {
    number: number * number
    for number in numbers
}
```

Resultado:

```python
{
    1: 1,
    2: 4,
    3: 9
}
```

---

# 13. Tabla comparativa

| Estructura | Orden | Mutable | Duplicados | Búsqueda típica | Caso de uso |
|---|---:|---:|---:|---|---|
| `list` | Sí | Sí | Sí | O(n) | listas ordenadas |
| `tuple` | Sí | No | Sí | O(n) | datos fijos |
| `dict` | Preserva inserción | Sí | claves únicas | O(1) promedio por clave | asociaciones / frecuencias |
| `set` | No usar como orden lógico | Sí | No | O(1) promedio | existencia / duplicados |
| `deque` | Sí | Sí | Sí | extremos O(1) | queues / BFS |
| `str` | Sí | No | Sí | depende de la operación | texto |

---

# 14. List vs Set vs Dict

Regla mental:

```text
list → secuencia ordenada
set  → existencia / valores únicos
dict → clave → valor
```

Ejemplo:

```python
numbers = [10, 20, 30]
# list
```

```python
seen = {10, 20, 30}
# set
```

```python
ages = {
    "Janer": 31,
    "Aura": 25
}
# dict
```

---

# 15. Patrones importantes para entrevistas

## Detectar duplicados

```python
def has_duplicate(numbers):
    seen = set()

    for number in numbers:
        if number in seen:
            return True

        seen.add(number)

    return False
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

```python
def first_duplicate(numbers):
    seen = set()

    for number in numbers:
        if number in seen:
            return number

        seen.add(number)

    return -1
```

Ejemplo:

```python
first_duplicate([2, 1, 3, 5, 3, 2])
# 3
```

---

## Contar frecuencias

```python
def count_frequency(numbers):
    frequency = {}

    for number in numbers:
        frequency[number] = frequency.get(number, 0) + 1

    return frequency
```

---

## `collections.Counter`

Python ofrece una herramienta especializada:

```python
from collections import Counter

numbers = [1, 2, 2, 3, 3, 3]

counter = Counter(numbers)
```

Resultado:

```python
Counter({
    3: 3,
    2: 2,
    1: 1
})
```

Conviene saber usar `Counter`, pero también saber implementar el conteo manualmente.

---

## Elemento único

Si todos aparecen dos veces excepto uno:

```python
def find_unique(numbers):
    seen = set()

    for number in numbers:
        if number in seen:
            seen.remove(number)
        else:
            seen.add(number)

    return next(iter(seen))
```

---

# 16. Evitar O(n²) innecesario

Esto:

```python
for i in range(len(numbers)):
    for j in range(i + 1, len(numbers)):
        if numbers[i] == numbers[j]:
            pass
```

puede costar:

```text
O(n²)
```

Si solo necesitas saber si un elemento ya apareció:

```python
seen = set()

for number in numbers:
    if number in seen:
        pass

    seen.add(number)
```

normalmente cuesta:

```text
O(n)
```

---

# 17. Slicing

Sintaxis:

```python
sequence[start:end:step]
```

Ejemplos:

```python
numbers = [0, 1, 2, 3, 4, 5]

numbers[1:4]
# [1, 2, 3]

numbers[:3]
# [0, 1, 2]

numbers[3:]
# [3, 4, 5]

numbers[::2]
# [0, 2, 4]

numbers[::-1]
# [5, 4, 3, 2, 1, 0]
```

---

# 18. Unpacking

```python
point = (10, 20)

x, y = point
```

También:

```python
numbers = [1, 2, 3, 4]

first, *middle, last = numbers
```

Resultado conceptual:

```python
first = 1
middle = [2, 3]
last = 4
```

---

# 19. `enumerate()`

Cuando necesites índice y valor:

```python
names = ["Janer", "Aura", "Pedro"]

for index, name in enumerate(names):
    print(index, name)
```

Resultado:

```text
0 Janer
1 Aura
2 Pedro
```

---

# 20. `zip()`

Permite recorrer colecciones al mismo tiempo.

```python
names = ["Janer", "Aura"]
ages = [31, 25]

for name, age in zip(names, ages):
    print(name, age)
```

Resultado:

```text
Janer 31
Aura 25
```

---

# 21. `range()`

Genera una secuencia de números.

```python
range(5)
```

Representa:

```text
0, 1, 2, 3, 4
```

Otros ejemplos:

```python
range(1, 5)
# 1, 2, 3, 4
```

```python
range(0, 10, 2)
# 0, 2, 4, 6, 8
```

---

# 22. `None`

`None` representa ausencia de valor.

```python
result = None
```

Se recomienda comparar así:

```python
if result is None:
    print("No value")
```

Preferir:

```python
is None
```

sobre:

```python
== None
```

---

# 23. Mutable vs Immutable

## Mutables

```text
list
dict
set
```

Ejemplo:

```python
numbers = [1, 2, 3]

numbers.append(4)
```

## Inmutables

```text
int
float
bool
str
tuple
frozenset
```

Regla mental:

```text
Mutable   → el objeto puede cambiar.
Immutable → necesitas crear otro valor.
```

---

# 24. `is` vs `==`

## `==`

Compara valores:

```python
a = [1, 2]
b = [1, 2]

print(a == b)
# True
```

## `is`

Compara identidad:

```python
print(a is b)
# False
```

Misma referencia:

```python
c = a

print(a is c)
# True
```

Regla mental:

```text
== → mismo valor
is → mismo objeto
```

---

# 25. Resumen rápido: qué estructura elegir

| Si el problema dice... | Piensa en... |
|---|---|
| "lista ordenada" | `list` |
| "posición / índice" | `list` |
| "datos fijos" | `tuple` |
| "ya apareció" | `set` |
| "eliminar duplicados" | `set` |
| "cuántas veces aparece" | `dict` / `Counter` |
| "clave → información" | `dict` |
| "último en entrar, primero en salir" | `list` como stack |
| "primero en entrar, primero en salir" | `deque` |
| "texto" | `str` |

---

# 26. Chuleta rápida de sintaxis

## List

```python
lst = []

lst.append(value)
lst.pop()

value in lst
len(lst)
```

## Tuple

```python
point = (10, 20)

x = point[0]
```

## Set

```python
seen = set()

seen.add(value)
value in seen
seen.remove(value)
seen.discard(value)
len(seen)
```

## Dictionary

```python
data = {}

data[key] = value
data.get(key)
data.get(key, default)
key in data
data.pop(key)
len(data)
```

## Stack

```python
stack = []

stack.append(value)
stack.pop()
```

## Queue

```python
from collections import deque

queue = deque()

queue.append(value)
queue.popleft()
```

---

# 27. Big-O que debes reconocer

| Complejidad | Interpretación |
|---|---|
| O(1) | tiempo constante |
| O(log n) | crecimiento lento |
| O(n) | recorre los datos una vez |
| O(n log n) | común en sorting eficiente |
| O(n²) | normalmente loops anidados |

Ejemplo O(1):

```python
numbers[0]
```

Ejemplo O(n):

```python
for number in numbers:
    print(number)
```

Ejemplo O(n²):

```python
for a in numbers:
    for b in numbers:
        print(a, b)
```

---

# 28. Reglas mentales para Codility / entrevistas

1. Identifica qué información necesitas guardar.
2. Si solo necesitas saber si algo apareció: `set`.
3. Si necesitas asociar una clave con un valor: `dict`.
4. Si necesitas orden e índices: `list`.
5. Si necesitas datos fijos: `tuple`.
6. Para queues grandes: `collections.deque`.
7. Si ves loops anidados, pregúntate si un `set` o `dict` puede reducir la complejidad.
8. Para frecuencias recuerda:
   ```python
   counter[value] = counter.get(value, 0) + 1
   ```
9. Revisa casos límite:
   - lista vacía;
   - un solo elemento;
   - todos iguales;
   - ningún duplicado;
   - negativos;
   - cero;
   - strings vacíos;
   - `None`.
10. Después de resolver, calcula:
    - complejidad temporal;
    - complejidad espacial.

---

# 29. Qué estudiar después

1. Lists y Strings.
2. Dict y Set.
3. Big-O.
4. Stack y Queue.
5. Comprehensions.
6. Two Pointers.
7. Sliding Window.
8. Sorting.
9. Binary Search.
10. Recursion.
11. Trees y Graphs.
12. BFS y DFS.
13. Dynamic Programming.

---

# 30. Resumen final

Las estructuras que debes dominar primero en Python son:

```text
list  → secuencia ordenada y mutable
tuple → secuencia ordenada e inmutable
set   → valores únicos / "¿ya apareció?"
dict  → clave → valor / frecuencias
deque → queue eficiente
str   → texto inmutable
```

La habilidad importante en una entrevista no es memorizar métodos, sino poder responder rápidamente:

```text
¿Qué información necesito guardar?
¿Qué estructura de datos encaja mejor?
¿Cuál es la complejidad de mi solución?
```
