---
title: Gegevens transformeren met U-SQL-script
description: Meer informatie over het verwerken of transformeren van gegevens door U-SQL-scripts uit te voeren op Azure Data Lake Analytics compute-service.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 94b584f0bea01754a4e955d1418a7a9a2e9aeace
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373909"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Gegevens verwerken door U-SQL-scripts uit te voeren op Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-usql-activity.md)
> * [Huidige versie](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met een pijp lijn in een Azure-data factory worden gegevens verwerkt in gekoppelde opslag Services met behulp van gekoppelde Compute-Services. Het bevat een reeks activiteiten waarbij elke activiteit een specifieke verwerkings bewerking uitvoert. In dit artikel wordt de **Data Lake Analytics u-SQL-activiteit** beschreven waarmee een **U-SQL** -script wordt uitgevoerd op een gekoppelde service van **Azure data Lake Analytics** compute. 

Maak een Azure Data Lake Analytics-account voordat U een pijp lijn maakt met een Data Lake Analytics U-SQL-activiteit. Zie aan de [slag met Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)voor meer informatie over Azure data Lake Analytics.


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics gekoppelde service
U maakt een **Azure data Lake Analytics** gekoppelde service om een Azure data Lake Analytics compute-service aan een Azure Data Factory te koppelen. De Data Lake Analytics U-SQL-activiteit in de pijp lijn verwijst naar deze gekoppelde service. 

De volgende tabel bevat beschrijvingen van de algemene eigenschappen die in de JSON-definitie worden gebruikt. 

| Eigenschap                 | Beschrijving                              | Vereist                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. | Ja                                      |
| **accountName**          | Azure Data Lake Analytics account naam.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics-URI.           | Nee                                       |
| **Abonnements**       | Azure-abonnements-ID                    | Nee                                       |
| **resourceGroupName**    | Naam van Azure-resourcegroep                | Nee                                       |

### <a name="service-principal-authentication"></a>Verificatie van service-principal
Voor de Azure Data Lake Analytics gekoppelde service is een Service-Principal-verificatie vereist om verbinding te maken met de Azure Data Lake Analytics-service. Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en verleent u deze toegang tot zowel de Data Lake Analytics als de Data Lake Store die wordt gebruikt. Zie [service-to-service-verificatie](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

* Toepassings-id
* Toepassings sleutel 
* Tenant-id

Ken Service-Principal machtigingen toe aan uw Azure Data Lake Anatlyics met behulp van de [wizard gebruiker toevoegen](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Gebruik Service-Principal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Vereist |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geef de client-ID van de toepassing op.     | Ja      |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op.           | Ja      |
| **tenant**              | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja      |

**Voor beeld: Service-Principal-verificatie**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Zie [gekoppelde services berekenen](compute-linked-services.md)voor meer informatie over de gekoppelde service.

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
Het volgende JSON-code fragment definieert een pijp lijn met een Data Lake Analytics U-SQL-activiteit. De definitie van de activiteit bevat een verwijzing naar de Azure Data Lake Analytics gekoppelde service die u eerder hebt gemaakt. Als u een Data Lake Analytics U-SQL-script wilt uitvoeren, Data Factory verzendt u het script dat u hebt opgegeven voor de Data Lake Analytics en worden de vereiste invoer en uitvoer in het script gedefinieerd voor Data Lake Analytics om op te halen en uit te voeren. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

In de volgende tabel worden namen en beschrijvingen van eigenschappen beschreven die specifiek zijn voor deze activiteit. 

| Eigenschap            | Beschrijving                              | Vereist |
| :------------------ | :--------------------------------------- | :------- |
| naam                | Naam van de activiteit in de pijp lijn     | Ja      |
| beschrijving         | Tekst die beschrijft wat de activiteit doet.  | Nee       |
| type                | Voor Data Lake Analytics U-SQL-activiteit is het type activiteit  **DataLakeAnalyticsU-SQL**. | Ja      |
| linkedServiceName   | Gekoppelde service aan Azure Data Lake Analytics. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service.  |Ja       |
| scriptPath          | Pad naar de map die het U-SQL-script bevat. De naam van het bestand is hoofdletter gevoelig. | Ja      |
| scriptLinkedService | Gekoppelde service waarmee de **Azure data Lake Store** of **Azure Storage** die het script bevat, wordt gekoppeld aan de Data Factory | Ja      |
| degreeOfParallelism | Het maximum aantal knoop punten dat tegelijkertijd wordt gebruikt om de taak uit te voeren. | Nee       |
| priority            | Hiermee wordt bepaald welke taken uit de wachtrij moeten worden geselecteerd om eerst te worden uitgevoerd. Hoe lager het getal, des te hoger de prioriteit. | Nee       |
| parameters          | Para meters die moeten worden door gegeven aan het U-SQL-script.    | Nee       |
| runtimeVersion      | Runtime versie van de U-SQL-engine die moet worden gebruikt. | Nee       |
| compilationMode     | <p>De compilatie modus van U-SQL. Moet een van de volgende waarden zijn: **semantisch:** alleen semantische controles en nood zakelijke Sanity controles uitvoeren, **volledig:** de volledige compilatie uitvoeren, met inbegrip van de syntaxis controle, optimalisatie, het genereren van code, enzovoort, **SingleBox:** Voer de volledige compilatie uit met de instelling target type in op SingleBox. Als u geen waarde opgeeft voor deze eigenschap, bepaalt de server de optimale compilatie modus. | Nee |

Zie [SearchLogProcessing.txt](#sample-u-sql-script) voor de script definitie. 

## <a name="sample-u-sql-script"></a>Voor beeld van een U-SQL-script

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

In het voor beeld van een script wordt de invoer en uitvoer naar het script gedefinieerd in de para meters **\@ in** en **\@ uit** . De waarden voor de para meters **\@ in** en **\@ uit** in het U-SQL-script worden dynamisch door gegeven door Data Factory met behulp van de sectie para meters. 

U kunt ook andere eigenschappen, zoals degreeOfParallelism en prioriteit, opgeven in de pijplijn definitie voor de taken die worden uitgevoerd op de Azure Data Lake Analytics-service.

## <a name="dynamic-parameters"></a>Dynamische parameters
In de definitie van de voorbeeld pijplijn worden in-en uitpara meters toegewezen met in code vastgelegde waarden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

U kunt in plaats daarvan dynamische para meters gebruiken. Bijvoorbeeld: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

In dit geval worden er nog steeds invoer bestanden uit de map/datalake/input verzameld en worden er uitvoer bestanden gegenereerd in de map/datalake/output. De bestands namen zijn dynamisch op basis van de start tijd van het venster die wordt door gegeven wanneer de pijp lijn wordt geactiveerd.  

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert: 

* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek)](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)