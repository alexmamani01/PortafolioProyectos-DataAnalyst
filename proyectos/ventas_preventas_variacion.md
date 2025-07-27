# Proyecto: Informe de Boletos de Venta de Preventas y Variación de Ventas

**Rol:** Data Analyst Colaborador / Data Engineer 
**Contexto:** Este proyecto se desarrolló para un grupo automotriz, buscando una visión consolidada de ventas y tendencias.

---

## 📊 Descripción del Proyecto

Este proyecto se dividió en dos componentes principales:
1.  **Consolidación de Preventas:** Agrupamiento y análisis de datos de preventas de todas las marcas del grupo empresarial.
2.  **Análisis de Variación de Ventas:** Creación de un reporte que muestra la evolución y variación de ventas en años anteriores (periodo 2022-2024).

---

## 🎯 Objetivos de Negocio

* Proveer una visión unificada y estratégica del rendimiento de las preventas a nivel de grupo.
* Identificar tendencias y variaciones en las ventas a lo largo del tiempo.
* Apoyar la planificación estratégica y la asignación de recursos basada en el desempeño de ventas.

---

## 🛠️ Habilidades Técnicas y Metodología

* **Extracción y Consolidación de Datos:** Elaboración de consultas SQL complejas para extraer y combinar datos de diversas tablas (`Preventas`, `Unidades`, `Usados`, `Clientes`, etc.) a través de múltiples *joins*.
* **Análisis de Tendencias:** Aplicación de funciones de tiempo en DAX para comparar períodos y calcular variaciones porcentuales año contra año.
* **KPIs y Métricas de Negocio:** Desarrollo de medidas DAX para cuantificar el desempeño de ventas y la variación.
* **Herramientas:** SQL (para extracción y unión de datos), Power BI (para modelado y DAX).
---
 ## 📊 Impacto y Resultados
Este proyecto permitió a la gerencia del grupo evaluar el rendimiento general de las ventas de preventas, identificar tendencias de mercado y tomar decisiones estratégicas sobre inventario, campañas de marketing y objetivos de ventas. La capacidad de comparar el desempeño año tras año fue crucial para la planificación y el ajuste de la estrategia comercial.

---
## MEDIDAS DAX UTILIZADAS
    // Cantidad de Preventas del mes anterior (año anterior)
totalPVmes-1 = CALCULATE(DISTINCTCOUNT(TODAS[Numero]), DATEADD(Calendario[Date], -1, YEAR))

// Total de Preventas acumulado al día del mes del año anterior
totPVmesal dia = CALCULATE([Total PV], DATEADD(DATESMTD(Calendario[Date] <= TODAY()), -1, YEAR))

// Total de Preventas del período anterior (comparación año anterior)
TotAnt = IF(
    MONTH(MAX('calendario'[Date])) = MONTH(TODAY()),
    [totPVmesal dia],
    [totalPVmes-1]
)

// Valor a mostrar para la comparación de la métrica (si el mes actual ya pasó o es el actual)
VMuestro = IF(
    MONTH(MAX('calendario'[Date])) <= MONTH(TODAY()),
    [TotAnt]
)

// Porcentaje de variación a mostrar (si el mes actual ya pasó o es el actual)
%Muestro = IF(
    MONTH(MAX('calendario'[Date])) <= MONTH(TODAY()),
    [Tot%],
    BLANK()
)

// Porcentaje de variación total (Total PV - TotAnt) / TotAnt
Tot% = DIVIDE([Total PV] - [TotAnt], [TotAnt])

// --- Cálculos para Total Acumulado 2023 ---
// Preventas del año anterior acumuladas
anterior = CALCULATE([totalPVmes-1], Calendario[Mes] < MONTH(TODAY()))

// Suma de 'anterior' y 'totPVmesal dia' para un acumulado específico del año anterior
2anterior = [anterior] + [totPVmesal dia]

// Porcentaje de variación del acumulado
%delacumulado = DIVIDE([Total PV] - [2anterior], [2anterior])

---
## 💻 Consultas SQL Utilizadas (Anonimizadas)

