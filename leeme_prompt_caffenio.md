Perfecto — te doy **todo** resuelto paso a paso, exactamente como pide la actividad. Voy a seguir el orden numérico que diste y te entrego los comandos, el código (views, urls, templates, admin) y las instrucciones para que el proyecto quede totalmente funcional usando **Python + Django** en **VS Code**, y el servidor en **puerto 8030**. Incluyo las variantes para Windows y para macOS/Linux donde hace falta (activa la que corresponda a tu SO).

---

# 1 → Crear la carpeta del proyecto

En la ubicación donde quieras trabajar (ej. `C:\Users\TuUsuario\Documentos\` o `~/Proyectos/`):

Windows (PowerShell / CMD):

```bash
mkdir UIII_Caffenio_0647
cd UIII_Caffenio_0647
```

macOS / Linux:

```bash
mkdir -p ~/Proyectos/UIII_Caffenio_0647
cd ~/Proyectos/UIII_Caffenio_0647
```

---

# 2 → Abrir VS Code sobre la carpeta

Desde la terminal estando dentro de `UIII_Caffenio_0647`:

```bash
code .
```

(Esto abre VS Code en la carpeta actual.)

Si `code` no funciona: abre VS Code y usa *File → Open Folder…* y selecciona `UIII_Caffenio_0647`.

---

# 3 → Abrir terminal en VS Code

En VS Code: menú `Terminal → New Terminal` (o atajo `Ctrl+ñ` / `Ctrl+` `(según tu teclado) o`Ctrl+Shift+` `).

La terminal se abrirá en la ruta del workspace (UIII_Caffenio_0647).

---

# 4 → Crear carpeta de entorno virtual `.venv` desde la terminal de VS Code

Desde la terminal en la carpeta del proyecto:

Windows (PowerShell):

```powershell
python -m venv .venv
```

macOS / Linux:

```bash
python3 -m venv .venv
```

Esto crea la carpeta oculta `.venv` dentro del proyecto.

---

# 5 → Activar el entorno virtual

Windows (PowerShell):

```powershell
.venv\Scripts\Activate.ps1
# si te dice ejecución de scripts bloqueada, usa:
# Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Windows (CMD):

```cmd
.venv\Scripts\activate
```

macOS / Linux:

```bash
source .venv/bin/activate
```

Verás el prompt con `(.venv)` al inicio cuando esté activo.

---

# 6 → Activar intérprete de Python en VS Code

1. En VS Code presiona `Ctrl+Shift+P`.
2. Escribe `Python: Select Interpreter`.
3. Selecciona el intérprete dentro de `UIII_Caffenio_0647/.venv/...` (aparecerá como `.venv`).

Con eso, VS Code usará el Python del entorno virtual.

---

# 7 → Instalar Django

Con el entorno activado:

```bash
pip install django
```

(Verifica instalación: `python -m django --version` o `django-admin --version`).

---

# 8 → Crear proyecto `backend_Caffenio` sin duplicar carpeta

Para evitar crear una carpeta nested, ejecutar *desde dentro de* `UIII_Caffenio_0647`:

```bash
django-admin startproject backend_Caffenio .
```

Nota: el punto al final crea el proyecto directamente en el directorio actual (sin carpeta extra). Si ya existe `manage.py` no corras esto de nuevo (evita duplicados).

Estructura esperada ahora:

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

# 9 → Ejecutar servidor en el puerto 8030

Con el entorno virtual activado y desde la raíz del proyecto (donde está `manage.py`):

```bash
python manage.py runserver 8030
```

Números concretos: `127.0.0.1:8030` o `http://localhost:8030/`.

---

# 10 → Copiar y pegar el link en el navegador

Abre tu navegador y pega:

```
http://127.0.0.1:8030/
```

Deberías ver la página inicial de Django (“The install worked successfully...”) si aún no creaste vistas.

---

# 11 → Crear aplicación `app_Caffenio`

Desde la raíz del proyecto:

```bash
python manage.py startapp app_Caffenio
```

Estructura básica creada:

```
app_Caffenio/
  migrations/
  __init__.py
  admin.py
  apps.py
  models.py
  tests.py
  views.py
```

---

# 12 → Aquí el `models.py`

Tú ya pusiste el modelo; te lo dejo con un par de mejoras menores (importantes: conserva exactamente los campos que pediste). Reemplaza `app_Caffenio/models.py` con esto:

```python
from django.db import models

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

(Has pedido dejar Producto y Venta pendientes: vamos a registrar los modelos en admin pero solo trabajarás con `Proveedor` por ahora.)

---

# 12.5 → Procedimiento para realizar migraciones (`makemigrations` y `migrate`)

Primero, agrega la app al `settings.py` (paso 25 abajo). Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

Si agregas modelos nuevos después, vuelve a correr `makemigrations` y `migrate`.

---

# 13 → Primero trabajar con el MODELO: PROVEEDOR

Entendido: el CRUD lo hacemos para `Proveedor` y dejamos Product/Ventas para después.

---

# 14 → Views en `app_Caffenio/views.py` (funciones para CRUD de Proveedor)

Voy a darte vistas *sin* `forms.py`, usando `request.POST` y redirecciones. Copia esto en `app_Caffenio/views.py`:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor

def inicio_caffenio(request):
    return render(request, 'inicio.html')

# Agregar proveedor
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

# Ver proveedores (lista)
def ver_proveedores(request):
    proveedores = Proveedor.objects.all()
    return render(request, 'proveedor/ver_proveedores.html', {'proveedores': proveedores})

# Página para mostrar formulario de actualización
def actualizar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, id=proveedor_id)
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

# Procesar la actualización
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

# Borrar proveedor (confirmación)
def borrar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, id=proveedor_id)
    if request.method == 'POST':
        proveedor.delete()
        return redirect('ver_proveedores')
    return render(request, 'proveedor/borrar_proveedor.html', {'proveedor': proveedor})
```

Nombres de URL que usaré:

* `inicio` o `inicio_caffenio` → `'inicio'`
* `agregar_proveedor` → `'agregar_proveedor'`
* `ver_proveedores` → `'ver_proveedores'`
* `actualizar_proveedor` → `'actualizar_proveedor'`
* `realizar_actualizacion_proveedor` → `'realizar_actualizacion_proveedor'`
* `borrar_proveedor` → `'borrar_proveedor'`

---

# 15 → Crear carpeta `templates` dentro de `app_Caffenio`

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

Crea esa carpeta y subcarpeta `proveedor`.

---

# 16 → Crear archivos HTML (base, header, navbar, footer, inicio)

Te doy plantillas simples y modernas (Bootstrap). Recuerda: **no validar entrada de datos** según instrucciones.

## `app_Caffenio/templates/base.html`

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% block title %}Caffenio{% endblock %}</title>

  <!-- Bootstrap CSS (cdn) -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    /* Colores suaves, modernos */
    :root{
      --primary: #a22245;
      --secondary: #792D42;
      --bg-soft: #f6f4f6;
      --card-bg: #ffffff;
    }
    body { background: var(--bg-soft); }
    .navbar-brand { font-weight: 700; color: var(--primary) !important; }
    footer { position: fixed; bottom: 0; left: 0; right: 0; background: #fff; padding: 0.5rem 1rem; box-shadow: 0 -1px 4px rgba(0,0,0,0.08);}
    .container-main { padding-bottom: 4rem; } /* espacio para footer */
  </style>
  {% block extra_head %}{% endblock %}
</head>
<body>
  {% include 'navbar.html' %}
  <div class="container container-main mt-4">
    {% block content %}{% endblock %}
  </div>

  {% include 'footer.html' %}

  <!-- Bootstrap JS (cdn) -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  {% block extra_js %}{% endblock %}
</body>
</html>
```

## `app_Caffenio/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
  <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'inicio' %}">Sistema de Administración Caffenio</a>
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

