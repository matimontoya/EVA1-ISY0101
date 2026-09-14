# Sistema RAG para Reglamentos de Copropiedad

## 1. Descripción

Sistema de **Retrieval-Augmented Generation (RAG)** para realizar consultas en lenguaje natural sobre reglamentos de copropiedad inmobiliaria.

El sistema recupera información relevante desde documentos PDF y utiliza Gemini para generar respuestas basadas en el contenido recuperado.

---

## 2. Tecnologías utilizadas

* Python
* Google Colab
* PyPDF
* LangChain
* Sentence Transformers
* FAISS
* Gemini

### Modelos utilizados

**Modelo de embeddings:**

```text
sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
```

**Modelo de lenguaje:**

```text
gemini-3.5-flash-lite
```

---

## 3. Fuentes de información

El sistema utiliza los siguientes documentos:

```text
Reglamento ley de copropiedad inmobiliaria (1).pdf
ACTUALIZACION_REGLAMENTO_DE_COPROPIEDAD_LEY_N_21.442_2024 (1).pdf
```

Estos documentos constituyen la base de conocimiento utilizada por el sistema.

---

## 4. Arquitectura

El sistema se divide en una etapa de preparación de documentos y una etapa de consulta.

```text
                    DOCUMENTOS PDF
                          │
                          ▼
                   Extracción de texto
                          │
                          ▼
                  Separación por artículos
                          │
                          ▼
                       Chunks
                          │
                          ▼
                     Embeddings
                          │
                          ▼
                        FAISS
                          │
                          │
──────────────────────────┼──────────────────────────
                          │
                    Pregunta usuario
                          │
                          ▼
                   Query Rewriting
                       Gemini
                          │
                          ▼
                     Embedding
                          │
                          ▼
                        FAISS
                          │
                          ▼
                         MMR
                          │
                          ▼
                 Contexto recuperado
                          │
                          ▼
                       Gemini
                          │
                          ▼
                   Respuesta final
```

### Flujo de funcionamiento

1. Los documentos PDF son procesados mediante PyPDF.
2. El contenido se separa por artículos y posteriormente se divide en chunks.
3. Los chunks se convierten en embeddings y se almacenan en FAISS.
4. La pregunta del usuario pasa por Query Rewriting.
5. La consulta se utiliza para realizar una búsqueda semántica en FAISS.
6. MMR selecciona los resultados finales.
7. Los resultados recuperados forman el contexto.
8. Gemini genera la respuesta utilizando la pregunta original y el contexto recuperado.

---

## 5. Requisitos

Para ejecutar el proyecto se requiere:

* Cuenta de Google para utilizar Google Colab.
* API Key de Google Gemini.
* Los dos documentos PDF utilizados como fuente de información.
* Conexión a Internet.

---

## 6. Configuración de la API Key

La API Key debe guardarse en los secretos de Google Colab.

En **Google Colab → Secrets**, crear un secreto con el nombre:

```text
GOOGLE_API_KEY
```

El notebook obtiene la clave mediante:

```python
API_KEY = userdata.get("GOOGLE_API_KEY")
```

**No se debe escribir la API Key directamente en el código ni subirla al repositorio.**

---

## 7. Instalación

Las dependencias necesarias se instalan mediante:

```python
!pip install -q pypdf langchain langchain-community langchain-google-genai langchain-text-splitters faiss-cpu sentence-transformers
```

---

# 8. Ejecución del sistema

### Paso 1 — Abrir el notebook

Abrir el archivo:

```text
notebook_RAG.ipynb
```

utilizando **Google Colab**.

### Paso 2 — Configurar la API Key

En Google Colab, abrir **Secrets** y crear:

```text
GOOGLE_API_KEY
```

Ingresar como valor una API Key válida de Google Gemini.

### Paso 3 — Instalar las dependencias

Ejecutar la primera celda del notebook y esperar hasta que finalice la instalación.

### Paso 4 — Importar las librerías

Ejecutar las siguientes celdas para cargar las librerías necesarias.

Si la ejecución es correcta, el notebook mostrará:

```text
Librerías importadas correctamente
```

### Paso 5 — Conectar Gemini

Ejecutar la celda de configuración del modelo.

El sistema utilizará:

```text
gemini-3.5-flash-lite
```

Si la API Key está correctamente configurada, se mostrará:

```text
Gemini 3.5 Flash Lite conectado correctamente
```

### Paso 6 — Cargar los documentos

Ejecutar la celda de carga de archivos.

Cuando Google Colab solicite seleccionar los archivos, cargar:

```text
Reglamento ley de copropiedad inmobiliaria (1).pdf
ACTUALIZACION_REGLAMENTO_DE_COPROPIEDAD_LEY_N_21.442_2024 (1).pdf
```

### Paso 7 — Procesar los documentos

Ejecutar las celdas siguientes.

El sistema realizará automáticamente:

1. Extracción del texto de los PDF.
2. Separación del contenido por artículos.
3. División en chunks.
4. Generación de embeddings.
5. Creación del índice vectorial FAISS.

Una vez finalizado este proceso, el sistema estará preparado para recibir consultas.

---

