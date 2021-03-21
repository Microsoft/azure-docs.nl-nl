---
title: Gegevens transformeren met Databricks notebook
description: Meer informatie over het verwerken of transformeren van gegevens door een Databricks-notebook uit te voeren in Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: fea572c2e75f62b5e7e7b4634e37da348bdcdaf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183485"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Gegevens transformeren door een Databricks-notebook uit te voeren
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de activiteit Azure Databricks notebook in een [Data Factory pijp lijn](concepts-pipelines-activities.md) wordt een Databricks-notebook in uw Azure Databricks-werk ruimte uitgevoerd. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definitie van Databricks-notebook-activiteit

Hier volgt een voor beeld van de JSON-definitie van een Databricks-notebook activiteit:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Eigenschappen van Databricks notebook-activiteit

In de volgende tabel worden de JSON-eigenschappen beschreven die in de JSON-definitie worden gebruikt:

|Eigenschap|Beschrijving|Vereist|
|---|---|---|
|naam|De naam van de activiteit in de pijp lijn.|Ja|
|beschrijving|Tekst die beschrijft wat de activiteit doet.|Nee|
|type|Voor Databricks notebook-activiteit is het type activiteit DatabricksNotebook.|Ja|
|linkedServiceName|De naam van de gekoppelde Databricks-service waarop de Databricks-notebook wordt uitgevoerd. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service.|Ja|
|notebookPath|Het absolute pad van de notebook dat moet worden uitgevoerd in de Databricks-werk ruimte. Dit pad moet beginnen met een slash.|Ja|
|baseParameters|Een matrix met Key-Value paren. Basis parameters kunnen worden gebruikt voor elke uitvoering van de activiteit. Als het notitie blok een para meter accepteert die niet is opgegeven, wordt de standaard waarde van het notitie blok gebruikt. Meer informatie over para meters in [Databricks-notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nee|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd waarmee de taak wordt uitgevoerd. Dit kan een matrix van zijn \<string, object> .|Nee|

## <a name="supported-libraries-for-databricks-activities"></a>Ondersteunde bibliotheken voor Databricks-activiteiten

In de bovenstaande definitie van de Databricks-activiteit geeft u deze typen tape wisselaars op: *jar*, *ei*, *WHL*, *maven*, *pypi*, *krans*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Zie de [Databricks-documentatie](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) voor bibliotheek typen voor meer informatie.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Para meters door geven tussen notitie blokken en Data Factory

U kunt data factory-para meters door geven aan notebooks met behulp van de eigenschap *baseParameters* in databricks-activiteit.

In bepaalde gevallen kan het nodig zijn om bepaalde waarden van het notitie blok terug te geven aan data factory, dat kan worden gebruikt voor controle stroom (voorwaardelijke controles) in data factory of worden verbruikt door downstream-activiteiten (grootte limiet is 2 MB).

1. In uw notitie blok kunt u [dbutils. notebook. Exit ("returnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) aanroepen en de bijbehorende ' returnValue ' wordt geretourneerd aan Data Factory.

2. U kunt de uitvoer in data factory gebruiken door gebruik te maken van een expressie zoals `@{activity('databricks notebook activity name').output.runOutput}` . 

   > [!IMPORTANT]
   > Als u een JSON-object doorgeeft, kunt u waarden ophalen door de namen van eigenschappen toe te voegen. Voorbeeld: `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Een bibliotheek uploaden in Databricks

### <a name="you-can-use-the-workspace-ui"></a>U kunt de werk ruimte gebruikers interface gebruiken:

1. [De gebruikers interface van de Databricks-werk ruimte gebruiken](/azure/databricks/libraries/#create-a-library)

2. Als u het pad naar de dbfs van de bibliotheek wilt ophalen die via de gebruikers interface is toegevoegd, kunt u [DATABRICKS cli](/azure/databricks/dev-tools/cli/#install-the-cli)gebruiken.

   De jar-bibliotheken worden meestal opgeslagen onder dbfs:/File Store/potten tijdens het gebruik van de gebruikers interface. U kunt alle weer geven via de CLI: *databricks FS ls dbfs:/File Store/job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>U kunt ook de Databricks CLI gebruiken:

1. Volg [de tape wisselaar met DATABRICKS cli](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Databricks CLI gebruiken [(installatie stappen)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Als voor beeld: als u een JAR naar dbfs wilt kopiëren, `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
