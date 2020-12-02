---
title: Visualisaties
description: Gebruik Azure Synapse-notebooks om uw gegevens te visualiseren
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 73b18d15ad054f1c485d6f61cdefe54993148bc4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450542"
---
# <a name="visualize-data"></a>Gegevens visualiseren
Azure Synapse is een geïntegreerde analyse service waarmee tijd kan worden versneld, in data warehouses en big data Analytics-systemen. Gegevens visualisatie is een belang rijk onderdeel waarmee u inzicht kunt krijgen in uw gegevens. Het helpt grote en kleine gegevens gemakkelijker te maken voor mensen. Daarnaast is het gemakkelijker om patronen, trends en uitschieters in groepen gegevens te detecteren. 

Wanneer u Apache Spark in azure Synapse Analytics gebruikt, zijn er verschillende ingebouwde opties beschikbaar om u te helpen uw gegevens te visualiseren, zoals Synapse-notitieblok diagram opties, toegang tot populaire open-source bibliotheken en integratie met Synapse SQL en Power BI.

## <a name="notebook-chart-options"></a>Opties voor notebook grafiek
Wanneer u een Azure Synapse-notebook gebruikt, kunt u de weer gave van uw tabellaire resultaten omzetten in een aangepast diagram met behulp van grafiek opties. Hier kunt u uw gegevens visualiseren zonder dat u code hoeft te schrijven. 

### <a name="displaydf-function"></a>de functie display (DF)
```display```Met de functie kunt u SQL-query's en Apache Spark dataframes en rdd's in Rich data-visualisaties omzetten. De ```display``` functie kan worden gebruikt op dataframes of rdd's die zijn gemaakt in PySpark, scala, Java en .net.

Voor toegang tot de grafiek opties:
1. De uitvoer van ```%%sql``` Magic-opdrachten wordt standaard weer gegeven in de gerenderde tabel weergave. U kunt ook ```display(df)``` een rdd-functie (DataFrames of robuuste gedistribueerde gegevens sets) aanroepen om de gerenderde tabel weergave te maken. 
   
2. Wanneer u een gerenderde tabel weergave hebt, gaat u naar de grafiek weergave.
   ![ingebouwde grafieken](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. U kunt uw visualisatie nu aanpassen door de volgende waarden op te geven:
   | Configuratie | Beschrijving |
   |--|--| 
   | Grafiektype | De ```display``` functie ondersteunt een breed scala aan grafiek typen, waaronder staaf diagrammen, spreidings grafieken, lijn grafieken en meer |
   | Sleutel | Geef het bereik van de waarden voor de x-as op|
   | Waarde | Geef het bereik van waarden voor de waarden van de y-as op |
   | Reeks groep | Wordt gebruikt om de groepen voor de aggregatie te bepalen | 
   | Aggregatie | Methode voor het samen voegen van gegevens in uw visualisatie| 
   
   
    > [!NOTE]
    > De ```display(df)``` functie gebruikt standaard alleen de eerste 1000 rijen van de gegevens om de grafieken weer te geven. Controleer de **aggregatie over alle resultaten** en klik op de knop **Toep assen** . u gaat de grafiek genereren van de hele gegevensset. Een Spark-taak wordt geactiveerd wanneer de grafiek instelling wordt gewijzigd. Houd er rekening mee dat het enkele minuten kan duren voordat de berekening is voltooid en de grafiek wordt weer gegeven.
   
4. Als u klaar bent, kunt u de uiteindelijke visualisatie bekijken en ermee werken.

### <a name="displaydf-statistic-details"></a>statistische gegevens weer geven (DF)
U kunt gebruiken <code>display(df, summary = true)</code> om de statistische samen vatting van een gegeven Apache Spark data frame te controleren die de kolom naam, het kolom Type, de unieke waarden en ontbrekende waarden voor elke kolom bevatten. U kunt ook selecteren op specifieke kolom om de minimale waarde, de maximum waarde, de gemiddelde waarde en de standaard afwijking weer te geven.
    ![ingebouwde grafieken-samen vatting](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (DF) optie
Azure Synapse Analytics-notebooks ondersteunen HTML-afbeeldingen met behulp van de ```displayHTML``` functie.

De volgende afbeelding is een voor beeld van het maken van visualisaties met behulp van [D3.js](https://d3js.org/).

   ![D3-js-voor beeld](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Voer de volgende code uit om de bovenstaande visualisatie te maken.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Populaire bibliotheken
Wanneer het gaat om gegevens visualisatie, biedt python meerdere Graph-bibliotheken die in veel verschillende functies zijn verpakt. Standaard bevat elke Apache Spark groep in azure Synapse Analytics een set met gehoste en populaire open-source bibliotheken. U kunt ook extra bibliotheken & versies toevoegen of beheren met behulp van de Azure Synapse Analytics-bibliotheek beheer mogelijkheden. 

### <a name="bokeh"></a>Bokeh
U kunt HTML-of interactieve Bibliotheken, zoals **bokeh**, weer geven met behulp van de ```displayHTML(df)``` . 

De volgende afbeelding is een voor beeld van het uitzetten van glyphs over een kaart met behulp van **bokeh**.

   ![bokeh-voor beeld](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Voer de volgende voorbeeld code uit om de bovenstaande afbeelding te tekenen.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
U kunt standaard uitzet bibliotheken weer geven, zoals matplotlib, met behulp van de ingebouwde rendering-functies voor elke bibliotheek.

De volgende afbeelding is een voor beeld van het maken van een staaf diagram met behulp van **matplotlib**.
   ![Voor beeld van lijn diagram.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Voer de volgende voorbeeld code uit om de bovenstaande afbeelding te tekenen.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>Extra bibliotheken 
Naast deze bibliotheken bevat de Azure Synapse Analytics-runtime ook de volgende set bibliotheken die vaak worden gebruikt voor gegevens visualisatie:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

U kunt de Azure Synapse Analytics runtime- [documentatie](./spark/../apache-spark-version-support.md) raadplegen voor de meest recente informatie over de beschik bare bibliotheken en versies.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Verbinding maken met Power BI met behulp van Apache Spark & SQL op aanvraag
Azure Synapse Analytics integreert diep met Power BI zodat data engineers analyse oplossingen kunnen bouwen.

Met Azure Synapse Analytics kunnen de verschillende reken kundige engines voor werk ruimten data bases en tabellen uitwisselen tussen de Spark-Pools en serverloze SQL-groep. Met behulp van het [gedeelde meta gegevens model](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview)kunt u een query uitvoeren op uw Apache Spark tabellen met behulp van SQL op aanvraag. Als u klaar bent, kunt u uw SQL on-demand-eind punt verbinden met Power BI om eenvoudig een query uit te voeren op uw gesynchroniseerde Spark-tabellen.


## <a name="next-steps"></a>Volgende stappen
- Voor meer informatie over het instellen van de Spark SQL DW-connector: [Synapse SQL-connector](./spark/../synapse-spark-sql-pool-import-export.md)
- De standaard bibliotheken weer geven: [Azure Synapse Analytics runtime](../spark/apache-spark-version-support.md)