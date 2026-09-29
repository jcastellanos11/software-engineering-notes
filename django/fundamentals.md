# Django — Fundamentals

> Guía introductoria para estudiar Django y repasar conceptos habituales en entrevistas.
>
> Objetivo: entender cómo recibe peticiones, organiza el código, trabaja con una base de datos y genera respuestas, sin profundizar en arquitectura avanzada.

Los ejemplos usan Python y una app llamada `notes` para administrar temas de estudio. Los nombres de archivo indican dónde iría cada fragmento en un proyecto Django; este documento es una guía, no una aplicación instalada en el repositorio.

## 1. ¿Qué es Django?

Django es un framework web de Python. Incluye herramientas para rutas, acceso a bases de datos, plantillas, formularios, autenticación y administración.

Puede generar páginas HTML o responder con datos, como JSON. React puede construir la interfaz de una aplicación cuyo backend sea Django, pero ninguno requiere al otro.

## 2. Arquitectura MVT

MVT significa **Model, View, Template**.

| Parte | Responsabilidad |
|---|---|
| Model | Representar datos y su comportamiento |
| View | Procesar una petición y producir una respuesta |
| Template | Definir cómo presentar los datos, normalmente en HTML |

En una comparación aproximada con MVC, la vista de Django asume parte del trabajo que suele atribuirse al controlador; el template corresponde a la presentación. Los nombres no tienen una equivalencia exacta.

Una petición típica sigue este recorrido:

```text
Navegador → middleware → URL → view → model / template → respuesta
```

El middleware también participa al devolver la respuesta. Una vista no está obligada a consultar un modelo ni a usar un template.

