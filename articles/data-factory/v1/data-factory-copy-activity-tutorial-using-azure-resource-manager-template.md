---
title: 'Zelfstudie: een pijplijn maken met een Resource Manager-sjabloon '
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn met behulp van een Azure Resource Manager-sjabloon. Met deze pijplijn worden gegevens vanuit een Azure-blobopslag gekopieerd naar Azure SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 18e8965a152deb6393d7e3b63ea22994484e100c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377241"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Zelfstudie: een Azure Resource Manager-sjabloon gebruiken voor het maken van een Data Factory-pijplijn om gegevens te kopiëren 
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md). 

In deze zelfstudie wordt uitgelegd hoe u een Azure Resource Manager-sjabloon maakt om een Azure-gegevensfactory te maken. In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Er worden geen invoergegevens mee getransformeerd in uitvoergegevens. Meer informatie over het transformeren van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: een pijplijn bouwen om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md).

In deze zelfstudie maakt u een pijplijn met één activiteit erin: kopieeractiviteit. De kopieeractiviteit in Data Factory kopieert gegevens uit een ondersteund gegevensarchief naar een ondersteund sinkgegevensarchief. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten.

Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Meerdere activiteiten in een pijplijn](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie. 

> [!NOTE] 
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Meer informatie over het transformeren van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: een pijplijn bouwen om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md). 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Neem [Overzicht van de zelfstudie en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) door en voer de **vereiste** stappen uit.
* Volg de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/) om de meest recente versie van Azure PowerShell te installeren op uw computer. In deze zelfstudie gebruikt u PowerShell om Data Factory-entiteiten te implementeren. 
* Zie [Authoring Azure Resource Manager Templates](../../azure-resource-manager/templates/template-syntax.md) (Azure Resource Manager-sjablonen samenstellen) voor meer informatie over Azure Resource Manager-sjablonen (optioneel).

## <a name="in-this-tutorial"></a>In deze zelfstudie
In deze zelfstudie maakt u een gegevensfactory met de volgende Data Factory-entiteiten:

| Entiteit | Beschrijving |
| --- | --- |
| Een gekoppelde Azure Storage-service |Koppelt uw Azure Storage-account aan de gegevensfactory. Azure Storage is de brongegevensopslag en Azure SQL Database de opvanggegevensopslag voor de kopieerbewerking in de handleiding. Het geeft de opslagaccount aan die de invoergegevens voor de kopieerbewerking bevat. |
| Een gekoppelde Azure SQL Database-service |Koppelt uw Azure SQL Database aan de gegevensfactory. Het geeft de database aan die de uitvoergegevens voor de kopieerbewerking bevat. |
| Azure Blob-invoergegevensset |Verwijst naar de gekoppelde Azure Storage-service. De gekoppelde service verwijst naar een Azure Storage-account en in de Azure Blob-gegevensset vindt u de container, map en bestandsnaam in de opslag die de invoergegevens bevat. |
| Azure SQL-uitvoergegevensset |Verwijst naar de gekoppelde Azure SQL-service. De gekoppelde Azure SQL-service verwijst naar een logische SQL-server en de Azure SQL-gegevensset bevat de naam van de tabel met de uitvoergegevens. |
| Gegevenspijplijn |De pijplijn heeft één activiteit van het type Kopiëren waarbij de Azure Blob-gegevensset als invoer en de Azure SQL-gegevensset als uitvoer wordt genomen. Met de kopieerbewerking worden gegevens van een Azure-blob naar een tabel in Azure SQL Database gekopieerd. |

Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Er zijn twee soorten activiteiten: [activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) en [activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md). In deze zelfstudie maakt u een pijplijn met één activiteit (kopiëren).

