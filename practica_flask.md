
## Antes de empezar con la demostración de la ___app web___, vamos a diseñar una práctica introductoria paso a paso. 

Esta aplicación constará de dos páginas:

- Página de Bienvenida / Inicio (index.html): Presenta la estructura básica con Bootstrap y un formulario de registro.

- Página de Confirmación / Listado (success.html): Muestra los datos que el usuario acaba de registrar y permite ver los registros acumulados tanto en MySQL (base de datos relacional) como en MongoDB (base de datos NoSQL).

## Requisitos Previos y Entorno de Trabajo
Antes de escribir código, asegúrate de tener instalado en las computadoras del laboratorio (o en las máquinas de los alumnos):

- Python (versión 3.8 o superior).
- MySQL Server (o XAMPP/WampServer que lo incluya).
- MongoDB (puede ser local o un cluster gratuito en MongoDB Atlas).

## 1. Crear un fichero en donde guardarmos la practica y creamos un [entorno virtual](inst_&_set.md).

## 2. Instalación de Dependencias
Los alumnos deben abrir su terminal (o consola) e instalar Flask y los conectores para bases de datos ejecutando:

  ```sh
  pip install flask pymysql mysql-connector-python pymongo
```

- Flask: Microframework de Python para crear la app web.
- PyMySQL / mysql-connector-python: Librerías para conectar Python con MySQL.
- PyMongo: Librería oficial para conectar Python con MongoDB.

## 📂 Estructura del Proyecto
Crea una carpeta llamada app_introduccion y dentro de ella la siguiente estructura de archivos y carpetas:

<img src="./image/estructura_practica.png" alt="Logo del proyecto" width="600"/>

## Parte 1: Conceptos Teóricos Clave para los Alumnos
Antes de codificar, explícales brevemente estos tres pilares:

___¿Qué es Flask?___ Es un "microframework" web de Python. Se llama micro porque es ligero y minimalista: nos da las herramientas básicas para escuchar peticiones web (GET, POST) y nosotros decidimos qué librerías agregar.

¿Por qué usar Bootstrap? Es un framework de CSS creado por Twitter. Nos ahorra diseñar botones, formularios y barras de navegación desde cero usando clases prediseñadas (ej. class="btn btn-primary").

___MySQL___ vs. ___MongoDB___ (Relacional vs. NoSQL):

___MySQL___ guarda la información en tablas con filas y columnas estrictas (esquema rígido). Ideal para datos muy estructurados.

___MongoDB___ guarda la información en documentos (formato similar a JSON, llamados BSON) dentro de colecciones. No exige un esquema fijo, lo que lo hace muy flexible.

## Parte 2: Creación del Código

Paso 1: El archivo principal (app.py)

Este archivo contiene las rutas web y la lógica para conectarnos a ambas bases de datos simultáneamente.

> Nota pedagógica: Asegúrate de cambiar las credenciales (root, contraseña, localhost) según la configuración del servidor de los alumnos.

