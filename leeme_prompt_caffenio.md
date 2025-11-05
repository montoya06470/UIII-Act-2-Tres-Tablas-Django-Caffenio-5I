Perfecto — te doy **instrucciones muy específicas y punto por punto** para que montes el proyecto **Caffenio** en Django (Python) usando VS Code. Incluiré los comandos exactos, la estructura de carpetas/archivos que debes crear, el código necesario (models ya lo proporcionaste — lo dejo tal cual), las vistas CRUD para **Proveedor**, las plantillas HTML (Bootstrap incluido), configuración de `urls.py` y `settings.py`, y los pasos para migrar y ejecutar el servidor en el **puerto 8030**.

---

# 0) Resumen rápido (qué vas a crear)

* Proyecto Django: `backend_Caffenio`
* App: `app_Caffenio`
* Entorno virtual: `.venv`
* Trabajaremos SOLO con **Proveedor** por ahora (Producto y Venta pendientes)
* Plantillas en `app_Caffenio/templates/` y subcarpeta `proveedor`
* Sin `forms.py` (formularios HTML manuales)
* Servidor en `http://127.0.0.1:8030/`

---

# 1) Crear carpeta del proyecto `UIII_Caffenio_0647`

Abre tu terminal (cmd / PowerShell / bash) y ejecuta:

```bash
# en la carpeta donde quieras crear el proyecto:
mkdir UIII_Caffenio_0647
cd UIII_Caffenio_0647
```

---

# 2) Abrir VS Code en la carpeta `UIII_Caffenio_0647`

En la misma terminal:

```bash
code .
```

(Este comando abre VS Code en la carpeta actual.)

---

# 3) Abrir terminal integrada en VS Code

