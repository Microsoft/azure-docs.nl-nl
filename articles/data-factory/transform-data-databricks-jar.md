---
title: Gegevens transformeren met Databricks jar
description: Meer informatie over het verwerken of transformeren van gegevens door een Databricks jar uit te voeren binnen een Azure Data Factory-pijp lijn.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374011"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Gegevens transformeren door een jar-activiteit uit te voeren in Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De Azure Databricks jar-activiteit in een [Data Factory pijp lijn](concepts-pipelines-activities.md) voert een Spark jar in uw Azure Databricks-cluster uit. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens transformatie](transform-data.md) , dat een algemene informatie bevat over de gegevens transformatie en de ondersteunde transformatie activiteiten. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

Bekijk de volgende video voor een inleiding en demonstratie van deze functie van 11 minuten:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definitie van Databricks jar-activiteit

Hier volgt een voor beeld van de JSON-definitie van een Databricks jar-activiteit:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Eigenschappen van Databricks jar-activiteit

In de volgende tabel worden de JSON-eigenschappen beschreven die in de JSON-definitie worden gebruikt:

|Eigenschap|Beschrijving|Vereist|
|:--|---|:-:|
|naam|De naam van de activiteit in de pijp lijn.|Yes|
|beschrijving|Tekst die beschrijft wat de activiteit doet.|No|
|type|Voor Databricks jar-activiteit is het type activiteit DatabricksSparkJar.|Yes|
|linkedServiceName|De naam van de gekoppelde Databricks-service waarop de jar-activiteit wordt uitgevoerd. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service.|Yes|
|mainClassName|De volledige naam van de klasse die de hoofd methode bevat die moet worden uitgevoerd. Deze klasse moet zich bevinden in een JAR die als bibliotheek is opgenomen. Een JAR-bestand kan meerdere klassen bevatten. Elk van de klassen kan een hoofd methode bevatten.|Yes|
|parameters|Para meters die worden door gegeven aan de methode Main. Deze eigenschap is een matrix met teken reeksen.|No|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd waarmee de taak wordt uitgevoerd. Dit kan een matrix zijn met <teken reeks, object>|Ja (ten minste één met de methode mainClassName)|

> [!NOTE]
> **Bekend probleem** : wanneer u hetzelfde [interactieve cluster](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) gebruikt voor het uitvoeren van gelijktijdige Databricks jar-activiteiten (zonder dat het cluster opnieuw hoeft te worden opgestart), is er een bekend probleem in Databricks waar in de para meters van de eerste activiteit ook door de volgende activiteiten wordt gebruikt. Daarom ontstaan er onjuiste para meters die worden door gegeven aan de volgende taken. Als u dit wilt beperken, moet u in plaats daarvan een [taak cluster](compute-linked-services.md#example---using-new-job-cluster-in-databricks) gebruiken.

## <a name="supported-libraries-for-databricks-activities"></a>Ondersteunde bibliotheken voor databricks-activiteiten

In de vorige definitie van de Databricks-activiteit hebt u deze typen bibliotheek opgegeven: `jar` , `egg` , `maven` , `pypi` , `cran` .

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

Zie de [Databricks-documentatie](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) voor bibliotheek typen voor meer informatie.

## <a name="how-to-upload-a-library-in-databricks"></a>Een bibliotheek uploaden in Databricks

### <a name="you-can-use-the-workspace-ui"></a>U kunt de werk ruimte gebruikers interface gebruiken:

1. [De gebruikers interface van de Databricks-werk ruimte gebruiken](/azure/databricks/libraries/#create-a-library)

2. Als u het pad naar de dbfs van de bibliotheek wilt ophalen die via de gebruikers interface is toegevoegd, kunt u [DATABRICKS cli](/azure/databricks/dev-tools/cli/#install-the-cli)gebruiken.

   De jar-bibliotheken worden meestal opgeslagen onder dbfs:/File Store/potten tijdens het gebruik van de gebruikers interface. U kunt alle weer geven via de CLI: *databricks FS ls dbfs:/File Store/job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>U kunt ook de Databricks CLI gebruiken:

1. Volg [de tape wisselaar met DATABRICKS cli](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Databricks CLI gebruiken [(installatie stappen)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Als voor beeld: als u een JAR naar dbfs wilt kopiëren, `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Volgende stappen

Bekijk de [video](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player)voor een inleiding en demonstratie van 11 minuten.
