# 🎯 Características Clave de un MVP

- Es funcional: Aunque sea básico, debe resolver el problema principal para el cual fue creado. No es un producto incompleto o de baja calidad, sino el núcleo esencial de este.

- Es rápido de construir: Su objetivo es reducir el tiempo de salida al mercado (time-to-market).

- Genera aprendizaje: Está diseñado para probar hipótesis mediante la retroalimentación real de los usuarios.

# 💡 Definición del MVP (Producto Mínimo Viable).

"Antes de escribir código, los equipos deben definir qué problema resuelve su aplicación y cuál es la funcionalidad mínima indispensable para que sea útil.
"Ejemplo práctico:Para una aplicación de que lleve un registro de Farmear el Aura, un MVP ideal podría centrarse exclusivamente en:

$\checkmark$ Registrar participantes.

$\checkmark$ Torneos y batallas.

$\checkmark$ Batallas consultar un ranking de los mejores.

# 🎯 Definición del MVP: "AuraTracker"
¿Qué problema resuelve?

No existe un sistema centralizado para registrar y consultar los resultados de torneos de farmeo de aura, ni para conocer quiénes son los mejores farmers a nivel global.

¿Cuál es la funcionalidad mínima indispensable?

El MVP debe permitir registrar participantes, torneos y batallas, y consultar un ranking de los mejores. Todo lo demás (autenticación de usuarios, comentarios, estadísticas avanzadas) queda fuera de la primera versión.

# 📊 Modelo de Datos (Entidades y Relaciones)

ES importante entender esto antes de escribir una sola línea de código.

# Entidades Principales

## 1. Farmer

|     Campo	    |      Tipo     |	     Descripción          |
| ------------- | ------------- | --------------------------|
|id	            | Integer (PK)  | Identificador único       |
|nombre	        | String(100)	  | Nombre del farmer         |
|nacionalidad	  | String(50)	  | País de origen            |
|nivel_aura	    | Integer	      | Puntos de aura acumulados |
|fecha_registro |	DateTime	    | Cuándo se unió            |

## 2. Torneo

|     Campo	    |          Tipo        |	     Descripción                |
| ------------- | -------------------- | ---------------------------------|
|id	            |Integer (PK)	         |Identificador único               |
|nombre	        |String(100)	         |Nombre del torneo                 |
|descripcion	  |Text	                 |Reglas o detalles                 |
|fecha_inicio	  |Date	                 |Fecha de inicio                   | 
|fecha_fin	    |Date                  |Fecha de finalización             |
|estado	        |String(20)	           |"activo", "finalizado", "próximo" |


## 3. Batalla

|      Campo	    |         Tipo         |	     Descripción                |
| --------------- | -------------------- | ---------------------------------|
|id	              |Integer (PK)	         |Identificador único               |
|torneo_id	      |Integer (FK)	         |Torneo al que pertenece
|participante1_id	|Integer (FK)	         |Primer competidor
|participante2_id	|Integer (FK)	         |Segundo competidor
|ganador_id	      |Integer (FK)	         |Quién ganó
|aura_ganada	    |Integer	             |Puntos de aura obtenidos
|fecha	          |DateTime	             |Cuándo se realizó


## 4. Inscripción (tabla intermedia)

|      Campo	    |         Tipo         |	     Descripción                |
| --------------- | -------------------- | ---------------------------------|
|id	Integer (PK)	|Identificador         |único                             |
|participante_id	|Integer (FK)	         |Participante inscrito             |
|torneo_id	      |Integer (FK)	         |Torneo en cuestión                |
|fecha_inscripcion|	DateTime	           |Cuándo se inscribió               |

Diagrama de Relaciones

Participante  ───< Inscripción >───  Torneo

     │                                  │
     
     │                                  │
     
     └──────< Batalla >─────────────────┘
              (participante1, participante2, ganador)

- Un participante puede estar en muchos torneos (a través de Inscripción).

- Un torneo puede tener muchos participantes y muchas batallas. 

- Una batalla siempre pertenece a un torneo y tiene dos participantes y un ganador.

- El top 10 se calcula ordenando a los participantes por nivel_aura de mayor a menor.

# 🗺️ Rutas (Páginas) del MVP

Estas son las rutas que se van implementar en Flask. Cada una corresponde a una función en routes.py y a una plantilla en templates/.

## Rutas Públicas (sin login en el MVP)

|       Ruta          |	  Método  |             Descripción	                    |          Plantilla           |
|---------------------|-----------|---------------------------------------------|------------------------------|
|/	                  | GET	      |Dashboard con Top 10 y torneos activos	      |index.html                    |
|/participantes	      | GET	      |Lista de todos los participantes	            |participantes/lista.html      |
|/participantes/<id>	| GET	      |Perfil del participante con su historial	    |participantes/detalle.html    |
|/participantes/nuevo |	GET, POST	|Formulario para registrar un farmer	        |participantes/formulario.html |
|/torneos	            | GET	      |Lista de torneos (activos y pasados)	        |torneos/lista.html            |
|/torneos/<id>	      | GET	      |Detalle del torneo: participantes y batallas	|torneos/detalle.html          |
|/torneos/nuevo	      | GET, POST	|Formulario para crear un torneo	            |torneos/formulario.html       |
|/batallas	          | GET	      |Historial de batallas	                      |batallas/lista.html           |
|/batallas/nueva	    | GET, POST	|Registrar resultado de una batalla	          |batallas/formulario.html      |
|/top10	              | GET	      |Ranking de los 10 mejores farmers	          |top10.html                    |

## Funcionalidades Clave del MVP

<ol>
  <li>Registrar participantes con nombre, nacionalidad y nivel inicial de aura.</li>
  <li>Crear torneos con nombre, fechas y estado.</li>
  <li>Inscribir participantes en torneos.</li>
  <li>Registrar batallas entre dos participantes de un torneo, indicando quién ganó y cuánta aura ganó.</li>
  <li>Actualizar el nivel de aura del ganador automáticamente al registrar una batalla.</li>
  <li>Mostrar el Top 10 ordenado por nivel de aura.</li>
  <li>Consultar el perfil de un participante con sus batallas y torneos.</li>
</ol>

## 🚫 ¿Qué NO incluye el MVP? (Para evitar que se desborden)

❌ Autenticación de usuarios (login/registro de cuentas).

❌ Panel de administración con roles.

❌ Comentarios o reacciones.

❌ Gráficos estadísticos avanzados.

❌ API REST para consumo externo.

❌ Edición/eliminación de batallas (solo creación).

Regla de oro del MVP: Si una funcionalidad no es esencial para que el sistema cumpla su propósito principal, se deja para la versión 2.0.







