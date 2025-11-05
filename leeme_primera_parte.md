# ✈️ PROYECTO: AEROPUERTO  
**Lenguaje:** Python  
**Framework:** Django  
**Editor:** VS Code  

---

# 🧱 PASO 1 — PROCEDIMIENTO PARA CREAR CARPETA DEL PROYECTO: UIII_aeropuerto_0409  
Abrir una carpeta de trabajo en tu computadora y crear el proyecto principal:  
```bash
mkdir UIII_aeropuerto_0409
```

---

# 💻 PASO 2 — PROCEDIMIENTO PARA ABRIR VS CODE SOBRE LA CARPETA UIII_aeropuerto_0409  
Abrir la carpeta creada directamente desde VS Code:  
```bash
code UIII_aeropuerto_0409
```

---

# 🧭 PASO 3 — PROCEDIMIENTO PARA ABRIR TERMINAL EN VS CODE  
En VS Code, ir a:  
**Ver → Terminal**  
o presionar **Ctrl + `** (o Ctrl + ñ según distribución).

---

# 🐍 PASO 4 — PROCEDIMIENTO PARA CREAR CARPETA ENTORNO VIRTUAL “.venv” DESDE TERMINAL DE VS CODE  
En la terminal, ejecutar:  
```bash
python -m venv .venv
```

---

# ⚙️ PASO 5 — PROCEDIMIENTO PARA ACTIVAR EL ENTORNO VIRTUAL  
Windows (PowerShell):
```powershell
.venv\Scripts\Activate.ps1
```
Windows (cmd):
```cmd
.venv\Scripts\activate
```
Linux / macOS:
```bash
source .venv/bin/activate
```

---

# 🧩 PASO 6 — PROCEDIMIENTO PARA ACTIVAR INTÉRPRETE DE PYTHON  
En VS Code:  
- Presiona **Ctrl + Shift + P**  
- Escribe: `Python: Select Interpreter`  
- Selecciona el intérprete que corresponde a `.venv` (p. ej. `.venv\Scripts\python.exe`).

---

# 📦 PASO 7 — PROCEDIMIENTO PARA INSTALAR DJANGO  
En la terminal (con el entorno activado):  
```bash
pip install django
```

---

# 🏗️ PASO 8 — PROCEDIMIENTO PARA CREAR PROYECTO backend_aeropuerto SIN DUPLICAR CARPETA  
Desde la raíz `UIII_aeropuerto_0409` ejecutar:
```bash
django-admin startproject backend_aeropuerto .
```
> Nota: el `.` al final evita crear una carpeta extra.

---

# 🚀 PASO 9 — PROCEDIMIENTO PARA EJECUTAR SERVIDOR EN EL PUERTO 8036  
```bash
python manage.py runserver 8036
```

---

# 🌐 PASO 10 — PROCEDIMIENTO PARA COPIAR Y PEGAR EL LINK EN EL NAVEGADOR  
Copiar el enlace en la terminal, por ejemplo:  
```
http://127.0.0.1:8036/
```
Pegar en el navegador.

---

# 🧰 PASO 11 — PROCEDIMIENTO PARA CREAR APLICACIÓN app_aeropuerto  
```bash
python manage.py startapp app_aeropuerto
```

---

# 🧠 PASO 12 — AQUI EL MODELO models.py  
Archivo: `app_aeropuerto/models.py` (ya provisto — incluir si no está):

```python
from django.db import models

# ==========================================
# MODELO: AVION
# ==========================================
class Avion(models.Model):
    matricula = models.CharField(max_length=20, unique=True)
    modelo = models.CharField(max_length=50)
    fabricante = models.CharField(max_length=50)
    capacidad = models.PositiveIntegerField()
    anio_fabricacion = models.PositiveIntegerField()
    tipo = models.CharField(max_length=30)
    estado = models.CharField(max_length=30)

    def __str__(self):
        return f"{self.matricula} - {self.modelo}"

# ==========================================
# MODELO: VUELO
# ==========================================
class Vuelo(models.Model):
    codigo_vuelo = models.CharField(max_length=20, unique=True)
    origen = models.CharField(max_length=50)
    destino = models.CharField(max_length=50)
    fecha_salida = models.DateTimeField()
    fecha_llegada = models.DateTimeField()
    duracion_horas = models.DecimalField(max_digits=5, decimal_places=2)
    estatus = models.CharField(max_length=30)

    avion = models.ForeignKey(Avion, on_delete=models.CASCADE, related_name="vuelos")

    def __str__(self):
        return f"{self.codigo_vuelo} ({self.origen} → {self.destino})"

