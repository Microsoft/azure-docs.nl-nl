---
title: De Azure Cosmos DB Table-API en Azure Table Storage gebruiken met Ruby
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage of de Azure Cosmos DB Table-API.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 2d0c8433fff58854cb77a4e806058eae1937e71b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101116"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Azure Table Storage en de Azure Cosmos DB Table-API gebruiken met Ruby
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Dit artikel laat zien hoe u tabellen maakt, uw gegevens opslaat en CRUD-bewerkingen op de gegevens uitvoert. Kies de Azure Table-service of de Azure Cosmos DB Table-API. De voorbeelden in dit artikel zijn geschreven in Ruby en maken gebruik van de [Azure Storage Table-clientbibliotheek voor Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). De behandelde scenario’s zijn onder meer het maken van een tabel, het verwijderen van een tabel, het invoegen van entiteiten en het uitvoeren van query’s op entiteiten vanuit de tabel.

## <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Een Azure-opslagaccount maken**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB-account maken**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-azure-storage-or-azure-cosmos-db"></a>Toegang toevoegen aan Azure Storage of Azure Cosmos DB

U moet voor het gebruik van Azure Storage of Azure Cosmos DB het Ruby Azure-pakket downloaden en gebruiken. Dit bevat een set met handige bibliotheken die met de Table REST-services communiceren.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems gebruiken om het pakket te verkrijgen

1. Gebruik een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Typ **gem install azure-storage-table** in het opdrachtvenster om de gem en afhankelijkheden te installeren.

### <a name="import-the-package"></a>Het pakket importeren

Gebruik uw favoriete teksteditor en voeg het volgende toe aan het begin van het Ruby-bestand waar u Storage wilt gebruiken:

```ruby
require "azure/storage/table"
```

## <a name="add-your-connection-string"></a>Uw verbindingsreeks toevoegen

U kunt verbinding maken met het Azure Storage-account of het Azure Cosmos DB Table-API-account. Haal de verbindingsreeks op voor het soort account dat u gebruikt.

### <a name="add-an-azure-storage-connection"></a>Een Azure Storage-verbinding toevoegen

De Azure Storage-module leest de omgevingsvariabelen **AZURE_STORAGE_ACCOUNT** en **AZURE_STORAGE_ACCESS_KEY** voor de informatie die nodig is om verbinding te maken met uw Azure Storage-account. Als deze omgevingsvariabelen niet zijn ingesteld, moet u de accountgegevens opgeven voordat u **Azure::Storage::Table::TableService** gebruikt, met de volgende code:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

U verkrijgt deze waarden als volgt van een klassiek of Resource Manager-opslagaccount op Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar het opslagaccount dat u wilt gebruiken.
3. Klik in de blade Instellingen aan de rechterkant op **Toegangssleutels**.
4. In de blade Toegang die wordt weergegeven, ziet u toegangssleutel 1 en toegangssleutel 2. U kunt een van beide gebruiken.
5. Klik op het kopieerpictogram om de sleutel naar het klembord kopiëren.

### <a name="add-an-azure-cosmos-db-connection"></a>Een Azure Cosmos DB-verbinding toevoegen

Wanneer u verbinding wilt maken met Azure Cosmos DB, kopieert u de primaire verbindingsreeks vanuit de Azure Portal en maakt u een **Client**-object met de gekopieerde verbindingsreeks. U kunt het **Client**-object doorgeven bij het maken van een **TableService**-object:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Een tabel maken

Het object **Azure::Storage::Table::TableService** biedt u de mogelijkheid te werken met tabellen en entiteiten. U maakt een tabel met de **create_table()** -methode. In het volgende voorbeeld wordt een tabel gemaakt of de fout afgedrukt als hier sprake van is.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Wanneer u een entiteit wilt toevoegen, moet u eerst een hashobject maken dat uw entiteitseigenschappen definieert. Houd er rekening mee dat u voor elke entiteit een **PartitionKey** en **RowKey** moet opgeven. Dit zijn de unieke id's voor uw entiteiten en de waarden waarop sneller query's kunnen worden uitgevoerd dan op uw andere eigenschappen. Azure Storage maakt gebruik van **PartitionKey** om de entiteiten van de tabel automatisch over veel Storage-knooppunten te verdelen. Entiteiten met dezelfde **PartitionKey** worden opgeslagen op hetzelfde knooppunt. De **RowKey** is de unieke id van de entiteit binnen de partitie waar deze bij hoort.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Een entiteit bijwerken

