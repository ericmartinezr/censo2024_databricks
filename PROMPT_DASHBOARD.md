# Prompt: Chile Census 2024 Dashboard (Spanish)

Create a comprehensive demographic analysis dashboard for Chile Census 2024 data with all content in Spanish.

## Data Source

- Table: `genie_catalog.default.censo_2024_completo`
- Use a **metric view dataset** (local metric view) as the primary dataset

## Dataset Configuration (Metric View)

### Metric View Structure

Create a local metric view dataset with version 1.1:

**Source table:** `genie_catalog.default.censo_2024_completo`

### Dimensions (with Spanish display names and COALESCE for NULL handling)

All descriptive fields should use `COALESCE(source.field_name, 'N/A')` to display 'N/A' instead of NULL values:

- **region_nombre** → Display: "Región"
- **sexo_desc** → Display: "Género"
- **edad** → Display: "Edad"
- **area_desc** → Display: "Tipo de Área"
- **estado_civil_desc** → Display: "Estado Civil"
- **sit_fuerza_trabajo_desc** → Display: "Estado Laboral"
- **tenencia_vivienda_desc** → Display: "Tenencia de Vivienda"
- **nacionalidad_desc** → Display: "Nacionalidad"
- **autoid_pueblo_desc** → Display: "Identidad Indígena"
- **discapacidad_desc** → Display: "Estado de Discapacidad"
- **servicio_internet_fija_desc** → Display: "Internet Fija"
- **servicio_internet_movil_desc** → Display: "Internet Móvil"
- **servicio_internet_satelital_desc** → Display: "Internet Satelital"
- **indice_hacinamiento_desc** → Display: "Índice de Hacinamiento"

### Measures (aggregated metrics with Spanish display names)

1. **total_population** (Población Total)
   - Expression: `COUNT(*)`
   - Display: "Población Total"

2. **poblacion** (Población) - for chart labels
   - Expression: `COUNT(*)`
   - Display: "Población"
   - Note: This measure is used in widgets to avoid "Count of Records" labels

3. **total_households** (Total Hogares)
   - Expression: `COUNT(DISTINCT CONCAT(CAST(source.id_vivienda AS STRING), '-', CAST(source.id_hogar AS STRING)))`
   - Display: "Total Hogares"
   - Note: Uses composite key (id_vivienda + id_hogar) for accurate household counting

4. **total_dwellings** (Total Viviendas)
   - Expression: `COUNT(DISTINCT source.id_vivienda)`
   - Display: "Total Viviendas"

5. **avg_persons_per_household** (Promedio Personas por Hogar)
   - Expression: `CAST(COUNT(*) AS DOUBLE) / NULLIF(COUNT(DISTINCT CONCAT(CAST(source.id_vivienda AS STRING), '-', CAST(source.id_hogar AS STRING))), 0)`
   - Display: "Promedio Personas por Hogar"

## Dashboard Structure

### Dashboard Title

"Censo Chile 2024 - Análisis Demográfico"

### Page Layout

Single page with all widgets on a 12-column grid.

### Widgets (in order, all in Spanish)

#### 1. Header (Row 0, Full Width)

- **Type:** Markdown text box
- **Position:** Row 0, Column 0, Width 12, Height 2
- **Content:**

```markdown
## Censo Chile 2024 - Análisis Demográfico Integral

Este dashboard proporciona una visión detallada del panorama demográfico de Chile basado en los datos del censo 2024. Explore la distribución de población por región, género, edad, áreas urbanas/rurales, estado civil, participación laboral, características de vivienda, nacionalidad, identidad indígena, estado de discapacidad y patrones de acceso a internet.
```

#### 2. Key Metrics - 4 Counters (Row 2)

All counters at Row 2, Height 3, Width 3 each:

- **Población Total** (Column 0)
  - Measure: `MEASURE(total_population)`
- **Total Hogares** (Column 3)
  - Measure: `MEASURE(total_households)`
- **Total Viviendas** (Column 6)
  - Measure: `MEASURE(total_dwellings)`
- **Promedio Personas por Hogar** (Column 9)
  - Measure: `MEASURE(avg_persons_per_household)`
  - Format: 2 decimal places

#### 3. Population by Region (Row 5)

