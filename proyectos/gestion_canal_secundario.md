# Proyecto: Gestión de Canal Secundario

**Rol:** Data Analyst / Data Engineer Colaborador
**Contexto:** Este proyecto se desarrolló para un grupo automotriz líder.

---

## 📊 Descripción del Proyecto

Este proyecto se centró en identificar y analizar a los clientes que compraban vehículos usados con fines de reventa, conocidos como el "canal secundario". El objetivo era obtener una visión clara de este segmento para entender sus patrones de compra, optimizar el inventario de vehículos usados y, potencialmente, desarrollar estrategias comerciales específicas.

---

## 🎯 Objetivos de Negocio

* Identificar y segmentar a los clientes dedicados a la reventa de vehículos usados.
* Comprender el comportamiento de compra de este canal (frecuencia, tipo de vehículos).
* Optimizar la rotación del inventario de vehículos usados.
* Informar la creación de estrategias de ventas y marketing dirigidas.

---

## 🛠️ Habilidades Técnicas y Metodología

* **Extracción y Transformación de Datos (ETL):** Diseño y ejecución de consultas SQL complejas para integrar datos de múltiples fuentes (base de datos de clientes, preventas, inventario de usados y un archivo Excel con información de revendedores).
* **Modelado de Datos:** Creación de una vista unificada y consolidada para el análisis.
* **Análisis de Datos:** Identificación de patrones de compra y perfiles de clientes de reventa.
* **Herramientas:** SQL (para extracción y transformación).

## 💻 Consulta SQL Utilizada

```sql
SELECT
    Preventas."Numero", Preventas."Fecha", Preventas."UsadoID", Preventas."PrecioVenta", Preventas."Anulada", Preventas."Modelo", Preventas."Financiacion_Importe",
    Clientes."Nombre", Clientes."Direccion", Clientes."Localidad", Clientes."Provincia", Clientes."CUIT_CUIL", Clientes."DNI", Clientes."FechaDeNacim",
    SucursalesDeventa."Nombre" AS "SucursalVenta",
    Vendedores."Nombre" AS "NombreVendedor", Vendedores."Jefe", Vendedores."Activo",
    Usados."Patente" AS "PatenteUsado", Usados."Nombre" AS "NombreUsado", Usados."Marca" AS "MarcaUsado",
    Unidades."Patente" AS "PatenteUnidad", Unidades."FechaAsignacion", Unidades."Facturada", Unidades."FechaPatentamiento", Unidades."Carroceria",
    Modelos."DescripcionOperativa" AS "ModeloOperativo",
    SaldosPorReferencia."SumaDeSumaDeSaldo",
    TipoVenta."Descripcion" AS "TipoDeVenta",
    Usados_1."Color", Usados_1."Anio", Usados_1."Patente" AS "PatenteUsado2", Usados_1."PrecioDeToma", Usados_1."Marca" AS "MarcaUsado2", Usados_1."Modelo" AS "ModeloUsado2",
    Ubicaciones."Descripcion" AS "UbicacionEntrega", Ubicaciones."Direccion" AS "DireccionEntrega", Ubicaciones."Localidad" AS "LocalidadEntrega", Ubicaciones."CodPost" AS "CodPostEntrega", Ubicaciones."Provincia" AS "ProvinciaEntrega",
    EntidadesBancarias."Nombre" AS "BancoFinanciacion",
    CondicionesIVA."Nombre" AS "CondicionIVACliente",
    Familias."Nombre" AS "FamiliaModelo"
FROM
    { oj ((((((((((((("Preventas" Preventas LEFT OUTER JOIN "SucursalesDeventa" SucursalesDeventa ON Preventas."Sucursal" = SucursalesDeventa."Numero")
    LEFT OUTER JOIN "Vendedores" Vendedores ON Preventas."VendedorID" = Vendedores."VendedorId")
    LEFT OUTER JOIN "Usados" Usados ON Preventas."UsadoID" = Usados."UsadoID")
    LEFT OUTER JOIN "Unidades" Unidades ON Preventas."UnidadID" = Unidades."UnidadID")
    LEFT OUTER JOIN "Modelos" Modelos ON Preventas."Modelo" = Modelos."Modelo")
    LEFT OUTER JOIN "SaldosPorReferencia" SaldosPorReferencia ON Preventas."Numero" = SaldosPorReferencia."Referencia")
    LEFT OUTER JOIN "TipoVenta" TipoVenta ON Preventas."TipoDeVentaID" = TipoVenta."TipoVentaID")
    LEFT OUTER JOIN "SAI"."dbo"."Usados" Usados_1 ON Preventas."Numero" = Usados_1."PreVentaOrigen")
    LEFT OUTER JOIN "SAI"."dbo"."Financiaciones" Financiaciones ON Preventas."FinanciacionID" = Financiaciones."FinanciacionID")
    LEFT OUTER JOIN "SAI"."dbo"."Ubicaciones" Ubicaciones ON Preventas."UbicacionEntregaID" = Ubicaciones."UbicacionID")
    LEFT OUTER JOIN "Clientes" Clientes ON Preventas."Cliente" = Clientes."Codigo")
    LEFT OUTER JOIN "SAI"."dbo"."CondicionesIVA" CondicionesIVA ON Clientes."TipoDeIVA" = CondicionesIVA."TipoDeIVAID")
    LEFT OUTER JOIN "SAI"."dbo"."Familias" Familias ON Modelos."Familia" = Familias."FamiliaID")
    LEFT OUTER JOIN "SAI"."dbo"."EntidadesBancarias" EntidadesBancarias ON Financiaciones."BancoID" = EntidadesBancarias."BancoID"}
WHERE
    Preventas."Fecha" >= {ts '2022-01-01 00:00:00.00'} AND
    Preventas."Fecha" < {ts '2030-11-10 00:00:00.00'} AND
    Preventas."Anulada" = 0;