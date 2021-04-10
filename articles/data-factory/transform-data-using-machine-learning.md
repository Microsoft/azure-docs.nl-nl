---
title: Voorspellende gegevens pijplijnen maken
description: Meer informatie over het maken van een voorspellende pijp lijn met behulp van Azure Machine Learning Studio (klassieke)-batch uitvoering-activiteit in Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: 37a31891c3c1d812b396548036c4b59cc6523c2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375660"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Maak een voorspellende pijp lijn met behulp van Azure Machine Learning Studio (klassiek) en Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Huidige versie](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met [Azure machine learning Studio (klassiek)](https://azure.microsoft.com/documentation/services/machine-learning/) kunt u Predictive Analytics oplossingen bouwen, testen en implementeren. Vanuit een weergave punt op hoog niveau, wordt deze in drie stappen uitgevoerd:

1. **Een trainings experiment maken**. U voert deze stap uit met behulp van de Azure Machine Learning Studio (klassiek). Azure Machine Learning Studio (klassiek) is een samen werkende ontwikkel omgeving die u gebruikt om een predictive analytics model te trainen en te testen met behulp van trainings gegevens.
2. **Converteer deze naar een voorspellend experiment**. Zodra uw model is getraind met bestaande gegevens en u klaar bent om het te gebruiken voor het beoordelen van nieuwe gegevens, kunt u uw experiment voorbereiden en stroom lijnen.
3. **Implementeer de service als een webservice**. U kunt uw score experiment publiceren als een Azure-webservice. U kunt gegevens naar uw model verzenden via dit eind punt van de webservice en resultaat voorspellingen van het model ontvangen.

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory en Azure Machine Learning Studio (klassiek) samen
Met Azure Data Factory kunt u eenvoudig pijp lijnen maken die gebruikmaken van een gepubliceerde [Azure machine learning Studio (klassieke)](https://azure.microsoft.com/documentation/services/machine-learning) webservice voor Predictive Analytics. Met de **activiteit voor batch uitvoering** in een Azure Data Factory-pijp lijn kunt u een Azure machine learning Studio (klassieke) webservice aanroepen om voor spellingen te maken voor de gegevens in batch.

Na verloop van tijd moeten de voorspellende modellen in de Azure Machine Learning Studio (klassieke) Score experimenten opnieuw worden getraind met nieuwe invoer gegevens sets. U kunt een model opnieuw trainen vanuit een Data Factory pijp lijn door de volgende stappen uit te voeren:

1. Publiceer het trainings experiment (niet voorspellend experiment) als een webservice. U voert deze stap uit in de Azure Machine Learning Studio (klassiek), net zoals u het voorspellende experiment als een webservice in het vorige scenario beschikbaar had kunnen maken.
2. Gebruik de batch uitvoerings activiteit Azure Machine Learning Studio (klassiek) om de webservice voor het trainings experiment aan te roepen. In principe kunt u de activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek) gebruiken voor het aanroepen van zowel de webservice voor training als de Score van het web service.

Wanneer u klaar bent met het opnieuw trainen, werkt u de Score-webservice (voorspellende experiment die als een webservice wordt weer gegeven) bij met het nieuwe getrainde model met behulp van de activiteit voor het bijwerken van de **resource van Azure machine learning Studio (klassiek)**. Zie [modellen bijwerken met update resource activiteit](update-machine-learning-models.md) artikel voor meer informatie.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Gekoppelde service Azure Machine Learning Studio (klassiek)

U maakt een gekoppelde Azure Machine Learning Studio-service **(klassiek)** om een Azure machine learning Studio (klassieke) webservice te koppelen aan een Azure-Data Factory. De gekoppelde service wordt gebruikt door Azure Machine Learning Studio (klassieke) batch uitvoerings activiteit en de [resource activiteit](update-machine-learning-models.md)bij te werken.

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Zie het artikel [Compute linked Services](compute-linked-services.md) voor beschrijvingen van eigenschappen in de JSON-definitie.

