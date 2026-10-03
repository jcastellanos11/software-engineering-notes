# React — TypeScript Tips

> Consejos prácticos para escribir componentes más claros y detectar errores antes de ejecutar la aplicación.

Esta guía complementa los [fundamentos de React](fundamentals.md). Los ejemplos son independientes y asumen un proyecto React con TypeScript configurado.

## 1. Usa `.tsx` y activa la comprobación estricta

Los archivos con JSX usan `.tsx`; los tipos y utilidades sin JSX pueden usar `.ts`. Mantén la configuración JSX de tu framework y activa `strict` en el `tsconfig.json` correspondiente:

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

Es un fragmento para integrar en la configuración existente, no un reemplazo completo. Los tipos de React provienen de `@types/react` y `@types/react-dom`; mantenlos compatibles con la versión del proyecto.

Referencia: [strict](https://www.typescriptlang.org/tsconfig/strict.html).

## 2. Define el contrato de las props

```tsx
type TopicCardProps = {
    title: string;
    completed?: boolean;
    onSelect: (title: string) => void;
};

export function TopicCard({
    title,
    completed = false,
    onSelect,
}: TopicCardProps) {
    return (
        <button onClick={() => onSelect(title)}>
            {title} {completed ? "✓" : "Pendiente"}
        </button>
    );
}
```

`?` indica una prop opcional. Define los argumentos de callbacks para que el consumidor sepa qué recibirá. No necesitas `React.FC` para tipar un componente funcional.

`type` e `interface` sirven para describir props. Usa una convención consistente; `type` también permite expresar uniones directamente.

Referencia: [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html).

## 3. Aprovecha la inferencia de `useState`

```tsx
import { useState } from "react";

type Topic = { id: number; title: string };

export function TopicPicker() {
    const [query, setQuery] = useState(""); // Infiere string.
    const [topics] = useState<Topic[]>([]);
    const [selected, setSelected] = useState<Topic | null>(null);

    return (
        <>
            <input
                aria-label="Buscar tema"
                value={query}
                onChange={event => setQuery(event.currentTarget.value)}
            />
            {topics.filter(topic => topic.title.includes(query)).map(topic => (
                <button key={topic.id} onClick={() => setSelected(topic)}>
                    {topic.title}
                </button>
            ))}
            <p>{selected?.title ?? "Sin selección"}</p>
        </>
    );
}
```

Anota arrays vacíos y estados inicialmente nulos cuando el valor inicial no describe todos sus futuros valores. Este ejemplo comienza sin temas; en una aplicación se cargarían o agregarían después.

## 4. Tipa los eventos cuando extraigas el manejador

```tsx
import { useState } from "react";
import type { ChangeEvent } from "react";

export function NameField() {
    const [name, setName] = useState("");

    function handleChange(event: ChangeEvent<HTMLInputElement>) {
        setName(event.currentTarget.value);
    }

    return <input aria-label="Nombre" value={name} onChange={handleChange} />;
}
```

Dentro del JSX, TypeScript suele inferir el evento. `currentTarget` representa el elemento que tiene el manejador; `target` puede ser un descendiente. Un input numérico sigue exponiendo `value` como string: convierte y valida antes de guardarlo como número.

## 5. Declara `children` y contempla refs nulas

```tsx
import { useRef } from "react";
import type { ReactNode } from "react";

export function Panel({ children }: { children: ReactNode }) {
    return <section>{children}</section>;
}

export function FocusField() {
    const inputRef = useRef<HTMLInputElement>(null);

    return (
        <Panel>
            <input ref={inputRef} aria-label="Tema" />
            <button onClick={() => inputRef.current?.focus()}>Enfocar</button>
        </Panel>
    );
}
```

`ReactNode` acepta contenido renderizable. La ref puede ser `null` antes del montaje o después del desmontaje; compruébala en vez de forzar `inputRef.current!`.

Referencia para los ejemplos de React: [Using TypeScript](https://react.dev/learn/typescript).

## 6. Evita estados imposibles con uniones discriminadas

```tsx
type Topic = { id: number; title: string };

type TopicsState =
    | { status: "loading" }
    | { status: "error"; message: string }
    | { status: "success"; data: Topic[] };

export function TopicsResult({ state }: { state: TopicsState }) {
    switch (state.status) {
        case "loading":
            return <p>Cargando...</p>;
        case "error":
            return <p role="alert">{state.message}</p>;
        case "success":
            return <ul>{state.data.map(topic => (
                <li key={topic.id}>{topic.title}</li>
            ))}</ul>;
    }
}
```

El campo `status` determina qué otros campos existen. Así no puedes representar accidentalmente una carga exitosa sin datos. TypeScript reduce el tipo en cada rama y permite acceder a los campos adecuados.

Referencia: [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html).

## 7. Valida lo que llega de una API

Los tipos desaparecen al ejecutar JavaScript. `data as Topic` no valida un JSON ni transforma su contenido.

```ts
type Topic = { id: number; title: string };

function isTopic(value: unknown): value is Topic {
    if (typeof value !== "object" || value === null) return false;
    return "id" in value && typeof value.id === "number"
        && "title" in value && typeof value.title === "string";
}

export async function loadTopic(id: number): Promise<Topic> {
    const response = await fetch(`/api/topics/${id}`);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);

    const data: unknown = await response.json();
    if (!isTopic(data)) throw new Error("Respuesta inválida");
    return data;
}
```

Usa `unknown` para datos no verificados. El guard anterior comprueba la estructura mínima; reglas como IDs positivos necesitan comprobaciones adicionales. El código que llame a `loadTopic` debe manejar su rechazo.

Evita `any`, conversiones dobles como `as unknown as Topic` y aserciones usadas solo para silenciar errores. Un type guard también puede estar mal escrito: comprueba todos los campos que promete.

Referencias: [Type assertions](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-assertions) y [Type predicates](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates).

## 8. Separa tipos de valores y comprueba el proyecto

Usa `import type` para dependencias que solo se necesitan durante la comprobación de tipos. Deja los tipos pequeños junto al componente y comparte únicamente los contratos usados en varios módulos.

Muchos entornos transforman TypeScript sin comprobar todos sus tipos. Mantén un comando de type-check en el proyecto, por ejemplo `tsc --noEmit` cuando corresponda a su configuración; proyectos con referencias pueden necesitar `tsc -b`.

Que el código compile no garantiza que la interfaz funcione: prueba interacciones, carga, errores y datos externos.

## 9. Lista de repaso

- Props y callbacks tienen contratos concretos.
- El estado distingue valores presentes, ausentes y en carga.
- Los eventos usan el tipo del elemento correspondiente.
- Las refs nulas se manejan sin aserciones innecesarias.
- Los datos externos se validan en ejecución.
- La comprobación de tipos forma parte de la verificación del proyecto.

Como práctica, convierte una lista de temas de JavaScript a TypeScript: tipa las props, agrega búsqueda, representa carga y errores con una unión y valida la respuesta de la API.
