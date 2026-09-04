# IceAnalytics — Product Activity Data Quality Challenge

Análisis de calidad de datos, limpieza con criterio y métricas descriptivas sobre un
dataset de actividad de producto (`product_activity.csv`), donde cada fila representa
un *post* (evento) generado por un usuario.

## 🎯 Objetivo

Construir sistemas es solo la mitad del trabajo — entender lo que producen es la otra
mitad. Este proyecto detecta inconsistencias en los datos, las limpia sin buscar
perfección, calcula métricas de negocio y traduce los hallazgos en decisiones de
producto respaldadas por evidencia.

## 📦 Dataset

- **Archivo de entrada:** `product_activity.csv`
- **Unidad de análisis:** cada fila = un post/evento (no un usuario).
- **Columnas principales:** `user_id`, `created_at` (signup), `country`, `plan_type`
  (free/pro/enterprise), `user_age`, `post_id`, `post_category`, `post_created_at`,
  `votes_received`, `user_total_posts`, `days_since_signup`, `device_type`.

## 🧱 Estructura del notebook

1. **Carga y Exploración** — `head()`, `info()`, `describe()`, shape del dataset.
2. **Data Quality Report — Diagnóstico** — nulos, duplicados exactos, valores únicos
   y frecuencias de columnas categóricas "sucias", chequeos lógicos de fechas.
3. **Normalización + Recálculo + Quarantine**
   - Normalización canónica de `plan_type`, `device_type`, `post_category`.
   - Corrección de typos vía diccionario de mapeo.
   - Recálculo de `days_since_signup_calc` a partir de fechas parseadas.
   - Separación en `df_core` (limpio) y `df_quarantine` (filas con errores duros,
     con `reason_code` explicando el motivo).
4. **Data Quality Report (resumen final)** — filas RAW vs CORE vs QUARANTINE,
   % quarantine, duplicados removidos, % de mismatch en fechas.
5. **Métricas y Análisis**
   - Distribuciones de volumen: usuarios por plan, actividad por país/categoría/dispositivo.
   - Engagement (votos) por plan, categoría, dispositivo y país.
   - Promedios e interpretación: unidad de análisis y sesgos.
   - Evento vs usuario: por qué difieren los promedios agrupados.
6. **Concentración y Temporalidad**
   - Regla de Pareto: % de posts/votos generado por el top 1/5/10/20% de usuarios.
   - Boxplots de votos (general y por plan) para visualizar asimetría.
   - Tendencia mensual de actividad y engagement.
   - Análisis de cohortes por mes de signup.
7. **Métrica propia — `engagement_score`**
   Combina promedio de votos con volumen de posts (`votos_promedio * log1p(total_posts)`)
   para evitar que un post viral aislado supere a un usuario consistente.
8. **Product Decisions** — segmento a priorizar, qué parte del tablero "mentía" antes
   de limpiar, nuevos datos a trackear, y 2 acciones concretas respaldadas por evidencia.

## 📊 Hallazgos clave

| Métrica | Valor |
|---|---|
| Filas RAW | 8,782 |
| Filas CORE (limpias) | 8,507 |
| Filas en Quarantine | 275 (3.13%) |
| Duplicados exactos removidos | 172 |
| Mismatch en `days_since_signup` original | 4,479 filas (51.0%) |
| Usuarios únicos | 1,994 |
| Votos promedio por evento | 6.91 |
| Votos promedio por usuario | 6.90 |
| % votos del top 1% de usuarios | 6.43% |
| % votos del top 20% de usuarios | 51.92% |

**El hallazgo más importante:** la columna `days_since_signup` original no coincidía con
el valor recalculado a partir de las fechas en el **51% de las filas** — cualquier
métrica construida sobre esa columna sin recalcularla estaba comprometida en la mitad
del dataset.

## 📁 Archivos exportados

- `clean_product_activity.csv` — dataset core, limpio y normalizado.
- `quarantine_product_activity.csv` — filas excluidas con su `reason_code`.
- `metrics_summary.csv` — tabla resumen de métricas principales.
- `boxplot_votos.png`, `tendencia_mensual.png`, `cohortes_signup.png` — visualizaciones.

## ▶️ Cómo correr

```bash
pip install pandas numpy matplotlib
jupyter notebook IceAnalytics.ipynb
```

Requiere `product_activity.csv` en el mismo directorio que el notebook.

## 🛠️ Stack

Python · pandas · numpy · matplotlib
