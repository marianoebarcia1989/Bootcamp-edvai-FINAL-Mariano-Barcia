# 🏆 Proyecto Final: Análisis Estratégico de Bienestar y Hábitos Saludables

## 🌟 Descripción General

Este proyecto de análisis de datos se centra en transformar un conjunto de datos transaccionales de *Wellness* (`STATUSWELLBEING final.csv`) en *insights* estratégicos para el área de **Recursos Humanos (RR. HH.)**. El objetivo es evaluar la correlación entre los hábitos de ejercicio, la nutrición y los resultados biométricos de la población, permitiendo a la empresa diseñar programas de bienestar basados en evidencia.

* **Herramientas:** Kaggle, Power BI Desktop, Power Query (M), DAX.
* **Concepto Central:** Implementación de un Modelo Dimensional (Esquema Estrella).

---

## 🎯 Objetivo

El objetivo principal es responder a las hipótesis de negocio mediante la creación de un modelo de datos robusto y un dashboard interactivo en Power BI, que permita a RR. HH. tomar decisiones informadas sobre la gestión del talento y la promoción de un estilo de vida saludable.

## 💼 Caso de Estudio: Wellness Corporativo

La empresa busca optimizar la salud de sus empleados y por ende su calidad de vida y productividad. El análisis se enfoca en segmentar a la población por demografía (edad, género, alimentación y actividad física) para identificar dónde se están logrando los mejores resultados de y dónde se requieren programas de intervención focalizados.

---

## 💡 Hipótesis de Negocio

El proyecto se desarrolla para validar las siguientes cinco hipótesis clave:

1. **H1: Edad vs. Rendimiento:** Los usuarios más jóvenes (`< 30`) queman más calorías promedio por sesión que los mayores (`>= 40`).
2. **H2: Frecuencia de Entrenamientos:** La frecuencia de entrenamientos semanal varía significativamente entre hombres y mujeres según su rango etario.
3. **H3: BMI y Dieta:** Existe una relación identificable entre la clasificación del BMI (Normal, Sobrepeso, Obesidad) y el tipo de dieta (`diet_type`) predominante.
4. **H4: Eficiencia Calórica:** Existen tipos de ejercicio (`Workout_Type`) más eficientes, medidos por la métrica de **`Calorias_Quemadas_Por_Hora`**.
5. **H5: % Grasa vs. Objetivo:** Las personas con un mayor porcentaje de grasa (`Fat_Percentage`) tienen un objetivo de quema de calorías (`Burns_Calories_bin`) más alto.

---

## 📈 Plan de Métricas

| Hipótesis | Métrica / KPI (Nombre DAX) | Fuente de Datos (Tabla: Columna) | Cruces y Segmentación | Fórmula DAX Principal |
| --- | --- | --- | --- | --- |
| **H1 (Rendimiento)** | `Calorias_Quemadas_Promedio_Sesion` | `fact_mediciones`: `Calories_Burned` | `dim_person`: **Segmento_Etario**, `Gender` | `AVERAGE('fact_mediciones'[Calories_Burned])` |
| **H2 (Frecuencia)** | `Frecuencia_Entrenamiento_Promedio` | `fact_mediciones`: `Workout_Frequency _days_week` | `dim_person`: **Gender**, **Segmento_Etario** | `AVERAGE('fact_mediciones'[Workout_Frequency _days_week])` |
| **H3 (Salud)** | `BMI_Promedio` | `fact_mediciones`: `BMI` | `dim_meal`: `diet_type`; `fact_mediciones`: **Clasificacion_BMI** | `AVERAGE('fact_mediciones'[BMI])` |
| **H4 (Eficiencia)** | `Eficiencia_Calorica_Por_Hora` | `fact_mediciones`: `Calories_Burned`, `Session_Duration_hours` | `dim_workout_type`: `Workout_Type` | `DIVIDE(SUM(CB), SUM(SDH), 0)` |
| **H5 (Correlación)** | `Porcentaje_Grasa_Promedio` | `fact_mediciones`: `Fat_Percentage` | `fact_mediciones`: **Burns_Calories_bin** | `AVERAGE('fact_mediciones'[Fat_Percentage])` |

---

## 🔑 Principales Medidas en DAX

### 1. Medidas DAX (KPIs)

```dax
Calorias_Quemadas_Promedio_Sesion = 
    AVERAGE ( fact_mediciones[Calories_Burned] )

Eficiencia_Calorica_Por_Hora = 
    DIVIDE (
        SUM ( fact_mediciones[Calories_Burned] ),
        SUM ( fact_mediciones[Session_Duration_hours] ),
        0
    )

Total_Usuarios = 
    DISTINCTCOUNT ( dim_person[person_key] )

```

### 2. Columnas Calculadas (Segmentación)

```dax
Segmento_Etario =
IF (
    'dim_person'[Age] < 30,
    "A. Joven (<30)",
    IF (
        'dim_person'[Age] < 40,
        "B. Mediana (30-39)",
        "C. Superior (>=40)"
    )
)

Clasificacion_BMI = 
SWITCH (
    TRUE(),
    'fact_mediciones'[BMI] < 18.5, "1. Bajo Peso",
    'fact_mediciones'[BMI] <= 24.9, "2. Peso Normal",
    'fact_mediciones'[BMI] <= 29.9, "3. Sobrepeso",
    "4. Obesidad"
)

```

---

## 🛠️ Desarrollo del Proyecto

### Columnas del Dataset (`STATUSWELLBEING final.csv`)

