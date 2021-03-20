---
title: Data Factory-.NET API-wijzigings logboek
description: Beschrijft de belang rijke wijzigingen, functie toevoegingen, fout oplossingen, enzovoort, in een specifieke versie van .NET API voor de Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 3f7c80f5818c1353f438a164bf52c777056ad6d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377479"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API-wijzigings logboek
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

Dit artikel bevat informatie over wijzigingen in Azure Data Factory SDK in een specifieke versie. U kunt [hier](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) het meest recente NuGet-pakket voor Azure Data Factory vinden

## <a name="version-4110"></a>Versie 4.11.0
Toevoegingen van functies:

* De volgende gekoppelde service typen zijn toegevoegd:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* De volgende typen gegevensset zijn toegevoegd:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* De volgende Kopieer bron typen zijn toegevoegd:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Versie 4.10.0
* De volgende optionele eigenschappen zijn toegevoegd aan TextFormat:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* De volgende gekoppelde service typen zijn toegevoegd:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* De volgende typen gegevensset zijn toegevoegd:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* De volgende Kopieer bron typen zijn toegevoegd:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Eigenschap [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) toevoegen aan AzureMLBatchExecutionActivity
  * Inschakelen van meerdere web service-ingangen naar een Azure Machine Learning experiment

## <a name="version-491"></a>Versie 4.9.1
### <a name="bug-fix"></a>Bug oplossen
* WebApi-verificatie op basis van [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)op te afschaffing.

## <a name="version-490"></a>Versie 4.9.0
### <a name="feature-additions"></a>Functie toevoegingen
* Voeg de eigenschappen [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) en [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) toe aan CopyActivity. Zie [gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over de functie.

### <a name="bug-fix"></a>Bug oplossen
* Een overbelasting van de methode [ActivityWindowOperationExtensions. list](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) introduceren, die een [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) -exemplaar gebruikt.
* Mark [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) en [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) als optioneel in CopySink.

## <a name="version-480"></a>Versie 4.8.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende optionele eigenschappen zijn toegevoegd aan het type Kopieer activiteit om het afstemmen van de Kopieer prestaties mogelijk te maken:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Versie 4.7.0
### <a name="feature-additions"></a>Functie toevoegingen
* Het nieuwe StorageFormat type [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) -type is toegevoegd om bestanden te kopiëren in de indeling van de geoptimaliseerde rij in kolommen (Orc).
* Voeg de eigenschappen [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) en PolyBaseSettings toe aan SqlDWSink.
  * Hiermee schakelt u het gebruik van poly base in voor het kopiëren van gegevens naar Azure Synapse Analytics.

## <a name="version-461"></a>Versie 4.6.1
### <a name="bug-fixes"></a>Opgeloste fouten
* Hiermee wordt HTTP-aanvraag voor Windows-aanbiedings activiteit opgelost.
  * Hiermee verwijdert u de naam van de resource groep en de naam van het data factory uit de aanvraag lading.

## <a name="version-460"></a>Versie 4.6.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende eigenschappen zijn toegevoegd aan [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties):
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Gegevenssets](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* De volgende eigenschappen zijn toegevoegd aan [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo):
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Het nieuwe [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) type [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) -type is toegevoegd voor het definiëren van gegevens sets met een JSON-indeling.

## <a name="version-450"></a>Versie 4.5.0
### <a name="feature-additions"></a>Functie toevoegingen
* [Lijst bewerkingen toegevoegd voor het activiteiten venster](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Er zijn methoden toegevoegd om activiteiten Vensters op te halen met filters op basis van de entiteits typen (dat wil zeggen, gegevens fabrieken, data sets, pijp lijnen en activiteiten).
* De volgende gekoppelde service typen zijn toegevoegd:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* De volgende typen gegevensset zijn toegevoegd:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* De volgende Kopieer bron typen zijn toegevoegd:     
  * [Websource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Versie 4.4.0
### <a name="feature-additions"></a>Functie toevoegingen
* Het volgende gekoppelde service type is toegevoegd als gegevens bronnen en sinks voor kopieer activiteiten:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Zie [Azure Storage gekoppelde SAS-service](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) voor conceptuele informatie en voor beelden.

## <a name="version-430"></a>Versie 4.3.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende gekoppelde service typen recent zijn toegevoegd als gegevens bronnen voor kopieer activiteiten:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Zie [gegevens verplaatsen van HDFS met Data Factory](data-factory-hdfs-connector.md) voor conceptuele informatie en voor beelden.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Zie [gegevens verplaatsen van ODBC-gegevens archieven met behulp van Azure Data Factory](data-factory-odbc-connector.md) voor conceptuele informatie en voor beelden.

## <a name="version-420"></a>Versie 4.2.0
### <a name="feature-additions"></a>Functie toevoegingen
* Het volgende nieuwe type activiteit is toegevoegd: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Zie [Azure ml-modellen bijwerken met de activiteit resource bijwerken](data-factory-azure-ml-batch-execution-activity.md)voor meer informatie over de activiteit.
* Er is een nieuwe optionele eigenschap [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) toegevoegd aan de [klasse AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice).
* De eigenschappen [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) en [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) zijn toegevoegd aan de klasse [datafactorymanagementclient te maken](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) .
* Configuratie van de time-outs voor client aanroepen naar de Data Factory-Service toestaan.

## <a name="version-410"></a>Versie 4.1.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende gekoppelde service typen zijn toegevoegd:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* De volgende typen activiteiten zijn toegevoegd:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* De volgende typen gegevensset zijn toegevoegd:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* De volgende bron-en Sink-typen voor kopieer activiteiten zijn toegevoegd:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Versie 4.0.1
### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
De naam van de volgende klassen is gewijzigd. De nieuwe namen bevinden zich in de oorspronkelijke namen van klassen vóór de release van 4.0.0.

| Naam in 4.0.0 | Naam in 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Versie 4.0.0
### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
* De naam van de volgende klassen/interfaces is gewijzigd.

| Oude naam | Nieuwe naam |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabel |[Gegevensset](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* De **lijst** methoden retour neren nu pagina resultaten. Als het antwoord een niet-lege eigenschap **Nextlink** bevat, moet de client toepassing de volgende pagina blijven ophalen totdat alle pagina's zijn geretourneerd.  Hier volgt een voorbeeld:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Lijst** PIJPLIJN-API retourneert alleen de samen vatting van een pijp lijn in plaats van volledige details. Bijvoorbeeld: activiteiten in een pijp lijn-samen vatting bevatten alleen naam en type.

### <a name="feature-additions"></a>Functie toevoegingen
* De klasse [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) ondersteunt twee nieuwe eigenschappen, **SliceIdentifierColumnName** en **SqlWriterCleanupScript**, ter ondersteuning van idempotent-kopie naar Azure Azure Synapse Analytics. Zie het artikel over [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) voor meer informatie over deze eigenschappen.
* We ondersteunen nu het uitvoeren van een opgeslagen procedure voor Azure SQL Database en Azure Synapse Analytics-bronnen als onderdeel van de Kopieer activiteit. De klassen [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) en [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) hebben de volgende eigenschappen: **SqlReaderStoredProcedureName** en **StoredProcedureParameters**. Raadpleeg de [Azure SQL database](data-factory-azure-sql-connector.md#sqlsource) -en [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) -artikelen over Azure.com voor meer informatie over deze eigenschappen.