
Perfecto — aquí tienes la **Primera parte** del proyecto **Caffenio** (Python + Django) lista, paso a paso y con todo el código necesario para dejar funcionando la parte de **Proveedor** (CRUD) y la estructura de carpetas/archivos que pediste. Lo entrego todo en español y listo para copiar/pegar en VS Code.

---

# 1 — Crear carpeta del proyecto

En tu terminal (PowerShell o CMD) o desde el explorador:

```bash
mkdir UIII_Caffenio_0647
cd UIII_Caffenio_0647
```

# 2 — Abrir VS Code en esa carpeta

Desde la misma terminal:

```bash
code .
```

(Esto abre VS Code en `UIII_Caffenio_0647`).

# 3 — Abrir terminal dentro de VS Code

En VS Code: `Terminal` → `New Terminal` (o `Ctrl+ñ` / `Ctrl+` ` (según tu teclado)).

# 4 — Crear carpeta entorno virtual “.venv” desde terminal de VS Code

En la terminal:

```bash
python -m venv .venv
```

Esto creará la carpeta `.venv` dentro de `UIII_Caffenio_0647`.

# 5 — Activar el entorno virtual

* Windows (PowerShell):

```powershell
.venv\Scripts\Activate.ps1
```

* Windows (CMD):

```cmd
.venv\Scripts\activate
```

* Linux / macOS:

```bash
source .venv/bin/activate
```

# 6 — Activar intérprete de Python (en VS Code)

En VS Code presiona `Ctrl+Shift+P` → escribe `Python: Select Interpreter` → selecciona el que apunta a `UIII_Caffenio_0647/.venv/...`. Esto asegura que VS Code use el `.venv`.

# 7 — Instalar Django

Con el entorno activado:

```bash
pip install django
```

(Verifica con `django-admin --version`).

# 8 — Crear proyecto `backend_Caffenio` sin duplicar carpeta

Dentro de `UIII_Caffenio_0647` ejecuta:

```bash
django-admin startproject backend_Caffenio .
```

**Importante:** el punto `.` crea el proyecto en la carpeta actual sin crear una subcarpeta adicional.

# 9 — Ejecutar servidor en el puerto 8030

Primero crear la app (ver paso 11). Para ejecutar:

```bash
python manage.py runserver 8030
```

# 10 — Copiar y pegar el link en el navegador

Abre en tu navegador:

```
http://127.0.0.1:8030/
```

o la dirección que muestre la terminal.

# 11 — Crear aplicación `app_Caffenio`

Con el entorno activado:

```bash
python manage.py startapp app_Caffenio
```

Esto crea la carpeta `app_Caffenio`.

---

## Estructura inicial recomendada (al inicio crear toda la estructura)

```
UIII_Caffenio_0647/
├─ .venv/
├─ backend_Caffenio/
│  ├─ __init__.py
│  ├─ settings.py
│  ├─ urls.py
│  └─ wsgi.py
├─ manage.py
├─ app_Caffenio/
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ header.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ proveedor/
│  │     ├─ agregar_proveedor.html
│  │     ├─ ver_proveedores.html
│  │     ├─ actualizar_proveedor.html
│  │     └─ borrar_proveedor.html
│  ├─ static/   (opcional)
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ views.py
│  └─ urls.py
```

---

# 12 — Migraciones (makemigrations y migrate)

Después de registrar la app y modelos (ver más abajo), ejecutar:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

# 13 — Empezamos con el MODELO: PROVEEDOR

Has proporcionado ya el `models.py`. Asegúrate de que tu `app_Caffenio/models.py` contenga al menos la clase `Proveedor`. (Yo lo dejo tal cual lo enviaste; además tendrás las clases Producto y Venta para dejar pero por ahora trabajamos sólo Proveedor).

Reproduzco aquí sólo `Proveedor` (para que lo copies en `app_Caffenio/models.py` si aún no está):

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
```

> Nota: Dejas los modelos `Producto` y `Venta` en el archivo (como los compartiste) pero por ahora sólo trabajaremos funcionalmente con `Proveedor`.

---

# 14 — `views.py` de `app_Caffenio` — funciones para Proveedor (inicio_caffenio, agregar_proveedor, actualizar_proveedor, realizar_actualizacion_proveedor, borrar_proveedor)

Pega este contenido en `app_Caffenio/views.py` (reemplazando lo que haya o agregando):

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor
from django.urls import reverse
from django.views.decorators.csrf import csrf_exempt

# Página de inicio del sistema
def inicio_caffenio(request):
    return render(request, 'inicio.html')

# Agregar proveedor (muestra el formulario y procesa POST)
def agregar_proveedor(request):
    if request.method == 'POST':
        # No se hacen validaciones según instrucciones
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

    # GET -> mostrar formulario
    return render(request, 'proveedor/agregar_proveedor.html')

# Ver todos los proveedores
def ver_proveedores(request):
    proveedores = Proveedor.objects.all().order_by('id')
    return render(request, 'proveedor/ver_proveedores.html', {'proveedores': proveedores})

# Mostrar formulario para actualizar (GET)
def actualizar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

# Procesar la actualización (POST)
def realizar_actualizacion_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)

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

    # si no es POST, redirigir a ver
    return redirect('ver_proveedores')

