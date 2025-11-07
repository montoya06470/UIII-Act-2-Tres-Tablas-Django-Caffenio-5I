Perfecto — ahora sí: **todo el proyecto desde cero, punto por punto, exactamente como lo pediste**. Instrucciones, comandos y **todos** los archivos (código listo para pegar). He mantenido **exactamente** tus requerimientos:

* Lenguaje: **Python**
* Framework: **Django**
* Editor: **VS Code**
* Nombre de carpeta del proyecto raíz: `UIII_Caffenio_0647`
* Nombre del proyecto Django: `backend_Caffenio` (creado dentro de la carpeta raíz **sin** duplicar)
* Nombre de la app: `app_Caffenio`
* Trabajaremos **por ahora sólo con el modelo Proveedor** (Producto y Venta quedan registrados pero sin CRUD público)
* **No usar `forms.py`** (todo con `request.POST`)
* `header.html` **solo** se incluye en `inicio.html`
* Servidor en **puerto 8030**
* Estilos: **solo Bootstrap CDN** (sin estilos personalizados)

Voy a seguir **tu numerado original** (1 → 31) y en cada punto te doy exactamente lo que necesitas.

---

# Preparación (comandos comunes)

Usa la terminal integrada de VS Code (Terminal → New Terminal).

## Windows (PowerShell)

```powershell
# crear carpeta del proyecto y entrar
mkdir UIII_Caffenio_0647
cd UIII_Caffenio_0647
# crear venv
python -m venv .venv
# activar
.venv\Scripts\Activate.ps1
# Si PowerShell bloquea: Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## macOS / Linux

```bash
mkdir -p ~/Proyectos/UIII_Caffenio_0647
cd ~/Proyectos/UIII_Caffenio_0647
python3 -m venv .venv
source .venv/bin/activate
```

Luego, dentro del entorno virtual:

```bash
pip install django
```

---

# 1 → Procedimiento para crear carpeta del proyecto: `UIII_Caffenio_0647`

(Hecho arriba: `mkdir UIII_Caffenio_0647` y `cd UIII_Caffenio_0647`)

---

# 2 → Procedimiento para abrir VS Code sobre la carpeta `UIII_Caffenio_0647`

Desde la terminal (en la carpeta):

```bash
code .
```

O en VS Code: *File → Open Folder…* → seleccionar `UIII_Caffenio_0647`.

---

# 3 → Procedimiento para abrir terminal en VS Code

En VS Code: `Terminal → New Terminal` (o `Ctrl+ñ` / `Ctrl+Shift+` según tu teclado). La terminal se abrirá en la carpeta del proyecto.

---

# 4 → Procedimiento para crear carpeta entorno virtual `.venv` desde terminal de VS Code

(comandos ya mostrados en Preparación)

```bash
python -m venv .venv    # Windows
python3 -m venv .venv   # macOS/Linux
```

---

# 5 → Procedimiento para activar el entorno virtual

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Windows CMD:

```cmd
.venv\Scripts\activate
```

macOS/Linux:

```bash
source .venv/bin/activate
```

Verás `(.venv)` en el prompt.

---

# 6 → Procedimiento para activar intérprete de Python en VS Code

1. `Ctrl+Shift+P` → escribir `Python: Select Interpreter`.
2. Elegir el intérprete que apunta a `UIII_Caffenio_0647/.venv/...` (aparecerá como `.venv`).

---

# 7 → Procedimiento para instalar Django

Con el entorno activado:

```bash
pip install django
```

Verifica:

```bash
python -m django --version
```

---

# 8 → Procedimiento para crear proyecto `backend_Caffenio` sin duplicar carpeta

En la raíz `UIII_Caffenio_0647` (asegúrate de NO tener `manage.py` ya creado; si lo tienes no repitas):

```bash
django-admin startproject backend_Caffenio .
```

**El punto final (`.`)** es importante: crea el proyecto **en la carpeta actual** (sin carpeta extra).

Estructura mínima esperada ahora:

```
UIII_Caffenio_0647/
  manage.py
  backend_Caffenio/
    __init__.py
    settings.py
    urls.py
    wsgi.py
    asgi.py
```

---

# 9 → Procedimiento para ejecutar servidor en el puerto 8030

Primero asegúrate de haber agregado la app a `INSTALLED_APPS` y de haber corrido migraciones (más abajo). Luego:

```bash
python manage.py runserver 8030
```

Abre: `http://127.0.0.1:8030/`

---

# 10 → Procedimiento para copiar y pegar el link en el navegador

