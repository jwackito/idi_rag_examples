## Tema
Non playable character adventure
## Integrantes del grupo
- Valentin Ciamparella
- Damián Bicocchi
- Juan Manuel Tarrío
- Lucas Matías Corbalan Saez
## Cronograma

### Selección y preprocesamiento de los datos
- Necesitamos adquirir de fuentes accesibles algunos cuentos o novelas (en este caso, importa que sean cortas) para poder crear los scripts
- El preprocesamiento se haría con una librería de procesamiento que lleve el formato original a formato markdown
- Una vez llevada la narración a markdown
	- Puede ser necesario algún tipo de curado (por ejemplo, anotaciones del traductor)
- Se guarda el archivo en una base de datos vectorial segun su embedding
- Se usan estos embeddings para que se generen scripts de tantos personajes como sean mencionados
	- Puede llegar a ser necesario la inclusión de un personaje adicional llamado “forense” o similar para ser la fuente de aspectos técnicos (por ejemplo, responder si el asesinato fue hecho por alguien diestro)
- Hacer una conexión entre el juego desarrollado en GODOT con algun modelo corriendo localmente (La cantidad de tokens que gastaremos en un aspecto real sería incalculable) tal que
	- Pueda correr con una gráfica muy humilde. En un futuro, que sea hasta posible con gráficos integrados
	- Tenga un tiempo de respuesta aceptable al usuario “jugador” (Límite de 3 segundos entre que emite el mensaje y le responde)
### Primer prototipo
- Con el cuento “Crimen casi perfecto” de Roberto Arlt
- Asumimos que, como máximo, habrá un poco más de 5 minutos de juego (Speedrunners abstenerse de jugar)
- Buscamos por ahora que haya un diálogo posible, con tiempos dentro de lo tolerable
- No va a ser meta la apariencia visual



| Fecha        | Aspecto                                                                                               | Detalle                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------ | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 5/6          | Presentación de scripts de personajes de “Crimen casi perfecto”.  <br>Decisión de un modelo para usar | Los scripts de este relato pueden ser tranquilamente tratados y verificados manualmente  <br>El modelo a buscar debe cumplir  <br>- Puede correr tranquilamente en una gráfica no tan potente (propondremos menos de 1 billón de parámetros para empezar)                                                                                                                                        |
| 12/6         | Avances de interacción juego-jugador.  <br>Buscar nuevos relatos para implementar                     | Buscamos que se pueda dar el ida y vuelta entre el jugador y cada llm. Por ahora solo buscamos que responda con un threshold bastante laxo (10 segundos)  <br>  <br>La búsqueda de nuevos relatos y generación de scripts va seguir constante durante el desarrollo del proyecto. Por ahora, desde ahora en más solo planteamos que el único caso existente es el relato “Crimen casi perfecto”. |
| 19/6         | Planteo de un módulo para las prompts injection                                                       | Plantearlo con  <br>- keywords  <br>- Análisis de un llm extra ?                                                                                                                                                                                                                                                                                                                                 |
| 26/6         | Avances interacción juego-jugador  <br>  <br>Introducción de métricas de respuesta                    | Avances visuales y baja del threshold (5 segundos)  <br>  <br>Puede crearse manualmente un set de preguntas y respuestas (incluso para cada script) para poder analizar la respuesta de cada modelo                                                                                                                                                                                              |
| 3/7          | Avances interacción juego-jugador                                                                     | Mejoras gráficas                                                                                                                                                                                                                                                                                                                                                                                 |
| 10/7         | Primer prototipo                                                                                      | Interfaz gráfica funcional                                                                                                                                                                                                                                                                                                                                                                       |
| Proximamente |                                                                                                       |                                                                                                                                                                                                                                                                                                                                                                                                  |


