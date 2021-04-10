---
title: Uw eerste gegevensfactory maken (Resource Manager-sjabloon)
description: In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn op basis van een Azure Resource Manager-sjabloon.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: f4f5156870ec5119c71a0acc77786c71ab8b7e62
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783044"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Zelfstudie: Uw eerste Azure-data factory maken met behulp van een Azure Resource Manager-sjabloon
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sjabloon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Quickstart: een gegevensfactory maken met Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

In dit artikel gebruikt u een Azure Resource Manager-sjabloon om uw eerste Azure-gegevensfactory te maken. Als u de zelfstudie wilt volgen met andere hulpprogramma's/SDK's, selecteert u een van de opties uit de vervolgkeuzelijst.

De pijplijn in deze zelfstudie heeft één activiteit: **HDInsight Hive-activiteit**. Deze activiteit voert een Hive-script uit op een Azure HDInsight-cluster dat invoergegevens transformeert om uitvoergegevens te produceren. De pijplijn is gepland on één keer per maand tussen de opgegeven begin- en eindtijd te worden uitgevoerd. 

> [!NOTE]
> Met de gegevenspijplijn in deze zelfstudie worden invoergegevens getransformeerd in uitvoergegevens. Meer informatie over het kopiëren van gegevens met behulp van Azure Data Factory vindt u in [Tutorial: Copy data from Blob storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Zelfstudie: gegevens kopiëren vanuit blobopslag naar SQL Database).
> 
> De pijplijn in deze zelfstudie heeft maar één activiteit van het type: HDInsightHive. Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lees het artikel [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) en voer de **vereiste** stappen uit.
* Volg de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/) om de meest recente versie van Azure PowerShell te installeren op uw computer.
* Zie [Authoring Azure Resource Manager Templates](../../azure-resource-manager/templates/template-syntax.md) (Azure Resource Manager-sjablonen samenstellen) voor meer informatie over Azure Resource Manager-sjablonen. 

## <a name="in-this-tutorial"></a>In deze zelfstudie

| Entiteit | Beschrijving |
| --- | --- |
| Een gekoppelde Azure Storage-service |Koppelt uw Azure Storage-account aan de gegevensfactory. Het Azure Storage-account bevat de in- en uitvoergegevens van de pijplijn in dit voorbeeld. |
| Een gekoppelde HDInsight-service op aanvraag |Koppelt een HDInsight-cluster op aanvraag aan de gegevensfactory. Het cluster wordt automatisch voor u gemaakt om gegevens te verwerken en wordt verwijderd nadat de verwerking is voltooid. |
| Azure Blob-invoergegevensset |Verwijst naar de gekoppelde Azure Storage-service. De gekoppelde service verwijst naar een Azure Storage-account en in de Azure Blob-gegevensset vindt u de container, map en bestandsnaam in de opslag die de invoergegevens bevat. |
| Azure Blob-uitvoergegevensset |Verwijst naar de gekoppelde Azure Storage-service. De gekoppelde service verwijst naar een Azure Storage-account en in de Azure Blob-gegevensset vindt u de container, map en bestandsnaam in de opslag die de uitvoergegevens bevat. |
| Gegevenspijplijn |De gegevenspijplijn bevat één activiteit van het type HDInsightHive die de invoergegevensset gebruikt en de uitvoergegevensset produceert. |

Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Er zijn twee soorten activiteiten: [activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) en [activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md). In deze zelfstudie maakt u een pijplijn met één activiteit (Hive-activiteit).