# ==========================================
# MODELO: EMPLEADO
# ==========================================
class Empleado(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    cargo = models.CharField(max_length=50)
    salario = models.DecimalField(max_digits=10, decimal_places=2)
    fecha_contratacion = models.DateField()
    licencia = models.CharField(max_length=30)
    turno = models.CharField(max_length=20)

    aviones = models.ManyToManyField(Avion, related_name="empleados")

    def __str__(self):
        return f"{self.nombre} {self.apellido}"
```

---

# 🧩 PASO 12.5 — PROCEDIMIENTO PARA REALIZAR LAS MIGRACIONES (makemigrations Y migrate)  
```bash
python manage.py makemigrations
python manage.py migrate
```

---

# ✈️ PASO 13 — PRIMERO TRABAJAMOS CON EL MODELO: AVION  
Por ahora implementaremos CRUD para `Avion` y dejaremos `Vuelo` y `Empleado` pendientes.

---

# 🧮 PASO 14 — EN views.py CREAR LAS FUNCIONES CORRESPONDIENTES  
Archivo: `app_aeropuerto/views.py` — contenido completo listo:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Avion
from django.urls import reverse

# VISTA: INICIO SISTEMA
def inicio_aeropuerto(request):
    return render(request, 'inicio.html')

# VISTA: AGREGAR AVION (GET -> FORM; POST -> CREAR)
def agregar_avion(request):
    if request.method == 'POST':
        # SIN VALIDACIÓN (según indicación)
        matricula = request.POST.get('matricula')
        modelo = request.POST.get('modelo')
        fabricante = request.POST.get('fabricante')
        capacidad = request.POST.get('capacidad') or 0
        anio_fabricacion = request.POST.get('anio_fabricacion') or 0
        tipo = request.POST.get('tipo')
        estado = request.POST.get('estado')

        Avion.objects.create(
            matricula=matricula,
            modelo=modelo,
            fabricante=fabricante,
            capacidad=int(capacidad),
            anio_fabricacion=int(anio_fabricacion),
            tipo=tipo,
            estado=estado
        )
        return redirect('ver_aviones')
    return render(request, 'avion/agregar_avion.html')

# VISTA: VER LISTA DE AVIONES
def ver_aviones(request):
    aviones = Avion.objects.all()
    return render(request, 'avion/ver_aviones.html', {'aviones': aviones})

# VISTA: MOSTRAR FORMULARIO DE ACTUALIZACION (GET)
def actualizar_avion(request, avion_id):
    avion = get_object_or_404(Avion, id=avion_id)
    return render(request, 'avion/actualizar_avion.html', {'avion': avion})

# VISTA: REALIZAR ACTUALIZACION (POST)
def realizar_actualizacion_avion(request, avion_id):
    avion = get_object_or_404(Avion, id=avion_id)
    if request.method == 'POST':
        avion.matricula = request.POST.get('matricula')
        avion.modelo = request.POST.get('modelo')
        avion.fabricante = request.POST.get('fabricante')
        avion.capacidad = int(request.POST.get('capacidad') or 0)
        avion.anio_fabricacion = int(request.POST.get('anio_fabricacion') or 0)
        avion.tipo = request.POST.get('tipo')
        avion.estado = request.POST.get('estado')
        avion.save()
        return redirect('ver_aviones')
    return redirect('ver_aviones')

# VISTA: BORRAR AVION (CONFIRMACION GET -> BORRAR POST)
def borrar_avion(request, avion_id):
    avion = get_object_or_404(Avion, id=avion_id)
    if request.method == 'POST':
        avion.delete()
        return redirect('ver_aviones')
    return render(request, 'avion/borrar_avion.html', {'avion': avion})
```

---

# 🗂️ PASO 15 — CREAR LA CARPETA “templates” DENTRO DE app_aeropuerto  
Ruta:  
```
app_aeropuerto/templates/
```

---

# 🧾 PASO 16 — EN LA CARPETA templates CREAR LOS ARCHIVOS HTML (base, header, navbar, footer, inicio)  
A continuación están los archivos completos.

**Archivo:** `app_aeropuerto/templates/base.html`
```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Sistema Administración Aeropuerto</title>
    <!-- Bootstrap CSS CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
      body { background-color: #f7fbff; }
      .footer { position: fixed; bottom: 0; width: 100%; }
      .card-soft { border-radius: 12px; box-shadow: 0 3px 10px rgba(0,0,0,0.04); }
    </style>
  </head>
  <body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}

    <main class="container my-4">
      {% block content %}{% endblock %}
    </main>

    {% include 'footer.html' %}

    <!-- Bootstrap JS CDN -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  </body>
</html>
```

**Archivo:** `app_aeropuerto/templates/header.html`
```html
<header class="bg-white shadow-sm py-3">
  <div class="container d-flex align-items-center gap-3">
    <span class="fs-3">✈️</span>
    <div>
      <h1 class="h5 mb-0">SISTEMA DE ADMINISTRACIÓN AEROPUERTO</h1>
      <small class="text-muted">UIII_aeropuerto_0409</small>
    </div>
  </div>
</header>
```

**Archivo:** `app_aeropuerto/templates/navbar.html`
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio' %}">🛩️ Aeropuerto</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="nav-link" href="{% url 'inicio' %}">🏠 Inicio</a>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">✈️ Avión</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_avion' %}">Agregar avión</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_aviones' %}">Ver aviones</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">🛫 Vuelos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar vuelos</a></li>
            <li><a class="dropdown-item" href="#">Ver vuelos</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">👷 Empleados</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar empleados</a></li>
            <li><a class="dropdown-item" href="#">Ver empleados</a></li>
          </ul>
        </li>
      </ul>
      <span class="navbar-text text-muted">Creado por Ing. Bryan Villalobos</span>
    </div>
  </div>
