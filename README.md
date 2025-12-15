# ¡Por supuesto\! Aquí tienes el `README.md` completo, formateado en Markdown, incluyendo todas las secciones requeridas para tu Trabajo Práctico Integrador y utilizando las convenciones de nombres de tu modelo (`fact_mediciones`, `dim_person`, etc.).

Este documento está listo para ser copiado y pegado en tu repositorio de GitHub.

-----

# 🏋️ Análisis de Bienestar y Hábitos Saludables en la Compañía

## 📄 Descripción General

Este proyecto integra y analiza datos de seguimiento de fitness, salud y nutrición (`STATUSWELLBEING final.csv`) para generar *insights* estratégicos. El análisis está enfocado en el área de **Recursos Humanos (RR. HH.)** para evaluar el impacto de los hábitos de los empleados en su bienestar, productividad y salud.

## 🎯 Objetivo

El objetivo principal es transformar los datos de origen en un **Modelo Dimensional (Esquema Estrella)** en Power BI, y utilizar expresiones DAX avanzadas para calcular métricas clave que permitan:

1.  **Evaluar la eficiencia** del entrenamiento físico y la quema calórica.
2.  **Correlacionar** factores demográficos (edad, género) con la frecuencia y los resultados de salud (BMI).
3.  **Determinar la relación** entre el tipo de dieta y los indicadores biométricos críticos.

-----

## 💼 Caso de Estudio: Wellness Corporativo

La empresa busca optimizar sus programas de bienestar internos. El informe generado a partir de este análisis proporciona la base de datos para responder preguntas cruciales: ¿Cómo varían los niveles de esfuerzo físico entre grupos etarios? ¿Qué correlación existe entre los hábitos nutricionales y los indicadores de sobrepeso/obesidad de la población?

-----

## 💡 Hipótesis de Negocio

El análisis se centra en la validación de las siguientes hipótesis:

1.  **H1: Edad vs. Rendimiento:** Los usuarios más jóvenes (`< 30`) queman más calorías promedio por sesión que los mayores (`>= 40`).
2.  **H2: Frecuencia de Entrenamientos:** La frecuencia de entrenamientos semanal varía significativamente entre hombres y mujeres según su rango etario.
3.  **H3: BMI y Dieta:** Existe una relación identificable entre la clasificación del BMI (Normal, Sobrepeso, Obesidad) y el tipo de dieta (`diet_type`) predominante.
4.  **H4: Eficiencia Calorica:** Existen tipos de ejercicio (`Workout_Type`) más eficientes, medidos por la métrica de `Calorias_Quemadas_Por_Hora`.
5.  **H5: % Grasa vs. Objetivo:** Las personas con un mayor porcentaje de grasa (`Fat_Percentage`) tienen un objetivo de quema de calorías (`Burns_Calories_bin`) más alto.

-----

## 📈 Plan de Métricas

| Tipo | Pregunta de Negocio (Hipótesis) | Métrica / KPI (Nombre DAX) | Fuente de Datos (Tabla: Columna) | Puntos de Vista (Segmentación) | Fórmula DAX Clave |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Comparación** | **H1: Edad vs. Rendimiento.** | `Calorias_Quemadas_Promedio_Sesion` | `fact_mediciones`: `Calories_Burned` | `dim_person`: **Segmento\_Etario**, `Gender` | `AVERAGE('fact_mediciones'[Calories_Burned])` |
| **Frecuencia** | **H2: Frecuencia de Entrenamientos.** | `Frecuencia_Entrenamiento_Promedio` | `fact_mediciones`: `Workout_Frequency_days_week` | `dim_person`: **Gender**, **Segmento\_Etario** | `AVERAGE('fact_mediciones'[Workout_Frequency_days_week])` |
| **Salud** | **H3: BMI y Dieta.** | `BMI_Promedio` | `fact_mediciones`: `BMI` | `dim_meal`: `diet_type`; `fact_mediciones`: **Clasificacion\_BMI** | `AVERAGE('fact_mediciones'[BMI])` |
| **Eficiencia** | **H4: Eficiencia Calorica.** | `Eficiencia_Calorica_Por_Hora` | `fact_mediciones`: `Calories_Burned`, `Session_Duration_hours` | `dim_workout_type`: `Workout_Type` | `DIVIDE(SUM('fact_mediciones'[Calories_Burned]), SUM('fact_mediciones'[Session_Duration_hours]), 0)` |
| **Correlación** | **H5: % Grasa vs. Objetivo.** | `Porcentaje_Grasa_Promedio` | `fact_mediciones`: `Fat_Percentage` | `fact_mediciones`: **Burns\_Calories\_bin** | `AVERAGE('fact_mediciones'[Fat_Percentage])` |

-----

## ⚙️ Desarrollo del Proyecto

### Columnas del Dataset (`STATUSWELLBEING final.csv`)

El archivo CSV de origen es la capa **Bronze/Raw** y contiene una mezcla de datos:

