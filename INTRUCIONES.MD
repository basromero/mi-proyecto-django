1️⃣ Crear la carpeta contenedora y entrar en ella
mkdir ProyectoFront
cd  C:\Users\Bastian\Desktop\trabajin(Ruta de la carpeta)
------------------------------------------------------------------------------------------------------------------------------------------------------------------
2️⃣ Crear y activar un ambiente virtual
# Crear ambiente virtual
py -m venv .venv
------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Activarlo (PowerShell)
.\.venv\Scripts\Activate.ps1

# (Opcional) Si falla la activación por políticas:
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
.\.venv\Scripts\Activate.ps1

3️⃣ Instalar Django dentro del ambiente
pip install django
pip freeze > requirements.txt
------------------------------------------------------------------------------------------------------------------------------------------------------------------
4️⃣ Crear el proyecto Django
django-admin startproject proyectofront .
------------------------------------------------------------------------------------------------------------------------------------------------------------------
5️⃣ Crear la app principal
python manage.py startapp aplicacion
------------------------------------------------------------------------------------------------------------------------------------------------------------------
6️⃣ Crear carpeta de templates dentro de la app
mkdir aplicacion\templates
mkdir aplicacion\templates\aplicacion

Crear el archivo home.html:

New-Item aplicacion\templates\aplicacion\home.html -ItemType File

New-Item aplicacion\templates\aplicacion\about.html -ItemType File


Después tendríamos que abrir visual studio y abrir nuestra carpeta y nos dirigimos a aplicacion y dentro de aplicacion nos vamos a template y colocamos el código HTML que esta abajo

<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Inicio ProyectoFront</title>
</head>
<body>
  <h1>¡Hola, Django funciona con templates!</h1>
  <p>Esta es la página de inicio de tu app Pfront.</p>
</body>
</html>
------------------------------------------------------------------------------------------------------------------------------------------------------------------
7️⃣ Definir la vista en aplicacion/views.py

from django.shortcuts import render
# Create your views here.
def home(request):
    return render(request, 'aplicacion/home.html')
def about(request):
    return render(request, 'aplicacion/about.html')
------------------------------------------------------------------------------------------------------------------------------------------------------------------
8️⃣ Crear rutas de la app aplicacion

Archivo aplicacion/urls.py:

from django.contrib import admin
from django.urls import path, include
from . import views
app_name = 'aplicacion'
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
    path('about/', views.about, name='about'),
]
------------------------------------------------------------------------------------------------------------------------------------------------------------------
9️⃣ Conectar la app al proyecto principal

En proyectofront/ copiamos el archivo urls.py y después nos dirigimos a la carpeta Pfront(como se llame tu app)y lo pegas dentro de la carpeta

Esta es la estructura que nos debriamos pegar 
from django.contrib import admin
from django.urls import path
from . import views 

app_name = 'Pfront'
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
]
------------------------------------------------------------------------------------------------------------------------------------------------------------------
🔟 Configurar settings.py para templates y registrar la app

En proyectofront/settings.py:

Agregar la app en INSTALLED_APPS:

INSTALLED_APPS = [
    # apps por defecto...
    'aplicacion',
]

Despus en powersshell corremos estos comandos
python manage.py migrate   # preparar base de datos inicial
python manage.py runserver
(Para asegurarnos de que corren las páginas web y el servidor)



Configurar la sección TEMPLATES para que use templates dentro de apps (generalmente ya está así):

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],  # vacío porque usas templates dentro de apps
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


------------------------------------------------------------------------------------------------------------------------------------------------------------------
1️⃣1️⃣ Crear el modelo de datos en aplicacion/models.py

from django.db import models

class Cliente(models.Model):
    id_cliente = models.CharField(primary_key=True, max_length=5)
    cli_nombre = models.CharField(max_length=40)
    cli_contacto = models.CharField(max_length=30)
    cli_titulo_contacto = models.CharField(max_length=30)
    cli_direccion = models.CharField(max_length=60)
    cli_ciudad = models.CharField(max_length=15)
    cli_region = models.CharField(max_length=15)
    cli_codigo_postal = models.CharField(max_length=10)
    cli_pais = models.CharField(max_length=15)
    cli_telefono = models.CharField(max_length=24)
    cli_mail = models.CharField(max_length=24)

    def __str__(self):
        return self.cli_nombre

class Empleado(models.Model):
    id_empleado = models.BigAutoField(primary_key=True)
    emp_nombre = models.CharField(max_length=20)
    emp_apellido = models.CharField(max_length=10)
    emp_titulo = models.CharField(max_length=30)
    emp_mail = models.CharField(max_length=25)
    emp_fecha_nac = models.DateField()
    emp_fecha_contrato = models.DateField()
    emp_direccion = models.CharField(max_length=60)
    emp_celular = models.CharField(max_length=24)
    emp_foto = models.BinaryField()
    emp_notas = models.TextField()

    def __str__(self):
        return f"{self.emp_nombre} {self.emp_apellido}"

class Categoria(models.Model):
    id_categoria = models.BigAutoField(primary_key=True)
    cat_nombre = models.CharField(max_length=15)
    cat_desc = models.TextField()
    cat_foto = models.BinaryField()

    def __str__(self):
        return self.cat_nombre

