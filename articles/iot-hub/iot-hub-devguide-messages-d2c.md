---
title: Meer informatie over Azure IoT Hub Message Routing | Microsoft Docs
description: 'Ontwikkelaars handleiding: berichten routering gebruiken om apparaat-naar-Cloud-berichten te verzenden. Bevat informatie over het verzenden van zowel telemetrie-als niet-telemetrie-gegevens.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: d2fe8f32ec854e1e6db644a039e6a249cfbddcaa
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012884"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met bericht routering kunt u berichten van uw apparaten naar Cloud Services verzenden in een geautomatiseerde, schaal bare en betrouw bare manier. Bericht routering kan worden gebruikt voor: 

* Het **verzenden van telemetrie-telefoon berichten en gebeurtenissen** , zoals levenscyclus gebeurtenissen van apparaten, gebeurtenissen met een dubbele wijziging van het apparaat en gebeurtenissen met een digitale dubbele wijziging naar de ingebouwde eind punten en aangepaste eind punten. Meer informatie over [Routing-eind punten](#routing-endpoints). Zie voor meer informatie over de gebeurtenissen die worden verzonden vanuit IoT Plug en Play-apparaten [inzicht in iot Plug en Play Digital apparaatdubbels](../iot-pnp/concepts-digital-twin.md).

* **Gegevens filteren voordat deze naar verschillende eind punten worden doorgestuurd** door uitgebreide query's toe te passen. Met bericht routering kunt u een query uitvoeren op de bericht eigenschappen en de bericht tekst, evenals dubbele labels voor apparaten en dubbele eigenschappen van het apparaat. Meer informatie over het gebruik [van query's in bericht routering](iot-hub-devguide-routing-query-syntax.md).

IoT Hub moet schrijf toegang hebben tot deze service-eind punten voor het werken met bericht routering. Als u uw eind punten via de Azure Portal configureert, worden de benodigde machtigingen voor u toegevoegd. Zorg ervoor dat u uw services zo configureert dat de verwachte door Voer wordt ondersteund. Als u bijvoorbeeld Event Hubs als een aangepast eind punt gebruikt, moet u de **doorvoer eenheden** voor die Event hub configureren zodat deze de ingangs gebeurtenissen kan verwerken die u via IOT hub bericht routering wilt verzenden. Op dezelfde manier moet u, wanneer u een Service Bus wachtrij als eind punt gebruikt, de **maximale grootte** configureren om ervoor te zorgen dat de wachtrij alle gegevens kan bevatten, totdat deze egressed door consumenten. Wanneer u uw IoT-oplossing voor het eerst configureert, moet u mogelijk de extra eind punten controleren en alle benodigde aanpassingen voor de werkelijke belasting aanbrengen.

Met de IoT Hub definieert u een [algemene indeling](iot-hub-devguide-messages-construct.md) voor alle apparaat-naar-Cloud-berichten voor interoperabiliteit in verschillende protocollen. Als een bericht overeenkomt met meerdere routes die naar hetzelfde eind punt wijzen, wordt IoT Hub alleen een bericht voor dat eind punt bezorgd. Daarom hoeft u geen ontdubbeling te configureren voor uw Service Bus wachtrij of onderwerp. In gepartitioneerde wacht rijen, wordt de rang orde van berichten door partitie-affiniteit gegarandeerd. Gebruik deze zelf studie voor meer informatie over het [configureren van bericht routering](tutorial-routing.md).

## <a name="routing-endpoints"></a>Routeringseindpunten

