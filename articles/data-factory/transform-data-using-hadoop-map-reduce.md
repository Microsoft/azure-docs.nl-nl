---
title: Gegevens transformeren met behulp van Hadoop MapReduce-activiteit
description: Meer informatie over hoe u gegevens kunt verwerken door Hadoop MapReduce-Program ma's uit te voeren op een Azure HDInsight-cluster vanuit een Azure-data factory.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: f03906586d6226c92cfa69e1a139d4c876cbf723
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375881"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van Hadoop MapReduce-activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-map-reduce.md)
> * [Huidige versie](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De HDInsight MapReduce-activiteit in een Data Factory [pijp lijn](concepts-pipelines-activities.md) roept het MapReduce-programma aan op [uw eigen](compute-linked-services.md#azure-hdinsight-linked-service) of HDInsight [-cluster op aanvraag](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten.

Als u niet bekend bent met Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en voert u de zelf studie uit: [zelf studie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat dit artikel wordt gelezen.

Zie [Pig](transform-data-using-hadoop-pig.md) en [Hive](transform-data-using-hadoop-hive.md) voor meer informatie over het uitvoeren van Pig/Hive-scripts in een HDInsight-cluster via een pijp lijn met behulp van HDInsight Pig en Hive-activiteiten.

## <a name="syntax"></a>Syntax

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
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
| naam              | Naam van de activiteit                     | Ja      |
| beschrijving       | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | Nee       |
| type              | Voor MapReduce-activiteit is het type activiteit HDinsightMapReduce | Ja      |
| linkedServiceName | Verwijzing naar het HDInsight-cluster dat is geregistreerd als een gekoppelde service in Data Factory. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Ja      |
| className         | Naam van de klasse die moet worden uitgevoerd         | Ja      |
| jarLinkedService  | Verwijzing naar een Azure Storage gekoppelde service die wordt gebruikt voor het opslaan van de jar-bestanden. Hier worden alleen **[Azure Blob Storage](./connector-azure-blob-storage.md)** -en **[ADLS Gen2](./connector-azure-data-lake-storage.md)** gekoppelde services ondersteund. Als u deze gekoppelde service niet opgeeft, wordt de Azure Storage gekoppelde service gebruikt die is gedefinieerd in de gekoppelde HDInsight-service. | Nee       |
| jarFilePath       | Geef het pad op naar de jar-bestanden die zijn opgeslagen in de Azure Storage waarnaar wordt verwezen door jarLinkedService. De bestands naam is hoofdletter gevoelig. | Ja      |
| jarlibs           | Teken reeks matrix van het pad naar de jar-bibliotheek bestanden waarnaar wordt verwezen door de taak die is opgeslagen in de Azure Storage gedefinieerd in jarLinkedService. De bestands naam is hoofdletter gevoelig. | Nee       |
| getDebugInfo      | Hiermee geeft u op wanneer de logboek bestanden worden gekopieerd naar de Azure Storage gebruikt door het HDInsight-cluster (of) dat is opgegeven door jarLinkedService. Toegestane waarden: geen, altijd of mislukt. Standaard waarde: geen. | Nee       |
| opmerkingen         | Hiermee wordt een matrix met argumenten voor een Hadoop-taak opgegeven. De argumenten worden door gegeven als opdracht regel argumenten voor elke taak. | Nee       |
| compliant           | Geef para meters op als sleutel/waarde-paren voor het verwijzen in het Hive-script. | Nee       |



## <a name="example"></a>Voorbeeld
U kunt de HDInsight MapReduce-activiteit gebruiken om een MapReduce jar-bestand uit te voeren op een HDInsight-cluster. In de volgende JSON-voorbeeld definitie van een pijp lijn is de HDInsight-activiteit geconfigureerd om een mahout JAR-bestand uit te voeren.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
U kunt argumenten opgeven voor het MapReduce-programma in de sectie **Arguments** . Tijdens runtime ziet u enkele extra argumenten (bijvoorbeeld: MapReduce. job. Tags) van het MapReduce-Framework. Als u de argumenten wilt onderscheiden met de MapReduce argumenten, kunt u de optie en waarde als argumenten gebruiken, zoals wordt weer gegeven in het volgende voor beeld (-s,--invoer,--uitvoer etc., zijn de opties direct gevolgd door hun waarden).

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek)](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)