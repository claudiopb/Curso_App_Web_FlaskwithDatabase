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
