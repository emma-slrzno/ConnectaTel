<a id="top"></a>

# 📱 Análisis ConnectaTel · ConnectaTel Analysis

**Perfil de clientes, patrones de uso y segmentación · Customer profile, usage patterns, and segmentation**

**🌎 Idioma / Language:** [🇪🇸 Español](#es) · [🇬🇧 English](#en)

---

<a id="es"></a>

## 🇪🇸 Español

Análisis exploratorio de los clientes de **ConnectaTel**, una empresa de telecomunicaciones en Latinoamérica, con datos registrados hasta 2024: calidad de datos, perfil estadístico de uso, detección de comportamientos atípicos y segmentación de clientes.

### 1. Problema o contexto de negocio

ConnectaTel ofrece dos planes móviles (Básico y Premium) en ciudades de México y Colombia. Para **diseñar estrategias de retención y mejorar su oferta de planes**, necesita entender cómo usan realmente el servicio sus clientes, quiénes son y qué comportamientos se salen de lo normal. Antes de eso, hay que asegurar que los datos sean confiables: los archivos de origen traían valores centinela, fechas imposibles y valores faltantes.

### 2. Objetivo del análisis

1. **Explorar, validar y limpiar** los tres datasets de la empresa.
2. Construir un **perfil estadístico de uso por cliente** (mensajes, llamadas y minutos).
3. Detectar **comportamientos atípicos (outliers)**.
4. Crear **segmentos de clientes** por nivel de uso y por edad.
5. Traducir los hallazgos en **recomendaciones para planes y retención**.

### 3. Dataset utilizado

| Archivo | Filas × columnas | Contenido |
|---------|-----------------:|-----------|
| `plans.csv` | 2 × 8 | Planes vigentes: precio mensual, mensajes, GB y minutos incluidos, y costo por unidad extra |
| `users_latam.csv` | 4,000 × 8 | Clientes: edad, ciudad, fecha de registro, plan y fecha de baja (`churn_date`) |
| `usage.csv` | 40,000 × 6 | Uso real en 2024: tipo de evento (llamada o mensaje), fecha, duración de llamadas y longitud de mensajes |

**Planes disponibles**

| | Básico | Premium |
|---|-------:|--------:|
| Precio mensual | $12 USD | $25 USD |
| Mensajes incluidos | 100 | 500 |
| GB al mes | 5 | 20 |
| Minutos incluidos | 100 | 600 |
| Extra por GB / mensaje / minuto | $1.20 / $0.08 / $0.10 | $1.00 / $0.05 / $0.07 |

### 4. Herramientas y tecnologías

- **Python:** `pandas`, `numpy`
- **Visualización:** `seaborn`, `matplotlib`
- **Jupyter Notebook**

### 5. Proceso realizado

1. **Carga y exploración:** revisión de estructura, tipos de datos y nulos de los tres datasets.
2. **Detección de problemas de calidad:**
   - Valores centinela: `age = -999` y `city = "?"`.
   - Fechas fuera de rango: registros con año 2026 cuando los datos llegan hasta 2024.
   - Nulos en `city`, `churn_date`, y en `duration` y `length` de `usage`.
3. **Limpieza básica:** reemplazo de la edad inválida por la mediana (48), de `"?"` por nulo, y marcado de fechas futuras como nulas. Los nulos de `duration` y `length` se dejaron como están porque dependen del tipo de evento.
4. **Perfil de uso por cliente:** agregación de `usage` por usuario (mensajes, llamadas y minutos) y unión con `users` (3,999 clientes con actividad).
5. **Distribuciones y outliers:** histogramas por plan y boxplots; cálculo del límite superior con el método IQR.
6. **Segmentación:**
   - **Por uso (`grupo_uso`):** *Bajo uso* (llamadas < 5 y mensajes < 5), *Uso medio* (llamadas < 10 y mensajes < 10) y *Alto uso* (el resto).
   - **Por edad (`grupo_edad`):** *Joven* (< 30), *Adulto* (30–59) y *Adulto Mayor* (≥ 60).
7. **Insight ejecutivo** con problemas de datos, segmentos y recomendaciones.

### 6. Principales hallazgos

#### 🧹 Calidad de datos

| Problema | Magnitud |
|----------|----------|
| `age = -999` (centinela) | ~55 clientes (~1.4%) |
| `city` faltante o con `"?"` | 565 clientes (14.1%): 469 nulos + 96 con `"?"` |
| `churn_date` vacío | 3,534 clientes (88.4%): corresponde a clientes **activos**, no a datos perdidos |
| Fechas de registro de 2026 | Año imposible según el alcance de los datos (hasta 2024) |
| `date` nulo en `usage` | 50 de 40,000 eventos (0.1%) |
| `duration` / `length` nulos | Nulos estructurales: la duración solo aplica a llamadas y la longitud solo a mensajes (con pequeñas excepciones: 16 mensajes con duración y 12 llamadas con longitud) |

#### 👥 Clientes y uso en 2024

| Indicador | Valor |
|-----------|------:|
| Clientes | 4,000 (3,999 con actividad registrada) |
| Eventos de uso | 40,000: 22,092 mensajes (55.2%) y 17,908 llamadas (44.8%) |
| Plan Básico / Premium | 64.87% / 35.13% |
| Clientes que se dieron de baja | 466 (**11.65%**) |
| Edad | 18 a 79 años, media de 48 |

| Uso por cliente en 2024 | Media | Mediana | Máximo |
|-------------------------|------:|--------:|-------:|
| Mensajes | 5.52 | 5 | 17 |
| Llamadas | 4.48 | 4 | 15 |
| Minutos de llamada | 23.32 | 19.78 | 155.69 |

- La edad se distribuye de forma **aplanada** (casi uniforme entre 18 y 79); mensajes y llamadas tienen distribuciones **concentradas** en un rango estrecho; los minutos de llamada están **sesgados a la derecha**.
- **Outliers:** edad no tiene; mensajes y llamadas tienen pocos; minutos de llamada tiene muchos (límite superior IQR ≈ 62 min frente a un máximo de 155.7). Se **mantuvieron** por ser usuarios atípicos pero válidos y porque el negocio quiere identificarlos.

#### 🗂️ Segmentos

- **Por uso:** predomina *Uso medio*; *Alto uso* es el segmento más pequeño.
- **Por edad:** predominan los *Adultos*; los *Jóvenes* son el segmento más pequeño. Como la edad es casi uniforme, el tamaño de cada segmento refleja en buena parte el ancho de su rango (12, 30 y 20 años), más que una menor presencia de jóvenes.

#### 🌎 Distribución geográfica (3,435 clientes con ciudad válida)

| Ciudad | Clientes | % Premium |
|--------|---------:|----------:|
| Bogotá | 808 | 35.4% |
| CDMX | 730 | 35.1% |
| Medellín | 616 | 35.4% |
| Guadalajara (GDL) | 450 | 33.8% |
| Cali | 424 | 38.2% |
| Monterrey (MTY) | 407 | 32.4% |

La mezcla de planes es similar entre ciudades (32–38% Premium). Colombia concentra ~54% de los clientes con ciudad válida y México ~46%.

#### 💵 Peso del plan Premium

Con precios de $12 (Básico) y $25 (Premium), el plan Premium representa el 35% de los clientes pero **~53% de la facturación base de planes** (estimación sobre los 3,999 clientes, sin descontar bajas ni cargos extra).

### 7. Recomendaciones e impacto para el negocio

Son hipótesis a validar con datos adicionales:

1. **Contrastar los planes con el uso real.** El uso registrado es muy bajo frente a lo incluido: el cliente con más minutos acumuló 155.7 en todo 2024, mientras que el plan Básico incluye 100 minutos *al mes*. Antes de decidir, hay que confirmar si `usage` es una muestra completa; si lo es, existe espacio para planes más ligeros o de precio menor, y los cobros por excedente aplicarían a muy pocos clientes.
2. **Revisar la propuesta de valor de Premium.** Los histogramas por plan muestran formas de uso similares entre Básico y Premium; conviene comparar medias y medianas por plan para confirmar si Premium realmente se usa más y justifica su precio.
3. **Evaluar un plan orientado a llamadas.** Existe un grupo con minutos muy superiores al resto (por encima de ~62 min al año). Antes de crear un plan, cuantificar cuántos clientes son y si son rentables.
4. **Usar *Alto uso* para pruebas de ascenso a Premium.** Es el segmento más intenso en llamadas o mensajes y el candidato natural para campañas de upsell, medidas con una prueba A/B.
5. **Convertir la baja de clientes en una métrica de retención.** Con 11.65% de bajas, el siguiente paso es analizar quién se va (por plan, edad, ciudad y nivel de uso) para dirigir las acciones de retención.
6. **Prevenir los problemas de datos en el origen.** Validaciones en captura (rangos de edad, catálogo de ciudades y fechas no futuras) evitan los centinelas y las fechas imposibles.

### 8. Limitaciones y puntos a mejorar

- **`reg_date` queda vacía tras la limpieza.** La instrucción que marca como nulas las fechas de 2026 está escrita de forma que sobrescribe toda la columna con `NaT`. Debe corregirse (`users.loc[users['reg_date'].dt.year > 2024, 'reg_date'] = pd.NaT`) antes de analizar antigüedad de los clientes.
- **`churn_date` no es una columna a eliminar.** Sus nulos significan "cliente activo"; descartarla impediría analizar la retención, que es el objetivo del proyecto.
- **El límite IQR se calculó solo para minutos de llamada.** El bucle sobrescribe Q1, Q3 e IQR en cada vuelta, por lo que mensajes y llamadas usaron el límite de los minutos. Con sus propios cuartiles, los límites serían ≈ 11.5 mensajes y ≈ 10.5 llamadas.
- **No se calcularon estadísticas por plan ni por segmento.** Las comparaciones entre Básico y Premium se hicieron solo de forma visual.
- **Sin análisis de baja ni de antigüedad.** No se evaluó qué segmentos tienen más churn.
- **Los tamaños de los segmentos de uso y edad no se reportan numéricamente** en el notebook (solo gráficos).
- **Volumen de uso bajo:** unos 10 eventos por cliente en todo el año sugiere que los datos son una muestra o un resumen.

### 9. Cómo reproducir el análisis

```bash
pip install pandas numpy seaborn matplotlib jupyter
jupyter notebook S7_ConnectaTel.ipynb
```

El notebook carga los archivos desde `/datasets/` (`plans.csv`, `users_latam.csv`, `usage.csv`); si lo ejecutas en local, ajusta las rutas.

### 10. Estructura del proyecto

```
├── S7_ConnectaTel.ipynb   # Análisis completo
├── plans.csv
├── users_latam.csv
├── usage.csv
└── README.md              # Bilingüe (ES/EN)
```

[⬆️ Volver arriba](#top) · [🇬🇧 Read in English](#en)

---

<a id="en"></a>

## 🇬🇧 English

Exploratory analysis of the customers of **ConnectaTel**, a telecommunications company in Latin America, using data recorded up to 2024: data quality, statistical usage profile, detection of atypical behavior, and customer segmentation.

### 1. Business problem and context

ConnectaTel offers two mobile plans (Basic and Premium) in cities across Mexico and Colombia. To **design retention strategies and improve its plan offering**, it needs to understand how customers actually use the service, who they are, and which behaviors fall outside the norm. Before that, the data must be made reliable: the source files contained sentinel values, impossible dates, and missing values.

### 2. Analysis objective

1. **Explore, validate, and clean** the company's three datasets.
2. Build a **statistical usage profile per customer** (messages, calls, and minutes).
3. Detect **atypical behavior (outliers)**.
4. Create **customer segments** by usage level and by age.
5. Turn the findings into **recommendations for plans and retention**.

### 3. Dataset

| File | Rows × columns | Content |
|------|---------------:|---------|
| `plans.csv` | 2 × 8 | Current plans: monthly price, included messages, GB, and minutes, and cost per extra unit |
| `users_latam.csv` | 4,000 × 8 | Customers: age, city, registration date, plan, and churn date (`churn_date`) |
| `usage.csv` | 40,000 × 6 | Actual usage in 2024: event type (call or message), date, call duration, and message length |

**Available plans**

| | Basic | Premium |
|---|------:|--------:|
| Monthly price | $12 USD | $25 USD |
| Included messages | 100 | 500 |
| GB per month | 5 | 20 |
| Included minutes | 100 | 600 |
| Extra per GB / message / minute | $1.20 / $0.08 / $0.10 | $1.00 / $0.05 / $0.07 |

### 4. Tools and technologies

- **Python:** `pandas`, `numpy`
- **Visualization:** `seaborn`, `matplotlib`
- **Jupyter Notebook**

### 5. Process

1. **Loading and exploration:** review of structure, data types, and missing values in the three datasets.
2. **Data quality issue detection:**
   - Sentinel values: `age = -999` and `city = "?"`.
   - Out-of-range dates: registrations in 2026 when the data runs only through 2024.
   - Missing values in `city`, `churn_date`, and in `duration` and `length` of `usage`.
3. **Basic cleaning:** invalid age replaced with the median (48), `"?"` replaced with null, and future dates marked as null. Missing `duration` and `length` values were left as they are because they depend on the event type.
4. **Usage profile per customer:** `usage` aggregated per user (messages, calls, and minutes) and joined with `users` (3,999 customers with activity).
5. **Distributions and outliers:** histograms by plan and boxplots; upper limit calculated with the IQR method.
6. **Segmentation:**
   - **By usage (`grupo_uso`):** *Low usage* (calls < 5 and messages < 5), *Medium usage* (calls < 10 and messages < 10), and *High usage* (the rest).
   - **By age (`grupo_edad`):** *Young* (< 30), *Adult* (30–59), and *Older adult* (≥ 60).
7. **Executive insight** covering data issues, segments, and recommendations.

### 6. Key findings

#### 🧹 Data quality

| Issue | Magnitude |
|-------|-----------|
| `age = -999` (sentinel) | ~55 customers (~1.4%) |
| `city` missing or `"?"` | 565 customers (14.1%): 469 nulls + 96 with `"?"` |
| `churn_date` empty | 3,534 customers (88.4%): these are **active** customers, not lost data |
| Registration dates in 2026 | Impossible year given the data scope (through 2024) |
| Null `date` in `usage` | 50 of 40,000 events (0.1%) |
| Null `duration` / `length` | Structural nulls: duration only applies to calls and length only to messages (with small exceptions: 16 messages with a duration and 12 calls with a length) |

#### 👥 Customers and usage in 2024

| Metric | Value |
|--------|------:|
| Customers | 4,000 (3,999 with recorded activity) |
| Usage events | 40,000: 22,092 messages (55.2%) and 17,908 calls (44.8%) |
| Basic / Premium plan | 64.87% / 35.13% |
| Customers who churned | 466 (**11.65%**) |
| Age | 18 to 79 years, mean of 48 |

| Usage per customer in 2024 | Mean | Median | Max |
|----------------------------|-----:|-------:|----:|
| Messages | 5.52 | 5 | 17 |
| Calls | 4.48 | 4 | 15 |
| Call minutes | 23.32 | 19.78 | 155.69 |

- Age has a **flat** distribution (almost uniform between 18 and 79); messages and calls are **concentrated** in a narrow range; call minutes are **right-skewed**.
- **Outliers:** age has none; messages and calls have few; call minutes have many (IQR upper limit ≈ 62 min versus a maximum of 155.7). They were **kept** because they are atypical but valid users and because the business wants to identify them.

#### 🗂️ Segments

- **By usage:** *Medium usage* predominates; *High usage* is the smallest segment.
- **By age:** *Adults* predominate; *Young* customers are the smallest segment. Since age is almost uniform, segment size largely reflects the width of each range (12, 30, and 20 years) rather than a lower presence of young customers.

#### 🌎 Geographic distribution (3,435 customers with a valid city)

| City | Customers | % Premium |
|------|----------:|----------:|
| Bogotá | 808 | 35.4% |
| CDMX | 730 | 35.1% |
| Medellín | 616 | 35.4% |
| Guadalajara (GDL) | 450 | 33.8% |
| Cali | 424 | 38.2% |
| Monterrey (MTY) | 407 | 32.4% |

The plan mix is similar across cities (32–38% Premium). Colombia accounts for ~54% of customers with a valid city and Mexico ~46%.

#### 💵 Weight of the Premium plan

With prices of $12 (Basic) and $25 (Premium), the Premium plan represents 35% of customers but **~53% of base plan billing** (estimate across the 3,999 customers, before subtracting churn and extra charges).

### 7. Recommendations and business impact

These are hypotheses to validate with additional data:

1. **Compare the plans with actual usage.** Recorded usage is very low relative to what is included: the customer with the most minutes accumulated 155.7 in all of 2024, while the Basic plan includes 100 minutes *per month*. Before deciding, confirm whether `usage` is a complete sample; if it is, there is room for lighter or lower-priced plans, and overage charges would apply to very few customers.
2. **Review Premium's value proposition.** The per-plan histograms show similar usage shapes for Basic and Premium; compare means and medians by plan to confirm whether Premium is actually used more and justifies its price.
3. **Evaluate a call-oriented plan.** A group has minutes far above the rest (above ~62 min per year). Before creating a plan, quantify how many customers it is and whether they are profitable.
4. **Use *High usage* for Premium upgrade tests.** It is the most intense segment in calls or messages and the natural candidate for upsell campaigns, measured with an A/B test.
5. **Turn churn into a retention metric.** With 11.65% churn, the next step is to analyze who leaves (by plan, age, city, and usage level) to target retention actions.
6. **Prevent data issues at the source.** Validation at data entry (age ranges, city catalog, and non-future dates) avoids sentinels and impossible dates.

### 8. Limitations and points to improve

- **`reg_date` ends up empty after cleaning.** The statement that marks 2026 dates as null is written in a way that overwrites the entire column with `NaT`. It must be fixed (`users.loc[users['reg_date'].dt.year > 2024, 'reg_date'] = pd.NaT`) before analyzing customer tenure.
- **`churn_date` is not a column to drop.** Its nulls mean "active customer"; discarding it would prevent retention analysis, which is the project's goal.
- **The IQR limit was only calculated for call minutes.** The loop overwrites Q1, Q3, and IQR on every iteration, so messages and calls used the minutes limit. With their own quartiles, the limits would be ≈ 11.5 messages and ≈ 10.5 calls.
- **No statistics by plan or segment were computed.** Comparisons between Basic and Premium were made only visually.
- **No churn or tenure analysis.** It was not assessed which segments have more churn.
- **Segment sizes for usage and age are not reported numerically** in the notebook (charts only).
- **Low usage volume:** about 10 events per customer over the whole year suggests the data is a sample or a summary.

### 9. How to reproduce

```bash
pip install pandas numpy seaborn matplotlib jupyter
jupyter notebook S7_ConnectaTel.ipynb
```

The notebook loads the files from `/datasets/` (`plans.csv`, `users_latam.csv`, `usage.csv`); if you run it locally, update the paths.

### 10. Project structure

```
├── S7_ConnectaTel.ipynb   # Full analysis
├── plans.csv
├── users_latam.csv
├── usage.csv
└── README.md              # Bilingual (ES/EN)
```

[⬆️ Back to top](#top) · [🇪🇸 Leer en español](#es)
