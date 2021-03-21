---
title: Gegevens transformeren met U-SQL-script-Azure
description: Meer informatie over het verwerken of transformeren van gegevens door U-SQL-scripts uit te voeren op Azure Data Lake Analytics compute-service-versie 1.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 5931cb28721e8658a771ceea1cd94624a0c09f7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392915"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Gegevens transformeren door U-SQL-scripts uit te voeren in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-usql-activity.md)
> * [Versie 2 (huidige versie)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt [u-SQL-activiteit in v2](../transform-data-using-data-lake-analytics.md).

Met een pijp lijn in een Azure-data factory worden gegevens verwerkt in gekoppelde opslag Services met behulp van gekoppelde Compute-Services. Het bevat een reeks activiteiten waarbij elke activiteit een specifieke verwerkings bewerking uitvoert. In dit artikel wordt de **Data Lake Analytics u-SQL-activiteit** beschreven waarmee een **U-SQL** -script wordt uitgevoerd op een gekoppelde service van **Azure data Lake Analytics** compute. 

Maak een Azure Data Lake Analytics-account voordat U een pijp lijn maakt met een Data Lake Analytics U-SQL-activiteit. Zie aan de [slag met Azure data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)voor meer informatie over Azure data Lake Analytics.

Bekijk de [zelf studie uw eerste pijp lijn bouwen](data-factory-build-your-first-pipeline.md) voor gedetailleerde stappen voor het maken van een Data Factory, gekoppelde services, gegevens sets en een pijp lijn. Gebruik JSON-fragmenten met Data Factory editor of Visual Studio of Azure PowerShell om Data Factory entiteiten te maken.

## <a name="supported-authentication-types"></a>Ondersteunde verificatie typen
U-SQL-activiteit ondersteunt onder verificatie typen voor Data Lake Analytics:
* Verificatie van service-principal
* Verificatie van gebruikers referenties (OAuth) 

We raden u aan Service-Principal-verificatie te gebruiken, met name voor een geplande U-SQL-uitvoering. Het verloop gedrag van tokens kan optreden bij verificatie van de gebruikers referenties. Zie de sectie eigenschappen van de [gekoppelde service](#azure-data-lake-analytics-linked-service) voor meer informatie over de configuratie.

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics gekoppelde service
U maakt een **Azure data Lake Analytics** gekoppelde service om een Azure data Lake Analytics compute-service aan een Azure Data Factory te koppelen. De Data Lake Analytics U-SQL-activiteit in de pijp lijn verwijst naar deze gekoppelde service. 

De volgende tabel bevat beschrijvingen van de algemene eigenschappen die in de JSON-definitie worden gebruikt. U kunt kiezen tussen Service-Principal en verificatie van de gebruikers referenties.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **type** |De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. |Ja |
| **accountName** |Azure Data Lake Analytics account naam. |Ja |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics-URI. |Nee |
| **Abonnements** |Azure-abonnements-id |Nee (als dit niet wordt opgegeven, wordt het abonnement van de data factory gebruikt). |
| **resourceGroupName** |Naam van Azure-resourcegroep |Nee (als dit niet is opgegeven, wordt de resource groep van de data factory gebruikt). |

### <a name="service-principal-authentication-recommended"></a>Service-Principal-verificatie (aanbevolen)
Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en verleent u deze toegang tot Data Lake Store. Zie [service-to-service-verificatie](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:
* Toepassings-id
* Toepassings sleutel 
* Tenant-id

Gebruik Service-Principal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **servicePrincipalId** | Geef de client-ID van de toepassing op. | Ja |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op. | Ja |
| **tenant** | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |

**Voor beeld: Service-Principal-verificatie**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Verificatie van gebruikers referenties
U kunt ook verificatie van de gebruikers referenties voor Data Lake Analytics gebruiken door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **autorisatie** | Klik op de knop **machtigen** in de Data Factory editor en voer uw referenties in die de automatisch gegenereerde autorisatie-URL aan deze eigenschap toewijzen. | Ja |
| **sessionId** | OAuth-sessie-ID van de OAuth-autorisatie sessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u de Data Factory editor gebruikt. | Ja |

**Voor beeld: verificatie van de gebruikers referenties**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token verloop tijd
De autorisatie code die u hebt gegenereerd met behulp van de knop **autorisatie** verloopt na enige tijd. Raadpleeg de volgende tabel voor de verloop tijden voor verschillende soorten gebruikers accounts. Mogelijk wordt het volgende fout bericht weer gegeven wanneer het verificatie **token verloopt**: referentie bewerkings fout: INVALID_GRANT-AADSTS70002: fout bij het valideren van referenties. AADSTS70008: de verleende toegangs toekenning is verlopen of ingetrokken. Tracerings-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 correlatie-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7-tijds tempel: 2015-12-15 21:09:31Z

| Gebruikers type | Verloopt na |
|:--- |:--- |
| Gebruikers accounts die niet worden beheerd door Azure Active Directory ( @hotmail.com , @live.com enz.) |12 uur |
| Gebruikers accounts die worden beheerd door Azure Active Directory (AAD) |14 dagen na de laatste uitvoering van het segment. <br/><br/>90 dagen, als een segment op basis van op OAuth gebaseerde gekoppelde service ten minste één keer per 14 dagen wordt uitgevoerd. |

Als u deze fout wilt voor komen of oplossen, autoriseert u de knop **autoriseren** wanneer het **token verloopt** en implementeert u de gekoppelde service opnieuw. U kunt ook met behulp van code op een programmatische manier waarden genereren voor **SessionID** -en **autorisatie** -eigenschappen:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Zie de onderwerpen [AzureDataLakeStoreLinkedService class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)en [AuthorizationSessionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) voor meer informatie over de Data Factory klassen die in de code worden gebruikt. Voeg een verwijzing toe naar: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll voor de klasse WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
Het volgende JSON-code fragment definieert een pijp lijn met een Data Lake Analytics U-SQL-activiteit. De definitie van de activiteit bevat een verwijzing naar de Azure Data Lake Analytics gekoppelde service die u eerder hebt gemaakt.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

In de volgende tabel worden namen en beschrijvingen van eigenschappen beschreven die specifiek zijn voor deze activiteit. 

| Eigenschap            | Beschrijving                              | Vereist                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | De eigenschap type moet worden ingesteld op **DataLakeAnalyticsU-SQL**. | Ja                                      |
| linkedServiceName   | Verwijzing naar de Azure Data Lake Analytics geregistreerd als een gekoppelde service in Data Factory | Ja                                      |
| scriptPath          | Pad naar de map die het U-SQL-script bevat. De naam van het bestand is hoofdletter gevoelig. | Nee (als u script gebruikt)                   |
| scriptLinkedService | Gekoppelde service die de opslag met het script koppelt aan de data factory | Nee (als u script gebruikt)                   |
| script              | Geef inline-script op in plaats van scriptPath en scriptLinkedService op te geven. Bijvoorbeeld: `"script": "CREATE DATABASE test"`. | Nee (als u scriptPath en scriptLinkedService gebruikt) |
| degreeOfParallelism | Het maximum aantal knoop punten dat tegelijkertijd wordt gebruikt om de taak uit te voeren. | Nee                                       |
| priority            | Hiermee wordt bepaald welke taken uit de wachtrij moeten worden geselecteerd om eerst te worden uitgevoerd. Hoe lager het getal, des te hoger de prioriteit. | Nee                                       |
| parameters          | Para meters voor het U-SQL-script          | Nee                                       |
| runtimeVersion      | Te gebruiken runtime versie van de U-SQL-engine | Nee                                       |
| compilationMode     | <p>De compilatie modus van U-SQL. Moet een van de volgende waarden zijn:</p> <ul><li>**Semantisch:** Voer alleen semantische controles en benodigde Sanity controles uit.</li><li>**Volledig:** Voer de volledige compilatie uit, inclusief syntaxis controle, optimalisatie, het genereren van code, enzovoort.</li><li>**SingleBox:** Voer de volledige compilatie uit met de instelling target type ingesteld op SingleBox.</li></ul><p>Als u geen waarde opgeeft voor deze eigenschap, bepaalt de server de optimale compilatie modus. </p> | Nee                                       |

Zie [SearchLogProcessing.txt script definitie](#sample-u-sql-script) voor de script definitie. 

## <a name="sample-input-and-output-datasets"></a>Voor beelden van invoer-en uitvoer gegevens sets
### <a name="input-dataset"></a>Invoer gegevensset
In dit voor beeld bevinden de invoer gegevens zich in een Azure Data Lake Store (SearchLog. TSV-bestand in de datalake/input-map). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Uitvoer gegevensset
In dit voor beeld worden de uitvoer gegevens die zijn geproduceerd door het U-SQL-script opgeslagen in een Azure Data Lake Store (map datalake/uitvoermap). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Voor beeld Data Lake Store gekoppelde service
Hier volgt de definitie van het voor beeld Azure Data Lake Store gekoppelde service die wordt gebruikt door de invoer-en uitvoer gegevens sets. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Zie [gegevens verplaatsen van en naar Azure data Lake Store](data-factory-azure-datalake-connector.md) artikel voor beschrijvingen van JSON-eigenschappen. 

## <a name="sample-u-sql-script"></a>Voor beeld van een U-SQL-script

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
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

De waarden voor de para meters **\@ in** en **\@ uit** in het U-SQL-script worden dynamisch door ADF door gegeven met behulp van de sectie para meters. Zie de sectie ' para meters ' in de definitie van de pijp lijn.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

In dit geval worden er nog steeds invoer bestanden uit de map/datalake/input verzameld en worden er uitvoer bestanden gegenereerd in de map/datalake/output. De bestands namen zijn dynamisch op basis van de begin tijd van het segment.