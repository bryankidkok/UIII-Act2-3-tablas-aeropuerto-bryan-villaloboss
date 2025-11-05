Paso 1 — ¿Procedimiento para crear carpeta del Proyecto UIII_aeropuerto_0409?
mkdir UIII_aeropuerto_0409
cd UIII_aeropuerto_0409

Paso 2 — ¿Procedimiento para abrir VS Code sobre la carpeta UIII_aeropuerto_0409?
# desde la terminal estando en la carpeta:
code .
# o abrir VS Code y usar File > Open Folder > seleccionar UIII_aeropuerto_0409

Paso 3 — ¿Procedimiento para abrir la terminal en VS Code?
# En VS Code: Menu -> Terminal -> New Terminal
# O usar el atajo: Ctrl + `  (control + backtick)

Paso 4 — ¿Procedimiento para crear carpeta entorno virtual “.venv” desde la terminal de VS Code?
python -m venv .venv
# (esto crea la carpeta oculta .venv en el proyecto)

Paso 5 — ¿Procedimiento para activar el entorno virtual?
# Windows (cmd)
.venv\Scripts\activate

# Windows (PowerShell)
.\.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate

Paso 6 — ¿Procedimiento para activar el intérprete de Python (seleccionar intérprete) en VS Code?
# En VS Code: Ctrl+Shift+P -> "Python: Select Interpreter" -> elegir el intérprete ubicado en ./UIII_aeropuerto_0409/.venv/...
# (seleccionar el Python dentro de .venv para que VS Code use el entorno)

Paso 7 — ¿Procedimiento para instalar Django?
pip install django
# (opcional: pip freeze > requirements.txt después para registrar dependencias)
pip freeze > requirements.txt

Paso 8 — ¿Procedimiento para crear proyecto backend_aeropuerto sin duplicar carpeta?
# ejecutar desde la raíz UIII_aeropuerto_0409
django-admin startproject backend_aeropuerto .
# El punto final evita crear una carpeta anidada y coloca el proyecto en la carpeta actual.

Paso 9 — ¿Procedimiento para ejecutar servidor en el puerto 8036?
python manage.py runserver 8036
# Abre http://127.0.0.1:8036/ en el navegador (o http://localhost:8036/)

Paso 10 — ¿Procedimiento para copiar y pegar el link en el navegador?
# Cuando ejecutes runserver verás:
# "Starting development server at http://127.0.0.1:8036/"
# Copia esa URL y pégala en tu navegador: http://127.0.0.1:8036/

Paso 11 — ¿Procedimiento para crear aplicación app_aeropuerto?
python manage.py startapp app_aeropuerto

Paso 12 — ¿Aquí el modelo models.py? (poner el código exacto)

Archivo: app_aeropuerto/models.py

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

    # Relación 1 a muchos → un avión puede tener varios vuelos
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

    # Relación muchos a muchos → un empleado puede trabajar en varios aviones y viceversa
    aviones = models.ManyToManyField(Avion, related_name="empleados")

    def __str__(self):
        return f"{self.nombre} {self.apellido}"

Paso 12.5 — ¿Procedimiento para realizar las migraciones (makemigrations y migrate)?
# Crear migraciones para la app
python manage.py makemigrations app_aeropuerto

# Aplicar las migraciones a la base de datos
python manage.py migrate

Paso 13 — ¿Primero trabajamos con el MODELO: AVION?
# Confirmación: las siguientes vistas, templates y urls se implementarán primero para el modelo Avion.

Paso 14 — ¿En views.py de app_aeropuerto crear las funciones con sus códigos correspondientes (inicio_aeropuerto, agregar_avion, actualizar_avion, realizar_actualizacion_avion, borrar_avion)?

Archivo: app_aeropuerto/views.py

from django.shortcuts import render, redirect, get_object_or_404
from .models import Avion
from django.utils import timezone

def inicio_aeropuerto(request):
    # muestra la página de inicio
    return render(request, "inicio.html", {"hoy": timezone.now()})