Azure Machine Learning Studio (klassiek) ondersteunt zowel klassieke webservices als nieuwe webservices voor uw voorspellende experiment. U kunt kiezen welk recht er moet worden gebruikt in Data Factory. Als u de vereiste informatie voor het maken van de gekoppelde Azure Machine Learning Studio-Service (klassiek) wilt weer geven, gaat u naar https://services.azureml.net , waar al uw (nieuwe) webservices en klassieke webservices worden weer gegeven. Klik op de webservice die u wilt openen en klik op pagina **verbruikt** . Kopieer de **primaire sleutel** voor de eigenschap **apiKey** en **batch-aanvragen** voor de eigenschap **mlEndpoint** .

![Webservices Azure Machine Learning Studio (klassiek)](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek)

In het volgende JSON-code fragment wordt een Azure Machine Learning Studio (klassieke) batch uitvoerings activiteit gedefinieerd. De definitie van de activiteit bevat een verwijzing naar de gekoppelde Azure Machine Learning Studio-Service (klassiek) die u eerder hebt gemaakt.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| naam              | Naam van de activiteit in de pijp lijn     | Yes      |
| beschrijving       | Tekst die beschrijft wat de activiteit doet.  | No       |
| type              | Voor Data Lake Analytics U-SQL-activiteit is het type activiteit **AzureMLBatchExecution**. | Yes      |
| linkedServiceName | Gekoppelde services aan de gekoppelde Azure Machine Learning Studio-Service (klassiek). Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Yes      |
| webServiceInputs  | Sleutel-, waardeparen, de namen van de webservices voor Azure Machine Learning Studio (klassiek) toewijzen. De sleutel moet overeenkomen met de invoer parameters die zijn gedefinieerd in de gepubliceerde Azure Machine Learning Studio (klassieke) webservice. Waarde is een Azure Storage gekoppelde services en eigenschappen paar voor het opgeven van de invoer-BLOB-locaties. | No       |
| webServiceOutputs | Sleutel-, waardeparen, de namen van de uitvoer van de webservice van Azure Machine Learning Studio (klassiek) toewijzen. De sleutel moet overeenkomen met de uitvoer parameters die zijn gedefinieerd in de gepubliceerde Azure Machine Learning Studio (klassieke) webservice. Waarde is een Azure Storage gekoppelde services en eigenschappen paar van het bestandspad die de uitvoer BLOB-locaties opgeven. | No       |
| globalParameters  | Sleutel-, waardeparen die moeten worden door gegeven aan het Azure Machine Learning Studio (klassiek) batch-uitvoerings service-eind punt. Sleutels moeten overeenkomen met de namen van de webservice-para meters die zijn gedefinieerd in de gepubliceerde Azure Machine Learning Studio (klassieke) webservice. De waarden worden door gegeven in de eigenschap GlobalParameters van de batch-uitvoerings aanvraag van Azure Machine Learning Studio (klassiek) | No       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: experimenten met behulp van invoer/uitvoer van web service die verwijst naar gegevens in Azure Blob Storage

In dit scenario maakt de Azure Machine Learning Studio (klassieke) webservice voor spellingen met behulp van gegevens uit een bestand in een Azure Blob-opslag en slaat de Voorspellings resultaten op in de Blob-opslag. De volgende JSON definieert een Data Factory pijp lijn met een AzureMLBatchExecution-activiteit. Er wordt verwezen naar de invoer-en uitvoer gegevens in Azure blog Storage met behulp van een koppelen en een bestandspad. In de voor beeld-gekoppelde service van invoer en uitvoer verschillen, kunt u verschillende gekoppelde services gebruiken voor elk van uw invoer/uitvoer voor Data Factory om de juiste bestanden op te halen en te verzenden naar Azure Machine Learning Studio (klassieke) webservice.

