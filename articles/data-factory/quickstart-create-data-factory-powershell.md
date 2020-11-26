---
title: Gegevens in Blob Storage kopiëren met behulp van Azure Data Factory
description: Maak een Azure-gegevensfactory om gegevens met PowerShell te kopiëren van de ene locatie naar de andere in Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.openlocfilehash: a7fcb4be47e0e1e62c190a9b089243a178df8e7a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013348"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Quickstart: Een Azure-gegevensfactory maken met behulp van PowerShell

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze quickstart wordt beschreven hoe u PowerShell kunt gebruiken om een Azure-gegevensfactory te maken. Met de pijplijn die u in deze data factory maakt, worden gegevens **gekopieerd** van één map naar een andere map in een Azure Blob Storage. Meer informatie over het **transformeren** van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: Gegevens transformeren met Spark](transform-data-using-spark.md).

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot de Data Factory-service. Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Aanmelden bij PowerShell

1. Start **PowerShell** op uw computer. Houd PowerShell geopend tot het einde van deze QuickStart. Als u het programma sluit en opnieuw opent, moet u deze opdrachten opnieuw uitvoeren.

2. Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal:

    ```powershell
    Connect-AzAccount
    ```

3. Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven:

    ```powershell
    Get-AzSubscription
    ```

4. Als u meerdere abonnementen hebt, voert u de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Vervang **SubscriptionId** door de id van uw Azure-abonnement:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) en voer de opdracht uit. Bijvoorbeeld: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit.

2. Voer de volgende opdracht uit om de resourcegroep te maken:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit.

3. Definieer een variabele voor de naam van de data factory. 

    > [!IMPORTANT]
    >  Werk de naam van de data factory zodanig bij dat deze uniek is. Bijvoorbeeld: ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Voer de volgende cmdlet **Set-AzDataFactoryV2** uit om de data factory te maken met de eigenschappen Locatie en ResourceGroupName van de variabele $ResGrp:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen **Inzender** of **Eigenaar**, of moet dit een **beheerder** van het Azure-abonnement zijn.

* Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.


## <a name="create-a-linked-service"></a>Een gekoppelde service maken

Maak gekoppelde services in een data factory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze QuickStart gaat u een gekoppelde Azure Storage-service maken die als bron- en als sinkopslag wordt gebruikt. De gekoppelde service beschikt over de verbindingsgegevens die de Data Factory-service tijdens runtime gebruikt om er een verbinding mee tot stand te brengen.

>[!TIP]
>In deze quickstart gebruikt u *Accountsleutel* als verificatietype voor uw gegevensarchief, maar u kunt zo nodig ook andere ondersteunde verificatiemethoden kiezen: *SAS URI*,*Service-principal* en *Beheerde identiteit*. Raadpleeg de bijbehorende secties in [dit artikel](./connector-azure-blob-storage.md#linked-service-properties) voor meer informatie.
>Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](./store-credentials-in-key-vault.md) voor gedetailleerde illustraties.

1. Maak een JSON-bestand met de naam **AzureStorageLinkedService.json** in de map **C:\ADFv2QuickStartPSH** met de volgende inhoud (maak de map ADFv2QuickStartPSH als deze nog niet bestaat):

    > [!IMPORTANT]
    > Vervang &lt;accountName&gt; en &lt;accountKey&gt; door de naam en sleutel van uw Azure Storage-account voordat u het bestand opslaat.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    Als u Kladblok gebruikt, selecteert u **Alle bestanden** voor het veld **Opslaan als** in het dialoogvenster **Opslaan als**. Als u dat niet doet, wordt mogelijk de extensie `.txt` toegevoegd aan het bestand. Bijvoorbeeld `AzureStorageLinkedService.json.txt`. Als u het bestand in Verkenner maakt voordat u het opent in Kladblok, ziet u de extensie `.txt` mogelijk niet omdat de optie **Extensies voor bekende bestandstypen verbergen** standaard is ingeschakeld. Verwijder de extensie `.txt` voordat u doorgaat met de volgende stap.

2. Schakel in **PowerShell** over naar de map **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Voer de cmdlet **Set-AzDataFactoryV2LinkedService** uit om de gekoppelde service te maken: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>Gegevenssets maken

In deze procedure maakt u twee gegevenssets: **InputDataset** en **OutputDataset**. Deze gegevenssets zijn van het type **Binair**. Ze verwijzen naar de gekoppelde Azure Storage-service die u in de vorige sectie hebt gemaakt.
De invoergegevensset vertegenwoordigt de brongegevens in de invoermap. In de definitie van de invoergegevensset geeft u de blob-container (**adftutorial**) en de map (**invoer**) op, en het bestand (**emp.txt**) dat de brongegevens bevat.
De uitvoergegevensset vertegenwoordigt de gegevens die worden gekopieerd naar de bestemming. In de definitie van de uitvoergegevensset geeft u de blob-container (**adftutorial**) en de map (**uitvoer**) op, en het bestand waarin de brongegevens zijn gekopieerd. 
1. Maak een JSON-bestand met de naam **InputDataset.json** in de map **C:\ADFv2QuickStartPSH**. Geef dit bestand de volgende inhoud:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. De gegevensset maken: **InputDataset**, voert u de cmdlet **Set-AzDataFactoryV2Dataset** uit.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. Herhaal de stappen om de uitvoergegevensset te maken. Maak een JSON-bestand met de naam **OutputDataset.json** in de map **C:\ADFv2QuickStartPSH**. Geef dit bestand de volgende inhoud:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. Voer de cmdlet **Set-AzDataFactoryV2Dataset** uit om de gegevensset **OutDataset** te maken.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>Een pijplijn maken

In deze procedure maakt en valideert u een pijplijn via een kopieeractiviteit die gebruikmaakt van de invoer- en uitvoergegevenssets. Met de kopieeractiviteit worden gegevens uit het bestand dat is opgegeven bij de instellingen voor de invoergegevensset gekopieerd naar het bestand dat is opgegeven in de instellingen voor de uitvoergegevensset.  

1. Maak een JSON-bestand met de naam **Adfv2QuickStartPipeline.json** in de map **C:\ADFv2QuickStartPSH**. Geef dit bestand de volgende inhoud:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 30,
                        "secureOutput": false,
                        "secureInput": false
                    },
                    "userProperties": [],
                    "typeProperties": {
                        "source": {
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

2. De pijplijn maken: **Adfv2QuickStartPipeline**, voer de cmdlet **Set-AzDataFactoryV2Pipeline** uit.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

In deze stap maakt u een pijplijnuitvoering.

Voer de cmdlet **Invoke-AzDataFactoryV2Pipeline** uit om de pijplijnuitvoering te maken. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer/plak het volgende script in het PowerShell-venster en druk op ENTER.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Hier volgt een uitvoervoorbeeld van de pijplijnuitvoering:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. Voer het volgende script uit om uitvoeringsdetails van de kopieeractiviteit op te halen, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Controleer of de uitvoer die u ziet vergelijkbaar is met de volgende voorbeelduitvoer van de uitvoering van de activiteit:

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. Doorloop de [zelfstudies](tutorial-copy-data-dot-net.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's.
