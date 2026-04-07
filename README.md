
# 🧠 Sistema RAG en Español con LangChain
**Autor:** Fernando Gutierrez  
**GitHub:** [L-FER-GT](https://github.com/L-FER-GT)  
**Fecha:** 06 de abril de 2026

---

## 📋 Descripción

Sistema de **Retrieval-Augmented Generation (RAG)** implementado en español que combina un modelo de lenguaje (LLM) con recuperación semántica de documentos para generar respuestas fundamentadas en contexto real. En lugar de depender únicamente del conocimiento interno del modelo, el sistema consulta una base de datos vectorial construida a partir de documentos externos y utiliza los fragmentos más relevantes como contexto para la generación.

El caso de uso incluido como demostración es una **base de conocimiento sobre Elden Ring**, pero la arquitectura es completamente adaptable a cualquier dominio o documento.

---

## 🏗️ Arquitectura

```
Documento (.txt)
      │
      ▼
 Chunking (manual por "|" o automático)
      │
      ▼
 Embeddings (sentence-transformers/all-mpnet-base-v2)
      │
      ▼
 ChromaDB (base vectorial persistente)
      │
      ▼
 Retriever (similarity / MMR)
      │
      ▼
 RetrievalQA Chain (LangChain) ◄── LLM (Mistral 7B / Falcon / Zephyr / Gemma)
      │
      ▼
 Respuesta en español
```

---

## ⚙️ Stack Tecnológico

| Componente | Tecnología |
|---|---|
| LLM | Mistral-7B-Instruct-v0.2 (configurable) |
| Framework LLM | Hugging Face Transformers |
| Orquestación | LangChain 0.2.16 |
| Embeddings | sentence-transformers/all-mpnet-base-v2 |
| Base vectorial | ChromaDB 0.5.5 |
| Cuantización | bitsandbytes (4-bit) |
| Entorno | Google Colab (GPU T4 recomendada) |

---

## 🚀 Inicio Rápido

### 1. Abrir en Google Colab

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

Subir el notebook `Sistema-RAG-con-LangChain.ipynb` a Colab o ejecutarlo desde GitHub.

### 2. Activar GPU

> Entorno de ejecución → Cambiar tipo de entorno de ejecución → **GPU**

### 3. Obtener token de Hugging Face

1. Crear cuenta en [huggingface.co](https://huggingface.co/)
2. Ir a **Settings → Access Tokens**
3. Crear un token de tipo **Read**
4. Pegarlo en la celda de autenticación del notebook

### 4. Ejecutar el notebook

Ejecutar las celdas en orden. Tras instalar dependencias, **reiniciar el entorno** y volver a ejecutar desde la primera celda.

---

## 📂 Estructura del Proyecto

```
├── notebook/
│   └── Sistema-RAG-con-LangChain.ipynb   # Notebook principal
├── utils/
│   └── elden_ring_info.txt      # Documento de ejemplo (base de conocimiento)
└── README.md
```

---

## 📓 Contenido del Notebook

El notebook está organizado en las siguientes secciones:

1. **Verificación del Entorno y Hardware** — Comprueba que haya GPU disponible.
2. **Instalación y Preparación del Entorno** — Instala todas las dependencias con versiones fijadas.
3. **Importación de Dependencias** — PyTorch, Transformers, LangChain, ChromaDB.
4. **Autenticación con Hugging Face** — Login con token para descargar modelos.
5. **Carga y Configuración del Modelo LLM** — Carga del modelo con cuantización 4-bit.
6. **Creación del Pipeline de Generación** — Pipeline de text-generation con Transformers.
7. **Pruebas Básicas del Modelo (Standalone)** — Inferencia directa sin RAG para validar el modelo.
8. **Integración con LangChain** — Envuelve el pipeline en `HuggingFacePipeline`.
9. **Carga y Preparación de Documentos** — Carga desde archivo local o URL remota.
10. **Fragmentación (Chunking)** — División manual por separador `|` o automática con `RecursiveCharacterTextSplitter`.
11. **Generación de Embeddings** — Vectorización semántica con sentence-transformers.
12. **Creación de Base Vectorial** — Indexación persistente en ChromaDB.
13. **Configuración del Sistema RAG** — Prompt en español + Retriever + RetrievalQA chain.
14. **Evaluación del Sistema RAG** — Consultas de prueba y análisis del retrieval.

---

## 🤖 Modelos Soportados

El notebook permite seleccionar entre los siguientes modelos (todos de acceso libre):

| Modelo | Parámetros |
|---|---|
| `mistralai/Mistral-7B-Instruct-v0.2` | 7B (por defecto) |
| `tiiuae/falcon-7b-instruct` | 7B |
| `HuggingFaceH4/zephyr-7b-beta` | 7B |
| `google/gemma-2b-it` | 2B |

Todos se cargan con cuantización 4-bit para funcionar en GPUs con VRAM limitada (como la T4 de Colab con 16GB).

---

## 🔧 Configuración Personalizable

El notebook expone parámetros interactivos mediante formularios de Colab:

- **Modelo LLM**: seleccionable desde un dropdown.
- **Fuente del documento**: local o URL remota.
- **Modo de chunking**: manual (separador `|`) o automático (por tamaño de caracteres).
- **Fragmentos recuperados**: cantidad de chunks que el retriever devuelve.
- **Tipo de cadena**: `stuff`, `map_reduce` o `refine`.
- **Tipo de búsqueda**: `similarity` o `mmr`.
- **Parámetros de generación**: `max_length`, `top_k`, `temperature`.

---

## 📄 Formato del Documento de Conocimiento

El archivo `.txt` utiliza el carácter `|` como separador entre chunks temáticos. Cada chunk contiene información completa sobre un tema específico (un jefe, un área, una mecánica, etc.).

```
TEMA 1: Descripción completa del tema uno con toda la información relevante...
|
TEMA 2: Descripción completa del tema dos con toda la información relevante...
|
TEMA 3: ...
```

Este formato permite un chunking semántico manual que respeta los límites temáticos del contenido, evitando que la información de un tema se corte y mezcle con otro.

---

## 💡 Ejemplo de Uso

**Pregunta:** *¿Quién es Malenia?*

El sistema:
1. Convierte la pregunta en un embedding.
2. Busca los fragmentos más similares en ChromaDB.
3. Inyecta los fragmentos como contexto en el prompt.
4. El LLM genera una respuesta basada exclusivamente en ese contexto.

---

## 📦 Dependencias Principales

```
torch
transformers < 5.0.0
accelerate
bitsandbytes
chromadb == 0.5.5
langchain == 0.2.16
langchain-community == 0.2.16
langchain-huggingface == 0.0.3
sentence-transformers
numpy == 1.26.4
```

---

## ⚠️ Notas Importantes

- **GPU requerida**: el proyecto no funciona en CPU por los requisitos de VRAM del modelo.
- **Reinicio obligatorio**: tras instalar dependencias, reiniciar el entorno antes de continuar.
- **Token de Hugging Face**: necesario para descargar modelos con licencia.
- **VRAM**: los modelos 7B cuantizados a 4-bit requieren aproximadamente 5-6 GB de VRAM.

---

## 📜 Licencia

Este proyecto es de uso educativo y demostrativo. Los modelos utilizados están sujetos a sus respectivas licencias en Hugging Face.