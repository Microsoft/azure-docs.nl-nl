---
title: Meer informatie over het Azure IoT Hub-identiteits register | Microsoft Docs
description: "Hand leiding voor ontwikkel aars: beschrijving van het IoT Hub identiteits register en hoe u dit kunt gebruiken om uw apparaten te beheren. Bevat informatie over het importeren en exporteren van apparaat-id's in bulk."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2d9b0d97fa1823314f5109a1c7fc79054806c148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146923"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Inzicht in het identiteits register in uw IoT-hub

Elke IoT-hub beschikt over een id-REGI ster waarin informatie wordt opgeslagen over de apparaten en modules die zijn toegestaan om verbinding te maken met de IoT hub. Voordat een apparaat of module verbinding kan maken met een IoT-hub, moet er een vermelding zijn voor dat apparaat of deze module in het identiteits register van de IoT-hub. Een apparaat of module moet ook worden geverifieerd met de IoT-hub op basis van de referenties die zijn opgeslagen in het identiteits register.

Het apparaat of de module-ID die is opgeslagen in het identiteits register is hoofdletter gevoelig.

Op hoog niveau is het id-REGI ster een verzameling van apparaten of module-id-resources die met de REST kan worden ondersteund. Wanneer u een vermelding in het id-REGI ster toevoegt, maakt IoT Hub een set van resources per apparaat, zoals de wachtrij met in-Flight Cloud-naar-apparaat-berichten.

Gebruik het identiteits register wanneer u het volgende moet doen:

* Apparaten of modules inrichten die verbinding maken met uw IoT-hub.
* Beheer per apparaat/per module toegang tot het apparaat of module gerichte eind punten van uw hub.

> [!NOTE]
> * Het id-REGI ster bevat geen toepassingsspecifieke meta gegevens.
> * Module-identiteit en module dubbele is beschikbaar als open bare preview. De onderstaande functie wordt ondersteund op module-identiteit wanneer deze algemeen beschikbaar is.
>

## <a name="identity-registry-operations"></a>Registerbewerkingen voor identiteit

Het REGI ster IoT Hub identiteiten bevat de volgende bewerkingen:

* Id van apparaat of module maken
* Identiteit van apparaat of module bijwerken
* Identiteit van apparaat of module ophalen op ID
* Id van apparaat of module verwijderen
* Maxi maal 1000 identiteiten vermelden
* Apparaat-id's exporteren naar Azure Blob-opslag
* Apparaat-id's importeren uit Azure Blob-opslag

