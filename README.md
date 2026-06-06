# 🚑 Proyecto MOTHER: Motor Operativo de Triage Hospitalario ante Emergencias y Respuesta

Este proyecto desarrolla un ecosistema predictivo (MLOps) para clasificar la gravedad de siniestros viales en la Ciudad Autónoma de Buenos Aires (CABA), usando datos contextuales y técnicas avanzadas de ciencia de datos y machine learning.

## 🎯 Objetivo del Proyecto
El objetivo principal de MOTHER es **optimizar la asignación de recursos médicos de emergencia y reducir los tiempos de respuesta**. A través de un modelo de Machine Learning de clasificación binaria (Riesgo Mortal vs. Leve/Basal), el sistema busca predecir la probabilidad de fatalidad en el instante en que ingresa el reporte.

* **Usuario Final:** Este modelo está diseñado como una herramienta operativa para **operadores de despacho de emergencias (SAME / 911)**, sugiriendo el despliegue dinámico de ambulancias o helicópteros sanitarios según el nivel de alerta.

---

## 🏗️ Arquitectura del Ecosistema MLOps

El proyecto MOTHER ha evolucionado de un simple modelo predictivo a un ecosistema de software completo con trazabilidad y automatización. Se compone de cuatro grandes bloques:

1. **El Cerebro Predictivo (LightGBM):** Modelo entrenado con estrategias avanzadas de balanceo (Undersampling + Class Weights) empaquetado en un artefacto inmutable (`.pkl`).
2. **La API REST (FastAPI):** El servidor local que expone el modelo y recibe las consultas en tiempo real.
3. **El Registro Operativo (MongoDB):** Una "Caja Negra" NoSQL que guarda la configuración táctica del modelo, las métricas de negocio y un registro auditable de cada predicción realizada desde el frontend.
4. **El Motor de Reentrenamiento (Papermill):** Un orquestador automatizado que inyecta nuevos datos en el cuaderno Jupyter, reentrena el modelo de forma invisible y genera reportes inmutables para auditoría.

---

## 📂 Estructura del Repositorio

El proyecto sigue una arquitectura de directorios estándar para proyectos de Machine Learning en producción:

```text
MOTHER/
├── auditoria_modelos/         
├── data/                      
│   ├── processed/             
│   └── raw/                   
├── inputs/                    
├── models/                    
├── assets/                    
├── notebooks/                 
├── scripts/                   
├── venv/                      
├── .gitignore                 
├── cargar_db.py               
├── ejemplo_prediccion.py      
├── interfaz_mother.html       
├── logging_automatizacion_reentrenamiento.log 
├── logging_motor_prediccion.log               
├── main.py                    
├── pipeline_papermill.py      
├── README.md                  
└── requirements.txt           
📖 Descripción de Directorios
auditoria_modelos/: Almacena los reportes inmutables (.ipynb ejecutados) generados automáticamente por Papermill cada vez que se reentrena el modelo.

data/: Contiene los conjuntos de datos. Suele subdividirse en raw/ (archivos crudos originales del gobierno) y processed/ (datasets limpios e integrados listos para el modelo). (Nota: Por el peso, los archivos CSV grandes están ignorados en el .gitignore).

inputs/: Contiene el Cuaderno Jupyter maestro parametrizado (3_modelado_evaluacion.ipynb) que utiliza Papermill como plantilla para ejecutar los reentrenamientos.

models/: Directorio donde se guardan los artefactos binarios (.pkl) empaquetados por el pipeline, listos para ser consumidos por la API.

assets/: Recursos visuales del proyecto, como diagramas de arquitectura, logotipos y capturas de pantalla de la interfaz de usuario.

notebooks/: Cuadernos de Jupyter utilizados para el Análisis Exploratorio de Datos (EDA) inicial, limpieza y pruebas de concepto.

scripts/: Módulos de Python con funciones auxiliares y utilidades reutilizables.

Archivos Raíz (.py, .html, .log): Los scripts principales de ejecución, incluyendo el servidor backend (main.py), el orquestador (pipeline_papermill.py), el frontend (interfaz_mother.html) y los registros del sistema.

⚙️ Guía de Instalación y Despliegue Local
Sigue estos pasos rigurosamente para levantar el entorno completo en tu máquina.

Paso 1: Clonar el Repositorio
Bash
git clone [https://github.com/tu-usuario/MOTHER.git](https://github.com/tu-usuario/MOTHER.git)
cd MOTHER
Paso 2: Crear y Activar Entorno Virtual
En Windows:

Bash
python -m venv venv
venv\Scripts\activate
En Linux/macOS:

Bash
python3 -m venv venv
source venv/bin/activate
Paso 3: Instalar Dependencias
Instala todas las librerías necesarias, incluyendo las herramientas de MLOps:

Bash
pip install -r requirements.txt
Paso 4: Preparar Datos Crudos
Coloca los archivos originales de datos CSV descargados desde las fuentes oficiales dentro de la carpeta data/raw/.

Paso 5: Registrar el Kernel de Python (Para Papermill)
Para que el motor de automatización reconozca tu entorno virtual al reentrenar cuadernos, ejecuta:

Bash
python -m ipykernel install --user --name=python3
Paso 6: Configurar MongoDB (El Registro MLOps)
El sistema utiliza MongoDB para guardar logs operativos.

Descarga e instala MongoDB Community Server.

Asegúrate de instalar también MongoDB Compass (el visor gráfico).

Levanta el servicio (suele correr por defecto en mongodb://localhost:27017/).

Inicializa la Base de Datos Histórica: Ejecuta el script de carga inicial para subir los parámetros, métricas y datos de auditoría a la base de datos:

Bash
python cargar_db.py
🚀 Uso del Ecosistema en Producción
1. Levantar el Servidor (Backend)
Con el entorno virtual activado, ejecuta el siguiente comando para encender la API de predicción:

Bash
uvicorn main:app --reload
(El servidor quedará escuchando en el puerto 8000. Los eventos quedarán registrados en logging_motor_prediccion.log).

2. Abrir la Interfaz de Usuario (Frontend)
El frontend está completamente desacoplado. No necesitas un servidor web adicional para visualizarlo:

Ve a la carpeta raíz del proyecto.

Haz doble clic en el archivo interfaz_mother.html para abrirlo en tu navegador.

Ingresa los datos de un siniestro y presiona "Realizar Triage". Verás la alerta visual (Código Rojo o Verde) y el log se guardará en MongoDB.

3. Ejecutar Reentrenamiento Automático (Papermill)
Si ingresan nuevos datos al sistema y deseas reentrenar el modelo generando un reporte auditable, ejecuta el robot orquestador:

Bash
python pipeline_papermill.py
Resultado: Se generará un nuevo modelo .pkl en la carpeta models/, un reporte inmutable con las gráficas de validación en la carpeta auditoria_modelos/, y la operación quedará registrada en logging_automatizacion_reentrenamiento.log.