Referencia: [Django at a glance](https://docs.djangoproject.com/en/stable/intro/overview/).

## 3. Project vs app

Un **project** reúne la configuración general del sitio. Una **app** agrupa una funcionalidad, como usuarios, pedidos o notas. Un proyecto puede contener varias apps.

En un entorno virtual con Django instalado:

```bash
django-admin startproject config .
python manage.py startapp notes
```

Estructura simplificada:

```text
manage.py
config/
    settings.py
    urls.py
    asgi.py
    wsgi.py
notes/
    admin.py
    apps.py
    migrations/
    models.py
    tests.py
    views.py
```

`manage.py` ejecuta comandos con la configuración del proyecto. `settings.py` contiene opciones como base de datos y apps habilitadas. `asgi.py` y `wsgi.py` exponen puntos de entrada para servidores compatibles.

`startapp` no crea automáticamente `urls.py`, `forms.py` ni la carpeta de templates de la app; los agregas según los necesites.

Referencia: [Tutorial — Part 1](https://docs.djangoproject.com/en/stable/intro/tutorial01/).

## 4. Modelos y migraciones

Registra primero la app en `config/settings.py`, conservando las entradas existentes:

```python
# Agregar a la lista existente de INSTALLED_APPS:
"notes.apps.NotesConfig",
```

Un modelo describe los datos mediante una clase:

```python
# notes/models.py
from django.db import models


class Topic(models.Model):
    title = models.CharField(max_length=150)
    completed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

Django agrega una clave primaria automáticamente si no defines una. `__str__` da una representación legible, por ejemplo en el admin.

Las **migraciones** versionan cambios en la estructura de los datos:

```bash
python manage.py makemigrations notes
python manage.py migrate
```

| Comando | Qué hace |
|---|---|
| `makemigrations` | Genera archivos que describen cambios en los modelos |
| `migrate` | Aplica las migraciones pendientes a la base de datos |

Cambiar `models.py` no modifica automáticamente la base de datos. Los archivos de migración se guardan en el control de versiones.

Referencia: [Tutorial — Part 2](https://docs.djangoproject.com/en/stable/intro/tutorial02/).

## 5. ORM y operaciones CRUD

El **ORM** permite consultar y modificar datos mediante Python, sin escribir SQL para cada operación. No elimina la necesidad de entender cómo funciona la base de datos.

Después de aplicar las migraciones, abre `python manage.py shell`:

```python
from notes.models import Topic

# Create: guardar un registro.
topic = Topic.objects.create(title="Estudiar Django")

# Read: consultar registros.
pending = Topic.objects.filter(completed=False)
same_topic = Topic.objects.get(pk=topic.pk)

# Update: modificar y guardar.
topic.completed = True
topic.save()

# Delete: eliminar el registro.
topic.delete()
```

`objects` es el manager que ofrece acceso a consultas. `pk` es un alias para la clave primaria.

### `get()` vs `filter()`

| Método | Resultado |
|---|---|
| `get(...)` | Un objeto; lanza `DoesNotExist` si no encuentra ninguno y `MultipleObjectsReturned` si encuentra varios |
| `filter(...)` | Un QuerySet que puede contener cero, uno o varios objetos |

Un **QuerySet** representa una consulta. Generalmente se evalúa de forma diferida: construirlo o encadenar filtros no ejecuta inmediatamente SQL; iterarlo, por ejemplo, sí puede hacerlo.

```python
topics = Topic.objects.filter(completed=False).order_by("title")
for topic in topics:
    print(topic.title)
```

Referencia: [Making queries](https://docs.djangoproject.com/en/stable/topics/db/queries/).

## 6. Relaciones entre modelos

| Campo | Relación | Ejemplo |
|---|---|---|
| `ForeignKey` | Muchos a uno | Muchos temas pertenecen a una categoría |
| `OneToOneField` | Uno a uno | Un perfil corresponde a un usuario |
| `ManyToManyField` | Muchos a muchos | Un tema tiene etiquetas y cada etiqueta pertenece a varios temas |

Ejemplo opcional para ampliar `notes/models.py`:

```python
class Category(models.Model):
    name = models.CharField(max_length=80)

# Campo que se agrega dentro de Topic:
# category = models.ForeignKey(Category, on_delete=models.PROTECT, null=True)
```

`on_delete` define qué ocurre al borrar el objeto referenciado. `PROTECT` impide borrar una categoría que todavía tiene temas; `CASCADE` también elimina los objetos relacionados que la referencian.

`null=True` permite `NULL` en la base de datos. `blank=True` permite un valor vacío en la validación de formularios; son opciones distintas. Al cambiar el modelo, genera y aplica una nueva migración.

Referencia: [Model field reference](https://docs.djangoproject.com/en/stable/ref/models/fields/).

## 7. URLs y views

Una vista recibe un `HttpRequest` y devuelve una respuesta. El URLconf relaciona rutas con vistas.

```python
# notes/views.py
from django.http import JsonResponse
from django.shortcuts import get_object_or_404, render
from .models import Topic


def topic_list(request):
    topics = Topic.objects.order_by("title")
    return render(request, "notes/topic_list.html", {"topics": topics})


def topic_detail(request, pk):
    topic = get_object_or_404(Topic, pk=pk)
    return JsonResponse({"id": topic.pk, "title": topic.title})
```

```python
# notes/urls.py — crear este archivo
from django.urls import path
from . import views

app_name = "notes"
urlpatterns = [
    path("", views.topic_list, name="list"),
    path("<int:pk>/", views.topic_detail, name="detail"),
]
```

```python
# config/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("topics/", include("notes.urls")),
]
```

`/topics/3/` entrega `pk=3` a la vista. `get_object_or_404` genera una respuesta 404 si no existe. `render` combina un template y un contexto para devolver HTML; `JsonResponse` devuelve JSON.

Los nombres de ruta permiten construir enlaces sin repetir rutas literales por toda la aplicación.

Referencia: [Django shortcut functions](https://docs.djangoproject.com/en/stable/topics/http/shortcuts/).

## 8. Templates

Con la configuración inicial de templates y la app registrada, crea:

```html
<!-- notes/templates/notes/topic_list.html -->
<h1>Temas de estudio</h1>
<ul>
    {% for topic in topics %}
        <li>
            <a href="{% url 'notes:detail' topic.pk %}">{{ topic.title }}</a>
            {% if topic.completed %}✓{% endif %}
        </li>
    {% empty %}
        <li>No hay temas.</li>
    {% endfor %}
</ul>
```

`{{ ... }}` muestra valores. `{% ... %}` ejecuta etiquetas de plantilla, como condiciones, ciclos o construcción de URLs.

Los templates presentan información; las consultas y las reglas de negocio deben resolverse fuera de ellos. Con `extends` y `block` puedes compartir una estructura base entre páginas.

Referencia: [The Django template language](https://docs.djangoproject.com/en/stable/ref/templates/language/).

## 9. GET, POST y formularios

**GET** se usa para obtener información; no debe modificar datos. **POST** se utiliza para enviar datos que el servidor procesa, por ejemplo para crear un registro.

Un `Form` define campos y validación. Un `ModelForm` construye un formulario a partir de un modelo.

```python
# notes/forms.py — crear este archivo
from django import forms
from .models import Topic


class TopicForm(forms.ModelForm):
    class Meta:
        model = Topic
        fields = ["title"]
```

```python
# Agregar a notes/views.py
from django.shortcuts import redirect
from .forms import TopicForm


def topic_create(request):
    if request.method == "POST":
        form = TopicForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect("notes:list")
    else:
        form = TopicForm()

    return render(request, "notes/topic_form.html", {"form": form})
```

Agrega `path("new/", views.topic_create, name="create")` a `notes/urls.py`.

```html
<!-- notes/templates/notes/topic_form.html -->
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Guardar</button>
</form>
```

`is_valid()` valida y deja los valores procesados en `cleaned_data`. Si hay errores, se muestra el formulario con ellos. Redirigir después de guardar evita reenviar el POST al recargar la página de destino.

Referencia: [Working with forms](https://docs.djangoproject.com/en/stable/topics/forms/).

## 10. Admin

El admin es una interfaz incorporada para gestionar datos con usuarios autorizados. Registra el modelo:

```python
# notes/admin.py
from django.contrib import admin
from .models import Topic

admin.site.register(Topic)
```

Después de `migrate`, crea un usuario administrador y ejecuta el servidor local:

```bash
python manage.py createsuperuser
python manage.py runserver
```

Accede a `/admin/`. El admin sirve para gestión interna; la interfaz pública de tu producto se construye por separado. `runserver` es un servidor de desarrollo.

Referencia: [Tutorial — Part 2: Admin](https://docs.djangoproject.com/en/stable/intro/tutorial02/#introducing-the-django-admin).

## 11. Autenticación y autorización

**Autenticación** responde quién es el usuario. **Autorización** determina qué puede hacer.

Django incluye usuarios, grupos, permisos y sesiones. Con el middleware correspondiente, `request.user` representa al usuario actual o a un usuario anónimo.

```python
from django.contrib.auth.decorators import login_required

# Aplicar a una vista que requiera sesión:
@login_required
def private_topics(request):
    return render(request, "notes/topic_list.html", {
        "topics": Topic.objects.order_by("title"),
    })
```

Este fragmento reutiliza los imports de `notes/views.py`. Requiere configurar una ruta de login para la redirección.

El decorador exige iniciar sesión, pero no filtra registros por propietario. Si cada usuario debe ver solo sus notas, necesitas una relación con el usuario y filtrar por ella, además de comprobar permisos al modificar datos.

Referencia: [Using the authentication system](https://docs.djangoproject.com/en/stable/topics/auth/default/).

## 12. Middleware

Un middleware participa en el procesamiento de peticiones y respuestas. Se configura en la lista `MIDDLEWARE` de settings.

Ejemplos incluidos: manejo de sesiones, autenticación y protección CSRF. El orden importa: las peticiones atraviesan las capas en orden y las respuestas regresan en orden inverso. Un middleware puede devolver una respuesta sin llegar a la vista.

Referencia: [Middleware](https://docs.djangoproject.com/en/stable/topics/http/middleware/).

## 13. Seguridad básica

- **CSRF:** protege frente a peticiones que intentan aprovechar la sesión del usuario desde otro sitio. Mantén el middleware y usa `{% csrf_token %}` en formularios POST internos.
- **SQL injection:** el ORM parametriza valores en las consultas habituales. No construyas SQL concatenando entradas del usuario.
- **XSS:** las plantillas escapan variables por defecto. Evita marcar contenido no confiable como seguro.
- **Configuración:** en producción, desactiva `DEBUG`, configura `ALLOWED_HOSTS` y mantén `SECRET_KEY` fuera del repositorio.

Estas herramientas no reemplazan la validación ni los controles de acceso propios de la aplicación.

Referencia: [Security in Django](https://docs.djangoproject.com/en/stable/topics/security/).

## 14. Archivos static vs media

**Static** son recursos de la aplicación: CSS, JavaScript e imágenes del diseño. **Media** son archivos subidos por usuarios.

```html
{% load static %}
<link rel="stylesheet" href="{% static 'notes/styles.css' %}">
```

Ese recurso puede vivir en `notes/static/notes/styles.css`. En producción, `collectstatic` reúne recursos estáticos en `STATIC_ROOT` para servirlos con la infraestructura elegida. Los archivos subidos requieren almacenamiento y configuración separados.

Referencia: [Managing static files](https://docs.djangoproject.com/en/stable/howto/static-files/).

## 15. Function-based views vs class-based views

Las **FBV** son funciones: hacen explícito el flujo de la petición y resultan sencillas para comenzar.

Las **CBV** son clases: permiten reutilizar comportamiento mediante herencia y vistas genéricas como `ListView`, `DetailView` o `CreateView`.

```python
from django.views.generic import ListView
from .models import Topic


class TopicListView(ListView):
    model = Topic
    template_name = "notes/topic_list.html"
    context_object_name = "topics"
    ordering = ["title"]

# Alternativa en urls.py, importando TopicListView:
# path("", TopicListView.as_view(), name="list")
```

Esta clase es una alternativa a `topic_list`, no una segunda ruta con el mismo nombre. `as_view()` produce el callable que utiliza el enrutador. Ningún enfoque es siempre mejor: depende de la claridad y reutilización que necesites.

Referencia: [Introduction to class-based views](https://docs.djangoproject.com/en/stable/topics/class-based-views/intro/).

## 16. Consultas N+1

El problema **N+1** aparece cuando consultas una lista y después haces otra consulta por cada elemento para leer sus relaciones.

| Herramienta | Uso habitual |
|---|---|
| `select_related()` | Relaciones `ForeignKey` y `OneToOneField`, mediante joins |
| `prefetch_related()` | Relaciones múltiples o inversas, mediante consultas adicionales cuyos resultados se combinan en Python |

Si agregaste el campo `category` del ejemplo de relaciones:

```python
topics = Topic.objects.select_related("category")
for topic in topics:
    print(topic.category.name if topic.category else "Sin categoría")
```

No agregues optimizaciones a ciegas: observa las consultas y mide primero.

Referencia: [Database access optimization](https://docs.djangoproject.com/en/stable/topics/db/optimization/).

## 17. Tests básicos

Django incluye herramientas para probar modelos y respuestas HTTP. `TestCase` permite trabajar con una base de datos de prueba aislada de la aplicación.

```python
# notes/tests.py
from django.test import TestCase
from django.urls import reverse
from .models import Topic


class TopicDetailTests(TestCase):
    def test_existing_topic_returns_json(self):
        topic = Topic.objects.create(title="ORM")
        response = self.client.get(reverse("notes:detail", args=[topic.pk]))
        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.json(), {"id": topic.pk, "title": "ORM"})

    def test_missing_topic_returns_404(self):
        response = self.client.get(reverse("notes:detail", args=[999]))
        self.assertEqual(response.status_code, 404)
