Markdown
# ⚽ Smart-Ops Football Market Analytics: Ecosistema Analítico de Fichajes y Finanzas Deportivas

Este repositorio contiene un ecosistema analítico de Business Intelligence (BI) de extremo a extremo, diseñado para auditar el mercado de transferencias de las **5 grandes ligas europeas (2020-2025)**, evaluar la inflación de precios, analizar la eficiencia financiera de clubes y ligas, y optimizar la toma de decisiones en la compra/venta de jugadores.

El proyecto se ha desarrollado bajo una arquitectura robusta, utilizando **PostgreSQL** para la ingesta, exploración y calidad de datos inicial (SQL QA), y **Power BI** (conectado a la fuente CSV exportada) como plataforma de modelado, analítica avanzada (DAX) y visualización interactiva de estándar corporativo (Big 4).

---

## 📌 Contexto, Flujo General y Objetivos Analíticos

### Flujo General de Arquitectura
```text
[Dataset Único (Raw Data 2020-2025)]
  └─► [PostgreSQL Database (Auditoría SQL, DDL, Constraints & QA)]
        └─► [Archivo CSV Exportado (Fuente para Power BI)]
              └─► [Power Query ETL (Tratamiento & Deduplicación de Transacciones In/Out)]
                    └─► [Power BI Data Model (Estructura de 3 Tablas: Raw, Fact_Transfers, _Medidas)]
                          └─► [Interactivity Architecture & Custom Tooltips UX]
Objetivos Analíticos Principales
Evaluar la inflación en los precios del mercado y el Net Spend por liga y club dentro del periodo 2020-2025.

Analizar la eficiencia financiera en la compra/venta de jugadores según su Market Value vs. Transfer Fee.

Monitorizar la evolución de cedidos (Loans) frente a traspasos permanentes (Permanent Transfers).

Facilitar una exploración interactiva detallada a nivel de jugador, club, liga y nacionalidad.
```

# 🔍 1. Auditoría de Calidad y ETL (PostgreSQL & Power Query)
## A. Capa de Base de Datos y Auditoría (PostgreSQL)
**Ingesta de Tabla Original (Raw):** Carga de la tabla maestra con el histórico de fichajes (2020-2025) de las 5 grandes ligas (Premier League, LaLiga, Serie A, Bundesliga, Ligue 1) en la base de datos PostgreSQL.

**Auditoría de Datos (SQL QA):** Uso exclusivo del entorno SQL para auditorías de calidad de datos, validación de montos negativos, verificación de tipos de movimiento (In/Out) e integridad de identificadores previo a la generación del archivo .csv final.

## B. Transformación y Deduplicación en Power Query
**Origen de Datos en Power BI:** Conexión directa del informe hacia el archivo CSV resultante de la auditoría.

**Tratamiento de Duplicados Inter-Liga (Paso Crítico ETL):** Dado que la fuente original registra las transacciones desde la perspectiva de ambos clubes, un traspaso entre dos equipos de las 5 grandes ligas aparecía duplicado (registrado como In para el club comprador y Out para el vendedor). Se creó en Power Query una nueva tabla deduplicada para aislar cada transacción única y evitar la doble contabilización de montos y volumen.

**Limpieza y Modelado:** Estandarización de tipos de datos, reordenamiento de columnas y tratamiento de nulos.

# 🏗️ 2. Estructura del Modelo de Datos (3 Tablas)
A diferencia de esquemas de datos extensos con múltiples dimensiones separadas, este modelo simplifica la arquitectura en 3 tablas principales para maximizar la agilidad y el rendimiento de consulta:

**Tabla Original (Raw):** Tabla fuente extraída del CSV sin transformar, con todos los registros históricos tal como provienen de la ingesta inicial.

**Fact_Transfers (Tabla Deduplicada):** Tabla principal de hechos limpia y deduplicada en Power Query, que consolida las transacciones únicas con sus montos, valor de mercado, tipo de transferencia (Loan / Permanent), edad del jugador, posición, nacionalidad, club comprador y club vendedor.

**_Medidas:** Tabla aislada creada exclusivamente para alojar y organizar el repositorio completo de la lógica de negocio en DAX.

# 📐 3. Capa de Modelado y Catálogo Completo de Medidas DAX
El modelo cuenta con una arquitectura de cálculo centralizada dentro de la tabla _Medidas:

## 📂 Algunas de las Medidas DAX (_Medidas):

**Number of players:** Conteo único de jugadores analizados.

**Number of transfers:** Conteo total de transacciones unificadas/deduplicadas registradas.

**Number of buying clubs:** Número de clubes que han comprado jugadores.

**Number of selling clubs:** Número de clubes que han vendido jugadores.

**Number of countries:** Países representados en las ligas o nacionalidades.

