---
title: Meerdere onafhankelijke Azure Functions triggers maken voor Cosmos DB
description: Meer informatie over het configureren van meerdere onafhankelijke Azure Functions triggers voor Cosmos DB voor het maken van op gebeurtenissen gebaseerde architecturen.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fff48a97965f0b80456cd3e56ed1507bc784fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336670"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Meerdere Azure Functions triggers maken voor Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt beschreven hoe u meerdere Azure Functions-triggers zo kunt configureren dat Cosmos DB parallel werkt en onafhankelijk reageert op wijzigingen.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Functies die zijn gebaseerd op gebeurtenissen op basis van een server werken met de Azure Functions trigger voor het Cosmos DB en delen van een lease-container" border="false":::

## <a name="event-based-architecture-requirements"></a>Architectuur vereisten op basis van gebeurtenissen

Bij het bouwen van serverloze architecturen met [Azure functions](../azure-functions/functions-overview.md), is het [raadzaam](../azure-functions/functions-best-practices.md#avoid-long-running-functions) om kleine functie sets te maken die samen werken in plaats van grote langlopende functies.

Wanneer u op gebeurtenissen gebaseerde serverloze stromen bouwt met behulp [van de Azure functions trigger voor Cosmos DB](./change-feed-functions.md), voert u in het scenario uit waar u meerdere dingen wilt doen wanneer er een nieuwe gebeurtenis is in een bepaalde [Azure Cosmos-container](./account-databases-containers-items.md#azure-cosmos-containers). Als de acties die u wilt activeren, onafhankelijk van elkaar zijn, is de ideale oplossing om **een Azure functions triggers te maken voor Cosmos DB per actie** die u wilt uitvoeren. alle luistert naar wijzigingen in dezelfde Azure Cosmos-container.

## <a name="optimizing-containers-for-multiple-triggers"></a>Containers voor meerdere triggers optimaliseren

Gezien de *vereisten* van de Azure functions trigger voor Cosmos DB hebben we een tweede container nodig om de status op te slaan, ook wel de *container leases*. Betekent dit dat u een afzonderlijke leases-container nodig hebt voor elke Azure-functie?

Hier hebt u twee opties:

* **Eén leases-container maken per functie**: deze aanpak kan worden omgezet in aanvullende kosten, tenzij u een [Data Base met gedeelde door Voer](./set-throughput.md#set-throughput-on-a-database)gebruikt. Houd er rekening mee dat de minimale door Voer op het container niveau 400 [aanvraag eenheden](./request-units.md)is, en in het geval van de container leases, wordt dit alleen gebruikt om de voortgang te controleren en de status te onderhouden.
* Beschikken over **één lease container en delen deze** voor al uw functies: met deze tweede optie wordt het gebruik van de ingerichte aanvraag eenheden in de container verbeterd, omdat het meerdere Azure functions mogelijk maakt om dezelfde ingerichte door voer te delen en te gebruiken.

Het doel van dit artikel is om u te helpen bij het uitvoeren van de tweede optie.

## <a name="configuring-a-shared-leases-container"></a>Een gedeelde lease-container configureren

Als u de container gedeelde leases wilt configureren, is de enige extra configuratie die u nodig hebt om uw triggers te maken, het kenmerk toe te voegen `LeaseCollectionPrefix` [](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) als u C# of `leaseCollectionPrefix` [kenmerk](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) gebruikt als u Java script gebruikt. De waarde van het kenmerk moet een logische descriptor zijn van wat de betreffende trigger is.

Als u bijvoorbeeld drie triggers hebt: een die e-mail berichten verzendt, een die een aggregatie maakt om een gerealiseerde weer gave te maken, en een die de wijzigingen naar een andere opslag verzendt, kunt u voor latere analyses de `LeaseCollectionPrefix` e-mail berichten toewijzen aan de eerste, ' materialed ', en ' Analytics ' aan de derde.

Het belang rijk deel is dat alle drie **de triggers dezelfde container configuratie voor leases** (account, data base en container naam) kunnen gebruiken.

Een eenvoudige code voorbeelden die gebruikmaken `LeaseCollectionPrefix` van het kenmerk in C#, ziet er als volgt uit:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

En voor Java script kunt u de configuratie Toep assen op het `function.json` bestand, met het `leaseCollectionPrefix` kenmerk:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Altijd controleren op de aanvraag eenheden die zijn ingericht in uw gedeelde leases-container. Elke trigger waarmee deze wordt gedeeld, verhoogt het verbruik van gemiddelde door Voer, dus u moet mogelijk de ingerichte door Voer verhogen als u het aantal Azure Functions dat het gebruikt, verhoogt.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de volledige configuratie voor de [Azure functions trigger voor Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Controleer de uitgebreide [lijst met voor beelden](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) voor alle talen.
* Ga naar de Serverloze recepten met Azure Cosmos DB en Azure Functions [github-opslag plaats](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) voor meer voor beelden.
