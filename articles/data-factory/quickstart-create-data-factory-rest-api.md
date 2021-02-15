---
title: Een data factory in Azure maken met behulp van REST API
description: Maak een Azure Data Factory-pijplijn om gegevens te kopiëren van de ene locatie in Azure Blob Storage naar de andere.
author: linda33wj
ms.service: data-factory
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: jingwang
ms.openlocfilehash: 527f8d63f2a2fd5c44e187c00c0651300eb4ad9f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373722"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Quickstart: Een Azure data factory en pijplijn maken door de REST-API te gebruiken

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory is een cloudgebaseerde gegevensintegratieservice waarmee u gegevensgestuurde werkstromen kunt maken in de cloud. Op deze manier kunt u de verplaatsing en transformatie van gegevens indelen en automatiseren. Met Azure Data Factory kunt u gegevensgestuurde werkstromen (ook wel pijplijnen) maken en plannen die gegevens uit verschillende gegevensarchieven kunnen opnemen en de gegevens kunnen verwerken/transformeren met behulp van rekenservices zoals Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics en Azure Machine Learning. Daarnaast kunt u de uitvoergegevens publiceren naar gegevensarchieven zoals Azure Synapse Analytics, zodat BI-toepassingen (business intelligence) ze kunnen gebruiken.