![Een Azure Blob-gegevensset kopiëren naar Azure SQL Database](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

In de volgende sectie vindt u de volledige Resource Manager-sjabloon voor het definiëren van Data Factory-entiteiten zodat u de zelfstudie snel kunt doorlopen en de sjabloon kunt testen. Raadpleeg de sectie [Data Factory-entiteiten in de sjabloon](#data-factory-entities-in-the-template) om te lezen hoe elke Data Factory-entiteit wordt gedefinieerd.

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
            "type": "Microsoft.DataFactory/datafactories",
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
Maak een JSON-bestand met de naam **ADFCopyTutorialARM.json** in de map **C:\ADFGetStarted**. Geef het bestand de volgende inhoud:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the logical SQL server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the logical SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the logical SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
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
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
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
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
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
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>JSON-bestand met parameters
Maak een JSON-bestand met de naam **ADFCopyTutorialARM-Parameters.json** dat parameters voor de Azure Resource Manager-sjabloon bevat. 

> [!IMPORTANT]
> Geef de naam en sleutel van uw Azure Storage-account op voor de parameters storageAccountName en storageAccountKey.  
> 
> Geef de logische SQL-server, database, gebruiker en het wachtwoord op voor de parameters sqlServerName, databaseName, sqlServerUserName en sqlServerPassword.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the logical SQL server>" },
        "databaseName": { "value": "<Name of the database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
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
   * Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken.
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. Voer de volgende opdracht uit om Data Factory-entiteiten te implementeren met behulp van het Resource Manager-sjabloon dat u in stap 1 hebt gemaakt.

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>De pijplijn bewaken

1. Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

1. Klik in het linkermenu op **Gegevensfabrieken**, (of) klik op **Alle services** en vervolgens op **Gegevensfabrieken** onder de categorie **Informatie en analytische gegevens**.
   
    ![Het menu voor gegevensfactory's](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)

1. Zoek uw gegevenfactory (AzureBlobToAzureSQLDatabaseDF) op de pagina **Gegevensfactory's** op. 
   
    ![Naar de gegevensfactory zoeken](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  

1. Klik op uw Azure-gegevensfactory. De startpagina voor de gegevensfactory wordt weergegeven.
   
    ![De startpagina voor de gegevensfactory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  

1. Volg de instructies in [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor het bewaken van de pijplijn en gegevenssets die u tijdens deze zelfstudie hebt gemaakt. Visual Studio biedt momenteel geen ondersteuning voor het bewaken van Data Factory-pijplijnen.

1. Wanneer een segment de status **Gereed** heeft, controleert u of de gegevens naar de tabel **emp** in de Azure SQL Database zijn gekopieerd.

Zie [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor meer informatie over het gebruik van Azure Portal voor het bewaken van de pijplijn en gegevenssets te bewaken die u tijdens deze zelfstudie hebt gemaakt.

Zie [Azure Data Factory-pijplijnen bewaken en beheren met de Controle-app](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de app Controleren en beheren voor het bewaken van uw gegevenspijplijnen.

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entiteiten in de sjabloon

### <a name="define-data-factory"></a>Een gegevensfactory definiëren

U definieert een gegevensfactory in de Resource Manager-sjabloon zoals in het volgende voorbeeld wordt weergegeven:

```json
{
  "resources": [
    {
      "name": "[variables('dataFactoryName')]",
      "apiVersion": "2015-10-01",
      "type": "Microsoft.DataFactory/datafactories",
      "location": "West US"
    }
  ]
}
```

De variabele dataFactoryName wordt als volgt gedefinieerd: 

```json
{
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
}
```

Het is een unieke tekenreeks op basis van de resourcegroep-id.

### <a name="defining-data-factory-entities"></a>Data Factory-entiteiten definiëren

De volgende Data Factory-entiteiten worden in de JSON-sjabloon gedefinieerd: 

1. [Een gekoppelde Azure Storage-service](#azure-storage-linked-service)
2. [Een gekoppelde Azure SQL-service](#azure-sql-database-linked-service)
3. [De Azure Blob-gegevensset](#azure-blob-dataset)
4. [De Azure SQL-gegevensset](#azure-sql-dataset)
5. [De gegevenspijplijn met een kopieerbewerking](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. U hebt een container gemaakt en gegevens naar dit opslagaccount geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). In deze sectie geeft u de naam en sleutel van uw Azure Storage-account op. Zie [Een gekoppelde Azure Storage-service](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde Azure Storage-service. 

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

De tekenreeks connectionString maakt gebruik van de parameters storageAccountName en storageAccountKey. De waarden voor deze parameters worden doorgegeven met behulp van een configuratiebestand. De definitie maakt ook gebruik van de variabelen azureStorageLinkedService en dataFactoryName die zijn gedefinieerd in de sjabloon. 

#### <a name="azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service

De AzureSqlLinkedService koppelt uw database in Azure SQL Database aan de gegevensfactory. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u de emp-tabel in deze database gemaakt. U geeft in deze sectie de logische SQL-servernaam, -databasenaam, -gebruikersnaam en -wachtwoord op. Zie [Een gekoppelde Azure SQL-service](data-factory-azure-sql-connector.md#linked-service-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde Azure SQL-service.  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
      "type": "AzureSqlDatabase",
      "description": "Azure SQL linked service",
      "typeProperties": {
        "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
      }
    }
}
```

De tekenreeks connectionString maakt gebruik van de parameters sqlServerName, databaseName, sqlServerUserName en sqlServerPassword waarvan de waarden worden doorgegeven met behulp van een configuratiebestand. De definitie maakt ook gebruik van de volgende variabelen uit de sjabloon: azureSqlLinkedServiceName en dataFactoryName.

#### <a name="azure-blob-dataset"></a>De Azure Blob-gegevensset
De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. In de gegevenssetdefinitie van de Azure-blob geeft u de namen op van de blobcontainer, map en het bestand met de invoergegevens op. Zie [Eigenschappen van de Azure Blob-gegevensset](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een Azure Blob-gegevensset. 

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
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>De Azure SQL-gegevensset
U geeft de naam op van de tabel in Azure SQL Database die de gekopieerde gegevens uit de Azure Blob-opslag bevat. Zie [Eigenschappen van de Azure SQL-gegevensset](data-factory-azure-sql-connector.md#dataset-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een Azure SQL-gegevensset. 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Gegevenspijplijn
U definieert een pijplijn waarmee gegevens uit de Azure Blob-gegevensset naar de Azure SQL-gegevensset worden gekopieerd. Zie [JSON-bestand voor een pijplijn](data-factory-create-pipelines.md#pipeline-json) voor beschrijvingen van JSON-elementen die worden gebruikt voor het definiëren van een pijplijn in dit voorbeeld. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>De sjabloon hergebruiken
U hebt in de zelfstudie een sjabloon voor het definiëren van Data Factory-entiteiten en een sjabloon voor het doorgeven van waarden voor parameters gemaakt. Met de pijplijn worden gegevens gekopieerd van een Azure Storage-account naar Azure SQL Database die zijn opgegeven via parameters. Als u dezelfde sjabloon wilt gebruiken voor het implementeren van Data Factory-entiteiten in verschillende omgevingen, maakt u een parameterbestand voor elke omgeving en gebruikt u dit bij het implementeren in die omgeving.     

Voorbeeld:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

De eerste opdracht maakt gebruik van het parameterbestand voor de ontwikkelomgeving, de tweede voor de testomgeving en de derde voor de productieomgeving.  

U kunt de sjabloon ook hergebruiken om herhaalde taken uit te voeren. U moet bijvoorbeeld veel gegevensfactory's maken met een of meer pijplijnen die dezelfde logica implementeren, maar elke gegevensfactory maakt gebruik van andere Storage- en SQL Database-accounts. In dit scenario gebruikt u dezelfde sjabloon in dezelfde omgeving (voor het ontwikkelen, testen of de productie) met andere parameterbestanden om de gegevensfactory's te maken.   

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u voor een kopieerbewerking Azure-blobopslag gebruikt als brongegevensarchief en Azure SQL Database als doelgegevensarchief. De volgende tabel bevat een lijst met gegevensarchieven die worden ondersteund als bron en doel voor de kopieeractiviteit: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klik op de koppeling voor de gegevensopslag in de tabel voor meer informatie over het kopiëren van gegevens naar/uit een gegevensarchief.