**Incoming transfers:** Total de transferencias de entrada hacia una liga/club.

**Outgoing transfers:** Total de transferencias de salida desde una liga/club.

**Total transfer spending:** Suma global del gasto de compra.

**Total transfer income:** Suma global de ingresos por ventas.

**Net Spend:** Balance financiero neto.

**Total market value:** Suma del valor estimado de mercado de los jugadores transferidos.

**Average transfer fee:** Promedio invertido por fichaje.

**Average market value / Average Market Value 2:** Valor de mercado promedio de los jugadores transferidos.

**Average Club Spending:** Gasto promedio por club.

**Average Club Transfers:** Número promedio de fichajes por club.

**Most expensive transfer:** Fichaje con la tarifa máxima registrada.

**Most Expensive Player:** Nombre o tarifa del jugador con la cotización más alta.

**Max Market Value per Player / Max Market Value per Position:** Valores máximos de mercado por entidad/grupo.

**Market share %:** Porcentaje de cuota de mercado en gasto respecto al total.

**Loan Percentage:** Porcentaje de cesiones sobre el total de transferencias.

**Loan transfers:** Conteo de transferencias bajo la modalidad de préstamo/cedido.

**Permanent transfers:** Conteo de transferencias permanentes de compra/venta.

**Previous season spending:** Gasto registrado en la temporada inmediatamente anterior.

**Sepnding Growth %:** Porcentaje de crecimiento del gasto respecto al año/temporada previa.

**YoY Growth €:** Variación absoluta en euros del gasto en fichajes interanual.

**YoY growth %:** Variación porcentual de crecimiento respecto al año anterior.

**Average age:** Edad media de los jugadores transferidos.

**League Net Spend:** Balance neto individualizado por liga.

**League Efficiency:** Ratio entre el valor de mercado adquirido y el gasto realizado.

**Most Efficient League:** Liga con mejor ratio de rendimiento financiero/deportivo.

**League Rank / League Spending Rank / League Income Rank:** Posicionamiento dinámico de ligas según gasto, ingresos o neto (RANKX).

**Buying Club Rank / Selling Club Rank:** Clasificación dinámica de clubes en compras y ventas.

**Fastest Growing League:** Liga con la mayor tasa de expansión económica interanual.

**Biggest Buying Club / Biggest Selling Club:** Nombres de los clubes líderes en inversión e ingresos.

**Buying Club Color / Selling Club Color / League Color:** Formato condicional dinámico mediante códigos hexadecimales.

**Selected league / Selected season:** Captura dinámica del contexto de filtro aplicado (SELECTEDVALUE).

# 🎨 4. Estructura e Interacción del Dashboard (Power BI)
El cuadro de mando está estructurado en 5 pantallas analíticas diseñadas bajo principios de User Experience (UX/UI) ejecutivo:

## 📄 Página 1: Executive Summary

**Objetivo:** Vista macro ejecutiva sobre tendencias globales de gasto, cuota de mercado por ligas y modalidades de contratación (2020-2025).

**Componentes Visuales:**

Botones interactivos para navegas por el resto de páginas.

Boton de Reset Filters.

Filtros de temporada y liga

**Tarjetas KPI Top:** Total transfer spending, Number of transfer, Average transfers fee y Most expensive transfer.

**Spending Evolution:** Evolución temporal del gasto por temporada (Líneas).

**Top 5 Transfer Spending by League:** Distribución del gasto total por liga (Barras horizontales).

**Market Share by League:** Porcentaje del volumen de mercado por liga (Donut).

**Transfers vs Loan:** Comparativa entre contratos permanentes y préstamos (Columnas y Línea).

## 📄 Página 2: Club Analysis

**Objetivo:** Analizar el comportamiento financiero de los clubes en el mercado de pases (compradores vs. vendedores) y su matriz de inversión.

**Componentes Visuales:**

Botones interactivos para navegas por el resto de páginas.

**Tarjetas KPI Top:** Total transfer spending, Total transfer income, Net spend, Top sepending club y Top selling club.

**Top 10 Buying Clubs:** Ranking de clubes con mayor desembolso (Barras horizontales).

**Top 10 Selling Clubs:** Ranking de clubes con mayor ingreso por ventas (Barras horizontales).

**Club Investment Matrix:** Compara gasto total frente a numero de fichajes (Gráfico de Dispersión).

## 📄 Página 3: Player Analysis

**Objetivo:** Evaluar métricas demográficas y la relación entre edad, posición táctica y cotización económica.

**Componentes Visuales:**

Botones interactivos para navegas por el resto de páginas.

**Tarjetas KPI Top:** Average age, Average market value, Average transfers fee, Most expensive player y Most represented nationality.

**Average Fee by Position:** Precio medio por posición táctica (Columnas).

**Transfers by Position:** Volumen de contrataciones según la zona del campo (Barras horizontales).