En VS Code: menú `Terminal` → `New Terminal` (o Ctrl+`).

---

# 4) Crear carpeta entorno virtual `.venv` desde terminal de VS Code

En la terminal integrada ejecuta:

* Windows (PowerShell):

```powershell
python -m venv .venv
```

* Linux / macOS:

```bash
python3 -m venv .venv
```

---

# 5) Activar el entorno virtual

* Windows (PowerShell):

```powershell
.venv\Scripts\Activate.ps1
# si da error de ejecución en PowerShell: usa
# Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
# y vuelve a ejecutar la activación
```

* Windows (cmd):

```cmd
.venv\Scripts\activate
```

* Linux / macOS:

```bash
source .venv/bin/activate
```

Verifica: `python -V` debe mostrar la versión de Python del entorno.

---

# 6) Activar intérprete de Python en VS Code

En VS Code: esquina inferior derecha → selecciona el intérprete y elige `.venv` (aparecerá como `.venv\Scripts\python` o similar). Así VS Code usará el intérprete del entorno virtual.

---

# 7) Instalar Django

Con el entorno activado:

```bash
pip install django
```

(opcional: `pip install django==4.2` si quieres una versión específica)

---

# 8) Crear proyecto `backend_Caffenio` SIN duplicar carpeta

Para que no cree una carpeta adicional, ejecuta **desde la raíz `UIII_Caffenio_0647`**:

```bash
django-admin startproject backend_Caffenio .
```

**Importante:** el punto `.` al final evita que Django cree una carpeta `backend_Caffenio` dentro de otra; la estructura quedará con `manage.py` en la raíz del repo.

---

# 9) Ejecutar servidor en el puerto 8030

Antes debemos crear la app y migrar; pero el comando para ejecutar el servidor en puerto 8030 es:

```bash
python manage.py runserver 8030
```

(Esto levantará: `http://127.0.0.1:8030/`)

---

# 10) Copiar y pegar el link en el navegador

Abre tu navegador y pega:

```
http://127.0.0.1:8030/
```

ó

```
http://localhost:8030/
```

---

# 11) Crear aplicación `app_Caffenio`

Desde la raíz del proyecto (donde está `manage.py`):

```bash
python manage.py startapp app_Caffenio
```

---

# 12) Aquí el `models.py`

Usa el `models.py` que compartiste. Colócalo en `app_Caffenio/models.py`. (Lo pego tal cual — no modificaciones):

```python
# app_Caffenio/models.py
from django.db import models

# ==========================================
# TABLA: PROVEEDORES  (7 CAMPOS)
# ==========================================
class Proveedor(models.Model):
    nombre = models.CharField(max_length=100)
    empresa = models.CharField(max_length=100)
    telefono = models.CharField(max_length=20)
    correo = models.EmailField()
    direccion = models.CharField(max_length=200)
    pais = models.CharField(max_length=50)
    tipo_producto = models.CharField(
        max_length=50,
        choices=[
            ('bebidas', 'Bebidas'),
            ('alimentos', 'Alimentos'),
            ('complementos', 'Complementos')
        ]
    )

    def __str__(self):
        return f"{self.nombre} - {self.empresa}"


# ==========================================
# TABLA: PRODUCTO  (7 CAMPOS)
# ==========================================
class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    descripcion = models.TextField()
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField()
    categoria = models.CharField(
        max_length=50,
        choices=[
            ('bebidas', 'Bebidas'),
            ('alimentos', 'Alimentos'),
            ('complementos', 'Complementos'),
            ('otro', 'Otro')
        ]
    )
    proveedor = models.ForeignKey(
        Proveedor,
        on_delete=models.CASCADE,
        related_name='productos'
    )
    sucursal = models.CharField(
        max_length=50,
        choices=[
            ('Sucursal 1', 'Sucursal 1'),
            ('Sucursal 2', 'Sucursal 2')
        ]
    )

    def __str__(self):
        return self.nombre


# ==========================================
# TABLA: VENTAS  (7 CAMPOS)
# ==========================================
class Venta(models.Model):
    fecha = models.DateTimeField(auto_now_add=True)
    cliente = models.CharField(max_length=100)
    total = models.DecimalField(max_digits=10, decimal_places=2)
    metodo_pago = models.CharField(
        max_length=50,
        choices=[
            ('efectivo', 'Efectivo'),
            ('tarjeta', 'Tarjeta'),
            ('transferencia', 'Transferencia')
        ]
    )
    sucursal = models.CharField(
        max_length=50,
        choices=[
            ('Sucursal 1', 'Sucursal 1'),
            ('Sucursal 2', 'Sucursal 2')
        ]
    )
    empleado = models.CharField(max_length=100)
    productos = models.ManyToManyField(
        Producto,
        related_name='ventas'
    )

    def __str__(self):
        return f"Venta {self.id} - {self.cliente}"
```

> Nota: por ahora trabajaremos solo con `Proveedor` como pediste; Producto y Venta quedan pendientes (están en models pero no los usaremos todavía).

---

# 12.5) Procedimiento para realizar las migraciones (`makemigrations` y `migrate`)

Primero registra la app en `settings.py` (punto 25 abajo). Luego:

```bash
# crear migrations para la app
python manage.py makemigrations app_Caffenio

# aplicar migraciones
python manage.py migrate
```

Si necesitas crear un superusuario para admin:

```bash
python manage.py createsuperuser
# sigue prompts (username, email, contraseña)
```

---

# 13) Primero trabajamos con el MODELO: PROVEEDOR

Ok — el CRUD que implementaremos será exclusivamente sobre el modelo `Proveedor`.

---

# 14) En `views.py` de `app_Caffenio` crear las funciones con su código

Abre `app_Caffenio/views.py` y reemplaza su contenido por lo siguiente:

```python
# app_Caffenio/views.py
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor
from django.urls import reverse

def inicio_caffenio(request):
    return render(request, 'inicio.html')

# Mostrar lista de proveedores
def ver_proveedores(request):
    proveedores = Proveedor.objects.all().order_by('id')
    return render(request, 'proveedor/ver_proveedores.html', {'proveedores': proveedores})

# Formulario para agregar proveedor (GET muestra, POST guarda)
def agregar_proveedor(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre', '').strip()
        empresa = request.POST.get('empresa', '').strip()
        telefono = request.POST.get('telefono', '').strip()
        correo = request.POST.get('correo', '').strip()
        direccion = request.POST.get('direccion', '').strip()
        pais = request.POST.get('pais', '').strip()
        tipo_producto = request.POST.get('tipo_producto', '').strip()
        Proveedor.objects.create(
            nombre=nombre,
            empresa=empresa,
            telefono=telefono,
            correo=correo,
            direccion=direccion,
            pais=pais,
            tipo_producto=tipo_producto
        )
        return redirect('app_Caffenio:ver_proveedores')
    return render(request, 'proveedor/agregar_proveedor.html')

# Vista para mostrar formulario de actualización (GET)
def actualizar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

# Procesar actualización (POST)
def realizar_actualizacion_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    if request.method == 'POST':
        proveedor.nombre = request.POST.get('nombre', proveedor.nombre).strip()
        proveedor.empresa = request.POST.get('empresa', proveedor.empresa).strip()
        proveedor.telefono = request.POST.get('telefono', proveedor.telefono).strip()
        proveedor.correo = request.POST.get('correo', proveedor.correo).strip()
        proveedor.direccion = request.POST.get('direccion', proveedor.direccion).strip()
        proveedor.pais = request.POST.get('pais', proveedor.pais).strip()
        proveedor.tipo_producto = request.POST.get('tipo_producto', proveedor.tipo_producto).strip()
        proveedor.save()
        return redirect('app_Caffenio:ver_proveedores')
    return redirect('app_Caffenio:ver_proveedores')

# Confirmar borrado (GET) y borrar (POST)
def borrar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    if request.method == 'POST':
        proveedor.delete()
        return redirect('app_Caffenio:ver_proveedores')
    return render(request, 'proveedor/borrar_proveedor.html', {'proveedor': proveedor})
```

> Notas: usamos `get_object_or_404`, redirecciones con nombres de rutas; no hay validación (tal como pediste).

---

# 15) Crear carpeta `templates` dentro de `app_Caffenio`

Estructura:

```
app_Caffenio/
  templates/
    base.html
    header.html
    navbar.html
    footer.html
    inicio.html
    proveedor/
      agregar_proveedor.html
      ver_proveedores.html
      actualizar_proveedor.html
      borrar_proveedor.html
```

Crea los directorios y archivos manualmente en VS Code.

---

# 16) Archivos HTML (contenido)

## `app_Caffenio/templates/base.html`

```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Caffenio - Sistema</title>

    <!-- Bootstrap CSS (CDN) -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Bootstrap Icons -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css" rel="stylesheet">

    <style>
      /* Colores suaves, morado suave como ejemplo */
      body { font-family: "Times New Roman", Times, serif; background: #f8f5fb; }
      .navbar-brand { font-family: "Dancing Script", cursive; font-size: 1.5rem; color: #6f42c1; }
      footer.fixed-footer { position: fixed; left:0; bottom:0; width:100%; background:#fff; border-top:1px solid #e6e6e6; padding:10px 0; }
      .card-hover { transition: transform .3s; }
      .card-hover:hover { transform: translateY(-5px); }
    </style>

    {% block extra_head %}{% endblock %}
  </head>
  <body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}

    <main class="container my-4">
      {% block content %}{% endblock %}
    </main>

    {% include 'footer.html' %}

    <!-- Bootstrap JS (CDN) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
    {% block extra_js %}{% endblock %}
  </body>
</html>
```

## `app_Caffenio/templates/header.html`

```html
<header class="py-3">
  <div class="container d-flex align-items-center">
    <a class="navbar-brand" href="{% url 'app_Caffenio:inicio' %}">Caffenio</a>
    <div class="ms-auto text-muted small">Sistema de Administración</div>
  </div>
</header>
```

## `app_Caffenio/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
  <div class="container">
    <a class="navbar-brand d-lg-none" href="{% url 'app_Caffenio:inicio' %}">Caffenio</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarsExample" aria-controls="navbarsExample" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarsExample">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="nav-link" href="{% url 'app_Caffenio:inicio' %}">
            <i class="bi bi-house-door"></i> Inicio
          </a>
        </li>

        <!-- Proveedores (con submenu) -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            <i class="bi bi-people"></i> Proveedores
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'app_Caffenio:agregar_proveedor' %}">Agregar Proveedor</a></li>
            <li><a class="dropdown-item" href="{% url 'app_Caffenio:ver_proveedores' %}">Ver Proveedores</a></li>
          </ul>
        </li>

        <!-- Productos -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            <i class="bi bi-basket"></i> Productos
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar productos</a></li>
            <li><a class="dropdown-item" href="#">Ver productos</a></li>
          </ul>
        </li>

        <!-- Ventas -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            <i class="bi bi-cash-stack"></i> Ventas
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar ventas</a></li>
            <li><a class="dropdown-item" href="#">Ver ventas</a></li>
          </ul>
        </li>

      </ul>
    </div>
  </div>