```sh
from flask import Flask, render_template, request, redirect, url_for
import mysql.connector
from pymongo import MongoClient

app = Flask(__name__)

# ==========================================
# CONFIGURACIÓN DE CONEXIÓN A BASES DE DATOS
# ==========================================

# 1. Configuración de MySQL (Relacional)
def get_mysql_connection():
    return mysql.connector.connect(
        host="localhost",
        user="root",
        password="",          # Cambiar si tu MySQL tiene contraseña
        database="escuela_db" # Asegúrate de haber creado esta BD previamente
    )

# 2. Configuración de MongoDB (NoSQL)
# Si usas MongoDB local:
client = MongoClient("mongodb://localhost:27017/")
mongo_db = client["escuela_nosql_db"]
mongo_collection = mongo_db["usuarios"]


# ==========================================
# RUTAS DE LA APLICACIÓN (CONTROLADOR)
# ==========================================

@app.route('/')
def index():
    """Ruta de Bienvenida: Muestra la estructura básica y el formulario."""
    return render_template('index.html')

@app.route('/registrar', methods=['POST'])
def registrar():
    """Ruta que procesa el formulario y guarda en ambas BD."""
    if request.method == 'POST':
        # Capturamos los datos enviados desde el formulario HTML
        nombre = request.form['nombre']
        email = request.form['email']
        edad = request.form['edad']

        # --- GUARDAR EN MYSQL ---
        try:
            conexion_mysql = get_mysql_connection()
            cursor = conexion_mysql.cursor()
            sql = "INSERT INTO usuarios (nombre, email, edad) VALUES (%s, %s, %s)"
            cursor.execute(sql, (nombre, email, edad))
            conexion_mysql.commit()
            cursor.close()
            conexion_mysql.close()
        except Exception as e:
            print(f"Error en MySQL: {e}")

        # --- GUARDAR EN MONGODB ---
        try:
            documento_usuario = {
                "nombre": nombre,
                "email": email,
                "edad": int(edad)
            }
            mongo_collection.insert_one(documento_usuario)
        except Exception as e:
            print(f"Error en MongoDB: {e}")

        # Redirigimos a la página de éxito mostrando el último registrado
        return redirect(url_for('exito', nombre=nombre))

@app.route('/exito/<nombre>')
def exito(nombre):
    """Ruta que muestra los registros acumulados en ambas bases de datos."""
    
    # 1. Obtener registros de MySQL
    usuarios_mysql = []
    try:
        conexion_mysql = get_mysql_connection()
        cursor = conexion_mysql.cursor(dictionary=True) # dictionary=True devuelve filas como diccionarios
        cursor.execute("SELECT * FROM usuarios")
        usuarios_mysql = cursor.fetchall()
        cursor.close()
        conexion_mysql.close()
    except Exception as e:
        print(f"Error al leer MySQL: {e}")

    # 2. Obtener registros de MongoDB
    # Convertimos el cursor de pymongo a una lista de Python
    usuarios_mongo = list(mongo_collection.find({}, {"_id": 0})) # Ocultamos el _id de mongo para simplificar

    return render_template('success.html', nombre=nombre, 
                           mysql_data=usuarios_mysql, 
                           mongo_data=usuarios_mongo)

if __name__ == '__main__':
    # Ejecutamos el servidor en modo debug para desarrollo escolar
    app.run(debug=True)
```

## Paso 2: La plantilla de Bienvenida (templates/index.html)
Crea la carpeta templates y dentro guarda este archivo. Utilizamos Bootstrap 5 mediante CDN para que los alumnos no tengan que instalar archivos CSS locales.

