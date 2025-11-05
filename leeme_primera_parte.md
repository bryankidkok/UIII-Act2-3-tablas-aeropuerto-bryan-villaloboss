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
o presionar **Ctrl + ñ**

---

# 🐍 PASO 4 — PROCEDIMIENTO PARA CREAR CARPETA ENTORNO VIRTUAL “.venv” DESDE TERMINAL DE VS CODE  
En la terminal, ejecutar:  
```bash
python -m venv .venv
```

---

# ⚙️ PASO 5 — PROCEDIMIENTO PARA ACTIVAR EL ENTORNO VIRTUAL  
Activar el entorno virtual:  
```bash
.venv\Scripts\activate
```

---

# 🧩 PASO 6 — PROCEDIMIENTO PARA ACTIVAR INTÉRPRETE DE PYTHON  
En VS Code:  
- Presiona **Ctrl + Shift + P**  
- Escribe: `Python: Select Interpreter`  
- Selecciona el entorno: **.venv**

---

# 📦 PASO 7 — PROCEDIMIENTO PARA INSTALAR DJANGO  
En la terminal (con el entorno activado):  
```bash
pip install django
```

---

# 🏗️ PASO 8 — PROCEDIMIENTO PARA CREAR PROYECTO backend_aeropuerto SIN DUPLICAR CARPETA  
```bash
django-admin startproject backend_aeropuerto .
```
> Nota: el punto al final evita crear carpetas duplicadas.

---

# 🚀 PASO 9 — PROCEDIMIENTO PARA EJECUTAR SERVIDOR EN EL PUERTO 8036  
```bash
python manage.py runserver 8036
```

---

# 🌐 PASO 10 — PROCEDIMIENTO PARA COPIAR Y PEGAR EL LINK EN EL NAVEGADOR  
Copiar el enlace que aparece en la terminal, por ejemplo:  
```
http://127.0.0.1:8036/
```
y pegarlo en el navegador.

---

# 🧰 PASO 11 — PROCEDIMIENTO PARA CREAR APLICACIÓN app_aeropuerto  
```bash
python manage.py startapp app_aeropuerto
```

---

# 🧠 PASO 12 — AQUI EL MODELO models.py  
Editar el archivo `app_aeropuerto/models.py` con el siguiente código:

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
Por ahora solo crearemos las vistas y plantillas del modelo **Avion**.

---

# 🧮 PASO 14 — EN views.py CREAR LAS FUNCIONES CORRESPONDIENTES  
En `app_aeropuerto/views.py` crear funciones:  
`inicio_aeropuerto`, `agregar_avion`, `actualizar_avion`, `realizar_actualizacion_avion`, `borrar_avion`.

---

# 🗂️ PASO 15 — CREAR LA CARPETA “templates” DENTRO DE app_aeropuerto  
Ruta:  
```
app_aeropuerto/templates/
```

---

# 🧾 PASO 16 — EN LA CARPETA templates CREAR LOS ARCHIVOS HTML (base, header, navbar, footer, inicio)  
Archivos:
```
base.html
header.html
navbar.html
footer.html
inicio.html
```

---

# 🎨 PASO 17 — EN base.html AGREGAR BOOTSTRAP PARA CSS Y JS  
Incluir los CDN de Bootstrap en `<head>` y antes de `</body>`.

---

# 🧭 PASO 18 — EN navbar.html INCLUIR OPCIONES Y SUBMENÚS INDICADOS  
Agregar menús:
- Sistema de Administración Aeropuerto  
- Inicio  
- Avión (submenu: Agregar, Ver, Actualizar, Borrar)  
- Vuelos (submenu: Agregar, Ver, Actualizar, Borrar)  
- Empleados (submenu: Agregar, Ver, Actualizar, Borrar)  
Con íconos en las opciones principales.

---

# 🕓 PASO 19 — EN footer.html INCLUIR DERECHOS DE AUTOR Y CRÉDITOS  
Texto fijo:  
```
© <fecha actual> Creado por Ing. Bryan Villalobos, Cbtis 128
```

---

# 🖼️ PASO 20 — EN inicio.html COLOCAR INFORMACIÓN DEL SISTEMA E IMAGEN DE AEROPUERTO  
Agregar descripción breve y una imagen desde internet relacionada con aeropuertos.

---

# 🗃️ PASO 21 — CREAR SUBCARPETA avion DENTRO DE templates  
Ruta:
```
app_aeropuerto/templates/avion/
```

---

# 🧱 PASO 22 — CREAR LOS ARCHIVOS HTML PARA CRUD DE AVION  
Dentro de `app_aeropuerto/templates/avion/` crear:  
```
agregar_avion.html  
ver_aviones.html  
actualizar_avion.html  
borrar_avion.html
```
> `ver_aviones.html` debe mostrar tabla con botones: **ver, editar, borrar**.

---

# 🚫 PASO 23 — NO UTILIZAR forms.py  
No se usarán formularios automáticos de Django.

---

# 🔗 PASO 24 — CREAR EL ARCHIVO urls.py EN app_aeropuerto  
Crear `app_aeropuerto/urls.py` con las rutas para las vistas CRUD de Avión.

---

# ⚙️ PASO 25 — AGREGAR app_aeropuerto EN settings.py  
En `backend_aeropuerto/settings.py` dentro de `INSTALLED_APPS`:  
```python
'app_aeropuerto',
```

---

# 🌍 PASO 26 — CONFIGURAR urls.py DE backend_aeropuerto PARA ENLAZAR CON app_aeropuerto  
En `backend_aeropuerto/urls.py`:  
```python
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_aeropuerto.urls')),
]
```

---

# 🧾 PASO 27 — REGISTRAR LOS MODELOS EN admin.py Y REALIZAR MIGRACIONES  
En `app_aeropuerto/admin.py`:  
```python
from django.contrib import admin
from .models import Avion, Vuelo, Empleado

admin.site.register(Avion)
admin.site.register(Vuelo)
admin.site.register(Empleado)
```
Luego ejecutar:
```bash
python manage.py makemigrations
python manage.py migrate
```

---

# ✈️ PASO 27 (NOTA) — SOLO TRABAJAR CON AVION  
Dejar **Vuelo** y **Empleado** pendientes.

---

# 🎨 PASO 28 — UTILIZAR COLORES SUAVES, ATRACTIVOS Y MODERNOS  
Usar Bootstrap con tonos suaves (celeste, gris claro, blanco, etc.)

---

# 🧮 PASO 28 (NOTA) — NO VALIDAR ENTRADA DE DATOS  
No implementar validaciones.

---

# 🧱 PASO 29 — AL INICIO CREAR LA ESTRUCTURA COMPLETA DE CARPETAS Y ARCHIVOS  
Crear todas las carpetas y archivos base desde el inicio del proyecto.

---

# ✅ PASO 30 — PROYECTO TOTALMENTE FUNCIONAL  
Verificar que todas las funciones CRUD de Avión operen correctamente.

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

# 📁 ESTRUCTURA FINAL DEL PROYECTO

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