# Borrar proveedor (confirmación GET y borrado POST)
def borrar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    if request.method == 'POST':
        proveedor.delete()
        return redirect('ver_proveedores')
    return render(request, 'proveedor/borrar_proveedor.html', {'proveedor': proveedor})
```

> Comentario: No usamos `forms.py` (cumple la instrucción 23). Usamos plantillas HTML con `<form method="post">` y `csrf_token`.

---

# 15 — Crear carpeta `templates` dentro de `app_Caffenio`

En `app_Caffenio/` crea `templates/` y dentro los archivos indicados. (Ver estructura antes).

Asegúrate de que en `backend_Caffenio/settings.py` tengas:

```python
# settings.py (fragmento relevante)
INSTALLED_APPS = [
    # ... otros apps ...
    'app_Caffenio',
]

# Si usas TEMPLATE DIRS personalizados, por lo general Django detecta templates dentro de cada app/templates
```

# 16 — Archivos html necesarios

Crea los siguientes archivos en `app_Caffenio/templates/` y `app_Caffenio/templates/proveedor/`.

---

## `base.html`

`app_Caffenio/templates/base.html`

```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Caffenio - Sistema de Administración</title>

    <!-- Bootstrap CSS (CDN) -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">

    <style>
      body { font-family: "Times New Roman", Times, serif; background: #f8f7fb; }
      .brand { font-family: "Dancing Script", cursive; font-size: 1.8rem; color: #5b2e8a; }
      .footer-fixed { position: fixed; left:0; bottom:0; width:100%; background:#faf7ff; padding:10px 0; border-top:1px solid #eee; }
    </style>

    <!-- Bootstrap JS (CDN) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  </head>
  <body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}

    <main class="container my-4">
      {% block content %}{% endblock %}
    </main>

    {% include 'footer.html' %}
  </body>
</html>
```

---

## `header.html`

`app_Caffenio/templates/header.html`

```html
<header class="py-2 bg-light border-bottom">
  <div class="container d-flex align-items-center">
    <div class="brand">MelodyRinku - Caffenio</div>
  </div>
</header>
```

(El logo en Dancing Script; la fuente Dancing Script no está importada aquí para mantener simple; si quieres, se puede agregar Google Fonts).

---

## `navbar.html`

`app_Caffenio/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
  <div class="container">
    <a class="navbar-brand d-flex align-items-center" href="{% url 'inicio' %}">
      <span style="color:#6a38a6; font-weight:700;">☕</span>
      <span class="ms-2">Sistema de Administración Caffenio</span>
    </a>

    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#mainNav">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="mainNav">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Proveedor</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedores' %}">Ver Proveedores</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Productos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar productos</a></li>
            <li><a class="dropdown-item" href="#">Ver productos</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Ventas</a>
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

> Iconos: agregué un icono simple (☕). Puedes reemplazar por `img` si deseas.

---

## `footer.html`

`app_Caffenio/templates/footer.html`

```html
<footer class="footer-fixed text-center">
  <div class="container">
    <small>&copy; {{ now.year }} - Creado por Andrea Montoya, Cbtis 128. Todos los derechos reservados.</small>
  </div>
</footer>
```

> Para que `now` funcione, en cada render puedes pasar `{'now': timezone.now()}` o habilitar `django.template.context_processors.request` y usar `{{ now.year }}` con contexto. Alternativamente reemplaza con año fijo si prefieres.

---

## `inicio.html`

`app_Caffenio/templates/inicio.html`

```html
{% extends 'base.html' %}

{% block content %}
  <div class="row">
    <div class="col-md-8">
      <h2>Bienvenido al Sistema Caffenio</h2>
      <p>Sistema de administración simple para el negocio Caffenio. Use el menú para navegar entre Proveedores, Productos y Ventas.</p>
    </div>
    <div class="col-md-4">
      <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/Cinepolis_building.jpg" alt="Cinepolis" class="img-fluid rounded">
    </div>
  </div>
{% endblock %}
```

(Imagen tomada desde la red — ejemplo; puedes cambiar la URL).

---

# 21 — Carpeta `proveedor` dentro de templates

Creamos `app_Caffenio/templates/proveedor/` y los archivos siguientes.

---

## `agregar_proveedor.html`

`app_Caffenio/templates/proveedor/agregar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Agregar Proveedor</h3>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label class="form-label">Nombre</label>
    <input class="form-control" name="nombre">
  </div>
  <div class="mb-3">
    <label class="form-label">Empresa</label>
    <input class="form-control" name="empresa">
  </div>
  <div class="mb-3">
    <label class="form-label">Teléfono</label>
    <input class="form-control" name="telefono">
  </div>
  <div class="mb-3">
    <label class="form-label">Correo</label>
    <input type="email" class="form-control" name="correo">
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
  <button class="btn btn-primary" type="submit">Guardar</button>
</form>
{% endblock %}
```

---

## `ver_proveedores.html`

`app_Caffenio/templates/proveedor/ver_proveedores.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Proveedores</h3>
<table class="table table-striped">
  <thead>
    <tr>
      <th>ID</th><th>Nombre</th><th>Empresa</th><th>Tel</th><th>Correo</th><th>País</th><th>Tipo</th><th>Acciones</th>
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
        <a class="btn btn-sm btn-info" href="{% url 'actualizar_proveedor' p.id %}">Editar</a>
        <a class="btn btn-sm btn-danger" href="{% url 'borrar_proveedor' p.id %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="8">No hay proveedores.</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

---

## `actualizar_proveedor.html`

`app_Caffenio/templates/proveedor/actualizar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Actualizar Proveedor</h3>
<form method="post" action="{% url 'realizar_actualizacion_proveedor' proveedor.id %}">
  {% csrf_token %}
  <div class="mb-3"><label class="form-label">Nombre</label><input class="form-control" name="nombre" value="{{ proveedor.nombre }}"></div>
  <div class="mb-3"><label class="form-label">Empresa</label><input class="form-control" name="empresa" value="{{ proveedor.empresa }}"></div>
  <div class="mb-3"><label class="form-label">Teléfono</label><input class="form-control" name="telefono" value="{{ proveedor.telefono }}"></div>
  <div class="mb-3"><label class="form-label">Correo</label><input class="form-control" name="correo" value="{{ proveedor.correo }}"></div>
  <div class="mb-3"><label class="form-label">Dirección</label><input class="form-control" name="direccion" value="{{ proveedor.direccion }}"></div>
  <div class="mb-3"><label class="form-label">País</label><input class="form-control" name="pais" value="{{ proveedor.pais }}"></div>

  <div class="mb-3">
    <label class="form-label">Tipo de producto</label>
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

## `borrar_proveedor.html`

`app_Caffenio/templates/proveedor/borrar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Borrar Proveedor</h3>
<p>¿Desea borrar al proveedor <strong>{{ proveedor.nombre }} - {{ proveedor.empresa }}</strong>?</p>
<form method="post">
  {% csrf_token %}
  <a href="{% url 'ver_proveedores' %}" class="btn btn-secondary">Cancelar</a>
  <button type="submit" class="btn btn-danger">Borrar</button>
</form>
{% endblock %}
```

---

# 22 — (Has pedido archivos para categoría, pero conforme a la estructura y pasos previos trabajamos con **Proveedor**).

He creado las páginas correspondientes a **Proveedor**: `agregar_proveedor.html`, `ver_proveedores.html`, `actualizar_proveedor.html`, `borrar_proveedor.html`.

# 23 — No utilizar `forms.py`

No lo usamos. Las vistas leen `request.POST` directamente tal como pediste (sin validación).

# 24 — `urls.py` en `app_Caffenio`

Crea `app_Caffenio/urls.py` con:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_caffenio, name='inicio'),
    path('proveedor/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedor/ver/', views.ver_proveedores, name='ver_proveedores'),
    path('proveedor/actualizar/<int:proveedor_id>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedor/actualizar/procesar/<int:proveedor_id>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedor/borrar/<int:proveedor_id>/', views.borrar_proveedor, name='borrar_proveedor'),
]
```

# 25 — Agregar `app_Caffenio` en `settings.py`

En `backend_Caffenio/settings.py`, dentro de `INSTALLED_APPS` añade:

```python
INSTALLED_APPS = [
    # ... otros ...
    'app_Caffenio',
]
```

# 26 — Configurar `backend_Caffenio/urls.py` para enlazar con `app_Caffenio`

En `backend_Caffenio/urls.py` modifica:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Caffenio.urls')),  # enruta la app al root
]
```

