---
title: Azure Cosmos DB gegevens bewaken met behulp van de diagnostische instellingen van Azure
description: Meer informatie over het gebruik van diagnostische instellingen van Azure voor het bewaken van de prestaties en beschik baarheid van gegevens die zijn opgeslagen in Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.openlocfilehash: 8df8305a5b50567c2f355721212a7c0feebd81d4
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033741"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure Cosmos DB gegevens bewaken met behulp van diagnostische instellingen in azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Diagnostische instellingen in Azure worden gebruikt om bron logboeken te verzamelen. Azure-resource logboeken worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. Deze logboeken worden vastgelegd per aanvraag en ze worden ook wel ' gegevens vlak logboeken ' genoemd. Enkele voor beelden van gegevens vlak bewerkingen zijn Delete, INSERT en readFeed. De inhoud van deze logboeken is afhankelijk van het bron type.

Metrische platform gegevens en de activiteiten logboeken worden automatisch verzameld, terwijl u een diagnostische instelling moet maken om bron logboeken te verzamelen of deze buiten Azure Monitor door te sturen. U kunt de diagnostische instelling voor Azure Cosmos-accounts inschakelen met behulp van de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Navigeer naar uw Azure Cosmos-account. Open het deel venster **Diagnostische instellingen** en selecteer optie **Diagnostische instelling toevoegen** .

1. In het deel venster **Diagnostische instellingen** vult u het formulier in met de volgende details: 

    * **Naam**: Voer een naam in voor de logboeken die u wilt maken.

    * U kunt de logboeken opslaan om te **archiveren naar een opslag account**, **naar een event hub streamen** of **verzenden naar log Analytics**

1. Wanneer u een diagnostische instelling maakt, geeft u op welke logboek categorie u wilt verzamelen. De categorieën logboeken die door Azure Cosmos DB worden ondersteund, worden hieronder weer gegeven, samen met het voorbeeld logboek dat door hen wordt verzameld:

 * **DataPlaneRequests**: Selecteer deze optie om back-end-aanvragen te registreren voor alle api's, waaronder SQL-, Graph-, MongoDb-, Cassandra-en Table-API-accounts in azure Cosmos db. De belangrijkste eigenschappen die u moet weten, zijn: `Requestcharge` ,, `statusCode` ,, `clientIPaddress` `partitionID` `resourceTokenPermissionId` en `resourceTokenPermissionMode` .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372", "resourceTokenPermissionId": "perm-prescriber-app","resourceTokenPermissionMode": "all", "resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecteer deze optie om door de gebruiker geïnitieerde aanvragen van de front-end te registreren om aanvragen voor de Azure Cosmos DB-API voor MongoDb te leveren. Dit logboek type is niet beschikbaar voor andere API-accounts. De belangrijkste eigenschappen die u moet weten, zijn: `Requestcharge` , `opCode` . Wanneer u MongoRequests in Diagnostische logboeken inschakelt, moet u ervoor zorgen dat u de DataPlaneRequests uitschakelt. U ziet één logboek voor elke aanvraag die wordt gedaan via de API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: Selecteer deze optie om door de gebruiker geïnitieerde aanvragen van de front-end te registreren om aanvragen voor de Azure Cosmos DB-API voor Cassandra te leveren. Dit logboek type is niet beschikbaar voor andere API-accounts. De sleutel eigenschappen die u moet weten `operationName` , `requestCharge` ,, `piiCommandText` . Wanneer u CassandraRequests in Diagnostische logboeken inschakelt, moet u ervoor zorgen dat u de DataPlaneRequests uitschakelt. U ziet één logboek voor elke aanvraag die wordt gedaan via de API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **GremlinRequests**: Selecteer deze optie om door de gebruiker geïnitieerde aanvragen van de front-end te registreren om aanvragen voor de Azure Cosmos DB-API voor Gremlin te leveren. Dit logboek type is niet beschikbaar voor andere API-accounts. De sleutel eigenschappen die u moet weten, zijn `operationName` en `requestCharge` . Wanneer u GremlinRequests in Diagnostische logboeken inschakelt, moet u ervoor zorgen dat u de DataPlaneRequests uitschakelt. U ziet één logboek voor elke aanvraag die wordt gedaan via de API.

  ```json
  { "time": "2021-01-06T19:36:58.2554534Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "GremlinRequests", "operationName": "eval", "properties": {"activityId": "b16bd876-0e5c-4448-90d1-7f3134c6b5ff", "errorCode": "200", "duration": "9.6036", "requestCharge": "9.059999999999999", "databaseName": "GraphDemoDatabase", "collectionName": "GraphDemoContainer", "authorizationTokenType": "PrimaryMasterKey", "address": "98.225.2.189", "estimatedDelayFromRateLimitingInMilliseconds": "0", "retriedDueToRateLimiting": "False", "region": "Australia East", "requestLength": "266", "responseLength": "364", "userAgent": "<empty>"}}
  ```

