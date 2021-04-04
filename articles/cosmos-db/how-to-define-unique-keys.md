---
title: Unieke sleutels voor een Azure Cosmos-container definiëren
description: Meer informatie over het definiëren van unieke sleutels voor een Azure Cosmos-container met behulp van Azure Portal, Power shell, .NET, Java en diverse andere Sdk's.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336619"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Unieke sleutels voor een Azure Cosmos-container definiëren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden de verschillende manieren beschreven waarop u [unieke sleutels](unique-keys.md) kunt definiëren bij het maken van een Azure Cosmos-container. Het is momenteel mogelijk deze bewerking uit te voeren met behulp van de Azure Portal of via een van de Sdk's.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account) of selecteer een bestaand abonnement.

1. Open het deel venster **Data Explorer** en selecteer de container waaraan u wilt werken.

1. Klik op **nieuwe container**.

1. Klik in het dialoog venster **container toevoegen** op **+ unieke sleutel toevoegen** om een unieke sleutel vermelding toe te voegen.

1. Voer een of meer paden in voor de beperking van de unieke sleutel

1. Voeg, indien nodig, meer unieke sleutel vermeldingen toe door te klikken op **+ unieke sleutel toevoegen**

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Scherm opname van de vermelding van een unieke-sleutel beperking op Azure Portal":::

## <a name="use-powershell"></a>PowerShell gebruiken

Als u een container met unieke sleutels wilt maken, gaat u naar [een Azure Cosmos-container maken met een unieke sleutel en TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Wanneer u een nieuwe container maakt met behulp van de [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), `UniqueKeyPolicy` kan een object worden gebruikt voor het definiëren van unieke sleutel beperkingen.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Wanneer u een nieuwe container maakt met behulp van de [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), gebruikt u de Fluent API van de SDK voor het declareren van unieke sleutels op een beknopte en lees bare manier.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Wanneer u een nieuwe container maakt met behulp van de [Java-SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), `UniqueKeyPolicy` kan een object worden gebruikt voor het definiëren van unieke sleutel beperkingen.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>De Node.js SDK gebruiken

Wanneer u een nieuwe container maakt met behulp van de [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos), `UniqueKeyPolicy` kan een object worden gebruikt voor het definiëren van unieke sleutel beperkingen.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>De Python-SDK gebruiken

Wanneer u een nieuwe container maakt met behulp van de [python-SDK](https://pypi.org/project/azure-cosmos/), kunnen er unieke sleutel beperkingen worden opgegeven als onderdeel van de woorden lijst die als para meter is door gegeven.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [partitioneren](partitioning-overview.md)
- Verkennen [hoe indexeren werkt](index-overview.md)
