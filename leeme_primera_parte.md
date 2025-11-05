🛫 PROYECTO: AEROPUERTO

LENGUAJE: Python
FRAMEWORK: Django
EDITOR: VS Code

💥 ESTRUCTURA FINAL DEL PROYECTO
UIII_aeropuerto_0409/
├── .venv/                        # Entorno virtual
├── backend_aeropuerto/           # Proyecto principal
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_aeropuerto/               # Aplicación principal
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   ├── migrations/
│   │   └── __init__.py
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
├── manage.py
└── requirements.txt


#PASO 1 — ¿CÓMO CREAR LA CARPETA DEL PROYECTO UIII_AEROPUERTO_0409?

mkdir UIII_aeropuerto_0409
cd UIII_aeropuerto_0409


#PASO 2 — ¿CÓMO ABRIR VS CODE SOBRE LA CARPETA DEL PROYECTO?

code .


#PASO 3 — ¿CÓMO ABRIR LA TERMINAL EN VS CODE?

Presiona Ctrl + ñ o ve al menú superior:
Ver → Terminal

#PASO 4 — ¿CÓMO CREAR LA CARPETA DEL ENTORNO VIRTUAL “.VENV”?

python -m venv .venv


#PASO 5 — ¿CÓMO ACTIVAR EL ENTORNO VIRTUAL?

Windows:

.venv\Scripts\activate


Mac/Linux:

source .venv/bin/activate


#PASO 6 — ¿CÓMO ACTIVAR EL INTÉRPRETE DE PYTHON EN VS CODE?

Presiona Ctrl + Shift + P

Escribe: Seleccionar intérprete de Python

Elige la opción que diga .venv

#PASO 7 — ¿CÓMO INSTALAR DJANGO?

pip install django
pip freeze > requirements.txt


#PASO 8 — ¿CÓMO CREAR EL PROYECTO BACKEND_AEROPUERTO SIN DUPLICAR CARPETA?

django-admin startproject backend_aeropuerto .


#PASO 9 — ¿CÓMO EJECUTAR EL SERVIDOR EN EL PUERTO 8036?

python manage.py runserver 8036


#PASO 10 — ¿CÓMO ABRIR EL LINK DEL SERVIDOR EN EL NAVEGADOR?

Copia y pega este enlace en tu navegador:
👉 http://127.0.0.1:8036/

#PASO 11 — ¿CÓMO CREAR LA APLICACIÓN APP_AEROPUERTO?

python manage.py startapp app_aeropuerto


#PASO 12 — ¿CUÁL ES EL CONTENIDO DEL ARCHIVO MODELS.PY?

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


#PASO 12.5 — ¿CÓMO REALIZAR LAS MIGRACIONES?

python manage.py makemigrations
python manage.py migrate


#PASO 13 — ¿CON QUÉ MODELO SE TRABAJA PRIMERO?

👉 Con el MODELO AVION

#PASO 14 — ¿CÓMO CREAR LAS FUNCIONES EN VIEWS.PY PARA CRUD DE AVIÓN?

from django.shortcuts import render, redirect, get_object_or_404
from .models import Avion

def inicio_aeropuerto(request):
    return render(request, 'inicio.html')

def agregar_avion(request):
    if request.method == "POST":
        Avion.objects.create(
            matricula=request.POST['matricula'],
            modelo=request.POST['modelo'],
            fabricante=request.POST['fabricante'],
            capacidad=request.POST['capacidad'],
            anio_fabricacion=request.POST['anio_fabricacion'],
            tipo=request.POST['tipo'],
            estado=request.POST['estado']
        )
        return redirect('ver_aviones')
    return render(request, 'avion/agregar_avion.html')

def ver_aviones(request):
    aviones = Avion.objects.all()
    return render(request, 'avion/ver_aviones.html', {'aviones': aviones})

def actualizar_avion(request, id):
    avion = get_object_or_404(Avion, id=id)
    return render(request, 'avion/actualizar_avion.html', {'avion': avion})

def realizar_actualizacion_avion(request, id):
    avion = get_object_or_404(Avion, id=id)
    if request.method == "POST":
        avion.matricula = request.POST['matricula']
        avion.modelo = request.POST['modelo']
        avion.fabricante = request.POST['fabricante']
        avion.capacidad = request.POST['capacidad']
        avion.anio_fabricacion = request.POST['anio_fabricacion']
        avion.tipo = request.POST['tipo']
        avion.estado = request.POST['estado']
        avion.save()
        return redirect('ver_aviones')

