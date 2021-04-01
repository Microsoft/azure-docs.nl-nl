---
title: Gebeurtenisroutes
titleSuffix: Azure Digital Twins
description: Meer informatie over het routeren van gebeurtenissen in azure Digital Apparaatdubbels en naar andere Azure-Services.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea412b695c12f3ff7fdfa6250e2a474b618b8032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102430918"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Gebeurtenissen binnen en buiten Azure Digital Apparaatdubbels routeren

Azure Digital apparaatdubbels gebruikt **gebeurtenis routes** voor het verzenden van gegevens naar gebruikers buiten de service. 

Er zijn twee belang rijke gevallen voor het verzenden van Azure Digital Apparaatdubbels-gegevens:
* Het verzenden van gegevens van een dubbele in de Azure Digital Apparaatdubbels-grafiek naar een andere. Als u bijvoorbeeld een eigenschap op één digitaal nadien wijzigt, wilt u wellicht een melding ontvangen en een andere digitale navolgende digitaal bijwerken.
* Verzenden van gegevens naar downstream gegevens Services voor extra opslag of verwerking (ook wel bekend als *gegevens* uitvoer). Bijvoorbeeld
  - Een zieken huis wil mogelijk Azure Digital Apparaatdubbels-gebeurtenis gegevens naar [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md)verzenden om de tijdreeks gegevens van handwashing gebeurtenissen voor bulk analyse vast te leggen.
  - Een bedrijf dat al gebruikmaakt van [Azure Maps](../azure-maps/about-azure-maps.md) kan gebruikmaken van Azure Digital apparaatdubbels om de oplossing te verbeteren. Ze kunnen snel een Azure-kaart inschakelen na het instellen van Azure Digital Apparaatdubbels, Azure-toewijzings entiteiten in azure Digital Apparaatdubbels als [digitale apparaatdubbels](concepts-twins-graph.md) in het dubbele diagram, of krachtige query's uitvoeren met behulp van hun Azure Maps en Azure Digital apparaatdubbels-gegevens samen.

Gebeurtenis routes worden gebruikt voor beide scenario's.

## <a name="about-event-routes"></a>Over gebeurtenis routes

