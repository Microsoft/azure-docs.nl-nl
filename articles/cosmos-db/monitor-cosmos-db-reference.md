---
title: Azure Cosmos DB gegevens referentie bewaken | Microsoft Docs
description: Belang rijk referentie materiaal dat nodig is wanneer u Logboeken en metrische gegevens in Azure Cosmos DB bewaakt.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 5f542b35110a6d967640ad91faead75f6cc0e0c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593291"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Azure Cosmos DB gegevens referentie bewaken

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dit artikel bevat naslaginformatie over logboek- en metrische gegevens die worden verzameld om de prestaties en beschikbaarheid van Azure Cosmos DB te analyseren. Zie de [Azure Cosmos DB artikel bewaken](monitor-cosmos-db.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Cosmos db.

## <a name="metrics"></a>Metrische gegevens

Alle metrische gegevens die overeenkomen met Azure Cosmos DB worden opgeslagen in de naam ruimte **Cosmos DB standaard metrische gegevens**. Zie [Azure monitor ondersteunde metrische gegevens](../azure-monitor/essentials/metrics-supported.md)voor een lijst met alle Azure monitor metrische gegevens voor ondersteuning (inclusief Azure Cosmos DB). In deze sectie vindt u alle automatisch verzamelde platform gegevens die zijn verzameld voor Azure Cosmos DB.  

### <a name="request-metrics"></a>Metrische gegevens aanvragen

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type) |Description|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totaal aantal aanvragen) | Aantal (aantal) | Aantal aanvragen dat is gedaan| DATABASENAME, verzamel-, regio, status code| Alles | TotalRequests, http 2xx, HTTP 3xx, HTTP 400, HTTP 401, interne server fout, service niet beschikbaar, beperkt aantal aanvragen per seconde | Wordt gebruikt voor het bewaken van aanvragen per status code, container op een minuut granulatie. Als u gemiddeld aantal aanvragen per seconde wilt ophalen, gebruikt u de aggregatie van Count op minuut en deelt u deze door 60. |
| MetadataRequests (meta gegevens aanvragen) |Aantal (aantal) | Aantal aanvragen voor meta gegevens. Azure Cosmos DB behoudt de container van de meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort, en hun configuraties, kosteloos kunt inventariseren. | DATABASENAME, verzamel-, regio, status code| Alles| |Wordt gebruikt voor het bewaken van vertragingen als gevolg van aanvragen voor meta gegevens.|
| MongoRequests (Mongo-aanvragen) | Aantal (aantal) | Aantal gemaakte Mongo-aanvragen | DATABASENAME, verzamel-, regio, opdrachtnaam, error code| Alles |Mongo query-aanvraag frequentie, frequentie van Mongo-update aanvragen, Mongo aanvraag frequentie voor verwijderen, Mongo aanvraag frequentie invoegen, Mongo aantal aanvraag frequentie| Wordt gebruikt voor het bewaken van Mongo-aanvraag fouten, gebruik per opdracht type. |

### <a name="request-unit-metrics"></a>Metrische gegevens van aanvraag eenheid

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo-aanvraag kosten) | Aantal (totaal) |Verbruikte Mongo-aanvraag eenheden| DATABASENAME, verzamel-, regio, opdrachtnaam, error code| Alles |Kosten voor query aanvragen voor Mongo, kosten voor Mongo update aanvragen, Mongo aanvraag kosten verwijderen, Mongo aanvraag kosten invoegen, Mongo aantal aanvraag kosten| Wordt gebruikt voor het bewaken van Mongo resource RUs in een minuut.|
| TotalRequestUnits (totaal aantal aanvraag eenheden)| Aantal (totaal) | Verbruikte aanvraag eenheden| DATABASENAME, verzamel-, regio, status code |Alles| TotalRequestUnits| Wordt gebruikt voor het bewaken van het totale aantal RU-gebruik met een minuut granulatie. Als u gemiddelde RU verbruikt per seconde, gebruikt u de totale aggregatie op minuut en deelt u dit door 60.|
| ProvisionedThroughput (ingerichte door Voer)| Aantal (maximum) |Ingerichte door Voer in container granulatie| DATABASENAME, containerName| 5 M| | Wordt gebruikt om de ingerichte door Voer per container te bewaken.|