</nav>
```

**Archivo:** `app_aeropuerto/templates/footer.html`
```html
<footer class="footer bg-white border-top py-2">
  <div class="container d-flex justify-content-between">
    <small>© {{ now.year }} Creado por Ing. Bryan Villalobos, Cbtis 128</small>
    <small class="text-muted">Sistema Aeropuerto</small>
  </div>
</footer>
```

**Archivo:** `app_aeropuerto/templates/inicio.html`
```html
{% extends 'base.html' %}
{% block content %}
<div class="card card-soft p-4">
  <div class="row">
    <div class="col-md-8">
      <h2>Bienvenido al Sistema de Administración Aeropuerto</h2>
      <p>Este sistema permite gestionar aviones (CRUD). Funciona con Django y está pensado como proyecto educativo.</p>
    </div>
    <div class="col-md-4">
      <img src="https://images.unsplash.com/photo-1542314831-068cd1dbfeeb" alt="Aeropuerto" class="img-fluid rounded">
    </div>
  </div>
</div>
{% endblock %}
```

---

# 🎨 PASO 17 — EN base.html AGREGAR BOOTSTRAP PARA CSS Y JS  
(Ya incluido en `base.html` anterior con CDN).

---

# 🧭 PASO 18 — EN navbar.html INCLUIR OPCIONES Y SUBMENÚS INDICADOS  
(Ya incluido en `navbar.html` con íconos en opciones principales).

---

# 🕓 PASO 19 — EN footer.html INCLUIR DERECHOS DE AUTOR Y CRÉDITOS  
(Ya incluido en `footer.html`; usa `{{ now.year }}` — para que funcione añade `django.template.context_processors.request` y/o usa `from django.utils import timezone` si prefieres. Si no, reemplaza con año fijo.)

---

# 🖼️ PASO 20 — EN inicio.html COLOCAR INFORMACIÓN DEL SISTEMA E IMAGEN DE AEROPUERTO  
(Ya incluido en `inicio.html` con imagen desde Unsplash).

---

# 🗃️ PASO 21 — CREAR SUBCARPETA avion DENTRO DE templates  
Ruta:
```
app_aeropuerto/templates/avion/
```

---

# 🧱 PASO 22 — CREAR LOS ARCHIVOS HTML PARA CRUD DE AVION  
A continuación los archivos dentro de `app_aeropuerto/templates/avion/`.

**Archivo:** `app_aeropuerto/templates/avion/agregar_avion.html`
```html
{% extends 'base.html' %}
{% block content %}
<div class="card p-4">
  <h3>➕ AGREGAR AVIÓN</h3>
  <form method="post">
    {% csrf_token %}
    <div class="mb-3">
      <label class="form-label">Matrícula</label>
      <input class="form-control" name="matricula" required>
    </div>
    <div class="mb-3">
      <label class="form-label">Modelo</label>
      <input class="form-control" name="modelo">
    </div>
    <div class="mb-3">
      <label class="form-label">Fabricante</label>
      <input class="form-control" name="fabricante">
    </div>
    <div class="mb-3">
      <label class="form-label">Capacidad</label>
      <input class="form-control" name="capacidad" type="number">
    </div>
    <div class="mb-3">
      <label class="form-label">Año de fabricación</label>
      <input class="form-control" name="anio_fabricacion" type="number">
    </div>
    <div class="mb-3">
      <label class="form-label">Tipo</label>
      <input class="form-control" name="tipo">
    </div>
    <div class="mb-3">
      <label class="form-label">Estado</label>
      <input class="form-control" name="estado">
    </div>
    <button class="btn btn-primary" type="submit">Guardar</button>
    <a class="btn btn-secondary" href="{% url 'ver_aviones' %}">Cancelar</a>
  </form>
