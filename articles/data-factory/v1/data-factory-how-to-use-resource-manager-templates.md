---
title: Resource Manager-sjablonen gebruiken in Data Factory
description: Meer informatie over het maken en gebruiken van Azure Resource Manager sjablonen voor het maken van Data Factory entiteiten.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4c840d50fb80a57e9c68645ade8baa22b455fa84
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495494"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Sjablonen gebruiken om Azure Data Factory entiteiten te maken
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

## <a name="overview"></a>Overzicht
Bij het gebruik van Azure Data Factory voor uw behoeften op het gebied van gegevens integratie, kunt u het hetzelfde patroon gebruiken voor verschillende omgevingen, of dezelfde taak herhaaldelijk in dezelfde oplossing implementeren. Met sjablonen kunt u deze scenario's op een eenvoudige manier implementeren en beheren. Sjablonen in Azure Data Factory zijn ideaal voor scenario's waarbij herbruikbaarheid en herhalingen betrokken zijn.

Denk na over de situatie waarin een organisatie tien productie bedrijven wereld wijd heeft. De logboeken van elke fabriek worden opgeslagen in een afzonderlijke SQL Server-Data Base. Het bedrijf wil één data warehouse in de Cloud bouwen voor ad-hoc-analyses. Het wil ook dezelfde logica maar verschillende configuraties hebben voor ontwikkelings-, test-en productie omgevingen.

In dit geval moet een taak in dezelfde omgeving worden herhaald, maar met verschillende waarden voor de 10 gegevens fabrieken voor elke fabriek. In feite is **herhaling** aanwezig. Sjabloon staat de abstractie van deze algemene stroom (dat wil zeggen, pijp lijnen met dezelfde activiteiten in elke data factory) toe, maar gebruikt een apart parameter bestand voor elke productie-fabriek.

Bovendien kunnen sjablonen, aangezien de organisatie deze 10 gegevens fabrieken meermaals wil implementeren in verschillende omgevingen, gebruikmaken van deze **herbruikbaarheid** door gebruik te maken van afzonderlijke parameter bestanden voor ontwikkelings-, test-en productie omgevingen.

## <a name="templating-with-azure-resource-manager"></a>Sjabloon met Azure Resource Manager
[Azure Resource Manager sjablonen](../../azure-resource-manager/templates/overview.md) zijn een uitstekende manier om sjabloon in azure Data Factory te krijgen. Resource Manager-sjablonen definiëren de infra structuur en configuratie van uw Azure-oplossing via een JSON-bestand. Omdat Azure Resource Manager sjablonen met alle/de meeste Azure-Services werken, kan deze veel worden gebruikt om eenvoudig alle resources van uw Azure-assets te beheren. Zie [Azure Resource Manager sjablonen ontwerpen](../../azure-resource-manager/templates/template-syntax.md) voor meer informatie over de Resource Manager-sjablonen in het algemeen.

## <a name="tutorials"></a>Zelfstudies
Raadpleeg de volgende zelf studies voor stapsgewijze instructies voor het maken van Data Factory entiteiten met behulp van Resource Manager-sjablonen:

* [Zelf studie: een pijp lijn maken om gegevens te kopiëren met behulp van Azure Resource Manager sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Zelf studie: een pijp lijn maken voor het verwerken van gegevens met behulp van Azure Resource Manager sjabloon](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory sjablonen op GitHub
Bekijk de volgende Azure Quick Start-sjablonen op GitHub:

* [Een gegevensfactory maken voor het kopiëren van gegevens van Azure Blob Storage naar Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Een gegevensfactory maken met hive-activiteit in een Azure HDInsight-cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Een gegevensfactory maken voor het kopiëren van gegevens uit Sales Force naar Azure-blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Een gegevensfactory maken die activiteiten koppelt: kopieert gegevens van een FTP-server naar Azure-blobs, roept een Hive-script op een HDInsight-cluster op aanvraag om de gegevens te transformeren en kopieert resultaat naar Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

U kunt uw Azure Data Factory sjablonen gratis delen via [Azure Quick](https://azure.microsoft.com/documentation/templates/)start. Raadpleeg de [bijdrage gids](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) bij het ontwikkelen van sjablonen die kunnen worden gedeeld via deze opslag plaats.

De volgende secties bevatten informatie over het definiëren van Data Factory resources in een resource manager-sjabloon.

## <a name="defining-data-factory-resources-in-templates"></a>Data Factory resources in sjablonen definiëren
De sjabloon op het hoogste niveau voor het definiëren van een data factory is:

```JSON
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
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Een gegevensfactory definiëren
U definieert een gegevensfactory in de Resource Manager-sjabloon zoals in het volgende voorbeeld wordt weergegeven:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
De dataFactoryName wordt als volgt gedefinieerd in ' variabelen ':

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Gekoppelde services definiëren

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Zie [gekoppelde](data-factory-azure-blob-connector.md#azure-storage-linked-service) Services voor opslag of [gekoppelde reken](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) service voor meer informatie over de JSON-eigenschappen voor de specifieke gekoppelde service die u wilt implementeren. De para meter ' dependsOn ' geeft de naam van de bijbehorende data factory. Een voor beeld van het definiëren van een gekoppelde service voor Azure Storage wordt weer gegeven in de volgende JSON-definitie:

### <a name="define-datasets"></a>Gegevens sets definiëren

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Raadpleeg de [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor meer informatie over de JSON-eigenschappen voor het specifieke type gegevensset dat u wilt implementeren. Houd er rekening mee dat de para meter ' dependsOn ' de naam van de bijbehorende data factory en de gekoppelde opslag service bevat. Een voor beeld van het definiëren van het type gegevensset van Azure Blob-opslag wordt weer gegeven in de volgende JSON-definitie:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Pijp lijnen definiëren

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Raadpleeg de [definitie van pijp lijnen](data-factory-create-pipelines.md#pipeline-json) voor meer informatie over de JSON-eigenschappen voor het definiëren van de specifieke pijp lijn en activiteiten die u wilt implementeren. Houd er rekening mee dat de para meter ' dependsOn ' de naam van de data factory bevat en alle bijbehorende gekoppelde services of gegevens sets. Een voor beeld van een pijp lijn waarmee gegevens worden gekopieerd van Azure-Blob Storage naar Azure SQL Database wordt weer gegeven in het volgende JSON-code fragment:

```JSON
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
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parameterizing-sjabloon Data Factory
Zie [Aanbevolen procedures voor het maken van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-best-practices.md)voor best practices voor parameterizing. Over het algemeen moet het parameter gebruik worden geminimaliseerd, met name als er variabelen kunnen worden gebruikt. Geef alleen para meters op in de volgende scenario's:

* Instellingen variëren per omgeving (bijvoorbeeld: ontwikkeling, testen en productie)
* Geheimen (zoals wacht woorden)

Als u geheimen van [Azure Key Vault](../../key-vault/general/overview.md) moet halen wanneer u Azure Data Factory entiteiten implementeert met behulp van sjablonen, geeft u de **sleutel kluis** en **geheime naam** op, zoals wordt weer gegeven in het volgende voor beeld:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Tijdens het exporteren van sjablonen voor bestaande gegevens fabrieken wordt momenteel nog niet ondersteund.
>
>