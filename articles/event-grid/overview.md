---
title: Wat is Azure Event Grid?
description: Gebeurtenisgegevens vanaf een bron naar handlers verzenden met Azure Event Grid. Op gebeurtenissen gebaseerde toepassingen ontwikkelen en integreren met Azure-services.
ms.topic: overview
ms.date: 01/28/2021
ms.openlocfilehash: e53665c88c3860d37b3512c6498ab626b02a6400
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055128"
---
# <a name="what-is-azure-event-grid"></a>Wat is Azure Event Grid?

Met Azure Event Grid kunt u eenvoudig toepassingen bouwen met op gebeurtenissen gebaseerde architecturen. Selecteer eerst de Azure-resource waarop u zich wilt abonneren en geef vervolgens de gebeurtenis-handler of het WebHook-eindpunt op waarnaar de gebeurtenis moet worden verzonden. Event Grid bevat ingebouwde ondersteuning voor gebeurtenissen die afkomstig zijn van Azure-services, zoals storage-blobs en resourcegroepen. Event Grid biedt ook ondersteuning voor uw eigen gebeurtenissen, met behulp van aangepaste onderwerpen. 

U kunt filters gebruiken voor het doorsturen van specifieke gebeurtenissen naar verschillende eindpunten, multicasting uitvoeren naar meerdere eindpunten en ervoor zorgen dat uw gebeurtenissen op betrouwbare wijze worden bezorgd.

