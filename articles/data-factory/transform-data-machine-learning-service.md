---
title: Azure Machine Learning pijp lijnen uitvoeren
description: Meer informatie over het uitvoeren van uw Azure Machine Learning-pijp lijnen in uw Azure Data Factory pijp lijnen.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.date: 07/16/2020
ms.openlocfilehash: db1816baf0ebc476a132b344d8db3cdbdd170e50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373926"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Machine Learning pijp lijnen uitvoeren in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Voer uw Azure Machine Learning-pijp lijnen uit als een stap in uw Azure Data Factory pijp lijnen. Met de activiteit pijp lijn uitvoeren Machine Learning kunt u batch Voorspellings scenario's maken, zoals het identificeren van mogelijke standaard waarden voor leningen, het bepalen van sentiment en het analyseren van gedrags patronen van klanten.

De onderstaande video bevat een inleiding en demonstratie van zes minuten voor deze functie.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntax

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
naam | Naam van de activiteit in de pijp lijn | Tekenreeks | Ja
type | Type activiteit is ' AzureMLExecutePipeline ' | Tekenreeks | Ja
linkedServiceName | Gekoppelde service aan Azure Machine Learning | Verwijzing naar gekoppelde service | Ja
mlPipelineId | ID van de gepubliceerde Azure Machine Learning pijp lijn | Teken reeks (of expressie met het resultType van de teken reeks) | Ja
experimentnaam | Naam van het experiment met de uitvoerings geschiedenis van de Machine Learning pijplijn uitvoering | Teken reeks (of expressie met het resultType van de teken reeks) | Nee
mlPipelineParameters | Sleutel-, waardeparen die moeten worden door gegeven aan het gepubliceerde eind punt van Azure Machine Learning pijplijn. Sleutels moeten overeenkomen met de namen van de pijplijn parameters die in de gepubliceerde Machine Learning pijp lijn zijn gedefinieerd | Object met sleutel waarde-paren (of expressie met het resultType-object) | Nee
mlParentRunId | De ID van de bovenliggende Azure Machine Learning pijplijn uitvoering | Teken reeks (of expressie met het resultType van de teken reeks) | Nee
continueOnStepFailure | Of de uitvoering van andere stappen in de Machine Learning pijplijn uitvoering moet worden voortgezet als een stap is mislukt | booleaans | Nee

> [!NOTE]
> Als u de vervolg keuzelijst wilt vullen met Machine Learning pijplijn naam en-ID, moet de gebruiker gemachtigd zijn om een lijst van ML-pijp lijnen te vermelden. ADF UX roept AzureMLService Api's rechtstreeks aan met de referenties van de aangemelde gebruiker.  

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert:

* [Activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md)
* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