# 27 — Registrar modelos en `admin.py` y migraciones

En `app_Caffenio/admin.py` añade:

```python
from django.contrib import admin
from .models import Proveedor, Producto, Venta

admin.site.register(Proveedor)
# registrar Producto y Venta si ya existen en models.py
admin.site.register(Producto)
admin.site.register(Venta)
```

Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

# 27 (bis) — Crear superusuario (opcional para acceder al admin)

```bash
python manage.py createsuperuser
```

Sigue los pasos para usuario/contraseña/email.

# 28 — Estilos: colores suaves, modernos

El `base.html` incorpora tonos morados suaves (`#6a38a6`) y fondo claro. Puedes ampliar CSS en `app_Caffenio/static/css/styles.css` y enlazarlo desde `base.html`.

# 29 — Al inicio crear la estructura completa de carpetas y archivos

(Ya listada en la estructura arriba). Crea `templates/proveedor/` y archivos. Crea `static/` si vas a agregar CSS/JS.

# 30 — Proyecto totalmente funcional (con lo solicitado)

Con lo anterior deberías poder:

* Agregar proveedores
* Ver lista de proveedores (tabla)
* Editar proveedor
* Borrar proveedor
* Navegar con la barra de menú y acceder a `inicio`

# 31 — Ejecutar servidor en el puerto 8030

