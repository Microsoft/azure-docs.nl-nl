---
title: Export gedrag van metrische gegevens met NULL-waarden en nulwaarden
description: Bespreking van NULL-waarden versus nulwaarden bij het exporteren van metrische gegevens en een verwijzing naar een lijst met gegevens die niet exporteerbaar zijn.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: 8f503623031b7071c5e0a206d078175e16a7e919
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610616"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen

Azure Monitor biedt standaard [platform metrieken](../essentials/data-platform-metrics.md) zonder configuratie. Het biedt verschillende manieren om te communiceren met platform metrieken, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Bekijk [metrische gegevens: ondersteund](../platform/metrics-supported.md) voor een volledige lijst met platform metrieken die momenteel beschikbaar zijn met de geconsolideerde metrische pijp lijn van Azure monitor. Als u deze metrische gegevens wilt opvragen en openen, gebruikt u de [2018-01-01-API-versie](/rest/api/monitor/metricdefinitions). Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>De metrische gegevens zijn niet exporteerbaar via Diagnostische instellingen

De inhoud die op deze locatie werd gebruikt, is verplaatst naar de [lijst met ondersteunde Azure monitor metrische gegevens](../platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

Er gelden beperkingen voor het exporteren van metrische gegevens via Diagnostische instellingen. Alle metrische gegevens kunnen worden geëxporteerd met behulp van de REST API. 

## <a name="exported-zero-vs-null-values"></a>Waarden van nul VS zijn geëxporteerd 

Metrische gegevens hebben een ander gedrag bij het verwerken van 0 of NULL-waarden.  Sommige metrische gegevens geven aan dat er geen data worden opgehaald, bijvoorbeeld meet waarden voor HTTP-fouten. Met andere metrische gegevens worden NULL-waarden opgeslagen wanneer er geen data worden opgehaald omdat deze kan aangeven dat de bron offline is. U kunt het verschil zien wanneer deze metrische gegevens worden gediagrameerd met NULL-waarden die worden weer gegeven als [onderbroken lijnen](metrics-troubleshoot.md#chart-shows-dashed-line). 

Wanneer de metrische gegevens van het platform kunnen worden geëxporteerd via Diagnostische instellingen, komen ze overeen met het gedrag van de metriek. Dat wil zeggen dat ze NULL-waarden exporteren wanneer de resource geen gegevens verzendt.  Ze exporteren ' 0 ' alleen wanneer ze daad werkelijk door de onderliggende resource zijn verzonden. 

Als u een resource groep of een specifieke resource verwijdert, worden metrische gegevens van de betrokken resources niet langer verzonden naar Diagnostische instellingen export doelen. Dat wil zeggen dat het niet meer wordt weer gegeven in Event Hubs, Azure Storage accounts en Log Analytics-werk ruimten.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Metrische gegevens die worden gebruikt voor het exporteren van nul voor NULL

Vóór 1 juni 2020 worden de metrische gegevens die worden **gebruikt voor** het verzenden van ' 0 ' weer gegeven wanneer er geen data zijn. Deze nullen kunnen vervolgens worden geëxporteerd naar downstream-systemen als Log Analytics en Azure Storage.  Dit gedrag heeft een zekere Verwar ring veroorzaakt tussen de werkelijke ' 0s ' (verzonden door de resource) en ' 0s ' (NULL-waarden) geïnterpreteerd, waardoor het gedrag is gewijzigd, zodat het overeenkomt met dat van de onderliggende metriek zoals vermeld in de vorige sectie. 

De wijziging is in alle open bare en persoonlijke Clouds opgetreden.

De wijziging heeft geen invloed op het gedrag van een van de volgende ervaringen: 
   - Platform bron logboeken geëxporteerd via Diagnostische instellingen
   - Metrische gegevens grafieken in Metrics Explorer
   - Waarschuwingen voor metrische gegevens van platform
 
Hier volgt een lijst met de metrische gegevens waarvan het gedrag is gewijzigd. 

| ResourceType                    | Metrisch               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Niet-geautoriseerde gateway aanvragen (afgeschaft)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Totaal aantal gateway aanvragen (afgeschaft)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Geslaagde gateway aanvragen (afgeschaft)  | 
| Microsoft.ApiManagement/service | Aanvragen |  Aanvragen  | 
| Microsoft.ApiManagement/service | OtherRequests |  Andere gateway aanvragen (afgeschaft)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Mislukte gateway aanvragen (afgeschaft)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Mislukte EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Totaal aantal EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Grootte van EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Time-out EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Vertraagde EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Geslaagde EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Geweigerde EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Verwijderde EventHub-gebeurtenissen  | 
| Microsoft.ApiManagement/service | Duur |  Totale duur van gateway aanvragen  | 
| Microsoft.ApiManagement/service | BackendDuration |  Duur van back-end-aanvragen  | 
| Microsoft.DBforMariaDB/servers | storage_used |  Gebruikte opslag  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  Opslag percentage  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  Opslag limiet  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  Gebruikte server logboek opslag  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  Percentage server logboek opslag  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  Opslag limiet voor server logboek  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  Replicatie vertraging in seconden  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  Netwerk in  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  Netwerk uit  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  Geheugen percentage  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  IO-percentage  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  CPU-percentage  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  Mislukte verbindingen  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  Gebruikte back-upopslag  | 
| Microsoft.DBforMariaDB/servers | active_connections |  Actieve verbindingen  | 
| Microsoft.DBforMySQL/servers | storage_used |  Gebruikte opslag  | 
| Microsoft.DBforMySQL/servers | storage_percent |  Opslag percentage  | 
| Microsoft.DBforMySQL/servers | storage_limit |  Opslag limiet  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  Gebruikte server logboek opslag  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  Percentage server logboek opslag  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  Opslag limiet voor server logboek  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  Replicatie vertraging in seconden  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  Netwerk in  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  Netwerk uit  | 
| Microsoft.DBforMySQL/servers | memory_percent |  Geheugen percentage  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  IO-percentage  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  CPU-percentage  | 
| Microsoft.DBforMySQL/servers | connections_failed |  Mislukte verbindingen  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  Gebruikte back-upopslag  | 
| Microsoft.DBforMySQL/servers | active_connections |  Actieve verbindingen  | 
| Micro soft. apparaten/account | digitaltwins. telemetrie. nodes |  Tijdelijke aanduiding voor telemetrie van Digital Apparaatdubbels-knoop punt  | 
| Microsoft.Devices/IotHubs | twinQueries. geslaagd |  Geslaagde dubbele query's  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  Resultaat grootte van dubbele query's  | 
| Microsoft.Devices/IotHubs | twinQueries. failure |  Mislukte dubbele query's  | 
| Microsoft.Devices/IotHubs | Jobs. queryJobs. geslaagd |  Geslaagde taak query's  | 
| Microsoft.Devices/IotHubs | Jobs. queryJobs. failure |  Mislukte taak query's  | 
| Microsoft.Devices/IotHubs | Jobs. listJobs. geslaagd |  Geslaagde aanroepen naar lijst taken  | 
| Microsoft.Devices/IotHubs | Jobs. listJobs. failure |  Mislukte aanroepen naar lijst taken  | 
| Microsoft.Devices/IotHubs | Jobs. mislukt |  Mislukte taken  | 
| Microsoft.Devices/IotHubs | Jobs. createTwinUpdateJob. geslaagd |  Geslaagde creatie van dubbele update taken  | 
| Microsoft.Devices/IotHubs | Jobs. createTwinUpdateJob. failure |  Kan geen dubbele update taken uitvoeren  | 
| Microsoft.Devices/IotHubs | Jobs. createDirectMethodJob. geslaagd |  Geslaagde creatie van methode aanroep taken  | 
| Microsoft.Devices/IotHubs | Jobs. createDirectMethodJob. failure |  Kan geen aanroepen van methode aanroep taken uitvoeren  | 
| Microsoft.Devices/IotHubs | Jobs. voltooid |  Voltooide taken  | 
| Microsoft.Devices/IotHubs | Jobs. cancelJob. geslaagd |  Voltooide taak annuleringen  | 
| Microsoft.Devices/IotHubs | Jobs. cancelJob. failure |  Mislukte taak annuleringen  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Event Grid latentie (preview-versie)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Event Grid leveringen (preview-versie)  | 
| Microsoft.Devices/IotHubs | apparaten. totalDevices |  Totaal aantal apparaten (afgeschaft)  | 
| Microsoft.Devices/IotHubs | apparaten. connectedDevices. allProtocol |  Verbonden apparaten (afgeschaft)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  Totaal gebruik van apparaatgegevens (preview-versie)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  Totale hoeveelheid gegevens gebruik van apparaat  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  Totaal aantal gebruikte berichten  | 
| Microsoft.Devices/IotHubs | D2C. dubbele. update. geslaagd |  Geslaagde dubbele updates van apparaten  | 
| Microsoft.Devices/IotHubs | D2C. dubbele. update. grootte |  Grootte van dubbele updates van apparaten  | 
| Microsoft.Devices/IotHubs | D2C. dubbele. update. failure |  Mislukte dubbele updates van apparaten  | 
| Microsoft.Devices/IotHubs | D2C. dubbele. lezen. geslaagd |  Geslaagde dubbele Lees bewerkingen van apparaten  | 
| Microsoft.Devices/IotHubs | D2C. dubbele. Lees. grootte |  Reactie grootte van dubbele Lees bewerkingen van apparaten  | 
| Microsoft.Devices/IotHubs | D2C. dubbele. Read. failure |  Mislukte dubbele Lees bewerkingen van apparaten  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie. ingress. geslaagd |  Verzonden telemetriegegevens  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie. ingress. sendThrottle |  Aantal beperkings fouten  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie. ingress. allProtocol |  Verzend pogingen voor telemetrie-berichten  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie. uitgangs. geslaagd |  Route ring: telemetrie-berichten  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie.. zwevend |  Route ring: telemetriegegevens van zwevende berichten   | 
| Microsoft.Devices/IotHubs | D2C. telemetrie. uitgangs. ongeldig |  Route ring: telemetrie-berichten incompatibel  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie.. fallback |  Route ring: berichten worden bezorgd bij terugval  | 
| Microsoft.Devices/IotHubs | D2C. telemetrie. uitgangs. verwijderd |  Route ring: telemetrie-berichten verwijderd   | 
| Microsoft.Devices/IotHubs | D2C. endpoints. latentie. opslag |  Route ring: bericht latentie voor opslag  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. latentie. serviceBusTopics |  Route ring: bericht latentie voor Service Bus onderwerp  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. latentie. serviceBusQueues |  Route ring: bericht latentie voor Service Bus wachtrij  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. latentie. Event hubs |  Route ring: bericht latentie voor Event hub  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. latentie. builtIn. Events |  Route ring: bericht latentie voor berichten/gebeurtenissen  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. out. storage. bytes |  Route ring: gegevens worden geleverd aan de opslag  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. outwaar. storage. blobs |  Route ring: blobs die aan de opslag worden geleverd  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. outwaarde. Storage |  Route ring: berichten worden bezorgd bij de opslag  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. uitgangs. serviceBusTopics |  Route ring: berichten die worden bezorgd bij Service Bus onderwerp  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. uitgangs. serviceBusQueues |  Route ring: berichten worden bezorgd bij Service Bus wachtrij  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. uitgangs. Event hubs |  Route ring: berichten worden bezorgd bij Event hub  | 
| Microsoft.Devices/IotHubs | D2C. endpoints. uitgangs punt. builtIn. Events |  Route ring: berichten worden bezorgd bij berichten/gebeurtenissen  | 
| Microsoft.Devices/IotHubs | configuraties |  Metrische configuratie gegevens  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  C2D-berichten verlopen (preview-versie)  | 
| Microsoft.Devices/IotHubs | C2D. dubbele. update. geslaagd |  Geslaagde dubbele updates van back-end  | 
| Microsoft.Devices/IotHubs | C2D. dubbele. update. grootte |  Grootte van dubbele updates van back-end  | 
| Microsoft.Devices/IotHubs | C2D. dubbele. update. failure |  Mislukte dubbele updates van back-end  | 
| Microsoft.Devices/IotHubs | C2D. dubbele. lezen. geslaagd |  Geslaagde dubbele Lees bewerkingen van back-end  | 
| Microsoft.Devices/IotHubs | C2D. dubbele. Lees. grootte |  Reactie grootte van dubbele Lees bewerkingen van de back-end  | 
| Microsoft.Devices/IotHubs | C2D. dubbele. Read. failure |  Mislukte dubbele Lees bewerkingen van back-end  | 
| Microsoft.Devices/IotHubs | C2D. methods. geslaagd |  Geslaagde directe aanroepen van de methode  | 
| Microsoft.Devices/IotHubs | C2D. methods. responseSize |  Antwoord grootte van directe methode aanroepen  | 
| Microsoft.Devices/IotHubs | C2D. methods. requestSize |  Aanvraag grootte van directe-methode aanroepen  | 
| Microsoft.Devices/IotHubs | C2D. methods. failure |  Mislukte directe aanroepen van methode  | 
| Microsoft.Devices/IotHubs | C2D. commands. uitgangs. reject. geslaagd |  Geweigerde C2D-berichten  | 
| Microsoft.Devices/IotHubs | C2D. commands. OUTuitgang. complete. geslaagd |  C2D-bericht leveringen voltooid  | 
| Microsoft.Devices/IotHubs | C2D. commands. uitgangs. Abandon. geslaagd |  C2D-berichten zijn afgebroken  | 
| Micro soft. devices/provisioningServices | RegistrationAttempts |  Registratie pogingen  | 
| Micro soft. devices/provisioningServices | DeviceAssignments |  Apparaten toegewezen  | 
| Micro soft. devices/provisioningServices | AttestationAttempts |  Attestation-pogingen  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Totaal aantal aanvraag eenheden  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Totaal aantal aanvragen  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo aanvragen  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Kosten voor Mongo-aanvragen  | 
| Micro soft. EventGrid/domeinen | PublishSuccessLatencyInMs |  Latentie van publicatie geslaagd  | 
| Micro soft. EventGrid/domeinen | PublishSuccessCount |  Gepubliceerde gebeurtenissen  | 
| Micro soft. EventGrid/domeinen | PublishFailCount |  Mislukte gebeurtenissen publiceren  | 
| Micro soft. EventGrid/domeinen | MatchedEventCount |  Overeenkomende gebeurtenissen  | 
| Micro soft. EventGrid/domeinen | DroppedEventCount |  Verwijderde gebeurtenissen  | 
| Micro soft. EventGrid/domeinen | DeliverySuccessCount |  Geleverde gebeurtenissen  | 
| Micro soft. EventGrid/domeinen | DeadLetteredCount |  Gebeurtenissen met onbestelbare berichten  | 
| Micro soft. EventGrid/eventSubscriptions | MatchedEventCount |  Overeenkomende gebeurtenissen  | 
| Micro soft. EventGrid/eventSubscriptions | DroppedEventCount |  Verwijderde gebeurtenissen  | 
| Micro soft. EventGrid/eventSubscriptions | DeliverySuccessCount |  Geleverde gebeurtenissen  | 
| Micro soft. EventGrid/eventSubscriptions | DeadLetteredCount |  Gebeurtenissen met onbestelbare berichten  | 
| Micro soft. EventGrid/extensionTopics | UnmatchedEventCount |  Niet-overeenkomende gebeurtenissen  | 
| Micro soft. EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Latentie van publicatie geslaagd  | 
| Micro soft. EventGrid/extensionTopics | PublishSuccessCount |  Gepubliceerde gebeurtenissen  | 
| Micro soft. EventGrid/extensionTopics | PublishFailCount |  Mislukte gebeurtenissen publiceren  | 
| Micro soft. EventGrid/topics | UnmatchedEventCount |  Niet-overeenkomende gebeurtenissen  | 
| Micro soft. EventGrid/topics | PublishSuccessLatencyInMs |  Latentie van publicatie geslaagd  | 
| Micro soft. EventGrid/topics | PublishSuccessCount |  Gepubliceerde gebeurtenissen  | 
| Micro soft. EventGrid/topics | PublishFailCount |  Mislukte gebeurtenissen publiceren  | 
| Micro soft. EventHub/clusters | OutgoingMessages |  Uitgaande berichten  | 
| Micro soft. EventHub/clusters | OutgoingBytes |  Uitgaande bytes.  | 
| Micro soft. EventHub/clusters | IncomingRequests |  Binnenkomende aanvragen  | 
| Micro soft. EventHub/clusters | IncomingMessages |  Binnenkomende berichten  | 
| Micro soft. EventHub/clusters | IncomingBytes |  Binnenkomende bytes.  | 
| Microsoft.EventHub/namespaces | SVRBSY |  Fouten bij server bezet (afgeschaft)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  Geslaagde aanvragen (afgeschaft)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  Uitgaande berichten (verouderd) (afgeschaft)  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  Uitgaande berichten  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  Uitgaande bytes.  | 
| Microsoft.EventHub/namespaces | MISCERR |  Andere fouten (afgeschaft)  | 
| Microsoft.EventHub/namespaces | INTERer |  Interne server fouten (afgeschaft)  | 
| Microsoft.EventHub/namespaces | INREQS |  Binnenkomende aanvragen (afgeschaft)  | 
| Microsoft.EventHub/namespaces | INMSGS |  Binnenkomende berichten (verouderd) (afgeschaft)  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  Binnenkomende aanvragen  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  Binnenkomende berichten  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  Binnenkomende bytes.  | 
| Microsoft.EventHub/namespaces | FAILREQ |  Mislukte aanvragen (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  Uitgaande berichten (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  Uitgaande bytes (verouderd) (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  Uitgaande bytes (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  Inkomende berichten (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  Binnenkomende bytes (verouderd) (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  Binnenkomende bytes (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  Berichten archiveren (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  Berichten doorvoer archiveren (afgeschaft)  | 
| Microsoft.EventHub/namespaces | EHABL |  Achterstallige berichten archiveren (afgeschaft)  | 
| Micro soft. HDInsight/clusters | NumActiveWorkers |  Aantal actieve werk rollen  | 
| Micro soft. HDInsight/clusters | GatewayRequests |  Gateway aanvragen  | 
| Micro soft. HDInsight/clusters | CategorizedGatewayRequests |  Gecategoriseerde gateway aanvragen  | 
| Micro soft. Insights/AutoscaleSettings | ScaleActionsInitiated |  Schaal acties gestart  | 
| Micro soft. Insights/onderdelen | traceringen/aantal |  Traceringen  | 
| Micro soft. Insights/onderdelen | Performance Counters/requestsPerSecond |  Frequentie van HTTP-aanvragen  | 
| Micro soft. Insights/onderdelen | Performance Counters/requestsInQueue |  HTTP-aanvragen in de toepassings wachtrij  | 
| Micro soft. Insights/onderdelen | Performance Counters/exceptionsPerSecond |  Uitzonderings frequentie  | 
| Micro soft. Insights/onderdelen | Page views/aantal |  Pagina weergaven  | 
| Micro soft. Insights/onderdelen | uitzonde ringen/aantal |  Uitzonderingen  | 
| Micro soft. Kusto/clusters | StreamingIngestResults |  Resultaat streamingopname  | 
| Micro soft. Kusto/clusters | StreamingIngestDuration |  Duur streamingopname  | 
| Micro soft. Kusto/clusters | StreamingIngestDataRate |  Gegevenssnelheid streamingopname  | 
| Micro soft. Kusto/clusters | SteamingIngestRequestRate |  Aanvraagsnelheid streamingopname  | 
| Micro soft. Kusto/clusters | QueryDuration |  Query duur  | 
| Micro soft. Kusto/clusters | KeepAlive |  Actief houden  | 
| Micro soft. Kusto/clusters | IngestionVolumeInMB |  Opname volume (in MB)  | 
| Micro soft. Kusto/clusters | IngestionUtilization |  Opnamegebruik  | 
| Micro soft. Kusto/clusters | IngestionResult |  Opname resultaat  | 
| Micro soft. Kusto/clusters | IngestionLatencyInSeconds |  Opname latentie (in seconden)  | 
| Micro soft. Kusto/clusters | ExportUtilization |  Exportgebruik  | 
| Micro soft. Kusto/clusters | EventsProcessedForEventHubs |  Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs)  | 
| Micro soft. Kusto/clusters | CPU |  CPU  | 
| Micro soft. Kusto/clusters | ContinuousExportResult |  Resultaat doorlopend exporteren  | 
| Micro soft. Kusto/clusters | ContinuousExportPendingCount |  Aantal doorlopend exporteren in behandeling  | 
| Micro soft. Kusto/clusters | ContinuousExportNumOfRecordsExported |  Doorlopend exporteren: aantal geëxporteerde records  | 
| Micro soft. Kusto/clusters | ContinuousExportMaxLatenessMinutes |  Maximale achterstand voor continue export  | 
| Micro soft. Kusto/clusters | CacheUtilization |  Cache gebruik  | 
| Micro soft. Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Trigger beperkings gebeurtenissen  | 
| Micro soft. Logic/integrationServiceEnvironments | TriggersSucceeded |  Geslaagde triggers   | 
| Micro soft. Logic/integrationServiceEnvironments | TriggersStarted |  Triggers gestart   | 
| Micro soft. Logic/integrationServiceEnvironments | TriggersSkipped |  Triggers overgeslagen  | 
| Micro soft. Logic/integrationServiceEnvironments | TriggersFired |  Geactiveerde triggers   | 
| Micro soft. Logic/integrationServiceEnvironments | TriggersFailed |  Mislukte triggers   | 
| Micro soft. Logic/integrationServiceEnvironments | TriggersCompleted |  Triggers voltooid   | 
| Micro soft. Logic/integrationServiceEnvironments | RunThrottledEvents |  Vertraagde gebeurtenissen uitvoeren  | 
| Micro soft. Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Vertraagde gebeurtenissen uitvoeren  | 
| Micro soft. Logic/integrationServiceEnvironments | RunsSucceeded |  Geslaagde uitvoeringen  | 
| Micro soft. Logic/integrationServiceEnvironments | RunsStarted |  Uitvoeringen gestart  | 
| Micro soft. Logic/integrationServiceEnvironments | RunsFailed |  Uitvoeringen mislukt  | 
| Micro soft. Logic/integrationServiceEnvironments | RunsCompleted |  Uitvoeringen voltooid  | 
| Micro soft. Logic/integrationServiceEnvironments | RunsCancelled |  Uitvoeringen geannuleerd  | 
| Micro soft. Logic/integrationServiceEnvironments | RunFailurePercentage |  Percentage mislukte uitvoeringen  | 
| Micro soft. Logic/integrationServiceEnvironments | ActionThrottledEvents |  Door actie vertraagde gebeurtenissen  | 
| Micro soft. Logic/integrationServiceEnvironments | ActionsSucceeded |  Acties geslaagd   | 
| Micro soft. Logic/integrationServiceEnvironments | ActionsStarted |  Gestarte acties   | 
| Micro soft. Logic/integrationServiceEnvironments | ActionsSkipped |  Overgeslagen acties   | 
| Micro soft. Logic/integrationServiceEnvironments | ActionsFailed |  Mislukte acties   | 
| Micro soft. Logic/integrationServiceEnvironments | ActionsCompleted |  Acties voltooid   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  Trigger beperkings gebeurtenissen  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  Geslaagde triggers   | 
| Microsoft.Logic/workflows | TriggersStarted |  Triggers gestart   | 
| Microsoft.Logic/workflows | TriggersSkipped |  Triggers overgeslagen  | 
| Microsoft.Logic/workflows | TriggersFired |  Geactiveerde triggers   | 
| Microsoft.Logic/workflows | TriggersFailed |  Mislukte triggers   | 
| Microsoft.Logic/workflows | TriggersCompleted |  Triggers voltooid   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  Totaal aantal factureer bare uitvoeringen  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  Vertraagde gebeurtenissen uitvoeren  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  Vertraagde gebeurtenissen uitvoeren  | 
| Microsoft.Logic/workflows | RunsSucceeded |  Geslaagde uitvoeringen  | 
| Microsoft.Logic/workflows | RunsStarted |  Uitvoeringen gestart  | 
| Microsoft.Logic/workflows | RunsFailed |  Uitvoeringen mislukt  | 
| Microsoft.Logic/workflows | RunsCompleted |  Uitvoeringen voltooid  | 
| Microsoft.Logic/workflows | RunsCancelled |  Uitvoeringen geannuleerd  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  Percentage mislukte uitvoeringen  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Facturerings gebruik voor uitvoeringen van opslag verbruik  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Facturerings gebruik voor uitvoeringen van opslag verbruik  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Facturerings gebruik voor het uitvoeren van standaard-connectors  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Facturerings gebruik voor het uitvoeren van standaard-connectors  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Facturerings gebruik voor uitvoering van systeem eigen bewerkingen  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Facturerings gebruik voor uitvoering van systeem eigen bewerkingen  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  Factureer bare trigger uitvoeringen  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  Factureer bare actie-uitvoeringen  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  Door actie vertraagde gebeurtenissen  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  Acties geslaagd   | 
| Microsoft.Logic/workflows | ActionsStarted |  Gestarte acties   | 
| Microsoft.Logic/workflows | ActionsSkipped |  Overgeslagen acties   | 
| Microsoft.Logic/workflows | ActionsFailed |  Mislukte acties   | 
| Microsoft.Logic/workflows | ActionsCompleted |  Acties voltooid   | 
| Micro soft. Network/frontdoors | WebApplicationFirewallRequestCount |  Aantal aanvragen voor Web Application firewall  | 
| Micro soft. Network/frontdoors | TotalLatency |  Totale latentie  | 
| Micro soft. Network/frontdoors | ResponseSize |  Grootte van antwoord  | 
| Micro soft. Network/frontdoors | RequestSize |  Aanvraag grootte  | 
| Micro soft. Network/frontdoors | RequestCount |  Aantal aanvragen  | 
| Micro soft. Network/frontdoors | BillableResponseSize |  Grootte van factureer bare antwoorden  | 
| Micro soft. Network/frontdoors | BackendRequestLatency |  Latentie van back-upaanvraag  | 
| Micro soft. Network/frontdoors | BackendRequestCount |  Aantal back-aanvragen  | 
| Micro soft. Network/frontdoors | BackendHealthPercentage |  Back-status percentage  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  Query's op eind punt geretourneerd  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | gepland. in behandeling |  Geplande meldingen in behandeling  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | registratie. update |  Registratie-update bewerkingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | registratie. ophalen |  Lees bewerkingen voor registratie  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | registratie. Delete |  Verwijderings bewerkingen voor registratie  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | registratie. Create |  Bewerkingen voor het maken van registratie  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | registratie. alle |  Registratie bewerkingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. wrongtoken |  WNS-autorisatie fouten (onjuist token)  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. tokenproviderunreachable |  WNS-verificatie fouten (onbereikbaar)  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. throttled |  WNS beperkte meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. wns. geslaagd |  Geslaagde meldingen WNS  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. pnserror |  WNS-fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. invalidtoken |  WNS-verificatie fouten (ongeldig token)  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. invalidnotificationsize |  Fout met ongeldige grootte van WNS-melding  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. invalidnotificationformat |  Ongeldige indeling van WNS-melding  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. invalidcredentials |  WNS-verificatie fouten (ongeldige referenties)  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. expiredchannel |  WNS-fout met verlopen kanaal  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. dropd |  WNS-verwijderde meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. channelthrottled |  WNS-kanaal beperkt  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. channeldisconnected |  Verbinding met WNS-kanaal verbroken  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. badchannel |  WNS ongeldige kanaal fout  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. wns. authenticationerror |  WNS-verificatie fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. throttled |  MPNS beperkte meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. mpns. geslaagd |  Geslaagde meldingen MPNS  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. pnserror |  MPNS-fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. invalidnotificationformat |  Ongeldige indeling van MPNS-melding  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. invalidcredentials |  Ongeldige referenties MPNS  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. dropd |  MPNS-verwijderde meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. channeldisconnected |  Verbinding met MPNS-kanaal verbroken  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. badchannel |  MPNS ongeldige kanaal fout  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. mpns. authenticationerror |  MPNS-verificatie fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. wrongchannel |  GCM onjuiste kanaal fout  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. throttled |  GCM beperkte meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. GCM. geslaagd |  Geslaagde meldingen GCM  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. pnserror |  GCM-fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. invalidnotificationsize |  Fout met ongeldige grootte van GCM-melding  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. invalidnotificationformat |  Ongeldige indeling van GCM-melding  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. invalidcredentials |  GCM-verificatie fouten (ongeldige referenties)  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. expiredchannel |  GCM-fout met verlopen kanaal  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. badchannel |  GCM ongeldige kanaal fout  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. GCM. authenticationerror |  GCM-verificatie fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. apns. geslaagd |  Geslaagde meldingen van APNS  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. apns. pnserror |  APNS-fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. apns. invalidnotificationsize |  Fout door ongeldige grootte van APNS-melding  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. apns. invalidcredentials |  APNS-autorisatie fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. apns. expiredchannel |  Fout bij verlopen van APNS-kanaal  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. apns. badchannel |  Fout met ongeldige APNS-kanaal  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaand. allpns. geslaagd |  Geslaagde meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. allpns. pnserror |  Externe meldingen systeem fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. allpns. invalidpayload |  Payload-fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | uitgaande. allpns. channelerror |  Kanaal fouten  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | notificationhub. pushes |  Alle uitgaande meldingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | installatie. upsert |  Installatie bewerkingen maken of bijwerken  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | installatie. patch |  Patch-installatie bewerkingen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | installatie. Get |  Installatie bewerkingen ophalen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | installatie. Delete |  Installatie bewerkingen verwijderen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | installatie. alle |  Bewerkingen voor installatie beheer  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | Binnenkomend. gepland. annuleren |  Geplande push meldingen geannuleerd  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | inkomend. gepland |  Geplande push meldingen verzonden  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | binnenkomende. alle. aanvragen |  Alle inkomende aanvragen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | inkomend. alle. failedrequests |  Alle binnenkomende mislukte aanvragen  | 
| Micro soft. notification hubs/naam ruimten/notification hubs | e-mail |  Binnenkomende berichten  | 
| Microsoft.OperationalInsights/workspaces | Hartslag |  Hartslag  | 
| Micro soft. relay/naam ruimten | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  Uitgaande berichten  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  Binnenkomende aanvragen  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  Binnenkomende berichten  | 
| Microsoft.SignalRService/SignalR | UserErrors |  Gebruikers fouten  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  Systeem fouten  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  Uitgaand verkeer  | 
| Microsoft.SignalRService/SignalR | MessageCount |  Aantal berichten  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  Binnenkomend verkeer  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  Aantal verbindingen  | 
| Micro soft. SQL/managedInstances | avg_cpu_percent |  Gemiddeld CPU-percentage  | 
| Microsoft.Sql/servers | dtu_used |  DTU gebruikt  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  DTU-percentage  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  Percentage In-Memory OLTP-opslag  | 
| Microsoft.Sql/servers/databases | workers_percent |  Percentage werk nemers  | 
| Microsoft.Sql/servers/databases | sessions_percent |  Percentage sessies  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  Gegevens-I/O-percentage  | 
| Microsoft.Sql/servers/databases | log_write_percent |  Logboek-IO-percentage  | 
| Microsoft.Sql/servers/databases | dwu_used |  DWU gebruikt  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  Percentage DWU  | 
| Microsoft.Sql/servers/databases | dtu_used |  DTU gebruikt  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  DTU-percentage  | 
| Microsoft.Sql/servers/databases | constateer |  Impasses  | 
| Microsoft.Sql/servers/databases | cpu_used |  CPU gebruikt  | 
| Microsoft.Sql/servers/databases | cpu_percent |  CPU-percentage  | 
| Microsoft.Sql/servers/databases | connection_successful |  Geslaagde verbindingen  | 
| Microsoft.Sql/servers/databases | connection_failed |  Mislukte verbindingen  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  Percentage cache treffers  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  Geblokkeerd door firewall  | 
| Micro soft. SQL/servers/elasticPools | xtp_storage_percent |  Percentage In-Memory OLTP-opslag  | 
| Micro soft. SQL/servers/elasticPools | workers_percent |  Percentage werk nemers  | 
| Micro soft. SQL/servers/elasticPools | sessions_percent |  Percentage sessies  | 
| Micro soft. SQL/servers/elasticPools | physical_data_read_percent |  Gegevens-I/O-percentage  | 
| Micro soft. SQL/servers/elasticPools | log_write_percent |  Logboek-IO-percentage  | 
| Micro soft. SQL/servers/elasticPools | eDTU_used |  eDTU gebruikt  | 
| Micro soft. SQL/servers/elasticPools | dtu_consumption_percent |  DTU-percentage  | 
| Micro soft. SQL/servers/elasticPools | cpu_percent |  CPU-percentage  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Totale front-ends  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Werk rollen voor kleine App Service plannen  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Aanvragen |  Aanvragen  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Geheugen percentage  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Werk nemers met gemiddeld App Service plannen  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Werk rollen voor grote App Service plannen  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Lengte van http-wachtrij  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http5xx |  Http-server fouten  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http4xx |  Http-4xx  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http403 |  HTTP 403  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http401 |  HTTP 401  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http3xx |  HTTP-3xx  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http2xx |  Http-2xx  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Wachtrij lengte voor schijf  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU-percentage  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | Bytes sent |  Gegevens uit  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | BytesReceived |  Gegevens in  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Gemiddelde reactie tijd  | 
| Micro soft. Web/hostingEnvironments/multiRolePools | ActiveRequests |  Actieve aanvragen  | 
| Micro soft. Web/hostingEnvironments/workerPools | WorkersUsed |  Gebruikte werk rollen  | 
| Micro soft. Web/hostingEnvironments/workerPools | WorkersTotal |  Totaal aantal werk rollen  | 
| Micro soft. Web/hostingEnvironments/workerPools | WorkersAvailable |  Beschik bare werk nemers  | 
| Micro soft. Web/hostingEnvironments/workerPools | MemoryPercentage |  Geheugen percentage  | 
| Micro soft. Web/hostingEnvironments/workerPools | CpuPercentage |  CPU-percentage  | 
| Micro soft. web/server farms | TcpTimeWait |  Wacht tijd voor TCP-bewerking  | 
| Micro soft. web/server farms | TcpSynSent |  TCP SYN verzonden  | 
| Micro soft. web/server farms | TcpSynReceived |  TCP SYN ontvangen  | 
| Micro soft. web/server farms | TcpLastAck |  TCP laatste ACK  | 
| Micro soft. web/server farms | TcpFinWait2 |  TCP FIN WAIT 2  | 
| Micro soft. web/server farms | TcpFinWait1 |  TCP-FIN-wacht 1  | 
| Micro soft. web/server farms | TcpEstablished |  TCP-verbinding  | 
| Micro soft. web/server farms | TcpClosing |  TCP sluiten  | 
| Micro soft. web/server farms | TcpCloseWait |  TCP-wacht tijd voor sluiten  | 
| Micro soft. web/server farms | MemoryPercentage |  Geheugen percentage  | 
| Micro soft. web/server farms | HttpQueueLength |  Lengte van http-wachtrij  | 
| Micro soft. web/server farms | DiskQueueLength |  Wachtrij lengte voor schijf  | 
| Micro soft. web/server farms | CpuPercentage |  CPU-percentage  | 
| Micro soft. web/server farms | Bytes sent |  Gegevens uit  | 
| Micro soft. web/server farms | BytesReceived |  Gegevens in  | 
| Micro soft. web/sites | TotalAppDomainsUnloaded |  Totaal aantal verwijderde app-domeinen  | 
| Micro soft. web/sites | TotalAppDomains |  Totaal aantal app-domeinen  | 
| Micro soft. web/sites | Lijnen |  Aantal threads  | 
| Micro soft. web/sites | RequestsInApplicationQueue |  Aanvragen in de wachtrij van de toepassing  | 
| Micro soft. web/sites | Aanvragen |  Aanvragen  | 
| Micro soft. web/sites | PrivateBytes |  Privé-bytes  | 
| Micro soft. web/sites | MemoryWorkingSet |  Werkset geheugen  | 
| Micro soft. web/sites | IoWriteOperationsPerSecond |  I/o-schrijf bewerkingen per seconde  | 
| Micro soft. web/sites | IoWriteBytesPerSecond |  I/o-schrijf bewerkingen in bytes per seconde  | 
| Micro soft. web/sites | IoReadOperationsPerSecond |  I/o-Lees bewerkingen per seconde  | 
| Micro soft. web/sites | IoReadBytesPerSecond |  I/o gelezen bytes per seconde  | 
| Micro soft. web/sites | IoOtherOperationsPerSecond |  Andere i/o-bewerkingen per seconde  | 
| Micro soft. web/sites | IoOtherBytesPerSecond |  Andere i/o-bytes per seconde  | 
| Micro soft. web/sites | HttpResponseTime |  Reactie tijd  | 
| Micro soft. web/sites | Http5xx |  Http-server fouten  | 
| Micro soft. web/sites | Http4xx |  Http-4xx  | 
| Micro soft. web/sites | Http406 |  Http 406  | 
| Micro soft. web/sites | Http404 |  Http 404  | 
| Micro soft. web/sites | Http403 |  HTTP 403  | 
| Micro soft. web/sites | Http401 |  HTTP 401  | 
| Micro soft. web/sites | Http3xx |  HTTP-3xx  | 
| Micro soft. web/sites | Http2xx |  Http-2xx  | 
| Micro soft. web/sites | Http101 |  Http 101  | 
| Micro soft. web/sites | HealthCheckStatus |  Status van de status controle  | 
| Micro soft. web/sites | Formuleer |  Aantal ingangen  | 
| Micro soft. web/sites | Gen2Collections |  Opschoon verzamelingen van generatie 2  | 
| Micro soft. web/sites | Gen1Collections |  1 garbagecollection-verzamelingen  | 
| Micro soft. web/sites | Gen0Collections |  Schone verzamelingen van 0 gen  | 
| Micro soft. web/sites | FunctionExecutionUnits |  Eenheden voor functie-uitvoering  | 
| Micro soft. web/sites | FunctionExecutionCount |  Aantal functie-uitvoeringen  | 
| Micro soft. web/sites | CurrentAssemblies |  Huidige Assembly's  | 
| Micro soft. web/sites | CpuTime |  CPU-tijd  | 
| Micro soft. web/sites | Bytes sent |  Gegevens uit  | 
| Micro soft. web/sites | BytesReceived |  Gegevens in  | 
| Micro soft. web/sites | AverageResponseTime |  Gemiddelde reactie tijd  | 
| Micro soft. web/sites | AverageMemoryWorkingSet |  Gemiddelde werkset geheugen  | 
| Micro soft. web/sites | AppConnections |  Verbindingen  | 
| Micro soft. web/sites/sleuven | TotalAppDomainsUnloaded |  Totaal aantal verwijderde app-domeinen  | 
| Micro soft. web/sites/sleuven | TotalAppDomains |  Totaal aantal app-domeinen  | 
| Micro soft. web/sites/sleuven | Lijnen |  Aantal threads  | 
| Micro soft. web/sites/sleuven | RequestsInApplicationQueue |  Aanvragen in de wachtrij van de toepassing  | 
| Micro soft. web/sites/sleuven | Aanvragen |  Aanvragen  | 
| Micro soft. web/sites/sleuven | PrivateBytes |  Privé-bytes  | 
| Micro soft. web/sites/sleuven | MemoryWorkingSet |  Werkset geheugen  | 
| Micro soft. web/sites/sleuven | IoWriteOperationsPerSecond |  I/o-schrijf bewerkingen per seconde  | 
| Micro soft. web/sites/sleuven | IoWriteBytesPerSecond |  I/o-schrijf bewerkingen in bytes per seconde  | 
| Micro soft. web/sites/sleuven | IoReadOperationsPerSecond |  I/o-Lees bewerkingen per seconde  | 
| Micro soft. web/sites/sleuven | IoReadBytesPerSecond |  I/o gelezen bytes per seconde  | 
| Micro soft. web/sites/sleuven | IoOtherOperationsPerSecond |  Andere i/o-bewerkingen per seconde  | 
| Micro soft. web/sites/sleuven | IoOtherBytesPerSecond |  Andere i/o-bytes per seconde  | 
| Micro soft. web/sites/sleuven | HttpResponseTime |  Reactie tijd  | 
| Micro soft. web/sites/sleuven | Http5xx |  Http-server fouten  | 
| Micro soft. web/sites/sleuven | Http4xx |  Http-4xx  | 
| Micro soft. web/sites/sleuven | Http406 |  Http 406  | 
| Micro soft. web/sites/sleuven | Http404 |  Http 404  | 
| Micro soft. web/sites/sleuven | Http403 |  HTTP 403  | 
| Micro soft. web/sites/sleuven | Http401 |  HTTP 401  | 
| Micro soft. web/sites/sleuven | Http3xx |  HTTP-3xx  | 
| Micro soft. web/sites/sleuven | Http2xx |  Http-2xx  | 
| Micro soft. web/sites/sleuven | Http101 |  Http 101  | 
| Micro soft. web/sites/sleuven | HealthCheckStatus |  Status van de status controle  | 
| Micro soft. web/sites/sleuven | Formuleer |  Aantal ingangen  | 
| Micro soft. web/sites/sleuven | Gen2Collections |  Opschoon verzamelingen van generatie 2  | 
| Micro soft. web/sites/sleuven | Gen1Collections |  1 garbagecollection-verzamelingen  | 
| Micro soft. web/sites/sleuven | Gen0Collections |  Schone verzamelingen van 0 gen  | 
| Micro soft. web/sites/sleuven | FunctionExecutionUnits |  Eenheden voor functie-uitvoering  | 
| Micro soft. web/sites/sleuven | FunctionExecutionCount |  Aantal functie-uitvoeringen  | 
| Micro soft. web/sites/sleuven | CurrentAssemblies |  Huidige Assembly's  | 
| Micro soft. web/sites/sleuven | CpuTime |  CPU-tijd  | 
| Micro soft. web/sites/sleuven | Bytes sent |  Gegevens uit  | 
| Micro soft. web/sites/sleuven | BytesReceived |  Gegevens in  | 
| Micro soft. web/sites/sleuven | AverageResponseTime |  Gemiddelde reactie tijd  | 
| Micro soft. web/sites/sleuven | AverageMemoryWorkingSet |  Gemiddelde werkset geheugen  | 
| Micro soft. web/sites/sleuven | AppConnections |  Verbindingen  | 
| Microsoft.Sql/servers/databases | cpu_percent | CPU-percentage | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | Gegevens-I/O-percentage | 
| Microsoft.Sql/servers/databases | log_write_percent | Logboek-IO-percentage | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU-percentage | 
| Microsoft.Sql/servers/databases | connection_successful | Geslaagde verbindingen | 
| Microsoft.Sql/servers/databases | connection_failed | Mislukte verbindingen | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | Geblokkeerd door firewall | 
| Microsoft.Sql/servers/databases | constateer | Impasses | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | Percentage In-Memory OLTP-opslag | 
| Microsoft.Sql/servers/databases | workers_percent | Percentage werk nemers | 
| Microsoft.Sql/servers/databases | sessions_percent | Percentage sessies | 
| Microsoft.Sql/servers/databases | dtu_used | DTU gebruikt | 
| Microsoft.Sql/servers/databases | cpu_used | CPU gebruikt | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | Percentage DWU | 
| Microsoft.Sql/servers/databases | dwu_used | DWU gebruikt | 
| Microsoft.Sql/servers/databases | cache_hit_percent | Percentage cache treffers | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | Toewijzing van werkbelasting groep per systeem percentage | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | Toewijzing van werkbelasting groep op Maxi maal resource percentage | 
| Microsoft.Sql/servers/databases | wlg_active_queries | Actieve query's voor werkbelasting groep | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | Werkbelasting groep in wachtrij geplaatste query's | 
| Microsoft.Sql/servers/databases | active_queries | Actieve query's | 
| Microsoft.Sql/servers/databases | queued_queries | Query's in de wachtrij | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | Time-outs query werkbelasting groep | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | Time-outs query wachtrij groep werk belasting | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | Effectief min resource percentage | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | Effectief cap-resource percentage | 
| Micro soft. SQL/servers/elasticPools | cpu_percent | CPU-percentage | 
| Micro soft. SQL/servers/elasticPools | physical_data_read_percent | Gegevens-I/O-percentage | 
| Micro soft. SQL/servers/elasticPools | log_write_percent | Logboek-IO-percentage | 
| Micro soft. SQL/servers/elasticPools | dtu_consumption_percent | DTU-percentage | 
| Micro soft. SQL/servers/elasticPools | workers_percent | Percentage werk nemers | 
| Micro soft. SQL/servers/elasticPools | sessions_percent | Percentage sessies | 
| Micro soft. SQL/servers/elasticPools | eDTU_used | eDTU gebruikt | 
| Micro soft. SQL/servers/elasticPools | xtp_storage_percent | Percentage In-Memory OLTP-opslag | 
| Microsoft.Sql/servers | dtu_consumption_percent | DTU-percentage | 
| Microsoft.Sql/servers | dtu_used | DTU gebruikt | 
| Micro soft. SQL/managedInstances | avg_cpu_percent | Gemiddeld CPU-percentage | 

