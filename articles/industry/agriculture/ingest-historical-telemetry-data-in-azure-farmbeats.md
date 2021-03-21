---
title: Historische telemetriegegevens opnemen
description: In dit artikel wordt beschreven hoe u historische telemetriegegevens opneemt.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.custom: has-adal-ref
ms.openlocfilehash: e4182f3718422be3a0aa99c980d33026df4ce9af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173746"
---
# <a name="ingest-historical-telemetry-data"></a>Historische telemetriegegevens opnemen

In dit artikel wordt beschreven hoe u historische sensor gegevens opneemt in azure FarmBeats.

Het opnemen van historische gegevens uit Internet of Things IoT-resources zoals apparaten en Sens oren is een veelvoorkomend scenario in FarmBeats. U maakt meta gegevens voor apparaten en Sens oren en vervolgens neemt u de historische data op in een canonieke notatie van FarmBeats.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u verder gaat met dit artikel, moet u ervoor zorgen dat u FarmBeats hebt geïnstalleerd en historische gegevens hebt verzameld van uw IoT-apparaten. U moet ook toegang tot de partner inschakelen zoals vermeld in de volgende stappen.

## <a name="enable-partner-access"></a>Partner toegang inschakelen

U moet de partner integratie inschakelen voor uw Azure FarmBeats-exemplaar. Met deze stap maakt u een client die toegang heeft tot uw Azure FarmBeats-exemplaar als uw apparaat-partner en biedt u de volgende waarden die vereist zijn in de volgende stappen:

- API-eind punt: dit is de Datahub-URL, bijvoorbeeld https:// \<datahub> . azurewebsites.net
- Tenant-id
- Client-id
- Clientgeheim
- EventHub connection string

Volg deze stappen:

> [!NOTE]
> U moet een beheerder zijn om de volgende stappen uit te voeren.

1. Meld u aan bij https://portal.azure.com/.

2. **Als u FarmBeats-versie 1.2.7 of hoger hebt, slaat u stap a, b en c over en gaat u naar stap 3.** U kunt de FarmBeats-versie controleren door het pictogram **instellingen** te selecteren in de rechter bovenhoek van de FarmBeats-gebruikers interface.

      a.  Ga naar   >  **app-registraties** voor Azure Active Directory

      b. Selecteer de **app-registratie** die is gemaakt als onderdeel van uw FarmBeats-implementatie. Deze heeft dezelfde naam als uw FarmBeats-datahub.

      c. Selecteer **een API beschikbaar** maken > Selecteer **een client toepassing toevoegen** en voer **04B07795-8ddb-461A-bbee-02f9e1bf7b46** in en controleer de **Scope voor autoriseren**. Hiermee krijgt u toegang tot de Azure CLI (Cloud Shell) om de onderstaande stappen uit te voeren:

3. Open Cloud Shell. Deze optie is beschikbaar op de werk balk in de rechter bovenhoek van de Azure Portal.

    ![Azure Portal werk balk](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Zorg ervoor dat de omgeving is ingesteld op **Power shell**. Standaard is deze ingesteld op bash.

    ![Power shell-werkbalk instelling](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Ga naar de basismap.

    ```azurepowershell-interactive
    cd
    ```

6. Voer de volgende opdracht uit. Hiermee wordt een geverifieerde account verbonden voor Azure AD-aanvragen

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Voer de volgende opdracht uit. Hiermee wordt een script gedownload naar uw basismap.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Voer het volgende script uit. Het script vraagt om de Tenant-id, die kan worden verkregen van **Azure Active Directory**  >  **overzichts** pagina.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. Volg de instructies op het scherm voor het vastleggen van de waarden voor het **API-eind punt**, **Tenant-ID**, **client-id**, **client geheim** en **EventHub-verbindings reeks**.


## <a name="create-device-or-sensor-metadata"></a>Meta gegevens van het apparaat of de sensor maken

 Nu u de vereiste referenties hebt, kunt u het apparaat en de Sens oren definiëren. Als u dit wilt doen, maakt u de meta gegevens door FarmBeats-Api's aan te roepen. Zorg ervoor dat u de Api's aanroept als de client-app die u in de bovenstaande sectie hebt gemaakt.

 FarmBeats Datahub heeft de volgende Api's waarmee het maken en beheren van meta gegevens van apparaten of Sens oren kan worden ingeschakeld.

 > [!NOTE]
 > Als partner hebt u alleen toegang om de meta gegevens te lezen, te maken en bij te werken. **de optie voor verwijderen is beperkt tot de partner.**

- /**DeviceModel**: DeviceModel komt overeen met de meta gegevens van het apparaat, zoals de fabrikant en het type apparaat, ofwel een gateway of een knoop punt.
- /**Apparaat**: het apparaat komt overeen met een fysiek apparaat dat aanwezig is op de farm.
- /**SensorModel**: SensorModel komt overeen met de meta gegevens van de sensor, zoals de fabrikant, het type sensor dat analoog of digitaal is en de sensor meting, zoals omgevings temperatuur en druk.
- /**Sensor**: sensor komt overeen met een fysieke sensor waarmee waarden worden vastgelegd. Een sensor is doorgaans verbonden met een apparaat met een apparaat-ID.

| DeviceModel | Suggesties |
|--|--|
| Type (knoop punt, gateway) | Type van het apparaat of de gateway |
| Fabrikant | De naam van de fabrikant |
| Code | Product code of model naam of-nummer van het apparaat. Bijvoorbeeld EnviroMonitor # 6800. |
| Poorten | Poort naam en-type, digitaal of analoog. |
| Name | Naam om de resource te identificeren. Bijvoorbeeld de naam van het model of de product naam. |
| Beschrijving | Geef een zinvolle beschrijving van het model op. |
| Eigenschappen | Aanvullende eigenschappen van de fabrikant. |
| **Apparaat** |  |
| DeviceModelId | ID van het gekoppelde model. |
| HardwareId | De unieke ID voor het apparaat, zoals het MAC-adres. |
| ReportingInterval | Rapportage-interval in seconden. |
| Locatie | Apparaat Latitude (-90 tot + 90), lengte graad (-180 tot 180) en uitbrei ding (in meters). |
| ParentDeviceId | De ID van het bovenliggende apparaat waarmee dit apparaat is verbonden. Bijvoorbeeld een knoop punt dat is verbonden met een gateway. Een knoop punt heeft parentDeviceId als de gateway. |
| Name | Een naam om de resource te identificeren. Apparaat-partners moeten een naam verzenden die consistent is met de apparaatnaam aan de partner zijde. Als de naam van de partner apparaat door de gebruiker is gedefinieerd, moet dezelfde door de gebruiker gedefinieerde naam worden door gegeven aan FarmBeats. |
| Beschrijving | Geef een zinvolle beschrijving op. |
| Eigenschappen | Aanvullende eigenschappen van de fabrikant. |
| **SensorModel** |  |
| Type (analoog, digitaal) | Het type sensor, of het analoog of digitaal is. |
| Fabrikant | De fabrikant van de sensor. |
| Code | Product code of model naam of-nummer. Bijvoorbeeld RS-CO2-N01. |
| > naam SensorMeasures | De naam van de sensor meting. Alleen kleine letters worden ondersteund. Geef voor metingen uit verschillende diepten de diepte op. Bijvoorbeeld soil_moisture_15cm. Deze naam moet consistent zijn met de telemetriegegevens. |
| SensorMeasures > data type | Het gegevens type telemetrie. Op dit moment wordt dubbele wordt ondersteund. |
| SensorMeasures > type | Meet type van de telemetrie van de sensor. De door het systeem gedefinieerde typen zijn AmbientTemperature, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitraat, O2, PH, fosfaat, PointInTime, kalium, druk, RainGauge, RelativeHumidity, zout, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Raadpleeg de/ExtendedType-API om meer toe te voegen. |
| SensorMeasures >-eenheid | De gegevens eenheid van de sensor-telemetrie. De door het systeem gedefinieerde eenheden zijn geen eenheid, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kwik, PSI, MilliMeter, CentiMeter, meter, inch, meter, mijl, kilo meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, het PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar,,,, DeciSiemensPerMeter en KiloPascal. Als u meer wilt toevoegen, raadpleegt u de VolumetricIonContent-API. |
| SensorMeasures > AggregationType | De waarden kunnen none, Average, maximum, minimum of StandardDeviation zijn. |
| Name | Naam voor het identificeren van een resource. Bijvoorbeeld de naam van het model of de product naam. |
| Beschrijving | Geef een zinvolle beschrijving van het model op. |
| Eigenschappen | Aanvullende eigenschappen van de fabrikant. |
| **Sensoren** |  |
| HardwareId | De unieke ID voor de sensor die door de fabrikant is ingesteld. |
| SensorModelId | ID van het gekoppelde sensor model. |
| Locatie | Sensor Latitude (-90 tot + 90), lengte graad (-180 tot 180) en uitbrei ding (in meters). |
| Naam van poort > | Naam en type van de poort waarop de sensor is aangesloten op het apparaat. Dit moet overeenkomen met de naam die is gedefinieerd in het model apparaat. |
| DeviceID | ID van het apparaat waarmee de sensor is verbonden. |
| Name | Naam voor het identificeren van de resource. Bijvoorbeeld de naam van de sensor of de product naam en het model nummer of de product code. |
| Beschrijving | Geef een zinvolle beschrijving op. |
| Eigenschappen | Aanvullende eigenschappen van de fabrikant. |

Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over objecten.

### <a name="api-request-to-create-metadata"></a>API-aanvraag voor het maken van meta gegevens

Als u een API-aanvraag wilt maken, combineert u de HTTP-methode (POST), de URL naar de API-service en de URI naar een resource om een query uit te voeren, gegevens te verzenden naar, te maken of te verwijderen. Vervolgens voegt u een of meer HTTP-aanvraag headers toe. De URL van de API-service is het API-eind punt, dat wil zeggen, de Datahub-URL (https:// \<yourdatahub> . azurewebsites.net).

### <a name="authentication"></a>Verificatie

FarmBeats Datahub maakt gebruik van Bearer-verificatie, die de volgende referenties nodig heeft die zijn gegenereerd in de vorige sectie:

- Client-id
- Clientgeheim
- Tenant-id

Met deze referenties kan de aanroeper een toegangs token aanvragen. Het token moet worden verzonden in de volgende API-aanvragen, in de koptekst sectie, als volgt:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

De volgende voor beeld-python-code geeft het toegangs token, dat kan worden gebruikt voor volgende API-aanroepen naar FarmBeats: 

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

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven wanneer u een API-aanroep maakt naar FarmBeats Datahub:

- **Content-type**: Application/JSON
- **Autorisatie**: bearer <Access-Token>
- **Accepteren**: toepassing/JSON

### <a name="input-payload-to-create-metadata"></a>Invoer lading voor het maken van meta gegevens

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Apparaat

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensoren

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Met de volgende voorbeeld aanvraag wordt een apparaat gemaakt. Deze aanvraag bevat de invoer JSON als Payload met de aanvraag tekst.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",
\"reportingInterval\": 900,  \"name\": \"Device123\",
\"description\": \"Test Device 123\"}" *
```

Hieronder volgt een voorbeeld code in python. Het toegangs token dat in dit voor beeld wordt gebruikt, is hetzelfde als tijdens de verificatie.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> De Api's retour neren unieke Id's voor elk gemaakt exemplaar. U moet de Id's bewaren om de bijbehorende telemetrie-berichten te verzenden.

### <a name="send-telemetry"></a>Telemetrie verzenden

Nu u de apparaten en Sens oren in FarmBeats hebt gemaakt, kunt u de bijbehorende telemetrie-berichten verzenden.

### <a name="create-a-telemetry-client"></a>Een telemetrie-client maken

U moet de telemetrie naar Azure Event Hubs verzenden voor verwerking. Azure Event Hubs is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen. Als u telemetriegegevens wilt verzenden naar FarmBeats, maakt u een client die berichten verzendt naar een Event Hub in FarmBeats. Zie [Azure Event hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)voor meer informatie over het verzenden van telemetrie.

### <a name="send-a-telemetry-message-as-the-client"></a>Een telemetrie-bericht verzenden als de client

Nadat u verbinding hebt gemaakt met een Event Hubs-client, kunt u berichten verzenden naar de Event Hub als JSON-bestand.

Hier volgt een voor beeld van een python-code die telemetrie verzendt als een client naar een opgegeven Event Hub:

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

Converteer de historische sensor gegevens indeling naar een canonieke indeling die door Azure FarmBeats wordt begrepen. De canonieke bericht indeling is als volgt:

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

Nadat u de overeenkomende apparaten en Sens oren hebt toegevoegd, moet u de apparaat-ID en de sensor-ID in het telemetrie-bericht ophalen, zoals beschreven in de vorige sectie.

Hier volgt een voor beeld van een telemetrie-bericht:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Er kunnen geen telemetriegegevens worden weer gegeven na het opnemen van historische/streaming-gegevens van uw Sens oren

**Symptoom**: er zijn apparaten of Sens oren geïmplementeerd en u hebt de apparaten/Sens oren op FarmBeats en opgenomen telemetrie naar de EventHub gemaakt, maar u kunt geen telemetriegegevens ophalen of weer geven op FarmBeats.

**Corrigerende actie**:

1. Zorg ervoor dat u de juiste partner registratie hebt uitgevoerd. u kunt dit controleren door naar de datahub Swagger te gaan, naar/partner API te gaan en te controleren of de partner is geregistreerd. Als dat niet het geval is, volgt u de [stappen hier](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) om een partner toe te voegen.

2. Zorg ervoor dat u de meta gegevens (DeviceModel, Device, SensorModel, sensor) hebt gemaakt met behulp van de referenties van de partner-client.

3. Zorg ervoor dat u de juiste indeling voor telemetrie-berichten hebt gebruikt (zoals hieronder is opgegeven):

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

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over op REST API gebaseerde integratie Details [rest API](rest-api-in-azure-farmbeats.md).