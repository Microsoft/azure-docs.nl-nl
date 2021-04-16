---
title: Inzicht in Azure IoT Hub identiteitsregister van | Microsoft Docs
description: "Ontwikkelaarshandleiding: beschrijving van het IoT Hub identiteitsregister en hoe u dit kunt gebruiken om uw apparaten te beheren. Bevat informatie over het bulksgewijs importeren en exporteren van apparaat-id's."
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
ms.openlocfilehash: 42def04db63d81bdb3eff8098daa8c75924bffec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502076"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Meer informatie over het identiteitsregister in uw IoT-hub

Elke IoT-hub heeft een identiteitsregister met informatie over de apparaten en modules die verbinding mogen maken met de IoT-hub. Voordat een apparaat of module verbinding kan maken met een IoT-hub, moet er een vermelding voor dat apparaat of de module in het id-register van de IoT-hub staan. Een apparaat of module moet ook worden geverifieerd bij de IoT-hub op basis van referenties die zijn opgeslagen in het identiteitsregister.

De apparaat- of module-id die is opgeslagen in het identiteitsregister is case-gevoelig.

Op hoog niveau is het identiteitsregister een met REST geschikte verzameling apparaat- of module-id-resources. Wanneer u een vermelding in het identiteitsregister toevoegt, maakt IoT Hub een set resources per apparaat, zoals de wachtrij die cloud-naar-apparaat-berichten bevat die worden verzonden.

Gebruik het identiteitsregister wanneer u het volgende moet doen:

* Apparaten of modules inrichten die verbinding maken met uw IoT-hub.
* Beheer de toegang per apparaat/per module tot de apparaat- of module-gerichte eindpunten van uw hub.

> [!NOTE]
> * Het identiteitsregister bevat geen toepassingsspecifieke metagegevens.
> * Module-id en module dubbel zijn beschikbaar als openbare preview. De onderstaande functie wordt ondersteund voor module-id's wanneer deze algemeen beschikbaar is.
>

## <a name="identity-registry-operations"></a>Registerbewerkingen voor identiteit

Het IoT Hub identiteitsregister bemaskert de volgende bewerkingen:

* Apparaat- of module-id maken
* Apparaat- of module-id bijwerken
* Apparaat- of module-id ophalen op id
* Apparaat- of module-id verwijderen
* Een lijst van maximaal 1000 identiteiten maken
* Apparaat-id's exporteren naar Azure Blob Storage
* Apparaat-id's importeren uit Azure Blob Storage

