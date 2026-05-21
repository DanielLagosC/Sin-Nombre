# Análisis de Comportamiento de Compras en la Plataforma Peru Compras (2022–2025)

## ¿Qué es este proyecto?

Peru Compras es la plataforma del Estado peruano donde las entidades públicas realizan compras de bienes y servicios a través de catálogos electrónicos preaprobados. Este proyecto extrae, limpia, modela y analiza **474,845 órdenes de compra reales** registradas entre 2022 y 2025, con el objetivo de caracterizar cómo, cuándo y con quién compran las entidades públicas peruanas.

## ¿A quién le sirve?

- **Auditores y analistas de la Contraloría** para identificar patrones de concentración de proveedores
- **Funcionarios del MEF** para evaluar comportamiento de ejecución presupuestal
- **Periodistas e investigadores** interesados en transparencia del gasto público

---

## 🔍 Hallazgos Principales

1. **Tendencia descendente con gasto mediano creciente:** El volumen anual de órdenes decreció de 137K (2022) a 91K (2025), mientras el gasto mediano por orden aumentó sostenidamente, sugiriendo una consolidación progresiva de las compras.

2. **Presión de ejecución presupuestal a fin de año:** El gasto total se concentra en el último trimestre mientras el volumen de órdenes decrece, patrón consistente con la presión de cierre del año fiscal peruano.

3. **Concentración de proveedores:** Se identificaron entidades con más del 60% de sus órdenes concentradas en un único proveedor, lo que representa un riesgo de dependencia en la cadena de abastecimiento público.

4. **Computadoras dominan el gasto en Grandes Compras:** La categoría de equipos informáticos concentra más del 50% del gasto total en órdenes ≥ S/ 100,000, mientras útiles de escritorio lideran en volumen de órdenes ordinarias.

---

## ⚙️ Pipeline ETL — Lo que hace este proyecto técnicamente

### 0. Extracción
- Consumo de la API de la Plataforma Nacional de Datos Abiertos del Perú
- Procesamiento de JSON anidado con múltiples niveles de indexación
- Concatenación de 40+ archivos CSV mensuales con normalización de columnas
- Manejo de encoding latin-1 y bloqueo de servidor resuelto con cabeceras HTTP

### 1. Limpieza
- Corrección de tipos de datos: RUCs como identificadores string, fechas a datetime con formatos inconsistentes por lote
- Estandarización de strings: strip, uppercase, corrección de caracteres mal codificados en latin-1
- Consolidación de ACUERDO_MARCO de 95 a 30 categorías únicas eliminando códigos adjuntos y variantes de tilde
- Corrección de TIPO_PROCEDIMIENTO: 24,587 registros mal clasificados reasignados según criterio normativo del umbral de S/ 100,000
- Validación de coherencia matemática SUB_TOTAL + IGV = TOTAL
- Validación de fechas con días_in_month

### 2. Normalización
- Modelo estrella con 3 dimensiones y 1 tabla de hechos
- Resolución de Slowly Changing Dimensions: nombre más reciente por RUC
- Validación de cardinalidades en ambas direcciones (RUC → nombre y nombre → RUC)
- Reducción de peso: tabla de hechos de 200MB → 38MB post-normalización

---

## 📊 Power BI Dashboard

Construido sobre un modelo estrella con 3 dimensiones y conectado a los CSVs normalizados. Incluye 2 páginas analíticas con narrativa propia.

### Medidas DAX:
- `Cantidad Ordenes Años Completos` — Filtra automáticamente años completos para evitar sesgos por años incompletos
- `% cercanía` — Concentración de órdenes de una entidad hacia un proveedor específico
- `% del Total Ordenes` — Participación de cada segmento sobre el total histórico
- `Gasto total Mediano` — Mediana del monto por orden, resistente a outliers

### Visuales utilizados:
- Cards KPI (cantidad de órdenes, monto total, participación porcentual)
- Gráfico de líneas dual (volumen vs gasto mediano, tendencia anual y mensual)
- Tabla de concentración entidad-proveedor con % de cercanía
- Gráfico combinado barras + línea (entidades y proveedores por volumen y gasto)
- Gráfico de torta (distribución de gasto por acuerdo marco)
- Slicers: Año, Tipo de procedimiento, Afectado por IGV
---

## ⚠️ Limitaciones

- Precios unitarios por orden disponibles solo en PDFs individuales, impide validar coherencia precio-producto a escala
- Montos en soles corrientes sin ajuste por inflación
- El dataset cubre exclusivamente el Catálogo Electrónico de Acuerdo Marco, no la totalidad del gasto público peruano

---

## 🛠️ Herramientas

- **Python 3.14** — Librerías: 'requests', 'pandas', 'numpy', 'os'
- **Power BI Desktop** — 'DAX', modelo estrella
- **GitHub** — control de versiones

---
## 👤 Autor

**Daniel Lagos**  
Estudiante de Ingeniería Industrial — Universidad Nacional del Callao  
[LinkedIn](https://www.linkedin.com/in/daniellagos/)
