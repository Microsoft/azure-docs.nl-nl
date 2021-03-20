---
title: Query's uitvoeren op Azure IoT Hub-bericht routering | Microsoft Docs
description: Meer informatie over de IoT Hub bericht routerings query taal die u kunt gebruiken om uitgebreide query's toe te passen op berichten om de gegevens te ontvangen die voor u van belang zijn.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 83c290adea02915db1dc52bd359b4d3165611522
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92547704"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Querysyntaxis voor IoT Hub-berichtroutering

Met bericht routering kunnen gebruikers verschillende gegevens typen routeren, namelijk telemetrie-berichten van apparaten, levenscyclus gebeurtenissen van apparaten en gebeurtenissen voor het wijzigen van het apparaat naar verschillende eind punten. U kunt ook uitgebreide query's Toep assen op deze gegevens voordat u deze doorstuurt om de gegevens te ontvangen die voor u van belang zijn. In dit artikel wordt de query taal voor het routeren van berichten IoT Hub beschreven en vindt u enkele veelgebruikte query patronen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met bericht routering kunt u een query uitvoeren op de bericht eigenschappen en de bericht tekst, evenals dubbele labels voor apparaten en dubbele eigenschappen van het apparaat. Als de bericht tekst niet JSON is, kan bericht routering het bericht nog steeds routeren, maar query's kunnen niet worden toegepast op de bericht tekst.  Query's worden beschreven als Boole-expressies waarbij een Booleaanse waarde True maakt dat de query slaagt en dat alle binnenkomende gegevens doorstuurt, en Boolean False mislukt de query en er worden geen gegevens doorgestuurd. Als de expressie wordt geëvalueerd als null of niet gedefinieerd, wordt deze als onwaar beschouwd en wordt er een fout gegenereerd in IoT Hub [route ring van bron logboeken](monitor-iot-hub-reference.md#routes) in het geval van een storing. De query syntaxis moet correct zijn voor het opslaan en evalueren van de route.  

## <a name="message-routing-query-based-on-message-properties"></a>Bericht routering query op basis van bericht eigenschappen 

Met de IoT Hub definieert u een [algemene indeling](iot-hub-devguide-messages-construct.md) voor alle apparaat-naar-Cloud-berichten voor interoperabiliteit in verschillende protocollen. IoT Hub bericht wordt uitgegaan van de volgende JSON-weer gave van het bericht. Systeem eigenschappen worden toegevoegd voor alle gebruikers en identificeren de inhoud van het bericht. Gebruikers kunnen eigenschappen van toepassingen selectief toevoegen aan het bericht. U kunt het beste unieke eigenschapnamen gebruiken als IoT Hub apparaat-naar-Cloud-berichten niet hoofdletter gevoelig zijn. Als u bijvoorbeeld meerdere eigenschappen met dezelfde naam hebt, wordt in IoT Hub slechts een van de eigenschappen verzonden.  

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

Systeem eigenschappen helpen de inhoud en bron van de berichten te identificeren. 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| Invoer | tekenreeks | De gebruiker geeft het inhouds type van het bericht op. Als u een query wilt toestaan voor de hoofd tekst van het bericht, moet u de waarde Application/JSON instellen. |
| contentEncoding | tekenreeks | De gebruiker geeft het type code ring van het bericht op. Toegestane waarden zijn UTF-8, UTF-16, UTF-32 als het content type is ingesteld op Application/JSON. |
| iothub-verbinding-apparaat-id | tekenreeks | Deze waarde wordt ingesteld door IoT Hub en identificeert de ID van het apparaat. Gebruik voor query's `$connectionDeviceId` . |
| iothub-enqueuedtime | tekenreeks | Deze waarde wordt ingesteld door IoT Hub en vertegenwoordigt de werkelijke tijd van enqueuing van het bericht in UTC. Gebruik voor query's `enqueuedTime` . |
| DT-dataschema | tekenreeks |  Deze waarde wordt ingesteld door IoT hub op apparaat-naar-Cloud-berichten. Het bevat de apparaat model-ID die is ingesteld in de apparaat-verbinding. Gebruik voor query's `$dt-dataschema` . |
| DT-onderwerp | tekenreeks | De naam van het onderdeel dat de apparaat-naar-Cloud-berichten verzendt. Gebruik voor query's `$dt-subject` . |

Zoals beschreven in de [IOT hub berichten](iot-hub-devguide-messages-construct.md), zijn er aanvullende systeem eigenschappen in een bericht. Naast de bovenstaande eigenschappen in de vorige tabel kunt u ook een query uitvoeren op **connectionDeviceId**, **connectionModuleId**.