```

En el proyecto de práctica, ejecuta `python manage.py test notes`. Estos ejemplos verifican tanto una respuesta correcta como el caso de un recurso inexistente.

Referencia: [Writing and running tests](https://docs.djangoproject.com/en/stable/topics/testing/overview/).

## 18. Preguntas rápidas de repaso

| Pregunta | Respuesta breve |
|---|---|
| ¿Project y app son lo mismo? | El proyecto reúne configuración y apps; una app agrupa una funcionalidad. |
| ¿Qué significa MVT? | Model, View, Template: datos, procesamiento y presentación. |
| ¿`makemigrations` modifica la base de datos? | No; genera migraciones. `migrate` las aplica. |
| ¿Qué diferencia hay entre `get` y `filter`? | `get` exige un resultado único; `filter` devuelve un QuerySet. |
| ¿Un QuerySet ejecuta SQL al crearse? | Generalmente no; se evalúa cuando se necesitan sus resultados. |
| ¿`null` y `blank` son equivalentes? | No: almacenamiento en la base de datos frente a validación. |
| ¿Qué hace `get_object_or_404`? | Recupera un objeto o produce un error HTTP 404. |
| ¿Autenticación implica autorización? | No; identificar al usuario no garantiza permiso sobre un recurso. |
| ¿Qué evita el token CSRF? | Peticiones falsificadas que buscan aprovechar la sesión del usuario. |
| ¿Qué es N+1? | Una consulta inicial y consultas adicionales por cada elemento relacionado. |

## 19. Práctica sugerida

Construye un pequeño registro de temas de estudio:

1. Crea el proyecto y la app `notes`.
2. Agrega `Topic`, genera las migraciones y aplícalas.
3. Registra el modelo en el admin y crea algunos temas.
4. Implementa la lista HTML y el detalle JSON.
5. Agrega el formulario de creación y revisa sus errores de validación.
6. Implementa marcar un tema como completado mediante POST con protección CSRF.
7. Ejecuta los tests y agrega uno para el cambio de estado.

Puedes repasar primero las [estructuras de datos de Python](../python/data-structures.md). Después de estos fundamentos, puedes explorar APIs REST y Django REST Framework como tema separado.