### <a name="storage-metrics"></a>Metrische opslag gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (beschik bare opslag) |Bytes (totaal) | Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M| Beschikbare opslag| Wordt gebruikt voor het bewaken van beschik bare opslag capaciteit (alleen van toepassing voor vaste opslag verzamelingen). de minimale granulatie moet 5 minuten zijn.| 
| DataUsage (gegevens gebruik) |Bytes (totaal) |Totaal gegevens gebruik gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M |Gegevens grootte | Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn.|
| IndexUsage (index gebruik) | Bytes (totaal) |Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M| Index grootte| Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn. |
| DocumentQuota (document quotum) | Bytes (totaal) | Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten per regio.| Databasenaam, verzamel-, regio| 5 M |Opslagcapaciteit| Voor het bewaken van het totale quotum in container en regio moet de minimale granulatie 5 minuten zijn.|
| DocumentCount (aantal documenten) | Aantal (totaal) |Totaal aantal documenten gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M |Aantal documenten|Voor het bewaken van het aantal documenten in de container en de regio moet de minimale granulatie 5 minuten zijn.|

### <a name="latency-metrics"></a>Metrische latentie gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| ReplicationLatency (replicatie latentie)| MilliSeconden (minimum, maximum, gemiddeld) | Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account| SourceRegion, TargetRegion| Alles | Wordt gebruikt voor het bewaken van de replicatie latentie van P99 tussen twee regio's voor een geo-gerepliceerd account. |
| Latentie aan server zijde| MilliSeconden (gemiddeld) | De tijd die de server nodig heeft om de aanvraag te verwerken. | Verzamelingnaam, ConnectionMode, DATABASENAME, OperationType, PublicAPIType, regio | Alles | Wordt gebruikt om de latentie van de aanvraag op de Azure Cosmos DB server te bewaken. |

### <a name="availability-metrics"></a>Metrische beschikbaarheids gegevens

|Metrisch (metrische weergave naam) |Eenheid (aggregatie type)|Description| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service beschikbaarheid)| Percentage (minimum, maximum) | Beschik baarheid van account aanvragen op een granulatie van één uur| 1U | Service beschikbaarheid | Vertegenwoordigt het percentage van het totaal aantal geslaagde aanvragen. Een aanvraag wordt als mislukt beschouwd als gevolg van een systeem fout als de status code 410, 500 of 503 wordt gebruikt om de beschik baarheid van het account te controleren op de granulatie van het uur. |

### <a name="cassandra-api-metrics"></a>Cassandra-API metrische gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-aanvragen) | Aantal (aantal) | Aantal gemaakte Cassandra-API aanvragen| DATABASENAME, Verzamelingsset, error code, regio, OperationType, resource type| Alles| Wordt gebruikt om Cassandra-aanvragen te bewaken met een granulatie van minuten. Als u gemiddeld aantal aanvragen per seconde wilt ophalen, gebruikt u de aggregatie van Count op minuut en deelt u deze door 60.|
| CassandraRequestCharges (Cassandra-aanvraag kosten) | Aantal (som, min, Max, Gem) | De aanvraag eenheden die worden verbruikt door de Cassandra-API | DATABASENAME, verzamel-, regio, OperationType, resource type| Alles| Wordt gebruikt om te controleren RUs dat per minuut wordt gebruikt door een Cassandra-API-account.|
| CassandraConnectionClosures (Cassandra-verbinding gesloten) |Aantal (aantal) |Aantal gesloten Cassandra-verbindingen| ClosureReason, regio| Alles | Wordt gebruikt om de connectiviteit tussen clients en de Azure Cosmos DB Cassandra-API te bewaken.|

