---
title: Partnerintegratie voor sensoren
description: In dit artikel wordt de integratie van de sensor partner beschreven.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179745"
---
# <a name="sensor-partner-integration"></a>Partnerintegratie voor sensoren

Dit artikel bevat informatie over het onderdeel Azure FarmBeats **Translator** , waarmee sensor-partner integratie mogelijk wordt.

Met dit onderdeel kunnen partners worden geïntegreerd met FarmBeats met behulp van FarmBeats Datahub-Api's en apparaatgegevens en telemetrie van de klant verzenden naar FarmBeats Datahub. Zodra de gegevens beschikbaar zijn in FarmBeats, wordt deze gevisualiseerd met behulp van de FarmBeats-Accelerator en kunnen ze worden gebruikt voor gegevens fusie en voor het bouwen van machine learning/kunst matige intelligentie modellen.

## <a name="before-you-start"></a>Voordat u begint

Voor het ontwikkelen van het onderdeel Translator hebt u de volgende referenties nodig om toegang tot de FarmBeats-Api's mogelijk te maken.

- API-eindpunt
- Tenant-id
- Client-id
- Clientgeheim
- EventHub-verbindings reeks

Zie deze sectie voor meer informatie over het ophalen van de bovenstaande referenties: [enable device Integration](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Translator-ontwikkeling

**Integratie op basis van REST API**

De mogelijkheden voor het integreren van sensor gegevens van FarmBeats worden weer gegeven via de REST API. Mogelijkheden zijn onder andere meta gegevens definitie, inrichting van apparaten en sensors en beheer van apparaten en Sens oren.

**Telemetrie-opname**

De telemetriegegevens worden toegewezen aan een canonieke bericht dat wordt gepubliceerd op Azure Event Hubs voor verwerking. Azure Event Hubs is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen.

**API-ontwikkeling**

De Api's bevatten technische documentatie voor Swagger. Zie [Swagger](https://aka.ms/FarmBeatsSwagger)voor meer informatie over de api's en de bijbehorende aanvragen of antwoorden.

**Verificatie**

FarmBeats maakt gebruik van Microsoft Azure Active Directory-verificatie.Azure App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie.

Zie [Azure Active Directory](../../app-service/overview-authentication-authorization.md)voor meer informatie.

FarmBeats Datahub maakt gebruik van Bearer-verificatie, die de volgende referenties nodig heeft:
   - Client-id
   - Clientgeheim
   - Tenant-id

Met deze referenties kan de aanroeper een toegangs token aanvragen. Het token moet worden verzonden in de volgende API-aanvragen, in de koptekst sectie, als volgt:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

De volgende voor beeld-python-code geeft het toegangs token, dat kan worden gebruikt voor de volgende API-aanroepen van FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**HTTP-aanvraag headers**

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven wanneer u een API-aanroep maakt naar FarmBeats Datahub.


**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type | De aanvraag indeling (content-type: Application/ <format> ). Voor FarmBeats Datahub-Api's is de indeling JSON. Content-type: Application/JSON
Autorisatie | Hiermee geeft u het toegangs token op dat vereist is om een API-aanroep te maken. Autorisatie: Bearer <Access-Token>
Accepteren | De antwoord indeling. Voor FarmBeats Datahub-Api's is de indeling JSON. Accepteren: toepassing/JSON

**API-aanvragen**

Als u een REST API aanvraag wilt maken, combineert u de HTTP-methode (GET, POST of PUT), de URL naar de API-service, de URI (Uniform Resource Identifier) voor een resource om een query uit te voeren, gegevens in te dienen bij, bij te werken of te verwijderen, en een of meer HTTP-aanvraag headers. De URL naar de API-service is het API-eind punt dat u opgeeft. Hier volgt een voor beeld: https:// \<yourdatahub-website-name> . azurewebsites.net

U kunt desgewenst query parameters toevoegen aan GET-aanroepen om te filteren, de grootte van de gegevens in de antwoorden te beperken en te sorteren.

De volgende voorbeeld aanvraag is om de lijst met apparaten op te halen.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Voor de meeste GET-, POST-en PUT-aanroepen is een JSON-aanvraag tekst vereist.

De volgende voorbeeld aanvraag is het maken van een apparaat. (Dit voor beeld heeft een invoer-JSON met de aanvraag tekst.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Gegevensindeling

JSON is een gemeen schappelijke taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie [JSON.org](http://json.org)voor meer informatie.

## <a name="metadata-specifications"></a>Specificaties van meta gegevens

FarmBeats Datahub heeft de volgende Api's waarmee de apparaat partners apparaat-of sensor-meta gegevens kunnen maken en beheren.

- /**DeviceModel**: DeviceModel komt overeen met de meta gegevens van het apparaat, zoals de fabrikant en het type apparaat, ofwel de gateway ofwel het knoop punt.
- /**Apparaat**: het apparaat komt overeen met een fysiek apparaat dat aanwezig is op de farm.
- /**SensorModel**: SensorModel komt overeen met de meta gegevens van de sensor, zoals de fabrikant, het type sensor dat analoog of digitaal is en de sensor meting, zoals omgevings temperatuur en druk.
- /**Sensor**: sensor komt overeen met een fysieke sensor waarmee waarden worden vastgelegd. Een sensor is doorgaans verbonden met een apparaat met een apparaat-ID.

  DeviceModel | Beschrijving |
  --- | ---
  Type (knoop punt, gateway)  | Type van het apparaat of de gateway |
  Fabrikant  | De naam van de fabrikant |
  Code  | Product code of model naam of-nummer van het apparaat. Bijvoorbeeld EnviroMonitor # 6800. |
  Poorten  | Poort naam en-type, digitaal of analoog.  |
  Name  | Naam voor het identificeren van de resource. Bijvoorbeeld model naam of product naam. |
  Beschrijving  | Geef een zinvolle beschrijving van het model op. |
  Eigenschappen  | Aanvullende eigenschappen van de fabrikant. |
  **Apparaat** | **Beschrijving** |
  DeviceModelId  |ID van het gekoppelde model. |
  HardwareId   |De unieke ID voor het apparaat, zoals een MAC-adres.  |
  ReportingInterval |Rapportage-interval in seconden. |
  Locatie    |Apparaat Latitude (-90 tot + 90), lengte graad (-180 tot 180) en uitbrei ding (in meters). |
  ParentDeviceId | De ID van het bovenliggende apparaat waarmee dit apparaat is verbonden. Als een knoop punt bijvoorbeeld is verbonden met een gateway, heeft het knoop punt parentDeviceID als de gateway. |
  Name  | Naam om de resource te identificeren. De partners van het apparaat moeten een naam verzenden die consistent is met de naam van het apparaat op de partner zijde van het apparaat. Als de apparaatnaam door de gebruiker is gedefinieerd op de partner zijde van het apparaat, moet dezelfde door de gebruiker gedefinieerde naam worden door gegeven aan FarmBeats.  |
  Beschrijving  | Geef een zinvolle beschrijving op.  |
  Eigenschappen  |Aanvullende eigenschappen van de fabrikant.  |
  **SensorModel** | **Beschrijving** |
  Type (analoog, digitaal)  |Vermeld een analoge of digitale sensor.|
  Fabrikant  | De naam van de fabrikant. |
  Code  | Product code of model naam of-nummer. Bijvoorbeeld RS-CO2-N01.  |
  > naam SensorMeasures  | De naam van de sensor meting. Alleen kleine letters worden ondersteund. Geef voor metingen uit verschillende diepten de diepte op. Bijvoorbeeld soil_moisture_15cm. Deze naam moet consistent zijn met de telemetrie-gegevens. |
  SensorMeasures > data type  | Het gegevens type telemetrie. Op dit moment wordt dubbele wordt ondersteund. |
  SensorMeasures > type  | Meet type van de telemetrie van de sensor. Hieronder ziet u de door het systeem gedefinieerde typen: AmbientTemperature, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitraat, O2, PH, fosfaat, PointInTime, kalium, druk, RainGauge, RelativeHumidity, zout, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Raadpleeg de/ExtendedType-API om meer toe te voegen.
  SensorMeasures >-eenheid | De gegevens eenheid van de sensor-telemetrie. Hieronder ziet u de door het systeem gedefinieerde eenheden: eenheid, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kwik, PSI, MilliMeter, CentiMeter, meter, inch, meter, mijl, kilo meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, seconden, UnixTimestamp, MicroMolPerMeterSquaredPerSecond en InchesPerHour. Raadpleeg de/ExtendedType-API om meer toe te voegen.
  SensorMeasures > AggregationType  | Geen, gemiddelde, maximum, minimum of StandardDeviation.
  Diepte van SensorMeasures->  | De diepte van de sensor in centimeters. Bijvoorbeeld de meting van het vocht 10 cm onder het wegdek.
  Beschrijving van SensorMeasures->  | Geef een duidelijke beschrijving van de meting op.
  Name  | Naam voor het identificeren van de resource. Bijvoorbeeld de naam van het model of de product naam.
  Beschrijving  | Geef een zinvolle beschrijving van het model op.
  Eigenschappen  | Aanvullende eigenschappen van de fabrikant.
  **Sensoren**  | **Beschrijving** |
  HardwareId  | De unieke ID voor de sensor die door de fabrikant is ingesteld.
  SensorModelId  | ID van het gekoppelde sensor model.
  Locatie  | Sensor Latitude (-90 tot + 90), lengte graad (-180 tot 180) en uitbrei ding (in meters).
  Naam van poort >  |Naam en type van de poort waarop de sensor is aangesloten op het apparaat. Dit moet dezelfde naam zijn als die is gedefinieerd in het model van het apparaat.
  DeviceId  | ID van het apparaat waarmee de sensor is verbonden.
  Name  | Naam om de resource te identificeren. Bijvoorbeeld, de naam van de sensor of de product naam en het model nummer of de product code.
  Beschrijving  | Geef een zinvolle beschrijving op.
  Eigenschappen  | Aanvullende eigenschappen van de fabrikant.

 Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over elk van de objecten en hun eigenschappen.

 > [!NOTE]
 > De Api's retour neren unieke Id's voor elk gemaakt exemplaar. Deze ID moet worden behouden door de vertaler voor het synchroniseren van apparaten en meta gegevens.


**Meta gegevens synchroniseren**

Het conversie programma moet updates voor de meta gegevens verzenden. Update scenario's zijn bijvoorbeeld wijziging van apparaat-of sensor naam en wijziging van apparaat of sensor locatie.

Het conversie programma moet de mogelijkheid hebben om nieuwe apparaten of Sens oren toe te voegen die zijn geïnstalleerd door de gebruiker na het koppelen van FarmBeats. Als een apparaat of sensor door de gebruiker is bijgewerkt, moet dit ook worden bijgewerkt in FarmBeats voor het betreffende apparaat of de bijbehorende sensor. Typische scenario's waarbij een apparaat of sensor moet worden bijgewerkt, zijn een wijziging in de locatie van een apparaat of het toevoegen van Sens oren in een knoop punt.


> [!NOTE]
> Verwijderen wordt niet ondersteund voor meta gegevens van apparaten of Sens oren.
>
> Voor het bijwerken van meta gegevens is het verplicht om/Get/{id} aan te roepen op het apparaat of de sensor, de gewijzigde eigenschappen bij te werken en vervolgens een/put/{id} te maken, zodat alle door de gebruiker ingestelde eigenschappen niet verloren gaan.

### <a name="add-new-types-and-units"></a>Nieuwe typen en eenheden toevoegen

FarmBeats biedt ondersteuning voor het toevoegen van nieuwe sensor metings typen en-eenheden. Zie [Swagger](https://aka.ms/FarmBeatsSwagger)voor meer informatie over de/EXTENDEDTYPE-API.

## <a name="telemetry-specifications"></a>Specificaties voor telemetrie

De telemetriegegevens worden toegewezen aan een canonieke bericht dat wordt gepubliceerd op Azure Event Hubs voor verwerking. Azure Event Hubs is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetriegegevens naar FarmBeats verzenden

Als u telemetriegegevens wilt verzenden naar FarmBeats, maakt u een client die berichten verzendt naar een Event Hub in FarmBeats. Zie [telemetrie verzenden naar een event hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)voor meer informatie over telemetrie-gegevens.

Hier volgt een voor beeld van een python-code die telemetrie verzendt als een client naar een opgegeven Event Hub.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

De canonieke bericht indeling is als volgt:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
Alle sleutel namen in de telemetrie-JSON moeten kleine letters zijn. Voor beelden zijn DeviceID en sensordata.

Hier volgt bijvoorbeeld een telemetrie-bericht:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> De volgende secties zijn gerelateerd aan andere wijzigingen (bijvoorbeeld UI, fout beheer etc.) dat de sensor partner kan verwijzen naar het ontwikkelen van het Translator-onderdeel.


## <a name="link-a-farmbeats-account"></a>Een FarmBeats-account koppelen

Nadat klanten apparaten of Sens oren hebben gekocht en geïmplementeerd, hebben ze toegang tot de apparaatgegevens en telemetrie op de SaaS-Portal (Software as a Service) van het apparaat. Met apparaat-partners kunnen klanten hun account koppelen aan hun FarmBeats-exemplaar in azure door de volgende referenties op te geven:

   - Weergave naam (een optioneel veld voor gebruikers voor het definiëren van een naam voor deze integratie)
   - API-eindpunt
   - Tenant-id
   - Client-id
   - Clientgeheim
   - EventHub connection string
   - Begindatum

   > [!NOTE]
   > De begin datum maakt de historische gegevensfeed, dat wil zeggen, de gegevens van de datum die door de gebruiker is opgegeven.

## <a name="unlink-farmbeats"></a>FarmBeats ontkoppelen

Met apparaat-partners kunnen klanten een bestaande FarmBeats-integratie ontkoppelen. Het ontkoppelen van FarmBeats mag geen apparaat-of sensor-meta gegevens verwijderen die zijn gemaakt in FarmBeats Datahub. Het ontkoppelen gaat als volgt:

   - Hiermee wordt de telemetrische stroom gestopt.
   - Hiermee verwijdert en wist u de integratie referenties op de partner van het apparaat.

## <a name="edit-farmbeats-integration"></a>FarmBeats-integratie bewerken

Met apparaat-partners kunnen klanten de FarmBeats-integratie-instellingen bewerken als het client geheim of het connection string verandert. In dit geval zijn alleen de volgende velden bewerkbaar:

   - Weergave naam (indien van toepassing)
   - Client geheim (moet worden weer gegeven in de indeling ' 2x8 * * * * * * * * * * * * ' of ' weer geven/verbergen ' in plaats van gewone tekst)
   - Verbindings reeks (moet worden weer gegeven in de indeling ' 2x8 * * * * * * * * * * * * ' of ' weer geven/verbergen ' in plaats van gewone tekst)

## <a name="view-the-last-telemetry-sent"></a>De laatste telemetriegegevens weer geven die zijn verzonden

Met apparaat-partners kunnen klanten de tijds tempel van de laatste gezonden telemetrie weer geven, die wordt gevonden onder **telemetrie verzonden**. Dit is het tijdstip waarop de laatste telemetrie is verzonden naar FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Probleem oplossing en fouten beheer

**Problemen met de optie of ondersteuning oplossen**

Als de klant geen apparaatgegevens of telemetrie kan ontvangen in het opgegeven FarmBeats-exemplaar, moet de apparaat-partner ondersteuning en een mechanisme voor het oplossen van problemen bieden.

**Bewaren van telemetrie-gegevens**

De telemetriegegevens moeten ook worden bewaard gedurende een vooraf gedefinieerde periode, zodat deze kan worden gebruikt bij het opsporen van fouten of het opnieuw verzenden van de telemetrie als er sprake is van een fout of gegevens verlies.

**Fout beheer of fout melding**

Als er een fout optreedt in de meta gegevens van het apparaat of de sensor of de gegevens stroom voor gegevens integratie of telemetrie in het apparaat-partner systeem, moet de klant een melding ontvangen. Een mechanisme om eventuele fouten op te lossen moet ook worden ontworpen en geïmplementeerd.

**Controle lijst voor verbindingen**

De fabrikant of partners van het apparaat kunnen de volgende controle lijst gebruiken om ervoor te zorgen dat de door de klant verschafte referenties nauw keurig zijn:

   - Controleer of er een toegangs token is ontvangen met de referenties die zijn geleverd.
   - Controleer of een API-aanroep slaagt met het toegangs token dat is ontvangen.
   - Controleer of de EventHub-client verbinding tot stand is gebracht.

## <a name="next-steps"></a>Volgende stappen

Zie [rest API](rest-api-in-azure-farmbeats.md)voor meer informatie over de rest API.