# 9. ¿Cómo realizar una pregunta?

Una vez ejecutado el notebook y cargados los documentos, el usuario puede realizar una consulta modificando la variable `pregunta` en la celda correspondiente.

Por ejemplo:

```python
pregunta = "¿Qué son los gastos comunes extraordinarios?"
```

Luego se deben ejecutar las celdas correspondientes al proceso de consulta.

El sistema realizará automáticamente las siguientes etapas:

```text
Pregunta del usuario
        ↓
Query Rewriting
        ↓
Búsqueda semántica
        ↓
FAISS
        ↓
MMR
        ↓
Contexto recuperado
        ↓
Gemini
        ↓
Respuesta
```

### Ejemplos de preguntas

```text
¿Qué son los gastos comunes extraordinarios?
```

```text
¿Quién debe pagar los gastos comunes?
```

```text
¿Qué ocurre si una propiedad permanece desocupada?
```

```text
¿Qué obligaciones económicas tiene un copropietario?
```

---

# 10. Demostración de salida

A continuación se muestra un ejemplo de una consulta realizada al sistema.

### Pregunta

```text
¿Qué son los gastos comunes extraordinarios?
```

### Consulta optimizada

El sistema transforma la pregunta antes de realizar la búsqueda semántica:

```text
gastos comunes extraordinarios
```

### Resultados recuperados

El sistema recupera fragmentos relacionados con la consulta y los procesa mediante MMR.

Ejemplo:

```text
RESULTADO MMR #1

Artículo: ARTICULO TRIGESIMO CUARTO. GASTOS EXTRAORDINARIOS
Fuente: ACTUALIZACION_REGLAMENTO_DE_COPROPIEDAD_LEY_N_21.442_2024 (1).pdf

Los gastos comunes extraordinarios son los adicionales o diferentes
de los gastos comunes ordinarios y las sumas destinadas a nuevas
obras comunes...
```

### Respuesta generada

```text
================================================================================
RESPUESTA FINAL DEL RAG
================================================================================

Respuesta:
Los gastos comunes extraordinarios son aquellos adicionales o diferentes
de los gastos comunes ordinarios, además de las sumas destinadas a nuevas
obras comunes.

Fundamento:
Artículo Trigésimo Cuarto. Gastos Extraordinarios.

Fuente:
ACTUALIZACION_REGLAMENTO_DE_COPROPIEDAD_LEY_N_21.442_2024 (1).pdf
```

La salida permite comprobar la relación entre la **pregunta**, la **información recuperada** y la **respuesta generada**.

---

# 11. Realizar una nueva consulta

Para realizar otra pregunta durante la misma sesión:

1. Volver a la celda donde se encuentra la variable `pregunta`.
2. Reemplazar la pregunta anterior.
3. Ejecutar nuevamente las celdas correspondientes al proceso de consulta.
4. Revisar la nueva respuesta generada.

Por ejemplo:

```python
pregunta = "¿Qué obligaciones económicas tiene un copropietario?"
```

No es necesario volver a cargar los documentos ni reconstruir el índice FAISS mientras la sesión de Google Colab permanezca activa.

---

# 12. Parámetros principales

### Chunking

```python
chunk_size=1500
chunk_overlap=250
```

Los artículos se dividen en fragmentos de aproximadamente 1500 caracteres, utilizando una superposición de 250 caracteres entre fragmentos consecutivos.

### MMR

```python
k=4
fetch_k=10
lambda_mult=0.7
```

* `k`: cantidad de resultados seleccionados finalmente.
* `fetch_k`: cantidad de candidatos considerados antes de aplicar MMR.
* `lambda_mult`: controla el equilibrio entre relevancia y diversidad de los resultados.

---

# 13. Validación

Para validar el funcionamiento del sistema se pueden realizar preguntas relacionadas con el contenido de los reglamentos.

Ejemplos:

```text
¿Qué son los gastos comunes extraordinarios?
```

```text
¿Quién debe pagar los gastos comunes?
```

```text
¿Qué obligaciones económicas tiene un copropietario?
```

```text
¿Qué ocurre si una propiedad permanece desocupada?
```

Durante la validación se debe comprobar que:

* Los resultados recuperados sean relevantes para la pregunta.
* El contexto contenga información relacionada.
* La respuesta esté respaldada por el contexto recuperado.
* Se indique el artículo cuando esté disponible.
* Se indique la fuente utilizada.
* El sistema pueda abstenerse cuando no exista información suficiente para responder con seguridad.

---

# 14. Estructura del proyecto

```text
/
├── README.md
├── notebook_RAG.ipynb
├── Reglamento ley de copropiedad inmobiliaria (1).pdf
└── ACTUALIZACION_REGLAMENTO_DE_COPROPIEDAD_LEY_N_21.442_2024 (1).pdf
```

Los nombres de los archivos pueden variar según la estructura final del repositorio.

---

# 15. Seguridad

La API Key de Google Gemini es información privada.

No debe:

* Escribirse directamente dentro del código.
* Subirse al repositorio.
* Compartirse públicamente.

Se debe utilizar el sistema de **Secrets de Google Colab** mediante:

```text
GOOGLE_API_KEY
```