Copia: `http://127.0.0.1:8030/` en tu navegador. Verás la página de inicio de Django hasta que crees tus vistas.

---

# 11 → Procedimiento para crear aplicación `app_Caffenio`

Desde la raíz:

```bash
python manage.py startapp app_Caffenio
```

Se crea la carpeta `app_Caffenio/` con archivos básicos.

---

# 12 → `models.py` (pon este código en `app_Caffenio/models.py`)

Copia exactamente esto (es tu modelo original, sin cambios funcionales):

```python
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

> Nota: aunque registraremos Producto y Venta en admin, tu CRUD público será **solo para Proveedor** (como pediste).

---

# 12.5 → Procedimiento para realizar las migraciones (`makemigrations` y `migrate`)

Antes de `makemigrations`, añade la app a `INSTALLED_APPS` (punto 25). Después en la terminal:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

# 13 → Primero trabajamos con el MODELO: PROVEEDOR

Entendido — lo siguiente (vistas, templates y urls) implementarán CRUD solo para `Proveedor`.

---

# 14 → `views.py` en `app_Caffenio/views.py` (funciones con su código)

Copia exactamente este `views.py` (usa los nombres de funciones que pediste: `inicio_caffenio`, `agregar_proveedor`, `actualizar_proveedor`, `realizar_actualizacion_proveedor`, `borrar_proveedor`):

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor
from datetime import datetime

def inicio_caffenio(request):
    # Pasamos la fecha para el footer
    return render(request, 'inicio.html', {'now': datetime.now()})

def agregar_proveedor(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre', '')
        empresa = request.POST.get('empresa', '')
        telefono = request.POST.get('telefono', '')
        correo = request.POST.get('correo', '')
        direccion = request.POST.get('direccion', '')
        pais = request.POST.get('pais', '')
        tipo_producto = request.POST.get('tipo_producto', '')

        Proveedor.objects.create(
            nombre=nombre,
            empresa=empresa,
            telefono=telefono,
            correo=correo,
            direccion=direccion,
            pais=pais,
            tipo_producto=tipo_producto
        )
        return redirect('ver_proveedores')
    return render(request, 'proveedor/agregar_proveedor.html')

def ver_proveedores(request):
    proveedores = Proveedor.objects.all()
    return render(request, 'proveedor/ver_proveedores.html', {'proveedores': proveedores})

def actualizar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, id=proveedor_id)
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

def realizar_actualizacion_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, id=proveedor_id)
    if request.method == 'POST':
        proveedor.nombre = request.POST.get('nombre', proveedor.nombre)
        proveedor.empresa = request.POST.get('empresa', proveedor.empresa)
        proveedor.telefono = request.POST.get('telefono', proveedor.telefono)
        proveedor.correo = request.POST.get('correo', proveedor.correo)
        proveedor.direccion = request.POST.get('direccion', proveedor.direccion)
        proveedor.pais = request.POST.get('pais', proveedor.pais)
        proveedor.tipo_producto = request.POST.get('tipo_producto', proveedor.tipo_producto)
        proveedor.save()
        return redirect('ver_proveedores')
    return redirect('actualizar_proveedor', proveedor_id=proveedor.id)

def borrar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, id=proveedor_id)
    if request.method == 'POST':
        proveedor.delete()
        return redirect('ver_proveedores')
    return render(request, 'proveedor/borrar_proveedor.html', {'proveedor': proveedor})
```

---

# 15 → Crear la carpeta `templates` dentro de `app_Caffenio`

Crea la estructura de carpetas:

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

---

# 16 → Archivos HTML (contenido completo)

A continuación **todos** los archivos HTML listos para pegar en sus respectivas rutas.

## `app_Caffenio/templates/base.html`

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% block title %}Caffenio{% endblock %}</title>

  <!-- Bootstrap CSS CDN -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  {% block extra_head %}{% endblock %}
</head>
<body>
  {% include 'navbar.html' %}

  <div class="container mt-4" style="padding-bottom: 5rem;">
    {% block content %}{% endblock %}
  </div>

  {% include 'footer.html' %}

  <!-- Bootstrap JS CDN -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  {% block extra_js %}{% endblock %}
