---
title: Niet-gepartitioneerde Azure Cosmos-containers migreren naar gepartitioneerde containers
description: Meer informatie over het migreren van alle bestaande niet-gepartitioneerde containers in gepartitioneerde containers.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: edb6114406922d55c439ae7426a2be933bba4aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334084"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Niet-gepartitioneerde containers migreren naar gepartitioneerde containers
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ondersteunt het maken van containers zonder een partitie sleutel. Op dit moment kunt u niet-gepartitioneerde containers maken met behulp van Azure CLI en Azure Cosmos DB Sdk's (.net, Java, NodeJs) die een versie hebben die kleiner is dan of gelijk is aan 2. x. U kunt geen niet-gepartitioneerde containers maken met behulp van de Azure Portal. Dergelijke niet-gepartitioneerde containers zijn echter niet elastisch en hebben een vaste opslag capaciteit van 20 GB en doorvoer limiet van 10.000 RU/s.

De niet-gepartitioneerde containers zijn verouderd en u moet uw bestaande niet-gepartitioneerde containers migreren naar gepartitioneerde containers om de opslag en doorvoer te schalen. Azure Cosmos DB biedt een door het systeem gedefinieerd mechanisme voor het migreren van niet-gepartitioneerde containers naar gepartitioneerde containers. In dit document wordt uitgelegd hoe alle bestaande niet-gepartitioneerde containers automatisch worden gemigreerd naar gepartitioneerde containers. U kunt de functie voor automatisch migreren alleen gebruiken als u de V3-versie van SDK's in alle talen gebruikt.

> [!NOTE]
> Op dit moment kunt u de MongoDB-en Gremlin-API-accounts niet Azure Cosmos DB migreren met behulp van de stappen die in dit document worden beschreven.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Container migreren met de door het systeem gedefinieerde partitie sleutel

Ter ondersteuning van de migratie bevat Azure Cosmos DB een door het systeem gedefinieerde partitie sleutel met `/_partitionkey` de naam op alle containers die geen partitie sleutel hebben. U kunt de partitie sleutel definitie niet wijzigen nadat de containers zijn gemigreerd. De definitie van een container die naar een gepartitioneerde container wordt gemigreerd, ziet er bijvoorbeeld als volgt uit:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Nadat de container is gemigreerd, kunt u documenten maken door de `_partitionKey` eigenschap samen met de andere eigenschappen van het document in te vullen. De `_partitionKey` eigenschap vertegenwoordigt de partitie sleutel van uw documenten.

Het kiezen van de juiste partitie sleutel is belang rijk om de ingerichte door Voer optimaal te benutten. Zie [het artikel een partitie sleutel kiezen](partitioning-overview.md) voor meer informatie.

> [!NOTE]
> U kunt alleen gebruikmaken van de door het systeem gedefinieerde partitie sleutel als u de nieuwste/V3 versie van Sdk's in alle talen gebruikt.

In het volgende voor beeld ziet u een voorbeeld code voor het maken van een document met de door het systeem gedefinieerde partitie sleutel en het lezen van het document:

**JSON-weer gave van het document**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Zie de GitHub-opslag plaats voor [.net][1] -voor beelden voor het volledige voor beeld.
                      
## <a name="migrate-the-documents"></a>De documenten migreren

Hoewel de container definitie wordt uitgebreid met een partitie sleutel eigenschap, worden de documenten in de container niet automatisch gemigreerd. Dit betekent dat het pad naar de eigenschap van de systeem partitie sleutel `/_partitionKey` niet automatisch wordt toegevoegd aan de bestaande documenten. U moet de bestaande documenten opnieuw partitioneren door de documenten te lezen die zijn gemaakt zonder partitie sleutel en ze terug te schrijven naar `_partitionKey` de eigenschap in de documenten.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Toegang tot documenten die geen partitie sleutel hebben

Toepassingen hebben toegang tot de bestaande documenten die geen partitie sleutel hebben met de speciale systeem eigenschap ' PartitionKey. none '. Dit is de waarde van de niet-gemigreerde documenten. U kunt deze eigenschap in alle ruwe en query bewerkingen gebruiken. In het volgende voor beeld ziet u een voor beeld van het lezen van één document uit de NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Zie de GitHub-opslag plaats voor .net-voor [beelden][1] voor het volledige voor beeld voor het opnieuw partitioneren van de documenten. 

## <a name="compatibility-with-sdks"></a>Compatibiliteit met Sdk's

Oudere versie van Azure Cosmos DB Sdk's zoals v2. x. x en v1. x. x biedt geen ondersteuning voor de door het systeem gedefinieerde partitie sleutel eigenschap. Als u de container definitie van een oudere SDK leest, bevat deze dus geen partitie sleutel definitie en deze containers gedragen zich precies hetzelfde als voorheen. Toepassingen die zijn gebouwd met de oudere versie van Sdk's, blijven werken met niet-gepartitioneerde zonder wijzigingen. 

Als een gemigreerde container wordt gebruikt door de nieuwste/V3-versie van de SDK en u begint met het vullen van de door het systeem gedefinieerde partitie sleutel in de nieuwe documenten, hebt u geen toegang tot (lezen, bijwerken, verwijderen en opvragen van) documenten van de oudere Sdk's meer.

## <a name="known-issues"></a>Bekende problemen

**Voor het tellen van items die zijn ingevoegd zonder een partitie sleutel met behulp van de V3 SDK is mogelijk een hoger doorvoer verbruik vereist**

Als u een query uitvoert vanuit de V3 SDK voor de items die worden ingevoegd met behulp van v2 SDK of de items die zijn ingevoegd met behulp van de V3 SDK met de `PartitionKey.None` para meter, kan de aantal query's meer ru/s verbruiken als de `PartitionKey.None` para meter wordt opgegeven in de FeedOptions. We raden u aan de `PartitionKey.None` para meter niet op te geven als er geen andere items zijn ingevoegd met een partitie sleutel.

Als er nieuwe items worden ingevoegd met verschillende waarden voor de partitie sleutel, telt het uitvoeren van een query op dergelijke items door het door geven van de juiste sleutel in `FeedOptions` zullen geen problemen ondervinden. Als u na het invoegen van nieuwe documenten met de partitie sleutel een query wilt uitvoeren op alleen het aantal documenten zonder de partitie sleutel waarde, kan deze query meer dan RU/s hebben die vergelijkbaar is met de reguliere gepartitioneerde verzamelingen.

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](./account-databases-containers-items.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration