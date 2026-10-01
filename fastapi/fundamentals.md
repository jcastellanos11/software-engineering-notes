# FastAPI — Fundamentals

> Guía introductoria para estudiar FastAPI y repasar conceptos habituales en entrevistas.
>
> Objetivo: entender cómo definir endpoints, validar datos y organizar una API con Python, sin profundizar en arquitectura avanzada.

Los ejemplos usan Python 3.10+ y Pydantic v2. Cada bloque es independiente salvo cuando se indica que amplía otro. Los archivos mencionados pertenecen a un proyecto de práctica; este repositorio contiene la guía, no una aplicación instalada.

## 1. ¿Qué es FastAPI?

FastAPI es un framework de Python para construir APIs. Aprovecha las anotaciones de tipos para validar datos, definir contratos y generar documentación.

Se apoya en **Starlette** para capacidades web y en **Pydantic** para validación y representación de datos.

Una API recibe peticiones HTTP, ejecuta lógica y devuelve respuestas, normalmente JSON. Puede ser consumida por una interfaz React, una aplicación móvil u otro servicio.

## 2. Primera aplicación

En un entorno virtual de práctica, instala FastAPI:

```bash
python -m pip install "fastapi[standard]"
```

```python
# main.py
from fastapi import FastAPI

app = FastAPI()


@app.get("/health")
def health():
    return {"status": "ok"}
```

Ejecuta `fastapi dev main.py` y abre `/health` en el servidor local indicado. Este comando está orientado al desarrollo.

El decorador `@app.get` registra una operación GET. FastAPI convierte el diccionario devuelto en una respuesta JSON.

## 3. OpenAPI y documentación automática

Por defecto, la aplicación expone:

| Ruta | Contenido |
|---|---|
| `/docs` | Interfaz interactiva Swagger UI |
| `/redoc` | Documentación alternativa ReDoc |
| `/openapi.json` | Descripción de la API en formato OpenAPI |

OpenAPI describe rutas, parámetros, esquemas y respuestas. La documentación permite explorar el contrato; no reemplaza las pruebas del comportamiento.