</body>
</html>
```

---

## `app_Caffenio/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
  <div class="container-fluid">
    <a class="navbar-brand fw-bold" href="{% url 'inicio' %}">Sistema de Administración Caffenio</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item">
          <a class="nav-link" href="{% url 'inicio' %}"><i class="bi bi-house"></i> Inicio</a>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown"> <i class="bi bi-people"></i> Proveedores</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedores' %}">Ver Proveedores</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Proveedor</a></li>
            <li><a class="dropdown-item" href="#">Borrar Proveedor</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown"> <i class="bi bi-box-seam"></i> Productos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar productos</a></li>
            <li><a class="dropdown-item" href="#">Ver productos</a></li>
            <li><a class="dropdown-item" href="#">Actualizar productos</a></li>
            <li><a class="dropdown-item" href="#">Borrar productos</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown"> <i class="bi bi-cash-stack"></i> Ventas</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar ventas</a></li>
            <li><a class="dropdown-item" href="#">Ver ventas</a></li>
            <li><a class="dropdown-item" href="#">Actualizar ventas</a></li>
            <li><a class="dropdown-item" href="#">Borrar ventas</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>

<!-- Bootstrap Icons -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.css">
```

---

## `app_Caffenio/templates/header.html`  ← **nuevo** (solo en inicio)

```html
<header class="text-center py-4 bg-light border-bottom">
  <h1 class="display-6 fw-bold">Sistema de Administración Caffenio</h1>
  <p class="text-muted">Gestión de proveedores, productos y ventas</p>
</header>
```

---

## `app_Caffenio/templates/footer.html`

```html
<footer class="text-center bg-white py-3" style="position: fixed; bottom: 0; left: 0; right: 0;">
  <div class="container d-flex justify-content-between">
    <div>© {{ now.year }}</div>
    <div>Creado por Andrea Montoya, Cbtis 128</div>
  </div>
</footer>
```

> Nota: la variable `now` la pasamos desde la vista `inicio_caffenio`.

---

## `app_Caffenio/templates/inicio.html`  ← **incluye header solo aquí**

```html
{% extends "base.html" %}
{% block content %}
  {% include "header.html" %}
  <div class="row mt-4">
    <div class="col-md-8">
      <h2>Bienvenido al Sistema Caffenio</h2>
      <p>Este sistema administra proveedores, productos y ventas.</p>
    </div>
    <div class="col-md-4">
      <img src="https://cdn.forbes.com.mx/2019/04/Caffenio-1.jpg"
           alt="Caffenio" class="img-fluid rounded">
    </div>
  </div>
{% endblock %}
```

---

## `app_Caffenio/templates/proveedor/agregar_proveedor.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Agregar Proveedor</h2>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label>Nombre</label>
    <input class="form-control" name="nombre">
  </div>
  <div class="mb-3">
    <label>Empresa</label>
    <input class="form-control" name="empresa">
  </div>
  <div class="mb-3">
    <label>Teléfono</label>
    <input class="form-control" name="telefono">
  </div>
  <div class="mb-3">
    <label>Correo</label>
    <input class="form-control" name="correo" type="email">
  </div>
  <div class="mb-3">
    <label>Dirección</label>
    <input class="form-control" name="direccion">
  </div>
  <div class="mb-3">
    <label>País</label>
    <input class="form-control" name="pais">
  </div>
  <div class="mb-3">
    <label>Tipo de producto</label>
    <select class="form-select" name="tipo_producto">
      <option value="bebidas">Bebidas</option>
      <option value="alimentos">Alimentos</option>
      <option value="complementos">Complementos</option>
    </select>
  </div>
  <button class="btn btn-primary" type="submit">Guardar</button>