</nav>
```

> Iconos sólo en las opciones principales (house, people, basket, cash-stack), no en submenús — como pediste.

## `app_Caffenio/templates/footer.html`

```html
<footer class="fixed-footer text-center">
  <div class="container d-flex justify-content-between align-items-center">
    <div>© Derechos reservados</div>
    <div id="fecha-sistema">{{ fecha_actual }}</div>
    <div>Creado por Andrea Montoya, Cbtis 128</div>
  </div>
</footer>
```

> En la vista `inicio_caffenio` pasaremos `fecha_actual` (ver abajo).

## `app_Caffenio/templates/inicio.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="row">
  <div class="col-md-8">
    <div class="card p-3">
      <h3>Bienvenido al Sistema de Administración Caffenio</h3>
      <p>Este sistema permite administrar proveedores, productos y ventas.</p>
    </div>
  </div>
  <div class="col-md-4">
    <div class="card">
      <!-- Imagen desde la red sobre Cinepolis (ejemplo) -->
      <img src="https://i.imgur.com/8Km9tLL.jpg" class="card-img-top" alt="Cinepolis">
      <div class="card-body">
        <p class="card-text">Imagen ejemplo tomada desde la red (Cinepolis).</p>
      </div>
    </div>
  </div>
</div>
{% endblock %}
```

> Cambia la URL de la imagen si deseas otra (esta es de ejemplo).

---

# 17) En `base.html` ya agregamos Bootstrap (punto 17 cumplido).

---

# 18) `navbar.html` ya incluye las opciones y submenús pedidos y los iconos para opciones principales (no en submenús).

---

# 19) `footer.html` incluye derechos de autor, fecha del sistema y texto "Creado por Andrea Montoya, Cbtis 128" y está fijado al final.

Para mostrar la fecha, modifica la vista `inicio_caffenio` en `views.py`:

```python
from django.utils import timezone

