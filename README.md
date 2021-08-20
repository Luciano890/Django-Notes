# Django-Notes
Notas del Framework Django

# Menú

- [Iniciar Proyecto](#iniciar-proyecto)
  - [Activa el Proyecto](#activa-proyecto)
  - [Correr Servidor](#correr-servidor)

- [MTV](#model-template-view)
  - [Vista](#vista) 
  - [Vista con parametros](#vista-parametros) 

- [Plantillas](#plantilla)
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
