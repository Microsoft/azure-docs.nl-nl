---
title: Gegevens transformeren met behulp van Hadoop streaming-activiteit
description: In dit artikel wordt uitgelegd hoe u Hadoop streaming-activiteit in Azure Data Factory kunt gebruiken om gegevens te transformeren door Hadoop-streaming Program ma's uit te voeren op een Hadoop-cluster.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361431"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Hadoop streaming-activiteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-hadoop-streaming-activity.md)
> * [Huidige versie](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de activiteit HDInsight streaming in een Data Factory [pijp lijn](concepts-pipelines-activities.md) voert u Hadoop streaming-Program ma's uit op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of HDInsight [-cluster op aanvraag](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

Als u geen ervaring hebt met Azure Data Factory, lees dan [Inleiding tot Azure Data Factory](introduction.md) en voer de [volgende zelf studie uit: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

## <a name="json-sample"></a>JSON-voor beeld
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Syntaxis Details

| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| naam              | Naam van de activiteit                     | Yes      |
| beschrijving       | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | No       |
| type              | Voor Hadoop streaming-activiteit is het type activiteit HDInsightStreaming | Yes      |
| linkedServiceName | Verwijzing naar het HDInsight-cluster dat is geregistreerd als een gekoppelde service in Data Factory. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Yes      |
| toewijzing            | Hiermee geeft u de naam van het uitvoer bare toewijzings programma | Yes      |
| reductier           | Hiermee geeft u de naam van het uitvoer bare bestand voor verkleining | Yes      |
| combinatie          | Hiermee geeft u de naam van het uitvoer bare bestand van de combi natie | No       |
| fileLinkedService | Verwijzing naar een Azure Storage gekoppelde service die wordt gebruikt voor het opslaan van de Mapper-, combi natie-en Reduceerere Program ma's die moeten worden uitgevoerd. Hier worden alleen **[Azure Blob Storage](./connector-azure-blob-storage.md)** -en **[ADLS Gen2](./connector-azure-data-lake-storage.md)** gekoppelde services ondersteund. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage gekoppelde service gebruikt die is gedefinieerd in de gekoppelde HDInsight-service. | No       |
| Bestandspad          | Geef een matrix met het pad op naar de toewijzings programma voor Mapper, combi natie en verminderen die zijn opgeslagen in de Azure Storage waarnaar wordt verwezen door fileLinkedService. Het pad is hoofdlettergevoelig. | Yes      |
| input             | Hiermee geeft u het pad naar het WASB naar het invoer bestand voor de Mapper. | Yes      |
| output            | Hiermee geeft u het pad naar de WASB naar het uitvoer bestand voor de verminderr. | Yes      |
| getDebugInfo      | Hiermee geeft u op wanneer de logboek bestanden worden gekopieerd naar de Azure Storage gebruikt door het HDInsight-cluster (of) dat is opgegeven door scriptLinkedService. Toegestane waarden: geen, altijd of mislukt. Standaard waarde: geen. | No       |
| opmerkingen         | Hiermee wordt een matrix met argumenten voor een Hadoop-taak opgegeven. De argumenten worden door gegeven als opdracht regel argumenten voor elke taak. | No       |
| compliant           | Geef para meters op als sleutel/waarde-paren voor het verwijzen in het Hive-script. | Nee       | 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek)](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)