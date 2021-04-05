---
title: Bestaande gegevens migreren naar een Table-API-account in Azure Cosmos DB
description: Informatie over het migreren of importeren van on-premises gegevens of gegevens in de cloud naar een Azure Table-API-account in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096073"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Gegevens migreren naar een Azure Cosmos DB Table-API-account
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In deze zelfstudie vindt u instructies voor het importeren van gegevens voor gebruik met de Azure Cosmos DB [Table-API](table-introduction.md). Als u gegevens hebt opgeslagen in Azure Table Storage, kunt u het gegevensmigratieprogramma of AzCopy gebruiken om de gegevens te importeren in Azure Cosmos DB Table-API. Als u gegevens hebt opgeslagen in een Azure Cosmos DB Table-API-account (preview), moet u het gegevensmigratieprogramma gebruiken om uw gegevens te migreren. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gegevens importeren met het gegevensmigratieprogramma
> * Gegevens importeren met AzCopy
> * Migreren vanuit Table-API (preview) naar Table-API 

## <a name="prerequisites"></a>Vereisten

* **Doorvoer vergroten:** de duur van de gegevensmigratie is afhankelijk van de hoeveelheid doorvoer die u voor een afzonderlijke container of een reeks containers instelt. Verhoog de doorvoer voor grotere gegevensmigraties. Nadat u de migratie hebt voltooid, verlaagt u de doorvoer om kosten te besparen. Zie Prestatieniveaus en prijscategorieën in Azure Cosmos DB voor meer informatie over het verhogen van de doorvoer in Azure Portal.

* **Azure Cosmos DB-resources maken:** voordat u gegevens gaat migreren, maakt u vooraf alle tabellen vanuit de Azure-portal. Als u migreert naar een Azure Cosmos DB-account dat doorvoer op databaseniveau heeft, moet u een partitiesleutel opgeven bij het maken van de Azure Cosmos DB-tabellen.

## <a name="data-migration-tool"></a>Gegevensmigratieprogramma

Het opdrachtregelprogramma voor Azure Cosmos DB-gegevensmigratie (dt.exe) kan worden gebruikt om uw bestaande Azure Table Storage-gegevens te importeren in een account voor een algemeen beschikbare Table-API of om gegevens te migreren van een Table-API-account (preview) naar een account voor een algemeen beschikbare Table-API. Andere bronnen worden momenteel niet ondersteund. Het gegevensmigratieprogramma dat is gebaseerd op een gebruikersinterface (dtui.exe) wordt momenteel niet ondersteund voor Table-API-accounts. 

Als u tabelgegevens wilt migreren, moet u de volgende taken uitvoeren:

