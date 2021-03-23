---
title: Azure Machine Learning Studio-modellen (klassiek) bijwerken met behulp van Azure Data Factory
description: Hierin wordt beschreven hoe u voorspellende pijp lijnen maakt met behulp van Azure Data Factory en Azure Machine Learning Studio (klassiek)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 5eac6f939ffcc39961f508a993ca17757221503f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783095"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Azure Machine Learning Studio-modellen (klassiek) bijwerken met resource activiteit bijwerken

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dit artikel vormt een aanvulling op het belangrijkste Azure Data Factory-Azure Machine Learning Studio (klassiek) integratie artikel: [Maak voorspellende pijp lijnen met behulp van Azure machine learning Studio (klassiek) en Azure Data Factory](transform-data-using-machine-learning.md). Als u dit nog niet hebt gedaan, raadpleegt u het hoofd artikel voordat u dit artikel leest.

## <a name="overview"></a>Overzicht
Als onderdeel van het proces van de inzet-modellen (klassieke Azure Machine Learning Studio) wordt uw model getraind en opgeslagen. Vervolgens gebruikt u het om een voorspellende webservice te maken. De webservice kan vervolgens worden gebruikt in websites, Dash boards en mobiele apps.

Modellen die u maakt met behulp van Azure Machine Learning Studio (klassiek) zijn doorgaans niet statisch. Wanneer er nieuwe gegevens beschikbaar zijn of wanneer de consumer van de API hun eigen gegevens heeft, moet het model opnieuw worden getraind. 

Het opnieuw trainen kan regel matig worden uitgevoerd. Met Azure Machine Learning Studio de activiteit batch-uitvoering en update resource kunt u het operationeel maken-model (klassiek) opnieuw trainen en de voorspellende webservice bijwerken met behulp van Data Factory.

In de volgende afbeelding ziet u de relatie tussen training en voorspellende webservices.