| Columna Representativa | Categoría | Uso en el Modelo |
| :--- | :--- | :--- |
| `Age`, `Gender` | Atributo Demográfico | Clave para la dimensión `dim_person` (H1 y H2). |
| `Calories_Burned` | Métrica de Rendimiento | Métrica central en la tabla de hechos. |
| `BMI`, `Fat_Percentage` | Métrica Biométrica | Indicadores de salud en `fact_mediciones` (H3 y H5). |
| `diet_type`, `meal_type` | Atributo Nutricional | Clave para la dimensión `dim_meal` (H3). |
| `ID` | Transaccional | Usado para generar la clave subrogada `fact_mediciones_id`. |

### 🖼️ Modelo de Datos

El modelo final utilizado en Power BI es un **Esquema Estrella**, ideal para la rapidez de consulta y agregación.

**Estructura de la BBDD (Glosario Simplificado):**

  * **Tabla de Hechos:** `fact_mediciones` - Contiene valores numéricos y métricas transaccionales (Calorías, BMI, Duración, Frecuencias).
  * **Dimensión `dim_person`:** Almacena atributos estables del usuario (`Age`, `Gender`, `Experience_Level`).
  * **Dimensión `dim_meal`:** Almacena atributos nutricionales (`diet_type`, `meal_type`).
  * **Dimensión `dim_exercise` / `dim_workout_type`:** Almacena la tipología del ejercicio (`Difficulty Level`, `Target Muscle Group`).

### 📐 Diagrama Entidad-Relación (DER) y Código

El DER del modelo final para Power BI se centra en la relación $1:N$ entre las dimensiones y la tabla de hechos.

#### Código en dbdiagram.io (Esquema SQL Simplificado)

```sql
// Esquema Star para Health & Wellness Analytics
Table dim_person {
  person_key INT [pk]
  Age INT
  Gender VARCHAR
  Experience_Level VARCHAR
}

Table dim_meal {
  meal_key INT [pk]
  diet_type VARCHAR
  meal_type VARCHAR
}

Table dim_date {
  date_key INT [pk]
  Year INT
  Month INT
}

Table fact_mediciones {
  fact_mediciones_id INT [pk] 
  person_key INT [fk]
  meal_key INT [fk]
  date_key INT [fk]

  Calories_Burned DECIMAL
  Session_Duration_hours DECIMAL
  BMI DECIMAL
  Fat_Percentage DECIMAL
  Workout_Frequency_days_week DECIMAL
}

Ref: fact_mediciones.person_key > dim_person.person_key
Ref: fact_mediciones.meal_key > dim_meal.meal_key
Ref: fact_mediciones.date_key > dim_date.date_key
```

-----

## 🔗 Pipeline de Datos y Proceso ETL

El flujo de datos se implementa en dos etapas principales de transformación: una inicial (simulada como Capa Bronze/Silver) y una analítica (Power BI).

### Proceso ETL (Transformación)

| Capa | Herramienta | Proceso Clave | Ejemplo de Transformación |
| :--- | :--- | :--- | :--- |
| **Silver (Power Query - M)** | Power Query | **Normalización y Claves Subrogadas.** | Creación de la columna **`fact_mediciones_id`** (Índice) y las claves primarias (`person_key`, `meal_key`, etc.) para todas las dimensiones. |
| **Analítica (Power BI - DAX)** | DAX | **Segmentación Lógica.** | Creación de columnas de agrupamiento (`Segmento_Etario`, `Clasificacion_BMI`) y de las medidas KPI para las visualizaciones. |

### DAX Principales (Medidas y Columnas)

Las siguientes son las fórmulas DAX esenciales para el modelo, centralizadas en una tabla de medidas para un mejor orden (requisito del TP):

#### Medidas (KPIs)

```dax
Calorias_Quemadas_Promedio_Sesion = 
    AVERAGE ( fact_mediciones[Calories_Burned] )

Eficiencia_Calorica_Por_Hora = 
    DIVIDE (
        SUM ( fact_mediciones[Calories_Burned] ),
        SUM ( fact_mediciones[Session_Duration_hours] ),
        0
    )
```

#### Columnas Calculadas (Segmentación)

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
```

```dax
Clasificacion_BMI = 
SWITCH (
    TRUE(),
    'fact_mediciones'[BMI] < 18.5, "1. Bajo Peso",
    'fact_mediciones'[BMI] <= 24.9, "2. Peso Normal",
    'fact_mediciones'[BMI] <= 29.9, "3. Sobrepeso",
    "4. Obesidad"
)
```

-----

## 🔗 Link de Acceso

  * **Link al Informe Publicado en Power BI:** \[INSERTAR LINK DE POWER BI PUBLICADO AQUÍ]
  * **Link al Plan de Métricas (Excel/Hoja de Cálculo):** \[INSERTAR LINK AL ARCHIVO DE PLAN DE MÉTRICAS AQUÍ]
