# React — Fundamentals

> Guía introductoria para estudiar React y repasar conceptos habituales en entrevistas.
>
> Objetivo: entender cómo construir interfaces con componentes, manejar datos y responder a interacciones, sin profundizar en optimizaciones avanzadas.

Los ejemplos usan componentes funcionales y JavaScript con JSX. Cada bloque es independiente; los fragmentos indicados como tales se colocan dentro de un componente. Se asumen conocimientos básicos de funciones, objetos, arrays y destructuring.

## 1. ¿Qué es React?

React es una biblioteca para construir interfaces de usuario a partir de componentes reutilizables.

Su enfoque es **declarativo**: describes cómo debería verse la interfaz según los datos actuales y React coordina sus actualizaciones.

## 2. Componentes y JSX

Un componente funcional es una función que devuelve contenido para la interfaz. Su nombre comienza con mayúscula.

JSX permite escribir marcado dentro de JavaScript; no es HTML literal. Usa `className` para clases CSS, cierra las etiquetas y coloca expresiones entre `{}`.

```jsx
function Welcome({ name }) {
    return (
        <>
            <h1 className="title">Hola, {name}</h1>
            <p>Vamos a estudiar React.</p>
        </>
    );
}

export default function App() {
    return <Welcome name="Ana" />;
}
```

`<>...</>` es un **Fragment**: agrupa elementos sin agregar un nodo extra al DOM.

## 3. Props y composición

Las **props** son los datos que un padre entrega a un hijo: textos, números, objetos o funciones. El hijo debe tratarlas como solo lectura; puede recibir nuevos valores en renders posteriores.

La prop `children` contiene el contenido anidado entre las etiquetas de un componente. Permite componer interfaces reutilizando contenedores.

```jsx
function Panel({ title = "Notas", children }) {
    return (
        <section>
            <h2>{title}</h2>
            {children}
        </section>
    );
}

export default function App() {
    return (
        <Panel title="React">
            <p>Un componente puede contener otros componentes.</p>
        </Panel>
    );
}
```

El valor por defecto se aplica si `title` falta o es `undefined`, pero no si es `null`.

Referencia: [Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component).

## 4. Estado con `useState`

El **estado** es información que un componente conserva entre renders. Su setter solicita una actualización de la interfaz.

```jsx
import { useState } from "react";

export default function Counter() {
    const [count, setCount] = useState(0);

    return (
        <button onClick={() => setCount(previous => previous + 1)}>
            Total: {count}
        </button>
    );
}
```

Una variable local normal no conserva por sí misma ese valor entre renders ni solicita actualizaciones.

| Props | State |
|---|---|
| Se reciben del padre | Se administra en el componente |
| El hijo no las modifica | Se actualiza mediante un setter |
| Configuran el componente | Representa su memoria |

## 5. Eventos

Pasa una función al evento: `onClick={handleClick}`. Escribir `onClick={handleClick()}` ejecuta la función durante el render.

Para enviar argumentos, usa `onClick={() => handleDelete(id)}`.