Met een gebeurtenis route kunt u gebeurtenis gegevens van digitale apparaatdubbels in azure Digital Apparaatdubbels verzenden naar aangepaste eind punten in uw abonnementen. Drie Azure-Services worden momenteel ondersteund voor eind punten: [Event hub](../event-hubs/event-hubs-about.md), [Event grid](../event-grid/overview.md)en [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Elk van deze Azure-Services kan worden verbonden met andere services en fungeert als middelsteman, waarbij gegevens worden verzonden naar eind bestemmingen, zoals een TSI of Azure Maps voor de gewenste verwerking.

In het volgende diagram ziet u de stroom van gebeurtenis gegevens via een grotere IoT-oplossing met een Azure Digital Apparaatdubbels-aspect:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure Digital Apparaatdubbels routerings gegevens via eind punten naar verschillende downstream-Services" border="false":::

Typische downstream doelen voor gebeurtenis routes zijn bronnen als TSI-, Azure Maps-, opslag-en analyse oplossingen.

### <a name="event-routes-for-internal-digital-twin-events"></a>Gebeurtenis routes voor interne digitale dubbele gebeurtenissen

Gebeurtenis routes worden ook gebruikt voor het afhandelen van gebeurtenissen in de twee grafieken en het verzenden van gegevens van digitale twee naar digitale twee. Dit wordt gedaan door gebeurtenis routes te verbinden via Event Grid naar reken resources, zoals [Azure functions](../azure-functions/functions-overview.md). Deze functies bepalen vervolgens hoe apparaatdubbels moeten worden ontvangen en reageren op gebeurtenissen. 

Wanneer een compute-resource de dubbele grafiek wil wijzigen op basis van een gebeurtenis die hij via de gebeurtenis route heeft ontvangen, is het handig om te weten welke dubbele it in de loop van de tijd wil wijzigen. 

Het gebeurtenis bericht bevat ook de ID van de bron die het bericht heeft verzonden, zodat de compute-resource query's kan gebruiken of relaties kunt door lopen om een doel te vinden dat ligt voor de gewenste bewerking. 

De compute-resource moet ook afzonderlijke beveiligings-en toegangs machtigingen tot stand brengen.

Zie [*How-to: een Azure-functie instellen voor het verwerken van gegevens*](how-to-create-azure-function.md)om het proces van het instellen van een Azure-functie voor het verwerken van digitale dubbele gebeurtenissen door te lopen.

## <a name="create-an-endpoint"></a>Een eindpunt maken

Ontwikkelaars moeten eerst eindpunten definiëren om een gebeurtenisroute te definiëren. Een **eind punt** is een bestemming buiten Azure Digital apparaatdubbels die een route verbinding ondersteunt. Ondersteunde bestemmingen zijn onder andere:
* Aangepaste onderwerpen Event Grid
* Event Hub
* Service Bus

Als u een eind punt wilt maken, kunt u de Azure Digital Apparaatdubbels [rest api's, CLI-opdrachten](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)of de [Azure Portal](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)gebruiken.

Wanneer u een eind punt definieert, moet u het volgende opgeven:
* De naam van het eind punt
* Het type eind punt (Event Grid, Event hub of Service Bus)
* De primaire connection string en secundaire connection string voor verificatie 
* Het pad naar het onderwerp van het eind punt, bijvoorbeeld *Your-topic.westus2.eventgrid.Azure.net*

De eindpunt-Api's die beschikbaar zijn in het besturings vlak zijn:
* Eind punt maken
* Lijst met eind punten ophalen
* Eind punt ophalen op naam
* Eind punt verwijderen op naam

## <a name="create-an-event-route"></a>Een gebeurtenis route maken
 
Als u een gebeurtenis route wilt maken, kunt u de Azure Digital Apparaatdubbels [rest api's, CLI-opdrachten](how-to-manage-routes-apis-cli.md#create-an-event-route)of de [Azure Portal](how-to-manage-routes-portal.md#create-an-event-route)gebruiken.

Hier volgt een voor beeld van het maken van een gebeurtenis route binnen een client toepassing, met behulp van de `CreateOrReplaceEventRouteAsync` [.net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client) aanroep: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Eerst wordt een- `DigitalTwinsEventRoute` object gemaakt en de constructor neemt de naam van een eind punt. Dit `endpointName` veld identificeert een eind punt, zoals een event hub, Event grid of service bus. Deze eind punten moeten in uw abonnement worden gemaakt en aan Azure Digital Apparaatdubbels gekoppeld met behulp van Control-Api's voordat u deze registratie oproep doet.

2. Het gebeurtenis route object heeft ook een [**filter**](how-to-manage-routes-apis-cli.md#filter-events) veld dat kan worden gebruikt om de typen gebeurtenissen te beperken die volgen op deze route. Met een filter van `true` kunt u de route inschakelen zonder extra filters (een filter van `false` de route wordt uitgeschakeld). 

3. Dit gebeurtenis route object wordt vervolgens door gegeven aan `CreateOrReplaceEventRouteAsync` , samen met een naam voor de route.

> [!TIP]
> Alle SDK-functies zijn beschikbaar in synchrone en asynchrone versies.

## <a name="dead-letter-events"></a>Onbestelbare gebeurtenissen

Wanneer een eind punt een gebeurtenis binnen een bepaalde tijds periode niet kan leveren of nadat de gebeurtenis een bepaald aantal keren is geprobeerd, kan de gebeurtenis worden verzonden naar een opslag account. Dit proces wordt **onbestelbare berichten** genoemd. Azure Digital Apparaatdubbels stuurt een gebeurtenis onbestelbaar wanneer aan **een van de volgende** voor waarden wordt voldaan. 

* De gebeurtenis wordt niet binnen de time-to-Live-periode bezorgd
* Het aantal pogingen om de gebeurtenis te leveren overschrijdt de limiet

Als aan een van de voor waarden wordt voldaan, wordt de gebeurtenis verwijderd of onbestelbaar. Standaard schakelt elk eind punt **geen** onbestelbare berichten in. Als u deze functie wilt inschakelen, moet u een opslag account opgeven om niet-bezorgde gebeurtenissen op te slaan bij het maken van het eind punt. U kunt vervolgens gebeurtenissen uit dit opslag account ophalen om leveringen op te lossen.

Voordat u de locatie van de onbestelbare letter instelt, moet u een opslag account hebben met een container. U geeft de URL voor deze container op wanneer u het eind punt maakt. De onbestelbare letter wordt gegeven als container-URL met een SAS-token. Dit token heeft alleen `write` machtigingen nodig voor de doel container in het opslag account. De volledig opgemaakte URL heeft de volgende indeling: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Zie voor meer informatie over SAS-tokens: [ *beperkte toegang verlenen tot Azure storage resources met behulp van Shared Access signatures (SAS)*](../storage/common/storage-sas-overview.md)

Voor meer informatie over het instellen van een eind punt met onbestelbare berichten raadpleegt u [*How to: eind punten en routes beheren in azure Digital apparaatdubbels (api's en CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Typen gebeurtenis berichten

Verschillende typen gebeurtenissen in IoT Hub en Azure Digital Apparaatdubbels genereren verschillende typen meldings berichten, zoals hieronder wordt beschreven.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Volgende stappen

Zie een gebeurtenis route instellen en beheren:
* [*Instructies: eind punten en routes beheren*](how-to-manage-routes-apis-cli.md)

U kunt ook zien hoe u Azure Functions gebruikt voor het routeren van gebeurtenissen in azure Digital Apparaatdubbels:
* [*Instructies: een Azure-functie voor het verwerken van gegevens instellen*](how-to-create-azure-function.md)