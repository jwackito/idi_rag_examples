**Plan de Trabajo - Grupo Niños RAGtas**

**Tema**

Queremos construir un sistema que funcione como consultor de ciberseguridad: le hacés una pregunta en lenguaje natural ("¿qué vulnerabilidades críticas afectan a Apache este mes?", "¿cómo mitigamos este tipo de ataque?") y te responde con información actualizada, citando de dónde sacó cada dato.

El problema que resuelve es concreto: los modelos de lenguaje tienen una fecha de corte de conocimiento y no saben nada de vulnerabilidades publicadas después de eso. Nosotros lo conectamos a fuentes que se actualizan a diario (como el catálogo de CVEs del NIST o las alertas de CISA), así el sistema siempre trabaja con información fresca.

Todo corre en local, sin mandar datos a ninguna API en la nube. Eso es importante porque un consultor de seguridad recibe preguntas sobre infraestructura propia, configs internas y vulnerabilidades expuestas — ese tipo de información no debería salir de la organización.

**Integrantes del grupo**

- Luca Giordano
- Francisco Suarez
- Valentino Turconi
- Miguel Miesionznik

**De dónde vienen los datos**

Las fuentes que le damos al sistema no son arbitrarias. Las organizamos en tres capas según qué tan difícil son de procesar:

La primera capa son los datos más estructurados: el catálogo de CVEs del NIST (más de 250.000 vulnerabilidades en formato JSON), el framework ATT&CK de MITRE (que mapea tácticas y técnicas de los atacantes), el catálogo CWE de debilidades de software, y dos feeds que nos dicen cuáles vulnerabilidades se están explotando actualmente en el mundo real (EPSS y CISA KEV). Son los más fáciles de ingerir y los que más valor aportan.

La segunda capa son documentos en prosa: las guías técnicas del NIST (que pueden tener 300 páginas), las alertas de CISA, y reportes de threat intelligence de empresas como Mandiant o CrowdStrike. Son más difíciles de procesar que un JSON, pero dan contexto que los datos estructurados solos no tienen.

La tercera capa son las más complejas: Exploit-DB mezcla código de exploit con descripción en el mismo documento, y Stack Exchange tiene un formato de pregunta/respuesta que además nos sirve para armar ejemplos de evaluación.

Lo interesante de este corpus es que cada tipo de documento necesita una estrategia de procesamiento distinta — un CVE es una entrada atómica que no se puede dividir, pero una guía NIST de 300 páginas hay que segmentarla de forma inteligente. Ese problema de "¿cómo procesás fuentes tan distintas de forma consistente?" es el aporte técnico central de nuestra investigación.

**Qué tipo de preguntas tiene que poder responder**

Pensamos en seis tipos de consultas con dificultad creciente:

Las más simples son las factuales: el usuario pide un dato puntual ("¿qué score CVSS tiene tal CVE?"). El desafío acá es la precisión y que el dato no esté desactualizado.

Las relacionales requieren conectar información de varias fuentes: "¿qué técnicas de ataque usa el grupo APT29?" implica buscar el actor, sus técnicas, y las sub-técnicas asociadas — todo en documentos distintos.

Las comparativas necesitan traer dos chunks distintos y contrastarlos: "¿cuál es la diferencia entre XSS e inyección SQL?".

Las procedimentales son las más útiles en la práctica: "¿cómo mitigamos esta técnica de ataque?" requiere juntar la descripción del ataque, las mitigaciones recomendadas y el contexto de detección.

Las contextuales son las más complejas: el usuario describe su situación ("tengo Windows con RDP expuesto") y el sistema tiene que entender qué está preguntando realmente antes de buscar.

Las temporales agregan la variable tiempo: "¿qué CVEs críticas salieron en los últimos 30 días?", lo que requiere ordenar los resultados por fecha.

**Objetivos**

Básicamente queremos demostrar cuatro cosas con este proyecto: que podemos darle al modelo información actualizada que no tiene en su entrenamiento y que el rendimiento del mismo se mantenga al incorporar nuevos datos, que el sistema puede responder los seis tipos de preguntas que describimos, y que todo funciona sin depender de ningún servicio externo.

**Desafíos**

El desafío más interesante — y el que creemos que tiene valor académico — es el de procesar fuentes tan heterogéneas de forma coherente. No existe hoy una métrica estándar para evaluar si un fragmento de texto está bien dividido. Nosotros proponemos dos: *boundary precision* (si el corte respeta límites semánticos reales) e *information density* (cuánta información útil hay por cada token). Eso es algo que la literatura todavía no tiene resuelto.

El modelo corriendo en local también es un desafío real: un modelo de 7B alucina más que uno de 70B o que GPT-4. La solución no es ignorarlo sino compensarlo: cada respuesta debe citar sus fuentes, y si el sistema no encuentra evidencia suficiente, tiene que decirlo en lugar de inventar.

Un riesgo que no queremos ignorar es el de prompt injection: si el corpus incluye texto de Exploit-DB (que puede contener instrucciones maliciosas embebidas), ese texto podría intentar manipular al modelo. Es el vector de ataque #1 en sistemas RAG según OWASP 2025.

**Cronograma**

- Selección y preprocesamiento de los datos (15/Jun): armamos los parsers para cada tipo de fuente y cargamos el corpus inicial.
- Primer prototipo completo (30/Jun): un sistema que funciona de punta a punta — desde la pregunta hasta la respuesta con citas — aunque no esté optimizado.
- Tests automatizados (15/Jul). Medimos cuatro cosas:
  - qué tan rápido responde (tokens por segundo, latencia por etapa)
  - qué tan bien recupera los documentos relevantes (Hit Rate, MRR, nDCG)
  - qué tan buenas son las respuestas (usando RAGAS, que no necesita respuestas de referencia)
  - qué tan bien están divididos los textos (con las métricas de chunking que propusimos). Para los tests de retrieval usamos solo CVEs publicadas después de la fecha de corte del modelo, así no podemos hacer trampa.
- Adaptamos los componentes del RAG según los hallazgos otorgados por los tests (30/Jul).
- Nuestra idea es lograr incorporar la funcionalidad de análisis de código y no solo chat en lenguaje natural.
  - El RAG recibe código y detecta vulnerabilidades.
- Demo expo-ciencia (2/Oct): fecha fija para todos los grupos.