Referencia para estas secciones: [First Steps](https://fastapi.tiangolo.com/tutorial/first-steps/).

## 4. Path parameters

Los parámetros de ruta identifican un recurso dentro de la URL.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/topics/{topic_id}")
def get_topic(topic_id: int):
    return {"id": topic_id}
```

`GET /topics/5` convierte `5` en un entero. `GET /topics/abc` produce un error de validación, normalmente con estado 422.

El tipo valida la forma del dato; no comprueba que exista un tema con ese ID en la base de datos.

El orden de las rutas importa. Declara una ruta fija como `/topics/search` antes de `/topics/{topic_id}` para evitar que `search` se interprete como un ID.

Referencia: [Path Parameters](https://fastapi.tiangolo.com/tutorial/path-params/).

## 5. Query parameters

Los parámetros de consulta aparecen después de `?` y suelen expresar filtros o paginación.

```python
from typing import Annotated
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/topics")
def list_topics(
    q: str | None = None,
    limit: Annotated[int, Query(ge=1, le=100)] = 10,
):
    return {"query": q, "limit": limit}
```

Ejemplo: `/topics?q=python&limit=5`.

`q` puede omitirse porque tiene un valor por defecto. `str | None` permite `None`, pero esa anotación por sí sola no establece un valor por defecto.

`Annotated` combina un tipo con metadatos. Aquí `Query` exige que `limit` esté entre 1 y 100.

Referencia: [Query Parameters](https://fastapi.tiangolo.com/tutorial/query-params/).

## 6. Request body y Pydantic

Un modelo Pydantic define los campos esperados en el cuerpo de una petición.

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class TopicCreate(BaseModel):
    title: str = Field(min_length=1, max_length=100)
    completed: bool = False


@app.post("/topics", status_code=201)
def create_topic(topic: TopicCreate):
    return topic.model_dump()
```

Cuerpo JSON de ejemplo:

```json
{
  "title": "Estudiar FastAPI",
  "completed": false
}
```

FastAPI valida el cuerpo antes de ejecutar la función. Si falta `title`, devuelve un error de validación. `model_dump()` obtiene un diccionario a partir del modelo.

Este ejemplo devuelve los datos recibidos; todavía no los guarda. Un esquema Pydantic no es por sí mismo una tabla ni un ORM.

La validación puede convertir valores compatibles según el tipo y su configuración. Además, una longitud mínima no impide un título formado solo por espacios: esa regla requiere validación adicional.

Referencia: [Request Body](https://fastapi.tiangolo.com/tutorial/body/).

## 7. Response models

El modelo de respuesta define qué datos expone un endpoint. Permite validar, documentar y filtrar la salida.

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class TopicPublic(BaseModel):
    id: int
    title: str


@app.get("/topics/{topic_id}", response_model=TopicPublic)
def get_topic(topic_id: int):
    return {
        "id": topic_id,
        "title": "Validación",
        "internal_note": "Dato interno",
    }
```

La respuesta contiene `id` y `title`; `internal_note` queda fuera. Separar modelos de entrada y salida ayuda a mantener un contrato claro y evitar exponer campos internos.

Si tu función devuelve datos incompatibles con el modelo de respuesta, es un error del servidor, no un error de validación atribuible al cliente.

Referencia: [Response Model](https://fastapi.tiangolo.com/tutorial/response-model/).

## 8. Códigos HTTP y `HTTPException`

| Código | Significado habitual |
|---|---|
| 200 | Operación exitosa |
| 201 | Recurso creado |
| 204 | Éxito sin cuerpo de respuesta |
| 401 | Faltan credenciales válidas |
| 403 | No se permite la operación |
| 404 | Recurso inexistente |
| 422 | Datos de entrada que no cumplen la validación |
| 500 | Error interno del servidor |

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()
topics = {1: {"id": 1, "title": "HTTP"}}


@app.get("/topics/{topic_id}")
def get_topic(topic_id: int):
    if topic_id not in topics:
        raise HTTPException(status_code=404, detail="Tema no encontrado")
    return topics[topic_id]
```

Se usa `raise`, no `return`, para interrumpir el flujo con `HTTPException`. Un fallo inesperado no debe convertirse indiscriminadamente en 404 ni exponer detalles internos al cliente.

Referencia: [Handling Errors](https://fastapi.tiangolo.com/tutorial/handling-errors/).

## 9. `def` vs `async def`

Usa `async def` cuando trabajes con APIs asíncronas que puedas esperar con `await`. FastAPI ejecuta los endpoints declarados con `def` en un pool de hilos para no bloquear directamente el event loop con su ejecución.

```python
import asyncio
from fastapi import FastAPI

app = FastAPI()


@app.get("/wait")
async def wait_example():
    await asyncio.sleep(0.1)
    return {"ready": True}
```

`asyncio.sleep` solo simula una espera; no hace falta añadirla a endpoints reales.

Evita llamar a operaciones bloqueantes como `time.sleep()` directamente desde un endpoint asíncrono. Declarar una función `async` tampoco hace que un cálculo intensivo de CPU se ejecute en paralelo.

Una función auxiliar normal que llamas tú mismo no pasa automáticamente a un pool de hilos. Importa cómo se ejecuta toda la cadena de llamadas.

Referencia: [Concurrency and async / await](https://fastapi.tiangolo.com/async/).

## 10. Inyección de dependencias con `Depends`

Una dependencia es una función que FastAPI ejecuta para obtener un valor o comprobar una condición antes de llamar al endpoint.

```python
from typing import Annotated
from fastapi import Depends, FastAPI, Query

app = FastAPI()


def pagination(
    limit: Annotated[int, Query(ge=1, le=100)] = 10,
    offset: Annotated[int, Query(ge=0)] = 0,
):
    return {"limit": limit, "offset": offset}


@app.get("/topics")
def list_topics(page: Annotated[dict, Depends(pagination)]):
    return page
```

Se pasa `pagination`, sin invocarla. FastAPI resuelve sus parámetros y entrega el resultado al endpoint.

Las dependencias permiten reutilizar paginación, validación de credenciales o acceso a una sesión de base de datos. Pueden tener subdependencias y reemplazarse durante pruebas.

Referencia: [Dependencies](https://fastapi.tiangolo.com/tutorial/dependencies/).

## 11. Organización con `APIRouter`

Cuando crece la aplicación, agrupa endpoints en routers.

```python
# topics.py
from fastapi import APIRouter

router = APIRouter(prefix="/topics", tags=["topics"])


@router.get("/")
def list_topics():
    return []
```

```python
# main.py — junto a topics.py
from fastapi import FastAPI
from topics import router

app = FastAPI()
app.include_router(router)
```

La ruta resultante es `/topics/`. Los tags agrupan operaciones en la documentación. El router organiza rutas; no es un servidor independiente.

Referencia: [Bigger Applications](https://fastapi.tiangolo.com/tutorial/bigger-applications/).

## 12. Base de datos y persistencia

FastAPI no exige una base de datos ni incluye un ORM obligatorio. Puedes usar bibliotecas como SQLAlchemy o SQLModel y elegir un motor de base de datos.

| Elemento | Responsabilidad |
|---|---|
| Modelo Pydantic | Validar y representar datos de la API |
| Modelo ORM | Representar datos persistidos y sus relaciones |
| Sesión de base de datos | Coordinar consultas y transacciones |
| Migración | Versionar cambios del esquema de la base de datos |

Una dependencia con `yield` puede entregar una sesión y cerrarla al terminar su uso. Mantén el ciclo de vida de las sesiones controlado y usa transacciones para cambios que deban completarse juntos.

Un diccionario global sirve para ejemplos, pero pierde sus datos al reiniciar y no se comparte automáticamente entre procesos de servidor.

Referencia: [SQL Databases](https://fastapi.tiangolo.com/tutorial/sql-databases/).

## 13. Autenticación y autorización

La **autenticación** identifica al usuario. La **autorización** comprueba si puede realizar una acción sobre un recurso.

FastAPI ofrece utilidades para describir y extraer credenciales. Por ejemplo, `OAuth2PasswordBearer` extrae un bearer token e integra el esquema en OpenAPI; no verifica por sí solo la firma, la expiración o los permisos de un JWT.

Una dependencia de seguridad debe validar las credenciales y resolver al usuario. Después hay que comprobar permisos y, cuando corresponda, la propiedad del recurso.

No basta con recibir un token para considerar autenticado a quien lo envía.

Referencia: [Security — First Steps](https://fastapi.tiangolo.com/tutorial/security/first-steps/).

## 14. Middleware y CORS

Un middleware procesa peticiones y respuestas alrededor de los endpoints. Puede medir tiempos, agregar cabeceras o realizar otras tareas transversales.

**CORS** controla qué orígenes permite el servidor para el acceso desde JavaScript en el navegador. Un origen combina protocolo, host y puerto.

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],
    allow_methods=["GET", "POST"],
    allow_headers=["Content-Type", "Authorization"],
)
```

Esto permite ese origen de desarrollo. Para cookies entre orígenes puede ser necesario configurar credenciales y otras opciones coherentes en cliente y servidor.

CORS no es autenticación ni impide que un cliente ajeno al navegador llame a la API.

Referencias: [Middleware](https://fastapi.tiangolo.com/tutorial/middleware/) y [CORS](https://fastapi.tiangolo.com/tutorial/cors/).

## 15. Background tasks

`BackgroundTasks` permite ejecutar trabajo después de enviar la respuesta, dentro del proceso que atiende la aplicación.

```python
import logging
from fastapi import BackgroundTasks, FastAPI

app = FastAPI()
logger = logging.getLogger(__name__)


def record_visit(topic_id: int):
    logger.info("Tema visitado: %s", topic_id)


@app.post("/topics/{topic_id}/visits", status_code=202)
def visit_topic(topic_id: int, tasks: BackgroundTasks):
    tasks.add_task(record_visit, topic_id)
    return {"accepted": True}
```

Es apropiado para tareas pequeñas. No proporciona una cola durable: si el proceso termina, el trabajo puede perderse. Una tarea pesada o que requiera reintentos persistentes suele necesitar un sistema de trabajos separado.

Referencia: [Background Tasks](https://fastapi.tiangolo.com/tutorial/background-tasks/).

## 16. Ciclo de vida con lifespan

`lifespan` permite inicializar recursos al arrancar y liberarlos al apagar la aplicación, por ejemplo un cliente compartido o un pool de conexiones.

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI


@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.topics_cache = {}
    yield
    app.state.topics_cache.clear()


app = FastAPI(lifespan=lifespan)
```

El código anterior al `yield` prepara recursos; el posterior los libera. Esta caché didáctica vive en memoria y es independiente en cada proceso.

Referencia: [Lifespan Events](https://fastapi.tiangolo.com/advanced/events/).

## 17. Tests con `TestClient`

`TestClient` permite enviar peticiones a la app desde pruebas sin levantar un servidor HTTP externo. Instala `pytest` y `httpx` en el entorno de práctica si no están disponibles.

Para probar el ejemplo de `HTTPException` de la sección 8, guárdalo como `main.py` y crea:

```python
# test_main.py
from fastapi.testclient import TestClient
from main import app


def test_existing_topic():
    with TestClient(app) as client:
        response = client.get("/topics/1")
    assert response.status_code == 200
    assert response.json() == {"id": 1, "title": "HTTP"}


def test_missing_topic():
    with TestClient(app) as client:
        response = client.get("/topics/999")
    assert response.status_code == 404


def test_invalid_id():
    with TestClient(app) as client:
        response = client.get("/topics/abc")
    assert response.status_code == 422
```

Ejecuta `python -m pytest`. El contexto `with TestClient(app)` también gestiona el lifespan cuando la aplicación lo define.

Referencia: [Testing](https://fastapi.tiangolo.com/tutorial/testing/).

## 18. Preguntas rápidas de repaso

| Pregunta | Respuesta breve |
|---|---|
| ¿Qué aporta Pydantic? | Validación y representación de datos mediante modelos tipados. |
| ¿Path y query parameters son lo mismo? | No: identificadores en la ruta frente a parámetros después de `?`. |
| ¿Un campo `str \| None` siempre puede omitirse? | No; necesita un valor por defecto para no ser requerido. |
| ¿Qué hace `response_model`? | Define, valida y filtra la salida del endpoint. |
| ¿Para qué sirve `Depends`? | Para resolver y reutilizar dependencias del endpoint. |
| ¿`async def` acelera cualquier función? | No; beneficia esperas asíncronas y no elimina el costo del trabajo de CPU. |
| ¿Cómo se devuelve un error 404? | Lanzando `HTTPException` con ese código. |
| ¿FastAPI incluye un ORM obligatorio? | No; la capa de persistencia se elige por separado. |
| ¿CORS protege un endpoint de usuarios no autorizados? | No; requiere autenticación y controles de acceso propios. |
| ¿Una tarea en background está garantizada tras un reinicio? | No; no es una cola persistente. |
| ¿Qué aporta OpenAPI? | Una descripción estructurada del contrato de la API. |

## 19. Práctica sugerida

Construye una API de temas de estudio:

1. Define modelos de entrada y salida para un tema.
2. Implementa listar, crear y consultar por ID.
3. Agrega un límite de resultados y valida su rango.
4. Devuelve 404 para temas inexistentes y 201 al crear.
5. Separa los endpoints usando `APIRouter`.
6. Prueba peticiones válidas, campos faltantes e IDs inválidos.
7. Sustituye el almacenamiento temporal por una base de datos.

Puedes repasar las [estructuras de datos de Python](../python/data-structures.md) y comparar con los [fundamentos de Django](../django/fundamentals.md).
