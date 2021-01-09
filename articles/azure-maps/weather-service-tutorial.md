---
title: 'Zelfstudie: Sensorgegevens samenvoegen met weervoorspellingsgegevens met behulp van Azure Notebooks (Python) met Microsoft Azure Maps'
description: Zelfstudie over hoe u met behulp van Azure Notebooks (Python) sensorgegevens samenvoegt met voorspellingsgegevens van de Microsoft Azure Maps Weather-services.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 259db10aa8f5cd2b711eca001bc6d48057d5280d
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683215"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Zelfstudie: Sensorgegevens samenvoegen met weervoorspellingsgegevens met behulp van Azure Notebooks (Python)

> [!IMPORTANT]
> Azure Maps Weather-services zijn momenteel beschikbaar in de openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Windkracht is een alternatieve energiebron voor fossiele brandstoffen om klimaatverandering tegen te gaan. Omdat wind van nature niet consistent is, moeten exploitanten van windenergie machine learning-modellen (ML) bouwen om de capaciteit van windenergie te voorspellen. Deze voorspelling is nodig om te kunnen voldoen aan de vraag naar elektriciteit en om de stabiliteit van het elektriciteitsnet te bewaren. In deze zelfstudie wordt uitgelegd hoe Azure Maps weervoorspellingsgegevens worden gecombineerd met voorbeeldgegevens voor weermetingen. Er worden gegevens over de weervoorspelling aangevraagd door de Azure Maps Weather-services (preview) aan te roepen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Werken met gegevensbestanden in [Azure Notebooks](../notebooks/index.yml) in de cloud.
> * Demogegevens laden uit een bestand.
> * Azure Maps REST API's aanroepen in Python.
> * Locatiegegevens op de kaart weergeven.
> * De demogegevens verrijken met de [dagelijkse weervoorspellingsgegevens](/rest/api/maps/weather/getdailyforecastpreview) van Azure Maps.
> * De voorspellingsgegevens in grafieken plotten.


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u eerst het volgende doen:

1. Een account voor een Azure Maps-abonnement in de prijscategorie S0 maken door de instructies in [Een account maken](quick-demo-map-app.md#create-an-azure-maps-account) te volgen.
2. De primaire abonnementssleutel voor uw account ophalen door de instructies in [Primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) te volgen.


Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

Volg de instructies in [Een Azure-notebook maken](./tutorial-ev-routing.md#create-an-azure-notebooks-project) om vertrouwd te raken met Azure Notebooks en te leren hoe u aan de slag kunt gaan.

> [!Note]
> Het Jupyter Notebook-bestand voor dit project kan worden gedownload van de [Jupyter Notebook-opslagplaats voor weerkaarten](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>De vereiste modules en frameworks laden

Voer het volgende script uit om alle vereiste modules en frameworks te laden:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Weergegevens importeren

Voor deze zelfstudie gebruiken we de weermeetgegevens van sensoren die zijn geïnstalleerd in vier verschillende windturbines. De voorbeeldgegevens bestaan uit 30 dagen aan weermetingen. Deze metingen worden verzameld van weerdatacentrums in de buurt van elke turbine. De demogegevens bevatten metingen van de temperatuur, windsnelheid en windrichting. U kunt de demogegevens [hier](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data) downloaden. Met het onderstaande script worden demogegevens in het Azure-notebook geïmporteerd.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Dagelijkse voorspellingsgegevens aanvragen

In ons scenario willen we dagelijks de voorspelling aanvragen voor elke sensorlocatie. Met het volgende script wordt de [dagelijkse voorspellings-API](/rest/api/maps/weather/getdailyforecastpreview) van de Azure Maps Weather-services (preview) aangeroepen. Deze API retourneert de weersvoorspelling voor elke windturbine, voor de komende 15 dagen vanaf de huidige datum.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services (Preview) to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Met het onderstaande script worden de turbinelocaties op de kaart weergegeven door de Azure Maps-service [Kaartafbeelding ophalen](/rest/api/maps/render/getmapimage) aan te roepen.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Turbinelocaties](./media/weather-service-tutorial/location-map.png)


De voorspellingsgegevens worden gegroepeerd met de demogegevens op basis van de station-id. De station-id is een aanduiding voor het weerdatacentrum. Deze groepering breidt de demogegevens uit met de voorspellingsgegevens.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

De volgende tabel bevat de gecombineerde historische en voorspellingsgegevens voor een van de turbinelocaties.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Gegroepeerde gegevens](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Voorspellingsgegevens plotten

We zetten de voorspelde waarden uit tegen de dagen waarvoor ze worden voorspeld. Met deze plot kunnen we de wijzigingen in de windsnelheid en -richting voor de komende 15 dagen weergeven.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

In de onderstaande grafieken worden de voorspellingsgegevens gevisualiseerd. Zie de linkergrafiek voor de wijzigingen in de windsnelheid. Zie de rechtergrafiek voor de wijzigingen in de windrichting. Deze gegevens vormen de voorspelling voor de komende 15 dagen vanaf de dag dat de gegevens worden aangevraagd.

<center>

![Plot van windsnelheid](./media/weather-service-tutorial/speed-date-plot.png) ![Plot van windrichting](./media/weather-service-tutorial/direction-date-plot.png)</center>

In deze zelfstudie hebt u geleerd hoe u Azure Maps REST API's aanroept om weervoorspellingsgegevens op te halen. U hebt ook geleerd hoe u de gegevens kunt visualiseren in grafieken.

Zie [Elektrische voertuigen routeren met Azure Notebooks](./tutorial-ev-routing.md) voor meer informatie over het aanroepen van Azure Maps REST API's in Azure Notebooks.

Als u meer wilt weten over de Azure Maps API's die in deze zelfstudie worden gebruikt, raadpleegt u:

* [Dagelijkse voorspelling](/rest/api/maps/weather/getdailyforecastpreview)
* [Renderen - Kaartafbeelding ophalen](/rest/api/maps/render/getmapimage)

Zie [Azure Maps REST API's](./consumption-model.md) voor een volledige lijst met Azure Maps REST API's.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Notebooks, zie

> [!div class="nextstepaction"]
> [Azure Notebooks](../notebooks/index.yml)
