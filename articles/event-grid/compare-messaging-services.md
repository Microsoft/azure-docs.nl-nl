---
title: Azure-berichtenservices vergelijken
description: Beschrijft de drie berichtenservices van - Azure Event Grid, Event Hubs en Service Bus. Geeft een aanbeveling over welke service voor verschillende scenario's het meest geschikt is.
ms.topic: overview
ms.date: 07/07/2020
ms.openlocfilehash: 7a3a0cd7f63a67206053ae55f33bd71aee2c19c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460402"
---
# <a name="choose-between-azure-messaging-services---event-grid-event-hubs-and-service-bus"></a>Kiezen tussen berichtenservices van Azure - Event Grid, Event Hubs en Service Bus

Azure beschikt over drie services die u helpen met het bezorgen van de gebeurtenisberichten binnen een oplossing. Deze services zijn:

* [Event Grid](./index.yml)
* [Event Hubs](../event-hubs/index.yml)
* [Service Bus](../service-bus-messaging/index.yml)

Hoewel ze een aantal overeenkomsten hebben, is elke service ontworpen voor specifieke scenario's. In dit artikel worden de verschillen tussen deze services beschreven en wordt uitgelegd welke voor uw toepassing het meest geschikt is. In veel gevallen zijn de berichtenservices aanvullend en kunnen ze samen worden gebruikt.

## <a name="event-vs-message-services"></a>Gebeurtenis- versus berichtenservices

Er is een belangrijk verschil tussen services die een gebeurtenis bezorgen en services die een bericht bezorgen.

### <a name="event"></a>Gebeurtenis

Een gebeurtenis is een lichtgewicht melding van een voorwaarde of een statuswijziging. De uitgever van de gebeurtenis heeft geen verwachting over hoe de gebeurtenis wordt afgehandeld. De consument van de gebeurtenis bepaalt wat er met de melding gebeurt. Gebeurtenissen kunnen afzonderlijke eenheden zijn of deel uitmaken van een reeks.

