# Proyecto: Análisis de Postventa y Comportamiento de Servicio de Unidades

**Rol:** Data Analyst
**Contexto:** Este proyecto se enfocó en comprender el comportamiento de los clientes y unidades en el servicio de postventa de un grupo automotriz, distinguiendo entre diferentes tipos de órdenes y orígenes de clientes. El objetivo era optimizar la gestión del servicio y las estrategias de fidelización.

---

## 📊 Descripción del Proyecto

Este informe presenta un análisis detallado del servicio de postventa, segmentando clientes y unidades. Se distingue entre:
* Clientes que adquirieron vehículos dentro del grupo empresarial y aquellos con historial de servicio previo a la formación del grupo.
* Unidades 0km entregadas por el grupo que han utilizado el servicio de postventa versus aquellas que no.
* Análisis descriptivo de la antigüedad de los vehículos, la familia de modelos, el intervalo de tiempo entre la entrega y el primer servicio, y los kilometrajes registrados.

El reporte también analiza el total de órdenes de servicio, diferenciando entre clientes internos y externos al grupo, y el intervalo de meses entre servicios para un mismo cliente, así como la antigüedad general de los vehículos que ingresan al taller.

---

## 🎯 Objetivos de Negocio

* Identificar patrones de uso del servicio de postventa entre diferentes segmentos de clientes.
* Evaluar la retención de clientes 0km en el servicio de postventa del grupo.
* Comprender la frecuencia y el tipo de servicios realizados en función de la antigüedad y el kilometraje del vehículo.
* Optimizar la planificación de recursos del taller y las campañas de marketing postventa.
* Mejorar las estrategias de fidelización de clientes.

---

## 🛠️ Habilidades Técnicas y Metodología

* **Extracción y Consolidación de Datos:** Uso de SQL para extraer y combinar datos de diversas tablas de órdenes de servicio (`OrdenesServicio`, `UnidadesRegistro`), información de clientes (`ClientesMaestro`), y detalles de vehículos (`ModelosVehiculo`, `ClasificacionesVehiculo`).
* **Modelado de Datos:** Construcción de un modelo de datos robusto en Power BI, incluyendo tablas de hechos para `Ordenes` y `Unidades`, y tablas de dimensiones (`Provincias`, `Ubicaciones`, `Calendario`, `Marcas`, etc.).
* **Creación de Columnas Calculadas y Medidas Avanzadas (DAX/Power Query):** Desarrollo de lógicas para calcular la antigüedad del vehículo, rangos de odómetro, y especialmente, el intervalo entre servicios sucesivos y el tiempo hasta el primer servicio.
* **Segmentación y Filtrado Dinámico:** Implementación de filtros para diferenciar tipos de órdenes de servicio (Chapa, Pintura, Garantía, Service) y segmentar clientes por origen (interno/externo al grupo).
* **Visualización de Datos:** Diseño de paneles para presentar el "Detalle de Órdenes" y el "Detalle de Unidades sin Servicio", permitiendo un análisis comparativo y de seguimiento.
* **Herramientas:** SQL, Power BI (Power Query para columnas personalizadas y DAX para medidas), Excel para datos auxiliares.


## ✨ Impacto y Resultados
Este reporte de postventa es una herramienta fundamental para la gestión estratégica del servicio al cliente y la fidelización. Permitió al grupo:

Identificar clientes de alto valor y patrones de servicio.

Evaluar la efectividad de las garantías y los servicios incluidos.

Proactivamente contactar a clientes para servicios de mantenimiento basados en los intervalos calculados.

Optimizar la capacidad del taller al entender la demanda basada en la antigüedad y kilometraje de los vehículos.

Medir la retención de clientes 0km en el servicio oficial, informando estrategias de marketing y postventa para mejorar la lealtad.

## 💻 Consultas SQL Utilizadas (Anonimizadas)