Finalmente:

```bash
python manage.py runserver 8030
```

Abrir: `http://127.0.0.1:8030/`

---

## Resumen de archivos clave para copiar/pegar

* `app_Caffenio/models.py` — (tu modelo completo: Proveedor, Producto, Venta).
* `app_Caffenio/views.py` — vistas que te puse arriba.
* `app_Caffenio/urls.py` — rutas de la app.
* `backend_Caffenio/urls.py` — `path('', include('app_Caffenio.urls'))`
* `backend_Caffenio/settings.py` — añadir `'app_Caffenio'` en INSTALLED_APPS.
* Templates en `app_Caffenio/templates/` y `app_Caffenio/templates/proveedor/` — como los listados arriba.
* `app_Caffenio/admin.py` — registrar modelos.

---

Si quieres, en la siguiente entrega (segunda parte) te doy:

* Los archivos exactamente listos para pegar (yo puedo generar cada archivo con contenido completo).
* Ajustes para static files, añadir logo con Dancing Script desde Google Fonts, y pequeños JS para mejorar UX (si lo permites).
* Implementación de Producto y Venta y relaciones en formularios (sin `forms.py`).

¿Quieres que genere ahora los archivos listos para descargar y te los coloque en un ZIP? (puedo generar el contenido para que copies — dime si prefieres que los coloque íntegros uno por uno).