- **Type:** Horizontal Bar Chart
- **Position:** Row 5, Column 0, Width 12, Height 5
- **Title:** "Población por Región"
- **X-axis:** `MEASURE(poblacion)` (quantitative)
- **Y-axis:** `region_nombre` (categorical, sorted by measure)
- **Sort:** Ascending by population

#### 4. Gender Distribution (Row 10, Left)

- **Type:** Pie Chart
- **Position:** Row 10, Column 0, Width 6, Height 5
- **Title:** "Distribución por Género"
- **Angle:** `MEASURE(poblacion)`
- **Color:** `sexo_desc`
- **Label:** Show labels with `sexo_desc`

#### 5. Urban vs Rural (Row 10, Right)

- **Type:** Vertical Bar Chart
- **Position:** Row 10, Column 6, Width 6, Height 5
- **Title:** "Distribución Urbano vs Rural"
- **X-axis:** `area_desc` (categorical)
- **Y-axis:** `MEASURE(poblacion)` (quantitative)

#### 6. Marital Status (Row 15, Left)

- **Type:** Vertical Bar Chart
- **Position:** Row 15, Column 0, Width 6, Height 5
- **Title:** "Estado Civil"
- **X-axis:** `estado_civil_desc` (categorical, sorted reversed by measure)
- **Y-axis:** `MEASURE(poblacion)` (quantitative)

#### 7. Labor Force Participation (Row 15, Right)

- **Type:** Pie Chart
- **Position:** Row 15, Column 6, Width 6, Height 5
- **Title:** "Participación en Fuerza Laboral"
- **Angle:** `MEASURE(poblacion)`
- **Color:** `sit_fuerza_trabajo_desc`
- **Label:** Show labels with `sit_fuerza_trabajo_desc`

#### 8. Housing Tenure (Row 20, Left)

- **Type:** Vertical Bar Chart
- **Position:** Row 20, Column 0, Width 6, Height 5
- **Title:** "Tenencia de Vivienda"
- **X-axis:** `tenencia_vivienda_desc` (categorical, sorted reversed by measure)
- **Y-axis:** `MEASURE(total_households)` (quantitative)
- **Note:** Uses household count, not population count

#### 9. Top Nationalities (Row 20, Right)

- **Type:** Horizontal Bar Chart
- **Position:** Row 20, Column 6, Width 6, Height 5
- **Title:** "Top 10 Nacionalidades"
- **X-axis:** `MEASURE(poblacion)` (quantitative)
- **Y-axis:** `nacionalidad_desc` (categorical, sorted by measure)

#### 10. Indigenous Identity (Row 25, Left)

- **Type:** Pie Chart
- **Position:** Row 25, Column 0, Width 6, Height 5
- **Title:** "Identidad Indígena"
- **Angle:** `MEASURE(poblacion)`
- **Color:** `autoid_pueblo_desc`
- **Label:** Show labels with `autoid_pueblo_desc`

#### 11. Disability Status (Row 25, Right)

- **Type:** Vertical Bar Chart
- **Position:** Row 25, Column 6, Width 6, Height 5
- **Title:** "Estado de Discapacidad"
- **X-axis:** `discapacidad_desc` (categorical, sorted reversed by measure)
- **Y-axis:** `MEASURE(poblacion)` (quantitative)

#### 12. Fixed Internet Access (Row 30, Left)

- **Type:** Vertical Bar Chart
- **Position:** Row 30, Column 0, Width 6, Height 5
- **Title:** "Acceso a Internet Fija (Hogares)"
- **X-axis:** `servicio_internet_fija_desc` (categorical, sorted reversed by measure)
- **Y-axis:** `MEASURE(total_households)` (quantitative)
- **Note:** Uses household count, not population count

#### 13. Overcrowding Index (Row 30, Right)

- **Type:** Pie Chart
- **Position:** Row 30, Column 6, Width 6, Height 5
- **Title:** "Índice de Hacinamiento (Hogares)"
- **Angle:** `MEASURE(total_households)`
- **Color:** `indice_hacinamiento_desc`
- **Label:** Show labels with `indice_hacinamiento_desc`
- **Note:** Uses household count, not population count

## Theme Configuration (Professional & Modern)