In deze snelstartgids wordt beschreven hoe u REST-API kunt gebruiken om een Azure data factory te maken. Met de pijplijn in deze data factory worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure-abonnement**. Als u geen abonnement hebt, kunt u een [gratis proefaccount](https://azure.microsoft.com/pricing/free-trial/) maken.
* **Een Azure Storage-account**. U gebruikt de blob-opslag als gegevensopslag voor **bron** en **sink**. Als u geen Azure-opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.
* Maak een **blob-container** in Blob Storage, maak een **invoermap** in de container en upload een aantal bestanden naar de map. U kunt hulpprogramma's zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om verbinding te maken met Azure Blob Storage, een blob-container te maken, een invoerbestand te uploaden en het uitvoerbestand te verifiëren.
* Installeer **Azure PowerShell**. Volg de instructies in [How to install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren). Deze snelstartgids maakt gebruik van PowerShell om REST-API-aanroepen aan te roepen.
* **Maak een toepassing in Azure Active Directory** aan de hand van [deze instructie](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Noteer de volgende waarden voor gebruik in latere stappen: **toepassings-id**, **clientSecrets** en **tenant-id**. Wijs de toepassing toe aan de rol '**Inzender**'.

## <a name="set-global-variables"></a>Globale variabelen instellen

1. Start **PowerShell**. Houd Azure PowerShell geopend tot het einde van deze snelstartgids. Als u het programma sluit en opnieuw opent, moet u de opdrachten opnieuw uitvoeren.

    Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal:

    ```powershell
    Connect-AzAccount
    ```
    Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven:

    ```powershell
    Get-AzSubscription
    ```
    Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Vervang **SubscriptionId** door de id van uw Azure-abonnement:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Voer de volgende opdrachten uit nadat u de plaatsaanduidingen hebt vervangen door uw eigen waarden, om globale variabelen in te stellen voor gebruik in latere stappen.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Verifiëren met Azure AD

Voer de volgende opdrachten uit om te verifiëren met Azure Active Directory (AAD):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $clientSecrets)
$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de volgende opdrachten uit om een data factory te maken:

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$factoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

Dit is het voorbeeldantwoord:

```json
{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>Gekoppelde services maken

U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze snelstartgids hoeft u maar één gekoppelde Azure Storage-service te maken die in het voorbeeld wordt gebruikt als zowel de bron voor het kopiëren en als de sinkopslag, met de naam 'AzureStorageLinkedService'.

Voer de volgende opdrachten uit om een gekoppelde service te maken met de naam **AzureStorageLinkedService**:

Vervang &lt;accountName&gt; en &lt;accountKey&gt; door de naam en sleutel van uw Azure-opslagaccount voordat u de opdrachten uitvoert.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier volgt een voorbeeld van uitvoer:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>Gegevenssets maken

U definieert een gegevensset die de gegevens vertegenwoordigt die van een bron naar een sink moeten worden gekopieerd. In dit voorbeeld maakt u twee gegevenssets: InputDataSet en OutputDataSet. Ze verwijzen naar de gekoppelde Azure Storage-service die u in de vorige sectie hebt gemaakt. De invoergegevensset vertegenwoordigt de brongegevens in de invoermap. In de definitie van de invoergegevensset geeft u de blob-container (adftutorial) en de map (input) op, en het bestand (emp.txt) dat de brongegevens bevat. De uitvoergegevensset vertegenwoordigt de gegevens die worden gekopieerd naar de bestemming. In de definitie van de uitvoergegevensset geeft u de blob-container (adftutorial) en de map (output) op, en het bestand waarnaar de gegevens worden gekopieerd.

**InputDataset maken**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier volgt een voorbeeld van uitvoer:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**OutputDataset maken**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier volgt een voorbeeld van uitvoer:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-pipeline"></a>Pijplijn maken

In dit voorbeeld bevat deze pijplijn één Copy-activiteit. De Copy-activiteit verwijst naar de gegevenssets InputDataset en OutputDataset die u in de vorige stap heb gemaakt als invoer en uitvoer.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
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
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier volgt een voorbeeld van uitvoer:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>Pijplijnuitvoering maken

In deze stap activeert u een pijplijnuitvoering. De pijplijn-run-id die in de hoofdtekst van het antwoord wordt geretourneerd, wordt later in de bewakings-API gebruikt.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Hier volgt een voorbeeld van uitvoer:

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

## <a name="monitor-pipeline"></a>De pijplijn bewaken

1. Voer het volgende script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```json
    {  
        "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "debugRunId":null,
        "runGroupId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "pipelineName":"Adfv2QuickStartPipeline",
        "parameters":{  

        },
        "invokedBy":{  
            "id":"2bb3938176ee43439752475aa12b2251",
            "name":"Manual",
            "invokedByType":"Manual"
        },
        "runStart":"2019-09-03T07:22:47.0075159Z",
        "runEnd":"2019-09-03T07:22:57.8862692Z",
        "durationInMs":10878,
        "status":"Succeeded",
        "message":"",
        "lastUpdated":"2019-09-03T07:22:57.8862692Z",
        "annotations":[  

        ],
        "runDimension":{  

        },
        "isLatest":true
    }
    ```

2. Voer het volgende script uit om uitvoeringsdetails van de kopieeractiviteit op te halen, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```
    Hier volgt een voorbeeld van uitvoer:

    ```json
    {  
        "value":[  
            {  
                "activityRunEnd":"2019-09-03T07:22:56.6498704Z",
                "activityName":"CopyFromBlobToBlob",
                "activityRunStart":"2019-09-03T07:22:49.0719311Z",
                "activityType":"Copy",
                "durationInMs":7577,
                "retryAttempt":null,
                "error":"@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}",
                "activityRunId":"32951886-814a-4d6b-b82b-505936e227cc",
                "iterationHash":"",
                "input":"@{source=; sink=; enableStaging=False}",
                "linkedServiceName":"",
                "output":"@{dataRead=20; dataWritten=20; filesRead=1; filesWritten=1; sourcePeakConnections=1; sinkPeakConnections=1; copyDuration=4; throughput=0.01; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (Central US); usedDataIntegrationUnits=4; usedParallelCopies=1; executionDetails=System.Object[]}",
                "userProperties":"",
                "pipelineName":"Adfv2QuickStartPipeline",
                "pipelineRunId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
                "status":"Succeeded",
                "recoveryStatus":"None",
                "integrationRuntimeNames":"defaultintegrationruntime",
                "executionDetails":"@{integrationRuntime=System.Object[]}"
            }
        ]
    }
    ```
## <a name="verify-the-output"></a>De uitvoer controleren

Gebruik Azure Storage Explorer om te controleren of het bestand is gekopieerd van 'inputPath' naar 'outputPath', zoals u hebt opgegeven bij het maken van een pijplijnuitvoering.

## <a name="clean-up-resources"></a>Resources opschonen
De resources die u hebt gemaakt in de Quick Start kunt u op twee manieren opschonen. U kunt de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) verwijderen, met alle resources uit de resourcegroep. Als u de andere resources intact wilt houden, verwijdert u alleen de data factory die u in deze zelfstudie hebt gemaakt.

Voer de volgende opdracht uit om de gehele resourcegroep te verwijderen:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Voer de volgende opdracht uit om alleen de data factory te verwijderen:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. Doorloop de [zelfstudies](tutorial-copy-data-dot-net.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's.
