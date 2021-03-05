---
title: Partnerintegratie voor afbeeldingen
description: In dit artikel wordt de installatie kopie van de partner beschreven.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.custom: has-adal-ref
ms.openlocfilehash: 1bc723892e29ccc24a7612cfbe50d4b274cdacdf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183791"
---
# <a name="imagery-partner-integration"></a>Partnerintegratie voor afbeeldingen

In dit artikel wordt beschreven hoe u het onderdeel Azure FarmBeats Translator gebruikt om afbeeldings gegevens naar FarmBeats te verzenden. Gegevens over agrarische beelden kunnen worden gegenereerd op basis van verschillende bronnen, zoals MultiSpectral-camera's, satellieten en drones. Agrarische installatie kopieën kunnen worden geïntegreerd met FarmBeats om klanten met aangepaste, gegenereerde kaarten voor hun farms te voorzien.

Gegevens, die beschikbaar zijn, kunnen worden gevisualiseerd via de FarmBeats-Accelerator en mogelijk worden gebruikt voor gegevens fusie en het model van machine learning/kunst matige intelligentie (ML/AI) dat is gebaseerd op agrarische ondernemingen of integrators van klant systemen.

FarmBeats biedt de mogelijkheid om het volgende te doen:

- Definieer aangepaste afbeeldings typen, bron en bestands indeling met behulp van/ExtendedType-Api's.
- Opname gegevens uit verschillende bronnen opnemen via de/scene-en/SceneFile-Api's.

De volgende informatie is gericht op het ophalen van een wille keurige vorm van installatie kopieën in het FarmBeats-systeem.

Wanneer u de sectie **drone installatie kopie** selecteert, wordt er een pop-up geopend met een afbeelding met een hoge resolutie van de drone-orthomosaic. U hebt toegang tot de partner software, die helpt om drone vluchten te plannen en onbewerkte gegevens op te halen. U kunt de software van de partner blijven gebruiken voor het plannen van paden en orthomosaic-installatie kopieën.

Drone-partners moeten klanten in staat stellen hun klant account te koppelen aan hun FarmBeats-exemplaar in Azure.

U moet de volgende referenties in de drone-partner software gebruiken om FarmBeats te koppelen:

- API-eindpunt
- Tenant-id
- Client-id
- Clientgeheim

## <a name="api-development"></a>API-ontwikkeling

De Api's bevatten technische documentatie voor Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over de api's en bijbehorende aanvragen of antwoorden.

## <a name="authentication"></a>Verificatie

FarmBeats maakt gebruik van Microsoft Azure [Active Directory](../../app-service/overview-authentication-authorization.md) (Azure AD). Azure App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie. 

Zie [Azure Active Directory](../../app-service/overview-authentication-authorization.md)voor meer informatie over Azure AD.   

FarmBeats Datahub maakt gebruik van Bearer-verificatie, die de volgende referenties nodig heeft:

- Client-id
- Clientgeheim
- Tenant-id

Met behulp van de voor gaande referenties kan de aanroeper een toegangs token aanvragen dat in de volgende API-aanvragen in de koptekst sectie als volgt moet worden verzonden:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Het volgende python-code voorbeeld haalt het toegangs token op. U kunt vervolgens het token gebruiken voor de volgende API-aanroepen naar FarmBeats.

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

## <a name="http-request-headers"></a>HTTP-aanvraag headers

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven wanneer u een API-aanroep maakt naar FarmBeats Datahub.

**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type  | De aanvraag indeling (content-type: Application/ <format> ). Voor FarmBeats Datahub-Api's is de indeling JSON. Content-type: Application/JSON
Autorisatie | Hiermee geeft u het toegangs token op dat vereist is om een API-aanroep te maken. Autorisatie: Bearer <Access-Token>
Accepteren  | De antwoord indeling. Voor FarmBeats Datahub-Api's is de indeling JSON. Accepteren: toepassing/JSON


## <a name="api-requests"></a>API-aanvragen

Als u een REST API aanvraag wilt maken, moet u het volgende combi neren:

- De HTTP-methode (GET, POST en PUT).
- De URL van de API-service.
- De resource-URI (om gegevens op te vragen, in te dienen, bij te werken of te verwijderen).
- Een of meer HTTP-aanvraag headers.

U kunt desgewenst query parameters toevoegen aan GET-aanroepen om te filteren, de grootte van de gegevens in de antwoorden te beperken en te sorteren.

De volgende voorbeeld aanvraag is om de lijst met apparaten op te halen:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Voor de meeste GET-, POST-en PUT-aanroepen is een JSON-aanvraag tekst vereist.

