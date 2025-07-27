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

## 💻 Consulta SQL Utilizada (Anonimizada)

```sql
SELECT
    UnidadesVehiculo."Numero_VIN", UnidadesVehiculo."ID_Fabricante", UnidadesVehiculo."Matricula", UnidadesVehiculo."Estado_Entregada", UnidadesVehiculo."Estado_Facturada", UnidadesVehiculo."Fecha_Patentamiento",
    OperacionesVenta."ID_Operacion", OperacionesVenta."Fecha_Operacion",
    ModelosVehiculo."Descripcion_Operativa",
    FuentesOrigenModelo."Nombre_Origen",
    TiposTransaccion."Descripcion_Tipo",
    AsesoresComerciales."Nombre_Asesor", AsesoresComerciales."Rol_Jefe",
    PuntosDeVenta."Nombre_Punto",
    ClientesMaestro."Nombre_Completo", ClientesMaestro."Localidad", ClientesMaestro."Provincia", ClientesMaestro."Direccion", ClientesMaestro."Numero_Identificacion", ClientesMaestro."Fecha_Nacimiento",
    CategoriasVehiculo."Nombre_Categoria"
FROM
    { oj ((((((("UnidadesVehiculo" UnidadesVehiculo LEFT OUTER JOIN "ModelosVehiculo" ModelosVehiculo ON
    UnidadesVehiculo."ID_Modelo" = ModelosVehiculo."ID_Modelo")
    LEFT OUTER JOIN "OperacionesVenta" OperacionesVenta ON
    UnidadesVehiculo."ID_Operacion" = OperacionesVenta."ID_Operacion")
    LEFT OUTER JOIN "TiposTransaccion" TiposTransaccion ON
    OperacionesVenta."ID_Tipo_Transaccion" = TiposTransaccion."ID_Tipo")
    LEFT OUTER JOIN "DB_Principal"."dbo"."AsesoresComerciales" AsesoresComerciales ON
    OperacionesVenta."ID_Asesor" = AsesoresComerciales."ID_Asesor")
    LEFT OUTER JOIN "DB_Principal"."dbo"."PuntosDeVenta" PuntosDeVenta ON
    OperacionesVenta."ID_Punto_Venta" = PuntosDeVenta."ID_Punto")
    LEFT OUTER JOIN "ClientesMaestro" ClientesMaestro ON
    OperacionesVenta."ID_Cliente" = ClientesMaestro."ID_Cliente")
    LEFT OUTER JOIN "DB_Principal"."dbo"."CategoriasVehiculo" CategoriasVehiculo ON
    ModelosVehiculo."ID_Categoria" = CategoriasVehiculo."ID_Categoria")
    LEFT OUTER JOIN "FuentesOrigenModelo" FuentesOrigenModelo ON
    ModelosVehiculo."ID_Origen" = FuentesOrigenModelo."ID_Origen"}
```
---

## 📐Medidas DAX Utilizadas
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