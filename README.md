# Práctica 2 – Visualización y análisis del IBEX-35 con Power BI

Repositorio: https://github.com/OscarCollantesPenalba/Practica2

Dashboard de Power BI sobre las 35 empresas del IBEX-35 que cruza su cotización diaria con un índice de
sentimiento por sector. Power BI no transforma el dato crudo: **solo consume la capa gold exportada a
Parquet** en `data/export/gold/`.

## Estructura

    data/export/gold/          capa gold en Parquet (un fichero por tabla), fuente del dashboard
        hecho_cotizacion/      tabla de hechos
        dim_empresa/           dimensión empresa
        dim_fecha/             dimensión fecha
    powerbi/
        practica2.pbix         informe de Power BI
        IBEX_Oscuro_Azul_Naranja.json   tema del informe
    informe/
        Informe_Practica2.pdf  informe de la práctica (Ej1 a Ej9)
        capturas/              figuras usadas en el informe
    docs/                      enunciados

## Datos

| Tabla | Filas | Grano | Columnas principales |
|---|---|---|---|
| `hecho_cotizacion` (Hechos) | 18.865 | una fila por empresa y sesión | Fecha, Ticker, Open, High, Low, Close, Volume, Dividends, Stock_Splits, Sentimiento, Variacion_Diaria_Pct, Rango_Intradia_Pct, Variacion_Acumulada_Pct, Media_Movil_20 |
| `dim_empresa` (Empresa) | 35 | una fila por empresa | Ticker, Nombre, Sector, Sector_ES, Industria, Pais, Capitalizacion, Beta, Recomendacion_Media, Num_Analistas, Precio_Objetivo |
| `dim_fecha` (Fecha) | 539 | una fila por sesión | Fecha, Anio, Trimestre, Mes, Semana |

Periodo: del 01/10/2024 al 10/11/2026. El sentimiento (escala 0–100) llega hasta el 30/09/2026.

## Modelo

Modelo en estrella en modo Import:

- `Empresa[Ticker]` 1 → * `Hechos[Ticker]`
- `Fecha[Fecha]` 1 → * `Hechos[Fecha]`

Ambas relaciones con filtro en una sola dirección (de la dimensión a los hechos).

Elementos DAX creados en Power BI:

| Nombre | Tipo | Fórmula |
|---|---|---|
| Num Empresas | Medida | `DISTINCTCOUNT(Hechos[Ticker])` |
| Num Sesiones | Medida | `DISTINCTCOUNT(Hechos[Fecha])` |
| Retorno Medio Diario | Medida | `AVERAGE(Hechos[Variacion_Diaria_Pct])` |
| Sentimiento Medio | Medida | `AVERAGE(Hechos[Sentimiento])` |
| Volatilidad Diaria | Medida | `STDEV.S(Hechos[Variacion_Diaria_Pct])` |
| Capitalizacion Total | Medida | `SUM(Empresa[Capitalizacion])` |
| Variacion Acumulada | Medida | `CALCULATE(AVERAGE(Hechos[Variacion_Acumulada_Pct]), LASTDATE(Hechos[Fecha]))` |
| Var Acum Seleccion | Medida | `AVERAGE(Hechos[Variacion_Acumulada_Pct])` |
| Var Acum IBEX | Medida | `CALCULATE(AVERAGE(Hechos[Variacion_Acumulada_Pct]), REMOVEFILTERS(Empresa))` |
| Titulo Dashboard | Medida | Título dinámico según la empresa o el sector seleccionados |
| DiaSemana | Columna (Fecha) | `FORMAT(Fecha[Fecha], "dddd")` |
| NumDiaSemana | Columna (Fecha) | `WEEKDAY(Fecha[Fecha], 2)` |
| AnioMes | Columna (Fecha) | `FORMAT(Fecha[Fecha], "yyyy-mm")` |

## Páginas del informe

| Página | Ejercicios | Contenido |
|---|---|---|
| Panorama | Ej3, Ej4 | Tarjetas KPI y evolución del precio de cierre con segmentador por empresa |
| Efecto Viernes | Ej5 | Retorno medio por día de la semana |
| Retorno por Sector | Ej6 | Ranking de retorno medio diario por sector |
| Dashboard | Ej7, Ej8, Ej9 | Cuadro de mando por sector y empresa: KPIs, variación acumulada frente al IBEX-35, volumen, precio y media móvil, sentimiento frente a retorno, retorno por mes y dividendos por trimestre |

## Abrir y actualizar el dashboard

1. Abrir `powerbi/practica2.pbix` con Power BI Desktop (gratuito, Windows). Los datos van importados en el
   fichero, así que el informe se ve sin hacer nada más.
2. Para recargar los datos en otro ordenador: *Transformar datos → Configuración de origen de datos →
   Cambiar origen* y apuntar cada consulta a su carpeta dentro de `data/export/gold/`. Después,
   *Cerrar y aplicar*.
3. El tema se puede volver a aplicar con *Diseño → Importar tema* →
   `powerbi/IBEX_Oscuro_Azul_Naranja.json`.

## Uso de IA

Se ha utilizado Claude (Anthropic) como apoyo para organizar los pasos en Power BI, proponer la
distribución del dashboard, generar el archivo del tema y redactar borradores del informe. El trabajo en
Power BI y las capturas son propios.