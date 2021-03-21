---
title: Gegevensfeeds van verschillende bronnen toevoegen aan metrische gegevens adviseur
titleSuffix: Azure Cognitive Services
description: verschillende gegevensfeeds toevoegen aan metrische gegevens Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348616"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Gegevensfeeds van verschillende gegevens bronnen toevoegen aan Metrics Advisor

In dit artikel vindt u informatie over de instellingen en vereisten voor het verbinden van verschillende typen gegevens bronnen met metrische Advisor. Lees hoe u [uw gegevens kunt voorbereiden](how-tos/onboard-your-data.md) voor meer informatie over de belangrijkste concepten voor het gebruik van uw gegevens met metrische Advisor. 

## <a name="supported-authentication-types"></a>Ondersteunde verificatie typen

| Verificatietypen | Beschrijving |
| ---------------------|-------------|
|**Basic** | U moet de algemene para meters voor toegang tot gegevens bronnen kunnen opgeven. Bijvoorbeeld een connection string of-sleutel. Beheerders van gegevens invoer kunnen deze referenties weer geven. |
| **AzureManagedIdentity** | [Beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor Azure-resources is een functie van Azure Active Directory. Het biedt Azure-Services met een automatisch beheerde identiteit in azure AD. U kunt de identiteit gebruiken om te verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie.|
| **AzureSQLConnectionString**| Sla uw SQL-connection string als een **referentie-entiteit** op in de data Advisor en gebruik deze direct telkens wanneer de metrische gegevens voor onboarding worden uitgevoerd. Alleen beheerders van de referentie-entiteit kunnen deze referenties weer geven, maar kunnen geautoriseerde kijkers gegevens feeds maken zonder dat ze meer informatie over de referenties hoeven te weten. |
| **DataLakeGen2SharedKey**| Sla uw data Lake-account sleutel als een **referentie-entiteit** op in Metrics Advisor en gebruik deze direct telkens wanneer de metrische gegevens voor onboarding worden uitgevoerd. Alleen beheerders van de referentie-entiteit kunnen deze referenties weer geven, maar kunnen geautoriseerde viewers een gegevensfeed maken zonder dat ze de referentie gegevens hoeven te kennen.|
| **Service-principal**| Sla uw service-principal op als **referentie-entiteit** in Metrics Advisor en gebruik deze direct telkens wanneer de metrische gegevens voor onboarding worden uitgevoerd. Alleen beheerders van de referentie-entiteit kunnen de referenties weer geven, maar kunnen geautoriseerde viewers een gegevensfeed maken zonder dat ze de referentie gegevens hoeven te kennen.|
| **Service-Principal van sleutel kluis**|Sla uw service-principal op in een sleutel kluis als **referentie-entiteit** in Metrics Advisor en gebruik deze direct telkens wanneer u de metrische gegevens voor de onboarding oplaadt. Alleen beheerders van een **referentie-entiteit** kunnen de referenties weer geven, maar u kunt ook zien dat viewers de data feed kunnen maken zonder dat ze gedetailleerde referenties hoeven te kennen. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Gegevens bronnen die worden ondersteund en de bijbehorende verificatie typen


| Gegevensbronnen | Verificatie typen |
|-------------| ---------------------|
|[**Inzichten Azure-toepassing**](#appinsights)|  Basic |
|[**Azure Blob Storage (JSON)**](#blob) | Basic<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer (Kusto)**](#kusto) | Basic<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>Service-principal<br>Service-Principal van sleutel kluis<br> |
|[**Azure SQL Database/SQL Server**](#sql) | Basic<br>ManagedIdentity<br>Service-principal<br>Service-Principal van sleutel kluis<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**HTTP-aanvraag**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

Maak een **referentie-entiteit** en gebruik deze om te verifiëren met uw gegevens bronnen. De volgende secties bevatten de para meters die vereist zijn voor *basis* verificatie. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **Toepassings-id**: dit wordt gebruikt om deze toepassing te identificeren wanneer u de Application INSIGHTS-API gebruikt. Ga als volgt te werk om de toepassings-ID op te halen:

    1. Klik vanuit uw Application Insights-resource op API-toegang.

    2. Kopieer de toepassings-ID die is gegenereerd in het veld **toepassings-id** in metrische gegevens Advisor. 
    
    Raadpleeg de [Azure bot service-documentatie](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) voor meer informatie.

* **API-sleutel**: API-sleutels worden door toepassingen buiten de browser gebruikt voor toegang tot deze resource. Ga als volgt te werk om de API-sleutel op te halen:

    1. Klik vanuit de Application Insights resource op API-toegang.

    2. Klik op API-sleutel maken.

    3. Voer een korte beschrijving in, Controleer de optie telemetrie lezen en klik op de knop sleutel genereren.

    4. Kopieer de API-sleutel naar het veld **API-sleutel** in Metrics Advisor.

* **Query**: Azure-toepassing Insights-logboeken zijn gebaseerd op Azure Data Explorer en Azure monitor logboek query's gebruiken een versie van dezelfde Kusto-query taal. De [documentatie voor de Kusto-query taal](/azure/data-explorer/kusto/query/) bevat alle Details voor de taal en moet uw primaire resource zijn voor het schrijven van een query op Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Verbindings reeks**: Zie de Azure Blob Storage [Connection String](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) -artikel voor informatie over het ophalen van deze teken reeks.

* **Container**: Metrics Advisor verwacht tijdreeks gegevens die zijn opgeslagen als blob-bestanden (één BLOB per tijds tempel) onder één container. Dit is het veld container naam.

* **BLOB-sjabloon**: dit is de sjabloon van de namen van de BLOB-bestanden. Bijvoorbeeld: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. De volgende para meters worden ondersteund:
  * `%Y` is het jaar opgemaakt als `yyyy`
  * `%m` is de maand opgemaakt als `MM`
  * `%d` is de dag notatie `dd`
  * `%h` is het uur dat is opgemaakt als `HH`
  * `%M` is de minuut notatie `mm`

* **JSON-indelings versie**: definieert het gegevens schema in de json-bestanden. De functie Advisor ondersteunt momenteel twee versies:
  
  * V1 (standaard waarde)

      Alleen de metrische *naam* en *waarde* worden geaccepteerd. Bijvoorbeeld:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      De metrische *dimensies* en de *tijds tempel* worden ook geaccepteerd. Bijvoorbeeld:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Er is slechts één tijds tempel toegestaan per JSON-bestand. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Verbindings reeks**: de Connection String voor toegang tot uw Azure Cosmos db. Deze vindt u in de Cosmos DB-resource, in **sleutels**. 
* **Data Base**: de data base waarop u een query wilt uitvoeren. U kunt dit vinden op de pagina **Bladeren** onder **containers** .
* **Verzamelings-id**: de VERZAMELINGS-id waarop de query moet worden uitgevoerd. U kunt dit vinden op de pagina **Bladeren** onder **containers** .
* **SQL-query**: een SQL-query om gegevens op te halen en te formuleren in multi-dimensionale time series-gegevens. U kunt de `@StartTime` variabelen en gebruiken `@EndTime` in uw query. Ze moeten de volgende indeling hebben: `yyyy-MM-dd HH:mm:ss` .

    Voorbeeldquery:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Voorbeeld query voor een gegevens segment van 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Verbindings reeks**: metrische gegevens Advisor ondersteunt toegang tot Azure Data Explorer (Kusto) met behulp van Azure AD-toepassings verificatie. U moet een Azure AD-toepassing maken en registreren en vervolgens machtigen om toegang te krijgen tot een Azure Data Explorer-data base. Raadpleeg de documentatie van [Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app) als u uw Connection String wilt ontvangen.

* **Query**: Zie [Kusto query language](/azure/data-explorer/kusto/query) om gegevens op te halen en te formuleren in multi-dimensionale time series-gegevens. U kunt de `@StartTime` variabelen en gebruiken `@EndTime` in uw query. Ze moeten de volgende indeling hebben: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Account naam**: de account naam van uw Azure data Lake Storage Gen2. Dit kunt u vinden in de bron van uw Azure Storage-account (Azure Data Lake Storage Gen2) in **toegangs sleutels**.

* **Account sleutel**: Geef de account naam op voor toegang tot uw Azure data Lake Storage Gen2. Dit kan worden gevonden in de instelling voor de **toegangs sleutels** van Azure Storage Account (Azure data Lake Storage Gen2).

* **Bestandssysteem naam (container)**: de metrische gegevens die worden opgeslagen als blob-bestanden (één BLOB per tijds tempel) onder één container, worden verwacht. Dit is het veld container naam. Dit kan worden gevonden in uw Azure Storage-account (Azure Data Lake Storage Gen2) en op containers in de sectie BLOB-service.

* **Directory sjabloon**: dit is de Directory sjabloon van het blobbestand. Bijvoorbeeld: */%Y/%m/%d*. De volgende para meters worden ondersteund:
  * `%Y` is het jaar opgemaakt als `yyyy`
  * `%m` is de maand opgemaakt als `MM`
  * `%d` is de dag notatie `dd`
  * `%h` is het uur dat is opgemaakt als `HH`
  * `%M` is de minuut notatie `mm`

* **Bestands sjabloon**: dit is de bestands sjabloon van het blobbestand. Bijvoorbeeld: *X_% Y-% m-% d-% h-% M.jsop*. De volgende para meters worden ondersteund:
  * `%Y` is het jaar opgemaakt als `yyyy`
  * `%m` is de maand opgemaakt als `MM`
  * `%d` is de dag notatie `dd`
  * `%h` is het uur dat is opgemaakt als `HH`
  * `%M` is de minuut notatie `mm`

De functie Advisor ondersteunt momenteel het gegevens schema in de JSON-bestanden als volgt. Bijvoorbeeld:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Verbindings reeks**: Metrics Advisor accepteert een [ADO.net stijl verbindings reeks](/dotnet/framework/data/adonet/connection-string-syntax) voor de gegevens bron van SQL Server.

    Voorbeeld connection string:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Query**: een SQL-query om gegevens op te halen en te formuleren in multi-dimensionale time series-gegevens. U kunt een `@StartTime` variabele in uw query gebruiken om de verwachte metrische waarde te verkrijgen.

  * `@StartTime`: een datum/tijd in de indeling van `yyyy-MM-dd HH:mm:ss`

    Voorbeeldquery:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Werkelijke query uitgevoerd voor gegevens segment van 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **Verbindings reeks**: raadpleeg [een Connection String weer geven en kopiëren](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys) voor informatie over het ophalen van de Connection String van Azure Table Storage.

* **Tabel naam**: Geef een tabel op waarop u een query wilt uitvoeren. Dit kan worden gevonden in uw exemplaar van het Azure Storage-account. Klik in de sectie **Table service** op **tabellen** .

* **Query uitvoeren** U kunt de `@StartTime` in uw query gebruiken. `@StartTime` wordt vervangen door een teken reeks voor de notatie JJJJ-MM-DDTuu: mm: SS in het script.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Host**: Geef de hoofddoel host van het Elasticsearch-cluster op.
* **Poort**: Geef de hoofd poort van het Elasticsearch-cluster op.
* **Autorisatie-header**: Geef de waarde van de autorisatie-header van het Elasticsearch-cluster op.
* **Query**: Geef de query op om gegevens op te halen. Tijdelijke aanduiding @StartTime wordt ondersteund. ( bijvoorbeeld wanneer gegevens van 2020-06-21T00:00:00Z wordt opgenomen, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP-aanvraag</span>

* **Aanvraag-URL**: een http-URL die een JSON kan retour neren. De tijdelijke aanduidingen% Y,% m,% d,% h,% M worden ondersteund:% Y = jaar in notatie JJJJ,% m = maand in notatie MM,% d = dag in notatie dd,% h = uur in indeling mm. Bijvoorbeeld: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **HTTP-methode van aanvraag**: gebruik Get of post.
* **Aanvraag header**: kan basis verificatie toevoegen. 
* **Aanvraag lading**: alleen JSON-nettolading wordt ondersteund. Tijdelijke aanduiding @StartTime wordt ondersteund in de payload. Het antwoord moet de volgende JSON-indeling hebben: [{"Time Stamp": "2018-01-01T00:00:00Z", "Market": "nl-nl", "Count": 11, "Revenue": 1,23}, {"Time Stamp": "2018-01-01T00:00:00Z", "Market": "zh-cn", "Count": 22, "opbrengst": 4,56}]. (bijvoorbeeld wanneer gegevens van 2020-06-21T00:00:00Z wordt opgenomen, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Verbindings reeks**: de Connection String voor toegang tot uw InfluxDB.
* **Data Base**: de data base waarop u een query wilt uitvoeren.
* **Query**: een query om gegevens op te halen en te formuleren in multi-dimensionale time series-gegevens voor opname.
* **Gebruikers naam**: dit is optioneel voor verificatie. 
* **Wacht woord**: dit is optioneel voor verificatie. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Verbindings reeks**: de Connection String voor toegang tot uw MongoDb.
* **Data Base**: de data base waarop u een query wilt uitvoeren.
* **Opdracht**: een opdracht voor het ophalen en formuleren van gegevens in multi-dimensionale time series-gegevens voor opname.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Verbindings reeks**: de Connection String voor toegang tot uw MySQL-data base.
* **Query**: een query om gegevens op te halen en te formuleren in multi-dimensionale time series-gegevens voor opname.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Verbindings reeks**: de Connection String voor toegang tot uw postgresql-data base.
* **Query**: een query om gegevens op te halen en te formuleren in multi-dimensionale time series-gegevens voor opname.

## <a name="next-steps"></a>Volgende stappen

* Terwijl u wacht tot uw metrische gegevens in het systeem worden opgenomen, leest u meer over [het beheren van data feed-configuraties](how-tos/manage-data-feeds.md).
* Wanneer uw metrische gegevens worden opgenomen, kunt u [metrische waarden configureren en de configuratie voor het detecteren van nauw keurigheid instellen](how-tos/configure-metrics.md).