</div>
{% endblock %}
```

**Archivo:** `app_aeropuerto/templates/avion/ver_aviones.html`
```html
{% extends 'base.html' %}
{% block content %}
<div class="card p-4">
  <div class="d-flex justify-content-between align-items-center mb-3">
    <h3>📋 LISTA DE AVIONES</h3>
    <a class="btn btn-success" href="{% url 'agregar_avion' %}">Agregar Avión</a>
  </div>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>Matrícula</th>
        <th>Modelo</th>
        <th>Fabricante</th>
        <th>Capacidad</th>
        <th>Año</th>
        <th>Tipo</th>
        <th>Estado</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      {% for avion in aviones %}
      <tr>
        <td>{{ forloop.counter }}</td>
        <td>{{ avion.matricula }}</td>
        <td>{{ avion.modelo }}</td>
        <td>{{ avion.fabricante }}</td>
        <td>{{ avion.capacidad }}</td>
        <td>{{ avion.anio_fabricacion }}</td>
        <td>{{ avion.tipo }}</td>
        <td>{{ avion.estado }}</td>
        <td>
          <a class="btn btn-sm btn-info" href="{% url 'actualizar_avion' avion.id %}">Editar</a>
          <a class="btn btn-sm btn-danger" href="{% url 'borrar_avion' avion.id %}">Borrar</a>
        </td>
      </tr>
      {% empty %}
      <tr><td colspan="9">No hay aviones registrados.</td></tr>
      {% endfor %}
    </tbody>
  </table>
</div>
{% endblock %}
```

**Archivo:** `app_aeropuerto/templates/avion/actualizar_avion.html`
```html
{% extends 'base.html' %}
{% block content %}
<div class="card p-4">
  <h3>✏️ ACTUALIZAR AVIÓN</h3>
  <form method="post" action="{% url 'realizar_actualizacion_avion' avion.id %}">
    {% csrf_token %}
    <div class="mb-3">
      <label class="form-label">Matrícula</label>
      <input class="form-control" name="matricula" value="{{ avion.matricula }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Modelo</label>
      <input class="form-control" name="modelo" value="{{ avion.modelo }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Fabricante</label>
      <input class="form-control" name="fabricante" value="{{ avion.fabricante }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Capacidad</label>
      <input class="form-control" name="capacidad" type="number" value="{{ avion.capacidad }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Año de fabricación</label>
      <input class="form-control" name="anio_fabricacion" type="number" value="{{ avion.anio_fabricacion }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Tipo</label>
      <input class="form-control" name="tipo" value="{{ avion.tipo }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Estado</label>
      <input class="form-control" name="estado" value="{{ avion.estado }}">
    </div>
    <button class="btn btn-primary" type="submit">Guardar Cambios</button>
    <a class="btn btn-secondary" href="{% url 'ver_aviones' %}">Cancelar</a>
  </form>