</form>
{% endblock %}
```

---

## `app_Caffenio/templates/proveedor/ver_proveedores.html`

```html
{% extends "base.html" %}
{% block content %}
  <h2 class="mb-4">Lista de Proveedores</h2>
  <table class="table table-striped table-bordered">
    <thead class="table-dark">
      <tr>
        <th>Nombre</th>
        <th>Empresa</th>
        <th>Teléfono</th>
        <th>Correo</th>
        <th>Dirección</th>
        <th>País</th>
        <th>Tipo de Producto</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      {% for proveedor in proveedores %}
      <tr>
        <td>{{ proveedor.nombre }}</td>
        <td>{{ proveedor.empresa }}</td>
        <td>{{ proveedor.telefono }}</td>
        <td>{{ proveedor.correo }}</td>
        <td>{{ proveedor.direccion }}</td>
        <td>{{ proveedor.pais }}</td>
        <td>{{ proveedor.tipo_producto }}</td>
        <td>
          <a href="{% url 'actualizar_proveedor' proveedor.id %}" class="btn btn-warning btn-sm">Editar</a>
          <a href="{% url 'borrar_proveedor' proveedor.id %}" class="btn btn-danger btn-sm">Borrar</a>
        </td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
{% endblock %}
```

---

## `app_Caffenio/templates/proveedor/actualizar_proveedor.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Actualizar Proveedor</h2>
<form method="post" action="{% url 'realizar_actualizacion_proveedor' proveedor.id %}">
  {% csrf_token %}
  <div class="mb-3">
    <label>Nombre</label>
    <input class="form-control" name="nombre" value="{{ proveedor.nombre }}">
  </div>
  <div class="mb-3">
    <label>Empresa</label>
    <input class="form-control" name="empresa" value="{{ proveedor.empresa }}">
  </div>
  <div class="mb-3">
    <label>Teléfono</label>
    <input class="form-control" name="telefono" value="{{ proveedor.telefono }}">
  </div>
  <div class="mb-3">
    <label>Correo</label>
    <input class="form-control" name="correo" value="{{ proveedor.correo }}">
  </div>
  <div class="mb-3">
    <label>Dirección</label>
    <input class="form-control" name="direccion" value="{{ proveedor.direccion }}">
  </div>
  <div class="mb-3">
    <label>País</label>
    <input class="form-control" name="pais" value="{{ proveedor.pais }}">
  </div>
  <div class="mb-3">
    <label>Tipo de producto</label>
    <select class="form-select" name="tipo_producto">
      <option value="bebidas" {% if proveedor.tipo_producto == "bebidas" %}selected{% endif %}>Bebidas</option>
      <option value="alimentos" {% if proveedor.tipo_producto == "alimentos" %}selected{% endif %}>Alimentos</option>
      <option value="complementos" {% if proveedor.tipo_producto == "complementos" %}selected{% endif %}>Complementos</option>
    </select>
  </div>
  <button class="btn btn-primary" type="submit">Actualizar</button>
</form>
{% endblock %}
```

---

## `app_Caffenio/templates/proveedor/borrar_proveedor.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Eliminar proveedor</h2>
<p>¿Seguro que deseas eliminar a <strong>{{ proveedor.nombre }}</strong> ({{ proveedor.empresa }})?</p>
<form method="post">
  {% csrf_token %}
  <a class="btn btn-secondary" href="{% url 'ver_proveedores' %}">Cancelar</a>
  <button class="btn btn-danger" type="submit">Eliminar</button>
</form>
{% endblock %}
```

---

# 17 → En `base.html` agregar Bootstrap para css y js

(Ya incluido en `base.html` anterior: CSS y JS por CDN.)

---

# 18 → `navbar.html` con opciones y submenús (iconos solo en opciones principales)

(Ya proporcionado arriba: incluye iconos para opciones principales usando Bootstrap Icons; submenús sin iconos.)

---

# 19 → `footer.html` incluir derechos de autor, fecha del sistema y “Creado por Andrea Montoya, Cbtis 128” y mantenerla fija al final

(Ya proporcionado arriba; es fixed-bottom-like using fixed positioning.)

---

# 20 → `inicio.html` usar imagen en la página de inicio (solo ahí) con link:

Se usa: `https://cdn.forbes.com.mx/2019/04/Caffenio-1.jpg` en `inicio.html`.
(En algunos mensajes previos usaste otra URL; ahora usé la de Forbes que mencionaste originalmente.)

---

# 21 → Crear subcarpeta `proveedor` dentro de `app_Caffenio/templates`

(Estructura y archivos ya listados arriba.)

---

# 22 → Crear los archivos HTML de proveedor (agregar, ver, actualizar, borrar)

(Ya incluidos en plantillas listadas en el punto 16.)

---

# 23 → **No utilizar `forms.py`.**

Las vistas usan `request.POST` y plantillas con inputs simples — **cumplido**.

---

# 24 → `urls.py` en `app_Caffenio` con el código para CRUD proveedores

Crea `app_Caffenio/urls.py` con este contenido:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_caffenio, name='inicio'),
    path('proveedor/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedor/', views.ver_proveedores, name='ver_proveedores'),
    path('proveedor/actualizar/<int:proveedor_id>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedor/actualizar/realizar/<int:proveedor_id>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedor/borrar/<int:proveedor_id>/', views.borrar_proveedor, name='borrar_proveedor'),
]
```

---

# 25 → Procedimiento para agregar `app_Caffenio` en `backend_Caffenio/settings.py`

En `backend_Caffenio/settings.py`, busca `INSTALLED_APPS` y agrega `'app_Caffenio',`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Caffenio',
]
```

