# Node.js — Fundamentals

> Guía introductoria para estudiar Node.js y repasar conceptos habituales en entrevistas.
>
> Objetivo: entender cómo ejecutar JavaScript fuera del navegador, trabajar con operaciones asíncronas y construir un servidor HTTP sencillo.

Los ejemplos usan JavaScript y módulos ES (ESM), salvo donde se indica CommonJS. Puedes guardarlos como archivos `.mjs` y ejecutarlos con `node archivo.mjs`. Son ejemplos independientes para practicar, no una aplicación instalada en este repositorio.

## 1. ¿Qué es Node.js?

Node.js es un **entorno de ejecución de JavaScript** basado en el motor V8. Permite crear servidores, herramientas de terminal y scripts de automatización.

JavaScript es el lenguaje; Node.js proporciona el entorno y APIs para archivos, redes y procesos. Un framework web agrega otras abstracciones sobre ese entorno.

Node.js resulta útil cuando una aplicación pasa mucho tiempo esperando operaciones de entrada/salida (**I/O**), como consultar una base de datos o recibir datos de una red.

Referencia: [Introduction to Node.js](https://nodejs.org/en/learn/getting-started/introduction-to-nodejs).

## 2. Node.js vs navegador

Ambos ejecutan JavaScript, pero ofrecen APIs distintas.

| Necesidad | Navegador | Node.js |
|---|---|---|
| Manipular una página | DOM, `document` | No incluye un DOM por defecto |
| Leer archivos del servidor | No tiene acceso directo general | `node:fs` |
| Crear un servidor HTTP | No mediante una API equivalente | `node:http` |
| Consultar configuración del proceso | No dispone de `process.env` de Node | `process.env` |
| Hacer una petición HTTP | `fetch` | `fetch` en versiones modernas |

No asumas que todo código del navegador funciona en Node.js. Compartir sintaxis no significa compartir todas las APIs.

Referencia: [Global objects](https://nodejs.org/api/globals.html).

## 3. npm y `package.json`

**npm** es un gestor de paquetes. Permite instalar dependencias y ejecutar scripts definidos en `package.json`.

En una carpeta de práctica, `npm init -y` crea un manifiesto inicial. Un ejemplo mínimo sería:

```json
{
  "name": "node-study",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "node server.js",
    "test": "node --test"
  }
}
```

`npm start` ejecuta el script `start`; `npm test` ejecuta `test`. Para otros nombres se usa `npm run nombre`.

| Campo | Propósito |
|---|---|
| `dependencies` | Paquetes necesarios para el funcionamiento de la aplicación |
| `devDependencies` | Herramientas de desarrollo, como linters |
| `scripts` | Comandos del proyecto |
| `type` | Permite declarar cómo interpretar archivos `.js` |
| `private` | Con `true`, evita publicar accidentalmente el paquete con npm |

`npm install paquete` agrega una dependencia; `npm install -D paquete` la agrega como dependencia de desarrollo. Los módulos incorporados, como `node:fs`, no requieren instalación.

Referencia: [package.json](https://docs.npmjs.com/cli/v11/configuring-npm/package-json/).

## 4. `package-lock.json` y `node_modules`

`package.json` expresa las dependencias y sus rangos de versiones. `package-lock.json` registra las versiones resueltas del árbol de dependencias para reproducir instalaciones.

`node_modules` contiene los paquetes instalados; normalmente no se guarda en Git. En una aplicación, sí se guardan el manifiesto y el lockfile.

| Comando | Uso |
|---|---|
| `npm install` | Instalar dependencias y actualizar el lockfile cuando corresponda |
| `npm ci` | Instalar a partir del lockfile, normalmente en integración continua |

`npm ci` requiere un lockfile compatible con el manifiesto, elimina un `node_modules` existente y falla si ambos archivos no coinciden, en lugar de corregirlos.

Referencia: [npm ci](https://docs.npmjs.com/cli/v11/commands/npm-ci/).

## 5. CommonJS vs ES Modules

Node.js admite dos sistemas de módulos:

| Sistema | Importar | Exportar |
|---|---|---|
| CommonJS | `require()` | `module.exports` |
| ES Modules | `import` | `export` |

Ejemplo ESM en dos archivos:

```js
// math.mjs
export function add(a, b) {
    return a + b;
}
```

```js
// app.mjs
import { add } from "./math.mjs";
console.log(add(2, 3)); // 5
```

Equivalente CommonJS:

```js
// math.cjs
function add(a, b) {
    return a + b;
}
module.exports = { add };
```

```js
// app.cjs
const { add } = require("./math.cjs");
console.log(add(2, 3)); // 5
```

`.mjs` identifica ESM y `.cjs` identifica CommonJS. Con `"type": "module"`, los `.js` del paquete se interpretan como ESM. Incluye la extensión en imports relativos ESM y mantén un sistema coherente en tus ejemplos.

Referencia: [ECMAScript modules](https://nodejs.org/api/esm.html).

## 6. Event loop y código no bloqueante

El **event loop** coordina la ejecución de callbacks asociados a operaciones asíncronas. En un hilo de JavaScript se ejecuta una tarea a la vez, pero Node puede tener varias operaciones de I/O pendientes.

Node utiliza el sistema operativo y, para ciertas operaciones, un pool de hilos de libuv. Por eso, decir que todo Node.js funciona con un único hilo es una simplificación.

```js
console.log("Inicio");

setTimeout(() => {
    console.log("Temporizador");
}, 0);

console.log("Fin");

// Inicio
// Fin
// Temporizador
```

Un timeout de `0` no ejecuta el callback inmediatamente ni garantiza un tiempo exacto. Debe terminar el código actual y llegar la oportunidad de procesarlo.

Un bucle de cálculo muy largo bloquea ese hilo y retrasa otras peticiones. Declarar la función como `async` no elimina ese bloqueo.

Referencia: [The Node.js Event Loop](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick).

## 7. Callbacks, Promises y `async` / `await`

Un **callback** es una función que se entrega a otra para que la invoque. Muchas APIs tradicionales de Node usan el primer argumento para el error:

```js
import { readFile } from "node:fs";

readFile("./notes.txt", "utf8", (error, content) => {
    if (error) {
        console.error("No se pudo leer:", error.message);
        return;
    }
    console.log(content);
});
```

Una **Promise** representa un resultado futuro: puede estar pendiente, cumplida o rechazada. `async` hace que una función devuelva una Promise; `await` suspende esa función hasta conocer el resultado, sin detener por sí mismo todo el proceso.

```js
import { readFile } from "node:fs/promises";

async function showNotes() {
    try {
        const content = await readFile("./notes.txt", "utf8");
        console.log(content);
    } catch (error) {
        console.error("No se pudo leer:", error.message);
    }
}

await showNotes();
```

El `await` fuera de una función es válido aquí porque el archivo es ESM. Crea `notes.txt` para probar el caso exitoso.

Referencias: [File system](https://nodejs.org/api/fs.html) y [Promises](https://nodejs.org/en/learn/asynchronous-work/discover-promises-in-nodejs).

## 8. Secuencial vs concurrente

Si una operación necesita el resultado de otra, ejecútalas en secuencia. Si son independientes, pueden iniciarse juntas.

```js
import { readFile } from "node:fs/promises";

try {
    const [notes, tasks] = await Promise.all([
        readFile("./notes.txt", "utf8"),
        readFile("./tasks.txt", "utf8"),
    ]);
    console.log({ notes, tasks });
} catch (error) {
    console.error("Falló alguna lectura:", error.message);
}
```

`Promise.all` conserva el orden de los resultados y rechaza si alguna operación rechaza. No cancela automáticamente las demás.

`Promise.allSettled` espera todos los resultados, incluyendo fallos. La concurrencia no implica que el JavaScript de esas funciones se ejecute en paralelo en distintos núcleos.

Referencia: [Promises in Node.js](https://nodejs.org/en/learn/asynchronous-work/discover-promises-in-nodejs).

## 9. Manejo de errores

| Situación | Manejo habitual |
|---|---|
| Una función síncrona lanza un error | `try` / `catch` |
| Una Promise rechaza | `await` dentro de `try` / `catch`, o `.catch()` |
| API con callback de error | Revisar su argumento `error` |
| Un EventEmitter emite `error` | Registrar un listener para ese evento |

Un `try` alrededor de `setTimeout(...)` no captura un error que se lance después dentro de su callback; el manejo debe estar en ese callback o usar una abstracción que convierta el fallo en rechazo.

No ocultes fallos con un `catch` vacío. Decide si corresponde informar, propagar, reintentar o terminar la operación.

Referencia: [Errors](https://nodejs.org/api/errors.html).

## 10. Archivos, rutas y Buffer

`readFile` carga el archivo completo en memoria. Con `"utf8"` devuelve texto; sin una codificación devuelve un **Buffer**, que representa bytes.

```js
import { readFile } from "node:fs/promises";

try {
    const file = new URL("./notes.txt", import.meta.url);
    const content = await readFile(file, "utf8");
    console.log(content);
} catch (error) {
    console.error(error.message);
}
```

Aquí la ruta es relativa al módulo. En cambio, `readFile("./notes.txt")` usa el directorio de trabajo actual, que puede ser distinto.

Las variantes como `readFileSync` bloquean el hilo mientras terminan. Pueden ser útiles en scripts pequeños, pero conviene evitar ese bloqueo al atender peticiones.

Referencia: [File system](https://nodejs.org/api/fs.html).

## 11. Servidor HTTP básico

Guarda este ejemplo como `server.mjs` y ejecútalo con `node server.mjs`:

```js
import { createServer } from "node:http";

const server = createServer((request, response) => {
    if (request.method === "GET" && request.url === "/topics") {
        response.writeHead(200, { "Content-Type": "application/json" });
        response.end(JSON.stringify([{ id: 1, title: "Node.js" }]));
        return;
    }

    response.writeHead(404, { "Content-Type": "application/json" });
    response.end(JSON.stringify({ error: "Ruta no encontrada" }));
});

server.on("error", error => {
    console.error("Error del servidor:", error.message);
    process.exitCode = 1;
});

server.listen(3000, "127.0.0.1", () => {
    console.log("Servidor: http://127.0.0.1:3000/topics");
});
```

`request` representa la petición; `response` permite construir la respuesta. `end()` la finaliza. El `return` evita continuar hacia la respuesta 404.

Este ejemplo compara la URL exactamente: `/topics?limit=5` no coincide. Para separar ruta y query string, puedes usar `new URL(request.url, "http://localhost")` y leer `pathname` y `searchParams`.

`node:http` no convierte automáticamente el cuerpo recibido en un objeto JSON. Para un POST hay que leerlo, limitar su tamaño, parsearlo y validar su contenido.

Referencia: [HTTP](https://nodejs.org/api/http.html).

## 12. Variables de entorno y `process`

`process` contiene información y controles del proceso actual. `process.env` permite consultar variables de entorno; sus valores presentes son strings.

```js
const port = Number(process.env.PORT ?? 3000);

if (!Number.isInteger(port) || port < 1 || port > 65535) {
    throw new Error("PORT debe ser un puerto válido");
}

console.log("Puerto configurado:", port);
console.log("Directorio de trabajo:", process.cwd());
```

En una terminal compatible con bash o zsh: `PORT=4000 node config.mjs`.

No guardes contraseñas o tokens en el código ni los imprimas en logs. Un archivo `.env` no se carga solo por existir: debe cargarse explícitamente con una opción o herramienta compatible con tu versión.

Referencia: [Process](https://nodejs.org/api/process.html).

## 13. EventEmitter

`EventEmitter` permite registrar funciones y emitir eventos por nombre.

```js
import { EventEmitter } from "node:events";

const events = new EventEmitter();

events.on("topicCreated", topic => {
    console.log("Nuevo tema:", topic.title);
});

events.emit("topicCreated", { title: "Streams" });
```

`on` registra un listener; `once` lo registra para una sola ejecución. Los listeners se llaman **sincrónicamente** cuando se emite el evento: emitir un evento no vuelve asíncrono el trabajo automáticamente.

Un evento `error` emitido sin listener puede terminar el proceso. Libera listeners que ya no necesites para evitar acumulaciones.

Referencia: [Events](https://nodejs.org/api/events.html).

## 14. Streams y backpressure

Un **stream** procesa datos por partes. Es útil para archivos grandes o transferencias de red sin cargar todo el contenido de una vez.

| Tipo | Función |
|---|---|
| Readable | Producir datos que se leen |
| Writable | Recibir datos que se escriben |
| Duplex | Leer y escribir |
| Transform | Transformar datos durante el flujo |

```js
import { createReadStream, createWriteStream } from "node:fs";
import { pipeline } from "node:stream/promises";

try {
    await pipeline(
        createReadStream("./notes.txt"),
        createWriteStream("./notes-copy.txt", { flags: "wx" }),
    );
    console.log("Copia terminada");
} catch (error) {
    console.error("No se pudo copiar:", error.message);
}
```

El ejemplo falla si el destino ya existe. **Backpressure** es el control del flujo cuando el consumidor recibe más lento de lo que el productor genera. `pipeline` coordina ese flujo y el manejo de errores entre streams.

Referencia: [Stream](https://nodejs.org/api/stream.html).

## 15. Trabajo intensivo de CPU

Una API puede atender muchas operaciones de I/O pendientes sin crear un hilo JavaScript por petición. Eso no significa que cálculos pesados sean gratuitos.

Para tareas intensivas de CPU, `worker_threads` permite ejecutar JavaScript en otros hilos. Los procesos separados ofrecen otra forma de distribuir trabajo con mayor aislamiento.

No necesitas un worker para cada consulta a una base de datos: las APIs asíncronas ya permiten esperar I/O sin ocupar continuamente el hilo principal. Primero identifica el cuello de botella.

Referencia: [Worker threads](https://nodejs.org/api/worker_threads.html).

## 16. Tests básicos

Node incluye un test runner. Puedes combinarlo con `node:assert/strict` para comprobar comportamiento.

```js
// math.test.mjs — junto al math.mjs de la sección de módulos
import test from "node:test";
import assert from "node:assert/strict";
import { add } from "./math.mjs";

test("suma números positivos y negativos", () => {
    assert.equal(add(2, 3), 5);
    assert.equal(add(-2, 3), 1);
});
```

Ejecuta `node --test math.test.mjs`. Un test asíncrono puede recibir una función `async`; espera sus operaciones para que el test no termine antes de comprobarlas.

En una API conviene probar respuestas correctas, datos inválidos y recursos inexistentes.

Referencia: [Test runner](https://nodejs.org/api/test.html).

## 17. Preguntas rápidas de repaso

| Pregunta | Respuesta breve |
|---|---|
| ¿Node.js es un lenguaje? | No, es un entorno para ejecutar JavaScript. |
| ¿Todo Node funciona en un único hilo? | No; el JavaScript principal usa un hilo, pero existen mecanismos internos y workers con otros hilos. |
| ¿`async` evita que un cálculo bloquee? | No; el cálculo sigue ocupando el hilo donde se ejecuta. |
| ¿Qué devuelve una función `async`? | Una Promise. |
| ¿`await` detiene todo el servidor? | No por sí mismo; suspende la ejecución de esa función. |
| ¿`Promise.all` cancela las demás tareas si una falla? | No automáticamente. |
| ¿Para qué sirve el lockfile? | Para registrar las versiones resueltas de las dependencias. |
| ¿`setTimeout(fn, 0)` ejecuta `fn` inmediatamente? | No; el callback espera su oportunidad de ejecución. |
| ¿Qué diferencia hay entre un Buffer y un string? | El Buffer representa bytes; el string representa texto. |
| ¿Cuándo usar un stream? | Cuando conviene procesar datos por partes. |
| ¿Un EventEmitter ejecuta listeners de forma asíncrona? | No; `emit` los llama sincrónicamente. |

## 18. Práctica sugerida

Construye una pequeña API de temas de estudio:

1. Crea un proyecto ESM y un script `start`.
2. Implementa `GET /topics` con el servidor básico.
3. Lee los temas desde un archivo JSON usando `node:fs/promises`.
4. Implementa un detalle por ID y devuelve 404 cuando no exista.
5. Agrega un POST con validación del título y límite del tamaño de entrada.
6. Prueba respuestas exitosas, datos inválidos y archivos ausentes.

Puedes repasar primero las [estructuras de datos de JavaScript](../javascript/data-structures.md). Como siguientes temas, estudia un framework web como Express, conexión a bases de datos y autenticación.
