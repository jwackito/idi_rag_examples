# Plan de Trabajo
## Desarrollo y Optimización de un Sistema RAG
### Asistente de Respuesta Temprana a Emergencias Viales

## 1. Información General

- **Proyecto seleccionado:** Asistente de respuesta temprana a situaciones de emergencia, acotado a siniestros viales.
- **Integrantes:** Andrés Murray Roppel, Lautaro Gutiérrez, Alexia Begue, Ladislao Bordón, Salvador Karakachoff.
- **Docentes guía:** Joaquín Bogado, Alejandra Garrido, Claudia Pons.
- **Cronograma general:** Junio – Octubre 2026.

## 2. Objetivos y Alcance del Desarrollo

### Breve descripción del sistema

Sistema RAG (Retrieval-Augmented Generation) de asistencia a la respuesta temprana en siniestros viales, desarrollado como Prueba de Concepto (PoC) para un organismo provincial. A partir de la descripción en lenguaje natural de una situación de emergencia, el sistema recupera los procedimientos aplicables de la documentación oficial y genera una respuesta accionable, paso a paso, fundamentada exclusivamente en esa documentación.

El requisito central es que el sistema responda en tiempo real (baja latencia), ya que se trata de una emergencia. Este requisito condiciona directamente la elección del modelo de lenguaje, el hardware utilizado y la cantidad de usuarios que el sistema puede atender en simultáneo. La interacción base es por texto; como mejora opcional (plus) se contempla la entrada y salida por voz (reconocimiento de habla y síntesis de voz).

### Alcance y decisiones de diseño

- **Dominio acotado:** siniestros viales. Es el tipo de emergencia con documentación pública argentina más sólida y accionable para RAG.
- **Corpus principal:** Protocolo de Actuación ante Siniestros Viales, elaborado por organismos públicos argentinos (Agencia Nacional de Seguridad Vial, Dirección Nacional de Emergencias Sanitarias y Bomberos Voluntarios de la República Argentina). Documentación complementaria de atención inicial al politraumatizado y de Defensa Civil cuando aplique.
- **Despliegue:** modelo de lenguaje abierto self-hosted. En esta primera instancia corre localmente en el equipo del grupo, lo que permite controlar y medir de forma real las palancas de modelo, hardware y concurrencia que impone el requisito de tiempo real.
- **Entregable:** PoC funcional + framework de evaluación automatizada + dos ciclos de mejora medidos contra una línea base.

## 3. Aspectos a Mejorar (Métricas de Rendimiento)

El requisito de funcionamiento en tiempo real determina la primera métrica. Las restantes aseguran que la respuesta, además de rápida, sea correcta y segura — aspecto crítico en un contexto de emergencia.

- **Latencia end-to-end (tiempo de respuesta).** Tiempo desde la consulta hasta la respuesta completa, incluyendo el time-to-first-token si se usa streaming. Es la métrica dura impuesta por el enunciado.
- **Relevancia de la recuperación (retrieval).** Métricas como precisión y recall: que los fragmentos del protocolo recuperados sean efectivamente los pertinentes a la situación descrita.
- **Fidelidad y precisión de la respuesta (faithfulness).** Que la respuesta generada se ajuste al manual y no alucine. En emergencias, una respuesta segura pero incorrecta es el peor escenario.
- **Calibración y manejo de consultas fuera de alcance.** Tasa de alucinación y capacidad del sistema de reconocer cuándo no posee el procedimiento y derivar (“escalá a un operador humano / comunicate con el 911”) en lugar de inventar.

## 4. Cronograma Detallado e Hitos Quincenales

### Mes 1 — Junio: Cimientos y Prototipo

**Quincena 1 (01/06 – 15/06): Selección y curación de datos**

- **Hito 1.1:** Obtención y curación del Protocolo de Actuación ante Siniestros Viales y de las fuentes complementarias; descarte de las secciones no accionables (p. ej. preservación de prueba judicial).
- **Hito 1.2:** Pipeline de extracción, limpieza y chunking del corpus, conservando metadatos por fase del protocolo (toma de conocimiento, arribo, intervención, liberación).

**Quincena 2 (16/06 – 30/06): Primer prototipo funcional**

