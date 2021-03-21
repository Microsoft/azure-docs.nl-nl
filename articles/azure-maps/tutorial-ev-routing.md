---
title: 'Zelfstudie: Elektrische voertuigen routeren met behulp van Azure Notebooks (Python) met Microsoft Azure Maps'
description: Zelfstudie over hoe u elektrische voertuigen kunt routeren met behulp van routerings-API's van Microsoft Azure Maps en Azure Notebooks
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680329"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Zelfstudie: Elektrische voertuigen routeren met behulp van Azure Notebooks (Python)

Azure Maps is een portfolio met georuimtelijke service-API's die systeemeigen zijn geïntegreerd in Azure. Met deze API's kunnen ontwikkelaars, ondernemingen en ISV's locatiegerichte apps en oplossingen ontwikkelen voor IoT, mobiliteit, logistiek en het bijhouden van assets. 

De REST API's van Azure Maps kunnen worden aangeroepen vanuit computertalen zoals Python en R voor het inschakelen van georuimtelijke gegevensanalyse en machine learning-scenario's. Azure Maps biedt een robuuste set [routerings-API's](/rest/api/maps/route) waarmee gebruikers routes tussen verschillende gegevenspunten kunnen berekenen. De berekeningen zijn gebaseerd op verschillende voorwaarden, zoals het voertuigtype of het bereikbare gebied. 

In deze zelfstudie leert u hoe u een bestuurder helpt van wie de accu van het elektrische voertuig bijna leeg is. De bestuurder moet het laadstation vinden dat zich het dichtst bij de locatie van het voertuig bevindt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Jupyter Notebook-bestand en voer het uit in [Azure Notebooks](https://notebooks.azure.com) in de cloud.
> * REST API's van Azure Maps aanroepen in Python.
> * Een bereik zoeken op basis van het verbruiksmodel van het elektrische voertuig.
> * Zoeken naar laadstations voor elektrische voertuigen binnen het bereik, ofwel de isochroon.
> * De bereikgrens en laadstations op een kaart weergeven.
> * Op basis van de rijtijd een route naar het dichtstbijzijnde laadstation voor elektrische voertuigen zoeken en visualiseren.


## <a name="prerequisites"></a>Vereisten 

Voor het volgen van deze zelfstudie moet u eerst een Azure Maps-account maken en uw primaire sleutel (abonnementssleutel) ophalen. 

Volg de instructies in [Een account maken](quick-demo-map-app.md#create-an-azure-maps-account) om een Azure Maps-accountabonnement te maken. U hebt een Azure Maps-accountabonnement nodig met de prijscategorie S1. 

U kunt de primaire abonnementssleutel voor uw account ophalen door de instructies in [Primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) te volgen.

Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="create-an-azure-notebooks-project"></a>Een Azure Notebooks-project maken

Voor deze zelfstudie moet u een Azure Notebooks-project maken en het Jupyter Notebook-bestand downloaden en uitvoeren. Het Jupyter Notebook-bestand bevat Python-code waarmee het scenario in deze zelfstudie wordt geïmplementeerd. Voer de volgende stappen uit om een Azure Notebooks-project te maken en het Jupyter Notebook-document te uploaden naar dat project:

1. Ga naar [Azure Notebooks](https://notebooks.azure.com) en meld u aan. Zie voor meer informatie [Snelstart: Aanmelden en een gebruikers-id instellen](https://notebooks.azure.com).
1. Selecteer bovenaan de openbare profielpagina **Mijn projecten**.

    ![De knop Mijn projecten](./media/tutorial-ev-routing/myproject.png)

1. Selecteer op de pagina **Mijn projecten** de optie **Nieuw project**.
 
   ![De knop Nieuw project](./media/tutorial-ev-routing/create-project.png)

1. Voer in het deelvenster **Nieuw project maken** een projectnaam en project-id in.
 
    ![Het deelvenster Nieuw Project maken](./media/tutorial-ev-routing/create-project-window.png)

1. Selecteer **Maken**.

1. Nadat uw project is gemaakt, downloadt u dit [Jupyter Notebook-documentbestand](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) uit de [Jupyter Notebook-opslagplaats van Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Selecteer in de lijst met projecten op de pagina **Mijn projecten** uw project en selecteer vervolgens **Uploaden** om het Jupyter Notebook-documentbestand te uploaden. 

    ![Jupyter Notebook uploaden](./media/tutorial-ev-routing/upload-notebook.png)

1. Upload het bestand vanaf uw computer en selecteer vervolgens **Gereed**.

1. Nadat het uploaden is voltooid, wordt uw bestand weergegeven op uw projectpagina. Dubbelklik op het bestand om het te openen als een Jupyter Notebook.

Probeer de functionaliteit te begrijpen die in het Jupyter Notebook-bestand is geïmplementeerd. Voer de code in het Jupyter Notebook-bestand cel voor cel uit. U kunt de code in elke cel uitvoeren door de knop **Uitvoeren** aan de bovenkant van de Jupyter Notebook-app te selecteren.

  ![De knop Uitvoeren](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Pakketten op projectniveau installeren

Voer de volgende stappen uit om de code in het Jupyter Notebook uit te voeren om pakketten op projectniveau te installeren:

1. Download het bestand [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) vanuit de [Jupyter Notebook-opslagplaats van Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) en upload het vervolgens naar uw project.
1. Selecteer **Projectinstellingen** op het projectdashboard. 
1. Selecteer in het deelvenster **Projectinstellingen** het tabblad **Omgeving** en selecteer vervolgens **Toevoegen**.
1. Ga als volgt te werk onder **Stappen voor het instellen van de omgeving**:   
    a. Selecteer in de eerste vervolgkeuzelijst **Requirements.txt**.  
    b. Selecteer in de tweede vervolgkeuzelijst uw bestand *requirements.txt*.  
    c. Selecteer in de derde vervolgkeuzelijst **Python-versie 3.6** als uw versie.
1. Selecteer **Opslaan**.

    ![Pakketten installeren](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>De vereiste modules en frameworks laden

Voer het volgende script uit om alle vereiste modules en frameworks te laden:

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>De bereikgrens opvragen

Een pakketdienst beschikt over enkele elektrische voertuigen. Overdag moeten de elektrische voertuigen opnieuw worden opgeladen zonder terug te keren naar het magazijn. Telkens wanneer de resterende lading onder een uur komt, zoekt u naar een set laadstations die zich binnen het bereik bevinden. U zoekt dus naar een laadstation wanneer de accu bijna leeg is. En u krijgt de grensinformatie voor het laadstationbereik terug. 

Omdat het bedrijf bij voorkeur een balans tussen zuinigheid en snelheid hanteert bij het kiezen van routes, is het aangevraagde routeType *eco*. Met het volgende script wordt de [API Routebereik ophalen](/rest/api/maps/route/getrouterange) van de routeringsservice van Azure Maps aangeroepen. Het maakt gebruik van parameters voor het verbruiksmodel van het voertuig. Met het script wordt het antwoord geparseerd om een veelhoekobject in de GeoJSON-indeling te maken, waarmee het maximumbereik van de auto wordt aangegeven.

U kunt de grenzen voor het bereik van het elektrische voertuig bepalen door het script in de volgende cel uit te voeren:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Zoeken naar laadstations voor elektrische voertuigen binnen het bereik

Nadat u het bereik (de isochroon) voor het elektrische voertuig hebt vastgesteld, kunt u zoeken naar laadstations binnen dat bereik. 

Met het volgende script wordt de [API Post-zoekopdracht binnen de geometrie](/rest/api/maps/search/postsearchinsidegeometry) van Azure Maps aangeroepen. Hiermee wordt gezocht naar laadstations voor elektrische voertuigen binnen de grenzen van het maximale bereik van de auto. Vervolgens wordt het antwoord door het script geparseerd naar een matrix van bereikbare locaties.

Voer het volgende script uit om te zoeken naar laadstations voor elektrische voertuigen binnen het bereik:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Het bereik en de laadpunten uploaden naar Azure Maps Data Service (preview)

Op een kaart wilt u de laadstations en de grens van het maximale bereik van het elektrische voertuig visualiseren. Hiervoor uploadt u de grensgegevens en laadstationgegevens als geoJSON-objecten naar Azure Maps Data Service (preview). Gebruik de [API Gegevens uploaden](/rest/api/maps/data/uploadpreview). 

Voer de volgende twee cellen uit om de grens- en laadpuntgegevens te uploaden naar de Azure Maps Data-service:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>De laadstations en het bereik weergeven op een kaart

Nadat u de gegevens hebt geüpload naar Data Service roept u de [service Kaartafbeelding ophalen](/rest/api/maps/render/getmapimage) van Azure Maps aan. Via deze service worden de laadpunten en de maximaal bereikbare grens weergegeven op de statische kaartafbeelding door het volgende script uit te voeren:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Een kaart waarop het locatiebereik wordt weergegeven](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Het optimale laadstation zoeken

Eerst wilt u alle mogelijke laadstations binnen het bereik bepalen. Vervolgens wilt u weten welke ervan kunnen worden bereikt binnen een minimale tijd. 

Met het volgende script wordt de [API Matrixroutering](/rest/api/maps/route/postroutematrix) van Azure Maps aangeroepen. Hiermee wordt de opgegeven voertuiglocatie, de reistijd en de afstand tot elk laadstation geretourneerd. Met het script in de volgende cel wordt het antwoord geparseerd om het dichtstbijzijnde bereikbare laadstation qua tijd te vinden.

Als u wilt zoeken naar het dichtstbijzijnde laadstation dat kan worden bereikt in de kortste tijd, voert u het script in de volgende cel uit:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>De route naar het dichtstbijzijnde laadstation berekenen

Nu u het dichtstbijzijnde laadstation hebt gevonden, kunt u de [API Routebeschrijving ophalen](/rest/api/maps/route/getroutedirections) aanroepen om de gedetailleerde route van de huidige locatie van het elektrische voertuig naar het laadstation op te vragen.

Voer het script in de volgende cel uit om de route naar het laadstation op te halen en het antwoord te parseren om een geoJSON-object te maken dat de route vertegenwoordigt:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>De route visualiseren

Als u de route wilt visualiseren moet u eerst de routegegevens als een geoJSON-object uploaden naar de Azure Maps Data-service (preview). Gebruik hiervoor de [API Gegevens uploaden](/rest/api/maps/data/uploadpreview) van Azure Maps. Vervolgens roept u de renderingservice, de [API Kaartafbeeldingen ophalen](/rest/api/maps/render/getmapimage), aan om de route op de kaart weer te geven en te visualiseren.

Voer het volgende script uit om een afbeelding voor de weergegeven route op de kaart op te halen:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Een kaart waarop de route wordt weergegeven](./media/tutorial-ev-routing/route.png)

In deze zelfstudie hebt u geleerd hoe u rechtstreeks REST API's van Azure Maps aanroept en Azure Maps-gegevens visualiseert met behulp van Python.

Als u meer wilt weten over de API's van Azure Maps die in deze zelfstudie worden gebruikt, raadpleegt u:

* [Routebereik ophalen](/rest/api/maps/route/getrouterange)
* [Post-zoekopdracht binnen geometrie](/rest/api/maps/search/postsearchinsidegeometry)
* [Gegevens uploaden](/rest/api/maps/data/uploadpreview)
* [Weergeven - Kaartafbeelding ophalen](/rest/api/maps/render/getmapimage)
* [Post-routematrix](/rest/api/maps/route/postroutematrix)
* [Routebeschrijving ophalen](/rest/api/maps/route/getroutedirections)
* [Azure Maps REST API's](./consumption-model.md)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Notebooks, zie

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)