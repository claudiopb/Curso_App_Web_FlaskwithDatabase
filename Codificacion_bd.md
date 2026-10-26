# 💻 Codificando la Base (Configuración y Modelos)
Crea la estructura de carpetas que diseñamos (app/, app/templates/, app/static/, etc.) y vamos a rellenar los primeros archivos clave.

1. app/config.py (Configuración de la Base de Datos)
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

Cuando en tu archivo  _app/__init__.py_ 

escribes esta línea: _app.config.from_object(config_class)_

Flask hace lo siguiente:

1. Lee la clase Config.
2. Busca todas las variables que estén escritas en MAYÚSCULAS (como SECRET_KEY o SQLALCHEMY_DATABASE_URI).
3. Las importa automáticamente a su memoria interna para saber cómo debe comportarse (por ejemplo, a qué base de datos MySQL debe conectarse o qué llave usar para la seguridad).

💡 ¿Por qué es una buena práctica usar una clase de configuración?

- Organización profesional: Todo lo que afecta la configuración de la app vive en un solo archivo limpio (config.py).
- Facilidad para cambiar de entorno: Más adelante, cuando tu aplicación crezca, podrás crear fácilmente otras clases (por ejemplo, una DevelopmentConfig para tu computadora y una ProductionConfig para cuando esté en internet) y cambiar de una a otra con una sola línea sin romper nada.
