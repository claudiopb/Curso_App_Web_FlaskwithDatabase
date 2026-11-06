# 💻 Codificando la Base (Configuración y Modelos)
Crea la estructura de carpetas que diseñamos (app/, app/templates/, app/static/, etc.) y vamos a rellenar los primeros archivos clave.

## 1. app/config.py (Configuración de la Base de Datos)
Aquí conectaremos Flask con tu MySQL local. Usaremos variables de entorno por seguridad.

   ```sh
   import os
   from dotenv import load_dotenv

   load_dotenv()

   class Config:
       SECRET_KEY = os.environ.get('SECRET_KEY') or 'clave-secreta-super-dura'
       # Configuración de conexión MySQL usando PyMySQL: mysql+pymysql://usuario:contraseña@servidor/nombre_base_datos
       SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'mysql+pymysql://root:@localhost/aura_tracker_db'
       SQLALCHEMY_TRACK_MODIFICATIONS = False
   ```

En Python, la palabra class se utiliza para crear una clase, que puedes imaginar como una "caja contenedora" o un plano estructurado donde agrupamos variables y configuraciones relacionadas entre sí.

## ¿Qué es exactamente class Config: en nuestro proyecto de Flask?
En nuestro archivo app/config.py, escribimos:

```sh
class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'clave-secreta-super-dura'
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'mysql+pymysql://root:@localhost/aura_tracker_db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
 ```

Esta clase es el panel de control o la "configuración central" de tu aplicación Flask. En lugar de tener las credenciales de la base de datos, las claves secretas y los ajustes regados por todo el código, los metemos a todos ordenadamente dentro de esta clase.

## ¿Para qué sirve y cómo lo usa Flask?

Cuando en tu archivo  **app/__init__.py** 

escribes esta línea: **app.config.from_object(config_class)**

Flask hace lo siguiente:

1. Lee la clase Config.
2. Busca todas las variables que estén escritas en MAYÚSCULAS (como SECRET_KEY o SQLALCHEMY_DATABASE_URI).
3. Las importa automáticamente a su memoria interna para saber cómo debe comportarse (por ejemplo, a qué base de datos MySQL debe conectarse o qué llave usar para la seguridad).

## 💡 ¿Por qué es una buena práctica usar una clase de configuración?

- Organización profesional: Todo lo que afecta la configuración de la app vive en un solo archivo limpio (config.py).
- Facilidad para cambiar de entorno: Más adelante, cuando tu aplicación crezca, podrás crear fácilmente otras clases (por ejemplo, una DevelopmentConfig para tu computadora y una ProductionConfig para cuando esté en internet) y cambiar de una a otra con una sola línea sin romper nada.

Desglose por partes:

1. os.environ.get('SECRET_KEY')
   - os: Es un módulo incorporado en Python que permite interactuar con el sistema operativo.
   - os.environ: Es un diccionario que contiene las variables de entorno de tu computadora o servidor (valores confidenciales que guardas fuera del código fuente, generalmente en un archivo .env).
   - .get('SECRET_KEY'): Le dice a Python: "Busca si existe una variable de entorno llamada SECRET_KEY". Si la encuentra, toma su valor. Si no la encuentra, devuelve None.

 2. or

    - En Python, el operador or funciona como un "plan de respaldo" (cortocircuito). Si la primera parte (os.environ.get('SECRET_KEY')) resulta ser falsa o vacía (porque no encontró la variable), Python evalúa e implementa automáticamente la segunda parte.

3. 'clave-secreta-super-dura'

- Es el valor de respaldo por defecto. Si estás programando en tu computadora local y aún no configuraste un archivo .env con una clave secreta real, Flask usará esta cadena de texto por defecto para que la app no falle.

## 💡 ¿Por qué se hace de esta manera (Buenas Prácticas)?

- En desarrollo local: Como estás probando en tu PC, no pasa nada si usa la clave por defecto ('clave-secreta-super-dura').
- En producción (cuando lo subas a internet, por ejemplo, a PythonAnywhere): Nunca debes subir contraseñas ni claves secretas escritas directamente en el código por seguridad. En su lugar, configuras una variable de entorno real en el servidor (un código larguísimo y aleatorio). Al hacer esto, Flask ignorará el texto por defecto y tomará la clave ultra segura del servidor automáticamente.

## 2. app/models.py (Modelos de la BD)
Aquí definiremos las tablas con Flask-SQLAlchemy. Esto mapeará tus tablas de MySQL a clases de Python puras:

```sh
from datetime import datetime
from app import db

class Farmer(db.Model):
    __tablename__ = 'farmers'
    
    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(100), nullable=False)
    red_instagram = db.Column(db.String(100), nullable=True)
    red_tiktok = db.Column(db.String(100), nullable=True)
    aura_acumulada = db.Column(db.Integer, default=0)
    batallas_ganadas = db.Column(db.Integer, default=0)
    
    # Relación con la tabla intermedia de torneos
    torneos_participaciones = db.relationship('TournamentParticipant', backref='farmer', lazy=True)
    
    def __repr__(self):
        return f'<Farmer {self.nombre} - Aura: {self.aura_acumulada}>'


class Tournament(db.Model):
    __tablename__ = 'torneos'
    
    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(150), nullable=False)
    descripcion = db.Column(db.Text, nullable=True)
    fecha = db.Column(db.DateTime, default=datetime.utcnow)
    
    participantes = db.relationship('TournamentParticipant', backref='torneo', lazy=True)
    batallas = db.relationship('Battle', backref='torneo', lazy=True)


class TournamentParticipant(db.Model):
    __tablename__ = 'torneos_participantes'
    
    id = db.Column(db.Integer, primary_key=True)
    # Ahora apunta a 'farmers.id'
    farmer_id = db.Column(db.Integer, db.ForeignKey('farmers.id'), nullable=False)
    torneo_id = db.Column(db.Integer, db.ForeignKey('torneos.id'), nullable=False)
    lugar_obtenido = db.Column(db.Integer, nullable=True)  # 1°, 2°, 3°, etc.


class Battle(db.Model):
    __tablename__ = 'batallas'
    
    id = db.Column(db.Integer, primary_key=True)
    # Apuntan a la tabla 'farmers.id'
    farmer_1_id = db.Column(db.Integer, db.ForeignKey('farmers.id'), nullable=False)
    farmer_2_id = db.Column(db.Integer, db.ForeignKey('farmers.id'), nullable=False)
    ganador_id = db.Column(db.Integer, db.ForeignKey('farmers.id'), nullable=False)
    aura_en_juego = db.Column(db.Integer, nullable=False)
    torneo_id = db.Column(db.Integer, db.ForeignKey('torneos.id'), nullable=True)
    fecha = db.Column(db.DateTime, default=datetime.utcnow)

    # Relaciones apuntando al modelo Farmer
    farmer_1 = db.relationship('Farmer', foreign_keys=[farmer_1_id])
    farmer_2 = db.relationship('Farmer', foreign_keys=[farmer_2_id])
    ganador = db.relationship('Farmer', foreign_keys=[ganador_id])
 ```
¿Qué gestor de MySQL estás usando localmente (XAMPP, MySQL Workbench o Docker)?

creado una base de datos vacía llamada aura_tracker_db para que Flask pueda conectarse

