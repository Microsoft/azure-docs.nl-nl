---
title: Meerdere tabellen incrementeel kopiëren met behulp van PowerShell
description: In deze zelfstudie maakt u een Azure-gegevensfactory met een pijplijn waarmee wijzigingsgegevens uit meerdere tabellen van een SQL Server-database naar Azure SQL Database worden gekopieerd.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl, maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 61a4119947b1412d3e874458e06748fd40a381b4
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510257"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database-using-powershell"></a>Incrementeel gegevens uit meerdere tabellen in SQL Server naar Azure SQL Database kopiëren met behulp van PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een Azure-gegevensfactory met een pijplijn waarmee wijzigingsgegevens uit meerdere tabellen van een SQL Server-database naar Azure SQL Database worden gekopieerd.    

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Bereid de bron- en doelserver gegevensopslag voor.
> * Een data factory maken.
> * Een zelf-hostende Integration Runtime maken.
> * De Integration Runtime installeren. 
> * Maak gekoppelde services. 
> * Bron-, sink- en grenswaardegegevenssets maken.
> * Maken, starten en controleren van een pijplijn.
> * Bekijk de resultaten.
> * Gegevens in brontabellen toevoegen of bijwerken.
> * De pijplijn opnieuw uitvoeren en controleren.
> * De eindresultaten bekijken.

## <a name="overview"></a>Overzicht
Dit zijn de belangrijke stappen voor het maken van deze oplossing: 

1. **Selecteer de grenswaardekolom**.

    Selecteer één kolom in elke tabel van de brongegevensopslag, die kan worden gebruikt om de nieuwe of bijgewerkte records voor elke uitvoering te segmenteren. Normaal gesproken nemen de gegevens in deze geselecteerde kolom (bijvoorbeeld, last_modify_time of id) toe wanneer de rijen worden gemaakt of bijgewerkt. De maximale waarde in deze kolom wordt gebruikt als grenswaarde.

2. **Bereid een gegevensopslag voor om de grenswaarde in op te slaan**.

    In deze zelfstudie slaat u de grenswaarde op in een SQL-database.