def agregar_avion(request):
    # muestra formulario para agregar; también puede crear si POST
    if request.method == "POST":
        Avion.objects.create(
            matricula=request.POST.get("matricula", "").strip(),
            modelo=request.POST.get("modelo", "").strip(),
            fabricante=request.POST.get("fabricante", "").strip(),
            capacidad=int(request.POST.get("capacidad") or 0),
            anio_fabricacion=int(request.POST.get("anio_fabricacion") or 0),
            tipo=request.POST.get("tipo", "").strip(),
            estado=request.POST.get("estado", "").strip()
        )
        return redirect("ver_aviones")
    return render(request, "avion/agregar_avion.html")

def ver_aviones(request):
    aviones = Avion.objects.all().order_by("matricula")
    return render(request, "avion/ver_aviones.html", {"aviones": aviones})

def actualizar_avion(request, avion_id):
    avion = get_object_or_404(Avion, pk=avion_id)
    return render(request, "avion/actualizar_avion.html", {"avion": avion})

def realizar_actualizacion_avion(request):
    # Guardar nuevo o actualizar si viene id en POST
    if request.method == "POST":
        avion_id = request.POST.get("id")
        matricula = request.POST.get("matricula", "").strip()
        modelo = request.POST.get("modelo", "").strip()
        fabricante = request.POST.get("fabricante", "").strip()
        capacidad = int(request.POST.get("capacidad") or 0)
        anio_fabricacion = int(request.POST.get("anio_fabricacion") or 0)
        tipo = request.POST.get("tipo", "").strip()
        estado = request.POST.get("estado", "").strip()

        if avion_id:
            avion = get_object_or_404(Avion, pk=avion_id)
            avion.matricula = matricula
            avion.modelo = modelo
            avion.fabricante = fabricante
            avion.capacidad = capacidad
            avion.anio_fabricacion = anio_fabricacion
            avion.tipo = tipo
            avion.estado = estado
            avion.save()
        else:
            Avion.objects.create(
                matricula=matricula,
                modelo=modelo,
                fabricante=fabricante,
                capacidad=capacidad,
                anio_fabricacion=anio_fabricacion,
                tipo=tipo,
                estado=estado
            )
    return redirect("ver_aviones")

def borrar_avion(request, avion_id):
    avion = get_object_or_404(Avion, pk=avion_id)
    if request.method == "POST":
        avion.delete()
        return redirect("ver_aviones")
    return render(request, "avion/borrar_avion.html", {"avion": avion})

Paso 15 — ¿Crear la carpeta templates dentro de app_aeropuerto?
# Crear carpeta templates y subcarpetas necesarias
mkdir -p app_aeropuerto/templates/avion
# (en Windows PowerShell usar New-Item o crear desde el explorador)

Paso 16 — ¿En la carpeta templates crear los archivos HTML (base.html, header.html, navbar.html, footer.html, inicio.html)?
# Crear archivos:
# app_aeropuerto/templates/base.html
# app_aeropuerto/templates/header.html
# app_aeropuerto/templates/navbar.html
# app_aeropuerto/templates/footer.html
# app_aeropuerto/templates/inicio.html
# (A continuación tienes el contenido recomendado para cada uno)


app_aeropuerto/templates/base.html