<!-- Iconos Bootstrap Icons CDN -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.css">
```

> Nota: incluí iconos **solo** en las opciones principales (no en submenú), como pediste.

## `app_Caffenio/templates/footer.html`

```html
<footer class="text-center">
  <div class="container d-flex justify-content-between">
    <div>© {{ now.year }} Caffenio</div>
    <div>Creado por Andrea Montoya, Cbtis 128</div>
  </div>
</footer>
```

(Django template variable `now` se puede pasar desde la vista `inicio_caffenio` o usar `django.template.context_processors.request` + `now`—pero para simplicidad, en `inicio_caffenio` pasaré la fecha.)

## `app_Caffenio/templates/header.html`

(No es estrictamente necesario si ya tienes navbar; deja archivo si quieres fragmentos. Lo omitimos en las includes para simplicidad.)

## `app_Caffenio/templates/inicio.html`

```html
{% extends "base.html" %}

{% block title %}Inicio - Caffenio{% endblock %}

{% block content %}
<div class="row">
  <div class="col-md-6">
    <h1>Bienvenido a Caffenio</h1>
    <p>Sistema de administración para proveedores, productos y ventas.</p>
  </div>
  <div class="col-md-6">
    <img src="https://cdn.forbes.com.mx/2019/04/Caffenio-1.jpg" alt="Caffenio" class="img-fluid rounded">
  </div>
