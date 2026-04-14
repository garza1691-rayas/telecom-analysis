# 📡 Análisis de Clientes — ConnectaTel

Proyecto de análisis de datos para evaluar el comportamiento de los clientes de **ConnectaTel**, una empresa de telecomunicaciones en Latinoamérica. El análisis cubre datos registrados hasta el año **2024**.

---
 
## 🗂️ Estructura del proyecto

```
├── S7_Version-Estudiante-Project-ConnectaTel.ipynb  # Notebook principal
├── datasets/
│   ├── plans.csv          # Información de planes (precios, minutos, GB)
│   ├── users_latam.csv    # Datos de usuarios (4,000 registros)
│   └── usage.csv          # Registros de uso (40,000 registros)
└── README.md
```

---

## 🎯 Objetivo

Evaluar el comportamiento de los clientes de ConnectaTel mediante:

- Limpieza y validación de datos
- Construcción de perfiles de usuario por comportamiento de uso
- Segmentación de clientes por edad y nivel de uso
- Identificación de outliers y patrones extremos
- Generación de recomendaciones accionables para el negocio

---

## 📦 Datasets

| Dataset | Filas | Columnas | Descripción |
|---|---|---|---|
| `plans.csv` | 2 | 8 | Planes Básico y Premium con precios y límites |
| `users_latam.csv` | 4,000 | 8 | Datos demográficos y plan de cada usuario |
| `usage.csv` | 40,000 | 6 | Registros de llamadas y mensajes por usuario |

---

## 🔍 Problemas detectados en los datos

### `users_latam.csv`
| Columna | Problema | Cantidad | % |
|---|---|---|---|
| `city` | Valores `"?"` (sentinel) | 565 filas | 14.1% |
| `age` | Valores `-999` (sentinel) | — | — |
| `reg_date` | Fechas en año 2026 (futuro) | 40 filas | 1.0% |

### `usage.csv`
| Columna | Problema | Cantidad | % |
|---|---|---|---|
| `duration` | Nulos estructurales (mensajes no tienen duración) | 22,076 | 55.19% |
| `length` | Nulos estructurales (llamadas no tienen longitud) | 17,896 | 44.74% |
| `date` | Nulos | 50 | 0.12% |

> Los nulos en `duration` y `length` son **MAR (Missing At Random)** — no son errores sino consecuencia del tipo de evento registrado (`call` vs `text`).

---

## 🧹 Limpieza aplicada

- `age`: valores `-999` reemplazados con la **mediana** (48 años)
- `city`: valores `"?"` reemplazados con `NaN`
- `reg_date`: fechas fuera del rango 2000–2024 marcadas como `NaT`
- `duration` y `length`: se dejaron como nulos (justificado por lógica de negocio)

---

## 📊 Métricas construidas por usuario

A partir de `usage.csv`, se agregaron las siguientes métricas por `user_id`:

| Columna | Descripción |
|---|---|
| `cant_mensajes` | Total de mensajes de texto enviados |
| `cant_llamadas` | Total de llamadas realizadas |
| `cant_minutos_llamada` | Total de minutos de llamada acumulados |

Estas métricas se combinaron con `users_latam.csv` en un dataframe llamado `user_profile`.

---

## 👥 Segmentación de clientes

### Por nivel de uso (`grupo_uso`)
| Segmento | Criterio | Usuarios | % |
|---|---|---|---|
| Bajo uso | llamadas < 5 y mensajes < 5 | 778 | 19.5% |
| Uso medio | llamadas < 10 y mensajes < 10 | 2,943 | 73.6% |
| Alto uso | resto | 279 | 7.0% |

### Por edad (`grupo_edad`)
| Segmento | Criterio | Usuarios | % |
|---|---|---|---|
| Joven | age < 30 | 760 | 19.0% |
| Adulto | 30 ≤ age < 60 | 2,018 | 50.5% |
| Adulto Mayor | age ≥ 60 | 1,222 | 30.6% |

---

## 📈 Outliers encontrados

| Columna | Outliers | Decisión |
|---|---|---|
| `age` | 0 | Sin acción |
| `cant_mensajes` | 46 | Conservar — valores reales |
| `cant_llamadas` | 30 | Conservar — valores reales |
| `cant_minutos_llamada` | 109 | Conservar con precaución (máx 155 min vs media 23 min) |

---

## 💡 Hallazgos clave

- El **65% de los usuarios de Alto uso** aún tiene plan Básico — oportunidad directa de upselling.
- La distribución de planes es casi homogénea entre segmentos (~65% Básico / ~35% Premium), lo que sugiere que el plan asignado **no refleja el comportamiento real**.
- Los **Adultos Mayores** (30.6% de la base) son propensos a usar más llamadas que mensajes — perfil sin plan dedicado.
- Los outliers en minutos (hasta 155 min) representan usuarios con necesidades de plan ilimitado o corporativo.

---

## 🚀 Recomendaciones para ConnectaTel

1. **Crear un plan intermedio** entre Básico (100 min) y Premium (600 min) para el segmento de Uso medio.
2. **Campaña de migración a Premium** dirigida a usuarios de Alto uso que aún están en plan Básico.
3. **Plan enfocado en llamadas para Adulto Mayor** — precio accesible, más minutos, menos mensajes.
4. **Mejorar el proceso de registro** — el 14% de ciudades inválidas y 1% de fechas futuras indican problemas en el formulario de captura.

---

## 🛠️ Tecnologías utilizadas

- Python 3
- pandas
- matplotlib
- seaborn

---

## ▶️ Cómo ejecutar

1. Clona el repositorio
2. Coloca los archivos `.csv` en la carpeta `/datasets/`
3. Abre el notebook `S7_Version-Estudiante-Project-ConnectaTel.ipynb`
4. Ejecuta las celdas en orden