Een IoT-hub heeft een standaard ingebouwde eind punt (**berichten/gebeurtenissen**) die compatibel is met Event hubs. U kunt [aangepaste eind punten](iot-hub-devguide-endpoints.md#custom-endpoints) maken om berichten naar te sturen door andere services in uw abonnement te koppelen aan de IOT hub. 

Elk bericht wordt doorgestuurd naar alle eind punten waarvan de Routing query's overeenkomen. Met andere woorden, een bericht kan naar meerdere eind punten worden doorgestuurd.

Als uw aangepaste eind punt firewall configuraties heeft, kunt u de micro soft Trusted First partij Exception gebruiken om uw IoT Hub toegang te geven tot het specifieke eind punt [Azure Storage](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) [Azure Event hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) en [Azure service bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing). Dit is beschikbaar in bepaalde regio's voor IoT-hubs met een [beheerde service-identiteit](./virtual-network-support.md).

IoT Hub ondersteunt momenteel de volgende eind punten:

 - Ingebouwd eind punt
 - Azure Storage
 - Service Bus-wacht rijen en Service Bus onderwerpen
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Ingebouwd eind punt als een eind punt voor route ring

U kunt standaard [Event hubs integratie en sdk's](iot-hub-devguide-messages-read-builtin.md) gebruiken om apparaat-naar-Cloud-berichten te ontvangen van het ingebouwde eind punt (**berichten/gebeurtenissen**). Zodra een route is gemaakt, stopt de gegevens naar het ingebouwde eind punt, tenzij er een route naar dat eind punt wordt gemaakt.

## <a name="azure-storage-as-a-routing-endpoint"></a>Azure Storage als een eind punt voor route ring

Er zijn twee opslag Services IoT Hub kunnen berichten verzenden naar-- [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) -en [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -accounts (ADLS Gen2). Azure Data Lake Storage accounts zijn [hiërarchische](../storage/blobs/data-lake-storage-namespace.md)opslag accounts met naam ruimten die boven op de Blob-opslag zijn gebouwd. Beide gebruiken blobs voor hun opslag.

IoT Hub ondersteunt het schrijven van gegevens naar Azure Storage in de [Apache Avro](https://avro.apache.org/) -indeling en in de JSON-indeling. De standaardindeling is AVRO. Wanneer u JSON-code ring gebruikt, moet u het content type instellen op **Application/JSON** en ContentEncoding naar **UTF-8** in de eigenschappen van het bericht [systeem](iot-hub-devguide-routing-query-syntax.md#system-properties). Beide waarden zijn niet hoofdletter gevoelig. Als de inhouds codering niet is ingesteld, schrijft IoT Hub de berichten in de indeling basis 64-code ring.

De coderings indeling kan alleen worden ingesteld wanneer het eind punt van de Blob-opslag is geconfigureerd; het kan niet worden bewerkt voor een bestaand eind punt. Als u de coderings indelingen voor een bestaand eind punt wilt wijzigen, moet u het aangepaste eind punt verwijderen en opnieuw maken met de gewenste indeling. Een handige strategie is het maken van een nieuw aangepast eind punt met de gewenste coderings indeling en het toevoegen van een parallelle route aan dat eind punt. Op deze manier kunt u uw gegevens controleren voordat u het bestaande eind punt verwijdert.

U kunt de coderings indeling selecteren met behulp van de IoT Hub REST API maken of bijwerken, met name de [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), de Azure Portal, de [Azure CLI](/cli/azure/iot/hub/routing-endpoint)of de [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint). In de volgende afbeelding ziet u hoe u de coderings indeling kunt selecteren in de Azure Portal.

![Eindpunt codering van Blob-opslag](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub batch berichten en schrijft gegevens naar de opslag wanneer de batch een bepaalde omvang of een bepaalde hoeveelheid tijd heeft bereikt. IoT Hub wordt standaard ingesteld op de volgende bestands naam Conventie:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

U kunt elke bestands naam Conventie gebruiken, maar u moet alle vermelde tokens gebruiken. IoT Hub wordt naar een lege BLOB geschreven als er geen gegevens zijn om te schrijven.

We raden u aan om de blobs of bestanden te vermelden en vervolgens te herhalen, om ervoor te zorgen dat alle blobs of bestanden worden gelezen zonder dat er veronderstellingen worden gemaakt van de partitie. Het partitie bereik kan mogelijk worden gewijzigd tijdens een door [micro soft geïnitieerde failover](iot-hub-ha-dr.md#microsoft-initiated-failover) of IOT hub [hand matige failover](iot-hub-ha-dr.md#manual-failover). U kunt de [List blobs API](/rest/api/storageservices/list-blobs) gebruiken om de lijst met blobs of de [lijst ADLS Gen2-API](/rest/api/storageservices/datalakestoragegen2/path/list) voor de lijst met bestanden op te sommen. Raadpleeg het volgende voor beeld als richt lijn.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Als u een met Azure Data Lake Gen2 compatibel opslag account wilt maken, maakt u een nieuw v2-opslag account en selecteert u *ingeschakeld* op het veld *hiërarchische naam ruimte* op het tabblad **Geavanceerd** , zoals wordt weer gegeven in de volgende afbeelding:

![Azure date Lake Gen2-opslag selecteren](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Service Bus-wacht rijen en Service Bus-onderwerpen als een eind punt voor route ring

Service Bus-wacht rijen en-onderwerpen die als IoT Hub-eind punten worden gebruikt, mogen geen **sessies** of **Duplicaten detectie** zijn ingeschakeld. Als een van deze opties is ingeschakeld, wordt het eind punt weer gegeven als **onbereikbaar** in de Azure Portal.

## <a name="event-hubs-as-a-routing-endpoint"></a>Event Hubs als een eind punt voor route ring

Naast het ingebouwde Event Hubs compatibele eind punt, kunt u ook gegevens routeren naar aangepaste eind punten van het type Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Gegevens lezen die zijn doorgestuurd

U kunt een route configureren door deze [zelf studie](tutorial-routing.md)te volgen.

Gebruik de volgende zelf studies voor meer informatie over het lezen van een bericht vanuit een eind punt.

* Lezen van [ingebouwd eind punt](quickstart-send-telemetry-node.md)

* Lezen uit [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Lezen van [Event hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lezen van [Service Bus wachtrijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lezen uit [Service Bus onderwerpen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)


## <a name="fallback-route"></a>Alternatieve route

De terugval route verzendt alle berichten die niet voldoen aan de query voorwaarden voor een van de bestaande routes naar de ingebouwde Event Hubs (**berichten/gebeurtenissen**) die compatibel zijn met [Event hubs](../event-hubs/index.yml). Als bericht routering is ingeschakeld, kunt u de mogelijkheid om de terugval route te gebruiken in te scha kelen. Zodra een route is gemaakt, stopt de gegevens stroom naar het ingebouwde eind punt, tenzij er een route naar dat eind punt wordt gemaakt. Als er geen routes naar het ingebouwde eind punt en een terugval route zijn ingeschakeld, worden alleen berichten die niet overeenkomen met de query voorwaarden op routes, verzonden naar het ingebouwde eind punt. Als alle bestaande routes worden verwijderd, moet er ook een terugval route worden ingeschakeld om alle gegevens bij het ingebouwde eind punt te ontvangen.

U kunt de terugval route in-of uitschakelen op de Blade voor de Azure Portal->bericht routering. U kunt Azure Resource Manager ook gebruiken voor [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) om een aangepast eind punt voor terugval route te gebruiken.

## <a name="non-telemetry-events"></a>Niet-telemetrie-gebeurtenissen

Naast het telemetrie van apparaten kunnen ook bericht routering het verzenden van dubbele wijzigings gebeurtenissen, levenscyclus gebeurtenissen van apparaten en gebeurtenissen voor digitale dubbele wijzigingen. Als er bijvoorbeeld een route is gemaakt met een gegevens bron die is ingesteld op **dubbele wijzigings gebeurtenissen** van het apparaat, IOT hub stuurt berichten naar het eind punt dat de wijziging in het dubbele apparaat bevat. Als er een route is gemaakt met een gegevens bron die is ingesteld op **levens cyclus gebeurtenissen van apparaten**, IOT hub een bericht verzenden dat aangeeft of het apparaat is verwijderd of gemaakt. Ten slotte kan een ontwikkelaar, als onderdeel van [Azure IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md), routes maken met een gegevens bron die is ingesteld op **digitale dubbele wijzigings gebeurtenissen** en IOT hub berichten verzendt wanneer een digitale dubbele eigenschap is ingesteld of gewijzigd, een digitaal dubbel punt wordt vervangen of wanneer een wijzigings gebeurtenis plaatsvindt voor het onderliggende apparaat dubbele.

[IOT hub integreert ook met Azure Event grid](iot-hub-event-grid.md) voor het publiceren van faxgebeurtenissen om realtime integraties en automatisering van werk stromen te ondersteunen op basis van deze gebeurtenissen. Bekijk de belangrijkste [verschillen tussen bericht Routering en Event grid](iot-hub-event-grid-routing-comparison.md) om te leren welke het beste werkt voor uw scenario.

## <a name="testing-routes"></a>Routes testen

Wanneer u een nieuwe route maakt of een bestaande route bewerkt, moet u de route query testen met een voorbeeld bericht. U kunt afzonderlijke routes testen of alle routes tegelijk testen en er tijdens de test geen berichten naar de eind punten worden doorgestuurd. Azure Portal, Azure Resource Manager, Azure PowerShell en Azure CLI kunnen worden gebruikt voor het testen. Resultaten helpen te bepalen of het voorbeeld bericht overeenkomt met de query. het bericht komt niet overeen met de query of de test kan niet worden uitgevoerd omdat het voorbeeld bericht of de query syntaxis onjuist is. Zie [route ring testen](/rest/api/iothub/iothubresource/testroute) en [alle routes testen](/rest/api/iothub/iothubresource/testallroutes)voor meer informatie.

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Garanties best Ellen met ten minste eenmaal per levering

IoT Hub berichten routering gegarandeerd besteld en ten minste één keer per levering van berichten aan de eind punten. Dit betekent dat er dubbele berichten kunnen zijn en dat een reeks berichten opnieuw kan worden verzonden met de oorspronkelijke bericht volgorde. Als de oorspronkelijke bericht volgorde bijvoorbeeld [1, 2, 3, 4] is, kunt u een bericht reeks als volgt ontvangen: [1, 2, 1, 2, 3, 1, 2, 3, 4]. Als u altijd bericht [1] ontvangt, wordt het gegarandeerd dat de service [2, 3, 4] wordt gevolgd.

Voor het afhandelen van berichten wordt u aangeraden een unieke id te stempelen in de toepassings eigenschappen van het bericht op het punt van de oorsprong, meestal een apparaat of module. De service die de berichten verbruikt, kan dubbele berichten verwerken die gebruikmaken van deze id.

## <a name="latency"></a>Latentie

Wanneer u apparaat-naar-Cloud-telemetrie-berichten rondstuurt met ingebouwde eind punten, is er een lichte toename van de end-to-end-latentie na het maken van de eerste route.

In de meeste gevallen is de gemiddelde toename in latentie kleiner dan 500 MS. U kunt de latentie bewaken met behulp **van route ring: bericht latentie voor berichten/gebeurtenissen** of **D2C. endpoints. latentie. builtIn. events** IOT hub metrische gegevens. Het maken of verwijderen van een route na de eerste heeft geen invloed op de end-to-end-latentie.

## <a name="monitoring-and-troubleshooting"></a>Bewaking en problemen oplossen

IoT Hub biedt diverse metrische gegevens met betrekking tot route ring en eind punten om u een overzicht te geven van de status van uw hub en verzonden berichten. Voor een lijst met alle IoT Hub metrische gegevens die zijn uitgesplitst per functionele categorie, raadpleegt u [de waarden in de naslag informatie over bewaking](monitor-iot-hub-reference.md#metrics). U kunt fouten volgen die zich voordoen tijdens de evaluatie van een routerings query en de eindpunt status, zoals wordt waargenomen door IoT Hub met de [categorie **routes** in IOT hub bron logboeken](monitor-iot-hub-reference.md#routes). Zie [IOT hub bewaken](monitor-iot-hub.md)voor meer informatie over het gebruik van metrische gegevens en resource logboeken met IOT hub.

U kunt de REST API status van [eind punt ophalen](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) gebruiken om [de status van](iot-hub-devguide-endpoints.md#custom-endpoints) de eind punten op te halen.

Gebruik de [gids voor probleem oplossing voor route ring](troubleshoot-message-routing.md) voor meer informatie en ondersteuning voor het oplossen van problemen met route ring.

## <a name="next-steps"></a>Volgende stappen

* Zie [IOT hub apparaat-naar-Cloud-berichten verwerken met routes](tutorial-routing.md)voor meer informatie over het maken van bericht routes.

* [Apparaat-naar-Cloud-berichten verzenden](quickstart-send-telemetry-node.md)

* Zie [Azure IOT sdk's](iot-hub-devguide-sdks.md)voor informatie over de sdk's die u kunt gebruiken om apparaat-naar-Cloud-berichten te verzenden.