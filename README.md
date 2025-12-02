# 🕵️‍♂️ HubSpot Data Auditor

Herramienta automatizada en Python para auditar, diagnosticar y limpiar la calidad de los datos en HubSpot CRM. Este script analiza exportaciones masivas de objetos (Empresas, Contactos, Negocios, etc.) cruzándolas con sus definiciones de propiedades para detectar "basura", duplicados y datos inútiles.

## 🚀 Características Principales

  * **Detección Inteligente:** Identifica automáticamente qué archivo es de *Propiedades* y cuál es de *Datos* (Empresas, Contactos, Custom Objects, etc.) sin necesidad de renombrarlos.
  * **Matching Automático:** Empareja el archivo de datos con su mapa de propiedades correcto (ej: `All-Deals.csv` con `properties-deals.csv`).
  * **Análisis Forense de Datos:**
      * **Ghost Data:** Detecta propiedades personalizadas que tienen datos pero **no se usan** en ninguna automatización, lista o reporte.
      * **Data Monótona:** Detecta campos donde todos los registros tienen el mismo valor (información estática que no segmenta).
      * **Campos Vacíos:** Identifica propiedades Custom que están 100% vacías.
  * **Búsqueda de Duplicados:** Utiliza algoritmos de *Fuzzy Matching* (lógica difusa) para encontrar propiedades con nombres muy similares (ej: `Mobile Phone` vs `Mobile_Phone`).

## 📋 Requisitos Previos

  * **Python 3.x** instalado.
  * Librerías necesarias (ver instalación).

## 🛠️ Instalación

1.  Clona este repositorio o descarga el script `auditor.py`.
2.  Instala las dependencias ejecutando:

<!-- end list -->

```bash
pip install pandas openpyxl fuzzywuzzy python-Levenshtein
```

*(Nota: `python-Levenshtein` es opcional pero recomendado para acelerar el proceso de comparación).*

## ▶️ Cómo Usar

1.  **Ejecuta el script por primera vez:**

    ```bash
    python auditor.py
    ```

    El script creará automáticamente dos carpetas: `input` y `output`.

2.  **Carga los archivos:**
    Coloca tus archivos CSV o Excel exportados de HubSpot en la carpeta **`input/`**.

      * Necesitas el export de **Datos** (ej: `all-companies.csv`).
      * Necesitas el export de **Propiedades** (ej: `hubspot-properties-export...csv`).

3.  **Ejecuta el análisis:**
    Vuelve a correr el comando:

    ```bash
    python auditor.py
    ```

    Sigue las instrucciones en pantalla (te pedirá un umbral de similitud, por defecto 92).

4.  **Revisa los resultados:**
    El reporte se generará en la carpeta **`output/`** con el nombre `Auditoria_[NombreArchivo].xlsx`.

## 📊 Entendiendo el Reporte (Excel)

El archivo generado contiene varias pestañas dinámicas (solo aparecen si se detectan problemas):

| Pestaña (Tab) | Descripción | Acción Recomendada |
| :--- | :--- | :--- |
| **Todas** | Vista general de todas las columnas analizadas con sus métricas (Fill Rate, Usos, Creador). | Referencia general. |
| **Custom Empty (Delete)** | Propiedades creadas por usuarios (`Custom`) que están **100% vacías** en la base de datos. | **🗑️ Borrar.** No contienen datos. |
| **Not Used in Automation** | "Ghost Data". Propiedades `Custom` que **tienen datos** pero HubSpot reporta **0 usos** (ni workflows, ni listas). | **⚠️ Revisar y Borrar.** Son datos huérfanos. |
| **Potential Duplicates** | Pares de propiedades con nombres muy similares (similitud \> 92%). | **🔄 Fusionar.** Probablemente son redundantes. |
| **Constant value** | Propiedades llenas pero con **valor constante** (ej: 5000 filas y todas dicen "YES"). | **🗄️ Archivar.** No sirven para segmentar. |

## ⚙️ Lógica Técnica

  * **Criterio Ghost Data:** `Type == 'Custom'` AND `Fill Rate > 0` AND `HubSpot Usages == Empty`.
  * **Criterio Monótono:** `Rows > 50` AND `Unique Values <= 1`.
  * **Batch Processing:** El script puede procesar múltiples archivos a la vez (Empresas y Contactos en una sola ejecución) y salta los que ya han sido procesados.

## 🤝 Contribución

Si encuentras un bug o quieres mejorar la lógica de detección, ¡haz un PR\!

-----

*Desarrollado para optimización de HubSpot CRM.*
