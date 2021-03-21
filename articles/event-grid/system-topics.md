---
title: Systeem onderwerpen in Azure Event Grid
description: Hierin worden systeem onderwerpen beschreven in Azure Event Grid.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b1fbecb1e372602f9c252d43d2a1f93524ef1846
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052962"
---
# <a name="system-topics-in-azure-event-grid"></a>Systeem onderwerpen in Azure Event Grid
Een systeem onderwerp in Event Grid vertegenwoordigt een of meer gebeurtenissen die zijn gepubliceerd door Azure-Services, zoals Azure Storage en Azure Event Hubs. Een systeem onderwerp kan bijvoorbeeld **alle BLOB-gebeurtenissen** vertegenwoordigen of alleen **blobs die zijn gemaakt** en **Verwijderde BLOB** -gebeurtenissen die zijn gepubliceerd voor een **specifiek opslag account**. Wanneer een BLOB in dit voor beeld wordt geüpload naar het opslag account, publiceert de Azure Storage-service een gebeurtenis die door een **blob is gemaakt** naar het onderwerp system in Event grid, waarna de gebeurtenis wordt doorgestuurd naar de [abonnees](event-handlers.md) van het onderwerp die de gebeurtenis ontvangen en verwerken. 

> [!NOTE] 
> Alleen Azure-Services kunnen gebeurtenissen publiceren naar systeem onderwerpen. Daarom krijgt u geen eind punt-of toegangs sleutels die u kunt gebruiken om gebeurtenissen te publiceren zoals u voor aangepaste onderwerpen of domeinen.

## <a name="azure-services-that-support-system-topics"></a>Azure-Services die systeem onderwerpen ondersteunen
Hier volgt de huidige lijst met Azure-Services die ondersteuning bieden voor het maken van systeem onderwerpen.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
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

## <a name="system-topics-as-azure-resources"></a>Systeem onderwerpen als Azure-resources
In het verleden was een systeem onderwerp impliciet en niet beschikbaar voor eenvoud. Systeem onderwerpen zijn nu zichtbaar als Azure-resources en bieden de volgende mogelijkheden:

- [Systeem onderwerpen weer geven in de Azure Portal](create-view-manage-system-topics.md#view-all-system-topics)
- Resource Manager-sjablonen exporteren voor systeem onderwerpen en gebeurtenis abonnementen in de Azure Portal
- [Diagnostische logboeken voor systeem onderwerpen instellen](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Waarschuwingen instellen voor fouten bij publiceren en verzenden 

## <a name="lifecycle-of-system-topics"></a>Levens cyclus van systeem onderwerpen
U kunt op twee manieren een systeem onderwerp maken: 

- Maak een [gebeurtenis abonnement op een Azure-resource als een extensie bron](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), waarmee automatisch een systeem onderwerp wordt gemaakt met de naam in de volgende indeling: `<Azure resource name>-<GUID>` . Het systeem onderwerp dat op deze manier wordt gemaakt, wordt automatisch verwijderd wanneer het laatste gebeurtenis abonnement voor het onderwerp wordt verwijderd. 
- Maak een systeem onderwerp voor een Azure-resource en maak vervolgens een gebeurtenis abonnement voor dat systeem onderwerp. Wanneer u deze methode gebruikt, kunt u een naam opgeven voor het onderwerp System. Het onderwerp System wordt niet automatisch verwijderd wanneer het laatste gebeurtenis abonnement wordt verwijderd. U moet deze hand matig verwijderen. 

    Wanneer u de Azure Portal gebruikt, gebruikt u altijd deze methode. Wanneer u een gebeurtenis abonnement maakt met behulp [van de pagina **gebeurtenissen** van een Azure-resource](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage), wordt het onderwerp systeem eerst gemaakt en vervolgens het abonnement voor het onderwerp gemaakt. U kunt expliciet een systeem onderwerp maken met behulp van de [pagina **Event grid systeem onderwerpen**](create-view-manage-system-topics.md#create-a-system-topic) en vervolgens een abonnement maken voor dat onderwerp. 

Wanneer u een [cli](create-view-manage-system-topics-cli.md)-, [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)-of [Azure Resource Manager-sjabloon](create-view-manage-system-topics-arm.md)gebruikt, kunt u een van de bovenstaande methoden kiezen. We raden u aan om eerst een systeem onderwerp te maken en vervolgens een abonnement op het onderwerp te maken, omdat dit de meest recente manier is om systeem onderwerpen te maken.

Het maken van het systeem mislukt als u Azure-beleid zodanig hebt ingesteld dat de Event Grid-Service dit niet kan maken. U kunt bijvoorbeeld een beleid hebben waarmee alleen bepaalde typen resources kunnen worden gemaakt (bijvoorbeeld: Azure Storage, Azure Event Hubs, enzovoort) in het abonnement. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Locatie en resource groep voor een systeem onderwerp
Voor Azure-gebeurtenis bronnen die zich in een specifieke regio/locatie bevinden, wordt het systeem onderwerp gemaakt op dezelfde locatie als de Azure-gebeurtenis bron. Als u bijvoorbeeld een gebeurtenis abonnement maakt voor een Azure Blob-opslag in VS-Oost, wordt het onderwerp System gemaakt in VS-Oost. Voor algemene Azure-gebeurtenis bronnen, zoals Azure-abonnementen, resource groepen of Azure Maps, maakt Event Grid het onderwerp System op de **globale** locatie. 

In het algemeen wordt het systeem onderwerp gemaakt in dezelfde resource groep als waarin de Azure-gebeurtenis bron zich bevindt. Voor gebeurtenis abonnementen die zijn gemaakt bij een Azure-abonnement, wordt het onderwerp System gemaakt in de standaard resource groep **EventGrid** in de regio **VS West 2** . Als de resource groep niet bestaat, wordt deze door Azure Event Grid gemaakt voordat het onderwerp System wordt gemaakt. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Systeem onderwerpen maken, weer geven en beheren met behulp van Azure Portal](create-view-manage-system-topics.md).
- [Event Grid systeem onderwerpen maken, weer geven en beheren met behulp van Azure CLI](create-view-manage-system-topics-cli.md)
- [Event Grid systeem onderwerpen maken met behulp van Azure Resource Manager sjablonen](create-view-manage-system-topics-arm.md)
