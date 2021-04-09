---
title: 'Zelfstudie: Een notebook in Azure Cosmos DB maken om de gegevens te analyseren en visualiseren'
description: 'Zelfstudie: Ontdek hoe u ingebouwde Jupyter-notebooks gebruikt om gegevens naar Azure Cosmos DB te importeren, de gegevens te analyseren en de uitvoer te visualiseren.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: beb58922172a045242f4f9bcaf647b3cfc8b5551
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380794"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Zelfstudie: Een notebook in Azure Cosmos DB maken om de gegevens te analyseren en visualiseren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit item wordt beschreven hoe u ingebouwde Jupyter-notebooks gebruikt om voorbeeldretailgegevens naar Azure Cosmos DB te importeren. U ziet hoe u de Magic-opdrachten van SQL en Azure Cosmos DB gebruikt om query's uit te voeren, de gegevens te analyseren en de resultaten te visualiseren.

## <a name="prerequisites"></a>Vereisten

* [Notebooks inschakelen op een Azure Cosmos-account](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>De resources maken en gegevens importeren
 
In deze sectie gaat u de Azure Cosmos-database en -container maken en de retailgegevens in de container importeren.

1. Navigeer naar uw Azure Cosmos-account en open **Data Explorer.**

1. Ga naar het tabblad **Notebooks**, selecteer `…` naast **Mijn notebooks** en maak een **Nieuwe notebook**. Selecteer **Python 3** als de standaard-Kernel.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Een nieuwe notebook maken":::

1. Nadat een nieuwe notebook is gemaakt, kunt u de naam wijzigen in bijvoorbeeld **VisualizeRetailData.ipynb.**

1. Hierna maakt u een database met de naam 'RetailDemo' en een container met de naam 'WebsiteData' om de retailgegevens op te slaan. U kunt /CartID als de partitiesleutel gebruiken. Kopieer en plak de volgende code in een nieuwe cel in uw notebook en voer deze code uit:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Als u een cel wilt uitvoeren, selecteert u `Shift + Enter`. Of selecteer de cel en kies de optie **Actieve cel uitvoeren** op de navigatiebalk van Data Explorer.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="De actieve cel uitvoeren":::

   De database en container worden gemaakt in uw huidige Azure Cosmos-account. De container wordt ingericht met 400 RU/s. U ziet de volgende uitvoer nadat de database en container zijn gemaakt. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   U kunt ook het tabblad **Gegevens** vernieuwen en de zojuist gemaakte resources bekijken:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Het tabblad Gegevens vernieuwen om de nieuwe container weer te geven":::

1. Hierna gaat u de voorbeeldretailgegevens importeren in de Azure Cosmos-container. Hier ziet u de indeling van een item uit de retailgegevens:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Voor deze zelfstudie worden de voorbeeldretailgegevens opgeslagen in Azure Blob Storage. U kunt deze importeren in de Azure Cosmos-container door de volgende code in een nieuwe cel te plakken. U kunt controleren of de gegevens zijn geïmporteerd door een query uit te voeren om het aantal items te selecteren.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Wanneer u de vorige query uitvoert, wordt de volgende uitvoer geretourneerd:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Uw gegevens downloaden in een DataFrame

Voordat u query's gaat uitvoeren om de gegevens te analyseren, kunt u de gegevens uit de container uitlezen naar een [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) voor analyse. Gebruik de volgende SQL Magic-opdracht om de gegevens uit te lezen in een DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Zie het artikel [Ingebouwde notebookopdrachten en -functies in Azure Cosmos DB](use-python-notebook-features-and-commands.md) voor meer informatie. U voert de volgende query uit: `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. De resultaten worden opgeslagen in een Pandas DataFrame met de naam df_cosmos. Plak de volgende opdracht in een nieuwe notebookcel en voer deze opdracht uit:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Voer in een nieuwe notebookcel de volgende code uit om de eerste tien items van de uitvoer te lezen:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Een query uitvoeren om de tien eerste resultaten op te halen":::

## <a name="run-queries-and-analyze-your-data"></a>Query's uitvoeren en uw gegevens analyseren

In deze sectie gaat u een aantal query's uitvoeren op de opgehaalde gegevens.

* **Query1**: Voer de opdracht Groeperen op query uit in de DataFrame om de som van de totale verkoopomzet voor elk land/elke regio op te halen en vijf items van de resultaten weer te geven. Voer in een nieuwe notebookcel de volgende code uit:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Uitvoer: totale verkoopomzet":::

* **Query2:** U kunt een lijst met de vijf meest aangeschafte items ophalen door een nieuwe notebookcel te openen en de volgende code uit te voeren:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Vijf meest aangeschafte items":::

## <a name="visualize-your-data"></a>Uw gegevens visualiseren  

1. Nu we over onze gegevens over de omzet beschikken uit de Azure Cosmos-container, kunt u uw gegevens visualiseren met de gewenste visualisatiebibliotheek. In deze zelfstudie gebruiken we de Bokeh-bibliotheek. Open een nieuwe notebookcel en voer de volgende code uit om de Bokeh-bibliotheek te installeren. Nadat aan alle vereisten is voldaan, wordt de bibliotheek geïnstalleerd.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Hierna gaat u het tekenen van de gegevens op een kaart voorbereiden. Verbind de gegevens in Azure Cosmos DB met land-/regiogegevens in Azure Blob Storage en zet het resultaat om in de GeoJSON-indeling. Kopieer de volgende code naar een nieuwe notebookcel en voer deze code uit.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualiseer de verkoopomzet van verschillende landen/regio's op een wereldkaart door de volgende code uit te voeren in een nieuwe notebookcel:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   Bij de uitvoer wordt de wereldkaart met verschillende kleuren weergegeven. De kleuren van donker naar licht staan voor de landen/regio's met de hoogste naar de laagste omzet.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Visualisatie op een kaart van de omzet per land/regio":::

1. Laten we eens een andere toepassing van gegevensvisualisatie bekijken. In de WebsiteData-container staat een record van gebruikers die een item hebben bekeken, dit aan hun winkelwagen hebben toegevoegd en het item hebben aangeschaft. Laten we de conversieverhouding van aangeschafte items eens uittekenen. Voer de volgende code uit in een nieuwe cel om de conversieverhouding voor elk item te visualiseren:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="De conversieverhouding van aankopen visualiseren":::

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [Ingebouwde notebookopdrachten en -functies in Azure Cosmos DB gebruiken](use-python-notebook-features-and-commands.md) voor meer informatie over Python-notebookopdrachten.