**Query para Órdenes de Servicio:**
```sql
SELECT
    OrdenesServicio."ID_Orden", OrdenesServicio."Anio_Orden", OrdenesServicio."Nombre_Cliente_Orden", OrdenesServicio."Email_Cliente", OrdenesServicio."Valor_Odometro", OrdenesServicio."Direccion_Cliente", OrdenesServicio."Localidad_Cliente", OrdenesServicio."Provincia_Cliente", OrdenesServicio."Celular_Cliente", OrdenesServicio."Modelo_Vehiculo_Orden", OrdenesServicio."Matricula_Vehiculo", OrdenesServicio."Fecha_Apertura",
    TiposServicio."Descripcion_Tipo_Servicio", TiposServicio."Abreviatura_Tipo",
    ClientesMaestro."ID_Identificacion", ClientesMaestro."Fecha_Nacimiento"
FROM
    { oj (("DB_Principal"."dbo"."OrdenesServicio" OrdenesServicio INNER JOIN "DB_Principal"."dbo"."TiposServicio" TiposServicio ON
    OrdenesServicio."ID_Orden" = TiposServicio."ID_Orden_Servicio")
    LEFT OUTER JOIN "DB_Principal"."dbo"."UnidadesRegistro" UnidadesRegistro ON
    OrdenesServicio."ID_Unidad_Fabricante" = UnidadesRegistro."ID_Fabricante")
    LEFT OUTER JOIN "DB_Principal"."dbo"."ClientesMaestro" ClientesMaestro ON
    TiposServicio."ID_Cliente" = ClientesMaestro."ID_Cliente"}
WHERE TiposServicio."Descripcion_Tipo_Servicio" IN (
    'CHAPA CLIENTE', 'CHAPA Y PINTURA CLIENTE', 'CLIENTE', 'CLIENTE SAN FCO', 'CLIENTE SERVICE',
    'COMPAÑIAS', 'GARANTIA', 'GARANTIA ', 'GARANTIA  SAN FRANCISCO', 'GARANTIA BONIFICADA',
    'GARANTIA CORDOBA', 'GARANTIA ROSARIO', 'GARANTÍA CAMPAÑAS', 'GARANTÍA MECÁNICA',
    'PINTURA CLIENTE', 'SERVICE'
);

Query para Unidades de Registro (Unidades):

SELECT
    UnidadesRegistro."ID_Responsable",
    UnidadesRegistro."Modelo_Vehiculo",
    UnidadesRegistro."Numero_VIN",
    UnidadesRegistro."Matricula",
    UnidadesRegistro."Fecha_Salida",
    UnidadesRegistro."Estado_Entregada",
    YEAR(UnidadesRegistro."Fecha_Registro_Patente") AS "Anio_Registro_Patente",
    ClientesMaestro."Direccion_Cliente", ClientesMaestro."Email_Cliente", ClientesMaestro."Celular_Cliente",
    ClientesMaestro."Localidad_Cliente",
    ClientesMaestro."Nombre_Completo" AS "NombreCliente",
    YEAR(ClientesMaestro."Fecha_Nacimiento") AS "Anio_Nacimiento",
    ClasificacionesVehiculo."Nombre_Clasificacion"
FROM
    { oj ((("DB_Principal"."dbo"."UnidadesRegistro" UnidadesRegistro LEFT OUTER JOIN "DB_Principal"."dbo"."ModelosVehiculo" ModelosVehiculo ON
    UnidadesRegistro."Modelo_Vehiculo" = ModelosVehiculo."ID_Modelo")
    LEFT OUTER JOIN "DB_Principal"."dbo"."OperacionesVenta" OperacionesVenta ON
    UnidadesRegistro."ID_Operacion_Venta" = OperacionesVenta."ID_Operacion")
    LEFT OUTER JOIN "DB_Principal"."dbo"."ClientesMaestro" ClientesMaestro ON
    OperacionesVenta."ID_Cliente" = ClientesMaestro."ID_Fiscal")
    LEFT OUTER JOIN "DB_Principal"."dbo"."ClasificacionesVehiculo" ClasificacionesVehiculo ON
    ModelosVehiculo."ID_Clasificacion" = ClasificacionesVehiculo."ID_Clasificacion"}
WHERE UnidadesRegistro."Estado_Entregada" = 1;
```

## 📅 Tablas de Dimensiones y Hechos
Tablas de Hechos:

OrdenesServicio: Contiene los detalles de cada orden de servicio (fecha, odómetro, tipo de servicio, etc.).

UnidadesRegistro: Registra información de las unidades (fecha de salida, patente, estado de entrega).

Tablas de Dimensiones:

Provincias

Ubicaciones

Calendario: Tabla de fechas generada para análisis de tiempo.

Marcas

ClientesMaestro

ModelosVehiculo

ClasificacionesVehiculo