- **Hito 1.3:** Configuración de la base de datos vectorial y de los embeddings (en español) en el entorno local.
- **Hito 1.4:** Despliegue de la arquitectura RAG básica (consulta → recuperación → generación) corriendo en el equipo local con un modelo de lenguaje abierto.

### Mes 2 — Julio: Diagnóstico y Automatización

**Quincena 3 (01/07 – 15/07): Implementación de tests automatizados**

- **Hito 2.1:** Armado del entorno de pruebas: conjunto de consultas de evaluación representativas de descripciones reales de siniestros, con respuestas esperadas.
- **Hito 2.2:** Automatización de las mediciones: latencia y time-to-first-token, métricas de recuperación (precisión) y de fidelidad.

**Quincena 4 (16/07 – 31/07): Línea base y entrega del plan de mejoras**

- **Hito 2.3:** Ejecución de las pruebas sobre el prototipo para establecer la métrica inicial (línea base) sobre el hardware local.
- **Hito 2.4:** Formalización y entrega del documento con la lista definitiva y priorizada de aspectos a mejorar.

### Mes 3 — Agosto: Mejora 1 — Tiempo real (latencia)

**Quincena 5 (01/08 – 15/08): Desarrollo de la Mejora 1**

- **Hito 3.1:** Optimización del pipeline orientada a latencia: cuantización y/o elección de un modelo más chico, streaming de la respuesta, batching de inferencia y caching de consultas frecuentes.

**Quincena 6 (16/08 – 31/08): Evaluación y entrega de la Mejora 1**

- **Hito 3.2:** Corrida de los tests automatizados para validar el impacto en latencia.
- **Hito 3.3:** Entrega y puesta en común de los resultados de la Mejora 1.

### Mes 4 — Septiembre: Mejora 2 — Calidad (relevancia y fidelidad)

**Quincena 7 (01/09 – 15/09): Desarrollo de la Mejora 2**

- **Hito 4.1:** Mejora de la calidad de recuperación y de la fidelidad de la respuesta: ajuste de chunking y embeddings, reranking, búsqueda híbrida (densa + léxica) y prompts para reducir alucinaciones y manejar las consultas fuera de alcance.

**Quincena 8 (16/09 – 30/09): Evaluación, entrega y estabilización**

- **Hito 4.2:** Evaluación final con el framework de tests, verificando que la mejora de calidad no haya degradado la latencia (gestión del trade-off velocidad/calidad).
- **Hito 4.3:** Armado del entorno local robusto para la demostración en vivo, sin dependencia de la red.

### Mes 5 — Octubre: Transferencia y Divulgación

**Quincena 9 (2/10): Práctica de la demo**

- **Hito 5.1:** Redacción del material de soporte visual.
- **Hito 5.2:** Simulacros de presentación y validación de la demo ante imprevistos (inputs inesperados, consultas fuera de alcance, caídas).

## 5. Gestión de Riesgos y Flexibilidad del Plan

El avance se revisa al cierre de cada quincena contra los hitos previstos. Riesgos identificados y mitigaciones:

- **Documentación insuficiente o de baja calidad.** Mitigación: mantener incendios como tipo de emergencia de respaldo; la decisión final se toma antes del cierre de la Quincena 1.
- **No alcanzar la latencia objetivo en el hardware local.** Mitigación: estrategia de degradación gradual (modelo más chico, cuantización, caching, acotar la concurrencia objetivo). Como red de seguridad para validar el RAG de forma temprana, la lógica de recuperación y generación puede probarse contra una API en la nube antes de migrar al modelo self-hosted local.
- **Una mejora no rinde lo esperado.** Mitigación: el framework de tests automatizados (Mes 2) permite descartar rápido un enfoque y reasignar la quincena.
- **Atrasos en el cronograma.** Mitigación: la Mejora 2 (Septiembre) puede acotarse en alcance si una etapa previa se demora, protegiendo el Code Freeze y la preparación de la Expo.

## 6. Potenciales mejoras

- Chat por voz
- Llamada automática al 911
- Recepción de imágenes y documentos
- Videollamada
- Expandir el dominio (que no solo responda a accidentes viales)
