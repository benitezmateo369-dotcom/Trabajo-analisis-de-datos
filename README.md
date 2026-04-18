# 📊 Análisis Exploratorio — Medical Cost Personal Dataset

**Trabajo Final Integrador | Herramientas básicas para el Análisis de Datos**

---

## Descripción general

Este notebook realiza un análisis exploratorio de datos (EDA) sobre el dataset `insurance.csv`, que contiene información sobre costos de seguros médicos personales en Estados Unidos. El objetivo central es identificar qué factores —edad, IMC, tabaquismo y región— tienen mayor influencia en el costo del seguro.

**Pregunta principal:** ¿Qué factores influyen más en el costo del seguro médico?

**KPIs definidos:**
- **KPI 1:** ¿Cuánto más pagan los fumadores respecto a los no fumadores?
- **KPI 2:** ¿Qué región concentra los costos más altos?
- **KPI 3:** ¿Cómo impacta el número de hijos en el costo del seguro?

---

## Estructura del análisis

### 1. Importación de librerías

Se importan las librerías necesarias para el análisis:

| Librería | Propósito |
|---|---|
| `pandas` | Manipulación y análisis de datos tabulares |
| `numpy` | Operaciones numéricas y matriciales |
| `matplotlib` | Generación de gráficos base |
| `seaborn` | Visualizaciones estadísticas de alto nivel |
| `mapclassify` | Clasificación espacial/estadística por cuantiles |

Con el propósito de asegurar la homogeneidad visual a lo largo del documento, se establece una configuración estética global mediante los parámetros de `seaborn` y `matplotlib` (`whitegrid`, paleta `muted`, dimensiones 10×5). Esta práctica responde a un estándar de presentación profesional que facilita la lectura comparativa entre gráficos y otorga coherencia al reporte final.

---

### 2. Ingesta del dataset

El archivo `insurance.csv` se incorpora al entorno de análisis mediante la función `pd.read_csv()`. A continuación, se lleva a cabo una inspección inicial estructurada a través de los métodos `df.shape`, `df.columns`, `df.head()`, `df.tail()` y `df.sample()`.

**Metodología y justificación:** La exploración preliminar del dataset constituye una etapa no negociable en cualquier proceso de análisis de datos. La consulta simultánea de registros iniciales, finales y aleatorios permite obtener una visión representativa del conjunto sin incurrir en sesgos de muestreo. Este relevamiento inicial es el fundamento sobre el cual se sostiene la validez de las etapas subsiguientes.

---

### 3. Auditoría de calidad de datos

Se ejecuta un proceso de auditoría integral sobre el dataset, estructurado en cuatro instancias de verificación:

**Tipos de datos y valores nulos**
Mediante `df.info()` se identifican los tipos de datos asignados a cada variable y la presencia de valores faltantes. Se complementa con el cálculo del conteo absoluto y el porcentaje relativo de nulos por columna, conforme a buenas prácticas de gobierno del dato.

**Estadísticas descriptivas**
La función `df.describe(include='all')` genera un perfil estadístico completo —media, desvío estándar, mínimo, máximo y percentiles— sobre la totalidad de las variables, tanto numéricas como categóricas. Su propósito es detectar distribuciones anómalas o valores fuera de rango que requieran atención antes de avanzar en el análisis.

**Detección y tratamiento de duplicados**
Se identifican registros duplicados mediante `df.duplicated()` y se procede a su eliminación. La presencia de duplicados compromete la representatividad estadística del análisis al inflar artificialmente la frecuencia de determinadas observaciones, lo cual afecta directamente la fiabilidad de los resultados agregados.

**Detección de valores atípicos mediante el método IQR**
Se implementa una función parametrizada que aplica el criterio del Rango Intercuartílico (IQR) sobre las variables continuas `age`, `bmi` y `charges`. Bajo este criterio estadístico estándar, se considera atípico todo valor que se ubique por debajo de `Q1 − 1.5×IQR` o por encima de `Q3 + 1.5×IQR`. Los resultados se reportan por columna a efectos de transparencia y trazabilidad.

**Metodología y justificación:** La integridad del dato es condición necesaria para la validez de cualquier conclusión analítica. La omisión de esta etapa de auditoría expone al análisis a errores sistemáticos de difícil detección posterior, con potencial impacto sobre la toma de decisiones basada en los resultados.

---

### 4. Limpieza y estandarización

**Normalización de nomenclatura**
Los encabezados de columnas son transformados a formato estándar: minúsculas, sin espacios (sustituidos por guión bajo) y sin caracteres especiales, mediante normalización Unicode NFKD. Esta homogeneización elimina fuentes de error en el acceso programático a las variables y garantiza la interoperabilidad del dataset con otras herramientas del ecosistema de datos.

**Estandarización de variables categóricas**
Las columnas `sex`, `smoker` y `region` son normalizadas mediante `.str.strip().str.lower()`, asegurando la eliminación de espacios residuales y la unificación de criterios de capitalización. Sin esta estandarización, variantes tipográficas de un mismo valor serían tratadas como categorías independientes, distorsionando los agrupamientos y los conteos.