</div>
{% endblock %}
```

> Importante: en la vista `inicio_caffenio` pondremos `{'now': datetime.now()}` para que el footer muestre año.

---

# 17 → Agregar Bootstrap para CSS y JS en `base.html`

Ya incluí los CDN de Bootstrap CSS y JS en `base.html` (ver arriba). También incluí `bootstrap-icons` en `navbar.html`.

---

# 18 → `navbar.html` opciones y submenus con iconos

Ya implementado arriba: opciones principales con iconos (`bi bi-people`, `bi bi-box-seam`, `bi bi-cash-stack`) y submenús sin iconos. Cumple tu requisito.

---

# 19 → `footer.html` con derechos de autor, fecha y “Creado por Andrea Montoya, Cbtis 128”, fija al final

Ya implementado en `footer.html`. Para que muestre la fecha actual, modifica `inicio_caffenio` para enviar `now`.

En `app_Caffenio/views.py` cambia `inicio_caffenio` a:

```python
from datetime import datetime

def inicio_caffenio(request):
    return render(request, 'inicio.html', {'now': datetime.now()})
```

---

# 20 → `inicio.html` usa la imagen con el link dado

Ya la añadí: `https://cdn.forbes.com.mx/2019/04/Caffenio-1.jpg`.

---

# 21 → Crear subcarpeta `proveedor` dentro de `templates`

La estructura ya indicada incluye `templates/proveedor/`. Crea esa carpeta y guarda las vistas HTML allí.

---

# 22 → Archivos HTML para proveedores

## `templates/proveedor/agregar_proveedor.html`

```html
{% extends "base.html" %}
{% block title %}Agregar Proveedor{% endblock %}
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

## `templates/proveedor/ver_proveedores.html`

```html
{% extends "base.html" %}
{% block title %}Ver Proveedores{% endblock %}
{% block content %}
<h2>Proveedores</h2>
<table class="table table-striped">
  <thead>
    <tr>
      <th>Nombre</th>
      <th>Empresa</th>
      <th>Teléfono</th>
      <th>Correo</th>
      <th>País</th>
      <th>Tipo</th>
      <th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for p in proveedores %}
    <tr>
      <td>{{ p.nombre }}</td>
      <td>{{ p.empresa }}</td>
      <td>{{ p.telefono }}</td>
      <td>{{ p.correo }}</td>
      <td>{{ p.pais }}</td>
      <td>{{ p.get_tipo_producto_display }}</td>
      <td>
        <a class="btn btn-sm btn-info" href="{% url 'actualizar_proveedor' p.id %}">Editar</a>
        <a class="btn btn-sm btn-secondary" href="{% url 'borrar_proveedor' p.id %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="7">No hay proveedores registrados.</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

## `templates/proveedor/actualizar_proveedor.html`

```html
{% extends "base.html" %}
{% block title %}Actualizar Proveedor{% endblock %}
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

## `templates/proveedor/borrar_proveedor.html`

```html
{% extends "base.html" %}
{% block title %}Borrar Proveedor{% endblock %}
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

