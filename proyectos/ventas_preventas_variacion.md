---

#### **`proyectos/ventas_preventas_variacion.md`**

```markdown
# Proyecto: Informe de Boletos de Venta de Preventas y Variación de Ventas

**Rol:** Data Analyst / Data Engineer Colaborador
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

## 💻 Consultas SQL Utilizadas (Extractos)

**Para Preventas:**
```sql
SELECT
    Preventas."Numero", Preventas."Fecha", Preventas."UsadoID", Preventas."PrecioVenta", Preventas."Anulada", Preventas."Modelo", Preventas."Financiacion_Importe",
    Clientes."Nombre" AS "NombreCliente",
    SucursalesDeventa."Nombre" AS "SucursalVenta",
    Vendedores."Nombre" AS "NombreVendedor", Vendedores."Jefe", Vendedores."Activo",
    Usados."Patente" AS "PatenteUsado", Usados."Nombre" AS "NombreUsado",
    Unidades."Patente" AS "PatenteUnidad", Unidades."FechaAsignacion", Unidades."FechaPatentamiento", Unidades."Carroceria",
    Modelos."DescripcionOperativa" AS "ModeloOperativo",
    SaldosPorReferencia."SumaDeSumaDeSaldo",
    TipoVenta."Descripcion" AS "TipoDeVenta",
    Usados_1."Color", Usados_1."Anio", Usados_1."Patente" AS "PatenteUsado2", Usados_1."PrecioDeToma", Usados_1."Marca" AS "MarcaUsado2", Usados_1."Modelo" AS "ModeloUsado2",
    Familias."Nombre" AS "FamiliaModelo",
    EntidadesBancarias."Nombre" AS "BancoFinanciacion"
FROM
    { oj ((((((((((("Preventas" Preventas LEFT OUTER JOIN "SucursalesDeventa" SucursalesDeventa ON Preventas."Sucursal" = SucursalesDeventa."Numero")
    LEFT OUTER JOIN "Vendedores" Vendedores ON Preventas."VendedorID" = Vendedores."VendedorId")
    LEFT OUTER JOIN "Usados" Usados ON Preventas."UsadoID" = Usados."UsadoID")
    LEFT OUTER JOIN "Unidades" Unidades ON Preventas."UnidadID" = Unidades."UnidadID")
    LEFT OUTER JOIN "Modelos" Modelos ON Preventas."Modelo" = Modelos."Modelo")
    LEFT OUTER JOIN "SaldosPorReferencia" SaldosPorReferencia ON Preventas."Numero" = SaldosPorReferencia."Referencia")
    LEFT OUTER JOIN "TipoVenta" TipoVenta ON Preventas."TipoDeVentaID" = TipoVenta."TipoVentaID")
    LEFT OUTER JOIN "SAI"."dbo"."Usados" Usados_1 ON Preventas."Numero" = Usados_1."PreVentaOrigen")
    LEFT OUTER JOIN "SAI"."dbo"."Financiaciones" Financiaciones ON Preventas."FinanciacionID" = Financiaciones."FinanciacionID")
    LEFT OUTER JOIN "Clientes" Clientes ON Preventas."Cliente" = Clientes."Codigo")
    LEFT OUTER JOIN "SAI"."dbo"."EntidadesBancarias" EntidadesBancarias ON Financiaciones."BancoID" = EntidadesBancarias."BancoID")
    LEFT OUTER JOIN "SAI"."dbo"."Familias" Familias ON Modelos."Familia" = Familias."FamiliaID"}
WHERE
    Preventas."Fecha" >= {ts '2000-01-01 00:00:00.00'} AND
    Preventas."Fecha" < {ts '2030-11-10 00:00:00.00'};


    MEDIDAS DAX UTILIZADAS
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