<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{% block title %}Sistema Aeropuerto{% endblock %}</title>

    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.css" rel="stylesheet">

    <style>
      body { background: #f6f9fc; color: #243447; padding-bottom: 80px; }
      .navbar-brand { font-weight: 600; }
      footer { position: fixed; bottom: 0; left: 0; right: 0; height: 60px; background: #ffffff; border-top: 1px solid #e2e8f0; display:flex; align-items:center; justify-content:center; }
      .card { border-radius: 12px; box-shadow: 0 2px 8px rgba(36,52,71,0.06); }
    </style>

    {% block extra_head %}{% endblock %}
  </head>
  <body>
    {% include "header.html" %}
    {% include "navbar.html" %}

    <main class="container my-4">
      {% block content %}{% endblock %}
    </main>

    {% include "footer.html" %}

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
    {% block extra_js %}{% endblock %}
  </body>
</html>


app_aeropuerto/templates/header.html

<header class="bg-white py-3 shadow-sm">
  <div class="container">
    <h4 class="m-0">Sistema de Administración Aeropuerto</h4>
  </div>
</header>


app_aeropuerto/templates/navbar.html

<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio_aeropuerto' %}">
      <i class="bi bi-airplane-fill"></i> Sistema de Administración Aeropuerto
    </a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio_aeropuerto' %}">Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
            <i class="bi bi-box-seam"></i> Avion
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_avion' %}">Agregar avion</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_aviones' %}">Ver aviones</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
            <i class="bi bi-cloud-sun"></i> Vuelos
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar vuelos</a></li>
            <li><a class="dropdown-item" href="#">Ver vuelos</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
            <i class="bi bi-people-fill"></i> Empleados
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar empleados</a></li>
            <li><a class="dropdown-item" href="#">Ver empleados</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>


app_aeropuerto/templates/footer.html

<footer>
  <div class="container text-center small text-muted">
    &copy; {{ hoy|date:"Y" }} Derechos reservados — Creado por Ing. Bryan Villalobos, Cbtis 128
  </div>
</footer>


app_aeropuerto/templates/inicio.html

{% extends "base.html" %}
{% block title %}Inicio - Sistema Aeropuerto{% endblock %}
{% block content %}
<div class="card p-4">
  <h2>Bienvenido al Sistema de Administración Aeropuerto</h2>
  <p>Gestiona aviones, vuelos y empleados de manera sencilla.</p>

  <div class="row">
    <div class="col-md-7">
      <p>Este sistema permite realizar CRUD para aviones (actualmente funcional). Diseño con colores suaves y moderno.</p>
    </div>
    <div class="col-md-5">
      <img src="https://images.unsplash.com/photo-1511910849309-1b4dd0f8d9f9?w=1200&q=80&auto=format&fit=crop" alt="Aeropuerto" class="img-fluid rounded">
    </div>
  </div>
</div>
{% endblock %}

Paso 17 — ¿En el archivo base.html agregar bootstrap para css y js?
# Ya incluido en base.html (líneas con CDN):
# <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
# <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
# y Bootstrap Icons CDN también incluido.

Paso 18 — ¿En navbar.html incluir las opciones y submenus con iconos en las opciones principales (no en submenus)?
# Ya implementado en templates/navbar.html: Avion (submenu Agregar/Ver), Vuelos (submenu), Empleados (submenu).
# Íconos principales: bi-airplane-fill, bi-cloud-sun, bi-people-fill (usando bootstrap-icons).

Paso 19 — ¿En footer.html incluir derechos de autor, fecha del sistema y "Creado por Ing. Bryan Villalobos, Cbtis 128" y mantenerla fija al final de la página?
<footer>
  <div class="container text-center small text-muted">
    &copy; {{ hoy|date:"Y" }} Derechos reservados — Creado por Ing. Bryan Villalobos, Cbtis 128
  </div>
</footer>
# Nota: la variable "hoy" se pasa desde la vista inicio_aeropuerto o usar {% now "Y" %} si prefieres.

Paso 20 — ¿En inicio.html usar para colocar información del sistema más una imagen tomada desde la red sobre aeropuerto?
# Contenido ya incluido en app_aeropuerto/templates/inicio.html (ver Paso 16).
# Usa una imagen pública (Unsplash) en la etiqueta <img>.

Paso 21 — ¿Crear la subcarpeta avion dentro de app_aeropuerto/templates?
mkdir -p app_aeropuerto/templates/avion

Paso 22 — ¿Crear los archivos HTML con su código correspondientes (agregar_avion.html, ver_aviones.html, actualizar_avion.html, borrar_avion.html) dentro de app_aeropuerto/templates/avion?

app_aeropuerto/templates/avion/agregar_avion.html

{% extends "base.html" %}
{% block title %}Agregar Avión{% endblock %}
{% block content %}
<div class="card p-4">
  <h3>Agregar Avión</h3>
  <form method="post" action="{% url 'realizar_actualizacion_avion' %}">
    {% csrf_token %}
    <div class="mb-3"><label class="form-label">Matrícula</label><input name="matricula" class="form-control" required></div>
    <div class="mb-3"><label class="form-label">Modelo</label><input name="modelo" class="form-control"></div>
    <div class="mb-3"><label class="form-label">Fabricante</label><input name="fabricante" class="form-control"></div>
    <div class="mb-3"><label class="form-label">Capacidad</label><input type="number" name="capacidad" class="form-control"></div>
    <div class="mb-3"><label class="form-label">Año de fabricación</label><input type="number" name="anio_fabricacion" class="form-control"></div>
    <div class="mb-3"><label class="form-label">Tipo</label><input name="tipo" class="form-control"></div>
    <div class="mb-3"><label class="form-label">Estado</label><input name="estado" class="form-control"></div>
    <button class="btn btn-primary">Guardar</button>
    <a href="{% url 'ver_aviones' %}" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
{% endblock %}


app_aeropuerto/templates/avion/ver_aviones.html

{% extends "base.html" %}
{% block title %}Ver Aviones{% endblock %}
{% block content %}
<div class="card p-4">
  <h3>Lista de Aviones</h3>
  <a class="btn btn-success mb-3" href="{% url 'agregar_avion' %}"><i class="bi bi-plus-lg"></i> Agregar</a>
  <div class="table-responsive">
    <table class="table table-striped">
      <thead>
        <tr>
          <th>Matrícula</th><th>Modelo</th><th>Fabricante</th><th>Capacidad</th><th>Año</th><th>Tipo</th><th>Estado</th><th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        {% for a in aviones %}
        <tr>
          <td>{{ a.matricula }}</td>
          <td>{{ a.modelo }}</td>
          <td>{{ a.fabricante }}</td>
          <td>{{ a.capacidad }}</td>
          <td>{{ a.anio_fabricacion }}</td>
          <td>{{ a.tipo }}</td>
          <td>{{ a.estado }}</td>
          <td>
            <a class="btn btn-sm btn-info" href="{% url 'actualizar_avion' a.id %}">Editar</a>
            <a class="btn btn-sm btn-danger" href="{% url 'borrar_avion' a.id %}">Borrar</a>
          </td>
        </tr>
        {% empty %}
        <tr><td colspan="8">No hay aviones registrados.</td></tr>
        {% endfor %}
      </tbody>
    </table>
  </div>
</div>
{% endblock %}


app_aeropuerto/templates/avion/actualizar_avion.html

{% extends "base.html" %}
{% block title %}Actualizar Avión{% endblock %}
{% block content %}
<div class="card p-4">
  <h3>Actualizar Avión</h3>
  <form method="post" action="{% url 'realizar_actualizacion_avion' %}">
    {% csrf_token %}
    <input type="hidden" name="id" value="{{ avion.id }}">
    <div class="mb-3"><label>Matrícula</label><input name="matricula" class="form-control" value="{{ avion.matricula }}"></div>
    <div class="mb-3"><label>Modelo</label><input name="modelo" class="form-control" value="{{ avion.modelo }}"></div>
    <div class="mb-3"><label>Fabricante</label><input name="fabricante" class="form-control" value="{{ avion.fabricante }}"></div>
    <div class="mb-3"><label>Capacidad</label><input type="number" name="capacidad" class="form-control" value="{{ avion.capacidad }}"></div>
    <div class="mb-3"><label>Año de fabricación</label><input type="number" name="anio_fabricacion" class="form-control" value="{{ avion.anio_fabricacion }}"></div>
    <div class="mb-3"><label>Tipo</label><input name="tipo" class="form-control" value="{{ avion.tipo }}"></div>
    <div class="mb-3"><label>Estado</label><input name="estado" class="form-control" value="{{ avion.estado }}"></div>
    <button class="btn btn-primary">Guardar cambios</button>
    <a href="{% url 'ver_aviones' %}" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
{% endblock %}


app_aeropuerto/templates/avion/borrar_avion.html

{% extends "base.html" %}
{% block title %}Borrar Avión{% endblock %}
{% block content %}
<div class="card p-4">
  <h3>Confirmar borrado</h3>
  <p>¿Eliminar el avión <strong>{{ avion.matricula }} - {{ avion.modelo }}</strong>?</p>
  <form method="post">
    {% csrf_token %}
    <button type="submit" class="btn btn-danger">Borrar</button>
    <a href="{% url 'ver_aviones' %}" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
{% endblock %}

Paso 23 — ¿No utilizar forms.py?
# Confirmado: el proyecto utiliza formularios HTML y request.POST directo, no se crea app_aeropuerto/forms.py.

Paso 24 — ¿Procedimiento para crear el archivo urls.py en app_aeropuerto con el código correspondiente para acceder a las funciones de views.py para operaciones CRUD en aviones?

Archivo: app_aeropuerto/urls.py

from django.urls import path
from . import views

urlpatterns = [
    path("", views.inicio_aeropuerto, name="inicio_aeropuerto"),
    path("avion/agregar/", views.agregar_avion, name="agregar_avion"),
    path("avion/guardar/", views.realizar_actualizacion_avion, name="realizar_actualizacion_avion"),
    path("avion/lista/", views.ver_aviones, name="ver_aviones"),
    path("avion/editar/<int:avion_id>/", views.actualizar_avion, name="actualizar_avion"),
    path("avion/borrar/<int:avion_id>/", views.borrar_avion, name="borrar_avion"),
]

Paso 25 — ¿Procedimiento para agregar app_aeropuerto en settings.py de backend_aeropuerto?

Archivo: backend_aeropuerto/settings.py → editar INSTALLED_APPS:

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_aeropuerto',  # agregar aquí
]