Azure Event Grid wordt geïmplementeerd om de beschikbaarheid te maximaliseren door systeem-eigen verspreiding uit te breiden over meerdere foutdomeinen in elke regio, en over beschikbaarheidszones (in regio's die deze ondersteunen). Voor een lijst met regio's die door Event Grid worden ondersteund, raadpleegt u [Producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

In dit artikel vindt u een overzicht van Azure Event Grid. Zie [Aangepaste gebeurtenissen maken en routeren met behulp van Azure Event Grid](custom-event-quickstart.md) als u aan de slag wilt met Azure Event Grid. 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Event Grid-model van bronnen en handlers" lightbox="./media/overview/functional-model-big.png":::

Deze afbeelding toont hoe Event Grid bronnen en handlers verbindt, maar biedt geen uitgebreide lijst met ondersteunde integraties.

## <a name="event-sources"></a>Gebeurtenisbronnen

Op dit moment ondersteunen de volgende Azure-services het verzenden van gebeurtenissen naar Event Grid. Selecteer de koppeling voor meer informatie over een bron in de lijst.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure-resourcegroepen](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure-abonnementen](event-schema-subscriptions.md)
- [Azure Cache voor Redis](event-schema-azure-cache.md)

## <a name="event-handlers"></a>Event Handlers

Zie [gebeurtenis-handlers](event-handlers.md) voor meer informatie over de mogelijkheden van elke handler, evenals de gerelateerde artikelen. Op dit moment ondersteunen de volgende Azure-services handling-gebeurtenissen uit Event Grid: 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Hybride verbindingen doorgeven](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Power Automate (voorheen bekend als Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [Webhooks](handler-webhooks.md)

## <a name="concepts"></a>Concepten

Azure Event Grid bevat vijf concepten waarmee u aan de slag kunt:

* **Gebeurtenissen**: wat er is gebeurd.
* **Gebeurtenisbronnen**: waar de gebeurtenis heeft plaatsgevonden.
* **Onderwerpen**: het eindpunt waarnaar uitgevers gebeurtenissen verzenden.
* **Gebeurtenisabonnementen**: het eindpunt of ingebouwde mechanisme voor het routeren van gebeurtenissen, soms naar meerdere handlers. Abonnementen worden ook gebruikt door handlers om binnenkomende gebeurtenissen op een slimme manier te filteren.
* **Gebeurtenis-handlers**: de app of de service die op de gebeurtenis reageert.

Zie [Concepten in Azure Event Grid](concepts.md) voor meer informatie over deze concepten.

## <a name="capabilities"></a>Functionaliteit

Hier volgt een aantal essentiële functies van Azure Event Grid:

* **Eenvoud**: wijs en klik om gebeurtenissen uit uw Azure-resource te richten op een gebeurtenis-handler of eindpunt.
* **Geavanceerde filters**: filter op gebeurtenistype of gebeurtenispublicatiepad om ervoor te zorgen dat gebeurtenis-handlers alleen relevante gebeurtenissen ontvangen.
* **Distributie**: abonneer verschillende eindpunten op dezelfde gebeurtenis om kopieën van de gebeurtenis te verzenden naar zoveel plaatsen als nodig is.
* **Betrouwbaarheid**: probeer 24 uur lang opnieuw met exponentieel uitstel om er zeker van te zijn dat gebeurtenissen worden bezorgd.
* **Betalen per gebeurtenis**: betaal alleen voor het bedrag waarvoor u Event Grid gebruikt.
* **Hoge doorvoer**: bouw workloads voor hoge volumes in Event Grid.
* **Ingebouwde gebeurtenissen**: ga snel aan de slag met voor resources gedefinieerde ingebouwde gebeurtenissen.
* **Aangepaste gebeurtenissen**: gebruik Event Grid om aangepaste gebeurtenissen op een betrouwbare manier te routeren, filteren en af te leveren.

Zie [Een keuze maken tussen Azure-services die berichten bezorgen](compare-messaging-services.md) voor een vergelijking van Event Grid, Event Hubs en Service Bus.

## <a name="what-can-i-do-with-event-grid"></a>Wat kan ik doen met Event Grid?

Azure Event Grid beschikt over verschillende functies die werkzaamheden zonder servers en met betrekking tot de automatisering van bewerkingen en [integratie](https://azure.com/integration) aanzienlijk verbeteren: 

### <a name="serverless-application-architectures"></a>Architecturen voor serverloze toepassingen

![Architectuur voor serverloze toepassingen](./media/overview/serverless_web_app.png)

Event Grid verbindt gegevensbronnen en gebeurtenis-handlers. Gebruik Event Grid bijvoorbeeld voor het activeren van een serverloze functie waarmee afbeeldingen worden geanalyseerd wanneer deze worden toegevoegd aan een blobopslagcontainer. 

### <a name="ops-automation"></a>Automatisering van bewerkingen

![De automatisering van bewerkingen](./media/overview/Ops_automation.png)

Met Event Grid kunt u sneller automatiseren en gemakkelijker beleid afdwingen. U kunt bijvoorbeeld met Event Grid een melding sturen naar Azure Automation wanneer er een virtuele machine of database in Azure SQL wordt gemaakt. Gebruik de gebeurtenissen om automatisch te controleren of serviceconfiguraties compatibel zijn, metagegevens aan te bieden aan tools voor bewerkingen, virtuele machines te taggen of werkitems te archiveren.

### <a name="application-integration"></a>Integratie van toepassingen

![Integratie van toepassingen met Azure](./media/overview/app_integration.png)

Event Grid verbindt uw app met andere services. Maak bijvoorbeeld een aangepast onderwerp om de gebeurtenisgegevens van uw app naar Event Grid te versturen en zo uw voordeel te doen met de betrouwbare bezorging, geavanceerde routering en directe integratie met Azure. U kunt Event Grid ook gebruiken met Logic Apps om op elke locatie gegevens te verwerken, zonder dat u hiervoor code hoeft te schrijven. 

## <a name="how-much-does-event-grid-cost"></a>Wat kost Event Grid?

Azure Event Grid maakt gebruik van een prijsmodel voor betalen per gebeurtenis, zodat u alleen betaalt voor wat u gebruikt. De eerste 100.000 bewerkingen per maand zijn gratis. Bewerkingen worden gedefinieerd als inkomende gebeurtenissen, bezorgingspogingen voor abonnementen, beheeroproepen, en filteren op achtervoegsels van onderwerpen. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/event-grid/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Storage Blob-gebeurtenissen routeren](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Reageer op Storage Blob-gebeurtenissen met Event Grid.
* [Aangepaste gebeurtenissen maken en u er op abonneren](custom-event-quickstart.md)  
  Begin meteen met het verzenden van uw eigen aangepaste gebeurtenissen naar een willekeurig eindpunt met behulp van de Azure Event Grid-quickstart.
* [Logic Apps als een gebeurtenis-handler gebruiken](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Een zelfstudie over het bouwen van een app met Logic Apps om te reageren op gebeurtenissen die door Event Grid zijn gepusht.
* [Big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)  
  Een zelfstudie die gebruikmaakt van Azure Functions om gegevens uit Event Hubs te streamen naar Azure Synapse Analytics.
* [Naslaginformatie over de REST-API voor Event Grid](/rest/api/eventgrid)  
  Bevat naslaginformatie voor het beheren van gebeurtenisabonnementen, routering en filters.