Afzonderlijke gebeurtenissen melden statuswijzigingen naar aanleiding waarvan acties kunnen worden uitgevoerd. Om de volgende stap te zetten, hoeft de consument alleen te weten dat er iets is gebeurd. De gebeurtenisgegevens bevatten informatie over wat er is gebeurd, maar bevatten niet de gegevens die de gebeurtenis hebben geactiveerd. Zo kan een gebeurtenis consumenten ervan op de hoogte stellen dat er een bestand is gemaakt. De gebeurtenis kan algemene informatie over het bestand bevatten, maar niet het bestand zelf. Afzonderlijke gebeurtenissen zijn ideaal voor [serverloze](https://azure.com/serverless) oplossingen die moeten worden geschaald.

Reeksgebeurtenissen rapporteren een voorwaarde en zijn analyseerbaar. De gebeurtenissen zijn chronologisch geordend en zijn met elkaar verbonden. De consument heeft de geordende reeks gebeurtenissen nodig om te kunnen analyseren wat is er gebeurd.

### <a name="message"></a>Bericht

Een bericht bestaat uit onbewerkte gegevens die zijn geproduceerd door een service en die moeten worden gebruikt of elders worden opgeslagen. Het bericht bevat de gegevens die de berichtenpijplijn hebben geactiveerd. De uitgever van het bericht heeft een verwachting over hoe de consument omgaat met het bericht. Er bestaat een overeenkomst tussen de twee zijden. Bijvoorbeeld, de uitgever verzendt een bericht met de onbewerkte gegevens en verwacht van de consument dat deze een bestand van die gegevens maakt en een antwoord terugstuurt wanneer dit is gebeurd.

## <a name="comparison-of-services"></a>Vergelijking van services

| Service | Doel | Type | Wanneer gebruikt u dit? |
| ------- | ------- | ---- | ----------- |
| Event Grid | Reactieve programmering | Gebeurtenisdistributie (discreet) | Reageren op statuswijzigingen |
| Event Hubs | Big Data-pijplijn | Gebeurtenisstromen (reeks) | Telemetrie- en gedistribueerde gegevensstromen |
| Service Bus | Hoogwaardige bedrijfsberichten | Bericht | Orderverwerking en financiële transacties |

### <a name="event-grid"></a>Event Grid

Event Grid is een gebeurtenisbackplane die gebeurtenisafhankelijke en reactieve programmering mogelijk maakt. Hiervoor wordt een Publiceren-Abonneren-model gebruikt. Uitgevers verzenden gebeurtenissen, maar hebben geen verwachting over welke gebeurtenissen worden afgehandeld. Abonnees bepalen welke gebeurtenissen ze willen afhandelen.

Event Grid is in grote mate geïntegreerd met Azure-services en kan worden geïntegreerd met services van derden. Het vereenvoudigt het verbruik van gebeurtenissen en verlaagt de kosten omdat constante polling niet nodig is. Event Grid routeert op efficiënte en betrouwbare wijze gebeurtenissen van Azure- en niet-Azure-resources. Het distribueert gebeurtenissen aan eindpunten van geregistreerde abonnees. Het gebeurtenisbericht bevat de informatie die u nodig hebt om te reageren op wijzigingen in services en toepassingen. Event Grid is geen gegevenspijplijn en levert niet het werkelijke object dat is bijgewerkt.

Event Grid biedt ondersteuning voor plaatsing in wachtrij voor onbestelbare berichten voor gebeurtenissen die niet zijn bezorgd bij een eindpunt.

Het heeft de volgende kenmerken:

* dynamisch schaalbaar
* lage kosten
* serverloos
* minimaal één keer levering

### <a name="event-hubs"></a>Event Hubs

Azure Event Hubs is een Big Data-pijplijn. Het vereenvoudigt het vastleggen, bewaren en opnieuw afspelen van telemetrie- en gebeurtenisstroomgegevens. De gegevens kunnen afkomstig zijn van veel gelijktijdige bronnen. Dankzij Event Hubs kunnen telemetrie- en gebeurtenisgegevens beschikbaar worden gesteld aan diverse stroomverwerkingsinfrastructuren en analyseservices. De gegevens zijn beschikbaar als gegevensstromen of gebundelde gebeurtenisbatches. Deze service biedt één oplossing waarmee u snel gegevens kunt ophalen voor realtime verwerking, maar ook voor het herhaaldelijk afspelen van opgeslagen onbewerkte gegevens. Hiermee kunnen de streaminggegevens voor verwerking en analyse worden vastgelegd in een bestand.

Het heeft de volgende kenmerken:

* lage latentie
* kan miljoenen gebeurtenissen per seconde ontvangen en verwerken
* minimaal één keer levering

### <a name="service-bus"></a>Service Bus

Service Bus is bedoeld voor traditionele bedrijfstoepassingen. Deze bedrijfstoepassingen hebben transacties, bestellingen, detectie van duplicaten en onmiddellijk consistentie nodig. Met Service Bus kunnen [cloudsysteemeigen](https://azure.microsoft.com/overview/cloudnative/) toepassingen betrouwbaar statusovergangsbeheer bieden voor bedrijfsprocessen. Gebruik Azure Service Bus bij de afhandeling van hoogwaardige berichten die niet verloren mogen gaan of gedupliceerd mogen worden. Service Bus vergemakkelijkt ook uitermate beveiligde communicatie tussen hybride cloudoplossingen en kan bestaande on-premises systemen verbinden met cloudoplossingen.

Service Bus is een Brokered Messaging-systeem. Het slaat berichten op in een 'broker' (bijvoorbeeld een wachtrij) tot de ontvangende partij gereed is om ze te ontvangen.

Het heeft de volgende kenmerken:

* betrouwbare asynchrone levering van berichten (enterprise messaging als een service) waarvoor polling is vereist
* geavanceerde berichtfuncties, zoals FIFO, batchverwerking/sessies, transacties, verwerking van onbestelbare berichten, tijdelijke controle, doorsturen en filteren en detectie van duplicaten
* minimaal één keer levering
* optionele levering op volgorde

## <a name="use-the-services-together"></a>De services samen gebruiken

In sommige gevallen kunt u de services naast elkaar gebruiken om verschillende rollen te kunnen vervullen. Zo kan een e-commerce-site Service Bus gebruiken om orders te verwerken, Event Hubs om telemetrie van de site vast te leggen en Event Grid om te reageren op gebeurtenissen, bijvoorbeeld een artikel dat is verzonden.

In andere gevallen kunt u ze koppelen om een gebeurtenis- en gegevenspijplijn te vormen. U gebruikt Event Grid om te reageren op gebeurtenissen in de andere services. Zie [Stream big data into a data warehouse](event-grid-event-hubs-integration.md) (Big Data streamen naar een data warehouse) voor een voorbeeld van het gebruik van Event Grid met Event Hubs om gegevens te migreren naar een datawarehouse. In de volgende afbeelding ziet u de werkstroom voor het streamen van de gegevens.

![Overzicht van gegevensstroom](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 
- [Opties voor asynchrone berichten in Azure](/azure/architecture/guide/technology-choices/messaging)
- [Gebeurtenissen, gegevenspunten en berichten - De juiste Azure-berichtenservice voor uw gegevens kiezen](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
- [Storage Queues and Service Bus Queues - Compared and Contrasted](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md) (Storage-wachtrijen en Service Bus-wachtrijen: overeenkomsten en verschillen)
- Zie [Aangepaste gebeurtenissen maken en routeren met behulp van Azure Event Grid](custom-event-quickstart.md) om aan de slag te gaan met Event Grid.
- Zie [Create an Event Hubs namespace and an event hub using the Azure portal](../event-hubs/event-hubs-create.md) (Een Event Hubs-naamruimte en een Event Hub maken met Azure Portal) om aan de slag te gaan met Event Hubs.
- Zie [Een Service Bus-naamruimte maken met Azure Portal](../service-bus-messaging/service-bus-create-namespace-portal.md) om aan de slag te gaan met Service Bus.
