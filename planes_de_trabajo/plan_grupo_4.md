# Plan de Trabajo - Grupo nro 4

## Tema
**RAG (Retrieval-Augmented Generation) para Hemeroteca Histórica de Página/12**  
*Desarrollo de un sistema de consultas e investigación histórica sobre el archivo digital y digitalizado del diario argentino Página/12 (período objetivo 2005 en adelante, con miras a la extensión del archivo histórico).*

---

## Integrantes del grupo
* **Luciano Wagner**
* **Camilo Espinazo**

---

## Cronograma

### 1. Selección y preprocesamiento de los datos (Hito: 15 de Junio)
* **Objetivos**:
  * Consolidar el scraper de Página/12 (`pagina12.py`) para descarga controlada y respetuosa.
  * Parser HTML robusto utilizando `trafilatura` para la extracción de texto limpio y eliminación de código y banners publicitarios.
  * Enriquecimiento de metadatos de los artículos: clasificación automática de alcance geográfico (`country_scope = argentina | international | unknown`), reconocimiento de entidades nombradas (personas y organizaciones con **spaCy** `es_core_news_md`) y filtrado con gazetteer nacional (`argentina.json`).
  * Estructuración del almacenamiento raw (`data/raw/`) y parsed (`data/parsed/`).

### 2. Primer prototipo funcional (Hito: 30 de Junio)
* **Objetivos**:
  * Indexación inicial en **Qdrant** utilizando embeddings vectoriales multilingües (`intfloat/multilingual-e5-large`) estructurados con prefijos de contexto (`passage: ` / `query: `).
  * Implementación del planificador de consultas (`QueryPlanner`) estructurado con Groq / Llama-3.3 para extraer intención y filtros estructurados (año, década, sección).
  * Pipeline de búsqueda híbrida integrado: combinación de recuperación léxica en español (BM25 tokenizado) y semántica (Qdrant) unificados mediante **RRF (Reciprocal Rank Fusion)**.
  * Reranker de segunda fase integrado (FlashRank / CrossEncoder).
  * Interfaz de usuario funcional conectada mediante Docker a **OpenWebUI**.

### 3. Tests automatizados para medir rendimiento (Hito: 15 de Julio)
Implementación de una suite de evaluación automatizada (`pytest` y scripts de evaluación cuantitativa) para medir los siguientes cuatro ejes de rendimiento:
* **Velocidad de respuesta**:
  * *Métrica*: Tokens por segundo (t/s) en la generación de respuestas y latencia de búsqueda extrema a extrema en milisegundos (ms) para el pipeline híbrido de recuperación.
* **Confianza del usuario en la respuesta (Abstención/Alucinación)**:
  * *Métrica*: Tasa de falsos positivos y precisión de la abstención utilizando la lógica del `EvidenceChecker` (consistencia temporal de años y umbrales mínimos de score de rerankeo por tipo de consulta fáctica vs conceptual).
* **Cercanía de las respuestas**:
  * *Métrica*: Distancia vectorial cosenoidal (usando embeddings de `multilingual-e5-large`) entre las respuestas generadas por el RAG y respuestas de referencia (gold standard) redactadas manualmente por el equipo de investigación para un set de evaluación de 50 preguntas.
* **Relevancia del retrieval (calidad de recuperación)**:
  * *Métrica*: Hit Rate @K (proporción de veces que el artículo relevante está en los primeros K resultados) y **MRR (Mean Reciprocal Rank)** utilizando el corpus indexado en Qdrant y consultas con filtros aplicados.

### 4. Entrega de mejora de la velocidad de respuesta (Hito: 30 de Julio)
* **Objetivos**:
  * Implementación de caché de inferencia para los embeddings locales de consultas.
  * Optimización del scroll paginado inicial de Qdrant hacia BM25 en memoria (carga diferida/ lazy loading) o almacenamiento en base de datos local ligera (SQLite/BM25 persistido).
  * Ajuste de la concurrencia y optimización del backend en FastAPI.

### 5. Entrega de mejora 2: Ingesta Histórica y OCR (Hito: 15 de Agosto)
* **Objetivos**:
  * Implementación del pipeline de parsing para archivos PDF históricos digitalizados (Biblioteca Nacional / Internet Archive).
  * Integración de extracción de texto híbrida (`pdfplumber` + fallback de motor de **OCR Tesseract** con español entrenado).
  * Limpieza específica de ruido OCR (separación de guiones de fin de línea, unión de párrafos y desambiguación de columnas).

### 6. Entrega de mejora 3: Calibración y Afinamiento (Hito: 30 de Agosto)
* **Objetivos**:
  * Ajuste y calibración fina de los pesos de la búsqueda híbrida (coeficientes de balance BM25 vs semántico) tras la evaluación del Hito de Julio.
  * Calibración de umbrales del detector de evidencia (`EvidenceChecker`) para optimizar el balance entre respuestas detalladas y abstenciones de seguridad.
  * Robustez frente a consultas conversacionales complejas.

### 7. Práctica de la demo para Expo-Ciencia (Hito: 2 de Octubre)
* **Objetivos**:
  * Simulación final y congelamiento del código.
  * Preparation de la presentación visual y el póster científico.
  * Práctica de demostraciones en vivo (ingesta rápida de un día de diario y respuesta inmediata de consultas de archivo histórico).
