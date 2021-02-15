---
title: SSIS-pakket aanroepen met behulp van Azure Data Factory-opgeslagen procedure-activiteit
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket aanroept vanuit een Azure Data Factory-pijp lijn met behulp van de opgeslagen procedure activiteit.
author: linda33wj
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 6c831fa1c6351840693f2b10a22f59c5cc424d0f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392881"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Een SSIS-pakket aanroepen met behulp van opgeslagen procedure activiteit in Azure Data Factory
In dit artikel wordt beschreven hoe u een SSIS-pakket aanroept vanuit een Azure Data Factory pijp lijn met behulp van een opgeslagen procedure activiteit. 

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [SSIS-pakketten aanroepen met behulp van opgeslagen procedure activiteit in](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Vereisten

### <a name="azure-sql-database"></a>Azure SQL Database 
In de stapsgewijze instructies in dit artikel wordt gebruikgemaakt van Azure SQL Database. U kunt ook een door Azure SQL beheerd exemplaar gebruiken.

### <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken
Een Azure SSIS Integration runtime maken als u er nog geen hebt door de stapsgewijze instructie in de [zelf studie te volgen: SSIS-pakketten implementeren](../tutorial-deploy-ssis-packages-azure.md). U kunt Data Factory versie 1 niet gebruiken om een Azure SSIS Integration runtime te maken. 

## <a name="azure-powershell"></a>Azure PowerShell
In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijp lijn te maken met een opgeslagen procedure activiteit die een SSIS-pakket aanroept.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
De volgende procedure bevat stappen voor het maken van een data factory. In deze data factory maakt u een pijp lijn met een opgeslagen procedure activiteit. Met de activiteit opgeslagen procedure wordt een opgeslagen procedure uitgevoerd in de SSISDB-data base om uw SSIS-pakket uit te voeren.

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) en voer de opdracht uit. Bijvoorbeeld: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit.
2. Voer de volgende opdracht uit om de resourcegroep te maken: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit. 
3. Definieer een variabele voor de naam van de data factory. 

    > [!IMPORTANT]
    >  Werk de naam van de data factory zodanig bij dat deze uniek is. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Als u de data factory wilt maken, voert u de volgende cmdlet **New-AzDataFactory** uit met behulp van de eigenschap Location en ResourceGroupName van de variabele $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen **Inzender** of **Eigenaar**, of moet dit een **beheerder** van het Azure-abonnement zijn.

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken
Maak een gekoppelde service om uw data base te koppelen aan Azure SQL Database die als host fungeert voor de SSIS-catalogus naar uw data factory. Data Factory maakt gebruik van informatie in deze gekoppelde service om verbinding te maken met SSISDB-data base en voert een opgeslagen procedure uit om een SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **AzureSqlDatabaseLinkedService.js** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud: 

    > [!IMPORTANT]
    > Vervang &lt; servername &gt; , &lt; username &gt; @ &lt; servername &gt; en &lt; Password door de &gt; waarden van uw Azure SQL database voordat u het bestand opslaat.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. Ga in **Azure PowerShell** naar de map **C:\ADF\RunSSISPackage** .
3. Voer de cmdlet **New-AzDataFactoryLinkedService** uit om de gekoppelde service te maken: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
Deze uitvoer gegevensset is een dummy-gegevensset waarmee de planning van de pijp lijn wordt gestimuleerd. U ziet dat de frequentie is ingesteld op uur en dat interval is ingesteld op 1. Daarom wordt de pijp lijn één keer per uur uitgevoerd binnen de begin-en eind tijd van de pijp lijn. 

1. Maak een OutputDataset.jsin het bestand met de volgende inhoud: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Voer de cmdlet **New-AzDataFactoryDataset** uit om een gegevensset te maken. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Een pijp lijn maken met een opgeslagen procedure activiteit 
In deze stap maakt u een pijp lijn met een opgeslagen procedure activiteit. Met de activiteit wordt de sp_executesql opgeslagen procedure aangeroepen om uw SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **MyPipeline.js** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud:

    > [!IMPORTANT]
    > Vervang &lt; de mapnaam &gt; , &lt; de project naam &gt; , &lt; de pakket naam &gt; met de namen van de map, het project en het pakket in de SSIS-catalogus voordat u het bestand opslaat.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Als u de pijp lijn wilt maken: **RunSSISPackagePipeline**, voert u de cmdlet **New-AzDataFactoryPipeline** uit.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Voer **Get-AzDataFactorySlice** uit om details over alle segmenten van de uitvoer gegevensset * * te verkrijgen. Dit is de uitvoer tabel van de pijp lijn.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    De StartDateTime die u hier opgeeft, is dezelfde begintijd die u hebt opgegeven in de JSON van de pijplijn. 
1. Voer **Get-AzDataFactoryRun** uit om gegevens over het uitvoeren van de activiteit op te halen voor een specifiek segment.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    U kunt deze cmdlet blijven uitvoeren tot u ziet dat het segment de status **Gereed** of **Mislukt** heeft gekregen. 

    U kunt de volgende query uitvoeren op de SSISDB-data base in uw server om te controleren of het pakket is uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel [opgeslagen procedure-activiteit](data-factory-stored-proc-activity.md) voor meer informatie over de opgeslagen procedure activiteit.