```sh
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bienvenida - Práctica Flask, MySQL y MongoDB</title>
    <!-- Importando Bootstrap 5 CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">

    <!-- Barra de navegación básica -->
    <nav class="navbar navbar-dark bg-dark shadow-sm">
        <div class="container">
            <a class="navbar-brand" href="#">🚀 Mi Primera Web FullStack</a>
        </div>
    </nav>

    <div class="container my-5">
        <!-- Sección de Bienvenida -->
        <div class="p-5 mb-4 bg-white rounded-3 shadow-sm border">
            <div class="container-fluid py-3">
                <h1 class="display-5 fw-bold text-primary">¡Bienvenido a la Práctica Integrada!</h1>
                <p class="col-md-8 fs-4 text-secondary">Esta aplicación demuestra la conexión simultánea entre un entorno web en Python (Flask), bases de datos relacionales (MySQL) y NoSQL (MongoDB).</p>
            </div>
        </div>

        <!-- Formulario de Registro -->
        <div class="row justify-content-center">
            <div class="col-md-6">
                <div class="card shadow border-0">
                    <div class="card-header bg-primary text-white text-center py-3">
                        <h4 class="mb-0">Formulario de Registro de Usuario</h4>
                    </div>
                    <div class="card-body p-4">
                        <!-- El formulario apunta a la ruta /registrar usando el método POST -->
                        <form action="/registrar" method="POST">
                            <div class="mb-3">
                                <label for="nombre" class="form-label">Nombre Completo:</label>
                                <input type="text" class="form-control" id="nombre" name="nombre" required placeholder="Ej. Ana Pérez">
                            </div>
                            <div class="mb-3">
                                <label for="email" class="form-label">Correo Electrónico:</label>
                                <input type="email" class="form-control" id="email" name="email" required placeholder="correo@ejemplo.com">
                            </div>
                            <div class="mb-3">
                                <label for="edad" class="form-label">Edad:</label>
                                <input type="number" class="form-control" id="edad" name="edad" required placeholder="Ej. 18">
                            </div>
                            <div class="d-grid">
                                <button type="submit" class="btn btn-success btn-lg">Registrar Datos</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Bootstrap JS Bundle CDN -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
## Paso 3: La plantilla de Confirmación y Visualización (templates/success.html)
Crea este archivo dentro de la carpeta templates. Aquí utilizaremos las herramientas de Jinja2 (el motor de plantillas de Flask) como los bucles {% for %} para pintar los datos que vienen de MySQL y MongoDB lado a lado.

```sh
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro Exitoso</title>
    <!-- Importando Bootstrap 5 CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">

    <nav class="navbar navbar-dark bg-dark shadow-sm">
        <div class="container">
            <a class="navbar-brand" href="/">🚀 Mi Primera Web FullStack</a>
            <a href="/" class="btn btn-outline-light btn-sm">Volver al Inicio</a>
        </div>
    </nav>

    <div class="container my-5">
        <!-- Alerta de éxito -->
        <div class="alert alert-success shadow-sm" role="alert">
            <h4 class="alert-heading">¡Registro Guardado con Éxito!</h4>
            <p>Hola <strong>{{ nombre }}</strong>, tus datos han sido almacenados de forma simultánea en la base de datos relacional (MySQL) y en la base de datos NoSQL (MongoDB).</p>
        </div>

        <div class="row mt-4">
            <!-- Columna de MySQL -->
            <div class="col-md-6 mb-4">
                <div class="card shadow border-0 h-100">
                    <div class="card-header bg-primary text-white">
                        <h5 class="mb-0">🗄️ Registros en MySQL (Relacional)</h5>
                    </div>
                    <div class="card-body">
                        <div class="table-responsive">
                            <table class="table table-striped table-hover">
                                <thead>
                                    <tr>
                                        <th>ID</th>
                                        <th>Nombre</th>
                                        <th>Email</th>
                                        <th>Edad</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    {% for row in mysql_data %}
                                    <tr>
                                        <td>{{ row.id }}</td>
                                        <td>{{ row.nombre }}</td>
                                        <td>{{ row.email }}</td>
                                        <td>{{ row.edad }}</td>
                                    </tr>
                                    {% else %}
                                    <tr>
                                        <td colspan="4" class="text-center text-muted">No hay registros aún en MySQL.</td>
                                    </tr>
                                    {% endfor %}
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Columna de MongoDB -->
            <div class="col-md-6 mb-4">
                <div class="card shadow border-0 h-100">
                    <div class="card-header bg-dark text-white">
                        <h5 class="mb-0">🍃 Registros en MongoDB (NoSQL)</h5>
                    </div>
                    <div class="card-body">
                        <div class="table-responsive">
                            <table class="table table-dark table-striped table-hover">
                                <thead>
                                    <tr>
                                        <th>Nombre</th>
                                        <th>Email</th>
                                        <th>Edad</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    {% for doc in mongo_data %}
                                    <tr>
                                        <td>{{ doc.nombre }}</td>
                                        <td>{{ doc.email }}</td>
                                        <td>{{ doc.edad }}</td>
                                    </tr>
                                    {% else %}
                                    <tr>
                                        <td colspan="3" class="text-center text-muted">No hay registros aún en MongoDB.</td>
                                    </tr>
                                    {% endfor %}
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