Al deze bewerkingen kunnen optimistische gelijktijdigheid gebruiken, zoals opgegeven in [RFC7232.](https://tools.ietf.org/html/rfc7232)

> [!IMPORTANT]
> De enige manier om alle identiteiten in het identiteitsregister van een IoT-hub op te halen, is met de [functionaliteit Exporteren.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Een IoT Hub identiteitsregister:

* Bevat geen metagegevens van toepassingen.
* Kan worden gebruikt als een woordenlijst, met behulp van **de deviceId** of **moduleId** als sleutel.
* Biedt geen ondersteuning voor expressieve query's.

Een IoT-oplossing heeft doorgaans een afzonderlijk oplossingssspecifieke opslag die toepassingsspecifieke metagegevens bevat. De oplossingsspecifieke opslag in een slimme gebouwoplossing zou bijvoorbeeld de ruimte registreren waarin een temperatuursensor wordt geïmplementeerd.

> [!IMPORTANT]
> Gebruik het identiteitsregister alleen voor apparaatbeheer- en inrichtingsbewerkingen. Bewerkingen met hoge doorvoer tijdens run time mogen niet afhankelijk zijn van het uitvoeren van bewerkingen in het identiteitsregister. Het controleren van de verbindingstoestand van een apparaat voordat een opdracht wordt verzonden, wordt bijvoorbeeld niet ondersteund. Controleer de [beperkingssnelheden voor](iot-hub-devguide-quotas-throttling.md) het identiteitsregister en het [heartbeat-patroon van het](iot-hub-devguide-identity-registry.md#device-heartbeat) apparaat.

## <a name="disable-devices"></a>Apparaten uitschakelen

U kunt apparaten uitschakelen door de **status-eigenschap van** een identiteit in het identiteitsregister bij te werken. Normaal gesproken gebruikt u deze eigenschap in twee scenario's:

* Tijdens een inrichtingsproces. Zie Device [Provisioning voor meer informatie.](iot-hub-devguide-identity-registry.md#device-provisioning)

* Als u om een of andere reden denkt dat een apparaat is aangetast of niet is geautoriseerd.

Deze functie is niet beschikbaar voor modules.

## <a name="import-and-export-device-identities"></a>Apparaat-id's importeren en exporteren

Gebruik asynchrone bewerkingen op het [eindpunt IoT Hub resourceprovider](iot-hub-devguide-endpoints.md) om apparaat-id's bulksgewijs te exporteren uit het id-register van een IoT-hub. Exports zijn langlopende taken die gebruikmaken van een door de klant geleverde blobcontainer om apparaat-id-gegevens op te slaan die zijn gelezen uit het identiteitsregister.

Gebruik asynchrone bewerkingen op het [eindpunt IoT Hub resourceprovider](iot-hub-devguide-endpoints.md) om apparaat-id's bulksgewijs te importeren in het id-register van een IoT-hub. Importen zijn langlopende taken die gebruikmaken van gegevens in een door de klant geleverde blobcontainer om apparaatidentiteitsgegevens naar het identiteitsregister te schrijven.

Zie voor meer informatie over de API's voor importeren en exporteren [IoT Hub REST API's van de resourceprovider.](/rest/api/iothub/iothubresource) Zie Bulksgewijs beheer van IoT Hub apparaatidentiteiten voor meer informatie over het uitvoeren [van import- en exporttaken.](iot-hub-bulk-identity-mgmt.md)

Apparaat-id's kunnen ook worden geëxporteerd en geïmporteerd uit een IoT Hub via de service-API via de [REST API](/rest/api/iothub/service/jobs/createimportexportjob) of een van de IoT Hub [Service SDK's.](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

## <a name="device-provisioning"></a>Apparaatinrichting

De apparaatgegevens die in een bepaalde IoT-oplossing worden op opslag, zijn afhankelijk van de specifieke vereisten van die oplossing. Maar een oplossing moet minimaal apparaat-id's en verificatiesleutels opslaan. Azure IoT Hub bevat een identiteitsregister dat waarden voor elk apparaat kan opslaan, zoals id's, verificatiesleutels en statuscodes. Een oplossing kan andere Azure-services gebruiken, zoals table storage, blob-opslag of Cosmos DB extra apparaatgegevens op te slaan.

*Apparaatinrichting* is het proces waarbij de initiële apparaatgegevens worden toegevoegd aan de winkels in uw oplossing. Als u een nieuw apparaat verbinding wilt laten maken met uw hub, moet u een apparaat-id en -sleutels toevoegen aan het IoT Hub-id-register. Als onderdeel van het inrichtingsproces moet u mogelijk apparaatspecifieke gegevens initialiseren in andere oplossingsopslag. U kunt ook de Azure IoT Hub Device Provisioning Service gebruiken om just-in-time inrichting naar een of meer IoT-hubs mogelijk te maken zonder menselijke tussenkomst. Zie de documentatie voor [inrichtingsservice voor meer informatie.](https://azure.microsoft.com/documentation/services/iot-dps)

## <a name="device-heartbeat"></a>Heartbeat van apparaat

Het IoT Hub identiteitsregister bevat een veld met de **naam connectionState.** Gebruik het veld **connectionState alleen** tijdens de ontwikkeling en debugging. IoT-oplossingen mogen tijdens run time geen query's uitvoeren op het veld. U kunt bijvoorbeeld geen query uitvoeren op het veld **connectionState** om te controleren of een apparaat is verbonden voordat u een cloud-naar-apparaat-bericht of sms verzendt. U wordt aangeraden u te abonneren op [  de](iot-hub-event-grid.md#event-types) gebeurtenis Verbinding met apparaat verbroken op Event Grid om waarschuwingen te krijgen en de verbindingstoestand van het apparaat te controleren. Gebruik deze [zelfstudie](iot-hub-how-to-order-connection-state-events.md) voor meer informatie over het integreren van gebeurtenissen die zijn verbonden met apparaten en gebeurtenissen die niet IoT Hub in uw IoT-oplossing.

Als uw IoT-oplossing moet weten of een apparaat is verbonden, kunt u het *heartbeat-patroon implementeren.*
In het heartbeat-patroon verzendt het apparaat ten minste eenmaal per vaste tijd (bijvoorbeeld ten minste één keer per uur) apparaat-naar-cloud-berichten. Dus zelfs als een apparaat geen gegevens heeft om te verzenden, verzendt het nog steeds een leeg apparaat-naar-cloud-bericht (meestal met een eigenschap die het als een heartbeat identificeert). Aan de servicezijde houdt de oplossing een kaart bij met de laatste heartbeat die voor elk apparaat is ontvangen. Als de oplossing geen heartbeatbericht ontvangt binnen de verwachte tijd van het apparaat, wordt ervan uitgenomen dat er een probleem is met het apparaat.

Een complexere implementatie kan [](../azure-monitor/index.yml) de informatie van Azure Monitor en [Azure Resource Health](../service-health/resource-health-overview.md) bevatten om apparaten te identificeren die verbinding proberen te maken of te communiceren, maar die niet goed werken. Zie Monitor [IoT Hub](monitor-iot-hub.md) and Check [IoT Hub resource health (Status](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health)van resources controleren) IoT Hub meer informatie. Wanneer u het heartbeat-patroon implementeert, controleert u of [IoT Hub quota en vertragingen.](iot-hub-devguide-quotas-throttling.md)

> [!NOTE]
> Als een IoT-oplossing alleen gebruikmaakt van de verbindingstoestand om te bepalen of berichten van cloud naar apparaat moeten worden verzonden en berichten niet worden uitgezonden naar grote sets apparaten, kunt u overwegen het eenvoudigere patroon voor korte verlooptijd te *gebruiken.* Dit patroon bereikt hetzelfde resultaat als het onderhouden van een register met de apparaatverbindingstoestand met behulp van het heartbeat-patroon, terwijl het efficiënter is. Als u bevestigingen voor berichten aanvraagt, IoT Hub u een melding ontvangen over welke apparaten berichten kunnen ontvangen en welke niet.

## <a name="device-and-module-lifecycle-notifications"></a>Meldingen over de levenscyclus van apparaten en modules

IoT Hub kunt uw IoT-oplossing waarschuwen wanneer een apparaat-id wordt gemaakt of verwijderd door meldingen over de levenscyclus te verzenden. Hiervoor moet uw IoT-oplossing een route maken en de gegevensbron instellen op *DeviceLifecycleEvents.* Standaard worden er geen meldingen over de levenscyclus verzonden, dat wil zeggen dat dergelijke routes niet bestaan. Door een route te maken met een gegevensbron die gelijk is aan *DeviceLifecycleEvents,* worden levenscyclusgebeurtenissen verzonden voor zowel apparaat-id's als module-id's; De inhoud van het bericht verschilt echter afhankelijk van of de gebeurtenissen worden gegenereerd voor module-id's of apparaat-id's.  Voor IoT Edge-modules is de stroom voor het maken van module-identiteiten anders dan voor andere modules. Als gevolg hiervan wordt voor IoT Edge-modules de melding voor maken alleen verzonden als het bijbehorende IoT Edge-apparaat voor de bijgewerkte IoT Edge-module-identiteit wordt uitgevoerd. Voor alle andere modules worden meldingen over de levenscyclus verzonden wanneer de module-id aan de IoT Hub bijgewerkt.  Het meldingsbericht bevat eigenschappen en de body.

Eigenschappen: Eigenschappen van het berichtsysteem worden vooraf laten gaan door het `$` symbool.

Meldingsbericht voor apparaat:

| Name | Waarde |
| --- | --- |
|$content-type | application/json |
|$iothub-enqueuedtime |  Tijdstip waarop de melding is verzonden |
|$iothub-message-source | deviceLifecycleEvents |
|$content-codering | utf-8 |
|opType | **createDeviceIdentity** **of deleteDeviceIdentity** |
|hubName | Naam van IoT Hub |
|deviceId | Id van het apparaat |
|operationTimestamp | ISO8601-tijdstempel van bewerking |
|iothub-message-schema | deviceLifecycleNotification |

Body: Deze sectie heeft de JSON-indeling en vertegenwoordigt de dubbel van de gemaakte apparaat-id. Bijvoorbeeld:

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
Meldingsbericht voor module:

| Name | Waarde |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  Tijdstip waarop de melding is verzonden |
$iothub-message-source | moduleLifecycleEvents |
$content-codering | utf-8 |
opType | **createModuleIdentity** **of deleteModuleIdentity** |
hubName | Naam van IoT Hub |
moduleId | Id van de module |
operationTimestamp | ISO8601-tijdstempel van bewerking |
iothub-message-schema | moduleLifecycleNotification |

Body: Deze sectie heeft de JSON-indeling en vertegenwoordigt de dubbel van de gemaakte module-identiteit. Bijvoorbeeld:

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

Apparaat-id's worden weergegeven als JSON-documenten met de volgende eigenschappen:

| Eigenschap | Opties | Beschrijving |
| --- | --- | --- |
| deviceId |vereiste, alleen-lezen updates |Een hoofdtekenreeks (maximaal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens plus bepaalde speciale tekens: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |vereist, alleen-lezen |Een door de IoT-hub gegenereerde, hoofd-gevoelige tekenreeks van maximaal 128 tekens. Deze waarde wordt gebruikt om apparaten te onderscheiden van dezelfde **deviceId** wanneer ze zijn verwijderd en opnieuw zijn gemaakt. |
| etag |vereist, alleen-lezen |Een tekenreeks die een zwakke ETag vertegenwoordigt voor de apparaat-id, volgens [RFC7232.](https://tools.ietf.org/html/rfc7232) |
| Auth |optioneel |Een samengesteld object met verificatiegegevens en beveiligingsmateriaal. |
| auth.symkey |optioneel |Een samengesteld object met een primaire en een secundaire sleutel, opgeslagen in base64-indeling. |
| status |vereist |Een toegangsindicator. Kan worden **ingeschakeld of** **uitgeschakeld.** Als **deze functie is ingeschakeld,** mag het apparaat verbinding maken. Als **deze is** uitgeschakeld, heeft dit apparaat geen toegang tot een apparaat-gericht eindpunt. |
| statusReason |optioneel |Een tekenreeks van 128 tekens lang die de reden voor de apparaat-id-status op bewaart. Alle UTF-8 tekens zijn toegestaan. |
| statusUpdateTime |alleen-lezen |Een tijdelijke indicator met de datum en tijd van de laatste statusupdate. |
| connectionState |alleen-lezen |Een veld dat de verbindingsstatus aangeeft: **Verbonden** of **Verbroken.** Dit veld vertegenwoordigt de IoT Hub weergave van de verbindingsstatus van het apparaat. **Belangrijk:** dit veld mag alleen worden gebruikt voor ontwikkelings-/buggingsdoeleinden. De verbindingstoestand wordt alleen bijgewerkt voor apparaten die MQTT of AMQP gebruiken. Het is ook gebaseerd op pings op protocolniveau (MQTT-pings of AMQP-pings) en kan een maximale vertraging van slechts 5 minuten hebben. Om deze redenen kunnen er fout-positieven zijn, zoals apparaten die zijn gerapporteerd als verbonden, maar die zijn losgekoppeld. |
| connectionStateUpdatedTime |alleen-lezen |Een tijdelijke indicator met de datum en laatste keer dat de verbindingsstatus is bijgewerkt. |
| lastActivityTime |alleen-lezen |Een tijdelijke indicator met de datum en laatste keer dat het apparaat verbinding heeft gemaakt, ontvangen of een bericht heeft verzonden. Deze eigenschap is uiteindelijk consistent, maar kan maximaal 5 tot 10 minuten worden vertraagd. Daarom mag deze niet worden gebruikt in productiescenario's. |

> [!NOTE]
> Verbindingsstatus kan alleen de IoT Hub weergave van de status van de verbinding vertegenwoordigen. Updates van deze status kunnen worden vertraagd, afhankelijk van netwerkomstandigheden en configuraties.

> [!NOTE]
> Op dit moment bieden de apparaat-SDK's geen ondersteuning voor het gebruik van de `+` `#` tekens en in **deviceId**.

## <a name="module-identity-properties"></a>Eigenschappen van module-identiteit

Module-identiteiten worden weergegeven als JSON-documenten met de volgende eigenschappen:

| Eigenschap | Opties | Beschrijving |
| --- | --- | --- |
| deviceId |vereiste, alleen-lezen updates |Een hoofdtekenreeks (maximaal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens plus bepaalde speciale tekens: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |vereiste, alleen-lezen updates |Een hoofdtekenreeks (maximaal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens plus bepaalde speciale tekens: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |vereist, alleen-lezen |Een door de IoT-hub gegenereerde, hoofd-gevoelige tekenreeks die maximaal 128 tekens lang is. Deze waarde wordt gebruikt om apparaten te onderscheiden van dezelfde **deviceId** wanneer ze zijn verwijderd en opnieuw zijn gemaakt. |
| etag |vereist, alleen-lezen |Een tekenreeks die een zwakke ETag vertegenwoordigt voor de apparaat-id, volgens [RFC7232.](https://tools.ietf.org/html/rfc7232) |
| Auth |optioneel |Een samengesteld object met verificatiegegevens en beveiligingsmateriaal. |
| auth.symkey |optioneel |Een samengesteld object met een primaire en een secundaire sleutel, opgeslagen in base64-indeling. |
| status |vereist |Een toegangsindicator. Kan worden **ingeschakeld of** **uitgeschakeld.** Als **deze functie is ingeschakeld,** mag het apparaat verbinding maken. Als **deze is** uitgeschakeld, heeft dit apparaat geen toegang tot een apparaat-gericht eindpunt. |
| statusReason |optioneel |Een tekenreeks van 128 tekens lang die de reden voor de apparaat-id-status op bewaart. Alle UTF-8 tekens zijn toegestaan. |
| statusUpdateTime |alleen-lezen |Een tijdelijke indicator met de datum en tijd van de laatste statusupdate. |
| connectionState |alleen-lezen |Een veld dat de verbindingsstatus aangeeft: **Verbonden** of **Verbroken.** Dit veld vertegenwoordigt de IoT Hub weergave van de verbindingsstatus van het apparaat. **Belangrijk:** dit veld mag alleen worden gebruikt voor ontwikkelings-/debuggingsdoeleinden. De verbindingstoestand wordt alleen bijgewerkt voor apparaten die MQTT of AMQP gebruiken. Het is ook gebaseerd op pings op protocolniveau (MQTT-pings of AMQP-pings) en kan een maximale vertraging van slechts 5 minuten hebben. Om deze redenen kunnen er fout-positieven zijn, zoals apparaten die zijn gerapporteerd als verbonden, maar die niet zijn verbonden. |
| connectionStateUpdatedTime |alleen-lezen |Een tijdelijke indicator met de datum en laatste keer dat de verbindingsstatus is bijgewerkt. |
| lastActivityTime |alleen-lezen |Een tijdelijke indicator met de datum en laatste keer dat het apparaat verbinding heeft gemaakt, ontvangen of een bericht heeft verzonden. |

> [!NOTE]
> Op dit moment bieden de apparaat-SDK's geen ondersteuning voor het gebruik van de tekens en `+` `#` in de **deviceId** en **moduleId**.

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere naslagonderwerpen in IoT Hub ontwikkelaarshandleiding zijn:

* [IoT Hub eindpunten worden](iot-hub-devguide-endpoints.md) de verschillende eindpunten beschreven die elke IoT-hub blootstelt voor run-time- en beheerbewerkingen.

* [Beperking en quota beschrijven](iot-hub-devguide-quotas-throttling.md) de quota en beperkingsgedrag die van toepassing zijn op de IoT Hub service.

* SDK's voor [Azure IoT-apparaten](iot-hub-devguide-sdks.md) en -service bevat de verschillende taal-SDK's die u kunt gebruiken bij het ontwikkelen van apparaat- en service-apps die communiceren met IoT Hub.

* [IoT Hub querytaal beschrijft](iot-hub-devguide-query-language.md) de querytaal die u kunt gebruiken om informatie op te halen uit IoT Hub over uw apparaattweelingen en taken.

* [IoT Hub ondersteuning voor MQTT](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het IoT Hub identiteitsregister gebruikt, bent u mogelijk geïnteresseerd in de volgende onderwerpen IoT Hub ontwikkelaarshandleiding:

* [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

* [Apparaattweeling gebruiken om de status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)

* [Een directe methode op een apparaat aanroepen](iot-hub-devguide-direct-methods.md)

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Als u enkele concepten wilt uitproberen die in dit artikel worden beschreven, bekijkt u de volgende IoT Hub zelfstudie:

* [Aan de slag met Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Zie voor meer informatie over IoT Hub Device Provisioning Service het inschakelen van zero-touch, Just-In-Time-inrichting: 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)
