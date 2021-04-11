---
title: Bestaande gegevens migreren naar een Table-API-account in Azure Cosmos DB
description: Meer informatie over het migreren of importeren van on-premises of Cloud gegevens naar een Azure Table-API-account in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443335"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Uw gegevens migreren naar een Azure Cosmos DB Table-API-account
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In deze zelfstudie vindt u instructies voor het importeren van gegevens voor gebruik met de Azure Cosmos DB [Table-API](table-introduction.md). Als u gegevens hebt opgeslagen in azure Table Storage, kunt u het hulp programma voor gegevens migratie of AzCopy gebruiken om uw gegevens te importeren naar de Azure Cosmos DB Table-API. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gegevens importeren met het hulp programma voor gegevens migratie
> * Gegevens importeren met AzCopy

## <a name="prerequisites"></a>Vereisten

* **Doorvoer vergroten:** de duur van de gegevensmigratie is afhankelijk van de hoeveelheid doorvoer die u voor een afzonderlijke container of een reeks containers instelt. Verhoog de doorvoer voor grotere gegevensmigraties. Nadat u de migratie hebt voltooid, verlaagt u de doorvoer om kosten te besparen.

* **Azure Cosmos DB resources maken:** Voordat u begint met het migreren van de gegevens, maakt u alle tabellen van de Azure Portal. Als u migreert naar een Azure Cosmos DB-account met door Voer op database niveau, moet u ervoor zorgen dat u een partitie sleutel opgeeft wanneer u de Azure Cosmos DB tabellen maakt.

## <a name="data-migration-tool"></a>Hulp programma voor gegevens migratie

U kunt het hulp programma voor gegevens migratie van de opdracht regel (dt.exe) in Azure Cosmos DB gebruiken om uw bestaande Azure Table Storage-gegevens te importeren in een Table-API-account. 

Tabel gegevens migreren:

1. Download het migratieprogramma op [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Voer uit `dt.exe` met behulp van de opdracht regel argumenten voor uw scenario. `dt.exe` wordt uitgevoerd met een opdracht in de volgende indeling:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

De ondersteunde opties voor deze opdracht zijn:

* **/ErrorLog:** Optioneel. De naam van het CSV-bestand voor het omleiden van fouten in de gegevens overdracht.
* **/OverwriteErrorLog:** Optioneel. Overschrijf het fouten logboek bestand.
* **/ProgressUpdateInterval:** Optioneel, standaard is `00:00:01` . Het tijds interval voor het vernieuwen van de voortgang van de gegevens overdracht op het scherm.
* **/ErrorDetails:** Optioneel, standaard is `None` . Hiermee geeft u op dat gedetailleerde fout informatie moet worden weer gegeven voor de volgende fouten: `None` , `Critical` of `All` .
* **/EnableCosmosTableLog:** Optioneel. Het logboek naar een Azure Cosmos DB Table-account door sturen. Als deze instelling is ingesteld, wordt de standaard instelling van het doel account connection string tenzij `/CosmosTableLogConnectionString` er ook is opgegeven. Dit is handig als er meerdere exemplaren van het hulp programma tegelijkertijd worden uitgevoerd.
* **/CosmosTableLogConnectionString:** Optioneel. Het connection string om het logboek naar een extern Azure Cosmos DB tabel account te leiden.

### <a name="command-line-source-settings"></a>Broninstellingen voor de opdrachtregel

Gebruik de volgende bron opties wanneer u Azure Table Storage definieert als de bron van de migratie.

* **/s: AzureTable:** Hiermee worden gegevens uit Table Storage gelezen.
* **/s.ConnectionString:** Verbindingsreeks voor het eindpunt van de tabel. U kunt dit ophalen uit de Azure Portal.
* **/s.LocationMode:** Optioneel, standaard is `PrimaryOnly` . Hiermee geeft u op welke locatie modus moet worden gebruikt om verbinding te maken met Table Storage: `PrimaryOnly` ,, `PrimaryThenSecondary` `SecondaryOnly` , `SecondaryThenPrimary` .
* **/s.table:** De naam van de Azure-tabel.
* **/s.InternalFields:** Ingesteld op `All` voor tabel migratie omdat `RowKey` en `PartitionKey` is vereist voor het importeren.
* **/s.Filter:** Optioneel. Filter teken reeks die moet worden toegepast.
* **/s.Projection:** Optioneel. Lijst met kolommen die moeten worden geselecteerd,

Open de Azure Portal om de bron connection string op te halen bij het importeren vanuit Table Storage. Selecteer **opslag accounts**  >  **account**  >  **toegangs sleutels** en kopieer de **verbindings reeks**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Scherm opname van opslag accounts > account > opties voor toegangs sleutels, en markeert het Kopieer pictogram.":::

### <a name="command-line-target-settings"></a>Doelinstellingen voor opdrachtregel

Gebruik de volgende doel opties wanneer u de Azure Cosmos DB Table-API als doel van de migratie definieert.

* **/t: TableAPIBulk:** Hiermee worden gegevens geüpload naar de Azure Cosmos DB-Table-API in batches.
* **/t.ConnectionString:** De connection string voor het eind punt van de tabel.
* **/t.TableName:** Hiermee geeft u de naam van de tabel waarnaar moet worden geschreven.
* **/t.overwrite:** Optioneel, standaard is `false` . Hiermee wordt aangegeven of bestaande waarden moeten worden overschreven.
* **/t.MaxInputBufferSize:** Optioneel, standaard is `1GB` . Geschatte geschatte invoer bytes voor buffer vóór het leegmaken van gegevens naar sink.
* **/t.Throughput:** Optioneel, standaardinstellingen van service als niet opgegeven. Specificeert de door Voer voor het configureren van de tabel.
* **/t.MaxBatchSize:** Optioneel, standaard is `2MB` . Geef de Batch grootte in bytes op.

### <a name="sample-command-source-is-table-storage"></a>Voorbeeld opdracht: bron is Table Storage

Hier volgt een voor beeld van een opdracht regel waarin wordt getoond hoe u kunt importeren uit Table Storage naar de Table-API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Gegevens migreren met AzCopy

U kunt ook het opdracht regel hulpprogramma AzCopy gebruiken om gegevens te migreren van Table Storage naar de Azure Cosmos DB Table-API. Als u AzCopy wilt gebruiken, exporteert u eerst uw gegevens zoals beschreven in [gegevens exporteren uit Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Vervolgens importeert u de gegevens naar Azure Cosmos DB, zoals beschreven in [Azure Cosmos DB Table-API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Raadpleeg het volgende voor beeld wanneer u importeert in Azure Cosmos DB. Houd er rekening mee dat de `/Dest` waarde wordt gebruikt `cosmosdb` , niet `core` .

Voorbeeld van opdracht voor importeren:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het opvragen van gegevens met behulp van de Azure Cosmos DB Table-API. 

> [!div class="nextstepaction"]
>[Hoe kan ik query’s uitvoeren op gegevens?](../cosmos-db/tutorial-query-table.md)