def borrar_avion(request, id):
    avion = get_object_or_404(Avion, id=id)
    if request.method == "POST":
        avion.delete()
        return redirect('ver_aviones')
    return render(request, 'avion/borrar_avion.html', {'avion': avion})


#PASO 15 — ¿CÓMO CREAR LA CARPETA TEMPLATES DENTRO DE APP_AEROPUERTO?

mkdir app_aeropuerto/templates


#PASO 16 — ¿QUÉ ARCHIVOS HTML SE DEBEN CREAR DENTRO DE TEMPLATES?

base.html
header.html
navbar.html
footer.html
inicio.html


#PASO 17 — ¿QUÉ INCLUIR EN BASE.HTML?

<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}Sistema Aeropuerto{% endblock %}</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
  {% include 'header.html' %}
  {% include 'navbar.html' %}
  <div class="container mt-4">
      {% block content %}{% endblock %}
  </div>
  {% include 'footer.html' %}
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>


#PASO 18 — ¿CÓMO DEBE SER NAVBAR.HTML?

<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">✈️ Sistema de Administración Aeropuerto</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown">Aviones</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_avion' %}">Agregar avión</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_aviones' %}">Ver aviones</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>


#PASO 19 — ¿QUÉ DEBE TENER FOOTER.HTML?

<footer class="bg-dark text-white text-center py-2 fixed-bottom">
  <p>© {{ now|date:"Y" }} - Creado por Ing. Bryan Villalobos, CBTis 128</p>
</footer>


#PASO 20 — ¿QUÉ DEBE CONTENER INICIO.HTML?

{% extends 'base.html' %}
{% block content %}
<h1 class="text-center">Bienvenido al Sistema de Administración del Aeropuerto</h1>
<img src="https://upload.wikimedia.org/wikipedia/commons/e/e8/Airport_Terminal.jpg" class="img-fluid rounded mx-auto d-block mt-4">
{% endblock %}


#PASO 21 — ¿CÓMO CREAR LA CARPETA DE AVIONES DENTRO DE TEMPLATES?

mkdir app_aeropuerto/templates/avion


#PASO 22 — ¿QUÉ ARCHIVOS HTML SE CREAN DENTRO DE /TEMPLATES/AVION?

agregar_avion.html
ver_aviones.html
actualizar_avion.html
borrar_avion.html


#PASO 23 — ¿SE UTILIZA FORMS.PY?

❌ No se utiliza forms.py

#PASO 24 — ¿CÓMO CREAR EL ARCHIVO URLS.PY EN APP_AEROPUERTO?

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_aeropuerto, name='inicio'),
    path('agregar/', views.agregar_avion, name='agregar_avion'),
    path('ver/', views.ver_aviones, name='ver_aviones'),
    path('actualizar/<int:id>/', views.actualizar_avion, name='actualizar_avion'),
    path('realizar_actualizacion/<int:id>/', views.realizar_actualizacion_avion, name='realizar_actualizacion_avion'),
    path('borrar/<int:id>/', views.borrar_avion, name='borrar_avion'),
]


#PASO 25 — ¿CÓMO AGREGAR LA APP EN SETTINGS.PY?

INSTALLED_APPS = [
    ...,
    'app_aeropuerto',
]


#PASO 26 — ¿CÓMO ENLAZAR URLS.PY DEL PROYECTO CON APP_AEROPUERTO?

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_aeropuerto.urls')),
]


#PASO 27 — ¿CÓMO REGISTRAR LOS MODELOS EN ADMIN.PY?

from django.contrib import admin
from .models import Avion, Vuelo, Empleado

admin.site.register(Avion)
admin.site.register(Vuelo)
admin.site.register(Empleado)


Luego:

python manage.py makemigrations
python manage.py migrate


#PASO 28 — ¿QUÉ COLORES O ESTILO USAR?

👉 Colores suaves, modernos y atractivos (azul, gris, blanco).
Usa Bootstrap 5.

#PASO 29 — ¿VALIDACIÓN DE DATOS?

❌ No validar datos.

#PASO 30 — ¿QUÉ HACER AL INICIO DEL PROYECTO?

✅ Crear toda la estructura de carpetas y archivos antes de programar.

#PASO 31 — ¿FINALMENTE EJECUTAR EL SERVIDOR EN EL PUERTO 8036?

python manage.py runserver 8036


👉 Abre en tu navegador:
http://127.0.0.1:8036/