def inicio_caffenio(request):
    fecha_actual = timezone.now().strftime('%Y-%m-%d %H:%M:%S')
    return render(request, 'inicio.html', {'fecha_actual': fecha_actual})
```

---

# 20) `inicio.html` ya tiene información del sistema más imagen desde la red (punto 20 cumplido).

---

# 21) Crear subcarpeta `proveedor` dentro de `app_Caffenio/templates` (he indicado la estructura).

---

# 22) Contenido de los archivos HTML dentro de `proveedor/`

## `app_Caffenio/templates/proveedor/agregar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h4>Agregar Proveedor</h4>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label class="form-label">Nombre</label>
    <input class="form-control" name="nombre" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Empresa</label>
    <input class="form-control" name="empresa" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Teléfono</label>
    <input class="form-control" name="telefono">
  </div>
  <div class="mb-3">
    <label class="form-label">Correo</label>
    <input class="form-control" name="correo" type="email">
  </div>
  <div class="mb-3">
    <label class="form-label">Dirección</label>
    <input class="form-control" name="direccion">
  </div>
  <div class="mb-3">
    <label class="form-label">País</label>
    <input class="form-control" name="pais">
  </div>
  <div class="mb-3">
    <label class="form-label">Tipo de producto</label>
    <select class="form-select" name="tipo_producto">
      <option value="bebidas">Bebidas</option>
      <option value="alimentos">Alimentos</option>
      <option value="complementos">Complementos</option>
    </select>
  </div>
  <button class="btn btn-primary">Guardar</button>
  <a class="btn btn-secondary" href="{% url 'app_Caffenio:ver_proveedores' %}">Cancelar</a>
</form>
{% endblock %}
```

## `app_Caffenio/templates/proveedor/ver_proveedores.html`

```html
{% extends 'base.html' %}
{% block content %}
<h4>Lista de Proveedores</h4>
<table class="table table-striped">
  <thead>
    <tr>
      <th>ID</th><th>Nombre</th><th>Empresa</th><th>Teléfono</th><th>Correo</th><th>País</th><th>Tipo</th><th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for p in proveedores %}
    <tr>
      <td>{{ p.id }}</td>
      <td>{{ p.nombre }}</td>
      <td>{{ p.empresa }}</td>
      <td>{{ p.telefono }}</td>
      <td>{{ p.correo }}</td>
      <td>{{ p.pais }}</td>
      <td>{{ p.get_tipo_producto_display }}</td>
      <td>
        <a class="btn btn-sm btn-info" href="{% url 'app_Caffenio:actualizar_proveedor' p.id %}">Editar</a>
        <a class="btn btn-sm btn-danger" href="{% url 'app_Caffenio:borrar_proveedor' p.id %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="8">No hay proveedores registrados.</td></tr>
    {% endfor %}
  </tbody>