**Para Operaciones de Venta (Pre-Operaciones):**
```sql
SELECT
    Operaciones."ID_Operacion", Operaciones."Fecha_Operacion", Operaciones."ID_Vehiculo_Usado", Operaciones."Precio_Venta", Operaciones."Estado_Anulada", Operaciones."ID_Modelo_Vehiculo", Operaciones."Monto_Financiacion",
    Compradores."Nombre_Completo" AS "NombreComprador",
    PuntosDeVenta."Nombre_Punto" AS "PuntoDeVenta",
    AsesoresVenta."Nombre_Asesor" AS "NombreAsesor", AsesoresVenta."Jefe_Directo", AsesoresVenta."Activo",
    VehiculosUsados."Matricula" AS "MatriculaVehiculoUsado", VehiculosUsados."Nombre_Vehiculo" AS "NombreVehiculoUsado",
    UnidadesInventario."Matricula" AS "MatriculaUnidad", UnidadesInventario."Fecha_Asignacion", UnidadesInventario."Fecha_Registro_Patente", UnidadesInventario."Tipo_Carroceria",
    DefinicionModelos."Descripcion_Operativa" AS "DescripcionModelo",
    SaldosTransaccion."Monto_Saldo_Total",
    TiposVenta."Descripcion_Tipo",
    DetalleUsados."Color_Vehiculo", DetalleUsados."Anio_Modelo", DetalleUsados."Matricula" AS "MatriculaUsado2", DetalleUsados."Precio_Toma", DetalleUsados."Marca_Vehiculo" AS "MarcaUsado2", DetalleUsados."ID_Modelo_Vehiculo" AS "ModeloUsado2",
    ClasificacionesVehiculos."Nombre_Clasificacion" AS "FamiliaModelo",
    BancosFinanciadores."Nombre_Banco" AS "BancoFinanciacion"
FROM
    { oj ((((((((((("OperacionesDeVenta" Operaciones LEFT OUTER JOIN "PuntosDeVenta" PuntosDeVenta ON Operaciones."ID_Punto_Venta" = PuntosDeVenta."ID_Punto")
    LEFT OUTER JOIN "AsesoresVenta" AsesoresVenta ON Operaciones."ID_Asesor" = AsesoresVenta."ID_Asesor")
    LEFT OUTER JOIN "VehiculosUsados" VehiculosUsados ON Operaciones."ID_Vehiculo_Usado" = VehiculosUsados."ID_Vehiculo")
    LEFT OUTER JOIN "UnidadesInventario" UnidadesInventario ON Operaciones."ID_Unidad_Inventario" = UnidadesInventario."ID_Unidad")
    LEFT OUTER JOIN "DefinicionModelos" DefinicionModelos ON Operaciones."ID_Modelo_Vehiculo" = DefinicionModelos."ID_Modelo")
    LEFT OUTER JOIN "SaldosTransaccion" SaldosTransaccion ON Operaciones."ID_Operacion" = SaldosTransaccion."ID_Referencia")
    LEFT OUTER JOIN "TiposVenta" TiposVenta ON Operaciones."ID_Tipo_Venta" = TiposVenta."ID_Tipo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."DetalleUsados" DetalleUsados ON Operaciones."ID_Operacion" = DetalleUsados."ID_Origen_PreVenta")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."Financiaciones" Financiaciones ON Operaciones."ID_Financiacion" = Financiaciones."ID_Financiacion")
    LEFT OUTER JOIN "Compradores" Compradores ON Operaciones."ID_Comprador" = Compradores."ID_Comprador")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."BancosFinanciadores" BancosFinanciadores ON Financiaciones."ID_Banco" = BancosFinanciadores."ID_Banco")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."ClasificacionesVehiculos" ClasificacionesVehiculos ON DefinicionModelos."ID_Clasificacion" = ClasificacionesVehiculos."ID_Clasificacion"}
WHERE
    Operaciones."Fecha_Operacion" >= {ts '2000-01-01 00:00:00.00'} AND
    Operaciones."Fecha_Operacion" < {ts '2030-11-10 00:00:00.00'};


Para Unidades de Inventario:

SELECT
    UnidadesInventario."ID_Pedido", UnidadesInventario."Fecha_Pedido", UnidadesInventario."Fecha_Recepcion", UnidadesInventario."Posicion_Inventario", UnidadesInventario."VIN", UnidadesInventario."ID_Fabrica", UnidadesInventario."Unidad_Activa", UnidadesInventario."Matricula", UnidadesInventario."Responsable_Unidad", UnidadesInventario."Estado_Unidad", UnidadesInventario."Fecha_Estado", UnidadesInventario."Numero_Factura", UnidadesInventario."Entregada", UnidadesInventario."Facturada", UnidadesInventario."Confirmada", UnidadesInventario."Factura_Compra", UnidadesInventario."Dia_Reserva", UnidadesInventario."Cliente_Reserva", UnidadesInventario."Usuario_Reserva", UnidadesInventario."Fecha_Vence_Reserva", UnidadesInventario."Fecha_Registro_Patente", UnidadesInventario."Tipo_Unidad", UnidadesInventario."Fecha_Orden_Embarque", UnidadesInventario."Fecha_Factura_Terminal", UnidadesInventario."Fecha_Factura",
    Operaciones."ID_Operacion" AS "ID_Operacion_PreVenta", Operaciones."Fecha_Operacion" AS "Fecha_Operacion_PreVenta",
    Lugares."Descripcion_Lugar" AS "DescripcionUbicacion",
    DefinicionModelos."Descripcion_Operativa" AS "DescripcionModelo",
    DetalleColores."Descripcion_Color",
    SolicitudesPlanAhorro."Titular_Solicitud",
    FuentesOrigen."Nombre_Fuente" AS "NombreOrigen",
    TiposVenta."Descripcion_Tipo",
    AsesoresVenta."Nombre_Asesor" AS "NombreAsesorPreventa", AsesoresVenta."Jefe_Directo",
    OtrosAsesores."Nombre_Asesor" AS "NombreAsesorPlanAhorro",
    PuntosDeVenta."Nombre_Punto" AS "NombreSucursalVenta",
    Compradores."Nombre_Completo" AS "NombreCliente", Compradores."Ciudad", Compradores."Provincia",
    ClasificacionesVehiculos."Nombre_Clasificacion" AS "FamiliaModelo"
FROM
    { oj ((((((((((("UnidadesInventario" UnidadesInventario LEFT OUTER JOIN "DefinicionModelos" DefinicionModelos ON UnidadesInventario."ID_Modelo_Vehiculo" = DefinicionModelos."ID_Modelo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."Lugares" Lugares ON UnidadesInventario."ID_Ubicacion" = Lugares."ID_Ubicacion")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."SolicitudesPlanAhorro" SolicitudesPlanAhorro ON UnidadesInventario."ID_Unidad" = SolicitudesPlanAhorro."ID_Unidad")
    LEFT OUTER JOIN "OperacionesDeVenta" Operaciones ON UnidadesInventario."ID_PreVenta" = Operaciones."ID_Operacion")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."DetalleColores" DetalleColores ON UnidadesInventario."ID_Color" = DetalleColores."ID_Color")
    LEFT OUTER JOIN "TiposVenta" TiposVenta ON Operaciones."ID_Tipo_Venta" = TiposVenta."ID_Tipo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."AsesoresVenta" AsesoresVenta ON Operaciones."ID_Asesor" = AsesoresVenta."ID_Asesor")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."AsesoresVenta" OtrosAsesores ON SolicitudesPlanAhorro."ID_Asesor" = OtrosAsesores."ID_Asesor")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."PuntosDeVenta" PuntosDeVenta ON Operaciones."ID_Punto_Venta" = PuntosDeVenta."ID_Punto")
    LEFT OUTER JOIN "Compradores" Compradores ON Operaciones."ID_Comprador" = Compradores."ID_Comprador")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."ClasificacionesVehiculos" ClasificacionesVehiculos ON DefinicionModelos."ID_Clasificacion" = ClasificacionesVehiculos."ID_Clasificacion")
    LEFT OUTER JOIN "FuentesOrigen" FuentesOrigen ON DefinicionModelos."ID_Origen" = FuentesOrigen."ID_Origen"}
WHERE
    UnidadesInventario."Fecha_Pedido" > {ts '2020-01-01 00:00:00.00'};


Para Vehículos Usados:

SQL

SELECT
    VehiculosUsados."Color_Vehiculo", VehiculosUsados."Anio_Modelo", VehiculosUsados."Matricula", VehiculosUsados."Precio_Toma", VehiculosUsados."Precio_Venta", VehiculosUsados."Fecha_Ingreso", VehiculosUsados."ID_Origen_PreVenta", VehiculosUsados."Fecha_Venta", VehiculosUsados."ID_PreVenta", VehiculosUsados."Kilometraje", VehiculosUsados."Marca_Vehiculo", VehiculosUsados."ID_Modelo_Vehiculo", VehiculosUsados."Estado_Vehiculo", VehiculosUsados."Nombre_Vehiculo",
    Lugares."Descripcion_Lugar",
    Peritajes."Monto_Peritaje",
    VariablesUnidad."Valor_Variable",
    Compradores."Nombre_Completo",
    AsesoresVenta."Nombre_Asesor",
    TiposVenta."Descripcion_Tipo",
    DefinicionVariables."Nombre_Variable"
FROM
    { oj ((((((("BaseDeDatosAux"."dbo"."VehiculosUsados" VehiculosUsados LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."Peritajes" Peritajes ON
    VehiculosUsados."ID_Vehiculo" = Peritajes."ID_Vehiculo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."VariablesUnidad" VariablesUnidad ON
    VehiculosUsados."ID_Vehiculo" = VariablesUnidad."ID_Vehiculo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."OperacionesDeVenta" Operaciones ON
    VehiculosUsados."ID_PreVenta" = Operaciones."ID_Operacion")
    LEFT OUTER JOIN "Lugares" Lugares ON
    VehiculosUsados."ID_Ubicacion" = Lugares."ID_Ubicacion")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."AsesoresVenta" AsesoresVenta ON
    Operaciones."ID_Asesor" = AsesoresVenta."ID_Asesor")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."TiposVenta" TiposVenta ON
    Operaciones."ID_Tipo_Venta" = TiposVenta."ID_Tipo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."DefinicionVariables" DefinicionVariables ON
    VariablesUnidad."ID_Variable" = DefinicionVariables."ID_Variable")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."Compradores" Compradores ON
    Operaciones."ID_Comprador" = Compradores."ID_Comprador"}
WHERE
    VehiculosUsados."Estado_Vehiculo" <> 'Desactivado';    


---