## Definición de Tabla de Calendario:
CALENDARIO =
ADDCOLUMNS (
    CALENDAR (DATE(2023,1,1), DATE(2024,12,31)),
    "Id Fecha", FORMAT ( [Date], "YYYYMMDD" ),
    "Año", YEAR ( [Date] ),
    "Mes", FORMAT ( [Date], "MM" ),
    "Nombre Mes", FORMAT ( [Date], "mmmm" ),
    "Día Semana número", WEEKDAY ( [Date] ),
    "Día Semana ", FORMAT ( [Date], "dddd" ),
    "Trimestre", "Q" & FORMAT ( [Date], "Q" ),
    "Año/trimestre", FORMAT ( [Date], "YYYY" ) & "/Q" & FORMAT ( [Date], "Q")
)

## 📈 Medidas DAX y Columnas Personalizadas (Power Query)
// Medida: Intervalo entre servicios (en meses)
// Calcula el tiempo transcurrido desde el servicio anterior de la misma unidad.
Intervalo entre servicios (meses) =
VAR vUltimoServicioAnterior =
    CALCULATE(
        MAX(ORDENES[Fecha_Apertura]), // Asumiendo 'ORDENES' es la tabla consolidada de órdenes y 'Fecha_Apertura' es la fecha del servicio
        FILTER(
            ALL(ORDENES),
            ORDENES[Matricula_Vehiculo] = EARLIER(ORDENES[Matricula_Vehiculo]) && // Para la misma matrícula
            ORDENES[Fecha_Apertura] < EARLIER(ORDENES[Fecha_Apertura]) // Servicio anterior
        )
    )
VAR vIntervaloEnMeses = DATEDIFF(vUltimoServicioAnterior, ORDENES[Fecha_Apertura], MONTH)
RETURN
    IF (ISBLANK(vIntervaloEnMeses), 0, vIntervaloEnMeses) // Si es el primer servicio, retorna 0 meses

prom dif service: Promedio del Intervalo entre servicios (meses):
prom dif service = AVERAGE(ORDENES[Intervalo entre servicios (meses)])


## Columnas Personalizadas (Power Query - Ejemplos de Lógica):
En la Tabla de Órdenes (OrdenesServicio):

Año de Nacimiento: Date.Year ([Fecha_Nacimiento])

Fecha Hoy: DateTime.LocalNow()

Año Hoy: Date.Year ([Fecha Hoy])

Rango Odometro: Clasificación del valor del odómetro en rangos predefinidos (e.g., "1000", "5000", "10000", etc.).


// Ejemplo de lógica para 'Rango Odometro'
each if [Valor_Odometro]= null then null
else if [Valor_Odometro] <=1000 then "1000km"
else if [Valor_Odometro] <=5000 then "5000km"
// ... y así sucesivamente para los demás rangos
else if [Valor_Odometro] <=300000 then "300000km"
else "Otros KM"
Filtro Relevancia Service: each if [Fecha_Salida_Unidad] = null then null else if [Fecha_Apertura] <= [Fecha_Salida_Unidad] then "No Relevante (Pre-entrega)" else "Relevante"

Antiguedad de Vehiculo: Clasificación de la antigüedad del vehículo desde su año de fabricación hasta el año actual en rangos (ej. "1 año", "2 años", "5 años", "10 años", "15 años", "20+ años").

Filtro-cliente: each if [Unidad_Entregada_0km] = true then "0km Grupo" else "No 0km Grupo"

En la Tabla de Unidades (UnidadesRegistro):

Taller (Estado Service): each if [Fecha_Ultimo_Service] <> null then "Con Servicio en Taller" else "Sin Servicio en Taller"

Rango Odométrico (Unidad): Similar al anterior, pero aplicado a los datos de la unidad.

Filtro Pre-entrega: each if [Fecha_Ultimo_Service] is null then null else if [Fecha_Salida] >= [Fecha_Ultimo_Service] then "Service Pre-entrega" else "Service Post-Venta" (Se usa para identificar servicios realizados antes de la entrega del vehículo, que no son "relevantes" para el seguimiento de la postventa regular).

Año de Salida: Date.Year ([Fecha_Salida])

Fecha Hoy (Unidad): DateTime.LocalNow()

Año Hoy (Unidad): Date.Year([Fecha Hoy (Unidad)])
