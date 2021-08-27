# Django-Notes
Notas del Framework Django

# Menú

- [Iniciar Proyecto](#iniciar-proyecto)
  - [Activa el proyecto](#activa-el-proyecto)
  - [Correr Servidor](#correr-servidor)

- [MTV](#mtv)
  - [Vista](#vista) 
  - [Vista con parametros](#vista-con-parametros) 

- [Plantillas](#plantillas)
  - [Variables](#variables)
  - [Bucles](#bucles)
  - [Condicionales](#condicionales)
  - [Control de flujo](#control-de-flujo)
  - [Filtros](#filtros)
  - [Cargadores de Plantillas](#cargadores-de-plantillas)
  - [Shortcuts](#shortcuts)
   - [render](#render)
  - [Plantillas incrustadas](#plantillas-incrustadas)
  - [Herencia](#Herencia)

- [Aplicación](#aplicación)
- [BBDD](#bbdd)
    - [Insertar Datos](#insertar-datos)
    - [Actualizar Datos](#actualizar-datos)
    - [Borrar Datos](#borrar-datos)
    - [Consultas](#consultas)

- [Panel de Administración](#panel-de-administracion)
  - [Filtros](#Filtros)
  - [Cambiar idioma](#cambiar-idioma)

- [Formularios](#formularios)
  - [Busqueda](#busqueda)

- [Api forms](#api-forms)

---

## Iniciar Proyecto

comando: `django-admin startproject nombre`

### Activa el Proyecto

comando: `python manage.py migrate`

### Correr Servidor

comando: `python manage.py runserver`

---

## MTV

> Model Template View
### Vista

> views.py
```python
from django.http import HttpResponse
def saludo(request): # Cada función es una vista
    return HttpResponse("Hola Mundo")
```

> urls.py
```python
from django.contrib import admin
from django.urls import path
from Main.views import saludo # Importando la vista
urlpatterns = [
    path('admin/', admin.site.urls),
    path('saludo/', saludo),
]
```

#### Vista con parametros

> views.py
```python
def getAge(request, age):
    
    edad = 21
    
    p = age - 2021
    
    fa = edad + p
    
    doc = f"""
    <html>
    <body>
    <h2>en el año {age} tendras {fa} años</h2>
    </body>
    </html>
    """
    
    return HttpResponse(doc)
```

> urls.py
```python
path('Edad/<int:age>', getAge), # el argumento de url siempre lo lee como string
```

> views.py
```python
def getAge(request,edad,age):
    
    # edad = 21
    
    p = age - 2021
    
    fa = edad + p
    
    doc = f"""
    <html>
    <body>
    <h2>en el año {age} tendras {fa} años</h2>
    </body>
    </html>
    """
    
    return HttpResponse(doc)
```

> urls.py
```python
path('Edad/<int:edad>/<int:age>', getAge),
```
---

## Plantillas

1. Creación objeto template

`plt = Template(doc_externo.read())`

2. Creación contexto

`ctx = Context()`

3. Renderizar objeto Template

`doc = plt.render(ctx)`

**Ejemplo**

```python
from django.http import HttpResponse
from django.template import Template,Context
def PlantillaMain(request):
    
    doc_externo = open("C:/Users/chava/OneDrive/Escritorio/Mis_cosas/Programacion/VSPROJECTS/VSPYTHON/WEB/Test/Main/Templates/index.html")
    
    plt = Template(doc_externo.read())
    
    doc_externo.close()
    
    ctx = Context()
    
    doc = plt.render(ctx)
    
    return HttpResponse(doc)
```

#### Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <h1>Hola Mundo!!</h1>

    <p>Lorem ipsum dolor sit.</p>
</body>
</html>
```

---

### Variables

```python
from django.http import HttpResponse
from django.template import Template,Context
def PlantillaMain(request):
    
    var_nombre = "Tom"
    
    doc_externo = open("C:/Users/chava/OneDrive/Escritorio/Mis_cosas/Programacion/VSPROJECTS/VSPYTHON/WEB/Test/Main/Templates/index.html")
    
    plt = Template(doc_externo.read())
    
    doc_externo.close()
    
    ctx = Context({"Nombre":var_nombre}) # uso de diccionarios
    
    doc = plt.render(ctx)
    
    return HttpResponse(doc)
```

#### Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <h1>Hola Mundo!!</h1>

    <!-- Uso de {{}} como lugar para hacer referencia a la variable por su llave -->
    <p>Mi apodo es {{Nombre}} y ahora es: {{tiempo}}</p> 
</body>
</html>
```

**Ejemplo 2**

```python
from django.http import HttpResponse
from django.template import Template,Context
import datetime
def PlantillaMain(request):
    
    tiempo = datetime.datetime.now()
    
    var_nombre = "Tom"
    
    doc_externo = open("C:/Users/chava/OneDrive/Escritorio/Mis_cosas/Programacion/VSPROJECTS/VSPYTHON/WEB/Test/Main/Templates/index.html")
    
    plt = Template(doc_externo.read())
    
    doc_externo.close()
    
    ctx = Context({"Nombre":var_nombre, "tiempo":tiempo}) # uso de diccionarios
    
    doc = plt.render(ctx)
    
    return HttpResponse(doc)
```

#### Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <h1>Hola Mundo!!</h1>

    <p>Mi apodo es {{Nombre}} y ahora es {{tiempo}}</p>
</body>
</html>
```

> _Nota: Si se desea utilizar un método del objeto se puede hacer desde el template, en este caso {{tiempo.day}} sin el uso de "()"_
**Ejemplo 3**

```python
from django.http import HttpResponse
from django.template import Template,Context
import datetime
def PlantillaMain(request):
    
    tiempo = datetime.datetime.now()
    
    lista = [x**2 for x in range(1,11)]
    
    var_nombre = "Tom"
    
    doc_externo = open("C:/Users/chava/OneDrive/Escritorio/Mis_cosas/Programacion/VSPROJECTS/VSPYTHON/WEB/Test/Main/Templates/index.html")
    
    plt = Template(doc_externo.read())
    
    doc_externo.close()
    
    ctx = Context({"Nombre":var_nombre, "tiempo":tiempo,"lista_cuadrados":lista}) # uso de diccionarios
    
    doc = plt.render(ctx)
    
    return HttpResponse(doc)
```

#### Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <h1>Hola Mundo!!</h1>

    <p>Mi apodo es {{Nombre}} y ahora es {{tiempo}}</p>
    <p>{{lista_cuadrados.4}}</p>
</body>
</html>
```

---

### Bucles

**Ejemplo Template**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <ul>
        {% for i in lista_cuadrados %}
            <li>{{i}}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

---

### Condicionales

**Ejemplo Template**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <ul>
        <!-- Verifica si existe y si tiene información -->
        {% if lista_cuadrados %} 
            {% for i in lista_cuadrados %}
                <li>{{i}}</li>
            {% endfor %}
        {% else %}
            <span>No hay nada</span>
        {% endif %}
    </ul>
</body>
</html>
```

---

### Control de flujo

**Ejemplo Template**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <ul>
        <!-- Muy similar a los condicionales normales en Python -->
        {% if Nombre == "Tom" %}
            <p>Si funciona</p>
        {% else %}
            <p>No es el apodo</p>
        {% endif %}
    </ul>
</body>
</html>
```

---

### Filtros

**Ejemplo Template**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <ul>
        <p>
            <!-- upper: todo en mayuscula -->
            {{Nombre|upper}}
            <br>
            <!-- lower: todo en minuscula -->
            {{Nombre|lower}}
            <br>
            <!-- Multiples filtros -->
            {{Nombre|first|lower}}
        </p>
    </ul>
</body>
</html>
```

> _Nota: hay otros filtros ademas de estos aplicables a strings_
[Django filtros](https://docs.djangoproject.com/en/3.2/ref/templates/builtins/ "Documentación Django")

---

### Cargadores de Plantillas

En el archivo `views.py` importar `from django.template import loader`

> settings.py
Dentro de `TEMPLATES` en `DIRS` especificamos la ruta

**Ejemplo**

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': ["C:/Users/chava/OneDrive/Escritorio/Mis_cosas/Programacion/VSPROJECTS/VSPYTHON/WEB/Test/Main/Templates/"],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

**Ejemplo de uso**

```python
from django.http import HttpResponse
from django.template import Template,Context
from django.template import loader
def Cargador(request):
    
    mensaje = "Hola Mundo"
    
    doc_temp = loader.get_template("index.html")
    
    doc = doc_temp.render({"Saludo":mensaje})
    
    return HttpResponse(doc)
```

Simplificando el código al importar `from django.template.loader import get_template` para solo usar el método

**Ejemplo**

```python
from django.http import HttpResponse
from django.template import Template,Context
from django.template.loader import get_template # Para solo usar el get_template
def Cargador(request):
    
    mensaje = "Hola Mundo"
    
    doc_temp = get_template("index.html")
    
    doc = doc_temp.render({"Saludo":mensaje})
    
    return HttpResponse(doc)
```

---

### Shortcuts

[Documentación](https://docs.djangoproject.com/en/3.2/topics/http/shortcuts/ "Shortcuts en Django")

#### render

Importar `from django.shortcuts import render`

**Ejemplo**

```python
from django.shortcuts import render
def ShortCut(request):
    
    mensaje = "Hola que tal!"
    
    return render(request, "index.html", {"Saludo":mensaje})
```

---

### Plantillas incrustadas

> `Menu.html` es creado
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Menu</title>
</head>
<body>
    <ul>
        <li>Opcion 1</li>
        <li>Opcion 2</li>
        <li>Opcion 3</li>
        <li>Opcion 4</li>
    </ul>
</body>
</html>
```

**Implementación**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <!-- Aqui se implementa la plantilla incrustada -->
    {% include "Menu.html" %} 
    <ul>
        <p>
            <!-- upper: todo en mayuscula -->
            {{Saludo|upper}}
            <br>
            <!-- lower: todo en minuscula -->
            {{Saludo|lower}}
            <br>
            <!-- Multiples filtros -->
            {{Saludo|first|lower}}
        </p>
    </ul>
</body>
</html>
```

---

### Herencia

> Se crea la plantilla `Padre.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Super</title>
</head>
<body>
    <header>
        <nav>
            <ul>
                <li>Opcion 1</li>
                <li>Opcion 2</li>
                <li>Opcion 3</li>
                <li>Opcion 4</li>
                <li>Opcion 5</li>
            </ul>
        </nav>
    </header>

    <footer>
        <span>Derechos Luciano 2021</span>
    </footer>
</body>
</html>
```

La plantilla `Hija.html` hereda de `Padre.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    {% extends "super/Padre.html" %}
    <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Sint fugit ea a dolorum repudiandae officia aperiam voluptatem iusto cupiditate reiciendis assumenda explicabo sunt, velit quis aliquid? Dolorum, provident! Suscipit, velit?</p>
</body>
</html>
```

## Aplicación

> _Nota: Todos los modelos (tablas de BBDD) tienen que estar en una aplicación_

**Comandos**

`python manage.py startapp nombre` Empezar aplicación

`python manage.py check nombre` Mirar que la aplicación ejecute correctamente

> models.py

```python
from django.db import models

# Create your models here.

class User(models.Model):
    
    nombre = models.CharField(max_length=30)
    direccion = models.CharField(max_length=30)
    edad = models.IntegerField()
    email = models.EmailField()
    tfno = models.CharField(max_length=30)
    
    
class Article(models.Model):
    
    nombre = models.CharField(max_length=30)
    seccion = models.CharField(max_length=30)
    precio = models.IntegerField()
    
class Pedidos(models.Model): # spanglish
    
    numero = models.IntegerField()
    fecha = models.DateField()
    entregado = models.BooleanField()
    
```

> settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'StoreApp', # Nuevas Apps
]
```

> BBDD hecha en Sqlite por defecto


`python manage.py makemigrations` migrar los modelos a la base de datos

`StoreApp\migrations\0001_initial.py` código de migración IMPORTANTE

`python manage.py sqlmigrate 0001` genera el código SQL

`python manage.py migrate` exporta información a la BBDD

---

## BBDD

### Insertar datos

`python manage.py shell` acceder al shell de python

`>>> from StoreApp.models import User` modulo con el cual se insertan datos

**Ejemplo de insersion**

```sh
>>> usr = User(nombre="Luciano",direccion="Calle 45",edad=21,email="chavarria890lc@gmail.com",tfno="232121344")
>>> usr.save()
```

`>>> usr = User.objects.create(nombre="Tom",direccion="Calle 12",edad=22,email="chavarria890@gmail.com",tfno="23244344") ` para simplificar el comando

### Actualizar datos

**Ejemplo**

`>>> usr.name = "Juan"`

`>>> usr.save()`

### Borrar datos

**Ejemplo**

`>>> usr2 = User.objects.get(id=1)`

`>>> usr2.delete()`

### Consultas

**Ejemplo**

```sh
>>> Lista = User.objects.all()
>>> Lista
<QuerySet [<User: User object (2)>, <User: User object (3)>]>
>>> Lista.query.__str__()  
'SELECT "StoreApp_user"."id", "StoreApp_user"."nombre", "StoreApp_user"."direccion", "StoreApp_user"."edad", "StoreApp_user"."email", "StoreApp_user"."tfno" FROM "StoreApp_user"'
```

**Ejemplo 2**

```sh
>>> Lista = User.objects.filter(edad=21)
```

**Ejemplo 3**

```sh
>>> Lista = User.objects.filter(edad=21, email="chavarria890lc@gmail.com")
```

**Ejemplo 4**

```sh
>>> Lista = User.objects.filter(edad__gte=22) # edad mas grande que 22
```

**Ejemplo 5**

```sh
>>> Lista = User.objects.filter(edad__gte=22)
```

**Ejemplo 6**

```sh
>>> Lista = User.objects.filter(edad__lte=22) # edad menor que 22
```

**Ejemplo 7**

```sh
>>> Lista = User.objects.filter(edad__range(10,20)) # edad entre 10 y 20
```

**Ejemplo 8**

```sh
>>> Lista = User.objects.filter(edad__gte=10).order_by("edad") # edad mayor que 10 y ordenada de menor a mayor
```

**Ejemplo 9**

```sh
>>> Lista = User.objects.filter(edad__gte=10).order_by("-edad") # edad mayor que 10 y ordenada de mayor a menor
```

---

## Panel de Administración

`python manane.py createsuperuser` crear super usuario admin.

> admin.py

```python
from django.contrib import admin
from StoreApp.models import *

# Register your models here.

admin.site.register(User)
admin.site.register(Article)
admin.site.register(Pedidos)
```

> Usar el método `__str__()` para darle una impresión al objeto personalizada

Modificar los atributos para que sea opcionales

```python
    email = models.EmailField(blank=True,null=True)
    tfno = models.CharField(blank=True,null=True,max_length=10)
```

Modificar el nombre del atributo en el panel


```python
tfno = models.CharField(blank=True,null=True,max_length=10,verbose_name="Telefono")
```

Como se modificaron los modelos toca hacer las migraciones con los comandos:

`python manage.py makemigrations`

`python manage.py migrate`

Agregar mas información al panel

> admin.py

```python
class UserAdmin(admin.ModelAdmin):
    
    list_display=("nombre","direccion","edad","tfno") # Muestra esta información en el panel
    search_fields=("nombre","tfno") # realiza busquedas por medio de estos parametros

admin.site.register(User, UserAdmin)
```

---

### Filtros

```python
from django.contrib import admin
from StoreApp.models import *

# Register your models here.

class UserAdmin(admin.ModelAdmin):
    
    list_display=("nombre","direccion","edad","tfno") # muestra esta información en el panel
    search_fields=("nombre","tfno") # realiza busquedas por medio de estos parametros
    
class ArticleAdmin(admin.ModelAdmin):
    
    list_filter = ("seccion","precio") # filtra resultados en el panel
    
class PedidosAdmin(admin.ModelAdmin):
    
    list_display = ("numero","fecha")
    list_filter = ("fecha",)
    date_hierarchy = "fecha" # Crea una seccion de menú para filtrar las fechas en el panel
    

admin.site.register(User, UserAdmin)
admin.site.register(Article, ArticleAdmin)
admin.site.register(Pedidos,PedidosAdmin)
```

### Cambiar idioma

dentro del modulo `settings.py` localizar `LANGUAGE_CODE`

y modificarlo a `LANGUAGE_CODE = 'es-us'`

---

## Formularios

[Request Object](https://docs.djangoproject.com/en/3.2/ref/request-response/ "Documentación Django")

**Ejemplo**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Main</title>
</head>
<body>
    <form action="/busca" method="">
    
        <input type="text" name="prd" id="">
        
        <input type="submit" value="Buscar">
    
    </form>
</body>
</html>
```

> viwes.py

```python
from django.shortcuts import render
from django.http import HttpResponse


def busqueda_productos(request):
    
    return render(request, "busqueda_productos.html")


def busca(request):
    
    mesanje = f"Articulo buscado: {request.GET['prd']}"
    
    return HttpResponse(mesanje)
```

> urls.py

```python
from django.contrib import admin
from django.urls import path
from Forms.views import busqueda_productos,busca

urlpatterns = [
    path('admin/', admin.site.urls),
    path('busqueda/', busqueda_productos),
    path('busca/', busca),
]
```

### Busqueda

> articulos_form.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Form Articulos</title>
</head>
<body>
    <form action="/articulos" method="">
    
        <input type="text" name="prd" required>
        
        <input type="submit" value="Buscar">
    
    </form>
</body>
</html>
```

> articulos.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Articulos</title>
</head>
<body>
    <h1>Tu articulos buscado es: {{articulo}}, una cantidad de {{articulo|length}}</h1>

    {% if articulos %}
        <ul>
            {% for i in articulos %}
                <li>{{i}}</li>
            {% endfor %}
        </ul>
    {% else %}
        <p>No hay ningun articulo de esos</p>
    {% endif %}
</body>
</html>
```

> views.py

```python
from django.shortcuts import render
from StoreApp.models import *

# Create your views here.

def Articulos_Formulario(request):
    
    return render(request, "articulos_form.html")

def Articulos(request):
    
    articulo = request.GET["prd"]
    
    articulos = Article.objects.filter(seccion__icontains="Consolas")
    
    return render(request, "articulos.html", {"articulo":articulo,"articulos":articulos})

```

### ~~EMAIL~~

> settings.py

al final

```python
# EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"

# EMAIL_HOST = "smtp.gmail.com"
# EMAIL_USE_TLS = True
# EMAIL_PORT = 587
# EMAIL_HOST_USER = "chavarria890lc@gmail.com"
# EMAIL_HOST_PASSWORD = "pass"
```

---

### API forms

[Documentación Api Forms](https://docs.djangoproject.com/en/3.2/ref/forms/api/)