**Construcción de variables derivadas**
Se incorporan dos atributos calculados que amplían la capacidad analítica del dataset:

- **`bmi_categoria`:** Clasifica el Índice de Masa Corporal según la escala de la Organización Mundial de la Salud en cuatro segmentos: *Bajo peso* (< 18.5), *Normal* (18.5–25), *Sobrepeso* (25–30) y *Obesidad* (≥ 30). La transformación de una variable continua en una categórica ordinal habilita análisis de segmentación más intuitivos y comparables con estándares clínicos reconocidos internacionalmente.
- **`grupo_edad`:** Segmenta la variable `age` en cuatro cohortes etarias (18–30, 31–45, 46–60, 61+) mediante `pd.cut()`. Esta discretización facilita el análisis de comportamiento por grupos poblacionales y la producción de visualizaciones comparativas por rango.

**Metodología y justificación:** La estandarización de los datos es una práctica fundamental en la gestión de la calidad del dato. Las variables derivadas no reemplazan a las originales sino que complementan el análisis, habilitando perspectivas de segmentación alineadas con marcos de referencia reconocidos —como los criterios OMS— que dotan al análisis de mayor rigor y credibilidad institucional.

---

### 5. KPIs principales

Se cuantifican los tres indicadores de desempeño definidos en el alcance del análisis:

**KPI 1 — Impacto diferencial del tabaquismo sobre el costo**
Se segmenta la población en dos cohortes —fumadores y no fumadores— mediante filtros booleanos sobre la variable `smoker`. Para cada grupo se calcula el costo promedio del seguro y se expresa la brecha como diferencia porcentual. Este indicador constituye la respuesta cuantitativa central a la hipótesis de trabajo.

**KPI 2 — Distribución geográfica del costo**
Se agrupan los registros por `region` y se calcula el costo promedio mediante `groupby().mean()`, ordenado de forma descendente. El resultado permite identificar la región de mayor exposición económica y establecer comparaciones entre jurisdicciones geográficas.

**KPI 3 — Correlación entre carga familiar y costo del seguro**
Se agrupa por la variable `children` y se calcula el costo promedio por grupo, con el objetivo de evaluar si el número de dependientes constituye un factor de incremento en la prima del seguro.

**Metodología y justificación:** La definición de KPIs previamente al análisis exploratorio es una práctica de gestión analítica que orienta el trabajo hacia resultados medibles y accionables. Los indicadores seleccionados responden a preguntas de negocio concretas y son expresados en unidades monetarias y porcentajes, lo cual facilita su comunicación a audiencias ejecutivas y no técnicas.

---

### 6. Exploración y visualizaciones

Se desarrollan cinco representaciones gráficas que cubren las principales dimensiones analíticas del dataset.

#### Figura 1 — Distribución de costos del seguro
- **Descripción:** Histograma con estimación de densidad kernel (KDE) y boxplot de la variable `charges`.
- **Metodología:** Se emplea `sns.histplot()` con el parámetro `kde=True` para superponer la curva de densidad estimada sobre la distribución de frecuencias. Se incorporan líneas de referencia verticales para la media y la mediana. El boxplot complementario permite visualizar la dispersión y la presencia de valores atípicos.
- **Justificación:** El análisis de la distribución de la variable objetivo es el punto de partida obligatorio de cualquier EDA. La visualización conjunta del histograma y el boxplot permite caracterizar con precisión la asimetría positiva de la distribución, evidenciando la existencia de una cola derecha de costos elevados que condiciona la interpretación de medidas de tendencia central.

#### Figura 2 — Impacto del tabaquismo en los costos
- **Descripción:** Boxplot comparativo entre fumadores y no fumadores, acompañado de un gráfico de barras con los promedios anotados.
- **Metodología:** Se utiliza `sns.boxplot()` con una paleta de colores diferenciada por grupo para facilitar la distinción visual. Las anotaciones de valor se incorporan directamente sobre cada barra mediante `axes.text()`, garantizando la lectura inmediata de los resultados sin necesidad de consultar ejes secundarios.
- **Justificación:** Esta visualización materializa el KPI 1 en un formato de alto impacto comunicacional. La combinación de boxplot y barras permite evaluar simultáneamente la magnitud de la brecha promedio y la variabilidad intragrupal, proporcionando una imagen completa del fenómeno más allá del dato puntual.

#### Figura 3 — Relación entre edad, IMC y costo (segmentado por tabaquismo)
- **Descripción:** Dos diagramas de dispersión: Edad vs. Costo e IMC vs. Costo, segmentados por condición de tabaquismo mediante codificación cromática.
- **Metodología:** Se itera sobre los dos grupos mediante `axes.scatter()`, asignando colores diferenciados y un nivel de transparencia (`alpha=0.5`) que permite visualizar la densidad de puntos en zonas de alta concentración. Se incorpora una línea de referencia vertical en IMC=30 para señalizar el umbral clínico de obesidad según criterios OMS.
- **Justificación:** El análisis bivariado segmentado por tabaquismo permite identificar patrones de interacción entre variables que no serían observables en un análisis univariado. La separación visual entre cohortes evidencia el efecto combinado del tabaquismo con otras variables sobre el costo, aportando profundidad analítica al reporte.