Asegúrate además de que `TEMPLATES` tenga `APP_DIRS: True` (para que Django busque plantillas en `app_Caffenio/templates`).

---

# 26 → Configuraciones en `backend_Caffenio/urls.py` para enlazar con `app_Caffenio`

Abre `backend_Caffenio/urls.py` y reemplaza/usa este contenido:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Caffenio.urls')),  # la app maneja la raíz
]
```

---

# 27 → Procedimiento para registrar los modelos en `admin.py` y volver a migrar

En `app_Caffenio/admin.py` pega:

```python
from django.contrib import admin
from .models import Proveedor, Producto, Venta

@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'empresa', 'telefono', 'correo', 'pais', 'tipo_producto')

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'precio', 'stock', 'categoria', 'proveedor', 'sucursal')

@admin.register(Venta)
class VentaAdmin(admin.ModelAdmin):
    list_display = ('id', 'fecha', 'cliente', 'total', 'metodo_pago', 'sucursal', 'empleado')
```

Luego vuelve a ejecutar:

```bash
python manage.py makemigrations
python manage.py migrate
```

Y crea superusuario:

```bash
python manage.py createsuperuser
# sigue prompts (usuario, email, contraseña)
```

---

# 27 (bis) → Por ahora trabajar solo con “Proveedor”

Aunque registramos Producto y Venta en admin, el CRUD público implementado y las plantillas están sólo para `Proveedor`. Producto y Venta quedan pendientes.

---

# 28 → Usar colores suaves, atractivo y moderno — pero manteniendo solo Bootstrap

Como pediste más tarde, **no** añadí estilos personalizados: solo utilicé Bootstrap para apariencia limpia y moderna. Si quieres colores suaves extra luego, puedo indicarlos o agregarlos, pero por ahora lo mantengo tal cual.

---

# 29 → Al inicio crear la estructura completa de carpetas y archivos

Resumen de la estructura (completa):

```
UIII_Caffenio_0647/
  .venv/
  manage.py
  backend_Caffenio/
    __init__.py
    settings.py
    urls.py
    wsgi.py
    asgi.py
  app_Caffenio/
    migrations/
    admin.py
    apps.py
    models.py
    views.py
    urls.py
    templates/
      base.html
      navbar.html
      header.html
      footer.html
      inicio.html
      proveedor/
        agregar_proveedor.html
        ver_proveedores.html
        actualizar_proveedor.html
        borrar_proveedor.html
```

---

# 30 → Proyecto totalmente funcional (prueba final)

Pasos finales:

1. Activar `.venv`.
2. Asegurarse de que `'app_Caffenio'` está en `INSTALLED_APPS`.
3. `python manage.py makemigrations`
4. `python manage.py migrate`
5. `python manage.py createsuperuser` (opcional, para admin)
6. `python manage.py runserver 8030`

Abrir `http://127.0.0.1:8030/` → ver `inicio.html` con `header.html` incluido y la imagen.
Ir a `http://127.0.0.1:8030/proveedor/agregar/` para agregar proveedores.
Ir a `http://127.0.0.1:8030/proveedor/` para ver la tabla con botones Editar y Borrar.
Admin: `http://127.0.0.1:8030/admin/`.

---

# 31 → Finalmente ejecutar servidor en el puerto 8030

```bash
python manage.py runserver 8030
```

y abre `http://127.0.0.1:8030/`.

---

## Errores comunes y soluciones rápidas

* **TemplateDoesNotExist** → Asegúrate `APP_DIRS: True` en `TEMPLATES` y que las plantillas estén en `app_Caffenio/templates/`.
* **manage.py not found** → Asegúrate de crear el proyecto con `django-admin startproject backend_Caffenio .` una sola vez.
* **PowerShell ExecutionPolicy error al activar .venv** → Ejecuta PowerShell como administrador y usa `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`.
* **No aparece el header** → Verifica que `inicio.html` tenga `{% include "header.html" %}` y que `inicio_caffenio` pase `now` (ya hecho en vistas).
* **Iconos (bi) no muestran** → Verifica que el CDN de `bootstrap-icons` esté incluido en `navbar.html` o en `base.html`.

---

## ¿Quieres que haga alguno de estos extras ahora?

* Empaquetar todo en un `.zip` listo para descargar.
* Pegar aquí **cada archivo** con su ruta exacta (ya hice la mayoría).
* Crear un *README.md* con instrucciones exactas para entregar.

Dime cuál prefieres y te lo doy **ahora mismo** (sin esperas).