Er zijn meerdere methoden beschikbaar voor het bijwerken van een bestaande entiteit:

* **update_entity():** werkt een bestaande entiteit bij door deze te vervangen.
* **merge_entity():** werkt een bestaande entiteit bij door nieuwe eigenschapswaarden in de bestaande entiteit samen te voegen.
* **insert_or_merge_entity():** werkt een bestaande entiteit bij door deze te vervangen. Als er geen entiteit bestaat, wordt er een nieuwe ingevoegd:
* **insert_or_replace_entity():** werkt een bestaande entiteit bij door nieuwe eigenschapswaarden in de bestaande entiteit samen te voegen. Als er geen entiteit bestaat, wordt er een nieuwe ingevoegd.

Het volgende voorbeeld laat zien hoe u een entiteit bijwerkt met **update_entity()** :

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Voor **update_entity()** en **merge_entity()** geldt dat als de entiteit die u wilt bijwerken niet bestaat, de updatebewerking mislukt. Dus als een entiteit wilt opslaan ongeacht of deze al bestaat, gebruikt u in plaats daarvan **insert_or_replace_entity()** of **insert_or_merge_entity()** .

## <a name="work-with-groups-of-entities"></a>Werken met groepen entiteiten

Soms is het zinvol om meerdere bewerkingen samen in een batch te verzenden om te zorgen dat ze atomisch worden verwerkt door de server. Om dat te realiseren, maakt u eerst een **Batch**-object en gebruikt u vervolgens de **execute_batch()** -methode op **TableService**. In het volgende voorbeeld ziet u hoe u twee entiteiten met RowKey 2 en 3 in een batch kunt verzenden. Dit werkt alleen voor entiteiten met dezelfde PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Een query uitvoeren voor een entiteit

Wanneer u een query wilt uitvoeren voor een entiteit in een tabel, gebruikt u de **get_entity()** -methode en geeft u de tabelnaam, **PartitionKey** en **RowKey** door.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Een query uitvoeren voor een aantal entiteiten

Wanneer u een query wilt uitvoeren voor een aantal entiteiten in een tabel, maakt u een query-hash-object en gebruikt u de **query_entities()** -methode. Het volgende voorbeeld ziet u hoe u alle entiteiten kunt ophalen met dezelfde **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Als de resultatenset te groot is om in één query om te retourneren, wordt een vervolgtoken geretourneerd dat u kunt gebruiken kunt voor het ophalen van de volgende pagina's.


## <a name="query-a-subset-of-entity-properties"></a>Een query uitvoeren op een subset van entiteitseigenschappen

Met een query naar een tabel kunnen slechts enkele eigenschappen van een entiteit worden opgehaald. Deze methode, 'projectie' genoemd, verbruikt minder bandbreedte en kan de queryprestaties verbeteren, vooral bij grote entiteiten. Gebruik de geselecteerde component en geef de namen van de eigenschappen door die u wilt overbrengen naar de client.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen

U verwijdert een entiteit met de methode **delete_entity()** . Geef de naam van de tabel door die de entiteit, de PartitionKey en de RowKey van de entiteit bevat.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Een tabel verwijderen

U verwijdert een tabel met de methode **delete_table()** en geeft hierbij de naam van de tabel door die u wilt verwijderen.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Volgende stappen

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
* [Ruby Developer Center](https://azure.microsoft.com/develop/ruby/)
* [Clientbibliotheek voor Microsoft Azure-opslagtabellen voor Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table)