#### Figura 4 — Costos por región y sexo
- **Descripción:** Boxplot de costos por región ordenado por mediana descendente, complementado con un gráfico de barras agrupadas por región y sexo.
- **Metodología:** El ordenamiento del boxplot se determina dinámicamente mediante `groupby().median().sort_values()`, asegurando que la región de mayor costo ocupe la posición más prominente. Las barras agrupadas se construyen a partir de una tabla dinámica generada con `groupby().unstack()`, que cruza la dimensión geográfica con la variable de sexo.
- **Justificación:** Esta visualización da respuesta al KPI 2 en un formato analíticamente completo, incorporando además la dimensión de género para evaluar si existen disparidades sistemáticas en el costo entre hombres y mujeres a nivel regional. La presentación ordenada por mediana facilita la priorización de regiones en función de su nivel de exposición.

#### Figura 5 — Mapa de calor de correlaciones
- **Descripción:** Mapa de calor de la matriz de correlación de Pearson, presentado en formato triangular inferior para eliminar redundancias.
- **Metodología:** Las variables categóricas `smoker` y `sex` son codificadas numéricamente (0/1) para habilitarlas en el cálculo de correlaciones lineales. Se aplica una máscara triangular superior mediante `np.triu()` a fin de evitar la duplicación de información. La representación se realiza con `sns.heatmap()` utilizando la paleta divergente `coolwarm` y anotaciones numéricas con dos decimales.
- **Justificación:** El mapa de correlaciones constituye una herramienta de síntesis analítica que permite evaluar simultáneamente las relaciones lineales entre todas las variables del modelo. Su inclusión en el reporte aporta evidencia cuantitativa sobre la jerarquía de factores explicativos del costo, y sirve como insumo orientador para etapas de modelado predictivo futuras.

---

### 7. Clasificación por cuantiles con `mapclassify`

- **Descripción:** Segmentación de los costos del seguro en cuatro cuartiles con etiquetas descriptivas (Bajo, Medio-Bajo, Medio-Alto, Alto) y análisis de la composición por condición de tabaquismo en cada segmento.
- **Metodología:** Se emplea `mc.Quantiles(df['charges'], k=4)` de la librería `mapclassify`, que construye grupos de igual frecuencia relativa sobre la distribución de la variable objetivo. Los segmentos resultantes son etiquetados y cruzados con la variable `smoker` mediante `groupby().value_counts(normalize=True)`, expresando los resultados en términos porcentuales.
- **Justificación:** La utilización de `mapclassify` —librería de referencia en clasificación estadística para análisis geoespacial y de datos— garantiza la reproducibilidad y el rigor metodológico de la segmentación. La clasificación por cuartiles permite distribuir los registros en grupos equipoblados, eliminando el sesgo que introducirían los umbrales arbitrarios. El cruce con la variable de tabaquismo valida empíricamente si los fumadores se concentran de manera desproporcionada en los segmentos de mayor costo, aportando evidencia adicional a los hallazgos del KPI 1.

---

### 8. Resumen ejecutivo

Se genera un reporte estructurado de cierre que consolida las métricas globales del dataset y los resultados cuantitativos de los tres KPIs definidos al inicio del análisis.

**Metodología y justificación:** La elaboración de un resumen ejecutivo al término del análisis responde a un estándar de comunicación profesional orientado a audiencias de perfil directivo o no técnico. Su función es condensar los hallazgos más relevantes en un formato de lectura rápida, garantizando que las conclusiones del análisis sean accesibles y accionables más allá del entorno técnico en el que fueron producidas.

---

## Principales hallazgos

- **El tabaquismo es el factor individual más determinante** del costo del seguro médico, con una correlación de r ≈ 0.79. Los fumadores pagan significativamente más en promedio.
- **La combinación tabaquismo + obesidad (IMC > 30)** genera los costos más extremos del dataset.
- **La región Southeast** concentra la mayor variabilidad y los costos promedio más altos entre las cuatro regiones.
- **La edad** muestra una correlación positiva moderada (r ≈ 0.30) con el costo, especialmente en la población fumadora.
- **La distribución de costos es asimétrica a la derecha**, con la mayoría de asegurados pagando menos de $20.000, pero una cola de valores extremadamente altos.

---

## Archivos generados

| Archivo | Contenido |
|---|---|
| `fig1_distribucion_costos.png` | Histograma y boxplot de la variable objetivo |
| `fig2_tabaquismo.png` | Comparación de costos fumadores vs. no fumadores |
| `fig3_edad_bmi_costos.png` | Scatterplots de edad e IMC vs. costo |
| `fig4_region_sexo.png` | Análisis geográfico y demográfico |
| `fig5_correlaciones.png` | Mapa de calor de correlaciones |

---

## Requisitos

```
pandas
numpy
matplotlib
seaborn
mapclassify
```

Instalación:

```bash
pip install pandas numpy matplotlib seaborn mapclassify
```
