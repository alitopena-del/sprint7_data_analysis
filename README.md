# sprint7_data_analysis

# Análisis de Clientes y Consumo | ConnectaTel

Este repositorio contiene el análisis exploratorio de datos (EDA) y la segmentación de la base de clientes de ConnectaTel durante el período 2024. El foco principal estuvo en sanear anomalías en los registros, analizar hábitos reales de comunicación y entender qué tanto se diferencian los usuarios de los planes Básico y Premium.

---

## 1. ¿De qué trata el proyecto?

El propósito fue revisar a fondo el comportamiento de consumo (llamadas y mensajes de texto) de los usuarios para:
* Detectar inconsistencias, errores de captura y datos nulos en los registros de telecomunicaciones.
* Evaluar si el plan que tiene contratado un usuario realmente se traduce en un nivel de consumo distinto.
* Construir segmentos claros por edad e intensidad de uso que sirvan para tomar decisiones comerciales y ajustar la oferta actual.

---

## 2. Datos utilizados

El análisis parte del cruce de dos fuentes de datos:

* **`users.csv`**: Base de clientes (5,000 registros). Contiene identificador único (`user_id`), edad (`age`), ciudad de residencia (`city`), fecha de alta en el servicio (`reg_date`) y tipo de suscripción (`plan`: Básico o Premium).
* **`usage.csv`**: Registro transaccional de actividad (aprox. 40,000 eventos). Incluye usuario (`user_id`), fecha del evento (`date`), tipo de servicio (`type`: llamada o SMS), duración en minutos (`duration`) y longitud en caracteres (`length`).

---

## 3. Etapas del análisis

El flujo de trabajo dentro del notebook se dividió en las siguientes fases:

1. **Auditoría y limpieza de datos:**
   * **Valores centinela en edad:** Corrección de registros con valor `-999`, reemplazándolos con la mediana muestral para evitar sesgos en métricas globales.
   * **Ubicaciones desconocidas:** Sustitución de `?` en la columna de ciudad por `NaN` (representando el 11.3% de los registros).
   * **Fechas anómalas:** Identificación y conversión a `NaT` de fechas futuras con año 2026 en fecha de registro (0.8% de los casos).
   * **Diagnóstico de nulos MAR:** Verificación de que la ausencia de valores en `duration` y `length` no se debe a pérdidas aleatorias, sino a la naturaleza técnica del servicio (los SMS no acumulan minutos y las llamadas no acumulan caracteres). Se mantuvieron como nulos para no adulterar promedios posteriores.

2. **Ingeniería de variables y consolidación:**
   * Agregación a nivel usuario a partir de transacciones para calcular: total de llamadas (`cant_llamadas`), total de mensajes (`cant_mensajes`) y total de minutos hablados (`cant_minutos_llamada`).
   * Cruce de tablas (*left join*) para generar la tabla maestra `user_profile`, rellenando en cero a usuarios sin actividad registrada.

3. **Análisis exploratorio y distribuciones:**
   * Generación de histogramas y densidades segmentados por plan (`hue='plan'`).
   * Comparación de perfiles de uso vs. edad y tipo de paquete contratado.

4. **Tratamiento de valores extremos (Outliers):**
   * Inspección visual mediante diagramas de caja (*boxplots*) y cálculo de límites mediante el rango intercuartílico (IQR).
   * Decisión de negocio: conservación de los usuarios intensivos (*heavy users*), ya que su consumo representa demanda real y no errores de sistema.

5. **Segmentación de clientes:**
   * Creación del atributo `grupo_uso` categorizado en: *Bajo uso*, *Uso medio* y *Alto uso*.
   * Creación del atributo `grupo_edad` clasificado en: *Joven* (< 30), *Adulto* (30 a 59) y *Adulto Mayor* (≥ 60).
   * Visualización de proporciones para conclusiones de negocio.

---

## 4. Cómo ejecutar el proyecto

### Opción A: Directo en Google Colab (Recomendada)
1. Ve a [Google Colab](https://colab.research.google.com/).
2. Haz clic en **Archivo** > **Subir notebook** y selecciona el archivo `.ipynb` de este repositorio.
3. En la barra lateral izquierda, abre la pestaña de archivos (icono de carpeta) y sube los datasets `users.csv` y `usage.csv`.
4. Ve al menú superior, selecciona **Entorno de ejecución** > **Ejecutar todas**.

### Opción B: En entorno local (Jupyter Notebook / Lab)
1. Clona este repositorio o descarga los archivos en tu equipo:
   ```bash
   git clone [https://github.com/tu-usuario/connectatel-eda.git](https://github.com/tu-usuario/connectatel-eda.git)
   cd connectatel-eda

## 5. Guía rápida para reproducir los resultados
Para obtener exactamente las mismas métricas y gráficos:

Asegúrate de correr primero las celdas de corrección de centinelas (-999 y ?) antes de cualquier cálculo estadístico o agregación.

Comprueba que las columnas temporales estén convertidas a tipo datetime antes de filtrar o extraer años.

Ejecuta la agregación de usage antes de crear user_profile para garantizar que las columnas cant_mensajes, cant_llamadas y cant_minutos_llamada queden incorporadas y sin valores nulos en el perfil final.
