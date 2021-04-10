---
title: SQL Server opgeslagen procedure-activiteit
description: Meer informatie over hoe u de SQL Server opgeslagen procedure activiteit kunt gebruiken om een opgeslagen procedure in een Azure SQL Database of Azure Synapse-analyse vanuit een Data Factory-pijp lijn aan te roepen.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 05717352936bed888e108277d0163e43bc5a37af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368758"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server opgeslagen procedure-activiteit
> [!div class="op_single_selector" title1="Transformatie activiteiten"]
> * [Hive-activiteit](data-factory-hive-activity.md)
> * [Pig-activiteit](data-factory-pig-activity.md)
> * [MapReduce-activiteit](data-factory-map-reduce.md)
> * [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
> * [Spark-activiteit](data-factory-spark.md)
> * [Azure Machine Learning Studio-batchuitvoeringsactiviteit (klassiek)](data-factory-azure-ml-batch-execution-activity.md)
> * [Resourceactiviteit voor Azure Machine Learning Studio (klassiek)](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [Aangepaste .NET-activiteit](data-factory-use-custom-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van opgeslagen procedure activiteit in Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Overzicht
U gebruikt gegevens transformatie activiteiten in een Data Factory- [pijp lijn](data-factory-create-pipelines.md) om onbewerkte gegevens te transformeren en te verwerken in voor spellingen en inzichten. De opgeslagen procedure activiteit is een van de transformatie activiteiten die Data Factory ondersteunt. In dit artikel wordt een overzicht gegeven van het artikel voor [gegevens transformatie-activiteiten](data-factory-data-transformation-activities.md) , dat een General overview bevat van gegevens transformatie en de ondersteunde transformatie activiteiten in Data Factory.

U kunt de opgeslagen procedure activiteit gebruiken voor het aanroepen van een opgeslagen procedure in een van de volgende gegevens archieven in uw onderneming of op een virtuele machine van Azure (VM):

- Azure SQL Database
- Azure Synapse Analytics
- SQL Server-Data Base. Als u SQL Server gebruikt, installeert u Data Management Gateway op dezelfde computer die als host fungeert voor de data base of op een afzonderlijke computer die toegang heeft tot de data base. Data Management Gateway is een onderdeel dat gegevens bronnen on-premises/op Azure VM met Cloud Services op een veilige en beheerde manier verbindt. Zie [Data Management Gateway](data-factory-data-management-gateway.md) artikel voor meer informatie.

> [!IMPORTANT]
> Bij het kopiëren van gegevens naar Azure SQL Database of SQL Server, kunt u de **SqlSink** in Kopieer activiteit configureren om een opgeslagen procedure aan te roepen met behulp van de eigenschap **sqlWriterStoredProcedureName** . Zie voor meer informatie [opgeslagen procedure aanroepen vanuit Kopieer activiteit](data-factory-invoke-stored-procedure-from-copy-activity.md). Zie voor meer informatie over de eigenschap de volgende connector artikelen: [Azure SQL database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Het aanroepen van een opgeslagen procedure bij het kopiëren van gegevens naar Azure Synapse Analytics met behulp van een Kopieer activiteit wordt niet ondersteund. Maar u kunt de opgeslagen procedure-activiteit gebruiken om een opgeslagen procedure in azure Synapse Analytics aan te roepen.
>
> Bij het kopiëren van gegevens uit Azure SQL Database of SQL Server of Azure Synapse Analytics kunt u **SqlSource** configureren in de Kopieer activiteit om een opgeslagen procedure aan te roepen voor het lezen van gegevens uit de bron database met behulp van de eigenschap **sqlReaderStoredProcedureName** . Zie voor meer informatie de volgende connector artikelen: [Azure SQL database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

In de volgende procedure wordt gebruikgemaakt van de opgeslagen procedure-activiteit in een pijp lijn om een opgeslagen procedure in Azure SQL Database aan te roepen.

## <a name="walkthrough"></a>Walkthrough
### <a name="sample-table-and-stored-procedure"></a>Voorbeeld tabel en opgeslagen procedure
1. Maak de volgende **tabel** in uw Azure SQL database met behulp van SQL Server Management Studio of een ander hulp programma waarmee u vertrouwd bent. De kolom/tijdstempel. is de datum en tijd waarop de bijbehorende ID wordt gegenereerd.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Id is de unieke geïdentificeerde en de kolom/tijdstempel. is de datum en tijd waarop de bijbehorende ID wordt gegenereerd.
    
    ![Voorbeeldgegevens](./media/data-factory-stored-proc-activity/sample-data.png)

    In dit voor beeld bevindt de opgeslagen procedure zich in een Azure SQL Database. Als de opgeslagen procedure zich in azure Synapse Analytics en SQL Server Data Base bevindt, is de benadering vergelijkbaar. Voor een SQL Server-Data Base moet u een [Data Management Gateway](data-factory-data-management-gateway.md)installeren.
2. Maak de volgende **opgeslagen procedure** waarmee gegevens worden ingevoegd in de **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Naam** en **behuizing** van de para meter (datetime in dit voor beeld) moet overeenkomen met de para meter die is opgegeven in de JSON van de pijp lijn/activiteit. Zorg ervoor dat in de opgeslagen procedure definitie **\@** wordt gebruikt als voor voegsel voor de para meter.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **Nieuw** in het menu links, klik op **Intelligence en analytische** gegevens en klik op **Data Factory**.

    ![Nieuw data factory 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Voer op de Blade **nieuw Data Factory** **SProcDF** in als naam. Azure Data Factory namen zijn **wereld wijd uniek**. U moet de naam van de data factory met uw naam als voor voegsel instellen, zodat de fabriek kan worden gemaakt.

   ![Nieuw data factory 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecteer uw **Azure-abonnement**.
5. Voer een van de volgende stappen uit voor de **resource groep**:
   1. Klik op **Nieuw maken** en voer een naam in voor de resource groep.
   2. Klik op **bestaande gebruiken** en selecteer een bestaande resource groep.
6. Selecteer de **locatie** voor de gegevensfactory.
7. Selecteer **vastmaken aan dash board** zodat u de Data Factory in het dash board kunt zien de volgende keer dat u zich aanmeldt.
8. Klik op de blade **Nieuwe gegevensfactory** op **Maken**.
9. U ziet dat de data factory wordt gemaakt in het **dash board** van de Azure Portal. Nadat de gegevensfactory is gemaakt, ziet u de gegevensfactorypagina. Hierop wordt de inhoud van de gegevensfactory weergegeven.

   ![Start pagina van Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Een gekoppelde Azure SQL-service maken
Nadat u de data factory hebt gemaakt, maakt u een gekoppelde Azure SQL-service waarmee u uw data base in Azure SQL Database kunt koppelen. Deze bevat de tabel sampletable en usp_sample opgeslagen procedure, naar uw data factory.

1. Klik op de Blade **Data Factory** op **ontwerpen en implementeren** voor **SProcDF** om de Data Factory editor te starten.
2. Klik op **Nieuw gegevens archief** op de opdracht balk en kies **Azure SQL database**. U ziet het JSON-script voor het maken van een gekoppelde Azure SQL-service in de editor.

   ![Nieuwe gegevens opslag 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. Breng de volgende wijzigingen aan in het JSON-script:

   1. Vervang `<servername>` door de naam van uw server.
   2. Vervang door `<databasename>` de Data Base waarin u de tabel en de opgeslagen procedure hebt gemaakt.
   3. Vervang door `<username@servername>` het gebruikers account dat toegang heeft tot de data base.
   4. Vervang door `<password>` het wacht woord voor het gebruikers account.

      ![Nieuwe gegevens opslag 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Klik op **implementeren** op de opdracht balk om de gekoppelde service te implementeren. Controleer of de AzureSqlLinkedService in de structuur weergave aan de linkerkant wordt weer gegeven.

    ![structuur weergave met gekoppelde service 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
U moet een uitvoer gegevensset opgeven voor een opgeslagen procedure activiteit, zelfs als de opgeslagen procedure geen gegevens produceert. Dat komt doordat het de uitvoer gegevensset is die de planning van de activiteit bewaart (hoe vaak de activiteit wordt uitgevoerd-per uur, dagelijks, enzovoort). De uitvoer gegevensset moet een **gekoppelde service** gebruiken die verwijst naar een Azure SQL database of Azure Synapse Analytics of een SQL Server-Data Base waarin u de opgeslagen procedure wilt uitvoeren. De uitvoer gegevensset kan worden gebruikt als een manier om het resultaat van de opgeslagen procedure voor verdere verwerking door een andere activiteit (het[koppelen van activiteiten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) in de pijp lijn) door te geven. Data Factory schrijft de uitvoer van een opgeslagen procedure echter niet automatisch naar deze gegevensset. Het is de opgeslagen procedure die naar een SQL-tabel schrijft waarnaar de uitvoer-gegevensset verwijst. In sommige gevallen kan de uitvoer gegevensset een dummy- **gegevensset** zijn (een gegevensset die verwijst naar een tabel die geen uitvoer van de opgeslagen procedure bevat). Deze dummy-gegevensset wordt alleen gebruikt om de planning op te geven voor het uitvoeren van de opgeslagen procedure activiteit.

1. Klik op **... Meer** op de werk balk, klikt u op **nieuwe gegevensset** en klikt u op **Azure SQL**. **Nieuwe gegevensset** op de opdracht balk en selecteer **Azure SQL**.

    ![structuur weergave met gekoppelde service 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopieer/plak het volgende JSON-script in de JSON-editor.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Klik op **implementeren** op de opdracht balk om de gegevensset te implementeren. Controleer of de gegevensset in de structuur weergave wordt weer gegeven.

    ![structuur weergave met gekoppelde services](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Een pijp lijn maken met SqlServerStoredProcedure-activiteit
Nu gaan we een pijp lijn maken met een opgeslagen procedure activiteit.

Let op de volgende eigenschappen:

- De eigenschap **type** wordt ingesteld op **SqlServerStoredProcedure**.
- De **storedProcedureName** in type eigenschappen is ingesteld op **usp_sample** (naam van de opgeslagen procedure).
- De sectie **storedProcedureParameters** bevat één para meter met de naam **DateTime**. De naam en het hoofdletter gebruik van de para meter in JSON moeten overeenkomen met de naam en de behuizing van de para meter in de definitie van de opgeslagen procedure. Als u null moet door geven voor een para meter, gebruikt u de syntaxis: `"param1": null` (alle kleine letters).

1. Klik op **... Meer** op de opdracht balk en klik op **nieuwe pijp lijn**.
2. Kopieer/plak het volgende JSON-fragment:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Klik op **implementeren** op de werk balk om de pijp lijn te implementeren.

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken
1. Klik op **X** om de blades van de Data Factory-editor te sluiten en om terug te keren naar de Data Factory-blade. Klik op **Diagram**.

    ![diagram tegel 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. In de **diagramweergave** ziet u een overzicht van de pijplijnen en gegevenssets die voor deze zelfstudie worden gebruikt.

    ![diagram tegel 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Dubbel klik in de diagram weergave op de gegevensset `sprocsampleout` . U ziet de segmenten met de status gereed. Er moeten vijf segmenten zijn omdat er een segment wordt geproduceerd voor elk uur tussen de begin-en eind tijd van de JSON.

    ![diagram Tegel 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Wanneer een segment de status **gereed** heeft, voert u een query uit op `select * from sampletable` de-data base om te controleren of de gegevens in de tabel zijn ingevoegd door de opgeslagen procedure.

   ![Uitvoergegevens](./media/data-factory-stored-proc-activity/output.png)

   Zie [de pijp lijn bewaken](data-factory-monitor-manage-pipelines.md) voor meer informatie over het bewaken van Azure Data Factory pijp lijnen.

## <a name="specify-an-input-dataset"></a>Een invoer-gegevensset opgeven
In de walkthrough bevat de opgeslagen procedure activiteit geen invoer gegevens sets. Als u een invoer-gegevensset opgeeft, wordt de opgeslagen procedure-activiteit pas uitgevoerd als het segment van de invoer gegevensset beschikbaar is (in de status gereed). De gegevensset kan een externe gegevensset zijn (die niet wordt geproduceerd door een andere activiteit in dezelfde pijp lijn) of een interne gegevensset die wordt geproduceerd door een upstream-activiteit (de activiteit die wordt uitgevoerd vóór deze activiteit). U kunt meerdere invoer gegevens sets opgeven voor de opgeslagen procedure activiteit. Als u dit doet, wordt de opgeslagen procedure activiteit alleen uitgevoerd wanneer alle segmenten van de invoer gegevensset beschikbaar zijn (in de status gereed). De invoer gegevensset kan niet worden gebruikt in de opgeslagen procedure als een para meter. Het wordt alleen gebruikt voor het controleren van de afhankelijkheid voordat de opgeslagen procedure activiteit wordt gestart.

## <a name="chaining-with-other-activities"></a>Koppelen met andere activiteiten
Als u een upstream-activiteit wilt koppelen aan deze activiteit, geeft u de uitvoer van de upstream-activiteit op als een invoer van deze activiteit. Wanneer u dit doet, wordt de opgeslagen procedure-activiteit pas uitgevoerd als de upstream-activiteit is voltooid en de uitvoer gegevensset van de upstream-activiteit beschikbaar is (in de status gereed). U kunt uitvoer gegevens sets van meerdere upstream-activiteiten opgeven als invoer gegevens sets van de opgeslagen procedure activiteit. Wanneer u dit doet, wordt de opgeslagen procedure activiteit alleen uitgevoerd wanneer alle segmenten van de invoer gegevensset beschikbaar zijn.

In het volgende voor beeld is de uitvoer van de Kopieer activiteit: output DataSet. Dit is een invoer van de opgeslagen procedure activiteit. Daarom wordt de opgeslagen procedure-activiteit pas uitgevoerd als de Kopieer bewerking is voltooid en het segment output dataset beschikbaar is (in de status gereed). Als u meerdere invoer gegevens sets opgeeft, wordt de opgeslagen procedure-activiteit pas uitgevoerd als alle segmenten van de invoer gegevensset beschikbaar zijn (in de status gereed). De invoer gegevens sets kunnen niet rechtstreeks worden gebruikt als para meters voor de opgeslagen procedure activiteit.

Zie [meerdere activiteiten in een pijp lijn](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline) voor meer informatie over het koppelen van activiteiten

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Op dezelfde manier geeft u de uitvoer gegevensset van de opgeslagen procedure activiteit op als invoer van de downstream-activiteit in de pijp lijn om de Store-procedure activiteit te koppelen aan **downstream-activiteiten** (de activiteiten die worden uitgevoerd nadat de opgeslagen procedure is voltooid).

> [!IMPORTANT]
> Bij het kopiëren van gegevens naar Azure SQL Database of SQL Server, kunt u de **SqlSink** in Kopieer activiteit configureren om een opgeslagen procedure aan te roepen met behulp van de eigenschap **sqlWriterStoredProcedureName** . Zie voor meer informatie [opgeslagen procedure aanroepen vanuit Kopieer activiteit](data-factory-invoke-stored-procedure-from-copy-activity.md). Voor meer informatie over de eigenschap raadpleegt u de volgende connector artikelen: [Azure SQL database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Bij het kopiëren van gegevens uit Azure SQL Database of SQL Server of Azure Synapse Analytics kunt u **SqlSource** configureren in de Kopieer activiteit om een opgeslagen procedure aan te roepen voor het lezen van gegevens uit de bron database met behulp van de eigenschap **sqlReaderStoredProcedureName** . Zie voor meer informatie de volgende connector artikelen: [Azure SQL database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON-indeling
Dit is de JSON-indeling voor het definiëren van een opgeslagen procedure activiteit:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

In de volgende tabel worden deze JSON-eigenschappen beschreven:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| naam | Naam van de activiteit |Yes |
| beschrijving |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt |No |
| type | Moet worden ingesteld op: **SqlServerStoredProcedure** | Yes |
| invoer | Optioneel. Als u een invoer-gegevensset opgeeft, moet deze beschikbaar zijn (in de status gereed) voor het uitvoeren van de opgeslagen procedure activiteit. De invoer gegevensset kan niet worden gebruikt in de opgeslagen procedure als een para meter. Het wordt alleen gebruikt voor het controleren van de afhankelijkheid voordat de opgeslagen procedure activiteit wordt gestart. |No |
| uitvoer | U moet een uitvoer gegevensset opgeven voor een opgeslagen procedure-activiteit. Uitvoer gegevensset Hiermee geeft u de **planning** op voor de opgeslagen procedure activiteit (elk uur, wekelijks, maandelijks, enzovoort). <br/><br/>De uitvoer gegevensset moet een **gekoppelde service** gebruiken die verwijst naar een Azure SQL database of Azure Synapse Analytics of een SQL Server-Data Base waarin u de opgeslagen procedure wilt uitvoeren. <br/><br/>De uitvoer gegevensset kan worden gebruikt als een manier om het resultaat van de opgeslagen procedure voor verdere verwerking door een andere activiteit (het[koppelen van activiteiten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) in de pijp lijn) door te geven. Data Factory schrijft de uitvoer van een opgeslagen procedure echter niet automatisch naar deze gegevensset. Het is de opgeslagen procedure die naar een SQL-tabel schrijft waarnaar de uitvoer-gegevensset verwijst. <br/><br/>In sommige gevallen kan de uitvoer gegevensset een dummy- **gegevensset** zijn, die alleen wordt gebruikt om de planning op te geven voor het uitvoeren van de opgeslagen procedure activiteit. |Yes |
| storedProcedureName |Geef de naam op van de opgeslagen procedure in Azure SQL Database, Azure Synapse Analytics of SQL Server die wordt vertegenwoordigd door de gekoppelde service die door de uitvoer tabel wordt gebruikt. |Yes |
| storedProcedureParameters |Geef waarden op voor opgeslagen procedure parameters. Als u null moet door geven voor een para meter, gebruikt u de syntaxis: "param1": Null (alle kleine letters). Raadpleeg het volgende voor beeld voor meer informatie over het gebruik van deze eigenschap. |No |

## <a name="passing-a-static-value"></a>Een statische waarde door geven
Nu kunt u een andere kolom met de naam scenario toevoegen aan de tabel met een statische waarde met de naam document Sample.

![Voorbeeld gegevens 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabel**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Opgeslagen procedure:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Geef nu de **scenario** parameter door en de waarde van de opgeslagen procedure activiteit. De sectie **typeProperties** in het voor gaande voor beeld ziet eruit als in het volgende code fragment:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory gegevensset:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory pijp lijn**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