In de volgende sectie vindt u de volledige Resource Manager-sjabloon voor het definiëren van Data Factory-entiteiten zodat u de zelfstudie snel kunt doorlopen en de sjabloon kunt testen. Raadpleeg de sectie [Data Factory-entiteiten in de sjabloon](#data-factory-entities-in-the-template) om te lezen hoe elke Data Factory-entiteit wordt gedefinieerd. Zie [Microsoft.DataFactory resource types](/azure/templates/microsoft.datafactory/allversions) (Microsoft.DataFactory-resourcetypen) voor informatie over de JSON-syntaxis en de eigenschappen voor Data Factory-resources in een sjabloon.

## <a name="data-factory-json-template"></a>JSON-sjabloon voor Data Factory
De Resource Manager-sjabloon op het hoogste niveau voor het definiëren van een gegevensfactory is als volgt: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Maak een JSON-bestand met de naam **ADFTutorialARM.json** in de map **C:\ADFGetStarted**. Geef het bestand de volgende inhoud:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> In [Tutorial: Create a pipeline with Copy Activity using an Azure Resource Manager template](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md) (Zelfstudie: met behulp van een Azure Resource Manager-sjabloon een pijplijn maken met een kopieerbewerking) vindt u een ander voorbeeld van een Resource Manager-sjabloon voor het maken van een Azure-gegevensfactory.  
> 
> 

## <a name="parameters-json"></a>JSON-bestand met parameters
Maak een JSON-bestand met de naam **ADFTutorialARM-Parameters.json** dat parameters voor de Azure Resource Manager-sjabloon bevat.  

> [!IMPORTANT]
> Geef de naam en sleutel van uw Azure Storage-account op voor de parameters **storageAccountName** en **storageAccountKey** in dit parameterbestand. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Mogelijk beschikt u over afzonderlijke JSON-bestanden met parameters voor ontwikkel-, test- en productieomgevingen die u met dezelfde JSON-sjabloon voor Data Factory kunt gebruiken. U kunt met behulp van een Power Shell-script het implementeren van Data Factory-entiteiten in deze omgevingen automatiseren. 
> 
> 

## <a name="create-data-factory"></a>Een gegevensfactory maken
1. Open **Azure PowerShell** en voer de volgende opdracht uit: 
   * Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal.
     ```PowerShell
     Connect-AzAccount
     ```  
   * Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven.
     ```PowerShell
     Get-AzSubscription
     ``` 
   * Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Dit moet hetzelfde abonnement zijn als het abonnement dat u in de Azure Portal gebruikt.
     ```
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```   
2. Voer de volgende opdracht uit om Data Factory-entiteiten te implementeren met behulp van het Resource Manager-sjabloon dat u in stap 1 hebt gemaakt. 

    ```PowerShell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>De pijplijn bewaken
1. Wanneer u zich hebt aangemeld bij [Azure Portal](https://portal.azure.com/), klikt u op **Bladeren** en selecteert u **Gegevensfactory’s**.
     ![Bladeren -> Gegevensfactory's](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. Klik op de blade **Gegevensfactory’s** op de gegevensfactory (**TutorialFactoryARM**) die u hebt gemaakt.    
3. Klik op de blade **Gegevensfactory** van uw gegevensfactory op **Diagram**.

     ![Tegel Diagram](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. In de **diagramweergave** ziet u een overzicht van de pijplijnen en gegevenssets die voor deze zelfstudie worden gebruikt.
   
   ![Diagramweergave](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. Dubbelklik in de diagramweergave op de gegevensset **AzureBlobOutput**. U ziet het segment dat momenteel wordt verwerkt.
   
    ![Schermafbeelding van de AzureBlobOutput-gegevensset.](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Als het verwerken is voltooid, ziet u dat het segment de status **Gereed** heeft. Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd (ongeveer 20 minuten). Daarom kunt u ervan uitgaan dat het **ongeveer 30 minuten** duurt voordat het segment in de pijplijn is verwerkt.
   
    ![Gegevensset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Wanneer het segment de status **Gereed** heeft, controleert u de map **partitioneddata** in de container **adfgetstarted** in uw blobopslag voor de uitvoergegevens.  

Zie [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor instructies over het gebruik van Azure Portal-blades om de pijplijn en gegevenssets te bewaken die u tijdens deze zelfstudie hebt gemaakt.

U kunt ook de app voor bewaking en beheer gebruiken om uw gegevenspijplijnen te bewaken. Zie [Azure Data Factory-pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

> [!IMPORTANT]
> Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (input.log) naar de map met invoergegevens van de container adfgetstarted.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entiteiten in de sjabloon
### <a name="define-data-factory"></a>Een gegevensfactory definiëren
U definieert een gegevensfactory in de Resource Manager-sjabloon zoals in het volgende voorbeeld wordt weergegeven:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/factories",
    "location": "West US"
}
```
De variabele dataFactoryName wordt als volgt gedefinieerd: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
Het is een unieke tekenreeks op basis van de resourcegroep-id.  

### <a name="defining-data-factory-entities"></a>Data Factory-entiteiten definiëren
De volgende Data Factory-entiteiten worden in de JSON-sjabloon gedefinieerd: 

* [Een gekoppelde Azure Storage-service](#azure-storage-linked-service)
* [Een gekoppelde HDInsight-service op aanvraag](#hdinsight-on-demand-linked-service)
* [De Azure Blob-invoergegevensset](#azure-blob-input-dataset)
* [De Azure Blob-uitvoergegevensset](#azure-blob-output-dataset)
* [De gegevenspijplijn met een kopieerbewerking](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
In deze sectie geeft u de naam en sleutel van uw Azure Storage-account op. Zie [Een gekoppelde Azure Storage-service](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde Azure Storage-service. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
De tekenreeks **connectionString** maakt gebruik van de parameters storageAccountName en storageAccountKey. De waarden voor deze parameters worden doorgegeven met behulp van een configuratiebestand. De definitie maakt ook gebruik van de variabelen azureStorageLinkedService en dataFactoryName die zijn gedefinieerd in de sjabloon. 

#### <a name="hdinsight-on-demand-linked-service"></a>Een gekoppelde HDInsight-service op aanvraag
Zie het artikel [Compute linked services (Gekoppelde services verwerken)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde HDInsight-service op aanvraag.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```

Houd rekening met de volgende punten: 

* Met de bovenstaande JSON maakt Data Factory voor u een HDInsight-cluster **op basis van Linux**. Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie. 
* U kunt **uw eigen HDInsight-cluster** gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) voor meer informatie.
* Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment moet worden verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid.
  
    Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers worden als volgt opgebouwd: adf **naamvanuwgegevensfactory**-**naamvangekoppeldeservice**-datum-/tijdstempel. Gebruik hulpprogramma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie.

#### <a name="azure-blob-input-dataset"></a>Azure Blob-invoergegevensset
U geeft de namen van de blobcontainer, map en het bestand met de invoergegevens op. Zie [Eigenschappen van de Azure Blob-gegevensset](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een Azure Blob-gegevensset. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Deze definitie maakt gebruik van de volgende parameters die in de parametersjabloon zijn gedefinieerd: blobContainer, inputBlobFolder en inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset
U geeft de namen van de blobcontainer en de map met de uitvoergegevens op. Zie [Eigenschappen van de Azure Blob-gegevensset](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een Azure Blob-gegevensset.  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Deze definitie maakt gebruik van de volgende parameters die in de parametersjabloon zijn gedefinieerd: blobContainer en outputBlobFolder. 

#### <a name="data-pipeline"></a>Gegevenspijplijn
U definieert een pijplijn waarmee gegevens worden omgezet door een Hive-script uit te voeren in een Azure HDInsight-cluster op aanvraag. Zie [JSON-bestand voor een pijplijn](data-factory-create-pipelines.md#pipeline-json) voor beschrijvingen van JSON-elementen die worden gebruikt voor het definiëren van een pijplijn in dit voorbeeld. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                    "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                        "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                    }
                },
                "inputs": [
                    {
                        "name": "[variables('blobInputDatasetName')]"
                    }
                ],
                "outputs": [
                    {
                        "name": "[variables('blobOutputDatasetName')]"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>De sjabloon hergebruiken
U hebt in de zelfstudie een sjabloon voor het definiëren van Data Factory-entiteiten en een sjabloon voor het doorgeven van waarden voor parameters gemaakt. Als u dezelfde sjabloon wilt gebruiken voor het implementeren van Data Factory-entiteiten in verschillende omgevingen, maakt u een parameterbestand voor elke omgeving en gebruikt u dit bij het implementeren in die omgeving.     

Voorbeeld:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
De eerste opdracht maakt gebruik van het parameterbestand voor de ontwikkelomgeving, de tweede voor de testomgeving en de derde voor de productieomgeving.  

U kunt de sjabloon ook hergebruiken om herhaalde taken uit te voeren. U moet bijvoorbeeld veel data factory's maken met een of meer pijplijnen die dezelfde logica implementeren, maar elke data factory maakt gebruik van andere Azure Storage- en Azure SQL Database-accounts. In dit scenario gebruikt u dezelfde sjabloon in dezelfde omgeving (voor het ontwikkelen, testen of de productie) met andere parameterbestanden om de gegevensfactory's te maken. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Resource Manager-sjabloon voor het maken van een gateway
Hier volgt een Resource Manager-voorbeeldsjabloon voor het op de achtergrond maken van een logische gateway. Installeer een gateway op uw on-premises computer of virtuele Azure IaaS-machine en registreer de gateway met een sleutel bij de Data Factory-service. Zie [Gegevens verplaatsen tussen on-premises en de cloud](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/factories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Met deze sjabloon maakt u een data factory met de naam GatewayUsingArmDF, met een gateway met de naam: GatewayUsingARM. 

## <a name="see-also"></a>Zie ook

| Onderwerp | Beschrijving |
|:--- |:--- |
| [Pijplijnen](data-factory-create-pipelines.md) |Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) |Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) |In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) |In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. |