</table>
<a class="btn btn-success" href="{% url 'app_Caffenio:agregar_proveedor' %}">Agregar Proveedor</a>
{% endblock %}
```

## `app_Caffenio/templates/proveedor/actualizar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h4>Actualizar Proveedor</h4>
<form method="post" action="{% url 'app_Caffenio:realizar_actualizacion_proveedor' proveedor.id %}">
  {% csrf_token %}
  <div class="mb-3">
    <label class="form-label">Nombre</label>
    <input class="form-control" name="nombre" value="{{ proveedor.nombre }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Empresa</label>
    <input class="form-control" name="empresa" value="{{ proveedor.empresa }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Teléfono</label>
    <input class="form-control" name="telefono" value="{{ proveedor.telefono }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Correo</label>
    <input class="form-control" name="correo" value="{{ proveedor.correo }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Dirección</label>
    <input class="form-control" name="direccion" value="{{ proveedor.direccion }}">
  </div>
  <div class="mb-3">
    <label class="form-label">País</label>
    <input class="form-control" name="pais" value="{{ proveedor.pais }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Tipo de producto</label>
    <select class="form-select" name="tipo_producto">
      <option value="bebidas" {% if proveedor.tipo_producto == 'bebidas' %}selected{% endif %}>Bebidas</option>
      <option value="alimentos" {% if proveedor.tipo_producto == 'alimentos' %}selected{% endif %}>Alimentos</option>
      <option value="complementos" {% if proveedor.tipo_producto == 'complementos' %}selected{% endif %}>Complementos</option>
    </select>
  </div>
  <button class="btn btn-primary">Guardar cambios</button>
  <a class="btn btn-secondary" href="{% url 'app_Caffenio:ver_proveedores' %}">Cancelar</a>
</form>
{% endblock %}
```

## `app_Caffenio/templates/proveedor/borrar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h4>Confirmar borrado</h4>
<p>¿Deseas eliminar al proveedor "<strong>{{ proveedor.nombre }}</strong>" (Empresa: {{ proveedor.empresa }})?</p>
<form method="post">
  {% csrf_token %}
  <button class="btn btn-danger" type="submit">Sí, eliminar</button>
  <a class="btn btn-secondary" href="{% url 'app_Caffenio:ver_proveedores' %}">Cancelar</a>
</form>
{% endblock %}
```

---

# 23) No utilizar `forms.py`

Ya lo respetamos: los formularios son HTML puros y se procesan con `request.POST` en las vistas.

---

# 24) Crear `urls.py` en `app_Caffenio` con rutas para CRUD proveedores

Crea `app_Caffenio/urls.py` con el siguiente contenido:

```python
# app_Caffenio/urls.py
from django.urls import path
from . import views

app_name = 'app_Caffenio'

urlpatterns = [
    path('', views.inicio_caffenio, name='inicio'),
    path('proveedores/', views.ver_proveedores, name='ver_proveedores'),
    path('proveedores/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedores/<int:proveedor_id>/editar/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedores/<int:proveedor_id>/actualizar/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedores/<int:proveedor_id>/borrar/', views.borrar_proveedor, name='borrar_proveedor'),
]
```

---

# 25) Agregar `app_Caffenio` en `settings.py` de `backend_Caffenio`

Abre `backend_Caffenio/settings.py` y en `INSTALLED_APPS` añade `'app_Caffenio',`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Apps propias
    'app_Caffenio',
]
```

Además (opcional pero recomendado) configura la carpeta de templates para que encuentre la carpeta app templates:

```python
import os
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

TEMPLATES = [
    {
        # ...
        'DIRS': [ os.path.join(BASE_DIR, 'app_Caffenio', 'templates') ],
        'APP_DIRS': True,
        # ...
    },
]
```

También asegúrate que `STATIC_URL = '/static/'` y tengas configurado `ALLOWED_HOSTS` (para desarrollo dejar `ALLOWED_HOSTS = []` está bien).

---

# 26) Configurar `urls.py` de `backend_Caffenio` para enlazar con `app_Caffenio`

