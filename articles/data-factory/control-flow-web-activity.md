---
title: Webactiviteit in Azure Data Factory
description: Meer informatie over het gebruik van webactiviteit, een van de controle stroom activiteiten die door Data Factory worden ondersteund om een REST-eind punt vanuit een pijp lijn aan te roepen.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: e4578b41e5cbb62c8a1bfa0c48d4fd60d042a506
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361516"
---
# <a name="web-activity-in-azure-data-factory"></a>Webactiviteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


De WebActivity kan worden gebruikt om een aangepast REST-eindpunt aan te roepen vanaf een Data Factory-pijplijn. U kunt gegevenssets en gekoppelde services doorgeven die moten worden verbruikt door en die toegankelijk zijn voor de activiteit.

> [!NOTE]
> Webactiviteit wordt ondersteund voor het aanroepen van URL's die worden gehost in een virtueel privénetwerk, door gebruik te maken van zelf-hostende integratieruntime. De integratieruntime moet het URL-eindpunt kunnen bereiken. 

> [!NOTE]
> De Maxi maal ondersteunde Payload-grootte voor uitvoer antwoorden is 4 MB.  

## <a name="syntax"></a>Syntax

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
naam | De naam van de Web-activiteit | Tekenreeks | Ja
type | Moet worden ingesteld op **webactiviteit**. | Tekenreeks | Ja
method | Rest API-methode voor het doel eindpunt. | Tekenreeks. <br/><br/>Ondersteunde typen: ' GET ', ' POST ', ' PUT ' | Yes
url | Doel eindpunt en-pad | Teken reeks (of expressie met het resultType van de teken reeks). Voor de activiteit wordt een time-out van één minuut met een fout weergegeven als er geen respons van het eindpunt wordt ontvangen. | Yes
koppen | Kopteksten die naar de aanvraag worden verzonden. U kunt bijvoorbeeld de taal en het type van een aanvraag instellen: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Teken reeks (of expressie met het resultType van de teken reeks) | Ja, content-type-header is vereist. `"headers":{ "Content-Type":"application/json"}`
body | Vertegenwoordigt de nettolading die naar het eind punt wordt verzonden.  | Teken reeks (of expressie met het resultType van de teken reeks). <br/><br/>Zie het schema van de sectie aanvraag lading in schema voor de lading van de [aanvraag](#request-payload-schema) . | Vereist voor POST/PUT-methoden.
verificatie | De verificatie methode die wordt gebruikt voor het aanroepen van het eind punt. De ondersteunde typen zijn Basic of ClientCertificate. Zie de sectie [verificatie](#authentication) voor meer informatie. Als verificatie niet is vereist, sluit u deze eigenschap. | Teken reeks (of expressie met het resultType van de teken reeks) | No
gegevenssets | Lijst met gegevens sets die zijn door gegeven aan het eind punt. | Matrix van gegevensset-verwijzingen. Dit kan een lege matrix zijn. | Yes
linkedServices | Lijst met gekoppelde services die zijn door gegeven aan het eind punt. | Matrix van gekoppelde service verwijzingen. Dit kan een lege matrix zijn. | Yes
connectVia | De [Integration runtime](./concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of de zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als deze eigenschap niet is opgegeven, gebruikt de service de standaard Azure Integration runtime. | De naslag informatie voor Integration runtime. | No 

> [!NOTE]
> REST-eindpunten die door de webactiviteit worden aangeroepen, moeten een respons van het type JSON retourneren. Voor de activiteit wordt een time-out van één minuut met een fout weergegeven als er geen respons van het eindpunt wordt ontvangen.

De volgende tabel bevat de vereisten voor JSON-inhoud:

| Waardetype | Aanvraagbody | Hoofdtekst van de reactie |
|---|---|---|
|JSON-object | Ondersteund | Ondersteund |
|JSON-matrix | Ondersteund <br/>(Momenteel werken JSON-matrices niet als gevolg van een fout. Er wordt een correctie uitgevoerd.) | Niet ondersteund |
| JSON-waarde | Ondersteund | Niet ondersteund |
| Niet-JSON-type | Niet ondersteund | Niet ondersteund |
||||

## <a name="authentication"></a>Verificatie

Hieronder vindt u de ondersteunde verificatie typen in de webactiviteit.

### <a name="none"></a>Geen

Als verificatie niet is vereist, moet u de eigenschap Authentication niet toevoegen.

### <a name="basic"></a>Basic

Geef de gebruikers naam en het wacht woord op die moeten worden gebruikt met de basis verificatie.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Client certificaat

Met base64 gecodeerde inhoud van een PFX-bestand en het wacht woord opgeven.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Beheerde identiteit

Geef de bron-URI op waarvoor het toegangs token wordt aangevraagd met behulp van de beheerde identiteit voor de data factory. Gebruik om de Azure Resource Management-API aan te roepen `https://management.azure.com/` . Zie de [pagina beheerde identiteiten voor Azure-resources Overview](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over hoe beheerde identiteiten werken.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Als uw data factory is geconfigureerd met een Git-opslag plaats, moet u uw referenties in Azure Key Vault opslaan om basis-of verificatie van client certificaten te gebruiken. Azure Data Factory slaat geen wacht woorden op in Git.

## <a name="request-payload-schema"></a>Payload-schema aanvragen
Wanneer u de POST/PUT-methode gebruikt, vertegenwoordigt de eigenschap Body de payload die naar het eind punt wordt verzonden. U kunt gekoppelde services en gegevens sets door geven als onderdeel van de payload. Dit is het schema voor de payload:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Voorbeeld
In dit voor beeld roept de Web-activiteit in de pijp lijn een REST-eind punt aan. Er wordt een gekoppelde Azure SQL-service en een Azure SQL-gegevensset aan het eind punt door gegeven. Het REST-eind punt maakt gebruik van Azure SQL connection string om verbinding te maken met de logische SQL-Server en retourneert de naam van het exemplaar van SQL Server.

### <a name="pipeline-definition"></a>Pijplijn definitie

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Waarden van pijplijn parameters

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Eindpunt code van webservice

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund:

- [Activiteit uitvoeren van pijplijn](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
