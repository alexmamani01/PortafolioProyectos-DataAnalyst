# Proyecto: Preventas Patentadas y Análisis de Perfil de Cliente 

 **Rol:** Data Analyst
 **Contexto:** Informe de seguimiento del ciclo de vida de las ventas de vehículos y análisis demográfico de clientes.

---

## 📊 Descripción del Proyecto

Este informe se enfoca en analizar el flujo de preventas que efectivamente se transforman en unidades patentadas. Adicionalmente, incluye un análisis del perfil demográfico de los clientes, específicamente la edad promedio de los compradores de vehículos. La actualización a Junio 2024 asegura la relevancia y actualidad de los datos.

---

## 🎯 Objetivos de Negocio

* Monitorear la eficiencia del embudo de ventas desde la preventa hasta la patente.
* Identificar posibles cuellos de botella en el proceso de entrega y patentamiento.
* Comprender el perfil demográfico de los clientes (edad promedio) para estrategias de marketing y ventas segmentadas.
* Informar sobre la conversión de preventas y la adecuación del producto al cliente.

---

## 🛠️ Habilidades Técnicas y Metodología

* **Integración de Datos:** Extracción y consolidación de información clave de las tablas `Unidades` y `Preventas` utilizando SQL para correlacionar el ciclo de venta.
* **Análisis Demográfico:** Cálculo de métricas como el promedio de edad de los clientes de preventas, con aplicación de filtros para asegurar la relevancia del análisis (ej. edad entre 25 y 65 años).
* **KPIs y Métricas:** Definición y cálculo de indicadores clave para el seguimiento de la conversión y el perfil del cliente.
* **Herramientas:** SQL (para extracción de datos), Power BI (para modelado y DAX).

---
✨ Impacto y Resultados
Este proyecto proporcionó a los equipos de ventas y gerencia una visibilidad crítica del embudo de conversión y un entendimiento más profundo del perfil demográfico de sus clientes. Esto permitió la optimización de los procesos de venta, la identificación de puntos de fricción y el ajuste de las estrategias de marketing para mejorar la efectividad y la rentabilidad.

---

## 💻 Consulta SQL Utilizada

```sql
SELECT
    Unidades."VIN", Unidades."IDFabrica", Unidades."Patente" AS "PatenteUnidad", Unidades."Entregada", Unidades."Facturada" AS "UnidadFacturada", Unidades."FechaPatentamiento",
    Preventas."Numero" AS "NumeroPreventa", Preventas."Fecha" AS "FechaPreventa",
    Modelos."DescripcionOperativa" AS "ModeloOperativo",
    Origenes."Nombre" AS "OrigenModelo",
    TipoVenta."Descripcion" AS "TipoDeVenta",
    Vendedores."Nombre" AS "NombreVendedor", Vendedores."Jefe",
    SucursalesDeventa."Nombre" AS "SucursalVenta",
    Clientes."Nombre" AS "NombreCliente", Clientes."Localidad", Clientes."Provincia", Clientes."Direccion", Clientes."DNI", Clientes."FechaDeNacim",
    Familias."Nombre" AS "FamiliaModelo"
FROM
    { oj ((((((("Unidades" Unidades LEFT OUTER JOIN "Modelos" Modelos ON Unidades."Modelo" = Modelos."Modelo")
    LEFT OUTER JOIN "Preventas" Preventas ON Unidades."Preventa" = Preventas."Numero")
    LEFT OUTER JOIN "TipoVenta" TipoVenta ON Preventas."TipoDeVentaID" = TipoVenta."TipoDeVentaID")
    LEFT OUTER JOIN "SAI"."dbo"."Vendedores" Vendedores ON Preventas."VendedorID" = Vendedores."VendedorId")
    LEFT OUTER JOIN "SAI"."dbo"."SucursalesDeventa" SucursalesDeventa ON Preventas."Sucursal" = SucursalesDeventa."Numero")
    LEFT OUTER JOIN "Clientes" Clientes ON Preventas."Cliente" = Clientes."Codigo")
    LEFT OUTER JOIN "SAI"."dbo"."Familias" Familias ON Modelos."Familia" = Familias."FamiliaID")
    LEFT OUTER JOIN "Origenes" Origenes ON Modelos."Origen" = Origenes."OrigenID"}
-- Considerar añadir una cláusula WHERE si se necesita filtrar por fecha u otros criterios relevantes para el análisis.
---

📐Medidas DAX Utilizadas
Fragmento de código

// Promedio de Edad de Clientes de Preventas (entre 25 y 65 años)
Promedio Edad PR = 
CALCULATE (
    AVERAGE ( TODOS[EDAD PR] ), // Asumiendo que 'TODOS[EDAD PR]' es una columna que contiene la edad de los clientes de preventa
    TODOS[EDAD PR] >= 25,
    TODOS[EDAD PR] <= 65
)

// Conteo total de Patentes únicas (representa el total de vehículos patentados)
Total Patentes = DISTINCTCOUNT(TODOS[Patente]) // Asumiendo que 'TODOS[Patente]' es la columna de patente de las unidades