class Producto(models.Model):
    id_producto = models.BigAutoField(primary_key=True)
    id_categoria = models.ForeignKey(Categoria, on_delete=models.CASCADE)
    pro_nombre = models.CharField(max_length=40)
    pro_descripcion = models.CharField(max_length=30)
    pro_precio = models.DecimalField(max_digits=19, decimal_places=4)
    pro_stock = models.IntegerField()

    def __str__(self):
        return self.pro_nombre

class Orden(models.Model):
    id_orden = models.BigAutoField(primary_key=True)
    id_cliente = models.ForeignKey(Cliente, on_delete=models.CASCADE)
    id_empleado = models.ForeignKey(Empleado, on_delete=models.CASCADE)
    ord_fecha = models.DateField()

    def __str__(self):
        return f"Orden {self.id_orden}"

class DetalleOrden(models.Model):
    id_detord = models.BigAutoField(primary_key=True)
    id_producto = models.ForeignKey(Producto, on_delete=models.CASCADE)
    deo_precio = models.FloatField()
    deo_cantidad = models.IntegerField()
    id_orden = models.ForeignKey(Orden, on_delete=models.CASCADE)

    def __str__(self):
        return f"Detalle {self.id_detord} - Producto {self.id_producto.pro_nombre}"

class Proveedor(models.Model):
    id_proveedor = models.BigAutoField(primary_key=True)
    prv_empresa = models.CharField(max_length=40)
    prv_giro = models.CharField(max_length=20)
    prv_contacto = models.CharField(max_length=30)
    prv_direccion = models.CharField(max_length=60)
    prv_pco_postal = models.CharField(max_length=10)
    prv_telefono = models.CharField(max_length=24)

    def __str__(self):
        return self.prv_empresa
------------------------------------------------------------------------------------------------------------------------------------------------------------------
1️⃣2️⃣ Registrar el modelo en admin para gestionarlo desde la interfaz web

Editar aplicacion/admin.py y añadir:


from django.contrib import admin
from .models import Cliente, Empleado, Categoria, Producto, Orden, DetalleOrden, Proveedor
@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('id_cliente', 'cli_nombre', 'cli_ciudad', 'cli_pais', 'cli_mail')
    search_fields = ('cli_nombre', 'cli_ciudad', 'cli_pais')
@admin.register(Empleado)
class EmpleadoAdmin(admin.ModelAdmin):
    list_display = ('id_empleado', 'emp_nombre', 'emp_apellido', 'emp_mail', 'emp_fecha_nac')
    search_fields = ('emp_nombre', 'emp_apellido', 'emp_mail')
@admin.register(Categoria)
class CategoriaAdmin(admin.ModelAdmin):
    list_display = ('id_categoria', 'cat_nombre')
    search_fields = ('cat_nombre',)
@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('id_producto', 'pro_nombre', 'id_categoria', 'pro_precio', 'pro_stock')
    list_filter = ('id_categoria',)
    search_fields = ('pro_nombre',)
@admin.register(Orden)
class OrdenAdmin(admin.ModelAdmin):
    list_display = ('id_orden', 'id_cliente', 'id_empleado', 'ord_fecha')
    list_filter = ('ord_fecha',)
    search_fields = ('id_cliente__cli_nombre', 'id_empleado__emp_nombre')
@admin.register(DetalleOrden)
class DetalleOrdenAdmin(admin.ModelAdmin):
    list_display = ('id_detord', 'id_producto', 'deo_precio', 'deo_cantidad', 'id_orden')
    search_fields = ('id_producto__pro_nombre',)
@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('id_proveedor', 'prv_empresa', 'prv_contacto', 'prv_telefono')
    search_fields = ('prv_empresa', 'prv_contacto')
------------------------------------------------------------------------------------------------------------------------------------------------------------------
1️⃣3️⃣ Generar scripts de migración

Una vez que el proyecto se crea, lo hace conjuntamente con la base de datos que está en el directorio del proyecto donde se encuentra manage.py, de esta forma una vez creado el modelo en models.py de la aplicación se realiza la creación de los scripts para una migración con el fin de aplicar modelo en el archivo de base de datos llamado db.sqlite3.

(Opcional) Podemos instalar en vs code la extensión de SQLITE para así poder visualizar los datos dentro de vs code

Luego ejecutamos la migración con el comando:

python manage.py makemigrations

Luego al crear la base de datos

Para aplicar el script creado en la base de datos se debe ejecutar el siguiente comando:

python manage.py migrate

------------------------------------------------------------------------------------------------------------------------------------------------------------------

1️⃣4️⃣ Crear un superusuario

python manage.py createsuperuser

Sigue las instrucciones para definir usuario y contraseña.

------------------------------------------------------------------------------------------------------------------------------------------------------------------

1️⃣5️⃣ Ejecutar el servidor y probar
python manage.py runserver


Acceder a la página principal:
http://127.0.0.1:8000/

Acceder al admin y gestionar productos:
http://127.0.0.1:8000/admin/


------------------------------------------------------------------------------------------------------------------------------------------------------------------

Requisitos de entorno
Python: 3.8 o superior (idealmente 3.10+).
Django: versión estable (por ejemplo, 4.2 o 5.0).
pip: para instalar dependencias.
Virtualenv (opcional, pero recomendable): para aislar el entorno del proyecto.
SQLite: como base de datos por defecto (ya viene con Django).
requirements.txt con dependencias del proyecto (puede incluir Django, gunicorn, etc.).
