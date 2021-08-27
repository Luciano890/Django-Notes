# Django-Notes
Notas del Framework Django

# Menú

- [Iniciar Proyecto](#iniciar-proyecto)
  - [Activa el Proyecto](#activa-el-proyecto)
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

## Aplicación

> _Nota:Todos los modelos (tablas de BBDD) tienen que estar en una aplicación_

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
    
class Pedidos(models.Model):
    
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