> [!IMPORTANT]
> In uw Azure Machine Learning Studio (klassiek) experiment, worden de invoer-en uitvoer poorten voor de web-service en de algemene para meters standaard namen ("input1", "input2"), die u kunt aanpassen. De namen die u gebruikt voor de instellingen webServiceInputs, webServiceOutputs en globalParameters moeten exact overeenkomen met de namen in de experimenten. U kunt de nettolading van de voorbeeld aanvraag bekijken op de Help-pagina voor het uitvoeren van de batch-uitvoering voor uw Azure Machine Learning Studio (klassiek)-eind punt om de verwachte toewijzing te controleren.


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: experimenten met behulp van Reader/Writer-modules om te verwijzen naar gegevens in verschillende opslag ruimten
Een ander algemeen scenario bij het maken van Azure Machine Learning Studio (klassieke) experimenten is het gebruik van import gegevens-en uitvoer gegevens modules. De module gegevens importeren wordt gebruikt om gegevens te laden in een experiment en de module output data is om gegevens van uw experimenten op te slaan. Zie voor meer informatie over het importeren van gegevens en uitvoer gegevens modules de onderwerpen [gegevens importeren](/azure/machine-learning/studio-module-reference/import-data) en [uitvoer gegevens](/azure/machine-learning/studio-module-reference/export-data) in de MSDN-bibliotheek.

Wanneer u de modules gegevens importeren en uitvoer gegevens gebruikt, is het verstandig om een para meter voor de webservice te gebruiken voor elke eigenschap van deze modules. Met deze web-para meters kunt u de waarden tijdens runtime configureren. U kunt bijvoorbeeld een experiment maken met een module voor gegevens import die gebruikmaakt van een Azure SQL Database: XXX.database.windows.net. Nadat de webservice is geïmplementeerd, wilt u de gebruikers van de webservice in staat stellen een andere logische SQL-Server op te geven `YYY.database.windows.net` . U kunt een webservice-para meter gebruiken om deze waarde te kunnen configureren.

> [!NOTE]
> De invoer en uitvoer van webservices verschillen van de para meters van de webservice. In het eerste scenario hebt u gezien hoe u een invoer-en uitvoer kunt opgeven voor een webservice Azure Machine Learning Studio (klassiek). In dit scenario geeft u para meters door voor een webservice die overeenkomt met de eigenschappen van de modules import data/output data.

Laten we eens kijken naar een scenario voor het gebruik van web service-para meters. U hebt een gedistribueerde Azure Machine Learning Studio (klassieke) webservice die gebruikmaakt van een lees module om gegevens te lezen uit een van de gegevens bronnen die worden ondersteund door Azure Machine Learning Studio (klassiek) (bijvoorbeeld: Azure SQL Database). Nadat de batch uitvoering is uitgevoerd, worden de resultaten geschreven met behulp van een schrijf module (Azure SQL Database).  Er zijn geen ingangen en uitvoer van de webservice gedefinieerd in de experimenten. In dit geval kunt u het beste relevante webservice-para meters configureren voor de modules lezer en Writer. Met deze configuratie kunnen de modules van lezer/Writer worden geconfigureerd wanneer de AzureMLBatchExecution-activiteit wordt gebruikt. U geeft de webservice-para meters op in de sectie **globalParameters** van de JSON van de activiteit als volgt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> De para meters van de webservice zijn hoofdletter gevoelig, dus zorg ervoor dat de namen die u opgeeft in de JSON van de activiteit overeenkomen met die van de webservice.

Wanneer u klaar bent met het opnieuw trainen, werkt u de Score-webservice (voorspellende experiment die als een webservice wordt weer gegeven) bij met het nieuwe getrainde model met behulp van de activiteit voor het bijwerken van de **resource van Azure machine learning Studio (klassiek)**. Zie [modellen bijwerken met update resource activiteit](update-machine-learning-models.md) artikel voor meer informatie.

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