Zie voor meer informatie een overzicht van [alle platform metrieken die worden ondersteund in azure monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="resource-logs"></a>Resourcelogboeken

De volgende tabel geeft een overzicht van de eigenschappen van resource Logboeken in Azure Cosmos DB. De bron logboeken worden verzameld in Azure Monitor Logboeken of Azure Storage. In Azure Monitor worden logboeken verzameld in de tabel **AzureDiagnostics** onder de resource provider * * naam van `MICROSOFT.DOCUMENTDB` .

| Azure Storage veld of eigenschap | Eigenschap Azure Monitor logboeken | Beschrijving |
| --- | --- | --- |
| **time** | **TimeGenerated** | De datum en tijd (UTC) waarop de bewerking plaatsvond. |
| **resourceId** | **Resource** | Het Azure Cosmos DB account waarvoor logboeken zijn ingeschakeld.|
| **category** | **Categorie** | Voor Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests**, **CassandraRequests**, **GremlinRequests** zijn de beschik bare logboek typen. |
| **operationName** | **OperationName** | De naam van de bewerking. De naam van de bewerking kan,,,,,,,,,,, of worden uitgevoerd  `Create` `Update` `Read` `ReadFeed` `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` `Upsert` .   |
| **properties** | n.v.t. | De inhoud van dit veld wordt beschreven in de volgende rijen. |
| **activityId** | **activityId_g** | De unieke GUID voor de geregistreerde bewerking. |
| **User agent** | **userAgent_s** | Een teken reeks die de gebruikers agent van de client opgeeft van waaruit de aanvraag is verzonden. De indeling van de gebruikers agent is `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | Het type van de resource waartoe toegang wordt verkregen. Deze waarde kan een Data Base, container, document, bijlage, gebruiker, machtiging, opgeslagen procedure, trigger, door de gebruiker gedefinieerde functie of een aanbieding zijn. |
| **statuscode** | **statusCode_s** | De antwoord status van de bewerking. |
| **requestResourceId** | **ResourceId** | De resourceId die betrekking heeft op de aanvraag. Afhankelijk van de bewerking die is uitgevoerd, kan deze waarde verwijzen naar `databaseRid` , `collectionRid` of `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | Het IP-adres van de client. |
| **requestCharge** | **requestCharge_s** | Het aantal RU/s dat door de bewerking wordt gebruikt |
| **collectionRid** | **collectionId_s** | De unieke ID voor de verzameling.|
| **hebben** | **duration_d** | De duur van de bewerking, in milliseconden. |
| **requestLength** | **requestLength_s** | De lengte van de aanvraag, in bytes. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|
| **resourceTokenPermissionId** | **resourceTokenPermissionId_s** | Met deze eigenschap geeft u de machtigings-id van het bron token op die u hebt opgegeven. Zie voor meer informatie over machtigingen de [beveiligde toegang tot uw gegevens](./secure-access-to-data.md#permissions) artikel. |
| **resourceTokenPermissionMode** | **resourceTokenPermissionMode_s** | Met deze eigenschap geeft u de machtigings modus aan die u hebt ingesteld bij het maken van het bron token. De machtigings modus kan waarden bevatten zoals "all" of "Read". Zie voor meer informatie over machtigingen de [beveiligde toegang tot uw gegevens](./secure-access-to-data.md#permissions) artikel. |
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Deze waarde is niet-leeg wanneer [bron tokens](./secure-access-to-data.md#resource-tokens) voor authenticatie worden gebruikt. De waarde verwijst naar de resource-ID van de gebruiker. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|

Zie [Azure monitor-logboeken categorieën en schema's](../azure-monitor/essentials/resource-logs-schema.md)voor een lijst met alle Azure monitor logboek categorieën en koppelingen naar gekoppelde schema's. 

## <a name="azure-monitor-logs-tables"></a>Tabellen Azure Monitor logboeken

Azure Cosmos DB maakt gebruik van Kusto-tabellen uit Azure Monitor-Logboeken. U kunt deze tabellen opvragen met log Analytics. Zie het artikel [Naslag informatie over Azure monitor-logboeken](/azure/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db) voor een lijst met Kusto Bales.

## <a name="see-also"></a>Zie ook

- Zie [bewaking Azure Cosmos DB](monitor-cosmos-db.md) voor een beschrijving van de bewakings Azure Cosmos db.
- Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.