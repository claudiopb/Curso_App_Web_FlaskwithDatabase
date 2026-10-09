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

Campo	Tipo	Descripción
id	Integer (PK)	Identificador único
torneo_id	Integer (FK)	Torneo al que pertenece
participante1_id	Integer (FK)	Primer competidor
participante2_id	Integer (FK)	Segundo competidor
ganador_id	Integer (FK)	Quién ganó
aura_ganada	Integer	Puntos de aura obtenidos
fecha	DateTime	Cuándo se realizó
4. Inscripción (tabla intermedia)

Campo	Tipo	Descripción
id	Integer (PK)	Identificador único
participante_id	Integer (FK)	Participante inscrito
torneo_id	Integer (FK)	Torneo en cuestión
fecha_inscripcion	DateTime	Cuándo se inscribió
Diagrama de Relaciones
