🛫 Proyecto: Aeropuerto

Lenguaje: Python
Framework: Django
Editor: VS Code

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


#Paso 1 — ¿Cómo crear la carpeta del proyecto UIII_aeropuerto_0409?

mkdir UIII_aeropuerto_0409
cd UIII_aeropuerto_0409


#Paso 2 — ¿Cómo abrir VS Code sobre la carpeta del proyecto?

code .


#Paso 3 — ¿Cómo abrir la terminal en VS Code?

Presiona Ctrl + ñ o ve al menú superior:
Ver → Terminal

#Paso 4 — ¿Cómo crear la carpeta del entorno virtual “.venv”?

python -m venv .venv


#Paso 5 — ¿Cómo activar el entorno virtual?

Windows:

.venv\Scripts\activate


Mac/Linux:

source .venv/bin/activate


#Paso 6 — ¿Cómo activar el intérprete de Python en VS Code?

Presiona Ctrl + Shift + P

Escribe: Seleccionar intérprete de Python

Elige la opción que diga .venv

#Paso 7 — ¿Cómo instalar Django?

pip install django
pip freeze > requirements.txt


#Paso 8 — ¿Cómo crear el proyecto backend_aeropuerto sin duplicar carpeta?

django-admin startproject backend_aeropuerto .


#Paso 9 — ¿Cómo ejecutar el servidor en el puerto 8036?

python manage.py runserver 8036


#Paso 10 — ¿Cómo abrir el link del servidor en el navegador?

Copia y pega este enlace en tu navegador:
👉 http://127.0.0.1:8036/

#Paso 11 — ¿Cómo crear la aplicación app_aeropuerto?

python manage.py startapp app_aeropuerto


#Paso 12 — ¿Cuál es el contenido del archivo models.py?

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


#Paso 12.5 — ¿Cómo realizar las migraciones?

python manage.py makemigrations
python manage.py migrate


#Paso 13 — ¿Con qué modelo se trabaja primero?

👉 Con el modelo AVION

#Paso 14 — ¿Cómo crear las funciones en views.py para CRUD de Avión?

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


#Paso 15 — ¿Cómo crear la carpeta templates dentro de app_aeropuerto?

mkdir app_aeropuerto/templates


#Paso 16 — ¿Qué archivos HTML se deben crear dentro de templates?

👉 Crea estos archivos:

base.html
header.html
navbar.html
footer.html
inicio.html


#Paso 17 — ¿Qué incluir en base.html?

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


#Paso 18 — ¿Cómo debe ser navbar.html?

Incluye menú principal, submenús e íconos (ejemplo con Bootstrap):

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


#Paso 19 — ¿Qué debe tener footer.html?

<footer class="bg-dark text-white text-center py-2 fixed-bottom">
  <p>© {{ now|date:"Y" }} - Creado por Ing. Bryan Villalobos, CBTis 128</p>
</footer>


#Paso 20 — ¿Qué debe contener inicio.html?

{% extends 'base.html' %}
{% block content %}
<h1 class="text-center">Bienvenido al Sistema de Administración del Aeropuerto</h1>
<img src="https://upload.wikimedia.org/wikipedia/commons/e/e8/Airport_Terminal.jpg" class="img-fluid rounded mx-auto d-block mt-4">
{% endblock %}


#Paso 21 — ¿Cómo crear la carpeta de aviones dentro de templates?

mkdir app_aeropuerto/templates/avion


#Paso 22 — ¿Qué archivos HTML se crean dentro de /templates/avion?

agregar_avion.html
ver_aviones.html
actualizar_avion.html
borrar_avion.html


(Cada uno con sus formularios y tablas básicos para CRUD sin forms.py)

#Paso 23 — ¿Se utiliza forms.py?

❌ No, no se utiliza forms.py

#Paso 24 — ¿Cómo crear el archivo urls.py en app_aeropuerto?

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


#Paso 25 — ¿Cómo agregar la app en settings.py?

INSTALLED_APPS = [
    ...,
    'app_aeropuerto',
]


#Paso 26 — ¿Cómo enlazar urls.py del proyecto con app_aeropuerto?

En backend_aeropuerto/urls.py agrega:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_aeropuerto.urls')),
]


#Paso 27 — ¿Cómo registrar los modelos en admin.py?

from django.contrib import admin
from .models import Avion, Vuelo, Empleado

admin.site.register(Avion)
admin.site.register(Vuelo)
admin.site.register(Empleado)


Luego ejecuta:

python manage.py makemigrations
python manage.py migrate


#Paso 28 — ¿Qué colores o estilo usar?

👉 Colores suaves, modernos y atractivos.
Usa Bootstrap, tonos azul, gris, blanco.

#Paso 29 — ¿Validación de datos?
❌ No validar datos.

#Paso 30 — ¿Qué hacer al inicio del proyecto?

✅ Crear toda la estructura de carpetas y archivos antes de programar.

#Paso 31 — ¿Finalmente ejecutar el servidor en el puerto 8036?

python manage.py runserver 8036


👉 Abre en tu navegador:
http://127.0.0.1:8036/