Apply these theme settings for a professional look:

### Colors

- **Visualization Palette:** `["#1f77b4", "#ff7f0e", "#2ca02c", "#d62728", "#9467bd", "#8c564b", "#e377c2", "#7f7f7f", "#bcbd22", "#17becf", "#aec7e8", "#ffbb78", "#98df8a", "#ff9896", "#c5b0d5", "#c49c94"]`

### Typography

- **Font Family:** Inter (from available open source fonts)
- **Base Font:** Inter, 13px, weight 400
- **Widget Titles:** Inter, 16px, weight 600

### Layout & Spacing

- **Widget Corner Radius:** 8px
- **Widget Shadow:** 10 (subtle shadow)
- **Widget Margin:** 8px
- **Widget Padding:** 12px

## Key Implementation Notes

### 1. Localization (Critical)

- **ALL widget titles must be in Spanish**
- **ALL field display names must be in Spanish** (set in metric view dimensions/measures)
- **Use `MEASURE(poblacion)`** instead of `COUNT(*)` in widgets to display "Población" instead of "Count of Records"
- **Dashboard title in Spanish:** "Censo Chile 2024 - Análisis Demográfico"

### 2. NULL Handling

- All dimension fields use `COALESCE(source.field_name, 'N/A')` to replace NULL with 'N/A'
- This prevents empty/null values from appearing in charts

### 3. Household Counting

- Use composite key: `CONCAT(CAST(source.id_vivienda AS STRING), '-', CAST(source.id_hogar AS STRING))`
- Widgets showing household-level data (housing tenure, internet access, overcrowding) must use `MEASURE(total_households)`, not `MEASURE(poblacion)`

### 4. Measure Reference Syntax

- In widget queries, reference measures using: `MEASURE(\`measure_name\`)`
- Example: `MEASURE(\`poblacion\`)`, `MEASURE(\`total_households\`)`
- Field name in widget: `measure(poblacion)` (no backticks)

### 5. Grid Layout

- Dashboard uses 12-column grid system
- No widget overlap allowed
- Widgets should span full width where appropriate (header, population by region)

### 6. Sorting

- Bar charts should be sorted by measure value for better readability
- Use `orderBys` in query and `sort` in scale encoding
- Ascending (1) or descending (2) in orderBys
- "by": "measure" or "by": "measure-reversed" in scale sort

## Verification Checklist

After creating the dashboard, verify:

- [ ] Dashboard title is in Spanish
- [ ] All widget titles are in Spanish
- [ ] All x-axis labels show Spanish field names (not English)
- [ ] All y-axis labels show Spanish measure names (not "Count of Records")
- [ ] Counter widgets show Spanish measure names
- [ ] NULL values display as "N/A" in all categorical fields
- [ ] Household-level widgets use household counts, not population
- [ ] All widgets render without errors
- [ ] Layout has no overlapping widgets
- [ ] Theme is applied (Inter font, rounded corners, shadows)
- [ ] Colors are vibrant and professional

## Example Metric View YAML

```yaml
version: '1.1'
source: genie_catalog.default.censo_2024_completo
dimensions:
  - name: region_nombre
    expr: COALESCE(source.region_nombre, 'N/A')
    display_name: Región
  - name: sexo_desc
    expr: COALESCE(source.sexo_desc, 'N/A')
    display_name: Género
  # ... (add all other dimensions with display_name in Spanish)
measures:
  - name: total_population
    expr: COUNT(*)
    display_name: Población Total
  - name: poblacion
    expr: COUNT(*)
    display_name: Población
  - name: total_households
    expr: COUNT(DISTINCT CONCAT(CAST(source.id_vivienda AS STRING), '-', CAST(source.id_hogar AS STRING)))
    display_name: Total Hogares
  # ... (add other measures)
```

## Tips for Success

1. **Create the metric view dataset FIRST** before any widgets
2. **Always use Spanish display names** in the metric view definition
3. **Use `MEASURE(poblacion)` for population counts** in widgets to get Spanish labels
4. **Test widget rendering** after creation to ensure Spanish labels appear correctly
5. **Apply theme settings** after all widgets are created
6. **Verify all text is in Spanish** - no English labels should remain