![Webservices](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Resource-activiteit voor het bijwerken van Azure Machine Learning Studio (klassiek)

In het volgende JSON-code fragment wordt een Azure Machine Learning Studio (klassieke) batch uitvoerings activiteit gedefinieerd.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Eigenschap                      | Beschrijving                              | Vereist |
| :---------------------------- | :--------------------------------------- | :------- |
| naam                          | Naam van de activiteit in de pijp lijn     | Ja      |
| beschrijving                   | Tekst die beschrijft wat de activiteit doet.  | Nee       |
| type                          | Voor Azure Machine Learning Studio (klassieke) resource activiteit bijwerken is het activiteitstype  **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Azure Machine Learning Studio (klassieke) gekoppelde service die de eigenschap updateResourceEndpoint bevat. | Ja      |
| trainedModelName              | De naam van de getrainde model module in het web service-experiment dat moet worden bijgewerkt | Ja      |
| trainedModelLinkedServiceName | De naam van Azure Storage gekoppelde service die het ilearner-bestand bewaart dat door de update bewerking is geüpload | Ja      |
| trainedModelFilePath          | Het relatieve bestandspad in trainedModelLinkedService dat het ilearner-bestand vertegenwoordigt dat door de update bewerking is geüpload | Ja      |

## <a name="end-to-end-workflow"></a>End-to-end werkstroom

Het hele proces van inzet het opnieuw trainen van een model en het bijwerken van de voorspellende webservices omvat de volgende stappen:

- Roep de **training-webservice** aan met behulp van de **batch Execution-activiteit**. Het aanroepen van een trainings-webservice is hetzelfde als het aanroepen van een voorspellende webservice die wordt beschreven in [voorspellende pijp lijnen maken met behulp van Azure machine learning Studio (klassiek) en Data Factory batch-uitvoerings activiteit](transform-data-using-machine-learning.md). De uitvoer van de training-webservice is een iLearner-bestand dat u kunt gebruiken om de voorspellende webservice bij te werken.
- Roep het **resource-eind punt** voor het bijwerken van de **voorspellende webservice** aan met de **activiteit resource bijwerken** om de webservice bij te werken met het nieuwe getrainde model.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Gekoppelde service Azure Machine Learning Studio (klassiek)

Voor de hierboven genoemde end-to-end werk stroom werkt, moet u twee Azure Machine Learning Studio (klassieke) gekoppelde services maken:

1. Een aan de trainings-webservice gekoppelde Azure Machine Learning Studio-Service (klassiek), wordt deze gekoppelde service gebruikt door activiteiten voor batch uitvoering op dezelfde manier als wat wordt vermeld in [voorspellende pijp lijnen maken met behulp van Azure machine learning Studio (klassiek) en Data Factory batch uitvoering](transform-data-using-machine-learning.md). Verschil is de uitvoer van de training-webservice is een iLearner-bestand dat vervolgens wordt gebruikt door de activiteit resource bijwerken om de voorspellende webservice bij te werken.
2. Een gekoppelde Azure Machine Learning Studio-Service (klassiek) aan het resource-eind punt van de update van de voorspellende webservice. Deze gekoppelde service wordt gebruikt door resource activiteit bijwerken om de voorspellende webservice bij te werken met het iLearner-bestand dat is geretourneerd door de bovenstaande stap.

Voor de tweede Azure Machine Learning Studio (klassieke) gekoppelde service is de configuratie anders wanneer uw Azure Machine Learning Studio (klassieke) webservice een klassieke webservice of een nieuwe webservice is. De verschillen worden afzonderlijk beschreven in de volgende secties.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web service is nieuw Azure Resource Manager-webservice

Als de webservice het nieuwe type webservice is dat een Azure Resource Manager-eind punt beschikbaar maakt, hoeft u niet het tweede **niet-standaard** eindpunt toe te voegen. De **updateResourceEndpoint** in de gekoppelde service heeft de volgende indeling:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

U kunt in de URL waarden ophalen voor de plaatsings houders bij het uitvoeren van query's op de webservice op de webservices van [Azure machine learning Studio (klassiek)](https://services.azureml.net/).

Voor het nieuwe type update resource-eind punt is Service-Principal-verificatie vereist. Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en geeft u deze de rol **Inzender** of **eigenaar** van het abonnement of de resource groep waarvan de webservice deel uitmaakt. De Zie [Service-Principal maken en machtigingen toewijzen om Azure-resources te beheren](../active-directory/develop/howto-create-service-principal-portal.md). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

- Toepassings-id
- Toepassings sleutel
- Tenant-id

Hier volgt een voor beeld van een gekoppelde service definitie:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Het volgende scenario bevat meer informatie. Het bevat een voor beeld voor het opnieuw trainen en bijwerken van Azure Machine Learning Studio (klassieke) modellen van een Azure Data Factory-pijp lijn.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Voor beeld: een model voor Azure Machine Learning Studio (klassiek) opnieuw trainen en bijwerken

Deze sectie bevat een voor beeld van een pijp lijn die gebruikmaakt van de activiteit voor het **uitvoeren van Azure machine learning Studio (klassiek)** voor het opnieuw trainen van een model. De pijp lijn gebruikt ook de activiteit voor het bijwerken van de **resource voor Azure machine learning Studio (klassiek)** om het model in de Score-webservice bij te werken. De sectie bevat ook JSON-fragmenten voor alle gekoppelde services, gegevens sets en pijp lijnen in het voor beeld.

### <a name="azure-blob-storage-linked-service"></a>Gekoppelde Azure Blob Storage-service:
De Azure Storage bevatten de volgende gegevens:

* trainings gegevens. De invoer gegevens voor de webservice van de Azure Machine Learning Studio (klassieke) training.
* iLearner-bestand. De uitvoer van de webservice voor de Azure Machine Learning Studio (klassieke) training. Dit bestand is ook de invoer voor de activiteit resource bijwerken.

Hier volgt een voor beeld van de JSON-definitie van de gekoppelde service:

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Trainings eindpunt van de gekoppelde service voor Azure Machine Learning Studio (klassiek)
Het volgende JSON-code fragment definieert een gekoppelde Azure Machine Learning Studio-Service (klassiek) die verwijst naar het standaard eindpunt van de training-webservice.

```JSON
{
    "name": "trainingEndpoint",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
            "apiKey": "myKey"
        }
    }
}
```

In **Azure machine learning Studio (klassiek)** gaat u als volgt te werk om waarden op te halen voor **mlEndpoint** en **apiKey**:

1. Klik op **WEBservices** in het menu links.
2. Klik op de training-webservice in de lijst met **webservices** .
3. Klik op Kopiëren naast tekstvak **API-sleutel** . Plak de sleutel in het klem bord in de Data Factory JSON-editor.
4. Klik in de **Azure machine learning Studio (klassiek)** op **batch-uitvoerings** koppeling.
5. Kopieer de **aanvraag-URI** uit de sectie **Request** en plak deze in de Data Factory JSON-editor.

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Gekoppelde service voor Azure Machine Learning Studio (klassiek) bij te werken Score-eind punt:
Het volgende JSON-code fragment definieert een gekoppelde Azure Machine Learning Studio-Service (klassiek) die verwijst naar het bijwerk bare eind punt van de Score-webservice.

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>Pijplijn
De pijp lijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. De batch Execution-activiteit neemt de trainings gegevens als invoer en produceert een iLearner-bestand als uitvoer. Met de activiteit resource bijwerken wordt dit iLearner-bestand gebruikt om de voorspellende webservice bij te werken.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