El archivo de origen está delimitado por `;`. La complejidad principal radica en la alta dimensionalidad y la necesidad de corrección en el formato y nombre de algunas columnas clave (`Weight _kg`).

### 🔗 Diagrama Entidad-Relación (DER) / Código en dbdiagram.io
## 🛠️ Desarrollo del Proyecto

### 🔗 Diagrama Entidad-Relación (DER)
El modelo implementado en Power BI sigue un **Esquema Estrella**, lo que permite una navegación fluida y un rendimiento óptimo de las medidas DAX.

<img width="956" height="660" alt="Untitled (1)" src="https://github.com/user-attachments/assets/84234598-8161-449e-b407-33113051d789" />

### Código en dbdiagram.io
 [dbdiagram.io](https://dbdiagram.io):

```sql
Table dim_person {
  person_key INT [pk]
  Age INT
  Gender VARCHAR
  Experience_Level VARCHAR
  Segmento_Etario VARCHAR 
}

Table dim_meal {
  meal_key INT [pk]
  diet_type VARCHAR
  meal_type VARCHAR
}

Table dim_date {
  date_key INT [pk]
  FECHA_INGRESO DATE
  Year INT
}

Table fact_mediciones {
  fact_mediciones_id INT [pk] 
  person_key INT 
  meal_key INT 
  date_key INT 
  Calories_Burned DECIMAL
  Session_Duration_hours DECIMAL
  Weight_kg DECIMAL 
  BMI DECIMAL
  Fat_Percentage DECIMAL
  "Workout_Frequency _days_week" DECIMAL
  Clasificacion_BMI VARCHAR 
}

Ref: fact_mediciones.person_key > dim_person.person_key
Ref: fact_mediciones.meal_key > dim_meal.meal_key
Ref: fact_mediciones.date_key > dim_date.date_key

---

### 🌊 Transformación y Carga de Datos: Pipeline de Datos

El flujo de datos sigue un proceso ETL que se realiza completamente en el **Editor de Power Query** para garantizar la limpieza, dimensionalización y creación de claves subrogadas.
<img width="501" height="368" alt="Diseño sin título (5)" src="https://github.com/user-attachments/assets/665894a3-cac4-4186-86b7-5795449ef9d1" />
### Proceso ETL (Power Query - Lenguaje M)

1. **Limpieza de Datos:** Corrección de formatos de texto (reemplazo de `,` por `.`), tipificación de datos numéricos y corrección del nombre de la columna `Weight _kg`.
2. **Dimensionalización:** Creación de consultas separadas para `dim_person`, `dim_meal`, y `dim_date`.
3. **Generación de Claves:** Creación de Claves Primarias (Índices) en las dimensiones y Claves Foráneas en la tabla de hechos (`fact_mediciones`) mediante operaciones de *Merge*.
---

### 📶 Modelo de datos en Power BI

La correcta configuración del Modelo Estrella es crucial. Todas las relaciones son **activa, unidireccional** y de **uno a muchos (1:N)**, asegurando que los filtros y las métricas DAX se propaguen correctamente desde las dimensiones hacia la tabla de hechos.

---

## 📝 Conclusiones

### Validación de Hipótesis

* **H1 (Edad vs. Rendimiento):** **Rechazada**. Los datos no muestran una ventaja clara para el grupo de usuarios más jóvenes. El análisis de `Calorias_Quemadas_Promedio_Sesion` revela un promedio equilibrado entre el grupo Joven y el grupo Senior.
* **H3 (BMI y Dieta):** Se valida una fuerte correlación entre las categorías de dieta y la `Clasificacion_BMI`, siendo un factor clave para la categorización de salud.

### Insights de RR. HH. y Propuestas de Acción 2026

* **Prioridad Joven:** Se detectó que el **37% de los jóvenes menores de 30 años** posee un porcentaje de sobrepeso superior al de las personas mayores de 35 años.
* **Oportunidad de Intervención:** Esta anomalía nos llevará a la creación de planes de acción 2026 para el bienestar de nuestros empleados. Identificamos como oportunidad la creación de **nuevas actividades y beneficios nutricionales para los jóvenes menores de 30 años**.
* **Objetivo Estratégico:** El grupo Joven es la categoría de edades que deberá lograr el objetivo más alto de quema de grasa, lo que justifica una campaña nutricional y de *fitness* focalizada en ellos.

---

## 🔗 Links

| Recurso | Enlace |
| --- | --- |![Diapositiva1](https://github.com/user-attachments/assets/df8df8b2-5d84-42dc-bf71-b51ff2ec7731)
![Diapositiva2](https://github.com/user-attachments/assets/6adc1847-f80f-4d0d-9ce4-11aaab68aeb1)
![Diapositiva3](https://github.com/user-attachments/assets/ea129a3e-17bc-4be3-b90a-df1ae0493693)
![Diapositiva4](https://github.com/user-attachments/assets/27b37209-0345-451e-8271-0c83a68c4f74)

* **Dashboard Interactivo:** [Ver Dashboard en Power BI Service](https://app.powerbi.com/view?r=eyJrIjoiYTc4NDYzOGQtMjhjYS00NGI1LTg5MzUtZjc1YTJiZWRiYWJkIiwidCI6IjUyMWU0MzBmLWJhM2MtNGQ0Ny05Zjk3LTE2Yjk0NWIwYTg1MCIsImMiOjR9)
* **Repositorio de Datos Archivo CSV (Data Source) :** [Dataset Original en GitHub](https://github.com/tu_usuario/tu_repositorio/blob/main/STATUSWELLBEING%20final.csv)
| **Dashboard Power BI Interactivo** | * [Ver Dashboard en Power BI Service] 

