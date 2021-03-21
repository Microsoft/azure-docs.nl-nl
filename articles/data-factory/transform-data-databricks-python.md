---
title: Gegevens transformeren met Databricks python
description: Meer informatie over hoe u gegevens verwerkt of transformeert door een Databricks python-activiteit uit te voeren in een Azure Data Factory-pijp lijn.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373943"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Gegevens transformeren door een python-activiteit uit te voeren in Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de activiteit python Azure Databricks in een [Data Factory-pijp lijn](concepts-pipelines-activities.md) wordt een python-bestand in uw Azure Databricks-cluster uitgevoerd. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

Bekijk de volgende video voor een inleiding en demonstratie van deze functie van 11 minuten:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definitie van Databricks python-activiteit

Hier volgt een voor beeld van de JSON-definitie van een Databricks python-activiteit:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Eigenschappen van Databricks python-activiteit

In de volgende tabel worden de JSON-eigenschappen beschreven die in de JSON-definitie worden gebruikt:

|Eigenschap|Beschrijving|Vereist|
|---|---|---|
|naam|De naam van de activiteit in de pijp lijn.|Ja|
|beschrijving|Tekst die beschrijft wat de activiteit doet.|Nee|
|type|Voor Databricks python-activiteit is het type activiteit DatabricksSparkPython.|Ja|
|linkedServiceName|De naam van de gekoppelde Databricks-service waarop de python-activiteit wordt uitgevoerd. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service.|Ja|
|pythonFile|De URI van het python-bestand dat moet worden uitgevoerd. Alleen DBFS-paden worden ondersteund.|Ja|
|parameters|Opdracht regel parameters die worden door gegeven aan het python-bestand. Dit is een matrix met teken reeksen.|Nee|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd waarmee de taak wordt uitgevoerd. Dit kan een matrix zijn met <teken reeks, object>|Nee|

## <a name="supported-libraries-for-databricks-activities"></a>Ondersteunde bibliotheken voor databricks-activiteiten

In de bovenstaande definitie van de Databricks-activiteit geeft u deze typen tape wisselaars op: *jar*, *ei*, *maven*, *pypi*, *krans*.

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

Raadpleeg de Databricks- [documentatie](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) voor bibliotheek typen voor meer informatie.

## <a name="how-to-upload-a-library-in-databricks"></a>Een bibliotheek uploaden in Databricks

### <a name="you-can-use-the-workspace-ui"></a>U kunt de werk ruimte gebruikers interface gebruiken:

1. [De gebruikers interface van de Databricks-werk ruimte gebruiken](/azure/databricks/libraries/#create-a-library)

2. Als u het pad naar de dbfs van de bibliotheek wilt ophalen die via de gebruikers interface is toegevoegd, kunt u [DATABRICKS cli](/azure/databricks/dev-tools/cli/#install-the-cli)gebruiken.

   De jar-bibliotheken worden meestal opgeslagen onder dbfs:/File Store/potten tijdens het gebruik van de gebruikers interface. U kunt alle weer geven via de CLI: *databricks FS ls dbfs:/File Store/job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>U kunt ook de Databricks CLI gebruiken:

1. Volg [de tape wisselaar met DATABRICKS cli](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Databricks CLI gebruiken [(installatie stappen)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Als voor beeld: als u een JAR naar dbfs wilt kopiëren, `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