* **QueryRuntimeStatistics**: Selecteer deze optie om de uitgevoerde query tekst te registreren. Dit logboek type is alleen beschikbaar voor SQL-API-accounts.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecteer deze optie om de statistieken van de partitie sleutels te registreren. Dit wordt momenteel weer gegeven met de opslag grootte (KB) van de partitie sleutels. Zie de sectie [problemen oplossen met behulp van Azure diagnostische query's](#diagnostic-queries) in dit artikel. Bijvoorbeeld query's die gebruikmaken van ' PartitionKeyStatistics '. Het logboek wordt verzonden op basis van de eerste drie partitie sleutels die de meeste gegevens opslag in beslag nemen. Dit logboek bevat gegevens, zoals de abonnements-ID, de naam van de regio, de database naam, de naam van de verzameling, de partitie sleutel en de opslag grootte in KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: in dit logboek wordt het geaggregeerde aantal exemplaren per seconde van partitie sleutels gerapporteerd. Op dit moment rapporteert Azure Cosmos DB alleen partitie sleutels voor SQL API-accounts en voor bewerkingen voor lezen/schrijven en opgeslagen procedures. andere Api's en bewerkings typen worden niet ondersteund. Voor andere Api's is de partitie sleutel kolom in de tabel met Diagnostische logboeken leeg. Dit logboek bevat gegevens zoals de abonnements-ID, de regio naam, de database naam, de naam van de verzameling, de partitie sleutel, het bewerkings type en de kosten voor aanvragen. Zie de sectie [problemen oplossen met behulp van Azure diagnostische query's](#diagnostic-queries) in dit artikel. Bijvoorbeeld query's die gebruikmaken van ' PartitionKeyRUConsumption '. 

* **ControlPlaneRequests**: dit logboek bevat details over besturings vlak bewerkingen zoals het maken van een account, het toevoegen of verwijderen van een regio, het bijwerken van de replicatie-instellingen voor het account, enzovoort. Dit logboek type is beschikbaar voor alle API-typen die SQL (core), MongoDB, Gremlin, Cassandra, Table-API, bevatten.

* **Aanvragen**: Selecteer deze optie als u metrische gegevens wilt verzamelen van Azure Cosmos DB op de doelen in de diagnostische instelling. Dit zijn dezelfde gegevens die automatisch worden verzameld in azure Metrics. Verzamelen van metrische gegevens met resource logboeken voor het analyseren van beide soorten gegevens en het verzenden van metrische gegevens buiten Azure Monitor.

Zie voor gedetailleerde informatie over het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell de [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/platform/diagnostic-settings.md) article.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> Problemen met diagnostische query's oplossen

1. Query's uitvoeren voor de bewerkingen die langer dan drie milliseconden duren om uit te voeren:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. Query's uitvoeren voor de gebruikers agent waarin de bewerkingen worden uitgevoerd:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. Query's uitvoeren voor langlopende bewerkingen:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. Partitie sleutel statistieken ophalen om schuine lagen voor de eerste drie partities voor een database account te evalueren:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. Hoe kunt u de aanvraag kosten voor dure query's ophalen?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Hoe kunt u vinden welke bewerkingen het meren deel zijn van RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. Alle query's ophalen die meer dan 100 RU/s zijn gekoppeld aan gegevens uit **DataPlaneRequests** en **QueryRunTimeStatistics**.

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. De aanvraag kosten en de uitvoerings duur van een query ophalen?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. Hoe kan ik de distributie voor verschillende bewerkingen verkrijgen?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Wat is de maximale door Voer die een partitie heeft verbruikt?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Informatie over het gebruik van de partitie sleutels RU/s per seconde ophalen?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. De aanvraag kosten voor een specifieke partitie sleutel ophalen

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. De belangrijkste partitie sleutels met de meeste RU/s ophalen in een specifieke periode 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Hoe kan ik de logboeken ophalen voor de partitie sleutels waarvan de opslag ruimte groter is dan 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Hoe kan ik P99-of P50-replicatie latentie ophalen voor bewerkingen, kosten voor aanvragen of de lengte van het antwoord?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. Hoe kan ik ControlPlane-logboeken ophalen?
 
   Vergeet niet om over te scha kelen op de vlag zoals beschreven in het artikel [schrijf toegang op basis van meta gegevens uitschakelen](audit-control-plane-logs.md#disable-key-based-metadata-write-access) en de bewerkingen uit te voeren met behulp van Azure PowerShell, de Azure CLI of Azure Resource Manager.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor voor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB](use-metrics.md)