Referencia para las secciones introductorias: [Quick Start](https://react.dev/learn).

## 6. El estado es una instantánea

Cada render trabaja con sus propios valores de estado. Llamar al setter no cambia la variable del render que está ejecutándose.

```jsx
// Fragmento dentro de un componente con estado count.
function handleClick() {
    setCount(count + 1);
    console.log(count); // Valor del render actual, no el siguiente.
}
```

React agrupa actualizaciones, por ejemplo las de un mismo manejador de eventos. No debes asumir un render inmediato por cada setter.

```jsx
// Si count vale 0, estas llamadas en el mismo evento dejan el valor en 1.
setCount(count + 1);
setCount(count + 1);

// En cambio, estas actualizaciones suman 2 al valor pendiente.
setCount(previous => previous + 1);
setCount(previous => previous + 1);
```

Usa la forma funcional cuando el nuevo valor dependa del anterior. La función actualizadora debe ser pura.

Referencia: [Queueing a Series of State Updates](https://react.dev/learn/queueing-a-series-of-state-updates).

## 7. Inmutabilidad

Trata los objetos y arrays del estado como solo lectura. Para actualizarlos, crea nuevas versiones.

```jsx
// Fragmentos dentro de sus respectivos componentes.
const [profile, setProfile] = useState({ name: "Ana", age: 25 });
const [topics, setTopics] = useState(["JSX", "Props"]);

// Actualizar un objeto conservando sus otras propiedades.
setProfile(previous => ({ ...previous, age: previous.age + 1 }));

// Agregar y eliminar elementos sin modificar el array existente.
setTopics(previous => [...previous, "State"]);
setTopics(previous => previous.filter(topic => topic !== "JSX"));
```

Estas llamadas a setters se ejecutan desde un evento u otro lugar apropiado, no incondicionalmente durante el render. Importa `useState` desde `react`.

Evita `profile.age++` o `topics.push(...)` sobre el estado. El spread realiza una copia superficial: si cambias un objeto anidado, copia también los niveles afectados.

Referencia: [Updating Objects in State](https://react.dev/learn/updating-objects-in-state).

## 8. Renderizado condicional y listas

```jsx
export default function TopicList({ topics, isLoading }) {
    if (isLoading) return <p>Cargando...</p>;
    if (topics.length === 0) return <p>No hay temas.</p>;

    return (
        <ul>
            {topics.map(topic => (
                <li key={topic.id}>
                    {topic.name} {topic.completed ? "✓" : "Pendiente"}
                </li>
            ))}
        </ul>
    );
}
```

Una **key** identifica un elemento entre sus hermanos. Debe ser estable: usa un ID de los datos. Evita valores aleatorios y el índice cuando la lista pueda reordenarse, insertar o eliminar elementos.

`key` no llega como una prop normal; si el hijo necesita el ID, pásalo aparte.

Para mostrar algo con `&&`, usa una condición booleana: `topics.length > 0 && <p>Hay temas</p>`. Con `topics.length && ...`, una lista vacía puede mostrar `0`.

Referencia: [Rendering Lists](https://react.dev/learn/rendering-lists).

## 9. Render, commit y Virtual DOM

**Renderizar** significa que React llama a los componentes para calcular la interfaz. En el **commit**, aplica al DOM los cambios necesarios.

Un componente puede renderizarse por una actualización de su estado, porque su padre se renderiza o por un cambio del contexto que consume. Un render no implica necesariamente modificar el DOM.

**Virtual DOM** es una forma habitual de describir la representación de la interfaz en memoria. React compara la estructura anterior con la nueva mediante reconciliación; los tipos de componentes y las keys ayudan a determinar qué conservar o reemplazar.

El render debe ser puro: para las mismas entradas, calcula el mismo resultado y no modifica sistemas externos. Una petición de red o un temporizador no deben iniciarse directamente en el cuerpo del componente.

Referencia: [Render and Commit](https://react.dev/learn/render-and-commit).

## 10. Hooks y sus reglas

Los Hooks permiten usar capacidades de React desde componentes funcionales. Ejemplos: `useState`, `useEffect`, `useRef` y `useContext`.

Para estos Hooks:

- Llámalos en el nivel superior, antes de retornos condicionales.
- No los llames dentro de condiciones, bucles o manejadores de eventos.
- Llámalos desde componentes funcionales o desde otros Hooks personalizados.

React necesita que estas llamadas mantengan su orden entre renders.

Referencia: [Rules of Hooks](https://react.dev/reference/rules/rules-of-hooks).

## 11. `useEffect`: sincronizar con sistemas externos

Un efecto sirve para sincronizar el componente con algo externo, como un temporizador, una suscripción o una API del navegador.

```jsx
import { useEffect, useState } from "react";

export default function Timer() {
    const [seconds, setSeconds] = useState(0);

    useEffect(() => {
        const intervalId = setInterval(() => {
            setSeconds(previous => previous + 1);
        }, 1000);

        return () => clearInterval(intervalId);
    }, []);

    return <p>Segundos: {seconds}</p>;
}
```

La función retornada es la **limpieza**: React la ejecuta antes de volver a configurar un efecto con dependencias diferentes y al desmontar el componente.

| Dependencias | Comportamiento |
|---|---|
| Sin array | Se ejecuta después de cada commit del componente |
| `[]` | Se configura al montar; no se repite por cambios de props o estado |
| `[value]` | Se configura al montar y cuando `value` cambia |

Incluye los valores reactivos que utiliza el efecto; no omitas dependencias para forzar un comportamiento. En desarrollo, `StrictMode` puede ejecutar un ciclo adicional de configuración y limpieza para detectar errores.

No necesitas un efecto para calcular `const fullName = firstName + " " + lastName`. Calcula datos derivados durante el render. Una acción específica de un clic suele ir en su manejador.

Referencia: [Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects).

## 12. Inputs controlados y no controlados

En un input **controlado**, React administra el valor mediante estado.

```jsx
import { useState } from "react";

export default function NameField() {
    const [name, setName] = useState("");

    return (
        <label>
            Nombre
            <input
                value={name}
                onChange={event => setName(event.target.value)}
            />
        </label>
    );
}
```

En uno **no controlado**, el DOM conserva el valor actual. Puedes dar un valor inicial con `<input defaultValue="Ana" />` y leerlo mediante una ref o al procesar el formulario.

Un input de texto controlado debe mantener un valor de tipo string; evita pasar de `undefined` a un string. Para checkboxes controlados, usa `checked` y lee `event.target.checked`.

Referencia: [Input](https://react.dev/reference/react-dom/components/input).

## 13. Comunicación y lifting state up

Los datos fluyen del padre al hijo mediante props. Un hijo puede informar de una interacción llamando a una función recibida como prop.

Si dos componentes necesitan el mismo estado, súbelo a su padre común más cercano. Esto se conoce como **lifting state up**.

```jsx
import { useState } from "react";

function SearchField({ value, onValueChange }) {
    return (
        <input
            aria-label="Buscar tema"
            value={value}
            onChange={event => onValueChange(event.target.value)}
        />
    );
}

function SearchSummary({ query }) {
    return <p>Búsqueda actual: {query || "Sin filtro"}</p>;
}

export default function SearchPage() {
    const [query, setQuery] = useState("");

    return (
        <>
            <SearchField value={query} onValueChange={setQuery} />
            <SearchSummary query={query} />
        </>
    );
}
```

Ambos hijos usan una única fuente de verdad. Evita mantener copias del mismo dato que después tengas que sincronizar.

Referencia: [Sharing State Between Components](https://react.dev/learn/sharing-state-between-components).

## 14. Context y prop drilling

**Prop drilling** es pasar props por varios componentes intermedios para llegar a uno más profundo.

Context permite que los descendientes lean un valor de un proveedor sin pasarlo manualmente por cada nivel. Es útil para datos como un tema visual o el idioma.

```jsx
import { createContext, useContext } from "react";

const ThemeContext = createContext("light");

function ThemeLabel() {
    const theme = useContext(ThemeContext);
    return <p>Tema: {theme}</p>;
}

export default function App() {
    return (
        <ThemeContext.Provider value="dark">
            <ThemeLabel />
        </ThemeContext.Provider>
    );
}
```

Context distribuye un valor; el estado puede seguir administrándose con `useState`. No es necesario usarlo para todos los datos. Cuando el valor del proveedor cambia, React actualiza a los consumidores correspondientes.

Referencia: [Passing Data Deeply with Context](https://react.dev/learn/passing-data-deeply-with-context).

## 15. `useRef` vs `useState`

`useRef` conserva un valor entre renders, pero modificar `ref.current` no provoca un render. También permite acceder a nodos del DOM.

```jsx
import { useRef } from "react";

export default function FocusField() {
    const inputRef = useRef(null);

    return (
        <>
            <input ref={inputRef} aria-label="Tema" />
            <button onClick={() => inputRef.current?.focus()}>
                Enfocar
            </button>
        </>
    );
}
```

| Necesidad | Herramienta |
|---|---|
| Mostrar un contador que cambia | `useState` |
| Guardar el ID de un temporizador | `useRef` |
| Enfocar un input | Ref al nodo del DOM |

No uses una ref como sustituto del estado para datos que deben actualizar la pantalla.

Referencia: [Referencing Values with Refs](https://react.dev/learn/referencing-values-with-refs).

## 16. Custom Hooks

Un Hook personalizado extrae lógica reutilizable. Su nombre comienza con `use` seguido de mayúscula.

```jsx
import { useState } from "react";

function useToggle(initialValue = false) {
    const [enabled, setEnabled] = useState(initialValue);
    const toggle = () => setEnabled(previous => !previous);
    return [enabled, toggle];
}

export default function Details() {
    const [isOpen, toggle] = useToggle();

    return (
        <>
            <button onClick={toggle} aria-expanded={isOpen}>
                Detalles
            </button>
            {isOpen && <p>Contenido adicional.</p>}
        </>
    );
}
```

Reutiliza **lógica**, no una misma instancia de estado: dos llamadas a `useToggle` tienen estados independientes.

Referencia: [Reusing Logic with Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks).

## 17. Preguntas rápidas de repaso

| Pregunta | Respuesta breve |
|---|---|
| ¿Props y estado son lo mismo? | Las props vienen del padre; el estado es la memoria administrada por el componente. |
| ¿Un setter cambia inmediatamente la variable actual? | No, solicita una actualización para un render posterior. |
| ¿Por qué usar una función en el setter? | Para calcular a partir del estado pendiente anterior. |
| ¿Para qué sirve una key? | Para identificar elementos entre renders, especialmente si una lista cambia. |
| ¿Renderizar equivale a cambiar el DOM? | No; React puede calcular la interfaz sin necesitar cambios en el DOM. |
| ¿Para qué sirve la limpieza de un efecto? | Para deshacer la sincronización anterior, por ejemplo cancelando un temporizador. |
| ¿Cómo comunico dos componentes hermanos? | Coloco el estado compartido en su padre común y paso props y callbacks. |
| ¿Cambiar una ref actualiza la pantalla? | No; para eso normalmente se usa estado. |
| ¿Un custom Hook comparte su estado entre componentes? | No automáticamente; cada llamada tiene su propia instancia. |

## 18. Práctica sugerida

Construye una pequeña lista de temas de estudio:

1. Muestra los temas usando componentes y keys estables.
2. Agrega un input controlado para buscar por nombre.
3. Permite marcar un tema como completado sin mutar el estado.
4. Muestra el total de completados calculándolo durante el render.
5. Divide el buscador y la lista en componentes que compartan estado desde el padre.

Puedes repasar primero las [estructuras de datos de JavaScript](../javascript/data-structures.md).