**Player market value by Age:** Correlación entre la edad del jugador y el valor de mercado (Líneas).

**Top 5 Nationalities:** Países con mayor número de jugadores traspasados (Barras).

## 📄 Página 4: Transfer Intelligence

**Objetivo:** Descubrir patrones analíticos avanzados sobre modelos de negocio (cedidos vs. permanentes) e inflación de mercado.

**Componentes Visuales:**

Botones interactivos para navegas por el resto de páginas.

**Tarjetas KPI Top:** Total spending growth, Loan percentage, Average transfers fee growth, Fastest growing league y Highest net spending league.

**Transfer Spending Evolution by League:** Evolución temporal de la inversión por liga (Líneas).

**Loan vs Permanent:** Desglose porcentual y absoluto por tipo de transacción (Columnas apiladas).

**League Spending Evolution:** Evolución por año y liga de la cuota de mercado (Áreas).

**Average Transfer Fee vs Market Value:** Desviación entre el valor de mercado real y el precio final pagado (Líneas).

## 📄 Página 5: Transfer Explorer

**Objetivo:** Módulo de exploración ad-hoc a nivel de detalle de jugador y transacción individual.

**Componentes Visuales:**

Botones interactivos para navegas por el resto de páginas.

Boton de Reset Filters.

Filtros de liga, club, posicion, nacionalidad, tipo de transferencia y precio.

**Top 10 Market Value Players:** Ranking de los jugadores con mayor valor de mercado Columnas).

**Transfers Above 100M€:** Listado de los fichajes superiores a 100M€ (Tabla).

**Market Value Distribution by Position:** Visualización jerárquica por valor de mercado/posición (Treemap).

**Top 5 Nationalities:** Países con mayor número de jugadores traspasados (Donut).

# 📊 5. Réplica e Implementación Completa en Tableau
Con el objetivo de garantizar una arquitectura multiplataforma y validar la agilidad del modelo en diferentes entornos BI corporativos, se ha replicado íntegramente la solución analítica en Tableau Desktop / Tableau Public:

**Conexión y Modelado en Tableau Data Source:** Carga de la estructura de datos deduplicada manteniendo la consistencia de tipos, agrupaciones e integridad transaccional.

**Traducción de Lógica de Negocio a Expresiones LOD:** Conversión de las fórmulas DAX avanzadas a expresiones de nivel de detalle (Level of Detail - LOD: FIXED, INCLUDE, EXCLUDE) y campos calculados equivalentes para preservar la precisión en métricas temporales, ratios de eficiencia y rankings.

**Diseño e Interacciones:** Reconstrucción de las 5 vistas analíticas (Executive Summary, Club Analysis, Player Analysis, Transfer Intelligence y Transfer Explorer) adaptando las acciones de filtro, navegación UX y tooltips a la suite de Tableau.

# 🐍 6. Análisis Exploratorio de Datos (EDA) y Automatización en Python Core
Desarrollo de scripts analíticos avanzados basados en **Python Puro (Core)** para procesar y validar datasets de partidos jornada a jornada (aplicado al análisis detallado de competiciones como La Liga):

**Carga e Ingesta Estructurada:** Lectura de archivos CSV mediante `csv.DictReader` y control estricto de tipos de datos y fechas con `datetime`.

**Agregación por Diccionarios y Listas:** Construcción de diccionarios anidados para el cálculo automatizado de partidos jugados, victorias, empates, derrotas, goles a favor/contra, diferencia de goles y puntos por equipo.

**Validación de Integridad Numérica cruzada:** Comprobación matemática de que la suma total de partidos, puntos y goles coincide exactamente con los registros oficiales de la competición.

**Generación Dinámica de Rankings:** Ordenamiento mediante funciones `lambda` y `sorted` para la extracción de tops y clasificaciones detalladas.

# 🚀 7. Estado Actual y Próximos Pasos
**[x] Fase 1:** Configuración de PostgreSQL, creación de DDL, ingesta y auditoría de datos mediante consultas SQL (SQL QA).

**[x] Fase 2:** Exportación a CSV, conexión de Power BI y transformación en Power Query (eliminación de duplicados In/Out).

**[x] Fase 3:** Modelado de 3 tablas y desarrollo del repositorio de medidas DAX.

**[x] Fase 4:** Diseño completo e interactivo del Dashboard en Power BI Desktop (UX/UI y Tooltips).

**[x] Fase 5:** Migración y réplica del ecosistema a Tableau Desktop / Tableau Public (expresiones LOD, dashboards equivalentes y publicación).

**[x] Fase 6:** Análisis Exploratorio de Datos (EDA) y procesamiento automatizado de partidos mediante Python Core.

Desarrollado como proyecto avanzado de Business Intelligence, Data Engineering y Visual Analytics.