</div>
{% endblock %}
```

**Archivo:** `app_aeropuerto/templates/avion/borrar_avion.html`
```html
{% extends 'base.html' %}
{% block content %}
<div class="card p-4">
  <h3>🗑️ BORRAR AVIÓN</h3>
  <p>¿Deseas borrar el avión <strong>{{ avion.matricula }} - {{ avion.modelo }}</strong>?</p>
  <form method="post">
    {% csrf_token %}
    <button class="btn btn-danger" type="submit">Sí, borrar</button>
    <a class="btn btn-secondary" href="{% url 'ver_aviones' %}">No, cancelar</a>
  </form>
</div>
{% endblock %}
```

---

# 🚫 PASO 23 — NO UTILIZAR forms.py  
(se usan formularios HTML simples en los templates).

---

# 🔗 PASO 24 — CREAR EL ARCHIVO urls.py EN app_aeropuerto  
Archivo: `app_aeropuerto/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_aeropuerto, name='inicio'),
    path('avion/agregar/', views.agregar_avion, name='agregar_avion'),
    path('aviones/', views.ver_aviones, name='ver_aviones'),
    path('avion/actualizar/<int:avion_id>/', views.actualizar_avion, name='actualizar_avion'),
    path('avion/actualizar/realizar/<int:avion_id>/', views.realizar_actualizacion_avion, name='realizar_actualizacion_avion'),
    path('avion/borrar/<int:avion_id>/', views.borrar_avion, name='borrar_avion'),
]
```

---

# ⚙️ PASO 25 — AGREGAR app_aeropuerto EN settings.py DE backend_aeropuerto  
En `backend_aeropuerto/settings.py`, dentro de `INSTALLED_APPS` añade:
```python
INSTALLED_APPS = [
    # ...
    'app_aeropuerto',
]
```

---

# 🌍 PASO 26 — CONFIGURAR urls.py DE backend_aeropuerto PARA ENLAZAR CON app_aeropuerto  
Archivo: `backend_aeropuerto/urls.py` (completo):

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_aeropuerto.urls')),
]
```

---

# 🧾 PASO 27 — REGISTRAR LOS MODELOS EN admin.py Y VOLVER A REALIZAR LAS MIGRACIONES  
Archivo: `app_aeropuerto/admin.py`:

```python
from django.contrib import admin
from .models import Avion, Vuelo, Empleado

admin.site.register(Avion)
admin.site.register(Vuelo)
admin.site.register(Empleado)
```

Ejecutar:
```bash
python manage.py makemigrations
python manage.py migrate
```

---

# ✈️ PASO 27 (NOTA) — SOLO TRABAJAR CON AVION  
Dejar `Vuelo` y `Empleado` pendientes para más adelante.

---

# 🎨 PASO 28 — UTILIZAR COLORES SUAVES, ATRACTIVOS Y MODERNOS  
(El CSS en `base.html` aplica tonos suaves; usa Bootstrap para mantener diseño sencillo).

---

# 🧮 PASO 28 (NOTA) — NO VALIDAR ENTRADA DE DATOS  
(No se implementaron validaciones).

---

# 🧱 PASO 29 — AL INICIO CREAR LA ESTRUCTURA COMPLETA DE CARPETAS Y ARCHIVOS  
(Crear todas las carpetas y archivos provistos arriba desde el principio).

---

# ✅ PASO 30 — PROYECTO TOTALMENTE FUNCIONAL  
Verifica:
1. `python manage.py runserver 8036`
2. Abrir `http://127.0.0.1:8036/`
3. Ir a "Avión → Agregar avión", crear, luego "Ver aviones", editar y borrar.

---

# 🚀 PASO 31 — FINALMENTE EJECUTAR SERVIDOR EN EL PUERTO 8036  
```bash
python manage.py runserver 8036
```
Abrir en navegador:
```
http://127.0.0.1:8036/
```

---

# 📁 ESTRUCTURA FINAL DEL PROYECTO (INCLUYE TODOS LOS ARCHIVOS Y HTML SOLICITADOS)

```
UIII_aeropuerto_0409/
│
├── .venv/
│
├── backend_aeropuerto/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
│
├── app_aeropuerto/
│   ├── migrations/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   ├── views.py
│   └── templates/
│       ├── base.html
│       ├── header.html
│       ├── navbar.html
│       ├── footer.html
│       ├── inicio.html
│       └── avion/
│           ├── agregar_avion.html
│           ├── ver_aviones.html
│           ├── actualizar_avion.html
│           └── borrar_avion.html
│
├── manage.py
└── db.sqlite3
```

---