Abre `backend_Caffenio/urls.py` y modifícalo:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Caffenio.urls', namespace='app_Caffenio')),
]
```

---

# 27) Registrar los modelos en `admin.py` y volver a realizar las migraciones

En `app_Caffenio/admin.py` añade:

```python
from django.contrib import admin
from .models import Proveedor, Producto, Venta

@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('id','nombre','empresa','telefono','correo','pais','tipo_producto')
    search_fields = ('nombre','empresa','correo')

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('id','nombre','precio','stock','categoria','proveedor')
    search_fields = ('nombre',)

@admin.register(Venta)
class VentaAdmin(admin.ModelAdmin):
    list_display = ('id','cliente','total','fecha','metodo_pago','sucursal')
    search_fields = ('cliente',)
```

Luego crea migraciones y aplica (si no lo hiciste antes):

```bash
python manage.py makemigrations app_Caffenio
python manage.py migrate
```

---

# 27 (repetido en tu lista) por lo pronto solo trabajar con “Proveedor”

Aunque registramos Producto y Venta en admin, en la parte funcional del CRUD sólo implementamos Proveedor. Producto y Venta quedan para más adelante.

---

# 28) Estética: Colores suaves y moderno

En `base.html` ya añadí estilos CSS simples con morado suave `#6f42c1` y fondo claro. Puedes ajustar colores en la etiqueta `<style>` o mover todo a un solo archivo CSS (si quieres que todo el estilo esté en un CSS único, crea `static/css/styles.css` y úsalo en `base.html`).

---

# 29) No validar entrada de datos

Los formularios no ejecutan validaciones extra — solo lo mínimo HTML (`required` en algunos campos). Así concuerda con el requerimiento.

---

# 30) Al inicio crear la estructura completa de carpetas y archivos

Estructura sugerida (créala):

```
UIII_Caffenio_0647/
├─ .venv/
├─ backend_Caffenio/
│   ├─ __init__.py
│   ├─ settings.py
│   ├─ urls.py
│   ├─ wsgi.py
│   └─ asgi.py
├─ app_Caffenio/
│   ├─ migrations/
│   ├─ templates/
│   │   ├─ base.html
│   │   ├─ header.html
│   │   ├─ navbar.html
│   │   ├─ footer.html
│   │   ├─ inicio.html
│   │   └─ proveedor/
│   │       ├─ agregar_proveedor.html
│   │       ├─ ver_proveedores.html
│   │       ├─ actualizar_proveedor.html
│   │       └─ borrar_proveedor.html
│   ├─ __init__.py
│   ├─ admin.py
│   ├─ apps.py
│   ├─ models.py
│   ├─ views.py
│   ├─ urls.py
│   └─ tests.py
├─ manage.py
└─ requirements.txt
```

Crea `requirements.txt` con:

```
Django
```

(instala con `pip install -r requirements.txt` si quieres)

---

# 31) Proyecto totalmente funcional — finalmente ejecutar servidor en el puerto 8030

Ya configuraste todo; pasos finales:

1. Asegúrate de tener el entorno activado.
2. Crear migraciones y aplicarlas:

```bash
python manage.py makemigrations
python manage.py migrate
```

3. Crear superusuario (opcional):

```bash
python manage.py createsuperuser
```

4. Ejecutar servidor en el puerto 8030:

```bash
python manage.py runserver 8030
```

5. Abrir en navegador:

```
http://127.0.0.1:8030/
```

6. Panel admin:

```
http://127.0.0.1:8030/admin/
```

---

# Consejos y notas finales

* Para editar el CSS en un solo archivo: crea `app_Caffenio/static/css/styles.css`, añade link en `base.html` y copia ahí las reglas. Asegúrate de que `STATICFILES_DIRS` o `STATIC_ROOT` estén configurados si lo necesitas.
* Si se usa Windows PowerShell y la activación falla por política de ejecución, ejecutar:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
.venv\Scripts\Activate.ps1
```

* No validamos inputs deliberadamente (punto 28).
* Si quieres que el footer muestre la fecha dinámica en todas las páginas, puedes crear un context processor o inyectarla desde cada vista. Aquí inyecté desde la vista `inicio_caffenio`.

---

Si quieres, ahora:

* Puedo generar los archivos completos listos para copiar/pegar (te los dejo en la conversación),
* O te doy los comandos para inicializar un repositorio Git y subirlo a GitHub,
* O continúo implementando el CRUD de **Producto** y **Venta**.

Dime cuál es el siguiente paso que quieres y lo hago inmediatamente.