### <a name="application-properties"></a>Toepassingseigenschappen

Toepassings eigenschappen zijn door de gebruiker gedefinieerde teken reeksen die aan het bericht kunnen worden toegevoegd. Deze velden zijn optioneel.  

### <a name="query-expressions"></a>Query-expressies

Een query op bericht systeem eigenschappen moet worden voorafgegaan door het `$` symbool. Query's op toepassings eigenschappen zijn toegankelijk met hun naam en mogen niet worden voorafgegaan door het `$` symbool. Als de naam van een toepassings eigenschap begint met `$` , zoekt IOT hub deze in de systeem eigenschappen en wordt deze niet gevonden. vervolgens wordt de toepassings eigenschappen weer gegeven. Bijvoorbeeld: 

Een query uitvoeren op de systeem eigenschap contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Query uitvoeren op toepassings eigenschap processingPath:

```sql
processingPath = 'hot'
```

U kunt deze query's combi neren met Boole-expressies en-functies:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Een volledige lijst met ondersteunde Opera tors en functies wordt weer gegeven in de [expressie en voor waarden](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Bericht routerings query op basis van bericht tekst

Als u het uitvoeren van query's op bericht hoofdtekst wilt inschakelen, moet het bericht in een JSON worden gecodeerd in UTF-8, UTF-16 of UTF-32. De `contentType` moet worden ingesteld op `application/JSON` `contentEncoding` een van de ondersteunde UTF-code ringen in de systeem eigenschap. Als deze eigenschappen niet worden opgegeven, wordt de query-expressie niet door IoT Hub geëvalueerd op de bericht tekst. 

In het volgende voor beeld ziet u hoe u een bericht maakt met een juist gevormde en gecodeerde JSON-hoofd tekst: 

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
> Hier ziet u hoe u de code ring van de hoofd tekst in Java script kunt afhandelen. Als u een voor beeld in C# wilt zien, downloadt u de [Azure IOT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)-voor beelden. Pak het bestand master.zip uit. Het bestand Program. CS van Visual Studio Solution *SimulatedDevice* laat zien hoe u berichten kunt coderen en verzenden naar een IOT hub. Dit is hetzelfde voor beeld dat wordt gebruikt voor het testen van de bericht routering, zoals wordt uitgelegd in de [zelf studie over bericht routering](tutorial-routing.md). Onder aan programma. cs beschikt u ook over een methode voor het lezen van een van de gecodeerde bestanden, het decoderen ervan en het wegschrijven van de back-up als ASCII, zodat u deze kunt lezen. 


### <a name="query-expressions"></a>Query-expressies

Een query op de bericht tekst moet worden voorafgegaan door de `$body` . U kunt een verwijzing naar een hoofd tekst, een verwijzing naar de hoofd tekst of meerdere hoofd verwijzingen in de query-expressie gebruiken. De query-expressie kan ook een verwijzing naar een hoofd tekst combi neren met bericht systeem eigenschappen en naslag informatie over de eigenschappen van een Message-toepassing. Zo zijn de volgende geldige query-expressies: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Bericht routering query op basis van het apparaat dubbele 

Met bericht routering kunt u een query uitvoeren op dubbele Tags en eigenschappen van het [apparaat](iot-hub-devguide-device-twins.md) , die JSON-objecten zijn. Query's uitvoeren op module dubbele wordt ook ondersteund. Hieronder ziet u een voor beeld van dubbele Tags en eigenschappen van een apparaat.

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

Een query op het bericht dat moet worden voorafgegaan door de `$twin` . De query-expressie kan ook een dubbele tag of eigenschaps verwijzing combi neren met een verwijzing naar een hoofd tekst, eigenschappen van het bericht systeem en naslag informatie over de eigenschappen van een bericht toepassing. U kunt het beste unieke namen gebruiken in tags en eigenschappen, aangezien de query niet hoofdletter gevoelig is. Dit geldt voor apparaatdubbels en module apparaatdubbels. U kunt ook onthouden `twin` of,, `$twin` `body` of `$body` , als eigenschaps namen. Zo zijn de volgende geldige query-expressies: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

De routerings query voor de hoofd tekst of het apparaat, met een punt in de payload of de naam van de eigenschap wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [bericht routering](iot-hub-devguide-messages-d2c.md).
* Probeer de [zelf studie voor bericht routering](tutorial-routing.md).
