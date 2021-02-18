---
title: Diagnostische logboeken instellen-Azure Event hub | Microsoft Docs
description: Meer informatie over het instellen van activiteiten logboeken en Diagnostische logboeken voor Event hubs in Azure.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: dc5cbea31583a4e8126897b2dcda63b216438e1b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573176"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnostische logboeken instellen voor een Azure Event Hub

U kunt twee typen logboeken voor Azure Event Hubs bekijken:

* **[Activiteiten logboeken](../azure-monitor/essentials/platform-logs-overview.md)**: deze logboeken bevatten informatie over bewerkingen die zijn uitgevoerd voor een taak. De logboeken zijn altijd ingeschakeld. U kunt de vermeldingen in het activiteiten logboek bekijken door **activiteiten logboek** te selecteren in het linkerdeel venster voor uw event hub naam ruimte in de Azure Portal. Bijvoorbeeld: ' naam ruimte maken of bijwerken ', ' event hub maken of bijwerken '.

    ![Activiteiten logboek voor een Event Hubs naam ruimte](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md)**: Diagnostische logboeken bieden uitgebreide informatie over bewerkingen en acties die worden uitgevoerd op uw naam ruimte met behulp van de API of via beheer-clients in de taal-SDK. 
    
    In de volgende sectie wordt beschreven hoe u Diagnostische logboeken inschakelt voor een Event Hubs naam ruimte.

## <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen
Diagnostische logboeken zijn standaard uitgeschakeld. Voer de volgende stappen uit om Diagnostische logboeken in te scha kelen:

1.  Navigeer in het [Azure Portal](https://portal.azure.com)naar uw event hubs naam ruimte. 
2. Selecteer **Diagnostische instellingen** onder **bewaking** in het linkerdeel venster en selecteer **+ Diagnostische instelling toevoegen**. 

    ![Pagina Diagnostische instellingen-diagnostische instelling toevoegen](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Selecteer in de sectie **categorie Details** de **typen Diagnostische logboeken** die u wilt inschakelen. Verderop in dit artikel vindt u meer informatie over deze categorieën. 
5. Stel in de sectie **doel Details** het archief doel (doel) in dat u wilt. bijvoorbeeld een opslag account, een Event Hub of een Log Analytics-werk ruimte.

    ![Pagina Diagnostische instellingen toevoegen](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Selecteer **Opslaan** op de werk balk om de diagnostische instellingen op te slaan.

    Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in het geconfigureerde archief doel in het deel venster **Diagnostische logboeken** .

    Zie voor meer informatie over het configureren van diagnostische gegevens het [overzicht van Azure Diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorieën met Diagnostische logboeken

Event Hubs Diagnostische logboeken vastleggen voor de volgende categorieën:

| Categorie | Beschrijving | 
| -------- | ----------- | 
| Archief logboeken | Legt informatie vast over [Event hubs Capture](event-hubs-capture-overview.md) -bewerkingen, met name logboeken die betrekking hebben op het vastleggen van fouten. |
| Operationele logboeken | Alle beheer bewerkingen vastleggen die worden uitgevoerd op de Azure Event Hubs-naam ruimte. Gegevens bewerkingen worden niet vastgelegd vanwege de grote hoeveelheid gegevens bewerkingen die worden uitgevoerd op Azure Event Hubs. |
| Logboeken automatisch schalen | Hiermee worden automatisch verg Roten van bewerkingen die zijn uitgevoerd op een Event Hubs naam ruimte vastgelegd. |
| Kafka Coordinator-logboeken | Legt Kafka-coördinator bewerkingen met betrekking tot Event Hubs. |
| Fout logboeken van Kafka-gebruikers | Legt informatie vast over Kafka-Api's die worden aangeroepen op Event Hubs. |
| Event Hubs verbinding met het virtuele netwerk (VNet) | Legt informatie vast over IP-adressen en virtuele netwerken die verkeer naar Event Hubs verzenden. |
| Door de klant beheerde sleutel gebruikers Logboeken | Registreert bewerkingen die betrekking hebben op door de klant beheerde sleutel. |


Alle logboeken worden opgeslagen in de indeling van de JavaScript Object Notation (JSON). Elke vermelding bevat teken reeks velden die gebruikmaken van de indeling die wordt beschreven in de volgende secties.

## <a name="archive-logs-schema"></a>Schema voor archief logboeken

De JSON-teken reeksen van het archief logboek bevatten elementen die in de volgende tabel worden weer gegeven:

Naam | Beschrijving
------- | -------
`TaskName` | Beschrijving van de mislukte taak
`ActivityId` | Interne ID, gebruikt voor bijhouden
`trackingId` | Interne ID, gebruikt voor bijhouden
`resourceId` | Resource-ID Azure Resource Manager
`eventHub` | Volledige naam van Event hub (inclusief naam van naam ruimte)
`partitionId` | De Event hub-partitie waarnaar wordt geschreven
`archiveStep` | mogelijke waarden: ArchiveFlushWriter, DestinationInit
`startTime` | Begin tijd van de fout
`failures` | Aantal keer dat de fout is opgetreden
`durationInSeconds` | Duur van de fout
`message` | Foutbericht
`category` | ArchiveLogs

De volgende code is een voor beeld van een JSON-teken reeks in het archief logboek:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Schema van operationele logboeken

De JSON-teken reeksen van het operationele logboek bevatten elementen die in de volgende tabel worden weer gegeven:

Naam | Beschrijving
------- | -------
`ActivityId` | Interne ID, gebruikt voor tracking doeleinden |
`EventName` | naam van bewerking. Voor een lijst met waarden voor dit element, zie de [gebeurtenis namen](#event-names) |
`resourceId` | Resource-ID Azure Resource Manager |
`SubscriptionId` | Abonnements-id |
`EventTimeString` | Bewerkings tijd |
`EventProperties` |Eigenschappen voor de bewerking. Dit element bevat meer informatie over de gebeurtenis, zoals wordt weer gegeven in het volgende voor beeld. |
`Status` | Bewerkings status. De waarde kan ofwel **geslaagd** of **mislukt** zijn.  |
`Caller` | Aanroeper van de bewerking (Azure Portal of Management-client) |
`Category` | OperationalLogs |

De volgende code is een voor beeld van een JSON-teken reeks voor een operationeel logboek:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

### <a name="event-names"></a>Gebeurtenis namen
De gebeurtenis naam wordt ingevuld als bewerkings type + resource type van de volgende opsommingen. Bijvoorbeeld, `Create Queue` `Retrieve Event Hu` of `Delete Rule` . 

| Het type bewerking | Resourcetype | 
| -------------- | ------------- | 
| <ul><li>Maken</li><li>Bijwerken</li><li>Verwijderen</li><li>Ophalen</li><li>Onbekend</li></ul> | <ul><li>Naamruimte</li><li>Wachtrij</li><li>Onderwerp</li><li>Abonnement</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>Regel</li>ConsumerGroup</li> |

## <a name="autoscale-logs-schema"></a>Schema voor automatisch schalen van Logboeken
JSON van het logboek voor automatisch schalen bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- | 
| `TrackingId` | Interne ID, die wordt gebruikt voor tracerings doeleinden |
| `ResourceId` | Azure Resource Manager-resource-id. |
| `Message` | Informatief bericht, dat details bevat over het automatisch verg Roten van de actie. Het bericht bevat de vorige en huidige waarde van de doorvoer eenheid voor een opgegeven naam ruimte en wat de verg Roten van de TU heeft veroorzaakt. |

Hier volgt een voor beeld van een gebeurtenis voor automatisch schalen: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Kafka Coordinator-logboeken schema
Kafka Coordinator-logboek JSON bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- | 
| `RequestId` | Aanvraag-ID, die wordt gebruikt voor tracerings doeleinden |
| `ResourceId` | Resource-ID Azure Resource Manager |
| `Operation` | De naam van de bewerking die wordt uitgevoerd tijdens de groeps coördinatie |
| `ClientId` | Client-id |
| `NamespaceName` | Naam van naamruimte | 
| `SubscriptionId` | Azure-abonnements-ID |
| `Message` | Informatief of waarschuwings bericht, dat details bevat over acties die zijn uitgevoerd tijdens de groeps coördinatie. |

### <a name="example"></a>Voorbeeld

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Schema voor Kafka-gebruikers fout logboeken
Kafka voor fout logboek van gebruikers bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- |
| `TrackingId` | Tracerings-ID, die wordt gebruikt voor tracerings doeleinden. |
| `NamespaceName` | Naam van naamruimte |
| `Eventhub` | Naam van Event Hub |
| `PartitionId` | Partitie-id |
| `GroupId` | Groeps-id |
| `ClientId` | Client-id |
| `ResourceId` | Azure Resource Manager-resource-id. |
| `Message` | Informatief bericht, dat details bevat over een fout |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Verbindings gebeurtenis schema voor virtuele netwerk Event Hubs
De JSON-verbindings gebeurtenis van het Event Hubs virtuele netwerk (VNet) bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---  | ----------- | 
| `SubscriptionId` | Azure-abonnements-ID |
| `NamespaceName` | Naam van naamruimte |
| `IPAddress` | IP-adres van een client die verbinding maakt met de Event Hubs-service |
| `Action` | De actie die door de Event Hubs-service wordt uitgevoerd bij de evaluatie van verbindings aanvragen. Ondersteunde acties zijn **verbinding accepteren** en **verbinding weigeren**. |
| `Reason` | Geeft een reden waarom de actie is uitgevoerd |
| `Count` | Aantal exemplaren voor de opgegeven actie |
| `ResourceId` | Azure Resource Manager-resource-id. |

Virtuele netwerk logboeken worden alleen gegenereerd als de naam ruimte toegang toestaat vanuit **geselecteerde netwerken** of van **specifieke IP-adressen** (IP-filter regels). Als u de toegang tot uw naam ruimte niet wilt beperken met behulp van deze functies en toch virtuele netwerk logboeken wilt ophalen om IP-adressen te volgen van clients die verbinding maken met de naam ruimte Event Hubs, kunt u de volgende tijdelijke oplossing gebruiken. Schakel IP-filtering in en voeg het totale adresseer bare IPv4-bereik toe (1.0.0.0/1-255.0.0.0/1). Event Hubs biedt geen ondersteuning voor IPv6-bereiken. 

### <a name="example"></a>Voorbeeld

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Door de klant beheerde sleutel gebruikers Logboeken
Door de klant beheerde Key gebruikers logboek JSON bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- | 
| `Category` | Type categorie voor een bericht. Dit is een van de volgende waarden: **fout** en **info** |
| `ResourceId` | Interne Resource-ID, waaronder de Azure-abonnements-ID en naam van de naam ruimte |
| `KeyVault` | De naam van de Key Vault resource |
| `Key` | De naam van de Key Vault sleutel. |
| `Version` | Versie van de Key Vault sleutel |
| `Operation` | De naam van een bewerking die is uitgevoerd om aanvragen te behandelen |
| `Code` | Statuscode |
| `Message` | Bericht, dat details over een fout of informatief bericht bevat |



## <a name="next-steps"></a>Volgende stappen
- [Inleiding tot Event Hubs](./event-hubs-about.md)
- [Event Hubs-voor beelden](sdks.md)
- Aan de slag met Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