(opcional: configurar TEMPLATES[0]['DIRS'] si quieres templates globales)

Paso 26 — ¿Realizar las configuraciones correspondientes a urls.py de backend_aeropuerto para enlazar con app_aeropuerto?

Archivo: backend_aeropuerto/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_aeropuerto.urls')),  # enruta la app como raíz
]

Paso 27 — ¿Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones?

Archivo: app_aeropuerto/admin.py

from django.contrib import admin
from .models import Avion, Vuelo, Empleado

@admin.register(Avion)
class AvionAdmin(admin.ModelAdmin):
    list_display = ("matricula", "modelo", "fabricante", "capacidad", "anio_fabricacion", "tipo", "estado")
    search_fields = ("matricula", "modelo", "fabricante")

admin.site.register(Vuelo)
admin.site.register(Empleado)


Luego en la terminal:

python manage.py makemigrations app_aeropuerto
python manage.py migrate

Paso 27 (nota) — ¿Por lo pronto sólo trabajar con “avion”; dejar pendiente MODELO: VUELO y MODELO: EMPLEADO?
# Implementación práctica: la UI y CRUD se hacen sólo para Avion. Vuelo y Empleado quedan definidos en models.py pero su CRUD queda para más adelante.

Paso 28 — ¿Utilizar colores suaves, atractivos y modernos; el código de las páginas web sencillas?
# Estilizado aplicado en base.html (colores suaves, cards, footer fijo). Opcional crear app_aeropuerto/static/css/styles.css y enlazarlo desde base.html.

