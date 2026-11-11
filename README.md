# Práctica 2 - Visualización y análisis con Power BI (IBEX-35)

Repositorio en GitHub: https://github.com/OscarCollantesPenalba/Practica2

Este repositorio reúne todo el proyecto: el data lake de la Práctica 1 (código PySpark que genera la
gold) y el dashboard de Power BI de la Práctica 2, que **solo consume** la capa gold exportada a
Parquet (patrón MVC: Power BI no toca la lógica ni el dato crudo).

## Estructura de la Práctica 2

    data/export/gold/               capa gold en Parquet (un fichero por tabla), fuente de Power BI
        hecho_cotizacion/           tabla de hechos: una fila por (Ticker, Fecha)
        dim_empresa/                dimensión empresa: una fila por Ticker
        dim_fecha/                  dimensión fecha: una fila por día con cotización
    powerbi/                        fichero .pbix del dashboard
    informe/                        informe en PDF de la práctica
        capturas/                   imágenes usadas en el informe (modelo, gráficos, sectores)
    docs/practica1/                 enunciado y salida explicada de la Práctica 1

## Abrir el dashboard
1. Instalar Power BI Desktop (gratuito, Windows; Microsoft Store).
2. Abrir `powerbi/*.pbix`. Los datos van importados en el fichero, así que se ven sin hacer nada más.
3. Para actualizar los datos en otro ordenador: Transformar datos → Administrar parámetros →
   cambiar `RutaGold` por la ruta de `data/export/gold` en ese equipo → Cerrar y aplicar.

## Regenerar la gold (Práctica 1)
La gold ya va incluida en el repositorio. Para recalcularla con datos nuevos, ejecutar la Práctica 1
(ver abajo) y después actualizar el dashboard en Power BI.