# 23 → No utilizar `forms.py`

Cumplido: las vistas usan `request.POST` y templates con inputs simples (sin `forms.py`).

---

# 24 → Crear `urls.py` en `app_Caffenio` y rutas CRUD

Crea `app_Caffenio/urls.py` con:

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

# 25 → Agregar `app_Caffenio` en `settings.py` de `backend_Caffenio`

Abre `backend_Caffenio/settings.py` y en `INSTALLED_APPS` agrega `'app_Caffenio',` y asegúrate de que `django.template.context_processors.request` esté en `TEMPLATES` → `OPTIONS` → `context_processors` (para seguridad y utilidades). Ejemplo:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    ...
    'app_Caffenio',
]
```

---

# 26 → Configurar `urls.py` de `backend_Caffenio` para enlazar con `app_Caffenio`

En `backend_Caffenio/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Caffenio.urls')),  # la app maneja la raíz
]
```

---

# 27 → Registrar modelos en `admin.py` y volver a migrar

En `app_Caffenio/admin.py`:

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

Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

Y crea superusuario para acceder a admin:

```bash
python manage.py createsuperuser
# sigue los prompts (usuario, email, contraseña)
```

---

# 27 (bis) → Sólo trabajar con “Proveedor” por ahora

Aunque registramos Producto y Venta en admin, el CRUD público implementado es **solo para Proveedor**, como pediste. Producto/Venta quedan pendientes.

---

# 28 → Estética: colores suaves y diseño sencillo

En `base.html` definí variables CSS con tonos suaves y usé Bootstrap para UI limpia y responsive.

---

# 29 → Al inicio crear la estructura completa de carpetas y archivos

Resumen de estructura que debes tener cuando termines:

```
UIII_Caffenio_0647/
  .venv/
  manage.py
  backend_Caffenio/
    settings.py
    urls.py
    ...
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
      footer.html
      inicio.html
      proveedor/
        agregar_proveedor.html
        ver_proveedores.html
        actualizar_proveedor.html
        borrar_proveedor.html
```

---

# 30 → Proyecto totalmente funcional

Pasos finales para comprobar que todo funciona:

1. Entorno activo (`.venv`).
2. `INSTALLED_APPS` incluye `app_Caffenio`.
3. Migraciones hechas (`makemigrations`, `migrate`).
4. Ejecutar:

```bash
python manage.py runserver 8030
```

5. Abre `http://127.0.0.1:8030/` → Verás la página de inicio.
6. Navega a `Proveedores → Agregar Proveedor` para crear registros.
7. Ve a `Proveedores → Ver Proveedores` para ver la tabla con botones Editar y Borrar.
8. Admin: `http://127.0.0.1:8030/admin/` (usa superuser).

---

# 31 → Ejecutar servidor en puerto 8030 (final)

Repite el comando final:

```bash
python manage.py runserver 8030
```

Y confirma en el navegador: `http://127.0.0.1:8030/`.

---

## Notas importantes y recomendaciones finales (para que no falles)

* **No validar entrada**: según tu requerimiento no añadí validaciones; ten en cuenta que en producción sí se deben validar campos.
* Si al crear proyecto tu `manage.py` ya existía, **no vuelvas a** `startproject backend_Caffenio .` porque duplicarías.
* Si ves error de *TemplateDoesNotExist*, revisa `TEMPLATES` en `settings.py` para que busque en `DIRS` o asegúrate que las plantillas estén en `app_Caffenio/templates/` y `APP_DIRS: True` en `settings.py`.
* Si ves error al activar `.venv` en PowerShell: ejecuta `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned` en PowerShell con permisos de admin (si tu escuela/PC lo permite).
* Para que `footer.html` muestre el año, pasé `now` desde la vista `inicio_caffenio`.

---

¿Quieres que te entregue **los archivos listos** (por ejemplo un `.zip` o te pego todo en archivos separados para copiar/pegar)? Puedo prepararte el contenido listo para pegar en cada archivo. ¿Te los entrego así ahora?
