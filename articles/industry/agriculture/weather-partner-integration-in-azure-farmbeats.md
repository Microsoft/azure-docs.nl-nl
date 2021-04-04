---
title: Partnerintegratie voor weer
description: Meer informatie over hoe een weer gegevens provider kan worden geïntegreerd met FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147076"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Weer partner integratie met FarmBeats

Dit artikel bevat informatie over het docker-onderdeel van de Azure FarmBeats-connector. Als weer gegevens provider kunt u de connector docker gebruiken om te integreren met FarmBeats. Gebruik de Api's om weer gegevens naar FarmBeats te verzenden. In FarmBeats kunnen de gegevens worden gebruikt voor gegevens fusie en voor het bouwen van machine learning modellen of kunst matige intelligentie modellen.

 > [!NOTE]
 > In dit artikel gebruiken we een [referentie-implementatie](https://github.com/azurefarmbeats/noaa_docker) die is gebouwd met behulp van Azure open data sets en weer gegevens uit National Oceanic en atmosferische toediening (NOAA). We gebruiken ook de bijbehorende [docker-installatie kopie](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa).

U moet een [geschikte docker-installatie kopie of programma](#docker-specifications) opgeven en de docker-installatie kopie hosten in een container register dat door klanten kan worden geopend. Geef de volgende informatie op voor uw klanten:

- URL van docker-installatie kopie
- Docker-afbeeldings code
- Sleutels of referenties voor toegang tot de docker-installatie kopie
- Klantspecifieke API-sleutels of-referenties voor toegang tot de gegevens van uw systeem
- Details van VM-SKU (geef deze details op als uw docker-installatie kopie specifieke VM-vereisten heeft. Anders kunnen klanten kiezen uit ondersteunde VM-Sku's in Azure.)

Klanten gebruiken deze docker-gegevens om een weer partner te registreren in hun FarmBeats-exemplaar. Voor meer informatie over hoe klanten de docker kunnen gebruiken om weer gegevens op te nemen in FarmBeats, raadpleegt u [gegevens ophalen van weer partners](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Docker-ontwikkeling van connector

**Integratie op basis van REST API**

De FarmBeats-Api's bevatten technische documentatie voor Swagger. Zie de [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)voor meer informatie over de api's en de bijbehorende aanvragen of antwoorden. 

Als u FarmBeats al hebt geïnstalleerd, hebt u toegang tot uw FarmBeats Swagger op `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Let op *:-API* wordt toegevoegd aan de naam van uw FarmBeats-website. Het API-eind punt is `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats biedt een lib die u kunt gebruiken. De lib is momenteel beschikbaar als [onderdeel van de implementatie van de verwijzing](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). Later is het beschikbaar als een SDK voor meerdere talen.

### <a name="authentication"></a>Verificatie

**Verificatie met FarmBeats-Api's**

FarmBeats maakt gebruik van Bearer-verificatie. U kunt toegang krijgen tot de Api's door een toegangs token op te geven in de koptekst sectie van de aanvraag. Hier volgt een voorbeeld:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

U kunt het toegangs token aanvragen via een Azure Functions-app die wordt uitgevoerd op het FarmBeats-exemplaar van de klant. De Azure Functions URL wordt als argument aan het docker-programma gegeven. U kunt het toegangs Token ophalen door een `GET` aanvraag op de URL te maken. Het antwoord van de URL bevat het toegangs token. 

Gebruik de hulp functies in de Datahub lib om het toegangs token op te halen. Zie de [github-pagina voor de NOAA docker-installatie kopie](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)voor meer informatie.

Het toegangs token is slechts enkele uren geldig. Wanneer het verloopt, moet u het opnieuw aanvragen.

**Verificatie met Api's aan de partner zijde**

Klanten moeten de referenties opgeven tijdens de registratie van de partner om te verifiëren met de Api's aan de partner zijde terwijl de docker-taak wordt uitgevoerd. Hier volgt een voorbeeld:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
De API-service serializeert deze dicteer en slaat deze op in een [sleutel kluis](../../key-vault/general/basic-concepts.md).

[Azure Data Factory](../../data-factory/introduction.md) wordt gebruikt om weer taken te organiseren. Het draait om resources om de docker-code uit te voeren. Data Factory biedt ook een mechanisme om gegevens veilig te pushen naar de virtuele machine waarop de docker-taak wordt uitgevoerd. De API-referenties worden vervolgens veilig opgeslagen in de sleutel kluis. 

Referenties worden gelezen als beveiligde teken reeksen uit de sleutel kluis. Ze worden gegeven als uitgebreide eigenschappen in de werkmap van de docker-container. Het bestandspad is */mnt/working_dir/activity.jsop*. 

De docker-code kan de referenties van *activity.js* tijdens runtime lezen om api's aan de partner zijde voor de klant te krijgen. In het JSON-bestand zien de referenties eruit als in het volgende code voorbeeld:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
De `partnerCredentials` referentie is beschikbaar op de manier die de klant heeft gegeven tijdens het registreren van de partner.

De FarmBeats lib biedt hulp functies. Gebruik deze functies om de referenties van de eigenschappen van de activiteit te lezen. Zie de [github-pagina voor de NOAA docker-installatie kopie](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)voor meer informatie.

Het bestand wordt alleen gebruikt als de docker-code wordt uitgevoerd. Nadat de code is voltooid, wordt het bestand verwijderd.

Zie [schema en gegevens type toewijzing](../../data-factory/copy-activity-schema-and-type-mapping.md)voor meer informatie over de werking van Data Factory pijp lijnen en activiteiten.

**HTTP-aanvraag headers**

De volgende tabel bevat de meest voorkomende aanvraag headers die u moet opgeven wanneer u een API-aanroep naar FarmBeats maakt.

Header | Beschrijving en voor beeld
--- | ---
Content-Type | De aanvraag indeling. Voorbeeld: `Content-Type: application/<format>` <br/>Voor FarmBeats Datahub-Api's is de indeling JSON. Voorbeeld: ` Content-Type: application/json`
Autorisatie | Het toegangs token dat vereist is om een API-aanroep uit te voeren. Voorbeeld: `Authorization: Bearer <Access-Token>`
Accepteren | De antwoord indeling. Voor FarmBeats Datahub-Api's is de indeling JSON. Voorbeeld: `Accept: application/json`

## <a name="data-format"></a>Gegevensindeling

JSON is een gemeen schappelijke taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie [JSON.org](https://json.org)voor meer informatie.

## <a name="docker-specifications"></a>Docker-specificaties

Het docker-programma heeft twee onderdelen nodig: de Boots trap en de taak. Het programma kan meer dan één taak hebben.

### <a name="bootstrap"></a>Bootstrap

Het Boots trap-onderdeel moet worden uitgevoerd wanneer de klant de docker-registratie op FarmBeats start. De volgende argumenten ( `arg1` en `arg2` ) worden door gegeven aan het programma:

- **FARMBEATS API-eind punt**: het FarmBeats API-eind punt voor API-aanvragen. Met dit eind punt maakt u API-aanroepen naar de FarmBeats-implementatie.
- **Azure functions URL**: uw eigen eind punt. Deze URL biedt uw toegangs token voor FarmBeats-Api's. U kunt `GET` op deze URL aanroepen om het toegangs token op te halen.

De Boots trap maakt de meta gegevens die gebruikers nodig hebben om uw taken uit te voeren om weer gegevens op te halen. Zie de [referentie-implementatie](https://github.com/azurefarmbeats/noaa_docker)voor meer informatie. 

Als u de *bootstrap_manifest.jsin* het bestand aanpast, worden de vereiste meta gegevens voor u gemaakt met het referentie Boots trap programma. Het Boots trap programma maakt de volgende meta gegevens: 

 > [!NOTE]
 > Als u de *bootstrap_manifest.jsin* het bestand bijwerkt als de [referentie-implementatie](https://github.com/azurefarmbeats/noaa_docker) beschrijft, hoeft u de volgende meta gegevens niet te maken. Het Boots trap-programma gebruikt uw manifest bestand om de benodigde meta gegevens te maken.

- /**WeatherDataModel**: de WeatherDataModel-meta gegevens vertegenwoordigen weers gegevens. Dit komt overeen met gegevens sets die door de bron worden geleverd. Een DailyForecastSimpleModel kan bijvoorbeeld een gemiddelde Tempe ratuur, vochtigheids graad en informatie eenmaal per dag bevatten. Een DailyForecastAdvancedModel kan daarentegen veel meer informatie bieden op het uurloon. U kunt een wille keurig aantal weer gegeven gegevens modellen maken.
- /**Taak type**: FarmBeats heeft een uitbreidbaar taak beheersysteem. Als weer gegeven gegevens provider hebt u diverse gegevens sets en Api's (bijvoorbeeld GetDailyForecasts). U kunt deze gegevens sets en Api's in FarmBeats inschakelen met behulp van taak type. Nadat een taak type is gemaakt, kan een klant taken van dat type activeren om weer gegevens op te halen voor hun locatie of voor hun eigen bedrijf. Zie taak type and job Api's (Engelstalig) in de [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)voor meer informatie.

### <a name="jobs"></a>Taken

Het onderdeel Jobs wordt geactiveerd wanneer een FarmBeats-gebruiker een taak uitvoert van de/JobType die u hebt gemaakt als onderdeel van het Boots trap proces. De opdracht docker run voor de taak wordt gedefinieerd als onderdeel van de/JobType die u hebt gemaakt.

De taak haalt gegevens op uit de bron en duwt deze naar FarmBeats. Tijdens de Boots trap-procedure moeten de para meters die nodig zijn om de gegevens op te halen, worden gedefinieerd als onderdeel van/JobType.

Als onderdeel van de taak moet het programma een/WeatherDataLocation maken op basis van de/WeatherDataModel die is gemaakt tijdens het Boots trap proces. De/WeatherDataLocation komt overeen met een locatie (breedte graad en lengte graad) die de gebruiker heeft gegeven als een para meter voor de taak.

### <a name="object-details"></a>Object Details

WeatherDataModel | Beschrijving |
--- | ---
Name  | De naam van het weer gegeven gegevens model. |
Description  | Een duidelijke beschrijving van het model. |
Eigenschappen  | Aanvullende eigenschappen die zijn gedefinieerd door de gegevens provider. |
> naam weatherMeasures  | De naam van de weer maat meting. Bijvoorbeeld humidity_max. |
weatherMeasures > data type  | Dubbel of Enum. Als Enum, is measureEnumDefinition vereist. |
weatherMeasures > measureEnumDefinition  | Alleen vereist als data type Enum is. Bijvoorbeeld: `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > type  | Type weer te gegeven telemetriegegevens. Bijvoorbeeld RelativeHumidity. De door het systeem gedefinieerde typen zijn AmbientTemperature, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitraat, O2, PH, fosfaat, PointInTime, kalium, druk, RainGauge, RelativeHumidity, zout, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration en pari. Zie de sectie [Extended type toevoegen](#add-extendedtype) in dit artikel voor meer informatie over het toevoegen van meer typen.
weatherMeasures >-eenheid | Eenheid van weers-telemetrie-gegevens. De door het systeem gedefinieerde eenheden zijn geen eenheid, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kwik, PSI, MilliMeter, CentiMeter, meter, inch, meter, mijl, kilo meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentage, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, seconden, UnixTimestamp, MicroMolePerMeterSquaredPerSecond en InchesPerHour. Zie de sectie [Extended type toevoegen](#add-extendedtype) in dit artikel om meer eenheden toe te voegen.
weatherMeasures > AggregationType  | Het type aggregatie. Mogelijke waarden zijn none, Average, maximum, minimum, StandardDeviation, Sum en Total.
Diepte van weatherMeasures->  | De diepte van de sensor in centimeters. Bijvoorbeeld de meting van het vocht 10 cm onder het wegdek.
Beschrijving van weatherMeasures->  | Een duidelijke beschrijving van de meting. 

Taak type | Beschrijving |
--- | ---
Name  | De naam van de taak. Bijvoorbeeld Get_Daily_Forecast. De klant zal deze taak uitvoeren om weer gegevens op te halen.|
pipelineDetails > para meters > naam  | De naam van de para meter. |
pipelineDetails > para meters > type | Het parameter type. Mogelijke waarden zijn String, int, float, BOOL en array. |
pipelineDetails >-para meters > isRequired | De Booleaanse waarde van de para meter. De waarde is True als de para meter is vereist. Anders is de waarde false. De standaard waarde is True. |
pipelineDetails > para meters > defaultValue | De standaard waarde van de para meter. |
pipelineDetails > para meters > beschrijving | Beschrijving van de para meter. |
Eigenschappen  | Aanvullende eigenschappen van de fabrikant.
Eigenschappen > programRunCommand | Opdracht docker run. Deze opdracht wordt uitgevoerd wanneer de klant de weer taak uitvoert. |

WeatherDataLocation | Description |
--- | ---
weatherDataModelId  | De ID van de bijbehorende WeatherDataModel die is gemaakt tijdens het Boots trap proces.|
location  | Breedte graad, lengte graad en uitbrei ding. |
Name | De naam van het object. |
Description | Beschrijving van de weer gegevens locatie. |
farmId | Beschrijving ID van de farm. De klant levert deze ID als onderdeel van de taak parameter. |
Eigenschappen  | Aanvullende eigenschappen van de fabrikant.

Zie de [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)voor meer informatie over de objecten en hun eigenschappen.

> [!NOTE]
> De Api's retour neren unieke Id's voor elk exemplaar dat wordt gemaakt. De vertaler voor Apparaatbeheer en meta gegevens synchronisatie moet deze ID behouden.

**Meta gegevens synchroniseren**

Het docker-onderdeel connector moet updates kunnen verzenden voor de meta gegevens. Het moet bijvoorbeeld updates verzenden wanneer de weer provider nieuwe para meters toevoegt aan een gegevensset of wanneer er nieuwe functionaliteit wordt toegevoegd, zoals een nieuwe prognose van 30 dagen.

> [!NOTE]
> Verwijderen wordt niet ondersteund voor meta gegevens in het data model weer.
>
> Als u meta gegevens wilt bijwerken, moet u aanroepen `/Get/{ID}` op het weer gegevens model. Werk de gewijzigde eigenschappen bij en voer vervolgens een uit `/Put/{ID}` om de eigenschappen te behouden die de gebruiker heeft ingesteld.

## <a name="weather-data-telemetry-specifications"></a>Specificaties voor weer gegevens (telemetrie)

Weer gegevens worden toegewezen aan een canonieke bericht dat wordt gepusht naar een Azure-Event Hub voor verwerking. Azure Event Hubs is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen. 

Als u weer gegevens naar FarmBeats wilt verzenden, maakt u een client die berichten verzendt naar een Event Hub in FarmBeats. Zie [telemetrie verzenden naar een event hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)voor meer informatie.

Met de volgende python-voorbeeld code wordt telemetrie als een client naar een opgegeven Event Hub verzonden.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Dit is de canonieke bericht indeling:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Hier volgt een voor beeld van een telemetrie-bericht:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Probleem oplossing en fouten beheer

### <a name="error-logging"></a>Fout registratie

De partner taak wordt uitgevoerd in het bestaande project Framework. De fouten worden dus op dezelfde manier geregistreerd als fouten voor andere bestaande FarmBeats-taken (zoals GetFarmData en SensorPlacement). De Data Factory activiteit die wordt uitgevoerd binnen de Data Factory pijp lijn registreert zowel `STDERR` als `STDOUT` . Beide bestanden zijn beschikbaar in het `datahublogs-xxx` opslag account in de resource groep FarmBeats.

De Datahub lib biedt hulp functies om logboek registratie in te scha kelen als onderdeel van de algemene Datahub-Logboeken. Zie de [github-pagina voor de NOAA docker-installatie kopie](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)voor meer informatie.

### <a name="troubleshooting-and-support"></a>Probleem oplossing en ondersteuning

Als de klant weer gegevens niet in het FarmBeats-exemplaar kan ontvangen, bieden we ondersteuning en een mechanisme om het probleem op te lossen.

## <a name="add-extendedtype"></a>Extended type toevoegen

FarmBeats biedt ondersteuning voor het toevoegen van nieuwe sensor metings typen en-eenheden. U kunt nieuwe eenheden of typen toevoegen door de *bootstrap_manifest.js* in het bestand bij te werken in de [referentie-implementatie](https://github.com/azurefarmbeats/noaa_docker).

Volg deze stappen om een nieuw WeatherMeasure-type toe te voegen, bijvoorbeeld PrecipitationDepth.

1. Een `GET` aanvraag indienen voor/ExtendedType met behulp van de query `filter - key = WeatherMeasureType` .
2. Noteer de ID van het geretourneerde object.
3. Voeg het nieuwe type toe aan de lijst in het geretourneerde object. Maak een `PUT` aanvraag op de/ExtendedType{id} met de volgende nieuwe lijst. De invoer lading moet hetzelfde zijn als het antwoord dat u eerder hebt ontvangen. De nieuwe eenheid moet aan het einde van de lijst met waarden worden toegevoegd.

Zie de [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)voor meer informatie over de/EXTENDEDTYPE-API.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een docker-onderdeel connector dat kan worden geïntegreerd met FarmBeats. Vervolgens leert u hoe u [weer gegevens kunt ophalen](get-weather-data-from-weather-partner.md) met behulp van uw docker-installatie kopie in FarmBeats. 