Paso 28 (otra nota) — ¿No validar entrada de datos?
# Confirmado: no se realizan validaciones en el backend ni JS; los formularios son simples y guardan lo que se envía en request.POST.

Paso 29 — ¿Al inicio crear la estructura completa de carpetas y archivos?
# Estructura recomendada (crear carpetas y archivos antes de ejecutar server):
mkdir -p app_aeropuerto/templates/avion
mkdir -p app_aeropuerto/static/css
touch app_aeropuerto/templates/base.html
touch app_aeropuerto/templates/header.html
touch app_aeropuerto/templates/navbar.html
touch app_aeropuerto/templates/footer.html
touch app_aeropuerto/templates/inicio.html
touch app_aeropuerto/templates/avion/agregar_avion.html
touch app_aeropuerto/templates/avion/ver_aviones.html
touch app_aeropuerto/templates/avion/actualizar_avion.html
touch app_aeropuerto/templates/avion/borrar_avion.html

Paso 30 — ¿Proyecto totalmente funcional?
# Con todos los pasos anteriores implementados, el CRUD de Avión estará funcional: crear, ver, editar, borrar. Admin accesible en /admin/.
# Si falta algo: revisar consola de Django para errores y comprobar settings.py, INSTALLED_APPS y rutas.

Paso 31 — ¿Finalmente ejecutar servidor en el puerto 8036?
python manage.py runserver 8036
# Abrir en navegador: http://127.0.0.1:8036/

Estructura final sugerida (resumen)
UIII_aeropuerto_0409/
├── .venv/
├── backend_aeropuerto/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
├── app_aeropuerto/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── urls.py
│   ├── views.py
│   ├── migrations/
│   │   └── __init__.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   ├── inicio.html
│   │   └── avion/
│   │       ├── agregar_avion.html
│   │       ├── ver_aviones.html
│   │       ├── actualizar_avion.html
│   │       └── borrar_avion.html
│   └── static/
│       └── css/
│           └── styles.css
├── manage.py
└── requirements.txt
