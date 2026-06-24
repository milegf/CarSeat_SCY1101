# Car Seats - Evaluación Parcial N°3 (SCY1101)

Solución end-to-end de análisis de ventas para el dataset Car Seats: pipeline ETL con dos fuentes de datos (CSV + API de tipo de cambio), análisis exploratorio, dashboard interactivo con Plotly Dash y modelos supervisados para predecir si una tienda tendrá ventas altas o bajas.

**Asignatura:** SCY1101 - Programación para la Ciencia de Datos

## Estructura del Proyecto

```
.
├── data/
│   ├── raw/                    # dataset_car_seats.csv (original, sin modificar)
│   └── processed/               # carseats_clp.csv (salida del ETL: CLP + HighSales)
├── notebooks/
│   └── CarSeats_Ev3.ipynb       # Notebook principal: ETL, EDA, preparación y modelos
├── src/
│   └── dashboard.py              # Dashboard Plotly Dash (proceso independiente)
├── docs/
│   ├── Arquitectura.pdf          # Comparación de arquitecturas y justificación
│   ├── Docker.pdf                # Investigación de Docker en ciencia de datos
│   └── img/                      # Diagramas usados en Arquitectura.pdf
├── requirements.txt
└── README.md
```

## Requisitos

- Python 3.11
- Dependencias listadas en `requirements.txt`, con versiones fijadas para reproducibilidad

## Instalación

```bash
pip install -r requirements.txt
```

## Cómo Ejecutar el Notebook

```bash
jupyter notebook notebooks/CarSeats_Ev3.ipynb
```

Se recomienda **Kernel → Restart & Run All** para verificar que corre de principio a fin sin errores. El notebook cubre, en orden: importaciones, pipeline ETL (Sección 1), análisis exploratorio (Sección 2), diseño del dashboard (Sección 3, documentado pero no ejecutado desde aquí), preparación de datos (Sección 4), modelos supervisados (Sección 5) y conclusiones generales (Sección 6).

El notebook genera `data/processed/carseats_clp.csv`, que es el archivo que después consume el dashboard de forma independiente.

## Cómo Ejecutar el Dashboard

El dashboard vive en un script aparte (`src/dashboard.py`) y no dentro del notebook, porque un servidor de Dash queda corriendo de forma indefinida y bloquearía la ejecución del notebook si estuviera en una celda.

```bash
# Solo local
python src/dashboard.py
# -> http://127.0.0.1:8050

# Local + túnel público con ngrok (requiere cuenta en ngrok.com)
export NGROK_AUTHTOKEN=tu_token_aqui
python src/dashboard.py --ngrok
```

Si `--ngrok` falla (sin token o sin internet), el dashboard local sigue funcionando igual, solo no se genera la URL pública.

Para detenerlo: `Ctrl+C` en la misma terminal, o `pkill -f "src/dashboard.py"`.

## Documentación Adicional

- **`docs/Arquitectura.pdf`**: compara las arquitecturas Lambda, Lakehouse y Pipeline Híbrido Modular, y justifica por qué esta última es la utilizada en el proyecto.
- **`docs/Docker.pdf`**: investigación sobre Docker en ciencia de datos.

## Resultados Principales

- **ETL:** conversión de 4 columnas monetarias de USD a CLP vía API (con fallback en cadena), y creación de la variable objetivo `HighSales` (59% bajas / 41% altas).
- **EDA:** `ShelveLoc` es la variable más asociada a las ventas (15% de ventas altas en ubicación `Bad` vs 78% en `Good`); `Price` y `Advertising` son predictores secundarios consistentes.
- **Modelos:** Regresión Logística y Random Forest, ambos optimizados con `GridSearchCV`. La Regresión Logística obtiene el mejor desempeño (F1 = 0.90, ROC-AUC = 0.98 en test).

## Flujo de Trabajo con Git

Una rama por etapa del proyecto (`feature/etl`, `feature/eda`, `feature/dashboard`, `feature/modelos`, `feature/docs`), integradas en `develop` antes de llegar a `main`.