Al deze bewerkingen kunnen gebruikmaken van optimistische gelijktijdigheid, zoals opgegeven in [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> De enige manier om alle identiteiten op te halen in het id-REGI ster van een IoT-hub is het gebruik van de [export](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) functionaliteit.

Een IoT Hub identiteits register:

* Bevat geen meta gegevens van de toepassing.
* Kan worden geopend als een woorden lijst met behulp van de **deviceId** of **moduleId** als sleutel.
* Biedt geen ondersteuning voor uitdrukkings query's.

Een IoT-oplossing heeft doorgaans een aparte Store met toepassingsspecifieke meta gegevens. Het oplossings specifieke archief in een oplossing voor slim bouwen legt bijvoorbeeld de ruimte vast waarin een temperatuur sensor wordt geïmplementeerd.

> [!IMPORTANT]
> Gebruik alleen het identiteits register voor Apparaatbeheer-en inrichtings bewerkingen. Bewerkingen met hoge door Voer op het moment van uitvoering mogen niet afhankelijk zijn van het uitvoeren van bewerkingen in het identiteits register. Als u bijvoorbeeld de verbindings status van een apparaat controleert voordat u een opdracht verzendt, wordt geen ondersteund patroon. Controleer de [bandbreedte tarieven](iot-hub-devguide-quotas-throttling.md) voor het identiteits register en het heartbeat-patroon van het [apparaat](iot-hub-devguide-identity-registry.md#device-heartbeat) .

## <a name="disable-devices"></a>Apparaten uitschakelen

U kunt apparaten uitschakelen door de **status** eigenschap van een identiteit in het identiteits register bij te werken. Normaal gesp roken gebruikt u deze eigenschap in twee scenario's:

* Tijdens een inrichtings installatie proces. Zie [Device Provisioning (apparaat inrichten](iot-hub-devguide-identity-registry.md#device-provisioning)) voor meer informatie.

* Als u om welke reden dan ook denkt dat een apparaat is aangetast of niet is toegestaan.

Deze functie is niet beschikbaar voor-modules.

## <a name="import-and-export-device-identities"></a>Apparaat-id's importeren en exporteren

Gebruik asynchrone bewerkingen op het [eind punt](iot-hub-devguide-endpoints.md) van de IOT hub van de resource provider om apparaat-id's in bulk te exporteren vanuit het id-REGI ster van een IOT-hub. Export bewerkingen zijn langlopende taken die gebruikmaken van een door de klant geleverde BLOB-container voor het opslaan van identiteits gegevens van apparaten die zijn gelezen in het identiteits register.

Gebruik asynchrone bewerkingen op het [eind punt](iot-hub-devguide-endpoints.md) van de IOT hub van de resource provider om apparaat-id's in bulk te importeren in het id-REGI ster van een IOT-hub. Import bewerkingen zijn langlopende taken die gebruikmaken van gegevens in een door de klant opgegeven BLOB-container voor het schrijven van identiteits gegevens van apparaten in het identiteits register.

Zie voor meer informatie over de import-en export-Api's [IOT hub rest-api's van de resource provider](/rest/api/iothub/iothubresource). Zie [Bulk beheer van IOT hub apparaat-id's](iot-hub-bulk-identity-mgmt.md)voor meer informatie over het uitvoeren van import-en export taken.

Apparaat-id's kunnen ook vanuit een IoT Hub via de service-API worden geëxporteerd en geïmporteerd via de [rest API](/rest/api/iothub/service/jobs/createimportexportjob) of een van de IOT hub [service-sdk's](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

## <a name="device-provisioning"></a>Apparaat inrichten

De apparaatgegevens die een bepaalde IoT-oplossing opslaat, zijn afhankelijk van de specifieke vereisten van die oplossing. Maar ten minste moet een oplossing apparaat-id's en verificatie sleutels opslaan. Azure IoT Hub bevat een identiteits register dat waarden voor elk apparaat kan opslaan, zoals Id's, verificatie sleutels en status codes. Een oplossing kan andere Azure-Services, zoals Table-opslag, Blob-opslag of Cosmos DB, gebruiken om eventuele extra apparaatgegevens op te slaan.

Het *inrichten van apparaten* is het proces van het toevoegen van de initiële gegevens van het apparaat aan de winkels in uw oplossing. Als u een nieuw apparaat wilt inschakelen om verbinding te maken met uw hub, moet u een apparaat-ID en sleutels toevoegen aan het IoT Hub-identiteits register. Als onderdeel van het inrichtings proces moet u mogelijk apparaatspecifieke gegevens initialiseren in andere oplossingen Stores. U kunt ook de Azure IoT Hub Device Provisioning Service gebruiken voor het inschakelen van Zero-Touch, just-in-time inrichting naar een of meer IoT-hubs zonder menselijke tussen komst. Zie de documentatie van de [inrichtings service](https://azure.microsoft.com/documentation/services/iot-dps)voor meer informatie.

## <a name="device-heartbeat"></a>Heartbeat van apparaat

Het IoT Hub identiteits register bevat een veld met de naam **connectionState**. Gebruik het veld **connectionState** alleen tijdens het ontwikkelen en opsporen van fouten. IoT-oplossingen mogen het veld niet in runtime opvragen. Vraag het veld **connectionState** bijvoorbeeld niet op om te controleren of een apparaat is verbonden voordat u een Cloud-naar-apparaat-bericht of SMS verzendt. U kunt het beste abonneren op de [ **verbroken** gebeurtenis](iot-hub-event-grid.md#event-types) van het apparaat op Event grid om waarschuwingen op te halen en de verbindings status van het apparaat te controleren. Gebruik deze [zelf studie](iot-hub-how-to-order-connection-state-events.md) voor meer informatie over het integreren van met het apparaat verbonden en verbroken gebeurtenissen van het apparaat van IOT hub in uw IOT-oplossing.

Als uw IoT-oplossing moet weten of een apparaat is verbonden, kunt u het *heartbeat-patroon* implementeren.
In het heartbeat-patroon verzendt het apparaat ten minste één keer per keer elke vaste periode een apparaat-naar-Cloud-berichten (bijvoorbeeld minstens één keer per uur). Dus zelfs als een apparaat geen gegevens heeft om te verzenden, verzendt het een leeg apparaat-naar-Cloud bericht (meestal met een eigenschap die het als een heartbeat identificeert). Aan de kant van de service onderhoudt de oplossing een kaart met de laatste heartbeat die voor elk apparaat is ontvangen. Als de oplossing geen heartbeat-bericht binnen de verwachte tijd van het apparaat ontvangt, wordt ervan uitgegaan dat er een probleem is met het apparaat.

Een complexere implementatie kan de informatie bevatten van [Azure monitor](../azure-monitor/index.yml) en [Azure resource Health](../service-health/resource-health-overview.md) om apparaten te identificeren die verbinding proberen te maken of te communiceren, maar niet. Zie [IOT hub controleren](monitor-iot-hub.md) en [IOT hub resource Health controleren](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health)voor meer informatie. Wanneer u het heartbeat-patroon implementeert, moet u controleren [IOT hub quota's en gashendel](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Als een IoT-oplossing de verbindings status alleen gebruikt om te bepalen of u Cloud-naar-apparaat-berichten wilt verzenden en berichten niet naar grote sets apparaten worden verzonden, overweeg dan het gebruik van het eenvoudiger *korte verloop tijd* patroon. Dit patroon behaalt hetzelfde resultaat als het onderhouden van een verbindings status register van een apparaat met behulp van het heartbeat-patroon. Dit is efficiënter. Als u bericht bevestigingen aanvraagt, kunt IoT Hub u verwittigen over welke apparaten berichten kunnen ontvangen en wat niet.

## <a name="device-and-module-lifecycle-notifications"></a>Levenscyclus meldingen apparaat en module

IoT Hub kunt uw IoT-oplossing waarschuwen wanneer een identiteit wordt gemaakt of verwijderd door levenscyclus meldingen te verzenden. Hiervoor moet uw IoT-oplossing een route maken en de gegevens bron instellen op *DeviceLifecycleEvents* of *ModuleLifecycleEvents*. Standaard worden er geen levenscyclus meldingen verzonden, dat wil zeggen dat er geen dergelijke routes vooraf bestaan. Het meldings bericht bevat eigenschappen en hoofd tekst.

Eigenschappen: eigenschappen van het bericht systeem worden voorafgegaan door het `$` symbool.

Meldings bericht voor het apparaat:

| Name | Waarde |
| --- | --- |
|$content-type | application/json |
|$iothub-enqueuedtime |  Tijdstip waarop de melding is verzonden |
|$iothub-bericht bron | deviceLifecycleEvents |
|$content-encoding | UTF-8 |
|opType | **createDeviceIdentity** of **deleteDeviceIdentity** |
|hubName | Naam van IoT Hub |
|deviceId | ID van het apparaat |
|operationTimestamp | ISO8601 tijds tempel van bewerking |
|iothub-Message-schema | deviceLifecycleNotification |

Hoofd tekst: deze sectie bevindt zich in JSON-indeling en vertegenwoordigt de dubbele identiteit van de gemaakte apparaat-id. Bijvoorbeeld:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Meldings bericht voor module:

| Name | Waarde |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  Tijdstip waarop de melding is verzonden |
$iothub-bericht bron | moduleLifecycleEvents |
$content-encoding | UTF-8 |
opType | **createModuleIdentity** of **deleteModuleIdentity** |
hubName | Naam van IoT Hub |
moduleId | ID van de module |
operationTimestamp | ISO8601 tijds tempel van bewerking |
iothub-Message-schema | moduleLifecycleNotification |

Hoofd tekst: deze sectie bevindt zich in JSON-indeling en vertegenwoordigt de dubbele van de gemaakte module-identiteit. Bijvoorbeeld:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Eigenschappen van apparaat-id

Apparaat-id's worden weer gegeven als JSON-documenten met de volgende eigenschappen:

| Eigenschap | Opties | Description |
| --- | --- | --- |
| deviceId |vereist, alleen-lezen voor updates |Een hoofdletter gevoelige teken reeks (Maxi maal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens plus bepaalde speciale tekens: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |vereist, alleen-lezen |Een met IoT-hub gegenereerde, hoofdletter gevoelige teken reeks tot 128 tekens lang. Deze waarde wordt gebruikt om apparaten met dezelfde **deviceId** te onderscheiden, wanneer ze zijn verwijderd en opnieuw gemaakt. |
| ETAG |vereist, alleen-lezen |Een teken reeks die een zwakke ETag vertegenwoordigt voor de apparaat-id, zoals per [RFC7232](https://tools.ietf.org/html/rfc7232). |
| dienst |optioneel |Een samengesteld object met verificatie-informatie en beveiligings materialen. |
| auth. symkey |optioneel |Een samengesteld object met een primaire en secundaire sleutel die is opgeslagen in Base64-indeling. |
| status |vereist |Een toegangs indicator. Kan worden **ingeschakeld** of **uitgeschakeld**. Als deze functie is **ingeschakeld**, mag het apparaat verbinding maken. Indien **uitgeschakeld**, kan dit apparaat geen toegang krijgen tot een eind punt op het apparaat. |
| statusReason |optioneel |Een teken reeks met een lengte van 128 waarmee de reden voor de status van de apparaat-id wordt opgeslagen. Alle UTF-8-tekens zijn toegestaan. |
| statusUpdateTime |alleen-lezen |Een tijdelijke indicator met de datum en tijd van de laatste status update. |
| connectionState |alleen-lezen |Een veld dat de verbindings status aangeeft: **verbinding** of **verbroken**. Dit veld vertegenwoordigt de IoT Hub weer gave van de verbindings status van het apparaat. **Belang rijk**: dit veld mag alleen worden gebruikt voor het oplossen van problemen met de ontwikkeling en fout opsporing. De verbindings status wordt alleen bijgewerkt voor apparaten met behulp van MQTT of AMQP. Het is ook gebaseerd op protocol niveau pings (MQTT pings of AMQP pings) en kan Maxi maal vijf minuten duren. Om deze redenen kan er sprake zijn van valse positieven, zoals apparaten die zijn gerapporteerd als verbonden, maar die niet zijn verbonden. |
| connectionStateUpdatedTime |alleen-lezen |Een tijdelijke indicator, met de datum en laatste keer dat de verbindings status is bijgewerkt. |
| lastActivityTime |alleen-lezen |Een tijdelijke indicator, met de datum en laatste keer dat het apparaat is verbonden, ontvangen of een bericht heeft verzonden. Deze eigenschap is uiteindelijk consistent, maar kan tot 5 tot 10 minuten worden uitgesteld. Daarom mag deze niet worden gebruikt in productie scenario's. |

> [!NOTE]
> De verbindings status kan alleen de IoT Hub weer gave van de status van de verbinding weer geven. Updates van deze status kunnen worden vertraagd, afhankelijk van de netwerk omstandigheden en configuraties.

> [!NOTE]
> De Sdk's van het apparaat bieden momenteel geen ondersteuning `+` voor het gebruik van de en- `#` tekens in het **deviceId**.

## <a name="module-identity-properties"></a>Eigenschappen van module-identiteit

Module-identiteiten worden weer gegeven als JSON-documenten met de volgende eigenschappen:

| Eigenschap | Opties | Description |
| --- | --- | --- |
| deviceId |vereist, alleen-lezen voor updates |Een hoofdletter gevoelige teken reeks (Maxi maal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens plus bepaalde speciale tekens: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |vereist, alleen-lezen voor updates |Een hoofdletter gevoelige teken reeks (Maxi maal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens plus bepaalde speciale tekens: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |vereist, alleen-lezen |Een met IoT-hub gegenereerde, hoofdletter gevoelige teken reeks tot 128 tekens lang. Deze waarde wordt gebruikt om apparaten met dezelfde **deviceId** te onderscheiden, wanneer ze zijn verwijderd en opnieuw gemaakt. |
| ETAG |vereist, alleen-lezen |Een teken reeks die een zwakke ETag vertegenwoordigt voor de apparaat-id, zoals per [RFC7232](https://tools.ietf.org/html/rfc7232). |
| dienst |optioneel |Een samengesteld object met verificatie-informatie en beveiligings materialen. |
| auth. symkey |optioneel |Een samengesteld object met een primaire en secundaire sleutel die is opgeslagen in Base64-indeling. |
| status |vereist |Een toegangs indicator. Kan worden **ingeschakeld** of **uitgeschakeld**. Als deze functie is **ingeschakeld**, mag het apparaat verbinding maken. Indien **uitgeschakeld**, kan dit apparaat geen toegang krijgen tot een eind punt op het apparaat. |
| statusReason |optioneel |Een teken reeks met een lengte van 128 waarmee de reden voor de status van de apparaat-id wordt opgeslagen. Alle UTF-8-tekens zijn toegestaan. |
| statusUpdateTime |alleen-lezen |Een tijdelijke indicator met de datum en tijd van de laatste status update. |
| connectionState |alleen-lezen |Een veld dat de verbindings status aangeeft: **verbinding** of **verbroken**. Dit veld vertegenwoordigt de IoT Hub weer gave van de verbindings status van het apparaat. **Belang rijk**: dit veld mag alleen worden gebruikt voor het oplossen van problemen met de ontwikkeling en fout opsporing. De verbindings status wordt alleen bijgewerkt voor apparaten met behulp van MQTT of AMQP. Het is ook gebaseerd op protocol niveau pings (MQTT pings of AMQP pings) en kan Maxi maal vijf minuten duren. Om deze redenen kan er sprake zijn van valse positieven, zoals apparaten die zijn gerapporteerd als verbonden, maar die niet zijn verbonden. |
| connectionStateUpdatedTime |alleen-lezen |Een tijdelijke indicator, met de datum en laatste keer dat de verbindings status is bijgewerkt. |
| lastActivityTime |alleen-lezen |Een tijdelijke indicator, met de datum en laatste keer dat het apparaat is verbonden, ontvangen of een bericht heeft verzonden. |

> [!NOTE]
> De Sdk's van het apparaat bieden momenteel geen ondersteuning `+` voor het gebruik van de en- `#` tekens in de **deviceId** en **moduleId**.

## <a name="additional-reference-material"></a>Extra referentie materiaal

Andere naslag onderwerpen in de IoT Hub ontwikkelaars handleiding zijn:

* [IOT hub-eind punten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eind punten die elke IOT-hub beschikbaar maakt voor runtime-en beheer bewerkingen.

* Met [beperking en quota's](iot-hub-devguide-quotas-throttling.md) worden de quota's en beperkings gedrag beschreven die van toepassing zijn op de IOT hub-service.

* Met de [sdk's van Azure IOT-apparaat en-service](iot-hub-devguide-sdks.md) worden de diverse sdk's voor de taal weer gegeven die u kunt gebruiken bij het ontwikkelen van zowel apparaat-als service-apps die communiceren met IOT hub.

* [IOT hub query taal](iot-hub-devguide-query-language.md) beschrijft de query taal die u kunt gebruiken om informatie op te halen van IOT hub over de apparaatdubbels en taken van uw apparaat.

* [IOT hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IOT hub ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het IoT Hub identiteits register kunt gebruiken, bent u mogelijk geïnteresseerd in de volgende IoT Hub onderwerpen over de ontwikkelaars handleiding:

* [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

* [Apparaatdubbels gebruiken om status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)

* [Een rechtstreekse methode aanroepen op een apparaat](iot-hub-devguide-direct-methods.md)

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Raadpleeg de volgende IoT Hub zelf studie als u een aantal van de concepten wilt uitproberen die in dit artikel worden beschreven:

* [Aan de slag met Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Zie voor meer informatie over het gebruik van de IoT Hub Device Provisioning Service om Zero-Touch, just-in-time inrichten in te scha kelen: 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)