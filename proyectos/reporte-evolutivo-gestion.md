# Proyecto: Reporte Evolutivo de Gestión (Performance de Call Center)

**Rol:** Data Analyst
**Contexto:** Desarrollo de un dashboard de seguimiento para el rendimiento del Call Center de un grupo empresarial.

---

## 📊 Descripción del Proyecto

Este reporte consiste en un dashboard interactivo diseñado en Power BI para el seguimiento integral de la performance del call center del grupo. Extrae datos del software CRM "Pilot Solutions" y abarca métricas clave como *leads* asignados, ventas derivadas a salones y *leads* cerrados/dados de baja, de todas las concesionarias a nivel nacional.

---

## 🎯 Objetivos de Negocio

* Monitorear y evaluar el desempeño individual y colectivo de los vendedores del call center.
* Identificar eficiencias y oportunidades de mejora en el proceso de gestión de *leads*.
* Optimizar la tasa de conversión de *leads* a ventas.
* Proveer a los gerentes de sucursal una herramienta de decisión ágil y visual.

---

## 🛠️ Habilidades Técnicas y Metodología

* **Extracción y Carga de Datos (ETL):** Descarga de bases de datos desde el CRM Pilot Solutions, carga a SharePoint y posterior importación a Power BI.
* **Modelado de Datos (Estrella/Copo de Nieve):**
    * Creación de tablas de hechos para `Leads`, `Ventas` y `Bajas` (Cerrado).
    * Generación de tablas de dimensiones para `Sucursales`, `Origen de Leads`, `Suborigen`, `Tipo de Negocio`, `Vendedores` y una `Tabla de Calendario`.
    * Establecimiento de relaciones entre la tabla general (anexando Leads/Ventas/Bajas) y las tablas de dimensiones, y con la tabla de calendario (utilizando una columna de fecha unificada `Fecha Todos`).
* **Creación de KPIs y Métricas:** Cuantificación de *leads* asignados, ventas efectuadas, *leads* cerrados y, fundamentalmente, la **tasa de conversión (ventas por *leads* asignados)**.
* **Diseño de Dashboard:** Creación de un dashboard intuitivo con filtros dinámicos (sucursal, tipo de negocio, vendedor, origen de *lead*, suborigen) para facilitar el análisis gerencial.
* **Manejo de Grandes Volúmenes de Datos:** Trabajo con aproximadamente 200,000 registros en el período Enero 2023 - Marzo 2024.
* **Herramientas:** CRM Pilot Solutions, SharePoint, Power BI (incluyendo DAX para cálculos).
---
✨ Impacto y Resultados
Este dashboard empoderó a los gerentes de sucursal con una visión en tiempo real de la gestión de su call center, permitiéndoles:

Identificar a los vendedores con mejor y peor desempeño.

Analizar las causas de la baja de leads para tomar acciones correctivas.

Optimizar la asignación de recursos y mejorar las estrategias de seguimiento.

Incrementar la tasa de conversión de leads a ventas, lo que se tradujo en un impacto directo en los ingresos del grupo.

Este proyecto es un claro ejemplo de cómo la transformación de datos en insights accionables puede conducir a una mejora significativa en la eficiencia operativa y los resultados comerciales.

---

## 📐 Medidas DAX Clave Utilizadas

```dax
// Tabla de Calendario (ejemplo de la estructura generada)
calendario = ADDCOLUMNS (
    CALENDAR (DATE(2023,1,1), DATE(2024,12,31)),
    "Id Fecha", FORMAT ( [Date], "YYYYMMDD" ),
    "Año", YEAR ( [Date] ),
    "Mes", FORMAT ( [Date], "MM" ),
    "Nombre Mes", FORMAT ( [Date], "mmmm" ),
    "Día Semana número", WEEKDAY ( [Date] ),
    "Día Semana ", FORMAT ( [Date], "dddd" ),
    "Trimestre", "Trim" & FORMAT ( [Date], "Q" ),
    "Año/trimestre", FORMAT ( [Date], "YYYY" ) & "/Q" & FORMAT ( [Date], "Q")
)

// Ejemplos de posibles medidas DAX para este reporte:

// Total de Leads Asignados
Total Leads Asignados = COUNTROWS('TablaGeneralLeadsVentasBajas') // Asumiendo una tabla general donde se anexan los leads

// Total de Ventas Efectuadas
Total Ventas = COUNTROWS(FILTER('TablaGeneralLeadsVentasBajas', 'TablaGeneralLeadsVentasBajas'[TipoRegistro] = "Venta")) // Asumiendo una columna 'TipoRegistro' para identificar ventas

// Total de Leads Cerrados (Bajas)
Total Leads Cerrados = COUNTROWS(FILTER('TablaGeneralLeadsVentasBajas', 'TablaGeneralLeadsVentasBajas'[TipoRegistro] = "Baja")) // Asumiendo una columna 'TipoRegistro' para identificar bajas

// Tasa de Conversión (%)
Tasa de Conversión = DIVIDE([Total Ventas], [Total Leads Asignados], 0)