1. Download het migratieprogramma op [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Voer `dt.exe` uit met de opdrachtregelargumenten voor uw scenario. `dt.exe` wordt uitgevoerd met een opdracht in de volgende indeling:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

De ondersteunde opties voor deze opdracht zijn:

* **/ErrorLog:** Optioneel. Naam van het CSV-bestand voor het omleiden van fouten in de gegevensoverdracht
* **/OverwriteErrorLog:** Optioneel. Foutenlogboekbestand overschrijven
* **/ProgressUpdateInterval:** Optioneel; standaardwaarde is 00:00:01. Tijdsinterval voor het vernieuwen van de voortgang van de gegevensoverdracht
* **/ErrorDetails:** Optioneel; standaardwaarde is Geen. Hiermee geeft u op dat gedetailleerde foutgegevens moeten worden weergegeven voor de volgende fouten: Geen, Kritiek, Alles
* **/EnableCosmosTableLog:** Optioneel. Het logboek naar een cosmos-tabel account sturen. Als deze instelling is ingesteld, wordt dit ook de standaardinstelling van de verbindingsreeks voor de doelaccount tenzij er ook /CosmosTableLogConnectionString is opgegeven. Dit is handig als er meerdere exemplaren van DT tegelijk worden uitgevoerd.
* **/CosmosTableLogConnectionString:** Optioneel. Verbindingsreeks om het logboek naar een extern cosmos-tabelaccount te sturen.

### <a name="command-line-source-settings"></a>Broninstellingen voor de opdrachtregel

Gebruik de volgende bronopties bij het definiëren van Azure Table Storage of Table-API preview als de bron van de migratie.

* **/s:AzureTable:** Gegevens lezen uit Azure Table Storage
* **/s.ConnectionString:** Verbindingsreeks voor het eindpunt van de tabel. Het kan worden opgehaald uit de Azure-portal
* **/s.LocationMode:** Optioneel; standaardwaarde is PrimaryOnly. Hiermee geeft u op welke locatiemodus moet worden gebruikt bij het maken van verbinding met Azure Table Storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.Table:** Naam van de Azure-tabel
* **/s.InternalFields:** Ingesteld op alle voor de tabelmigratie als RowKey en PartitionKey zijn vereist voor het importeren.
* **/s.Filter:** Optioneel. Filterreeks die moet worden toegepast
* **/s.Projection:** Optioneel. Lijst met te selecteren kolommen

Als u de verbindingsreeks voor de bron wilt ophalen bij het importeren uit Azure Table Storage, opent u Azure Portal, klikt u op **Storage-accounts** > **Account** > **Toegangssleutels** en kopieert u vervolgens de **verbindingsreeks** met de knop Kopiëren.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Schermopname van de opties Opslagaccounts > Account > Toegangssleutels met de knop Kopiëren gemarkeerd.":::

Als u de verbindingsreeks voor de bron wilt ophalen bij het importeren uit een Azure Cosmos DB Table-API-account (preview), opent u Azure Portal, klikt u op **Azure Cosmos DB** > **Account** > **Verbindingsreeks** en kopieert u vervolgens de **verbindingsreeks** met de knop Kopiëren.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Schermopname van HBase-bronopties":::

[Voorbeeld van Azure Table Storage-opdracht](#azure-table-storage)

[Voorbeeld van opdracht voor Azure Cosmos DB Table-API (preview)](#table-api-preview)

### <a name="command-line-target-settings"></a>Doelinstellingen voor opdrachtregel

Gebruik de volgende doelopties bij het definiëren van de Azure Cosmos DB Table-API als het doel van de migratie.

* **/t:TableAPIBulk:** Hiermee worden gegevens geüpload naar Azure CosmosDB-tabel in batches
* **/t.ConnectionString:** Verbindingsreeks voor het eindpunt van de tabel
* **/t.TableName:** Geeft de naam van de tabel waarnaar moet worden geschreven
* **/t.Overwrite:** Optioneel; standaardwaarde is false. Hiermee wordt aangegeven of bestaande waarden moeten worden overschreven
* **/t.MaxInputBufferSize:** Optioneel; standaardwaarde is 1GB. Geschatte invoerbytes om te bufferen vóór het leegmaken van gegevens naar sink
* **/t.Throughput:** Optioneel, standaardinstellingen van service als niet opgegeven. Specificeert de doorvoer voor het configureren van de tabel
* **/t.MaxBatchSize:** Optioneel; standaardwaarde is 2MB. De batchgrootte in bytes opgeven

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Voorbeeldopdracht: bron is Azure-tabelopslag

Hier ziet u een voorbeeld van een opdrachtregel voor het importeren vanuit Azure Table Storage in Table-API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Voorbeeldopdracht: bron is Azure Cosmos DB Table-API (preview)

Hier ziet u een voorbeeld van een opdrachtregel voor het importeren vanuit Table-API preview in een algemeen beschikbare Table-API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Gegevens migreren met AzCopy

U kunt ook met het AzCopy-opdrachtregelprogramma gegevens vanuit Azure Table Storage migreren naar de Azure Cosmos DB Table-API. Als u AzCopy wilt gebruiken, exporteert u eerst de gegevens zoals wordt beschreven in [Gegevens exporteren vanuit Azure Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) en importeert u de gegevens vervolgens in Azure Cosmos DB zoals wordt beschreven in [Azure Cosmos DB Table-API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Bij het importeren in Azure Cosmos DB kunt u de volgende opdracht als voorbeeld nemen. Houd er rekening mee dat de waarde voor /Dest cosmosdb is en niet core.

Voorbeeld van opdracht voor importeren:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migreren vanuit Table-API (preview) naar Table-API

> [!WARNING]
> Als u direct voordeel wilt hebben van de algemeen beschikbare tabellen, migreert u uw bestaande previewtabellen zoals in deze sectie is beschreven. Anders worden de tabellen de komende weken automatisch gemigreerd voor bestaande previewklanten. Houd er echter rekening mee dat er voor automatisch gemigreerde previewtabellen bepaalde beperkingen gelden die niet van toepassing zijn op onlangs gemaakte tabellen.

De Table-API is nu algemeen beschikbaar. Er zijn verschillen tussen de previewversie en de algemeen beschikbare versie voor tabellen in de code die wordt uitgevoerd in de cloud en in de code die wordt uitgevoerd op de client. Daarom wordt het niet aangeraden om een preview SDK-client te combineren met een account voor een algemeen beschikbare Table-API. Als klanten van de Table-API preview de bestaande tabellen willen blijven gebruiken in een productieomgeving, moeten ze de tabellen vanuit de previewomgeving migreren naar de omgeving met een algemeen beschikbare Table-API of wachten totdat de tabellen automatisch worden gemigreerd. Als u wacht op de automatische migratie, krijgt u een melding over de beperkingen die voor de gemigreerde tabellen gelden. Na de migratie kunt u voor uw bestaande account nieuwe tabellen zonder beperkingen maken (alleen gemigreerde tabellen hebben beperkingen).

U migreert als volgt van Table-API (preview) naar de algemeen beschikbare Table-API:

1. Maak een nieuw Azure Cosmos DB-account en stel hiervoor het API-type in op Azure Table, zoals wordt beschreven in [Een databaseaccount maken](create-table-dotnet.md#create-a-database-account).

2. Zorg ervoor dat de clients gebruikmaken van een algemeen beschikbare release van de [Table-API-SDK's](table-sdk-dotnet.md).

3. Migreer de clientgegevens van de previewtabellen naar de algemeen beschikbare tabellen met behulp van het gegevensmigratieprogramma. Instructies voor het gebruik van het gegevensmigratieprogramma voor dit doeleinde worden beschreven in [Gegevensmigratieprogramma](#data-migration-tool). 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Gegevens importeren met het gegevensmigratieprogramma
> * Gegevens importeren met AzCopy
> * Migreren vanuit Table-API (preview) naar Table-API

U kunt nu verdergaan met de volgende zelfstudie om te leren hoe u query's uitvoert op gegevens met de Azure Cosmos DB Table-API. 

> [!div class="nextstepaction"]
>[Hoe kan ik query’s uitvoeren op gegevens?](../cosmos-db/tutorial-query-table.md)