3. **Maak een pijplijn met de volgende activiteiten**:
    
    a. Maak een ForEach-activiteit die door een lijst met namen van gegevensbrontabellen loopt, die als parameter is doorgegeven aan de pijplijn. Voor elke brontabel roept deze de volgende activiteiten voor het laden van de deltagegevens voor deze tabel op.

    b. Maak twee opzoekactiviteiten. Gebruik de eerste opzoekactiviteit om de laatste grenswaarde op te halen. Gebruik de tweede opzoekactiviteit om de nieuwe grenswaarde op te halen. Deze grenswaarden worden doorgegeven aan de kopieeractiviteit.

    c. Maak een kopieeractiviteit waarmee rijen uit de brongegevensopslag worden gekopieerd met een waarde uit de grenswaardekolom die groter is dan de oude grenswaarde en kleiner dan de nieuwe grenswaarde. Vervolgens worden de deltagegevens uit de brongegevensopslag als een nieuw bestand gekopieerd naar een Azure Blob-opslag.

    d. Maak een opgeslagen-procedureactiviteit waarmee de grenswaarde wordt bijgewerkt voor de pijplijn die de volgende keer wordt uitgevoerd. 

    Hier volgt de diagramoplossing op hoog niveau: 

    ![Stapsgewijs gegevens laden](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* **SQL Server**. In deze zelfstudie gebruikt u een SQL Server-database als een brongegevensopslag. 
* **Azure SQL-database**. U gebruikt een database in Azure SQL Database als de sink-gegevensopslag. Als u geen SQL-database hebt, raadpleegt u het artikel [Een Azure SQL Database maken](../azure-sql/database/single-database-create-quickstart.md) voor de stappen voor het maken van een database. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Brontabellen maken in uw SQL Server-database

1. Open [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) of [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) en maak verbinding met de SQL Server-database.

2. In **Server Explorer (SSMS)** of in het deelvenster **Verbindingen (Azure Data Studio)** klikt u met de rechtermuisknop op de database en kiest u **Nieuwe query**.

3. Voer de volgende SQL-opdracht uit op uw database om tabellen te maken met de naam `customer_table` en `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Doeltabellen in uw Azure SQL Database maken

1. Open [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) of [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) en maak verbinding met de SQL Server-database.

2. In **Server Explorer (SSMS)** of in het deelvenster **Verbindingen (Azure Data Studio)** klikt u met de rechtermuisknop op de database en kiest u **Nieuwe query**.

3. Voer de volgende SQL-opdracht uit op uw database om tabellen te maken met de naam `customer_table` en `project_table`:  

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
    ```

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Nog een tabel in Azure SQL Database maken om de bovengrenswaarde op te slaan

1. Voer de volgende SQL-opdracht uit op uw database om een tabel met de naam `watermarktable` te maken om de bovengrenswaarde op te slaan: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Initiële watermerkwaarden voor beide brontabellen in de watermerktabel invoegen.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Een opgeslagen procedure maken in de Azure SQL Database 

Voer de volgende opdracht uit om een opgeslagen procedure te maken in uw database. Deze opgeslagen procedure werkt de bovengrenswaarde bij elke pijplijnuitvoering bij. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Gegevenstypen en aanvullende opgeslagen procedures maken in Azure SQL Database

Voer de volgende query uit om twee opgeslagen procedures en twee gegevenstypen in uw database te maken. Deze worden gebruikt voor het samenvoegen van de gegevens uit de brontabellen in doeltabellen. 

Om het begin van de beleving toegankelijk te houden, gebruiken we direct deze opgeslagen procedures. Hiermee worden de deltagegevens doorgegeven via een tabelvariabele en vervolgens samengevoegd in het doelarchief. Let erop dat er geen 'groot' aantal deltarijen (meer dan 100) wordt verwacht dat moet worden opgeslagen in de tabelvariabele.  

Als u een groot aantal deltarijen in het doelarchief moet samenvoegen, kunt u het beste de kopieeractiviteit gebruiken om alle deltagegevens te kopiëren naar een tijdelijke ‘faseringstabel’ in het doelarchief. Vervolgens moet u uw eigen opgeslagen procedure maken zonder tabelvariabelen te gebruiken om ze te kunnen samenvoegen vanuit de ‘faseringstabel’ in de ‘definitieve tabel’. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END
```

### <a name="azure-powershell"></a>Azure PowerShell

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) en voer de opdracht uit. Een voorbeeld is `"adfrg"`.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$resourceGroupName`-variabele en voer de opdracht opnieuw uit.

2. Definieer een variabele voor de locatie van de data factory. 

    ```powershell
    $location = "East US"
    ```
3. Voer de volgende opdracht uit om de resourcegroep te maken: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$resourceGroupName`-variabele en voer de opdracht opnieuw uit.

4. Definieer een variabele voor de naam van de data factory. 

    > [!IMPORTANT]
    >  Werk de naam van de data factory zodanig bij dat deze uniek is. Bijvoorbeeld: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Voer de volgende cmdlet **Set AzDataFactoryV2** uit om de data factory te maken: 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Houd rekening met de volgende punten:

* De naam van de data factory moet een Globally Unique Identifier zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen Inzender of Eigenaar, of moet dit een beheerder van het Azure-abonnement zijn.

* Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensarchieven (Azure Storage, SQL Database, SQL Managed Instance, enzovoort) en rekenprocessen (Azure HDInsight, enzovoort) die worden gebruikt in de data factory, kunnen zich in andere regio's bevinden.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Gekoppelde services maken

U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze sectie maakt u gekoppelde services in uw SQL Server-database en uw database in Azure SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>De gekoppelde service voor SQL Server maken

In deze stap gaat u uw SQL Server-database aan de data factory koppelen.

1. Maak een JSON-bestand met de naam **SqlServerLinkedService.json** in de map C:\ADFTutorials\IncCopyMultiTableTutorial (maak de lokale mappen als deze nog niet bestaan) met de volgende inhoud. Selecteer de juiste sectie op basis van de verificatie die u gebruikt om verbinding te maken met SQL Server.  

    > [!IMPORTANT]
    > Selecteer de juiste sectie op basis van de verificatie die u gebruikt om verbinding te maken met SQL Server.

    Als u gebruikmaakt van SQL-verificatie, moet u de volgende JSON-definitie kopiëren:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Als u gebruikmaakt van Windows-verificatie, moet u de volgende JSON-definitie kopiëren:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Selecteer de juiste sectie op basis van de verificatie die u gebruikt om verbinding te maken met SQL Server.
    > - Vervang &lt;integration runtime name> door de naam van uw integration runtime.
    > - Vervang &lt;servername>, &lt;databasename>, &lt;username> en &lt;password> door de waarden van uw SQL Server-exemplaar voordat u het bestand opslaat.
    > - Als u een slash wilt gebruiken (`\`) in de naam van uw gebruikersaccount of server, moet u het escapeteken (`\`) gebruiken. Een voorbeeld is `mydomain\\myuser`.

2. Voer in PowerShell de volgende cmdlet uit om over te schakelen naar de map C:\ADFTutorials\IncCopyMultiTableTutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Voer de cmdlet **Set-AzDataFactoryV2LinkedService** uit om de gekoppelde service AzureStorageLinkedService te maken. In het volgende voorbeeld geeft u de waarden door voor de parameters *ResourceGroupName* en *DataFactoryName*: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```console
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>De gekoppelde SQL Database-service maken

1. Maak een JSON-bestand met de naam **AzureSQLDatabaseLinkedService.json** in de map C:\ADFTutorials\IncCopyMultiTableTutorial met de volgende inhoud. (Maak de map ADF als deze nog niet bestaat.) Vervang voordat u het bestand opslaat &lt;servername&gt;, &lt;database name&gt;, &lt;user name&gt; en &lt;password&gt; door de naam van uw SQL Server-database, databasenaam, gebruikersnaam en wachtwoord. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
2. Voer in PowerShell de cmdlet **Set-AzDataFactoryV2LinkedService** uit om de gekoppelde service AzureSQLDatabaseLinkedService te maken. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Gegevenssets maken

In deze stap maakt u gegevenssets die de gegevensbron, het gegevensdoel en de plaats voor het opslaan van de bovengrens aangeven.

### <a name="create-a-source-dataset"></a>Een brongegevensset maken

1. Maak een JSON-bestand met de naam **SourceDataset.json** in dezelfde map met de volgende inhoud: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    De kopieeractiviteit in de pijplijn gebruikt een SQL-query voor het laden van de gegevens in plaats van de hele tabel te laden.

2. Voer de cmdlet **Set-AzDataFactoryV2Dataset** uit om de gegevensset SourceDataset te maken.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Hier volgt een uitvoervoorbeeld van de cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Een sinkgegevensset maken

1. Maak een JSON-bestand met de naam **SinkDataset.json** in dezelfde map met de volgende inhoud. Het element tableName wordt in runtime dynamisch ingesteld door de pijplijn. De ForEach-activiteit in de pijplijn doorloopt een lijst met namen van tabellen en geeft de tabelnaam door aan deze gegevensset in elke iteratie. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

2. Voer de cmdlet **Set-AzDataFactoryV2Dataset** uit om de gegevensset SinkDataset te maken.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Hier volgt een uitvoervoorbeeld van de cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Een gegevensset maken voor een grenswaarde

In deze stap maakt u een gegevensset voor het opslaan van een bovengrenswaarde. 

1. Maak een JSON-bestand met de naam **WatermarkDataset.json** in dezelfde map met de volgende inhoud: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2. Voer de cmdlet **Set-AzDataFactoryV2Dataset** uit om de gegevensset WatermarkDataset te maken.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Hier volgt een uitvoervoorbeeld van de cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Een pijplijn maken

In deze pijplijn wordt een lijst met tabelnamen gebruikt als parameter. De **ForEach-activiteit** doorloopt de lijst met namen van tabellen en voert de volgende bewerkingen uit: 

1. Gebruik de **opzoekactiviteit** voor het ophalen van de oude bovengrenswaarde (de initiële waarde, of de waarde die is gebruikt in de laatste iteratie).

2. Gebruik de **opzoekactiviteit** voor het ophalen van de nieuwe bovengrenswaarde (de maximale waarde van de bovengrenskolom in de brontabel).

3. Gebruik de **kopieeractiviteit** voor het kopiëren van gegevens tussen deze twee bovengrenswaarden uit de brondatabase naar de doeldatabase.

4. Gebruik de **opgeslagen-procedureactiviteit** voor het bijwerken van de oude bovengrenswaarde die in de eerste stap van de volgende iteratie moet worden gebruikt. 

### <a name="create-the-pipeline"></a>Maak de pijplijn

1. Maak een JSON-bestand met de naam **IncrementalCopyPipeline.json** in dezelfde map met de volgende inhoud: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
2. Voer de cdmlet **Set-AzDataFactoryV2Pipeline** uit om de pijplijn IncrementalCopyPipeline te maken.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Hier volgt een voorbeeld van uitvoer: 

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>De pijplijn uitvoeren

1. Maak een parameterbestand met de naam **Parameters.json** in dezelfde map met de volgende inhoud:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
2. Voer de pijplijn IncrementalCopyPipeline uit met behulp van de cmdlet **Invoke-AzDataFactoryV2Pipeline**. Vervang tijdelijke aanduidingen door de namen van uw eigen resourcegroep en data factory.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Alle services**, zoek met het trefwoord *gegevensfactory's* en selecteer **Gegevensfactory's**. 

3. Zoek naar uw data factory in de lijst met data factory’s, en selecteer deze om de pagina **Data Factory** te openen. 

4. Ga naar de pagina **Data Factory** en selecteer **Maken en controleren** om de Azure Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

5. Selecteer op de pagina **Aan de slag** de optie **Controleren** aan de linkerkant. 
![Schermafbeelding toont de pagina Aan de slag voor Azure Data Factory.](media/doc-common-process/get-started-page-monitor-button.png)    

6. U kunt alle pijplijnactiviteiten en hun status zien. Let erop dat in het volgende voorbeeld de status van de pijplijnactiviteit **Geslaagd** is. U kunt parameters controleren die zijn doorgegeven aan de pijplijn door de koppeling in de kolom **Parameters** te selecteren. Als er een fout is opgetreden, ziet u een koppeling in de kolom **Fout**.

    ![Schermopname van pijplijnuitvoeringen voor een data factory, inclusief uw pijplijn.](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. Wanneer u de koppeling in de kolom **Acties** selecteert, ziet u alle uitvoeringen van activiteiten voor de pijplijn. 

8. Als u wilt terugkeren naar de weergave **Pijplijnuitvoeringen**, selecteert u **Alle pijplijnuitvoeringen** bovenaan. 

## <a name="review-the-results"></a>De resultaten bekijken

Voer in SQL Server Management Studio de volgende query's uit op de SQL-doeldatabase om te controleren of de gegevens van de brontabellen naar de doeltabellen zijn gekopieerd: 

**Query** 
```sql
select * from customer_table
```

**Uitvoer**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Query**

```sql
select * from project_table
```

**Uitvoer**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Query**

```sql
select * from watermarktable
```

**Uitvoer**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

U ziet dat de bovengrenswaarden voor beide tabellen zijn bijgewerkt. 

## <a name="add-more-data-to-the-source-tables"></a>Meer gegevens toevoegen aan de brontabellen

Voer de volgende query uit op de SQL Server-brondatabase om een bestaande rij bij te werken in customer_table. Voeg een nieuwe rij in project_table in. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Voer de pijplijn uit

1. Voer nu de pijplijn opnieuw uit door de volgende PowerShell-opdracht te gebruiken:

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Volg de pijplijnuitvoeringen met behulp van de instructies in de sectie [De pijplijn bewaken](#monitor-the-pipeline). Als de pijplijnstatus **In uitvoering** is, ziet u een andere actiekoppeling onder **Acties** om de pijplijn te annuleren. 

3. Klik op **Vernieuwen** om de lijst te vernieuwen totdat de uitvoering van de pijplijn is voltooid. 

4. Selecteer desgewenst de koppeling **Uitvoeringen van activiteit weergeven** onder **Acties** om alle activiteitsuitvoeringen te bekijken die gekoppeld zijn aan deze pijplijnuitvoering. 

## <a name="review-the-final-results"></a>De eindresultaten bekijken

Voer in SQL Server Management Studio de volgende query's uit op de doeldatabase om te controleren dat de bijgewerkte/nieuwe gegevens van de brontabellen naar de doeltabellen zijn gekopieerd. 

**Query** 
```sql
select * from customer_table
```

**Uitvoer**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Let op de nieuwe waarden van **Name** en **LastModifytime** voor de **PersonID** voor nummer 3. 

**Query**

```sql
select * from project_table
```

**Uitvoer**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Let erop dat de invoer van **NewProject** toegevoegd is aan project_table. 

**Query**

```sql
select * from watermarktable
```

**Uitvoer**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

U ziet dat de bovengrenswaarden voor beide tabellen zijn bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Bereid de bron- en doelserver gegevensopslag voor.
> * Een data factory maken.
> * Een zelf-hostende integration runtime (IR) maken.
> * De Integration Runtime installeren.
> * Maak gekoppelde services. 
> * Bron-, sink- en grenswaardegegevenssets maken.
> * Maken, starten en controleren van een pijplijn.
> * Bekijk de resultaten.
> * Gegevens in brontabellen toevoegen of bijwerken.
> * De pijplijn opnieuw uitvoeren en controleren.
> * De eindresultaten bekijken.

Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Spark-cluster in Azure:

> [!div class="nextstepaction"]
>[Incrementeel gegevens kopiëren van Azure SQL Database naar Azure Blob Storage met behulp van technologie voor het bijhouden van wijzigingen](tutorial-incremental-copy-change-tracking-feature-powershell.md)
