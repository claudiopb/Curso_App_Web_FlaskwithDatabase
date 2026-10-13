

# Requisitos previos

Primero devemos checar si python esta instalado y configurar sus variables de entorno.

## Checar instalacio de python y su versión.

1 .- Habre una termibal en power shell de preferencia como Administrador.


Instala la extensión de Python.

Instala una versión de Python 3 (para la cual está escrito este tutorial). Las opciones incluyen:

(Todos los sistemas operativos) Descarga desde python.orgNormalmente, utilice el botón de descarga que aparece primero en la página.
(Linux) La instalación integrada de Python 3 funciona bien, pero para instalar otros paquetes de Python debe ejecutar sudo apt install python3-pipen la terminal.
(macOS) Instalación mediante Homebrewen macOS usando brew install python3.
(Todos los sistemas operativos) Una descarga desde Anaconda(con fines de ciencia de datos).
En Windows, asegúrese de que la ubicación de su intérprete de Python esté incluida en la variable de entorno PATH. Puede comprobar la ubicación ejecutando pathel comando en la línea de comandos. Si la carpeta del intérprete de Python no está incluida, abra la Configuración de Windows, busque "entorno", seleccione " Editar variables de entorno para su cuenta " y, a continuación, edite la variable PATH para incluir dicha carpeta.

Crea un entorno de proyecto para el tutorial de Flask.
En esta sección, crearás un entorno virtual donde se instalará Flask. El uso de un entorno virtual evita la instalación de Flask en un entorno Python global y te brinda un control preciso sobre las bibliotecas utilizadas en una aplicación.

La extensión Python Environments admite varios tipos de entornos, incluidos venv, conda, poetry y otros. Este tutorial utiliza venv porque está integrado en Python y no requiere herramientas adicionales. Los pasos para otros tipos de entornos son similares; consulte la sección «Crear entornos» para obtener más detalles.

En su sistema de archivos, cree una carpeta para este tutorial, como por ejemplo hello_flask: .

Abra esta carpeta en VS Code navegando a la carpeta en una terminal y ejecutando code ., o ejecutando VS Code y usando el comando Archivo > Abrir carpeta .

Crea un entorno virtual utilizando el comando Python: Create Environment :

Abre la paleta de comandos ( Ctrl+Mayús+P )
Busque y seleccione Python: Crear entorno
Seleccione Venv para crear un entorno venv.
Seleccione un intérprete de Python para usar en el entorno.
VS Code crea una .venvcarpeta en tu espacio de trabajo y selecciona automáticamente el nuevo entorno.
