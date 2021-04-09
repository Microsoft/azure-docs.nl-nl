---
title: Azure Cosmos-containers maken met een grote partitie sleutel
description: Meer informatie over het maken van een container in Azure Cosmos DB met een grote partitie sleutel met behulp van Azure Portal en verschillende Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339289"
---
# <a name="create-containers-with-large-partition-key"></a>Containers met een grote partitie sleutel maken
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB gebruikt een op hash gebaseerd partitie schema om gegevens Horizon taal te schalen. Alle Azure Cosmos-containers die zijn gemaakt vóór 3 2019 kunnen een hash-functie gebruiken waarmee hash wordt berekend op basis van de eerste 100 bytes van de partitie sleutel. Als er meerdere partitie sleutels zijn met dezelfde eerste 100 bytes, worden deze logische partities beschouwd als dezelfde logische partitie door de service. Dit kan leiden tot problemen als het quotum voor de partitie grootte onjuist is en dat er unieke indexen worden toegepast op de partitie sleutels. Grote partitie sleutels zijn geïntroduceerd om dit probleem op te lossen. Azure Cosmos DB ondersteunt nu grote partitie sleutels met waarden van Maxi maal 2 KB.

Grote partitie sleutels worden ondersteund met behulp van de functionaliteit van een verbeterde versie van de hash-functie, waarmee een unieke hash kan worden gegenereerd van grote partitie sleutels tot 2 KB. Deze hash-versie wordt ook aanbevolen voor scenario's met een hoogwaardige kardinaliteit van de partitie, ongeacht de grootte van de partitie sleutel. Een partitie sleutel kardinaliteit wordt gedefinieerd als het aantal unieke logische partities, bijvoorbeeld in de volg orde van ~ 30000 logische partities in een container. In dit artikel wordt beschreven hoe u een container met een grote partitie sleutel maakt met behulp van de Azure Portal en verschillende Sdk's.

## <a name="create-a-large-partition-key-azure-portal"></a>Een grote partitie sleutel maken (Azure Portal)

Als u een grote partitie sleutel wilt maken wanneer u een nieuwe container maakt met behulp van de Azure Portal, controleert u of de **sleutel mijn partitie groter is dan 100-bytes** . Schakel het selectie vakje uit als u geen grote partitie sleutels nodig hebt of als u toepassingen hebt die worden uitgevoerd op de Sdk's-versie die ouder is dan 1,18.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Grote partitie sleutels maken met behulp van Azure Portal":::

## <a name="create-a-large-partition-key-powershell"></a>Een grote partitie sleutel maken (Power shell)

Als u een container met ondersteuning voor grote partitie sleutels wilt maken, raadpleegt u

* [Een Azure Cosmos-container maken met een grote partitie sleutel grootte](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Een grote partitie sleutel maken (.NET SDK)

Als u een container met een grote partitie sleutel met behulp van de .NET-SDK wilt maken, geeft u de `PartitionKeyDefinitionVersion.V2` eigenschap op. In het volgende voor beeld ziet u hoe u de eigenschap Version opgeeft in het PartitionKeyDefinition-object en deze instelt op PartitionKeyDefinitionVersion. v2.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>Ondersteunde SDK-versies

De grote partitie sleutels worden ondersteund met de volgende minimum versies van Sdk's:

|Type SDK  | Minimale versie   |
|---------|---------|
|.Net     |    1,18     |
|Java-synchronisatie     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST-API | versie hoger dan met `2017-05-03` behulp van de `x-ms-version` aanvraag header.|
| Resource Manager-sjabloon | versie 2 met behulp van de `"version":2` eigenschap binnen het `partitionKey` object. |

Op dit moment kunt u geen containers met een grote partitie sleutel gebruiken in Power BI en Azure Logic Apps. U kunt containers gebruiken zonder een grote partitie sleutel van deze toepassingen.

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](./account-databases-containers-items.md)