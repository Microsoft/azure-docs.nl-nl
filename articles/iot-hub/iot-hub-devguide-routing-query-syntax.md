---
title: Query uitvoeren op Azure IoT Hub routeringsgegevens | Microsoft Docs
description: Meer informatie over de IoT Hub querytaal voor berichtroutering die u kunt gebruiken om uitgebreide query's toe te passen op berichten om de gegevens te ontvangen die voor u belangrijk zijn.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: c4ba48377d868404ff130ec458e50e2b42fae977
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790513"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Querysyntaxis voor IoT Hub-berichtroutering

Met berichtroutering kunnen gebruikers verschillende gegevenstypen routeren: telemetrieberichten van apparaten, levenscyclusgebeurtenissen van apparaten en wijzigingsgebeurtenissen van apparaattwee naar verschillende eindpunten. U kunt ook uitgebreide query's op deze gegevens toepassen voordat u deze doorsturen om de gegevens te ontvangen die voor u belangrijk zijn. In dit artikel wordt de IoT Hub querytaal voor berichtroutering beschreven en vindt u enkele algemene querypatronen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met berichtroutering kunt u query's uitvoeren op de berichteigenschappen en bericht body, evenals tags van apparaattwee en eigenschappen van apparaattwee. Als de bericht-body niet JSON is, kan berichtroutering het bericht nog steeds routeren, maar kunnen query's niet worden toegepast op de bericht body.  Query's worden beschreven als Booleaanse expressies waarbij een Booleaanse true de query slaagt, waardoor alle binnenkomende gegevens worden gerouteerd en Booleaanse false de query mislukt en er geen gegevens worden gerouteerd. Als de expressie null of niet-gedefinieerd is, wordt deze beschouwd als onwaar en wordt er een fout gegenereerd in IoT Hub [routet](monitor-iot-hub-reference.md#routes) u resourcelogboeken in geval van een fout. De querysyntaxis moet juist zijn om de route te kunnen opgeslagen en geëvalueerd.  

## <a name="message-routing-query-based-on-message-properties"></a>Berichtrouteringsquery op basis van berichteigenschappen 

De IoT Hub definieert een [algemene indeling](iot-hub-devguide-messages-construct.md) voor alle apparaat-naar-cloud-berichten voor interoperabiliteit tussen protocollen. IoT Hub bericht wordt uit van de volgende JSON-weergave van het bericht. Systeemeigenschappen worden toegevoegd voor alle gebruikers en identificeren de inhoud van het bericht. Gebruikers kunnen selectief toepassingseigenschappen toevoegen aan het bericht. We raden u aan unieke eigenschapsnamen te gebruiken IoT Hub apparaat-naar-cloud-berichten niet casegevoelig zijn. Als u bijvoorbeeld meerdere eigenschappen met dezelfde naam hebt, IoT Hub slechts één van de eigenschappen verzenden.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systeemeigenschappen

Systeemeigenschappen helpen bij het identificeren van inhoud en de bron van de berichten. 

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| Contenttype | tekenreeks | De gebruiker geeft het inhoudstype van het bericht op. Als u query's wilt toestaan voor de bericht body, moet deze waarde worden ingesteld op application/JSON. |
| contentEncoding | tekenreeks | De gebruiker geeft het coderingstype van het bericht op. Toegestane waarden zijn UTF-8, UTF-16, UTF-32 als contentType is ingesteld op application/JSON. |
| iothub-connection-device-id | tekenreeks | Deze waarde wordt ingesteld door IoT Hub en identificeert de id van het apparaat. Gebruik om een query uit te `$connectionDeviceId` voeren. |
| iothub-enqueuedtime | tekenreeks | Deze waarde wordt ingesteld door IoT Hub en vertegenwoordigt de werkelijke tijd van het enqueuing van het bericht in UTC. Gebruik om een query uit te `enqueuedTime` voeren. |
| dt-dataschema | tekenreeks |  Deze waarde wordt ingesteld door IoT Hub voor apparaat-naar-cloud-berichten. Deze bevat de apparaatmodel-id die is ingesteld in de apparaatverbinding. Gebruik om een query uit te `$dt-dataschema` voeren. |
| dt-subject | tekenreeks | De naam van het onderdeel dat de apparaat-naar-cloud-berichten verstuurt. Gebruik om een query uit te `$dt-subject` voeren. |

Zoals beschreven in [IoT Hub Berichten](iot-hub-devguide-messages-construct.md), zijn er aanvullende systeemeigenschappen in een bericht. Naast de bovenstaande eigenschappen in de vorige tabel kunt u ook een query uitvoeren **op connectionDeviceId,** **connectionModuleId.**

### <a name="application-properties"></a>Toepassingseigenschappen

Toepassingseigenschappen zijn door de gebruiker gedefinieerde tekenreeksen die aan het bericht kunnen worden toegevoegd. Deze velden zijn optioneel.  

### <a name="query-expressions"></a>Query-expressies

Een query op de eigenschappen van het berichtsysteem moet worden voorafgevoegd met het `$` symbool. Query's op toepassingseigenschappen worden gebruikt met hun naam en mogen niet worden voorafgevoegd met het `$` -symbool. Als de naam van een toepassingseigenschappen begint met , zoekt IoT Hub ernaar in de systeemeigenschappen en wordt deze niet gevonden. Vervolgens wordt deze in de eigenschappen van `$` de toepassing gezocht. Bijvoorbeeld: 

Een query uitvoeren op inhoud van systeem-eigenschappenCoderen 

```sql
$contentEncoding = 'UTF-8'
```

Een query uitvoeren op application property processingPath:

```sql
processingPath = 'hot'
```

Als u deze query's wilt combineren, kunt u Boole-expressies en -functies gebruiken:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Een volledige lijst met ondersteunde operators en functies wordt weergegeven in [Expressie en voorwaarden.](iot-hub-devguide-query-language.md#expressions-and-conditions)

## <a name="message-routing-query-based-on-message-body"></a>Berichtrouteringsquery op basis van bericht bericht

Als u query's wilt inschakelen voor de bericht body, moet het bericht zich in een JSON-code in UTF-8, UTF-16 of UTF-32. De `contentType` moet worden ingesteld op en op een van de `application/JSON` `contentEncoding` ondersteunde UTF-coderingen in de systeem-eigenschap. Als deze eigenschappen niet zijn opgegeven, IoT Hub de query-expressie op de berichtbe body niet evalueren. 

In het volgende voorbeeld ziet u hoe u een bericht maakt met een correct gevormde en gecodeerde JSON-body: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Dit laat zien hoe u de codering van de body in JavaScript verwerkt. Als u een voorbeeld in C# wilt zien, downloadt u [de Azure IoT C#-voorbeelden.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Pak het bestand master.zip uit. In Visual Studio bestand *SimulatedDevice* van de oplossing Program.cs ziet u hoe u berichten kunt coderen en verzenden naar een IoT Hub. Dit is hetzelfde voorbeeld dat wordt gebruikt voor het testen van de berichtroutering, zoals uitgelegd in de [zelfstudie Berichtroutering.](tutorial-routing.md) Aan de onderkant van Program.cs heeft het ook een methode om een van de gecodeerde bestanden te lezen, te decoderen en weer weg te schrijven als ASCII, zodat u deze kunt lezen. 


### <a name="query-expressions"></a>Query-expressies

Een query op de bericht-body moet worden voorafgevoegd met de `$body` . U kunt een verwijzing naar de body, verwijzing naar de matrix van de body of meerdere verwijzingen naar de body gebruiken in de queryexpressie. Uw queryexpressie kan ook een hoofdverwijzing combineren met systeemeigenschappen van berichten en verwijzing naar berichttoepassingseigenschappen. Het volgende zijn bijvoorbeeld allemaal geldige query-expressies: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

> [!NOTE] 
> U kunt query's en functies alleen uitvoeren op eigenschappen in de naslag voor de body. U kunt geen query's of functies uitvoeren op de volledige naslag voor de body. De volgende query wordt bijvoorbeeld *niet ondersteund en* retournt `undefined` :
> 
> ```sql
> $body[0] = 'Feb'
> ```

## <a name="message-routing-query-based-on-device-twin"></a>Query voor berichtroutering op basis van apparaat dubbel 

Met berichtroutering kunt u query's uitvoeren [op tags](iot-hub-devguide-device-twins.md) en eigenschappen van apparaattwee, die JSON-objecten zijn. Query's uitvoeren op module twin wordt ook ondersteund. Hieronder vindt u een voorbeeld van tags en eigenschappen voor apparaattweeling.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Query-expressies

Een query op de berichtent twin moet worden voorafgevoegd met de `$twin` . Uw query-expressie kan ook een dubbele tag of verwijzing naar eigenschappen combineren met een hoofdbeslag, systeemeigenschappen van berichten en verwijzing naar berichttoepassingseigenschappen. We raden u aan unieke namen te gebruiken in tags en eigenschappen, omdat de query niet case-gevoelig is. Dit geldt voor zowel apparaat-dubbels als module-tweelingen. Gebruik ook niet `twin` , , of als `$twin` `body` `$body` eigenschapsnamen. Het volgende zijn bijvoorbeeld allemaal geldige query-expressies: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Routeringsquery op de body of apparaatt dubbel met een punt in de nettolading of eigenschapsnaam wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [berichtroutering.](iot-hub-devguide-messages-d2c.md)
* Probeer de [zelfstudie voor berichtroutering](tutorial-routing.md)uit.