De volgende voorbeeld aanvraag is het maken van een apparaat. Dit voor beeld heeft een invoer-JSON met de aanvraag tekst.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Gegevensindeling

JSON is een gemeen schappelijke taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie [JSON org](https://JSON.org)(Engelstalig) voor meer informatie.

## <a name="ingest-imagery-into-farmbeats"></a>Afbeeldingen opnemen in FarmBeats

Nadat de partner referenties heeft om verbinding te maken met FarmBeats Datahub, voert de partner de volgende stappen uit in het onderdeel Translator.

1.  Maak een nieuw uitgebreid type voor de volgende velden, in overeenstemming met het type afbeelding dat moet worden geüpload:

    - **Scène bron**: bijvoorbeeld drone_partner_name
    - **Type scène**: bijvoorbeeld Drone
    - **Type scène bestand**: bijvoorbeeld Chlorophyll index
    - **Type inhoud van scène bestand**: bijvoorbeeld afbeelding/TIFF

2.  Roep de/Farms-API aan om de lijst met Farms op te halen in het Azure FarmBeats-systeem.
3.  Geef de klant de mogelijkheid om één farm te kiezen uit de lijst met farms.

    Het partner systeem moet de farm binnen de partner software weer geven om de drone-vlucht en-installatie kopie verzameling uit te voeren.

4.  Roep de/scene-API aan en geef de vereiste gegevens op om een nieuwe scène te maken met een unieke scène-ID.
5.  Ontvang een BLOB SAS-URL voor het uploaden van de vereiste installatie kopieën in FarmBeats Datahub, in de context van de gekozen Farm, in het FarmBeats-systeem.

Hier volgt een gedetailleerde stroom van de API-aanroepen.

### <a name="step-1-extendedtype"></a>Stap 1: Extended type

Raadpleeg de/ExtendedType-API om te zien of het type en de bestands bron beschikbaar zijn op FarmBeats. Als u dit wilt doen, roept u de/ExtendedType-API aan.

Dit zijn de door het systeem gedefinieerde waarden:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Deze stap is een eenmalige installatie. Het bereik van dit nieuwe type scène is beperkt tot het abonnement waarin de Azure-FarmBeats is geïnstalleerd.

Bijvoorbeeld, om SceneSource toe te voegen: "SlantRange", voert u een PUT uit op de ID van de/ExtendedType-API met de sleutel "SceneSource" invoer payload.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Het groene veld is de nieuwe toevoeging aan de door het systeem gedefinieerde scène bron waarden.

### <a name="step-2-get-farm-details"></a>Stap 2: Farm Details ophalen

De scènes (TIFF-of CSV-bestanden) bevinden zich in de context van een farm. U moet de gegevens van de farm ophalen met behulp van de/farm-API. De API retourneert de lijst met Farms die beschikbaar zijn in FarmBeats. U kunt de farm selecteren waarvoor u de gegevens wilt opnemen.

/Farm-antwoord ophalen:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Stap 3: een scène-ID maken (POST-aanroep)

Maak een nieuw scène-(TIFF-of CSV-bestand) met de opgegeven informatie, die de datum, de volg orde en de farm-ID bevat waarmee de scène is gekoppeld. De meta gegevens die aan de scène zijn gekoppeld, kunnen worden gedefinieerd onder eigenschappen, die de duur en het type meting bevatten.

Als u een nieuwe scène maakt, wordt er een nieuwe scène-ID gemaakt, die is gekoppeld aan de farm. Nadat de scène-ID is gemaakt, kan de gebruiker dezelfde gebruiken om een nieuw bestand (. TIFF of. CSV) te maken en de inhoud van het bestand op te slaan.

Voor beeld van een nettolading voor de POST-aanroep van de/scene-API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API-antwoord:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Een scène bestand maken**

De scène-ID die u in stap 3 hebt geretourneerd, is de invoer voor het scène bestand. Het scène bestand retourneert een SAS-URL-token dat 24 uur geldig is.

Als de gebruiker een gegevens stroom van installatie kopieën op een programmatische manier moet uploaden, kan de SDK voor Blob Storage worden gebruikt voor het definiëren van een methode met behulp van de ID, locatie en URL van het scène bestand.

Voor beeld van een nettolading voor de POST-aanroep van de/SceneFile-API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API-antwoord:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

De POST-aanroep van de/SceneFile-API retourneert een SAS-upload-URL, die kan worden gebruikt om het. CSV-of. TIFF-bestand te uploaden met behulp van de Azure Blob Storage-client of-bibliotheek.


## <a name="next-steps"></a>Volgende stappen

Zie [rest API](rest-api-in-azure-farmbeats.md)voor meer informatie over op rest API gebaseerde integratie Details.