# Proyecto: Gestión de Canal Secundario

**Rol:** Data Analyst Colaborador / Data Engineer
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

## 🛠️ Impacto y Resultados
El informe resultante permitió al equipo de ventas y gerencia tomar decisiones informadas sobre cómo enfocar sus esfuerzos hacia este canal, optimizando la asignación de recursos y mejorando la gestión de stock de vehículos usados. Este proyecto transformó datos dispersos en inteligencia de mercado accionable.

## 💻 Consulta SQL Utilizada (Anonimizada)

```sql
SELECT
    Operaciones."ID_Operacion", Operaciones."Fecha_Operacion", Operaciones."ID_Vehiculo_Usado", Operaciones."Precio_Venta", Operaciones."Estado_Anulada", Operaciones."ID_Modelo_Vehiculo", Operaciones."Monto_Financiacion",
    Compradores."Nombre_Completo", Compradores."Direccion", Compradores."Ciudad", Compradores."Provincia", Compradores."ID_Fiscal", Compradores."Numero_Identificacion", Compradores."Fecha_Nacimiento",
    PuntosDeVenta."Nombre_Punto",
    AsesoresVenta."Nombre_Asesor", AsesoresVenta."Jefe_Directo", AsesoresVenta."Activo",
    VehiculosUsados."Matricula", VehiculosUsados."Nombre_Vehiculo", VehiculosUsados."Marca_Vehiculo",
    UnidadesInventario."Matricula", UnidadesInventario."Fecha_Asignacion", UnidadesInventario."Facturada", UnidadesInventario."Fecha_Registro_Patente", UnidadesInventario."Tipo_Carroceria",
    DefinicionModelos."Descripcion_Operativa",
    SaldosTransaccion."Monto_Saldo_Total",
    TiposVenta."Descripcion_Tipo",
    DetalleUsados."Color_Vehiculo", DetalleUsados."Anio_Modelo", DetalleUsados."Matricula" AS "MatriculaUsado2", DetalleUsados."Precio_Toma", DetalleUsados."Marca_Vehiculo" AS "MarcaUsado2", DetalleUsados."ID_Modelo_Vehiculo" AS "ModeloUsado2",
    LugaresEntrega."Descripcion_Lugar", LugaresEntrega."Direccion", LugaresEntrega."Ciudad", LugaresEntrega."Codigo_Postal", LugaresEntrega."Provincia",
    BancosFinanciadores."Nombre_Banco",
    CondicionesFiscales."Nombre_Condicion",
    ClasificacionesVehiculos."Nombre_Clasificacion"
FROM
    { oj ((((((((((((("OperacionesDeVenta" Operaciones LEFT OUTER JOIN "PuntosDeVenta" PuntosDeVenta ON
    Operaciones."ID_Punto_Venta" = PuntosDeVenta."ID_Punto")
    LEFT OUTER JOIN "AsesoresVenta" AsesoresVenta ON
    Operaciones."ID_Asesor" = AsesoresVenta."ID_Asesor")
    LEFT OUTER JOIN "VehiculosUsados" VehiculosUsados ON
    Operaciones."ID_Vehiculo_Usado" = VehiculosUsados."ID_Vehiculo")
    LEFT OUTER JOIN "UnidadesInventario" UnidadesInventario ON
    Operaciones."ID_Unidad_Inventario" = UnidadesInventario."ID_Unidad")
    LEFT OUTER JOIN "DefinicionModelos" DefinicionModelos ON
    Operaciones."ID_Modelo_Vehiculo" = DefinicionModelos."ID_Modelo")
    LEFT OUTER JOIN "SaldosTransaccion" SaldosTransaccion ON
    Operaciones."ID_Operacion" = SaldosTransaccion."ID_Referencia")
    LEFT OUTER JOIN "TiposVenta" TiposVenta ON
    Operaciones."ID_Tipo_Venta" = TiposVenta."ID_Tipo")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."DetalleUsados" DetalleUsados ON
    Operaciones."ID_Operacion" = DetalleUsados."ID_Origen_PreVenta")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."Financiaciones" Financiaciones ON
    Operaciones."ID_Financiacion" = Financiaciones."ID_Financiacion")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."LugaresEntrega" LugaresEntrega ON
    Operaciones."ID_Ubicacion_Entrega" = LugaresEntrega."ID_Ubicacion")
    LEFT OUTER JOIN "Compradores" Compradores ON
    Operaciones."ID_Comprador" = Compradores."ID_Comprador")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."CondicionesFiscales" CondicionesFiscales ON
    Compradores."ID_Tipo_Fiscal" = CondicionesFiscales."ID_Tipo_Fiscal")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."ClasificacionesVehiculos" ClasificacionesVehiculos ON
    DefinicionModelos."ID_Clasificacion" = ClasificacionesVehiculos."ID_Clasificacion")
    LEFT OUTER JOIN "BaseDeDatosAux"."dbo"."BancosFinanciadores" BancosFinanciadores ON
    Financiaciones."ID_Banco" = BancosFinanciadores."ID_Banco"}
WHERE
    Operaciones."Fecha_Operacion" >= {ts '2022-01-01 00:00:00.00'} AND
    Operaciones."Fecha_Operacion" < {ts '2030-11-10 00:00:00.00'} AND
    Operaciones."Estado_Anulada" = 0;

