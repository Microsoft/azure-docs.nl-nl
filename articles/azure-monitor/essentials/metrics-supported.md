---
title: Azure Monitor ondersteunde metrische gegevens per resourcetype
description: Lijst met metrische gegevens die beschikbaar zijn voor elk resourcetype met Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/15/2021
ms.author: robb
ms.openlocfilehash: 1091d103428315a065dd1ff9800ce2ad16632df0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600009"
---
# <a name="supported-metrics-with-azure-monitor"></a>Ondersteunde metrische gegevens met Azure Monitor

> [!NOTE]
> Deze lijst wordt grotendeels automatisch gegenereerd. Eventuele wijzigingen in deze lijst via GitHub kunnen zonder waarschuwing worden overschreven. Neem contact op met de auteur van dit artikel voor meer informatie over het maken van permanente updates.

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, waaronder het in kaart brengen van deze gegevens in de portal, het openen ervan via de REST API of het uitvoeren van query's met PowerShell of CLI. 

Dit artikel is een volledige lijst met alle platformmetrieken (dat wil zeggen, automatisch verzameld) die momenteel beschikbaar zijn Azure Monitor geconsolideerde pijplijn voor metrische gegevens van Azure Monitor. Metrische gegevens die zijn gewijzigd of toegevoegd na de datum bovenaan dit artikel, worden mogelijk nog niet hieronder weergegeven. Gebruik de [API-versie 2018-01-01-2018](/rest/api/monitor/metricdefinitions)om de lijst met metrische gegevens op te vragen en er toegang toe te krijgen. Andere metrische gegevens die niet in deze lijst staan, zijn mogelijk beschikbaar in de portal of met verouderde API's.

De metrische gegevens zijn ingedeeld op resourceproviders en resourcetype. Zie Resourceproviders voor [Azure-services](../../azure-resource-manager/management/azure-services-resource-providers.md)voor een lijst met services en de resourceproviders en typen die bij hen horen.  

## <a name="exporting-platform-metrics-to-other-locations"></a>Metrische platformgegevens exporteren naar andere locaties

U kunt de metrische platformgegevens van de Azure Monitor-pijplijn op een van de twee manieren exporteren naar andere locaties.
1. De [metrische gegevens REST API](/rest/api/monitor/metrics/list)
2. Diagnostische [instellingen gebruiken om](../essentials/diagnostic-settings.md) metrische gegevens van het platform door te geven aan 
    - Azure Storage
    - Azure Monitor Logboeken (en dus Log Analytics)
    - Event Hubs: dit is hoe u ze naar niet-Microsoft-systemen kunt halen 

Het gebruik van diagnostische instellingen is de eenvoudigste manier om de metrische gegevens te routen, maar er zijn enkele beperkingen: 

- **Sommige kunnen niet worden geëxporteerd:** alle metrische gegevens kunnen worden geëxporteerd met behulp van de REST API, maar sommige kunnen niet worden geëxporteerd met diagnostische instellingen vanwege de Azure Monitor back-Azure Monitor. In de *onderstaande tabellen ziet* u in de kolom Exportable via diagnostische instellingen welke metrische gegevens op deze manier kunnen worden geëxporteerd.  

- **Multidimensionale metrische gegevens:** het verzenden van multidimensionale metrische gegevens naar andere locaties via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden. *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.

## <a name="guest-os-and-host-os-metrics"></a>Metrische gegevens van gast-besturingssysteem en host-besturingssysteem

> [!WARNING]
> Metrische gegevens voor het gastbesturingssysteem (gastbesturingssysteem) die worden uitgevoerd in Azure Virtual Machines, Service Fabric en Cloud Services worden **hier** NIET vermeld. Metrische gegevens van gastbesturingssystemen moeten worden verzameld via een of meer agents die worden uitgevoerd op of als onderdeel van het gastbesturingssysteem.  Metrische gegevens van het gast-besturingssysteem bevatten prestatiemeters die het CPU-percentage of het geheugengebruik van gasten bijhouden. Beide worden vaak gebruikt voor automatisch schalen of waarschuwingen. 
>
> **De metrische gegevens van het host-besturingssysteem zijn beschikbaar en worden hieronder weergegeven.** Ze zijn niet hetzelfde. De metrische gegevens van het host-besturingssysteem hebben betrekking op de Hyper-V-sessie die als host voor uw gast-besturingssysteemsessie wordt gebruikt. 

> [!TIP]
> De best practice is [](../agents/diagnostics-extension-overview.md) om de Azure Diagnostics-extensie te gebruiken en te configureren om metrische prestatiegegevens van het gast besturingssysteem te verzenden naar dezelfde metrische database Azure Monitor waarin platformmetrieken worden opgeslagen. De extensie routeer metrische gegevens van het gast-besturingssysteem via de [API voor aangepaste metrische](../essentials/metrics-custom-overview.md) gegevens. Vervolgens kunt u grafieken maken, waarschuwingen geven en op andere wijze metrische gegevens van het gast-besturingssysteem gebruiken, zoals metrische platformgegevens. U kunt ook de Log Analytics-agent gebruiken om metrische gegevens van het gastbesturingssysteem te verzenden Azure Monitor logboeken/Log Analytics. Daar kunt u query's uitvoeren op deze metrische gegevens in combinatie met niet-metrische gegevens. 

Zie Monitoring Agents Overview (Overzicht van [bewakingsagents) voor belangrijke aanvullende informatie.](../agents/agents-overview.md)

## <a name="table-formatting"></a>Tabelopmaak

> [!IMPORTANT] 
> Met deze laatste update wordt een nieuwe kolom toegevoegd en de volgorde van de metrische gegevens is alfabetisch. De informatie over de toevoeging betekent dat de onderstaande tabellen mogelijk onderaan een horizontale schuifbalk hebben, afhankelijk van de breedte van het browservenster. Als u denkt dat er informatie ontbreekt, gebruikt u de schuifbalk om de hele tabel te bekijken.
## <a name="microsoftaadiamazureadmetrics"></a>microsoft.aambuam/azureADMetrics

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Count|Gemiddeld|Metrische gegevens van het type azureADMetrics|Geen dimensies|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ChonerCurrentPrice|Yes|Geheugen: Huidige, schone prijs|Count|Gemiddeld|Huidige prijs van geheugen, $/byte/time, genormaliseerd naar 1000.|ServerResourceType|
|ChonerMemoryNonshrinkable|Yes|Geheugen: Schoon geheugen kan niet worden omsinkt|Bytes|Gemiddeld|Hoeveelheid geheugen, in bytes, niet onderhevig aan opschoning door de achtergrondschoonmaker.|ServerResourceType|
|ChonerMemoryShrinkable|Yes|Geheugen: Kleiner geheugen kan worden verkleind|Bytes|Gemiddeld|Hoeveelheid geheugen, in bytes, onderhevig aan opschoning door de achtergrondschooner.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Threads: Opdrachtgroep bezet threads|Count|Gemiddeld|Aantal bezette threads in de opdrachtthreadpool.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Threads: Niet-actieve threads voor de opdrachtgroep|Count|Gemiddeld|Aantal niet-actieve threads in de opdrachtthreadgroep.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Wachtrijlengte van taak in opdrachtgroep|Count|Gemiddeld|Het aantal taken in de wachtrij van de opdrachtthreadgroep.|ServerResourceType|
|CurrentConnections|Yes|Verbinding: Huidige verbindingen|Count|Gemiddeld|Huidig aantal tot stand gebrachte clientverbindingen.|ServerResourceType|
|CurrentUserSessions|Yes|Huidige gebruikerssessies|Count|Gemiddeld|Het huidige aantal gebruikerssessies dat tot stand is gebracht.|ServerResourceType|
|LongParsingBusyThreads|Yes|Threads: Lange parseren van bezette threads|Count|Gemiddeld|Aantal bezette threads in de threadgroep voor lange parsering.|ServerResourceType|
|LongParsingIdleThreads|Yes|Threads: Lang inactieve threads parseren|Count|Gemiddeld|Aantal niet-actieve threads in de threadgroep voor lange parsering.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Threads: lengte van de wachtrij voor lang parseren|Count|Gemiddeld|Het aantal taken in de wachtrij van de threadgroep voor lange parsering.|ServerResourceType|
|mashup_engine_memory_metric|Yes|Geheugen van M-engine|Bytes|Gemiddeld|Geheugengebruik door mashup-engineprocessen|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|Persoonlijke bytes M-engine|Bytes|Gemiddeld|Gebruik van persoonlijke bytes door mashup-engineprocessen.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|M Engine QPU|Count|Gemiddeld|QPU-gebruik door mashup-engine-processen|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|Virtuele bytes van M-engine|Bytes|Gemiddeld|Gebruik van virtuele bytes door mashup-engineprocessen.|ServerResourceType|
|memory_metric|Yes|Geheugen|Bytes|Gemiddeld|Geheugen. Bereik van 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|ServerResourceType|
|memory_thrashing_metric|Yes|Geheugenthrashing|Percentage|Gemiddeld|Gemiddelde geheugenbeperking.|ServerResourceType|
|MemoryLimitHard|Yes|Geheugen: geheugenlimiet is hard|Bytes|Gemiddeld|Limiet voor het harde geheugen, uit het configuratiebestand.|ServerResourceType|
|MemoryLimitHigh|Yes|Geheugen: geheugenlimiet hoog|Bytes|Gemiddeld|Hoge geheugenlimiet, uit configuratiebestand.|ServerResourceType|
|MemoryLimitLow|Yes|Geheugen: Geheugenlimiet laag|Bytes|Gemiddeld|Lage geheugenlimiet, uit configuratiebestand.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Geheugen: Geheugenlimiet VertiPaq|Bytes|Gemiddeld|In-memory limiet, vanuit het configuratiebestand.|ServerResourceType|
|MemoryUsage|Yes|Geheugen: geheugengebruik|Bytes|Gemiddeld|Geheugengebruik van het serverproces, zoals gebruikt bij het berekenen van de geheugenprijs voor een schoner geheugen. Gelijk aan teller Process\PrivateBytes plus de grootte van geheugen toegewezen gegevens, waarbij geheugen wordt genegeerd dat is toegewezen of toegewezen door de xVelocity in-memory analyse-engine (VertiPaq) groter is dan de geheugenlimiet van de xVelocity-engine.|ServerResourceType|
|private_bytes_metric|Yes|Persoonlijke bytes|Bytes|Gemiddeld|Persoonlijke bytes.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Threads: Verwerkingsgroep bezet I/O-taakthreads|Count|Gemiddeld|Aantal threads met I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Threads: Verwerkingsgroep bezet niet-I/O-threads|Count|Gemiddeld|Aantal threads met niet-I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Threads: Inactieve I/O-taakthreads van de verwerkingspool|Count|Gemiddeld|Aantal niet-actieve threads voor I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Threads: Niet-I/O-threads die niet inactief zijn voor de verwerkingspool|Count|Gemiddeld|Het aantal niet-actieve threads in de verwerkingsthreadgroep dat is toegewezen aan niet-I/O-taken.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Threads: Wachtrijlengte voor I/O-taak van verwerkingspool|Count|Gemiddeld|Het aantal I/O-taken in de wachtrij van de verwerkingsthreadpool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Wachtrijlengte voor verwerkingspool|Count|Gemiddeld|Aantal niet-I/O-taken in de wachtrij van de verwerkingsthreadgroep.|ServerResourceType|
|qpu_metric|Yes|QPU|Count|Gemiddeld|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Querygroep bezet threads|Count|Gemiddeld|Aantal bezette threads in de querythreadpool.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Threads: Niet-actieve threads van querypool|Count|Gemiddeld|Aantal niet-actieve threads voor I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Threads: Query pool job queue lengt|Count|Gemiddeld|Het aantal taken in de wachtrij van de querythreadgroep.|ServerResourceType|
|Quota|Yes|Geheugen: quotum|Bytes|Gemiddeld|Huidig geheugenquotum, in bytes. Geheugenquotum wordt ook wel een geheugenreservering of geheugenreservering genoemd.|ServerResourceType|
|Quota geblokkeerd|Yes|Geheugen: Quotum geblokkeerd|Count|Gemiddeld|Huidig aantal quotumaanvragen dat wordt geblokkeerd totdat andere geheugenquota worden vrijgehouden.|ServerResourceType|
|RowsConvertedPerSec|Yes|Verwerken: Rijen geconverteerd per seconde|CountPerSecond|Gemiddeld|De snelheid van rijen die tijdens de verwerking worden geconverteerd.|ServerResourceType|
|RowsReadPerSec|Yes|Verwerken: Rijen die per seconde worden gelezen|CountPerSecond|Gemiddeld|De snelheid van rijen die uit alle relationele databases worden gelezen.|ServerResourceType|
|RowsWrittenPerSec|Yes|Verwerken: Rijen geschreven per seconde|CountPerSecond|Gemiddeld|De snelheid van rijen die zijn geschreven tijdens de verwerking.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Threads: Korte parsering van bezette threads|Count|Gemiddeld|Aantal bezette threads in de korte parseringsthreadgroep.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Threads: Korte parsering van niet-actieve threads|Count|Gemiddeld|Aantal niet-actieve threads in de korte parseringsthreadgroep.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Threads: Lengte van de wachtrij voor korte parseren van een taak|Count|Gemiddeld|Het aantal taken in de wachtrij van de korte parseringsthreadgroep.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Geslaagde verbindingen per seconde|CountPerSecond|Gemiddeld|De snelheid van geslaagde verbindings voltooid.|ServerResourceType|
|TotalConnectionFailures|Yes|Totaal aantal verbindingsfouten|Count|Gemiddeld|Totaal aantal mislukte verbindingspogingen.|ServerResourceType|
|TotalConnectionRequests|Yes|Totaal aantal verbindingsaanvragen|Count|Gemiddeld|Totaal aantal verbindingsaanvragen. Dit zijn aankomsttijden.|ServerResourceType|
|VertiPaqNonpaged|Yes|Geheugen: VertiPaq Nonpaged|Bytes|Gemiddeld|Bytes geheugen die zijn vergrendeld in de werkset voor gebruik door de in-memory engine.|ServerResourceType|
|VertiPaqPaged|Yes|Geheugen: VertiPaq Paged|Bytes|Gemiddeld|Bytes aan geheugen met pagina's die worden gebruikt voor gegevens in het geheugen.|ServerResourceType|
|virtual_bytes_metric|Yes|Virtuele bytes|Bytes|Gemiddeld|Virtuele bytes.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Duur van back-end-aanvragen|Milliseconden|Gemiddeld|Duur van back-aanvraag in milliseconden|Locatie, Hostnaam|
|Capaciteit|Yes|Capaciteit|Percentage|Gemiddeld|Metrische gegevens over gebruik voor ApiManagement-service|Locatie|
|Duur|Yes|Totale duur van gatewayaanvragen|Milliseconden|Gemiddeld|Totale duur van gatewayaanvragen in milliseconden|Locatie, Hostnaam|
|EventHubDroppedEvents|Yes|Uitgevallen EventHub-gebeurtenissen|Aantal|Totaal|Aantal gebeurtenissen dat is overgeslagen omdat de limiet voor de wachtrijgrootte is bereikt|Locatie|
|EventHubRejectedEvents|Yes|Geweigerde EventHub-gebeurtenissen|Aantal|Totaal|Aantal geweigerde EventHub-gebeurtenissen (verkeerde configuratie of niet geautoriseerd)|Locatie|
|EventHubSuccessfulEvents|Yes|Geslaagde EventHub-gebeurtenissen|Aantal|Totaal|Aantal geslaagde EventHub-gebeurtenissen|Locatie|
|EventHubThrottledEvents|Yes|EventHub-gebeurtenissen beperkt|Aantal|Totaal|Aantal beperkt eventhub-gebeurtenissen|Locatie|
|EventHubTimedoutEvents|Yes|EventHub-gebeurtenissen met time-out|Aantal|Totaal|Aantal time-outgebeurtenissen voor EventHub|Locatie|
|EventHubTotalBytesSent|Yes|Grootte van EventHub-gebeurtenissen|Bytes|Totaal|Totale grootte van EventHub-gebeurtenissen in bytes|Locatie|
|EventHubTotalEvents|Yes|Totaal aantal EventHub-gebeurtenissen|Aantal|Totaal|Aantal gebeurtenissen verzonden naar EventHub|Locatie|
|EventHubTotalFailedEvents|Yes|Mislukte EventHub-gebeurtenissen|Aantal|Totaal|Aantal mislukte EventHub-gebeurtenissen|Locatie|
|FailedRequests|Yes|Mislukte gatewayaanvragen (afgeschaft)|Aantal|Totaal|Aantal fouten in gatewayaanvragen: gebruik in plaats daarvan metrische gegevens voor aanvragen met meerdere dimensies met de dimensie GatewayResponseCodeCategory|Locatie, Hostnaam|
|NetworkConnectivity|Yes|Netwerkverbindingsstatus van resources (preview)|Count|Gemiddeld|Netwerkverbindingsstatus van afhankelijke resourcetypen van API Management service|Locatie, ResourceType|
|OtherRequests|Yes|Andere gatewayaanvragen (afgeschaft)|Aantal|Totaal|Aantal andere gatewayaanvragen: gebruik in plaats daarvan metrische gegevens voor aanvragen met meerdere dimensies met de dimensie GatewayResponseCodeCategory|Locatie, Hostnaam|
|Aanvragen|Yes|Aanvragen|Aantal|Totaal|Metrische gegevens van gatewayaanvraag met meerdere dimensies|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Geslaagde gatewayaanvragen (afgeschaft)|Aantal|Totaal|Aantal geslaagde gatewayaanvragen: gebruik in plaats daarvan metrische gegevens voor aanvragen met meerdere dimensies met de dimensie GatewayResponseCodeCategory|Locatie, Hostnaam|
|TotalRequests|Yes|Totaal aantal gatewayaanvragen (afgeschaft)|Aantal|Totaal|Aantal gatewayaanvragen: gebruik in plaats daarvan metrische gegevens voor aanvragen met meerdere dimensies met de dimensie GatewayResponseCodeCategory|Locatie, Hostnaam|
|UnauthorizedRequests|Yes|Niet-geautoriseerde gatewayaanvragen (afgeschaft)|Aantal|Totaal|Aantal niet-geautoriseerde gatewayaanvragen: gebruik in plaats daarvan metrische gegevens voor aanvragen met meerdere dimensies met de dimensie GatewayResponseCodeCategory|Locatie, Hostnaam|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Count|Count|Totaal aantal inkomende HTTP-aanvragen.|StatusCode, verificatie|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Count|Gemiddeld|Latentie bij een HTTP-aanvraag.|StatusCode, verificatie|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Count|Count|BEPERKT HTTP-aanvragen.|Geen dimensies|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active-timer-count|Yes|active-timer-count|Count|Gemiddeld|Aantal timers dat momenteel actief is|Implementatie, AppName, Pod|
|alloc-rate|Yes|alloc-rate|Bytes|Gemiddeld|Het aantal bytes dat is toegewezen in de beheerde heap|Implementatie, AppName, Pod|
|AppCpuUsage|Yes|CPU-gebruik van app |Percentage|Gemiddeld|Het recente CPU-gebruik voor de app|Implementatie, AppName, Pod|
|assembly-count|Yes|assembly-count|Count|Gemiddeld|Aantal geladen assemblies|Implementatie, AppName, Pod|
|cpu-gebruik|Yes|cpu-gebruik|Percentage|Gemiddeld|% tijd dat het proces de CPU heeft gebruikt|Implementatie, AppName, Pod|
|current-requests|Yes|current-requests|Count|Gemiddeld|Totaal aantal aanvragen in verwerking tijdens de levensduur van het proces|Implementatie, AppName, Pod|
|aantal uitzonderingen|Yes|aantal uitzonderingen|Aantal|Totaal|Aantal uitzonderingen|Implementatie, AppName, Pod|
|failed-requests|Yes|failed-requests|Count|Gemiddeld|Totaal aantal mislukte aanvragen tijdens de levensduur van het proces|Implementatie, AppName, Pod|
|gc-heap-size|Yes|gc-heap-size|Count|Gemiddeld|Totale heap-grootte gerapporteerd door de GC (MB)|Implementatie, AppName, Pod|
|gen-0-gc-count|Yes|gen-0-gc-count|Count|Gemiddeld|Aantal GC's van Gen 0|Implementatie, AppName, Pod|
|Gen-0-grootte|Yes|Gen-0-grootte|Bytes|Gemiddeld|Gen 0 Heap-grootte|Implementatie, AppName, Pod|
|gen-1-gc-count|Yes|gen-1-gc-count|Count|Gemiddeld|Aantal Gen 1-GC's|Implementatie, AppName, Pod|
|gen-1-grootte|Yes|gen-1-grootte|Bytes|Gemiddeld|Gen 1 Heap-grootte|Implementatie, AppName, Pod|
|gen-2-gc-count|Yes|gen-2-gc-count|Count|Gemiddeld|Aantal Gen 2 GCs|Implementatie, AppName, Pod|
|Gen-2-grootte|Yes|Gen-2-grootte|Bytes|Gemiddeld|Gen 2 Heap-grootte|Implementatie, AppName, Pod|
|jvm.gc.live.data.size|Yes|jvm.gc.live.data.size|Bytes|Gemiddeld|Grootte van de oude generatie geheugengroep na een volledige GC|Implementatie, AppName, Pod|
|jvm.gc.max.data.size|Yes|jvm.gc.max.data.size|Bytes|Gemiddeld|Maximale grootte van de geheugengroep van de oude generatie|Implementatie, AppName, Pod|
|jvm.gc.memory.allocated|Yes|jvm.gc.memory.allocated|Bytes|Maximum|Verhoogd voor een toename van de grootte van de geheugengroep van de jonge generatie na één GC tot vóór de volgende|Implementatie, AppName, Pod|
|jvm.gc.memory.promoted|Yes|jvm.gc.memory.promoted|Bytes|Maximum|Aantal positieve toenamen in de grootte van de oude generatie geheugengroep vóór GC tot na GC|Implementatie, AppName, Pod|
|jvm.gc.pause.total.count|Yes|jvm.gc.pause.total.count|Aantal|Totaal|Aantal GC-onderbrekingen|Implementatie, AppName, Pod|
|jvm.gc.pause.total.time|Yes|jvm.gc.pause.total.time|Milliseconden|Totaal|Totale tijd GC onderbreken|Implementatie, AppName, Pod|
|jvm.memory.committed|Yes|jvm.memory.committed|Bytes|Gemiddeld|Geheugen toegewezen aan JVM in bytes|Implementatie, AppName, Pod|
|jvm.memory.max|Yes|jvm.memory.max|Bytes|Maximum|De maximale hoeveelheid geheugen in bytes die kan worden gebruikt voor geheugenbeheer|Implementatie, AppName, Pod|
|jvm.memory.used|Yes|jvm.memory.used|Bytes|Gemiddeld|App-geheugen gebruikt in bytes|Implementatie, AppName, Pod|
|loh-grootte|Yes|loh-grootte|Bytes|Gemiddeld|LOH Heap-grootte|Implementatie, AppName, Pod|
|monitor-lock-contention-count|Yes|monitor-lock-contention-count|Count|Gemiddeld|Aantal keren dat er sprake was van een schermvergrendeling tijdens een poging om de monitor te vergrendelen|Implementatie, AppName, Pod|
|process.cpu.usage|Yes|process.cpu.usage|Percentage|Gemiddeld|Het recente CPU-gebruik voor het JVM-proces|Implementatie, AppName, Pod|
|aanvragen per seconde|Yes|aantal aanvragen|Count|Gemiddeld|Aanvraagsnelheid|Implementatie, AppName, Pod|
|system.cpu.usage|Yes|system.cpu.usage|Percentage|Gemiddeld|Het recente CPU-gebruik voor het hele systeem|Implementatie, AppName, Pod|
|threadpool-completed-items-count|Yes|threadpool-completed-items-count|Count|Gemiddeld|Aantal voltooide threadpoolwerkitems|Implementatie, AppName, Pod|
|threadpool-queue-length|Yes|threadpool-queue-length|Count|Gemiddeld|Wachtrijlengte voor ThreadPool-werkitems|Implementatie, AppName, Pod|
|threadpool-thread-count|Yes|threadpool-thread-count|Count|Gemiddeld|Aantal threadpoolthreads|Implementatie, AppName, Pod|
|time-in-gc|Yes|time-in-gc|Percentage|Gemiddeld|% tijd in GC sinds de laatste GC|Implementatie, AppName, Pod|
|tomcat.global.error|Yes|tomcat.global.error|Aantal|Totaal|Tomcat Global Error|Implementatie, AppName, Pod|
|tomcat.global.received|Yes|tomcat.global.received|Bytes|Totaal|Tomcat Total Received Bytes|Implementatie, AppName, Pod|
|tomcat.global.request.avg.time|Yes|tomcat.global.request.avg.time|Milliseconden|Gemiddeld|Gemiddelde tijd van Tomcat-aanvraag|Implementatie, AppName, Pod|
|tomcat.global.request.max|Yes|tomcat.global.request.max|Milliseconden|Maximum|Maximale tijd tomcat-aanvraag|Implementatie, AppName, Pod|
|tomcat.global.request.total.count|Yes|tomcat.global.request.total.count|Aantal|Totaal|Totaal aantal Tomcat-aanvragen|Implementatie, AppName, Pod|
|tomcat.global.request.total.time|Yes|tomcat.global.request.total.time|Milliseconden|Totaal|Totale tijd tomcat-aanvraag|Implementatie, AppName, Pod|
|tomcat.global.sent|Yes|tomcat.global.sent|Bytes|Totaal|Tomcat totaal aantal verzonden bytes|Implementatie, AppName, Pod|
|tomcat.sessions.active.current|Yes|tomcat.sessions.active.current|Aantal|Totaal|Aantal actieve Tomcat-sessies|Implementatie, AppName, Pod|
|tomcat.sessions.active.max|Yes|tomcat.sessions.active.max|Aantal|Totaal|Maximum aantal tomcat-sessies|Implementatie, AppName, Pod|
|tomcat.sessions.alive.max|Yes|tomcat.sessions.alive.max|Milliseconden|Maximum|Tomcat-sessie Max Alive Time|Implementatie, AppName, Pod|
|tomcat.sessions.created|Yes|tomcat.sessions.created|Aantal|Totaal|Aantal aangemaakte Tomcat-sessies|Implementatie, AppName, Pod|
|tomcat.sessions.expired|Yes|tomcat.sessions.expired|Aantal|Totaal|Aantal verlopen Tomcat-sessies|Implementatie, AppName, Pod|
|tomcat.sessions.rejected|Yes|tomcat.sessions.rejected|Aantal|Totaal|Aantal geweigerde Tomcat-sessies|Implementatie, AppName, Pod|
|tomcat.threads.config.max|Yes|tomcat.threads.config.max|Aantal|Totaal|Maximum aantal threads tomcat-configuratie|Implementatie, AppName, Pod|
|tomcat.threads.current|Yes|tomcat.threads.current|Aantal|Totaal|Aantal huidige Tomcat-threads|Implementatie, AppName, Pod|
|totaal aantal aanvragen|Yes|totaal aantal aanvragen|Count|Gemiddeld|Totaal aantal aanvragen tijdens de levensduur van het proces|Implementatie, AppName, Pod|
|working-set|Yes|working-set|Count|Gemiddeld|Hoeveelheid werkset die wordt gebruikt door het proces (MB)|Implementatie, AppName, Pod|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Totaal aantal taken|Aantal|Totaal|Het totale aantal taken|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Yes|Totaal aantal machine-uitvoeringen van update-implementaties|Aantal|Totaal|Totaal aantal software-update-implementatiemachines dat wordt uitgevoerd in een software-update-implementatie|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Totaal aantal uitvoeringen van update-implementaties|Aantal|Totaal|Totaal aantal implementaties van software-updates|SoftwareUpdateConfigurationName, Status|


## <a name="microsoftavsprivateclouds"></a>Microsoft.AVS/privateClouds

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Totale capaciteit van gegevensstoreschijf|Bytes|Gemiddeld|De totale capaciteit van de schijf in de gegevensstore|dsname|
|DiskUsedPercentage|Yes| Percentage datastore-schijf gebruikt|Percentage|Gemiddeld|Percentage beschikbare schijf dat wordt gebruikt in gegevensstore|dsname|
|EffectiveCpuAverage|Yes|Percentage CPU|Percentage|Gemiddeld|Percentage gebruikte CPU-resources in cluster|clusternaam|
|EffectiveMemAverage|Yes|Gemiddeld effectief geheugen|Bytes|Gemiddeld|Totale beschikbare hoeveelheid machinegeheugen in cluster|clusternaam|
|OverheadAverage|Yes|Gemiddelde geheugenoverhead|Bytes|Gemiddeld|Fysiek geheugen hosten dat wordt gebruikt door de virtualisatie-infrastructuur|clusternaam|
|TotalMbAverage|Yes|Gemiddeld totaal geheugen|Bytes|Gemiddeld|Totaal geheugen in cluster|clusternaam|
|UsageAverage|Yes|Gemiddeld geheugengebruik|Percentage|Gemiddeld|Geheugengebruik als percentage van het totale geconfigureerde of beschikbare geheugen|clusternaam|
|UsedLatest|Yes|Datastore-schijf gebruikt|Bytes|Gemiddeld|De totale hoeveelheid schijf die wordt gebruikt in de gegevensstore|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|CoreCount|No|Aantal toegewezen kernen|Aantal|Totaal|Totaal aantal toegewezen kernen in het batchaccount|Geen dimensies|
|CreatingNodeCount|No|Aantal knooppunt maken|Aantal|Totaal|Het aantal knooppunten dat wordt gemaakt|Geen dimensies|
|IdleNodeCount|No|Aantal niet-actieve knooppunt|Aantal|Totaal|Aantal niet-actieve knooppunten|Geen dimensies|
|JobDeleteCompleteEvent|Yes|Taak verwijderen voltooide gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is verwijderd.|jobId|
|JobDeleteStartEvent|Yes|Startgebeurtenissen voor het verwijderen van de taak|Aantal|Totaal|Totaal aantal taken dat is aangevraagd om te worden verwijderd.|jobId|
|JobDisableCompleteEvent|Yes|Taak voltooide gebeurtenissen uitschakelen|Aantal|Totaal|Totaal aantal taken dat is uitgeschakeld.|jobId|
|JobDisableStartEvent|Yes|Taak Startgebeurtenissen uitschakelen|Aantal|Totaal|Totaal aantal taken dat is aangevraagd om te worden uitgeschakeld.|jobId|
|JobStartEvent|Yes|Startgebeurtenissen van taak|Aantal|Totaal|Totaal aantal taken dat is gestart.|jobId|
|JobTerminateCompleteEvent|Yes|Voltooide gebeurtenissen voor het beëindigen van de taak|Aantal|Totaal|Totaal aantal taken dat is beëindigd.|jobId|
|JobTerminateStartEvent|Yes|Startgebeurtenissen voor het beëindigen van de taak|Aantal|Totaal|Totaal aantal taken dat is aangevraagd om te worden beëindigd.|jobId|
|LeavingPoolNodeCount|No|Aantal pool-knooppunt verlaten|Aantal|Totaal|Aantal knooppunten dat de pool verlaat|Geen dimensies|
|LowPriorityCoreCount|No|Aantal kernen met lage prioriteit|Aantal|Totaal|Totaal aantal kernen met lage prioriteit in het batchaccount|Geen dimensies|
|OfflineNodeCount|No|Offline aantal knooppunt|Aantal|Totaal|Aantal offlineknooppunten|Geen dimensies|
|PoolCreateEvent|Yes|Gebeurtenissen voor het maken van een pool|Aantal|Totaal|Totaal aantal pools dat is gemaakt|poolId|
|PoolDeleteCompleteEvent|Yes|Gebeurtenissen pool verwijderen voltooid|Aantal|Totaal|Totaal aantal pool-verwijderde items dat is voltooid|poolId|
|PoolDeleteStartEvent|Yes|Begingebeurtenissen pool verwijderen|Aantal|Totaal|Totaal aantal verwijderde pool dat is gestart|poolId|
|PoolResizeCompleteEvent|Yes|Gebeurtenissen van het poolize-resize Complete|Aantal|Totaal|Totaal aantal pool-grootten dat is voltooid|poolId|
|PoolResizeStartEvent|Yes|Begingebeurtenissen voor het resize van de pool|Aantal|Totaal|Totaal aantal pool-grootten dat is gestart|poolId|
|PreemptedNodeCount|No|Aantal vooraf geprempte knooppunt|Aantal|Totaal|Aantal verwijderde knooppunten|Geen dimensies|
|RebootingNodeCount|No|Aantal knooppunt opnieuw opstarten|Aantal|Totaal|Aantal knooppunten dat opnieuw wordt opgestart|Geen dimensies|
|ReimagingNodeCount|No|Aantal knooppunt opnieuw maken|Aantal|Totaal|Aantal herimagingsknooppunten|Geen dimensies|
|RunningNodeCount|No|Aantal knooppunt uitvoeren|Aantal|Totaal|Aantal actief knooppunten|Geen dimensies|
|StartingNodeCount|No|Aantal knooppunt starten|Aantal|Totaal|Aantal knooppunten dat begint|Geen dimensies|
|StartTaskFailedNodeCount|No|Aantal mislukte knooppunt van begintaak|Aantal|Totaal|Aantal knooppunten waarop de begintaak is mislukt|Geen dimensies|
|TaskCompleteEvent|Yes|Gebeurtenissen taak voltooid|Aantal|Totaal|Totaal aantal taken dat is voltooid|poolId, jobId|
|TaskFailEvent|Yes|Taakgebeurtenissen mislukken|Aantal|Totaal|Totaal aantal taken dat is voltooid met de status Mislukt|poolId, jobId|
|TaskStartEvent|Yes|Begingebeurtenissen van taak|Aantal|Totaal|Totaal aantal taken dat is gestart|poolId, jobId|
|TotalLowPriorityNodeCount|No|Low-Priority aantal knooppunt|Aantal|Totaal|Totaal aantal knooppunten met lage prioriteit in het batchaccount|Geen dimensies|
|TotalNodeCount|No|Aantal toegewezen knooppunt|Aantal|Totaal|Totaal aantal toegewezen knooppunten in het batchaccount|Geen dimensies|
|UnusableNodeCount|No|Onbruikbaar aantal knooppunt|Aantal|Totaal|Aantal onbruikbaar knooppunten|Geen dimensies|
|WaitingForStartTaskNodeCount|No|Wachten op aantal knooppunt voor begintaak|Aantal|Totaal|Aantal knooppunten dat wacht tot de begintaak is voltooid|Geen dimensies|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/werkruimten

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Actieve kernen|Yes|Actieve kernen|Count|Gemiddeld|Aantal actieve kernen|Scenario, ClusterName|
|Actieve knooppunten|Yes|Actieve knooppunten|Count|Gemiddeld|Aantal actief knooppunten|Scenario, ClusterName|
|Niet-actieve kernen|Yes|Niet-actieve kernen|Count|Gemiddeld|Aantal niet-actieve kernen|Scenario, ClusterName|
|Niet-actieve knooppunten|Yes|Niet-actieve knooppunten|Count|Gemiddeld|Aantal niet-actieve knooppunten|Scenario, ClusterName|
|Taak voltooid|Yes|Taak voltooid|Aantal|Totaal|Aantal voltooide taken|Scenario, ClusterName, ResultType|
|Verzonden taak|Yes|Verzonden taak|Aantal|Totaal|Aantal verzonden taken|Scenario, ClusterName|
|Kernen verlaten|Yes|Kernen verlaten|Count|Gemiddeld|Aantal verlatende kernen|Scenario, ClusterName|
|Knooppunten verlaten|Yes|Knooppunten verlaten|Count|Gemiddeld|Aantal verlatende knooppunten|Scenario, ClusterName|
|Preempted Cores|Yes|Preempted Cores|Count|Gemiddeld|Aantal bestaande kernen|Scenario, ClusterName|
|Verwijderde knooppunten|Yes|Verwijderde knooppunten|Count|Gemiddeld|Aantal verwijderde knooppunten|Scenario, ClusterName|
|Percentage quotumgebruik|Yes|Percentage quotumgebruik|Count|Gemiddeld|Percentage van het gebruikte quotum|Scenario, ClusterName, VmFamilyName, VmPriority|
|Totaal aantal kernen|Yes|Totaal aantal kernen|Count|Gemiddeld|Het totale aantal kernen|Scenario, ClusterName|
|Totaal aantal knooppunten|Yes|Totaal aantal knooppunten|Count|Gemiddeld|Het totale aantal knooppunten|Scenario, ClusterName|
|Onbruikbaar kernen|Yes|Onbruikbaar kernen|Count|Gemiddeld|Aantal onbruikbaar kernen|Scenario, ClusterName|
|Onbruikbaar knooppunten|Yes|Onuitsbare knooppunten|Count|Gemiddeld|Aantal onbruikbaar knooppunten|Scenario, ClusterName|

## <a name="microsoftbingaccounts"></a>microsoft.bing/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Geblokkeerde aanroepen|Aantal|Totaal|Aantal aanroepen dat de limiet voor snelheid of quotum heeft overschreden|ApiName, ServingRegion, StatusCode|
|ClientErrors|Yes|Clientfouten|Aantal|Totaal|Aantal aanroepen met een clientfout (HTTP-statuscode 4xx)|ApiName, ServingRegion, StatusCode|
|DataIn|Yes|Gegevens in|Bytes|Totaal|Inkomende aanvraag Content-Length in bytes|ApiName, ServingRegion, StatusCode|
|DataOut|Yes|Gegevens uit|Bytes|Totaal|Uitgaande reactie Content-Length in bytes|ApiName, ServingRegion, StatusCode|
|Latentie|Yes|Latentie|Milliseconden|Gemiddeld|Latentie in milliseconden|ApiName, ServingRegion, StatusCode|
|ServerErrors|Yes|Serverfouten|Aantal|Totaal|Aantal aanroepen met een serverfout (HTTP-statuscode 5xx)|ApiName, ServingRegion, StatusCode|
|SuccessfulCalls|Yes|Geslaagde aanroepen|Aantal|Totaal|Aantal geslaagde aanroepen (HTTP-statuscode 2xx)|ApiName, ServingRegion, StatusCode|
|TotalCalls|Yes|Totaal aantal aanroepen|Aantal|Totaal|Totaal aantal aanroepen|ApiName, ServingRegion, StatusCode|
|TotalErrors|Yes|Totaalaantal fouten|Aantal|Totaal|Aantal aanroepen met een fout (HTTP-statuscode 4xx of 5xx)|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Count|Gemiddeld|Het aantal verwerkte transacties.|Knooppunt, kanaal, type, status|
|ChaincodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|Count|Gemiddeld|Het aantal chaincode-uitvoeringen (Init of Invoke) dat een time-out heeft gehad.|Knooppunt, chaincode|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Count|Gemiddeld|Het aantal mislukte chaincode-lanceringen.|Knooppunt, chaincode|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Count|Gemiddeld|Het aantal chaincode-lanceringen dat een time-out heeft gehad.|Knooppunt, chaincode|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Count|Gemiddeld|Het aantal aanvragen voor de chaincode-shim voltooid.|Knooppunt, type, kanaal, chaincode, geslaagd|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Count|Gemiddeld|Het aantal ontvangen aanvragen voor de chaincode-shim.|Knooppunt, type, kanaal, chaincode|
|ClusterCommegressQueueCapacity|Yes|ClusterCommegressQueueCapacityDisplayName|Count|Gemiddeld|Capaciteit van de wachtrij voor het egress-gebruik.|Knooppunt, host, msg_type, kanaal|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Count|Gemiddeld|De lengte van de wachtrij voor het egress-gedrag.|Knooppunt, host, msg_type, kanaal|
|ClusterCommegressQueueWorkers|Yes|ClusterCommegressQueueWorkersDisplayName|Count|Gemiddeld|Aantal egress queue workers.|Knooppunt, kanaal|
|ClusterCommegressStreamCount|Yes|ClusterCommegressStreamCountDisplayName|Count|Gemiddeld|Aantal stromen naar andere knooppunten.|Knooppunt, kanaal|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Count|Gemiddeld|Aantal TLS-verbindingen met andere knooppunten.|Knooppunt|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Count|Gemiddeld|Aantal stromen van andere knooppunten.|Knooppunt|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Count|Gemiddeld|Aantal berichten dat is uitgevallen.|Knooppunt, host, kanaal|
|ConnectionAccepted|Yes|Geaccepteerde verbindingen|Aantal|Totaal|Geaccepteerde verbindingen|Knooppunt|
|ConnectionActive|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|Knooppunt|
|ConnectionHandled|Yes|Verwerkte verbindingen|Aantal|Totaal|Verwerkte verbindingen|Knooppunt|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Count|Gemiddeld|Het aantal actieve knooppunten in dit kanaal.|Knooppunt, kanaal|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Count|Gemiddeld|Het aantal knooppunten in dit kanaal.|Knooppunt, kanaal|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Count|Gemiddeld|Het bloknummer van het laatste blok dat is vastgelegd.|Knooppunt, kanaal|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Count|Gemiddeld|Het totale aantal ontvangen voorstellen voor transacties van het configuratietype.|Knooppunt, kanaal|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Count|Gemiddeld|De leidinggevende status van het huidige knooppunt: 1 als het de leider anders 0 is.|Knooppunt, kanaal|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Count|Gemiddeld|Het aantal leiderwijzigingen sinds het proces is begonnen.|Knooppunt, kanaal|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Count|Gemiddeld|Het totale aantal ontvangen voorstellen voor transacties van het normale type.|Knooppunt, kanaal|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Count|Gemiddeld|Het aantal mislukte voorstellen.|Knooppunt, kanaal|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Count|Gemiddeld|Het bloknummer van de meest recente momentopname.|Knooppunt, kanaal|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Count|Gemiddeld|De gemiddelde batchgrootte in bytes die naar onderwerpen worden verzonden.|Knooppunt, onderwerp|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Count|Gemiddeld|De gemiddelde compressieverhouding (als percentage) voor onderwerpen.|Knooppunt, onderwerp|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Count|Gemiddeld|Bytes per seconde afgelezen brokers.|Knooppunt, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Count|Gemiddeld|De offset die is opgegeven in de blokmetagegevens van het laatst vastgelegde blok.|Knooppunt, kanaal|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Count|Gemiddeld|Bytes per seconde geschreven naar brokers.|Knooppunt, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Count|Gemiddeld|Het aantal records per seconde dat naar onderwerpen wordt verzonden.|Knooppunt, onderwerp|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Count|Gemiddeld|Het gemiddelde aantal records dat per aanvraag naar onderwerpen wordt verzonden.|Knooppunt, onderwerp|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Count|Gemiddeld|De gemiddelde latentie van aanvragen in ms naar brokers.|Knooppunt, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Count|Gemiddeld|Aanvragen/seconde verzonden naar brokers.|Knooppunt, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Count|Gemiddeld|De gemiddelde aanvraaggrootte in bytes voor brokers.|Knooppunt, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Count|Gemiddeld|Aanvragen/seconde verzonden naar brokers.|Knooppunt, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Count|Gemiddeld|De gemiddelde antwoordgrootte in bytes van brokers.|Knooppunt, broker_id|
|CpuUsagePercentageInBle|Yes|Percentage CPU-gebruik|Percentage|Maximum|Percentage CPU-gebruik|Knooppunt|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Count|Gemiddeld|Het aantal blokken dat door de leveringsservice wordt verzonden.|Knooppunt, kanaal, gefilterd, data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Count|Gemiddeld|Het aantal leveringsaanvragen dat is voltooid.|Knooppunt, kanaal, gefilterd, data_type, geslaagd|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Count|Gemiddeld|Het aantal bezorgingsaanvragen dat is ontvangen.|Knooppunt, kanaal, gefilterd, data_type|
|DeliverStreamsClosed|Yes|DeliverStreamsClosedDisplayName|Count|Gemiddeld|Het aantal GRPC-stromen dat is gesloten voor de leveringsservice.|Knooppunt|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Count|Gemiddeld|Het aantal GRPC-streams dat is geopend voor de leveringsservice.|Knooppunt|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Count|Gemiddeld|Het aantal chaincode-instantiations of upgraden dat is mislukt.|Knooppunt, kanaal, chaincode|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Count|Gemiddeld|Het aantal mislukte voorstellen vanwege dubbele transactie-id.|Knooppunt, kanaal, chaincode|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Count|Gemiddeld|Het aantal mislukte goedkeuringen.|Knooppunt, kanaal, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Count|Gemiddeld|Het aantal voorstellen dat mislukte ACL-controles heeft uitgevoerd.|Knooppunt, kanaal, chaincode|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Count|Gemiddeld|Het aantal mislukte voorstelsimulaties.|Knooppunt, kanaal, chaincode|
|EndorserProposalsReceived|Yes|EndorserProposalsReceivedDisplayName|Count|Gemiddeld|Het aantal ontvangen voorstellen.|Knooppunt|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|Count|Gemiddeld|Het aantal voorstellen dat de initiële validatie is mislukt.|Knooppunt|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|Count|Gemiddeld|Het aantal geslaagde voorstellen.|Knooppunt|
|FabricVersion|Yes|FabricVersionDisplayName|Count|Gemiddeld|De actieve versie van Fabric.|Knooppunt, versie|
|CommMessagesReceived|Yes|CommMessagesReceivedDisplayName|Count|Gemiddeld|Het aantal ontvangen berichten.|Knooppunt|
|CommMessagesSent|Yes|CommMessagesSentDisplayName|Count|Gemiddeld|Aantal verzonden berichten.|Knooppunt|
|CommOverflowCount|Yes|CommOverflowCountDisplayName|Count|Gemiddeld|Aantal uitgaande wachtrijbuffer-overloop.|Knooppunt|
|LeaderElectionLeader|Yes|LeaderElectionLeaderDisplayName|Count|Gemiddeld|Peer is leider (1) of volger (0).|Knooppunt, kanaal|
|MbpsMembershipTotalPeersKnown|Yes|StagesMembershipTotalPeersKnownDisplayName|Count|Gemiddeld|Totaal aantal bekende peers.|Knooppunt, kanaal|
|PayloadBufferSize|Yes|PayloadBufferSizeDisplayName|Count|Gemiddeld|Grootte van de payloadbuffer.|Knooppunt, kanaal|
|16:00 uur|Yes|12:00 UurStateHeightDisplayName|Count|Gemiddeld|Huidige grootboekhoogte.|Knooppunt, kanaal|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Count|Gemiddeld|gRPC-verbindingen gesloten. Open min gesloten is het actieve aantal verbindingen.|Knooppunt|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Count|Gemiddeld|gRPC-verbindingen geopend. Open min gesloten is het actieve aantal verbindingen.|Knooppunt|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Count|Gemiddeld|Het aantal ontvangen stroomberichten.|Knooppunt, service, methode|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Count|Gemiddeld|Het aantal verzonden stroomberichten.|Knooppunt, service, methode|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Count|Gemiddeld|Het aantal stroomaanvragen dat is voltooid.|Knooppunt, service, methode, code|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Count|Gemiddeld|Het aantal niet-ontvangen unaire aanvragen.|Knooppunt, service, methode|
|IOReadBytes|Yes|IO-bytes lezen|Bytes|Totaal|IO-bytes lezen|Knooppunt|
|IOWriteBytes|Yes|IO-bytes schrijven|Bytes|Totaal|IO-bytes schrijven|Knooppunt|
|GrootboekBlockchainHeight|Yes|GrootboekBlockchainHeightDisplayName|Count|Gemiddeld|Hoogte van de keten in blokken.|Knooppunt, kanaal|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Count|Gemiddeld|Aantal verwerkte transacties.|Knooppunt, kanaal, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Count|Gemiddeld|Het aantal logboekgegevens dat is gecontroleerd op het niveau van actieve logboekregistratie.|Knooppunt, niveau|
|LoggingEntriesWritten|Yes|LoggingEntriesWrittenDisplayName|Count|Gemiddeld|Het aantal logboekgegevens dat is geschreven.|Knooppunt, niveau|
|MemoryLimit|Yes|Geheugenlimiet|Bytes|Gemiddeld|Geheugenlimiet|Knooppunt|
|MemoryUsage|Yes|Geheugengebruik|Bytes|Gemiddeld|Geheugengebruik|Knooppunt|
|MemoryUsagePercentageInBle|Yes|Percentage geheugengebruik|Percentage|Gemiddeld|Percentage geheugengebruik|Knooppunt|
|PendingTransactions|Yes|Transacties in behandeling|Count|Gemiddeld|Transacties in behandeling|Knooppunt|
|ProcessedBlocks|Yes|Verwerkte blokken|Aantal|Totaal|Verwerkte blokken|Knooppunt|
|ProcessedTransactions|Yes|Verwerkte transacties|Aantal|Totaal|Verwerkte transacties|Knooppunt|
|QueuedTransactions|Yes|Transacties in de wachtrij|Count|Gemiddeld|Transacties in de wachtrij|Knooppunt|
|RequestHandled|Yes|Verwerkte aanvragen|Aantal|Totaal|Verwerkte aanvragen|Knooppunt|
|StorageUsage|Yes|Opslaggebruik|Bytes|Gemiddeld|Opslaggebruik|Knooppunt|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|Latentie van aanvraag|Milliseconden|Totaal|De tijd die de server nodig heeft om de aanvraag te verwerken|Bewerking, verificatie, protocol, DataCenter|
|RequestsTraffic|Yes|Aanvragen voor verkeer|Percentage|Count|Aantal ingediende aanvragen|Bewerking, Verificatie, Protocol, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Cachetreffers (op basis van instanties)|Aantal|Totaal||ShardId, Poort, Primair|
|allcachemisses|Yes|Cache-missers (op basis van instanties)|Aantal|Totaal||ShardId, Poort, Primair|
|allcacheRead|Yes|Cache lezen (op basis van exemplaar)|BytesPerSecond|Maximum||ShardId, Poort, Primair|
|allcacheWrite|Yes|Cache schrijven (op basis van exemplaar)|BytesPerSecond|Maximum||ShardId, Poort, Primair|
|allconnectedclients|Yes|Verbonden clients (op basis van exemplaren)|Count|Maximum||ShardId, poort, primair|
|allevictedkeys|Yes|Verloren sleutels (op basis van exemplaar)|Aantal|Totaal||ShardId, poort, primair|
|allexpiredkeys|Yes|Verlopen sleutels (op basis van exemplaar)|Aantal|Totaal||ShardId, poort, primair|
|allgetcommands|Yes|Haalt op (op basis van exemplaar)|Aantal|Totaal||ShardId, Poort, Primair|
|alloperationsPerSecond|Yes|Bewerkingen per seconde (op basis van een exemplaar)|Count|Maximum||ShardId, Poort, Primair|
|allserverLoad|Yes|Serverbelasting (op basis van exemplaar)|Percentage|Maximum||ShardId, Poort, Primair|
|allsetcommands|Yes|Sets (op basis van exemplaar)|Aantal|Totaal||ShardId, Poort, Primair|
|alltotalcommandsprocessed|Yes|Totaal aantal bewerkingen (op basis van een exemplaar)|Aantal|Totaal||ShardId, Poort, Primair|
|alltotalkeys|Yes|Totaal aantal sleutels (op basis van exemplaar)|Count|Maximum||ShardId, Poort, Primair|
|allusedmemory|Yes|Gebruikt geheugen (op basis van een exemplaar)|Bytes|Maximum||ShardId, Poort, Primair|
|allusedmemorypercentage|Yes|Percentage gebruikt geheugen (op basis van exemplaar)|Percentage|Maximum||ShardId, Poort, Primair|
|allusedmemoryRss|Yes|Gebruikt geheugen RSS (op basis van een exemplaar)|Bytes|Maximum||ShardId, Poort, Primair|
|cachehits|Yes|Cachetreffers|Aantal|Totaal||ShardId|
|cachehits0|Yes|Cachetreffers (Shard 0)|Aantal|Totaal||Geen dimensies|
|cachehits1|Yes|Cachetreffers (Shard 1)|Aantal|Totaal||Geen dimensies|
|cachehits2|Yes|Cachetreffers (Shard 2)|Aantal|Totaal||Geen dimensies|
|cachehits3|Yes|Cachetreffers (Shard 3)|Aantal|Totaal||Geen dimensies|
|cachehits4|Yes|Cachetreffers (Shard 4)|Aantal|Totaal||Geen dimensies|
|cachehits5|Yes|Cachetreffers (Shard 5)|Aantal|Totaal||Geen dimensies|
|cachehits6|Yes|Cachetreffers (Shard 6)|Aantal|Totaal||Geen dimensies|
|cachehits7|Yes|Cachetreffers (Shard 7)|Aantal|Totaal||Geen dimensies|
|cachehits8|Yes|Cachetreffers (Shard 8)|Aantal|Totaal||Geen dimensies|
|cachehits9|Yes|Cachetreffers (Shard 9)|Aantal|Totaal||Geen dimensies|
|cacheLatency|Yes|Microseconden cachelatentie (preview)|Count|Gemiddeld||ShardId|
|cachemisses|Yes|Cachemissers|Aantal|Totaal||ShardId|
|cachemisses0|Yes|Cache-missers (Shard 0)|Aantal|Totaal||Geen dimensies|
|cachemisses1|Yes|Cache-missers (Shard 1)|Aantal|Totaal||Geen dimensies|
|cachemisses2|Yes|Cache-missers (Shard 2)|Aantal|Totaal||Geen dimensies|
|cachemisses3|Yes|Cache-missers (Shard 3)|Aantal|Totaal||Geen dimensies|
|cachemisses4|Yes|Cache-missers (Shard 4)|Aantal|Totaal||Geen dimensies|
|cachemisses5|Yes|Cache-missers (Shard 5)|Aantal|Totaal||Geen dimensies|
|cachemisses6|Yes|Cache-missers (Shard 6)|Aantal|Totaal||Geen dimensies|
|cachemisses7|Yes|Cache-missers (Shard 7)|Aantal|Totaal||Geen dimensies|
|cachemisses8|Yes|Cache-missers (Shard 8)|Aantal|Totaal||Geen dimensies|
|cachemisses9|Yes|Cache-missers (Shard 9)|Aantal|Totaal||Geen dimensies|
|cachemissrate|Yes|Percentage cache-missers|Percentage|cachemissrate||ShardId|
|cacheRead|Yes|Gelezen uit cache|BytesPerSecond|Maximum||ShardId|
|cacheRead0|Yes|Cache lezen (Shard 0)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead1|Yes|Cache lezen (Shard 1)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead2|Yes|Cache lezen (Shard 2)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead3|Yes|Cache lezen (Shard 3)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead4|Yes|Cache lezen (Shard 4)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead5|Yes|Cache lezen (Shard 5)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead6|Yes|Cache lezen (Shard 6)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead7|Yes|Cache lezen (Shard 7)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead8|Yes|Cache lezen (Shard 8)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead9|Yes|Cache lezen (Shard 9)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite|Yes|Geschreven naar cache|BytesPerSecond|Maximum||ShardId|
|cacheWrite0|Yes|Cache schrijven (Shard 0)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite1|Yes|Schrijven in cache (Shard 1)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite2|Yes|Schrijven in cache (Shard 2)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite3|Yes|Schrijven in cache (Shard 3)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite4|Yes|Schrijven in cache (Shard 4)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite5|Yes|Schrijven in cache (Shard 5)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite6|Yes|Schrijven in cache (Shard 6)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite7|Yes|Schrijven in cache (Shard 7)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite8|Yes|Schrijven in cache (Shard 8)|BytesPerSecond|Maximum||Geen dimensies|
|cacheWrite9|Yes|Schrijven in cache (Shard 9)|BytesPerSecond|Maximum||Geen dimensies|
|connectedclients|Yes|Verbonden clients|Count|Maximum||ShardId|
|connectedclients0|Yes|Verbonden clients (Shard 0)|Count|Maximum||Geen dimensies|
|connectedclients1|Yes|Verbonden clients (Shard 1)|Count|Maximum||Geen dimensies|
|connectedclients2|Yes|Verbonden clients (Shard 2)|Count|Maximum||Geen dimensies|
|connectedclients3|Yes|Verbonden clients (Shard 3)|Count|Maximum||Geen dimensies|
|connectedclients4|Yes|Verbonden clients (Shard 4)|Count|Maximum||Geen dimensies|
|connectedclients5|Yes|Verbonden clients (Shard 5)|Count|Maximum||Geen dimensies|
|connectedclients6|Yes|Verbonden clients (Shard 6)|Count|Maximum||Geen dimensies|
|connectedclients7|Yes|Verbonden clients (Shard 7)|Count|Maximum||Geen dimensies|
|connectedclients8|Yes|Verbonden clients (Shard 8)|Count|Maximum||Geen dimensies|
|connectedclients9|Yes|Verbonden clients (Shard 9)|Count|Maximum||Geen dimensies|
|fouten|Yes|Fouten|Count|Maximum||ShardId, ErrorType|
|evictedkeys|Yes|Verwijderde sleutels|Aantal|Totaal||ShardId|
|evictedkeys0|Yes|Verloren sleutels (Shard 0)|Aantal|Totaal||Geen dimensies|
|evictedkeys1|Yes|Verloren sleutels (Shard 1)|Aantal|Totaal||Geen dimensies|
|evictedkeys2|Yes|Verloren sleutels (Shard 2)|Aantal|Totaal||Geen dimensies|
|evictedkeys3|Yes|Verloren sleutels (Shard 3)|Aantal|Totaal||Geen dimensies|
|evictedkeys4|Yes|Verloren sleutels (Shard 4)|Aantal|Totaal||Geen dimensies|
|evictedkeys5|Yes|Verloren sleutels (Shard 5)|Aantal|Totaal||Geen dimensies|
|evictedkeys6|Yes|Verloren sleutels (Shard 6)|Aantal|Totaal||Geen dimensies|
|evictedkeys7|Yes|Verloren sleutels (Shard 7)|Aantal|Totaal||Geen dimensies|
|evictedkeys8|Yes|Verloren sleutels (Shard 8)|Aantal|Totaal||Geen dimensies|
|evictedkeys9|Yes|Verloren sleutels (Shard 9)|Aantal|Totaal||Geen dimensies|
|expiredkeys|Yes|Verlopen sleutels|Aantal|Totaal||ShardId|
|expiredkeys0|Yes|Verlopen sleutels (Shard 0)|Aantal|Totaal||Geen dimensies|
|expiredkeys1|Yes|Verlopen sleutels (Shard 1)|Aantal|Totaal||Geen dimensies|
|expiredkeys2|Yes|Verlopen sleutels (Shard 2)|Aantal|Totaal||Geen dimensies|
|expiredkeys3|Yes|Verlopen sleutels (Shard 3)|Aantal|Totaal||Geen dimensies|
|expiredkeys4|Yes|Verlopen sleutels (Shard 4)|Aantal|Totaal||Geen dimensies|
|expiredkeys5|Yes|Verlopen sleutels (Shard 5)|Aantal|Totaal||Geen dimensies|
|expiredkeys6|Yes|Verlopen sleutels (Shard 6)|Aantal|Totaal||Geen dimensies|
|expiredkeys7|Yes|Verlopen sleutels (Shard 7)|Aantal|Totaal||Geen dimensies|
|expiredkeys8|Yes|Verlopen sleutels (Shard 8)|Aantal|Totaal||Geen dimensies|
|expiredkeys9|Yes|Verlopen sleutels (Shard 9)|Aantal|Totaal||Geen dimensies|
|getcommands|Yes|Ophalingen|Aantal|Totaal||ShardId|
|getcommands0|Yes|Haalt op (Shard 0)|Aantal|Totaal||Geen dimensies|
|getcommands1|Yes|Gets (Shard 1)|Aantal|Totaal||Geen dimensies|
|getcommands2|Yes|Gets (Shard 2)|Aantal|Totaal||Geen dimensies|
|getcommands3|Yes|Haalt op (Shard 3)|Aantal|Totaal||Geen dimensies|
|getcommands4|Yes|Haalt (Shard 4)|Aantal|Totaal||Geen dimensies|
|getcommands5|Yes|Haalt op (Shard 5)|Aantal|Totaal||Geen dimensies|
|getcommands6|Yes|Gets (Shard 6)|Aantal|Totaal||Geen dimensies|
|getcommands7|Yes|Gets (Shard 7)|Aantal|Totaal||Geen dimensies|
|getcommands8|Yes|Gets (Shard 8)|Aantal|Totaal||Geen dimensies|
|getcommands9|Yes|Gets (Shard 9)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond|Yes|Bewerkingen per seconde|Count|Maximum||ShardId|
|operationsPerSecond0|Yes|Bewerkingen per seconde (Shard 0)|Count|Maximum||Geen dimensies|
|operationsPerSecond1|Yes|Bewerkingen per seconde (Shard 1)|Count|Maximum||Geen dimensies|
|operationsPerSecond2|Yes|Bewerkingen per seconde (Shard 2)|Count|Maximum||Geen dimensies|
|operationsPerSecond3|Yes|Bewerkingen per seconde (Shard 3)|Count|Maximum||Geen dimensies|
|operationsPerSecond4|Yes|Bewerkingen per seconde (Shard 4)|Count|Maximum||Geen dimensies|
|operationsPerSecond5|Yes|Bewerkingen per seconde (Shard 5)|Count|Maximum||Geen dimensies|
|operationsPerSecond6|Yes|Bewerkingen per seconde (Shard 6)|Count|Maximum||Geen dimensies|
|operationsPerSecond7|Yes|Bewerkingen per seconde (Shard 7)|Count|Maximum||Geen dimensies|
|operationsPerSecond8|Yes|Bewerkingen per seconde (Shard 8)|Count|Maximum||Geen dimensies|
|operationsPerSecond9|Yes|Bewerkingen per seconde (Shard 9)|Count|Maximum||Geen dimensies|
|percentProcessorTime|Yes|CPU|Percentage|Maximum||ShardId|
|percentProcessorTime0|Yes|CPU (Shard 0)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime1|Yes|CPU (Shard 1)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime2|Yes|CPU (Shard 2)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime3|Yes|CPU (Shard 3)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime4|Yes|CPU (Shard 4)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime5|Yes|CPU (Shard 5)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime6|Yes|CPU (Shard 6)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime7|Yes|CPU (Shard 7)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime8|Yes|CPU (Shard 8)|Percentage|Maximum||Geen dimensies|
|percentProcessorTime9|Yes|CPU (Shard 9)|Percentage|Maximum||Geen dimensies|
|serverLoaden|Yes|Serverbelasting|Percentage|Maximum||ShardId|
|serverLoad0|Yes|Serverbelasting (Shard 0)|Percentage|Maximum||Geen dimensies|
|serverLoad1|Yes|Server laden (Shard 1)|Percentage|Maximum||Geen dimensies|
|serverLoad2|Yes|Serverbelasting (Shard 2)|Percentage|Maximum||Geen dimensies|
|serverLoad3|Yes|Serverbelasting (Shard 3)|Percentage|Maximum||Geen dimensies|
|serverLoad4|Yes|Serverbelasting (Shard 4)|Percentage|Maximum||Geen dimensies|
|serverLoad5|Yes|Serverbelasting (Shard 5)|Percentage|Maximum||Geen dimensies|
|serverLoad6|Yes|Serverbelasting (Shard 6)|Percentage|Maximum||Geen dimensies|
|serverLoad7|Yes|Serverbelasting (Shard 7)|Percentage|Maximum||Geen dimensies|
|serverLoad8|Yes|Serverbelasting (Shard 8)|Percentage|Maximum||Geen dimensies|
|serverLoad9|Yes|Serverbelasting (Shard 9)|Percentage|Maximum||Geen dimensies|
|setcommands|Yes|Sets|Aantal|Totaal||ShardId|
|setcommands0|Yes|Sets (Shard 0)|Aantal|Totaal||Geen dimensies|
|setcommands1|Yes|Sets (Shard 1)|Aantal|Totaal||Geen dimensies|
|setcommands2|Yes|Sets (Shard 2)|Aantal|Totaal||Geen dimensies|
|setcommands3|Yes|Sets (Shard 3)|Aantal|Totaal||Geen dimensies|
|setcommands4|Yes|Sets (Shard 4)|Aantal|Totaal||Geen dimensies|
|setcommands5|Yes|Sets (Shard 5)|Aantal|Totaal||Geen dimensies|
|setcommands6|Yes|Sets (Shard 6)|Aantal|Totaal||Geen dimensies|
|setcommands7|Yes|Sets (Shard 7)|Aantal|Totaal||Geen dimensies|
|setcommands8|Yes|Sets (Shard 8)|Aantal|Totaal||Geen dimensies|
|setcommands9|Yes|Sets (Shard 9)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed|Yes|Totaalaantal bewerkingen|Aantal|Totaal||ShardId|
|totalcommandsprocessed0|Yes|Totaal aantal bewerkingen (Shard 0)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed1|Yes|Totaal aantal bewerkingen (Shard 1)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed2|Yes|Totaal aantal bewerkingen (Shard 2)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed3|Yes|Totaal aantal bewerkingen (Shard 3)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed4|Yes|Totaal aantal bewerkingen (Shard 4)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed5|Yes|Totaal aantal bewerkingen (Shard 5)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed6|Yes|Totaal aantal bewerkingen (Shard 6)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed7|Yes|Totaal aantal bewerkingen (Shard 7)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed8|Yes|Totaal aantal bewerkingen (Shard 8)|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed9|Yes|Totaal aantal bewerkingen (Shard 9)|Aantal|Totaal||Geen dimensies|
|totalkeys|Yes|Totaal aantal sleutels|Count|Maximum||ShardId|
|totalkeys0|Yes|Totaal aantal sleutels (Shard 0)|Count|Maximum||Geen dimensies|
|totalkeys1|Yes|Totaal aantal sleutels (Shard 1)|Count|Maximum||Geen dimensies|
|totalkeys2|Yes|Totaal aantal sleutels (Shard 2)|Count|Maximum||Geen dimensies|
|totalkeys3|Yes|Totaal aantal sleutels (Shard 3)|Count|Maximum||Geen dimensies|
|totalkeys4|Yes|Totaal aantal sleutels (Shard 4)|Count|Maximum||Geen dimensies|
|totalkeys5|Yes|Totaal aantal sleutels (Shard 5)|Count|Maximum||Geen dimensies|
|totalkeys6|Yes|Totaal aantal sleutels (Shard 6)|Count|Maximum||Geen dimensies|
|totalkeys7|Yes|Totaal aantal sleutels (Shard 7)|Count|Maximum||Geen dimensies|
|totalkeys8|Yes|Totaal aantal sleutels (Shard 8)|Count|Maximum||Geen dimensies|
|totalkeys9|Yes|Totaal aantal sleutels (Shard 9)|Count|Maximum||Geen dimensies|
|usedmemory|Yes|Gebruikt geheugen|Bytes|Maximum||ShardId|
|usedmemory0|Yes|Gebruikt geheugen (Shard 0)|Bytes|Maximum||Geen dimensies|
|usedmemory1|Yes|Gebruikt geheugen (Shard 1)|Bytes|Maximum||Geen dimensies|
|usedmemory2|Yes|Gebruikt geheugen (Shard 2)|Bytes|Maximum||Geen dimensies|
|usedmemory3|Yes|Gebruikt geheugen (Shard 3)|Bytes|Maximum||Geen dimensies|
|usedmemory4|Yes|Gebruikt geheugen (Shard 4)|Bytes|Maximum||Geen dimensies|
|usedmemory5|Yes|Gebruikt geheugen (Shard 5)|Bytes|Maximum||Geen dimensies|
|usedmemory6|Yes|Gebruikt geheugen (Shard 6)|Bytes|Maximum||Geen dimensies|
|usedmemory7|Yes|Gebruikt geheugen (Shard 7)|Bytes|Maximum||Geen dimensies|
|usedmemory8|Yes|Gebruikt geheugen (Shard 8)|Bytes|Maximum||Geen dimensies|
|usedmemory9|Yes|Gebruikt geheugen (Shard 9)|Bytes|Maximum||Geen dimensies|
|usedmemorypercentage|Yes|Percentage gebruikt geheugen|Percentage|Maximum||ShardId|
|usedmemoryRss|Yes|Gebruikt geheugen RSS|Bytes|Maximum||ShardId|
|usedmemoryRss0|Yes|Gebruikt geheugen RSS (Shard 0)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss1|Yes|Gebruikt geheugen RSS (Shard 1)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss2|Yes|Gebruikt geheugen RSS (Shard 2)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss3|Yes|Gebruikt geheugen RSS (Shard 3)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss4|Yes|Gebruikt geheugen RSS (Shard 4)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss5|Yes|Gebruikt geheugen RSS (Shard 5)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss6|Yes|Gebruikt geheugen RSS (Shard 6)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss7|Yes|Gebruikt geheugen RSS (Shard 7)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss8|Yes|Gebruikt geheugen RSS (Shard 8)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss9|Yes|Gebruikt geheugen RSS (Shard 9)|Bytes|Maximum||Geen dimensies|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|cachehits|Yes|Cachetreffers|Aantal|Totaal||Geen dimensies|
|cacheLatency|Yes|Microseconden cachelatentie (preview)|Count|Gemiddeld||InstanceId|
|cachemisses|Yes|Cachemissers|Aantal|Totaal||InstanceId|
|cacheRead|Yes|Gelezen uit cache|BytesPerSecond|Maximum||InstanceId|
|cacheWrite|Yes|Geschreven naar cache|BytesPerSecond|Maximum||InstanceId|
|connectedclients|Yes|Verbonden clients|Count|Maximum||InstanceId|
|fouten|Yes|Fouten|Count|Maximum||InstanceId, ErrorType|
|evictedkeys|Yes|Verwijderde sleutels|Aantal|Totaal||Geen dimensies|
|expiredkeys|Yes|Verlopen sleutels|Aantal|Totaal||Geen dimensies|
|getcommands|Yes|Ophalingen|Aantal|Totaal||Geen dimensies|
|operationsPerSecond|Yes|Bewerkingen per seconde|Count|Maximum||Geen dimensies|
|percentProcessorTime|Yes|CPU|Percentage|Maximum||InstanceId|
|serverLoaden|Yes|Serverbelasting|Percentage|Maximum||Geen dimensies|
|setcommands|Yes|Sets|Aantal|Totaal||Geen dimensies|
|totalcommandsprocessed|Yes|Totaalaantal bewerkingen|Aantal|Totaal||Geen dimensies|
|totalkeys|Yes|Totaal aantal sleutels|Count|Maximum||Geen dimensies|
|usedmemory|Yes|Gebruikt geheugen|Bytes|Maximum||Geen dimensies|
|usedmemorypercentage|Yes|Percentage gebruikt geheugen|Percentage|Maximum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall aantal aanvragen|Aantal|Totaal|Het aantal clientaanvragen dat door de Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Verhouding byte treffers|Percentage|Gemiddeld|Dit is de verhouding van het totale aantal bytes dat vanuit de cache wordt bediend in vergelijking met het totale aantal antwoord-bytes|Eindpunt|
|OriginHealthPercentage|Yes|Statuspercentage van oorsprong|Percentage|Gemiddeld|Het percentage geslaagde statustests van AFDX naar back-enden.|Origin, OriginGroup|
|OriginLatency|Yes|Oorspronglatentie|Milliseconden|Gemiddeld|De tijd die wordt berekend op basis van het moment waarop de aanvraag door AFDX Edge naar de back-end werd verzonden tot AFDX de laatste antwoord-byte van de back-end heeft ontvangen.|Oorsprong, eindpunt|
|OriginRequestCount|Yes|Aantal origin-aanvragen|Aantal|Totaal|Het aantal aanvragen dat van AFDX naar de oorsprong wordt verzonden.|HttpStatus, HttpStatusGroup, Origin, Endpoint|
|Percentage4XX|Yes|Percentage van 4XX|Percentage|Gemiddeld|Het percentage van alle clientaanvragen waarvoor de antwoordstatuscode 4XX is|Eindpunt, ClientRegion, ClientCountry|
|Percentage5XX|Yes|Percentage van 5XX|Percentage|Gemiddeld|Het percentage van alle clientaanvragen waarvoor de antwoordstatuscode 5XX is|Eindpunt, ClientRegion, ClientCountry|
|RequestCount|Yes|Aantal aanvragen|Aantal|Totaal|Het aantal clientaanvragen dat wordt ingediend door de HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Eindpunt|
|RequestSize|Yes|Aanvraaggrootte|Bytes|Totaal|Het aantal bytes dat als aanvragen van clients naar AFDX wordt verzonden.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Eindpunt|
|ResponseSize|Yes|Antwoordgrootte|Bytes|Totaal|Het aantal bytes verzonden als antwoorden van HTTP/S-proxy naar clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Eindpunt|
|TotalLatency|Yes|Totale latentie|Milliseconden|Gemiddeld|De tijd die is berekend op basis van het moment waarop de clientaanvraag is ontvangen door de HTTP/S-proxy totdat de client de laatste antwoord-byte van de HTTP/S-proxy heeft bevestigd|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Eindpunt|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall aantal aanvragen|Aantal|Totaal|Het aantal clientaanvragen dat door de Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Gelezen bytes per seconde op schijf|No|Schijf lezen|BytesPerSecond|Gemiddeld|Gemiddeld aantal bytes dat tijdens de bewakingsperiode van de schijf wordt gelezen.|RoleInstanceId|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|Lees-IOPS op schijf.|RoleInstanceId|
|Geschreven bytes per seconde schijf|No|Schijf schrijven|BytesPerSecond|Gemiddeld|Gemiddeld aantal bytes dat tijdens de bewakingsperiode naar de schijf wordt geschreven.|RoleInstanceId|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijf schrijven.|RoleInstanceId|
|Netwerk in|Yes|Netwerk in|Bytes|Totaal|Het aantal bytes dat op alle netwerkinterfaces wordt ontvangen door de virtuele machine(s) (inkomend verkeer).|RoleInstanceId|
|Netwerk uit|Yes|Netwerk uit|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer).|RoleInstanceId|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s).|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Gelezen bytes per seconde op schijf|No|Schijf lezen|BytesPerSecond|Gemiddeld|Gemiddeld aantal bytes dat tijdens de bewakingsperiode van de schijf wordt gelezen.|Geen dimensies|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor lezen van schijf.|Geen dimensies|
|Geschreven bytes per seconde voor schijf|No|Schijf schrijven|BytesPerSecond|Gemiddeld|Gemiddeld aantal bytes dat tijdens de bewakingsperiode naar de schijf wordt geschreven.|Geen dimensies|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijfschrijven.|Geen dimensies|
|Netwerk in|Yes|Netwerk in|Bytes|Totaal|Het aantal bytes dat op alle netwerkinterfaces wordt ontvangen door de virtuele machine(s) (binnenkomend verkeer).|Geen dimensies|
|Netwerk uit|Yes|Netwerk uit|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer).|Geen dimensies|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s).|Geen dimensies|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, verificatie|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uit te geven gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, verificatie|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, verificatie|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De latentie die wordt gebruikt Azure Storage voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, verificatie|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|No|Gebruikte capaciteit|Bytes|Gemiddeld|Gebruikte capaciteit van account|Geen dimensies|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|
|BlobCapacity|No|Blobcapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de opslagaccounts Blob service in bytes.|BlobType, Laag|
|BlobCount|No|Aantal blobs|Count|Gemiddeld|Het aantal blobs in de opslagaccount-Blob service.|BlobType, Laag|
|ContainerCount|Yes|Aantal blobcontainers|Count|Gemiddeld|Het aantal containers in de opslagaccount-Blob service.|Geen dimensies|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid gegevens van het egress, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, verificatie|
|IndexCapacity|No|Indexcapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door ADLS Gen2 (hiërarchische) index in bytes.|Geen dimensies|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, verificatie|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De latentie die wordt gebruikt Azure Storage voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, verificatie|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication, FileShare|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid gegevens van het egress, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|No|Bestandscapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de bestandsservice van het opslagaccount in bytes.|Bestandsshare|
|FileCount|No|Aantal bestanden|Count|Gemiddeld|Het aantal bestanden in de bestandsservice van het opslagaccount.|Bestandsshare|
|FileShareCount|No|Aantal bestandsdeels|Count|Gemiddeld|Het aantal bestands shares in de bestandsservice van het opslagaccount.|Geen dimensies|
|FileShareQuota|No|Quotumgrootte bestandsdeel|Bytes|Gemiddeld|De bovengrens voor de hoeveelheid opslag die kan worden gebruikt door Azure Files Service in bytes.|Bestandsshare|
|FileShareSnapshotCount|No|Aantal momentopnamen van bestands share|Count|Gemiddeld|Het aantal momentopnamen dat aanwezig is op de share in de Files Service van het opslagaccount.|Bestandsshare|
|FileShareSnapshotSize|No|Grootte van momentopname van bestands share|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de momentopnamen in de bestandsservice van het opslagaccount in bytes.|Bestandsshare|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie, FileShare|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De latentie die wordt gebruikt Azure Storage voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie, FileShare|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid gegevens van het egress, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Yes|Wachtrijcapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de opslagaccounts Queue-service in bytes.|Geen dimensies|
|QueueCount|Yes|Aantal wachtrijen|Count|Gemiddeld|Het aantal wachtrijen in de Queue-service.|Geen dimensies|
|QueueMessageCount|Yes|Aantal wachtrij-berichten|Count|Gemiddeld|Het geschatte aantal wachtrijberichten in de Queue-service.|Geen dimensies|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, verificatie|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De latentie die wordt gebruikt Azure Storage voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, verificatie|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, verificatie|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uit te geven gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, verificatie|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De latentie die wordt gebruikt Azure Storage om een geslaagde aanvraag te verwerken, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Yes|Tabelcapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de Table-service van het opslagaccount in bytes.|Geen dimensies|
|TableCount|Yes|Aantal tabel|Count|Gemiddeld|Het aantal tabel in de tabelservice van het opslagaccount.|Geen dimensies|
|TableEntityCount|Yes|Aantal tabelentiteiten|Count|Gemiddeld|Het aantal tabelentiteiten in de tabelservice van het opslagaccount.|Geen dimensies|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Geblokkeerde aanroepen|Aantal|Totaal|Aantal aanroepen dat de snelheid of quotumlimiet heeft overschreden.|ApiName, OperationName, Region|
|TekensGetraind|Yes|Getrainde tekens|Aantal|Totaal|Totaal aantal getrainde tekens.|ApiName, OperationName, Region|
|Tekenstranslated|Yes|Vertaalde tekens|Aantal|Totaal|Totaal aantal tekens in binnenkomende tekstaanvraag.|ApiName, OperationName, Region|
|ClientErrors|Yes|Clientfouten|Aantal|Totaal|Aantal aanroepen met fout aan clientzijde (HTTP-antwoordcode 4xx).|ApiName, OperationName, Region|
|DataIn|Yes|Gegevens in|Bytes|Totaal|Grootte van binnenkomende gegevens in bytes.|ApiName, OperationName, Region|
|DataOut|Yes|Gegevens uit|Bytes|Totaal|Grootte van uitgaande gegevens in bytes.|ApiName, OperationName, Region|
|Latentie|Yes|Latentie|Milliseconden|Gemiddeld|Latentie in milliseconden.|ApiName, OperationName, Region|
|LearnedEvents|Yes|Geleerde gebeurtenissen|Aantal|Totaal|Aantal geleerde gebeurtenissen.|IsMatchBaseline, Mode, RunId|
|MatchedRewards|Yes|Overeenkomende beloningen|Aantal|Totaal| Aantal overeenkomende beloningen.|IsMatchBaseline, Mode, RunId|
|ObservedRewards|Yes|Waargenomen beloningen|Aantal|Totaal|Aantal waargenomen beloningen.|IsMatchBaseline, Mode, RunId|
|ProcessedCharacters|Yes|Verwerkte tekens|Aantal|Totaal|Aantal tekens.|ApiName, FeatureName, UsageChannel, Region|
|ProcessedTextRecords|Yes|Verwerkte tekstrecords|Aantal|Totaal|Aantal tekstrecords.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors|Yes|Serverfouten|Aantal|Totaal|Aantal aanroepen met interne servicefout (HTTP-antwoordcode 5xx).|ApiName, OperationName, Region|
|SpeechSessionDuration|Yes|Duur van de spraaksessie|Seconden|Totaal|Totale duur van de spraaksessie in seconden.|ApiName, OperationName, Region|
|SuccessfulCalls|Yes|Geslaagde aanroepen|Aantal|Totaal|Aantal geslaagde aanroepen.|ApiName, OperationName, Region|
|TotalCalls|Yes|Totaal aantal aanroepen|Aantal|Totaal|Totaal aantal aanroepen.|ApiName, OperationName, Region|
|TotalErrors|Yes|Totaalaantal fouten|Aantal|Totaal|Totaal aantal aanroepen met foutreactie (HTTP-antwoordcode 4xx of 5xx).|ApiName, OperationName, Region|
|TotalTokenCalls|Yes|Totaal aantal token-aanroepen|Aantal|Totaal|Totaal aantal token-aanroepen.|ApiName, OperationName, Region|
|TotalTransactions|Yes|Totaal aantal transacties|Aantal|Totaal|Totaal aantal transacties.|Geen dimensies|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Verificatie-API-aanvragen|Count|Count|Telling van alle aanvragen op basis van Communication Services verificatie-eindpunt.|Bewerking, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Chat-API-aanvragen|Count|Count|Telling van alle aanvragen voor het Communication Services Chat-eindpunt.|Bewerking, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|sms-API aanvragen|Count|Count|Telling van alle aanvragen op het Communication Services sms-eindpunt.|Bewerking, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Gelezen bytes op de schijf|Yes|Gelezen bytes op de schijf|Bytes|Totaal|Bytes gelezen van schijf tijdens bewakingsperiode|RoleInstanceId, RoleId|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor lezen van schijf|RoleInstanceId, RoleId|
|Geschreven bytes op de schijf|Yes|Geschreven bytes op de schijf|Bytes|Totaal|Bytes geschreven naar schijf tijdens bewakingsperiode|RoleInstanceId, RoleId|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijf schrijven|RoleInstanceId, RoleId|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s)|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Gelezen bytes op de schijf|Yes|Gelezen bytes op de schijf|Bytes|Totaal|Bytes die tijdens de bewakingsperiode van de schijf worden gelezen|RoleInstanceId, RoleId|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor lezen van schijf|RoleInstanceId, RoleId|
|Geschreven bytes op de schijf|Yes|Geschreven bytes op de schijf|Bytes|Totaal|Bytes geschreven naar schijf tijdens bewakingsperiode|RoleInstanceId, RoleId|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijf schrijven|RoleInstanceId, RoleId|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s)|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Lees bytes per seconde samengestelde schijf|No|Gelezen bytes per seconde schijf (preview)|Bytes|Gemiddeld|Bytes per seconde gelezen van schijf tijdens bewakingsperiode. Houd er rekening mee dat deze metrische gegevens in preview zijn en onderhevig zijn aan veranderingen voordat deze algemeen beschikbaar komen||
|Leesbewerkingen samengestelde schijf per seconde|No|Leesbewerkingen op schijf per seconde (preview)|Bytes|Gemiddeld|Het aantal lees-IO's dat tijdens de bewakingsperiode op een schijf wordt uitgevoerd. Deze metrische gegevens zijn in preview en kunnen worden gewijzigd voordat deze algemeen beschikbaar worden||
|Geschreven bytes per seconde samengestelde schijf|No|Geschreven bytes per seconde schijf (preview)|Bytes|Gemiddeld|Bytes per seconde geschreven naar schijf tijdens de bewakingsperiode. Houd er rekening mee dat deze metrische gegevens in preview zijn en onderhevig zijn aan veranderingen voordat deze algemeen beschikbaar komen||
|Schrijfbewerkingen voor samengestelde schijven per seconde|No|Schrijfbewerkingen op schijf per seconde (preview)|Bytes|Gemiddeld|Het aantal schrijf-IO's dat op een schijf is uitgevoerd tijdens de bewakingsperiode. Houd er rekening mee dat deze metrische gegevens in preview zijn en onderhevig zijn aan veranderingen voordat deze algemeen beschikbaar worden||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Verbruikt CPU-tegoed|Yes|Verbruikt CPU-tegoed|Count|Gemiddeld|Het totale aantal tegoeden dat door de virtuele machine is verbruikt. Alleen beschikbaar op burstbare VM's uit de B-serie|Geen dimensies|
|Resterend CPU-tegoed|Yes|Resterend CPU-tegoed|Count|Gemiddeld|Totaal aantal tegoeden dat beschikbaar is voor burst. Alleen beschikbaar op burstbare VM's uit de B-serie|Geen dimensies|
|Percentage verbruikte bandbreedte voor gegevensschijf|Yes|Percentage verbruikte bandbreedte voor gegevensschijf|Percentage|Gemiddeld|Percentage van de bandbreedte van de gegevensschijf die per minuut wordt verbruikt|Lun|
|Verbruikt percentage voor IOPS voor gegevensschijf|Yes|Verbruikt percentage voor IOPS voor gegevensschijf|Percentage|Gemiddeld|Percentage verbruikte I/O's van gegevensschijven per minuut|Lun|
|Maximale burst-bandbreedte van gegevensschijf|Yes|Maximale burst-bandbreedte van gegevensschijf|Count|Gemiddeld|Maximale doorvoer bytes per seconde Gegevensschijf kan worden bereikt met bursting|Lun|
|Max. burst-IOPS voor gegevensschijf|Yes|Max. burst-IOPS voor gegevensschijf|Count|Gemiddeld|Maximale IOPS-gegevensschijf kan worden bereikt met bursting|Lun|
|Wachtrijlengte van gegevensschijf|Yes|Wachtrijlengte van gegevensschijf|Count|Gemiddeld|Wachtrijlengte van gegevensschijf (of wachtrijlengte)|Lun|
|Gelezen bytes per seconde van gegevensschijf|Yes|Gelezen bytes per seconde van gegevensschijf|BytesPerSecond|Gemiddeld|Bytes per seconde gelezen vanaf één schijf tijdens bewakingsperiode|Lun|
|Leesbewerkingen gegevensschijf per seconde|Yes|Leesbewerkingen gegevensschijf per seconde|CountPerSecond|Gemiddeld|IOPS van één schijf lezen tijdens de bewakingsperiode|Lun|
|Doelbandbreedte van gegevensschijf|Yes|Doelbandbreedte van gegevensschijf|Count|Gemiddeld|Basislijndoorvoer bytes per seconde Gegevensschijf kan bereiken zonder bursting|Lun|
|Doel-IOPS voor gegevensschijf|Yes|Doel-IOPS voor gegevensschijf|Count|Gemiddeld|Basislijn-IOPS-gegevensschijf kan bereiken zonder bursting|Lun|
|Gebruikte burst-BPS-tegoedpercentage voor gegevensschijf|Yes|Gebruikte burst-BPS-tegoedpercentage voor gegevensschijf|Percentage|Gemiddeld|Percentage tot nu toe gebruikte burst-bandbreedtetegoeden voor gegevensschijven|Lun|
|IO-tegoedpercentage gebruikt voor gegevensschijf|Yes|IO-tegoedpercentage gebruikt voor gegevensschijf|Percentage|Gemiddeld|Percentage I/O-tegoeden voor burst-gegevensschijven dat tot nu toe is gebruikt|Lun|
|Geschreven bytes per seconde voor gegevensschijf|Yes|Geschreven bytes per seconde voor gegevensschijf|BytesPerSecond|Gemiddeld|Bytes per seconde geschreven naar één schijf tijdens bewakingsperiode|Lun|
|Schrijfbewerkingen gegevensschijf per seconde|Yes|Schrijfbewerkingen gegevensschijf per seconde|CountPerSecond|Gemiddeld|IOPS schrijven vanaf één schijf tijdens de bewakingsperiode|Lun|
|Gelezen bytes op de schijf|Yes|Gelezen bytes op de schijf|Bytes|Totaal|Bytes die tijdens de bewakingsperiode van de schijf worden gelezen|Geen dimensies|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor lezen van schijf|Geen dimensies|
|Geschreven bytes op de schijf|Yes|Geschreven bytes op de schijf|Bytes|Totaal|Bytes geschreven naar schijf tijdens bewakingsperiode|Geen dimensies|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijf schrijven|Geen dimensies|
|Binnenkomende stromen|Yes|Binnenkomende stromen|Count|Gemiddeld|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat naar de VM gaat)|Geen dimensies|
|Maximale aanmaakfrequentie voor binnenkomende stromen|Yes|Maximale aanmaakfrequentie voor binnenkomende stromen|CountPerSecond|Gemiddeld|De maximale aanmaaksnelheid van binnenkomende stromen (verkeer dat naar de VM gaat)|Geen dimensies|
|Netwerk in|Yes|Netwerk in factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes dat op alle netwerkinterfaces is ontvangen door de virtuele machine(s) (inkomend verkeer) (afgeschaft)|Geen dimensies|
|Netwerk in totaal|Yes|Netwerk in totaal|Bytes|Totaal|Het aantal bytes dat op alle netwerkinterfaces wordt ontvangen door de virtuele machine(s) (binnenkomend verkeer)|Geen dimensies|
|Netwerk uit|Yes|Factureerbaar netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer) (afgeschaft)|Geen dimensies|
|Totaal netwerk uit|Yes|Totaal netwerk uit|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer)|Geen dimensies|
|Percentage verbruikte bandbreedte van besturingssysteemschijf|Yes|Percentage verbruikte bandbreedte van besturingssysteemschijf|Percentage|Gemiddeld|Percentage van de bandbreedte van de besturingssysteemschijf die per minuut wordt verbruikt|Lun|
|Verbruikt percentage IOPS voor besturingssysteemschijf|Yes|Verbruikt percentage IOPS voor besturingssysteemschijf|Percentage|Gemiddeld|Percentage I/O's van de besturingssysteemschijf dat per minuut wordt verbruikt|Lun|
|Maximale burstbandbreedte van besturingssysteemschijf|Yes|Maximale burstbandbreedte van besturingssysteemschijf|Count|Gemiddeld|Maximum aantal bytes per seconde doorvoer besturingssysteemschijf kan worden bereikt met bursting|Lun|
|Max. Burst IOPS van besturingssysteemschijf|Yes|Max. Burst IOPS van besturingssysteemschijf|Count|Gemiddeld|Maximale IOPS-besturingssysteemschijf kan worden bereikt met bursting|Lun|
|Wachtrijlengte van besturingssysteemschijf|Yes|Wachtrijlengte van besturingssysteemschijf|Count|Gemiddeld|Wachtrijlengte van besturingssysteemschijf (of wachtrijlengte)|Geen dimensies|
|Gelezen bytes per seconde van besturingssysteemschijf|Yes|Gelezen bytes per seconde van besturingssysteemschijf|BytesPerSecond|Gemiddeld|Bytes per seconde gelezen vanaf één schijf tijdens bewakingsperiode voor besturingssysteemschijf|Geen dimensies|
|Leesbewerkingen besturingssysteemschijf per seconde|Yes|Leesbewerkingen besturingssysteemschijf per seconde|CountPerSecond|Gemiddeld|IOPS van één schijf lezen tijdens de bewakingsperiode voor de besturingssysteemschijf|Geen dimensies|
|Doelbandbreedte van besturingssysteemschijf|Yes|Doelbandbreedte van besturingssysteemschijf|Count|Gemiddeld|Basislijndoorvoer bytes per seconde besturingssysteemschijf kan bereiken zonder bursting|Lun|
|Doel-IOPS van besturingssysteemschijf|Yes|Doel-IOPS van besturingssysteemschijf|Count|Gemiddeld|Basislijn-IOPS-besturingssysteemschijf kan zonder bursting worden bereikt|Lun|
|Gebruikt bps-tegoedpercentage van besturingssysteemschijf|Yes|Gebruikt bps-tegoedpercentage van besturingssysteemschijf|Percentage|Gemiddeld|Percentage burst-bandbreedtetegoeden voor besturingssysteemschijven dat tot nu toe is gebruikt|Lun|
|IO-tegoedpercentage gebruikt voor besturingssysteemschijf|Yes|IO-tegoedpercentage gebruikt voor besturingssysteemschijf|Percentage|Gemiddeld|Percentage I/O-tegoeden voor burst van besturingssysteemschijven dat tot nu toe is gebruikt|Lun|
|Geschreven bytes per seconde op besturingssysteemschijf|Yes|Geschreven bytes per seconde op besturingssysteemschijf|BytesPerSecond|Gemiddeld|Bytes per seconde geschreven naar één schijf tijdens bewakingsperiode voor besturingssysteemschijf|Geen dimensies|
|Schrijfbewerkingen besturingssysteemschijf per seconde|Yes|Schrijfbewerkingen besturingssysteemschijf per seconde|CountPerSecond|Gemiddeld|IOPS schrijven vanaf één schijf tijdens de bewakingsperiode voor de besturingssysteemschijf|Geen dimensies|
|Uitgaande stromen|Yes|Uitgaande stromen|Count|Gemiddeld|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat de VM gaat)|Geen dimensies|
|Maximale aanmaakfrequentie voor uitgaande stromen|Yes|Maximale aanmaakfrequentie voor uitgaande stromen|CountPerSecond|Gemiddeld|De maximale aanmaaksnelheid van uitgaande stromen (verkeer dat uit de VM gaat)|Geen dimensies|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s)|Geen dimensies|
|Premium Data Disk Cache Read Hit|Yes|Premium Data Disk Cache Read Hit|Percentage|Gemiddeld|Premium Data Disk Cache Read Hit|Lun|
|Premium Data Disk Cache Read Miss|Yes|Premium Data Disk Cache Read Miss|Percentage|Gemiddeld|Premium Data Disk Cache Read Miss|Lun|
|Premium OS Disk Cache Read Hit|Yes|Premium OS Disk Cache Read Hit|Percentage|Gemiddeld|Premium OS Disk Cache Read Hit|Geen dimensies|
|Premium OS Disk Cache Read Miss|Yes|Premium OS Disk Cache Read Miss|Percentage|Gemiddeld|Premium OS Disk Cache Read Miss|Geen dimensies|
|Percentage verbruikte bandbreedte in de cache van de VM|Yes|Percentage verbruikte bandbreedte in de cache van de VM|Percentage|Gemiddeld|Percentage bandbreedte voor schijf in cache dat door de VM wordt gebruikt|Geen dimensies|
|Percentage verbruikte IOPS in de cache van de VM|Yes|Percentage verbruikte IOPS in de cache van de VM|Percentage|Gemiddeld|Percentage IOPS voor schijf in cache dat door de VM wordt gebruikt|Geen dimensies|
|Percentage verbruikte niet-gecachede bandbreedte van VM|Yes|Percentage verbruikte niet-gecachede bandbreedte van VM|Percentage|Gemiddeld|Percentage niet-gecachede schijfbandbreedte dat wordt gebruikt door de VM|Geen dimensies|
|Percentage verbruikte IOPS zondercachede VM|Yes|Percentage verbruikte IOPS zondercachede VM|Percentage|Gemiddeld|Percentage niet-gecachede schijf-IOPS dat door de VM wordt gebruikt|Geen dimensies|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Verbruikt CPU-tegoed|Yes|Verbruikt CPU-tegoed|Count|Gemiddeld|Het totale aantal tegoeden dat door de virtuele machine is verbruikt. Alleen beschikbaar op burstbare VM's uit de B-serie|Geen dimensies|
|Resterend CPU-tegoed|Yes|Resterend CPU-tegoed|Count|Gemiddeld|Totaal aantal tegoeden dat beschikbaar is voor burst. Alleen beschikbaar op burstbare VM's uit de B-serie|Geen dimensies|
|Percentage verbruikte bandbreedte van gegevensschijf|Yes|Percentage verbruikte bandbreedte van gegevensschijf|Percentage|Gemiddeld|Percentage van de bandbreedte van de gegevensschijf die per minuut wordt verbruikt|LUN, VMName|
|Verbruikt percentage voor IOPS voor gegevensschijf|Yes|Verbruikt percentage voor IOPS voor gegevensschijf|Percentage|Gemiddeld|Percentage verbruikte I/O's van gegevensschijven per minuut|LUN, VMName|
|Maximale burst-bandbreedte van gegevensschijf|Yes|Maximale burst-bandbreedte van gegevensschijf|Count|Gemiddeld|Maximale doorvoer bytes per seconde Gegevensschijf kan worden bereikt met bursting|LUN, VMName|
|Max. burst-IOPS voor gegevensschijf|Yes|Max. burst-IOPS voor gegevensschijf|Count|Gemiddeld|Maximale IOPS-gegevensschijf kan worden bereikt met bursting|LUN, VMName|
|Wachtrijlengte van gegevensschijf|Yes|Wachtrijlengte van gegevensschijf|Count|Gemiddeld|Wachtrijlengte van gegevensschijf (of wachtrijlengte)|LUN, VMName|
|Gelezen bytes per seconde voor gegevensschijf|Yes|Gelezen bytes per seconde voor gegevensschijf|BytesPerSecond|Gemiddeld|Bytes per seconde gelezen vanaf één schijf tijdens bewakingsperiode|LUN, VMName|
|Leesbewerkingen gegevensschijf per seconde|Yes|Leesbewerkingen gegevensschijf per seconde|CountPerSecond|Gemiddeld|IOPS van één schijf lezen tijdens de bewakingsperiode|LUN, VMName|
|Doelbandbreedte van gegevensschijf|Yes|Doelbandbreedte van gegevensschijf|Count|Gemiddeld|Doorvoer van basislijn bytes per seconde Gegevensschijf kan bereiken zonder bursting|LUN, VMName|
|Doel-IOPS voor gegevensschijf|Yes|Doel-IOPS voor gegevensschijf|Count|Gemiddeld|Basislijn-IOPS-gegevensschijf kan bereiken zonder bursting|LUN, VMName|
|Data Disk gebruikt Burst BPS-tegoedpercentage|Yes|Data Disk gebruikte Burst BPS-tegoedpercentage|Percentage|Gemiddeld|Percentage data disk burst-bandbreedtetegoeden dat tot nu toe is gebruikt|LUN, VMName|
|Percentage gebruikte Burst IO-tegoeden op de gegevensschijf|Yes|Percentage gebruikte Burst IO-tegoeden op de gegevensschijf|Percentage|Gemiddeld|Percentage I/O-tegoeden voor burst van gegevensschijven dat tot nu toe is gebruikt|LUN, VMName|
|Geschreven bytes per seconde van gegevensschijf|Yes|Geschreven bytes per seconde van gegevensschijf|BytesPerSecond|Gemiddeld|Bytes per seconde geschreven naar één schijf tijdens bewakingsperiode|LUN, VMName|
|Schrijfbewerkingen gegevensschijf per seconde|Yes|Schrijfbewerkingen gegevensschijf per seconde|CountPerSecond|Gemiddeld|IOPS schrijven vanaf één schijf tijdens de bewakingsperiode|LUN, VMName|
|Gelezen bytes op de schijf|Yes|Gelezen bytes op de schijf|Bytes|Totaal|Bytes die tijdens de bewakingsperiode van de schijf worden gelezen|VMName|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor lezen van schijf|VMName|
|Geschreven bytes op de schijf|Yes|Geschreven bytes op de schijf|Bytes|Totaal|Bytes geschreven naar schijf tijdens bewakingsperiode|VMName|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijf schrijven|VMName|
|Binnenkomende stromen|Yes|Binnenkomende stromen|Count|Gemiddeld|Binnenkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat naar de VM gaat)|VMName|
|Maximale aanmaaksnelheid voor binnenkomende stromen|Yes|Maximale aanmaaksnelheid voor binnenkomende stromen|CountPerSecond|Gemiddeld|De maximale aanmaaksnelheid van binnenkomende stromen (verkeer dat naar de VM gaat)|VMName|
|Netwerk in|Yes|Netwerk in factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes dat op alle netwerkinterfaces is ontvangen door de virtuele machine(s) (inkomend verkeer) (afgeschaft)|VMName|
|Netwerk in totaal|Yes|Netwerk in totaal|Bytes|Totaal|Het aantal bytes dat op alle netwerkinterfaces is ontvangen door de virtuele machine(s) (inkomend verkeer)|VMName|
|Netwerk uit|Yes|Factureerbaar netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer) (afgeschaft)|VMName|
|Totaal netwerk|Yes|Totaal netwerk|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer)|VMName|
|Percentage verbruikte bandbreedte van besturingssysteemschijf|Yes|Percentage verbruikte bandbreedte van besturingssysteemschijf|Percentage|Gemiddeld|Percentage van de bandbreedte van de besturingssysteemschijf die per minuut wordt verbruikt|LUN, VMName|
|Percentage verbruikte IOPS van besturingssysteemschijf|Yes|Percentage verbruikte IOPS van besturingssysteemschijf|Percentage|Gemiddeld|Percentage van de verbruikte I/O's van de besturingssysteemschijf per minuut|LUN, VMName|
|Maximale burst-bandbreedte van besturingssysteemschijf|Yes|Maximale burst-bandbreedte van besturingssysteemschijf|Count|Gemiddeld|Maximum aantal bytes per seconde doorvoer besturingssysteemschijf kan worden bereikt met bursting|LUN, VMName|
|Max. burst-IOPS van besturingssysteemschijf|Yes|Max. burst-IOPS van besturingssysteemschijf|Count|Gemiddeld|Maximale IOPS-besturingssysteemschijf kan worden bereikt met bursting|LUN, VMName|
|Wachtrijlengte van besturingssysteemschijf|Yes|Wachtrijlengte van besturingssysteemschijf|Count|Gemiddeld|Wachtrijlengte van besturingssysteemschijf (of wachtrijlengte)|VMName|
|Gelezen bytes per seconde op besturingssysteemschijf|Yes|Gelezen bytes per seconde op besturingssysteemschijf|BytesPerSecond|Gemiddeld|Bytes per seconde gelezen vanaf één schijf tijdens bewakingsperiode voor besturingssysteemschijf|VMName|
|Leesbewerkingen besturingssysteemschijf per seconde|Yes|Leesbewerkingen besturingssysteemschijf per seconde|CountPerSecond|Gemiddeld|IOPS van één schijf lezen tijdens bewakingsperiode voor besturingssysteemschijf|VMName|
|Doelbandbreedte van besturingssysteemschijf|Yes|Doelbandbreedte van besturingssysteemschijf|Count|Gemiddeld|De doorvoer van basis bytes per seconde besturingssysteemschijf kan worden bereikt zonder bursting|LUN, VMName|
|Doel-IOPS van besturingssysteemschijf|Yes|Doel-IOPS van besturingssysteemschijf|Count|Gemiddeld|Basislijn-IOPS-besturingssysteemschijf kan bereiken zonder bursting|LUN, VMName|
|Besturingssysteemschijf gebruikt burst BPS-tegoedpercentage|Yes|Besturingssysteemschijf gebruikt percentage burst-BPS-tegoed|Percentage|Gemiddeld|Percentage bandbreedtetegoeden voor burst-bandbreedte van besturingssysteemschijven dat tot nu toe is gebruikt|LUN, VMName|
|IO-tegoedpercentage van burst gebruikt op besturingssysteemschijf|Yes|IO-tegoedpercentage van burst gebruikt op besturingssysteemschijf|Percentage|Gemiddeld|Percentage I/O-tegoeden voor burst van besturingssysteemschijven dat tot nu toe is gebruikt|LUN, VMName|
|Schrijf bytes per seconde van besturingssysteemschijf|Yes|Geschreven bytes per seconde van besturingssysteemschijf|BytesPerSecond|Gemiddeld|Bytes per seconde geschreven naar één schijf tijdens bewakingsperiode voor besturingssysteemschijf|VMName|
|Schrijfbewerkingen besturingssysteemschijf per seconde|Yes|Schrijfbewerkingen besturingssysteemschijf per seconde|CountPerSecond|Gemiddeld|IOPS schrijven vanaf één schijf tijdens de bewakingsperiode voor de besturingssysteemschijf|VMName|
|Uitgaande stromen|Yes|Uitgaande stromen|Count|Gemiddeld|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat de VM gaat)|VMName|
|Maximale aanmaakfrequentie voor uitgaande stromen|Yes|Maximale aanmaakfrequentie voor uitgaande stromen|CountPerSecond|Gemiddeld|De maximale aanmaaksnelheid van uitgaande stromen (verkeer dat uit de VM gaat)|VMName|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s)|VMName|
|Premium Data Disk Cache Read Hit|Yes|Premium Data Disk Cache Read Hit|Percentage|Gemiddeld|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Yes|Premium Data Disk Cache Read Miss|Percentage|Gemiddeld|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Yes|Premium OS Disk Cache Read Hit|Percentage|Gemiddeld|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Yes|Premium OS Disk Cache Read Miss|Percentage|Gemiddeld|Premium OS Disk Cache Read Miss|VMName|
|Percentage verbruikte bandbreedte in de cache van de VM|Yes|Percentage verbruikte bandbreedte in de cache van de VM|Percentage|Gemiddeld|Percentage bandbreedte van schijf in cache die wordt gebruikt door de VM|VMName|
|Percentage verbruikte IOPS in de cache van de VM|Yes|Percentage verbruikte IOPS in de cache van de VM|Percentage|Gemiddeld|Percentage IOPS van schijf in cache dat door de VM wordt gebruikt|VMName|
|Percentage verbruikte bandbreedte voor niet-gecachede VM's|Yes|Percentage verbruikte bandbreedte voor niet-gecachede VM's|Percentage|Gemiddeld|Percentage niet-gecachede schijfbandbreedte dat wordt gebruikt door de VM|VMName|
|Percentage verbruikte IOPS zondercachede VM|Yes|Percentage verbruikte IOPS zondercachede VM|Percentage|Gemiddeld|Percentage niet-gecachede schijf-IOPS dat door de VM wordt gebruikt|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Verbruikt CPU-tegoed|Yes|Verbruikt CPU-tegoed|Count|Gemiddeld|Het totale aantal tegoeden dat door de virtuele machine is verbruikt. Alleen beschikbaar op burstbare VM's uit de B-serie|Geen dimensies|
|Resterend CPU-tegoed|Yes|Resterend CPU-tegoed|Count|Gemiddeld|Totaal aantal tegoeden dat beschikbaar is voor burst. Alleen beschikbaar op burstbare VM's uit de B-serie|Geen dimensies|
|Percentage verbruikte bandbreedte voor gegevensschijf|Yes|Percentage verbruikte bandbreedte voor gegevensschijf|Percentage|Gemiddeld|Percentage van de bandbreedte van de gegevensschijf die per minuut wordt verbruikt|Lun|
|Verbruikt percentage voor IOPS voor gegevensschijf|Yes|Verbruikt percentage voor IOPS voor gegevensschijf|Percentage|Gemiddeld|Percentage verbruikte I/O's van gegevensschijven per minuut|Lun|
|Maximale burst-bandbreedte van gegevensschijf|Yes|Maximale burst-bandbreedte van gegevensschijf|Count|Gemiddeld|Maximale doorvoer bytes per seconde Gegevensschijf kan worden bereikt met bursting|Lun|
|Max. burst-IOPS voor gegevensschijf|Yes|Max. burst-IOPS voor gegevensschijf|Count|Gemiddeld|Maximale IOPS-gegevensschijf kan worden bereikt met bursting|Lun|
|Wachtrijlengte van gegevensschijf|Yes|Wachtrijlengte van gegevensschijf|Count|Gemiddeld|Wachtrijlengte van gegevensschijf (of wachtrijlengte)|Lun|
|Gelezen bytes per seconde voor gegevensschijf|Yes|Gelezen bytes per seconde van gegevensschijf|BytesPerSecond|Gemiddeld|Bytes per seconde gelezen vanaf één schijf tijdens bewakingsperiode|Lun|
|Leesbewerkingen gegevensschijf per seconde|Yes|Leesbewerkingen gegevensschijf per seconde|CountPerSecond|Gemiddeld|IOPS van één schijf lezen tijdens de bewakingsperiode|Lun|
|Doelbandbreedte van gegevensschijf|Yes|Doelbandbreedte van gegevensschijf|Count|Gemiddeld|Doorvoer van basislijn bytes per seconde Gegevensschijf kan bereiken zonder bursting|Lun|
|Doel-IOPS voor gegevensschijf|Yes|Doel-IOPS voor gegevensschijf|Count|Gemiddeld|Basislijn-IOPS-gegevensschijf kan bereiken zonder bursting|Lun|
|Data Disk gebruikte Burst BPS-tegoedpercentage|Yes|Data Disk gebruikte Burst BPS-tegoedpercentage|Percentage|Gemiddeld|Percentage data disk burst bandbreedtetegoeden dat tot nu toe is gebruikt|Lun|
|IO-tegoedpercentage gebruikt voor gegevensschijf|Yes|IO-tegoedpercentage gebruikt voor gegevensschijf|Percentage|Gemiddeld|Percentage I/O-tegoeden voor burst-gegevensschijven dat tot nu toe is gebruikt|Lun|
|Geschreven bytes per seconde voor gegevensschijf|Yes|Geschreven bytes per seconde voor gegevensschijf|BytesPerSecond|Gemiddeld|Bytes per seconde geschreven naar één schijf tijdens bewakingsperiode|Lun|
|Schrijfbewerkingen gegevensschijf per seconde|Yes|Schrijfbewerkingen gegevensschijf per seconde|CountPerSecond|Gemiddeld|IOPS schrijven vanaf één schijf tijdens de bewakingsperiode|Lun|
|Gelezen bytes op de schijf|Yes|Gelezen bytes op de schijf|Bytes|Totaal|Bytes die tijdens de bewakingsperiode van de schijf worden gelezen|Geen dimensies|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor lezen van schijf|Geen dimensies|
|Geschreven bytes op de schijf|Yes|Geschreven bytes op de schijf|Bytes|Totaal|Bytes geschreven naar schijf tijdens bewakingsperiode|Geen dimensies|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|IOPS voor schijf schrijven|Geen dimensies|
|Binnenkomende stromen|Yes|Binnenkomende stromen|Count|Gemiddeld|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat naar de VM gaat)|Geen dimensies|
|Maximale aanmaakfrequentie voor binnenkomende stromen|Yes|Maximale aanmaakfrequentie voor binnenkomende stromen|CountPerSecond|Gemiddeld|De maximale aanmaaksnelheid van binnenkomende stromen (verkeer dat naar de VM gaat)|Geen dimensies|
|Netwerk in|Yes|Netwerk in factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes dat op alle netwerkinterfaces is ontvangen door de virtuele machine(s) (inkomend verkeer) (afgeschaft)|Geen dimensies|
|Netwerk in totaal|Yes|Netwerk in totaal|Bytes|Totaal|Het aantal bytes dat op alle netwerkinterfaces is ontvangen door de virtuele machine(s) (inkomend verkeer)|Geen dimensies|
|Netwerk uit|Yes|Factureerbaar netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer) (afgeschaft)|Geen dimensies|
|Totaal netwerk uit|Yes|Totaal netwerk uit|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (uitgaand verkeer)|Geen dimensies|
|Percentage verbruikte bandbreedte van besturingssysteemschijf|Yes|Percentage verbruikte bandbreedte van besturingssysteemschijf|Percentage|Gemiddeld|Percentage van de bandbreedte van de besturingssysteemschijf die per minuut wordt verbruikt|Lun|
|Verbruikt percentage IOPS voor besturingssysteemschijf|Yes|Verbruikt percentage IOPS voor besturingssysteemschijf|Percentage|Gemiddeld|Percentage I/O's van de besturingssysteemschijf dat per minuut wordt verbruikt|Lun|
|Maximale burstbandbreedte van besturingssysteemschijf|Yes|Maximale burstbandbreedte van besturingssysteemschijf|Count|Gemiddeld|Maximum aantal bytes per seconde doorvoer besturingssysteemschijf kan worden bereikt met bursting|Lun|
|Max. burst-IOPS van besturingssysteemschijf|Yes|Max. burst-IOPS van besturingssysteemschijf|Count|Gemiddeld|Maximale IOPS-besturingssysteemschijf kan worden bereikt met bursting|Lun|
|Wachtrijlengte van besturingssysteemschijf|Yes|Wachtrijlengte van besturingssysteemschijf|Count|Gemiddeld|Wachtrijlengte van besturingssysteemschijf (of wachtrijlengte)|Geen dimensies|
|Gelezen bytes per seconde van besturingssysteemschijf|Yes|Gelezen bytes per seconde van besturingssysteemschijf|BytesPerSecond|Gemiddeld|Bytes per seconde gelezen vanaf één schijf tijdens bewakingsperiode voor besturingssysteemschijf|Geen dimensies|
|Leesbewerkingen besturingssysteemschijf per seconde|Yes|Leesbewerkingen besturingssysteemschijf per seconde|CountPerSecond|Gemiddeld|IOPS van één schijf lezen tijdens de bewakingsperiode voor de besturingssysteemschijf|Geen dimensies|
|Doelbandbreedte van besturingssysteemschijf|Yes|Doelbandbreedte van besturingssysteemschijf|Count|Gemiddeld|Basislijndoorvoer bytes per seconde besturingssysteemschijf kan bereiken zonder bursting|Lun|
|Doel-IOPS van besturingssysteemschijf|Yes|Doel-IOPS van besturingssysteemschijf|Count|Gemiddeld|Basislijn-IOPS-besturingssysteemschijf kan zonder bursting worden bereikt|Lun|
|Gebruikt bps-tegoedpercentage van besturingssysteemschijf|Yes|Gebruikt bps-tegoedpercentage van besturingssysteemschijf|Percentage|Gemiddeld|Percentage burst-bandbreedtetegoeden voor besturingssysteemschijven dat tot nu toe is gebruikt|Lun|
|IO-tegoedpercentage gebruikt voor besturingssysteemschijf|Yes|IO-tegoedpercentage gebruikt voor besturingssysteemschijf|Percentage|Gemiddeld|Percentage I/O-tegoeden voor burst van besturingssysteemschijven dat tot nu toe is gebruikt|Lun|
|Geschreven bytes per seconde op besturingssysteemschijf|Yes|Geschreven bytes per seconde op besturingssysteemschijf|BytesPerSecond|Gemiddeld|Bytes per seconde geschreven naar één schijf tijdens bewakingsperiode voor besturingssysteemschijf|Geen dimensies|
|Schrijfbewerkingen besturingssysteemschijf per seconde|Yes|Schrijfbewerkingen besturingssysteemschijf per seconde|CountPerSecond|Gemiddeld|IOPS schrijven vanaf één schijf tijdens de bewakingsperiode voor besturingssysteemschijf|Geen dimensies|
|Uitgaande stromen|Yes|Uitgaande stromen|Count|Gemiddeld|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat de VM gaat)|Geen dimensies|
|Maximale aanmaakfrequentie voor uitgaande stromen|Yes|Maximale aanmaakfrequentie voor uitgaande stromen|CountPerSecond|Gemiddeld|De maximale aanmaaksnelheid van uitgaande stromen (verkeer dat uit de VM gaat)|Geen dimensies|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het percentage toegewezen rekeneenheden dat momenteel wordt gebruikt door de virtuele machine(s)|Geen dimensies|
|Premium Data Disk Cache Read Hit|Yes|Premium Data Disk Cache Read Hit|Percentage|Gemiddeld|Premium Data Disk Cache Read Hit|Lun|
|Premium Data Disk Cache Read Miss|Yes|Premium Data Disk Cache Read Miss|Percentage|Gemiddeld|Premium Data Disk Cache Read Miss|Lun|
|Premium OS Disk Cache Read Hit|Yes|Premium OS Disk Cache Read Hit|Percentage|Gemiddeld|Premium OS Disk Cache Read Hit|Geen dimensies|
|Premium OS Disk Cache Read Miss|Yes|Premium OS Disk Cache Read Miss|Percentage|Gemiddeld|Premium OS Disk Cache Read Miss|Geen dimensies|
|Percentage verbruikte bandbreedte in de cache van de VM|Yes|Percentage verbruikte bandbreedte in de cache van de VM|Percentage|Gemiddeld|Percentage bandbreedte voor schijf in cache dat door de VM wordt gebruikt|Geen dimensies|
|Percentage verbruikte IOPS in de cache van de VM|Yes|Percentage verbruikte IOPS in de cache van de VM|Percentage|Gemiddeld|Percentage IOPS voor schijf in cache dat door de VM wordt gebruikt|Geen dimensies|
|Percentage verbruikte niet-gecachede bandbreedte van VM|Yes|Percentage verbruikte niet-gecachede bandbreedte van VM|Percentage|Gemiddeld|Percentage niet-gecachede schijfbandbreedte dat door de VM wordt gebruikt|Geen dimensies|
|Percentage verbruikte IOPS zondercachede VM|Yes|Percentage verbruikte IOPS zondercachede VM|Percentage|Gemiddeld|Percentage niet-gecachede schijf-IOPS dat door de VM wordt gebruikt|Geen dimensies|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|CPU-gebruik|Count|Gemiddeld|CPU-gebruik voor alle kernen incores.|containerName|
|MemoryUsage|Yes|Geheugengebruik|Bytes|Gemiddeld|Totaal geheugengebruik in byte.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Ontvangen netwerk bytes per seconde|Bytes|Gemiddeld|De netwerk-bytes die per seconde worden ontvangen.|Geen dimensies|
|NetworkBytesTransmittedPerSecond|Yes|Verzonden netwerk bytes per seconde|Bytes|Gemiddeld|De verzonden netwerk-bytes per seconde.|Geen dimensies|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|CPU-tijd agentpool|Seconden|Totaal|CPU-tijd agentpool in seconden|Geen dimensies|
|RunDuration|Yes|Duur van de run|Milliseconden|Totaal|Duur van de run in milliseconden|Geen dimensies|
|SuccessfulPullCount|Yes|Geslaagde pull-telling|Count|Gemiddeld|Aantal geslaagde pulls van afbeeldingen|Geen dimensies|
|SuccessfulPushCount|Yes|Aantal geslaagde push-pushs|Count|Gemiddeld|Aantal geslaagde pushes van afbeeldingen|Geen dimensies|
|TotalPullCount|Yes|Totaal aantal pull-werk|Count|Gemiddeld|Totaal aantal pulls van afbeeldingen|Geen dimensies|
|Totaal aantalpushcount|Yes|Totaal aantal push-pushs|Count|Gemiddeld|Totaal aantal pushes voor afbeeldingen|Geen dimensies|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Inflight-aanvragen|Count|Gemiddeld|Maximum aantal momenteel gebruikte inflight-aanvragen op de apiserver per aanvraag in de laatste seconde|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|No|Clusterstatus|Count|Gemiddeld|Bepaalt of automatische schaalverdedering van clusters actie onderneemt op het cluster||
|cluster_autoscaler_scale_down_in_cooldown|No|Cooldown omlaag schalen|Count|Gemiddeld|Bepaalt of de omlaag schalen zich in cooldown- geen knooppunten worden verwijderd tijdens dit tijdsbestek||
|cluster_autoscaler_unneeded_nodes_count|No|Onnodige knooppunten|Count|Gemiddeld|Cluster auotscaler markeert deze knooppunten als kandidaten voor verwijdering en worden uiteindelijk verwijderd||
|cluster_autoscaler_unschedulable_pods_count|No|Niet-bewerkbare pods|Count|Gemiddeld|Aantal pods dat momenteel niet kan worden geseuleerd in het cluster||
|kube_node_status_allocatable_cpu_cores|No|Totaal aantal beschikbare CPU-kernen in een beheerd cluster|Count|Gemiddeld|Totaal aantal beschikbare CPU-kernen in een beheerd cluster||
|kube_node_status_allocatable_memory_bytes|No|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Bytes|Gemiddeld|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster||
|kube_node_status_condition|No|Statussen voor verschillende knooppuntvoorwaarden|Count|Gemiddeld|Statussen voor verschillende knooppuntvoorwaarden|voorwaarde, status, status2, knooppunt|
|kube_pod_status_phase|No|Aantal pods per fase|Count|Gemiddeld|Aantal pods per fase|fase, naamruimte, pod|
|kube_pod_status_ready|No|Aantal pods met de status Gereed|Count|Gemiddeld|Aantal pods met de status Gereed|naamruimte, pod, voorwaarde|
|node_cpu_usage_millicores|Yes|Cpu-gebruikCores|Cores|Gemiddeld|Geaggregeerde meting van CPU-gebruik incores in het cluster|node, nodepool|
|node_cpu_usage_percentage|Yes|PERCENTAGE CPU-gebruik|Percentage|Gemiddeld|Geaggregeerd gemiddeld CPU-gebruik gemeten in percentages in het cluster|node, nodepool|
|node_disk_usage_bytes|Yes|Schijf gebruikt bytes|Bytes|Gemiddeld|Schijfruimte die wordt gebruikt in bytes per apparaat|node, nodepool, device|
|node_disk_usage_percentage|Yes|Percentage gebruikt schijf|Percentage|Gemiddeld|Schijfruimte die wordt gebruikt in procenten per apparaat|node, nodepool, device|
|node_memory_rss_bytes|Yes|RSS-bytes voor geheugen|Bytes|Gemiddeld|RSS-geheugen van container dat wordt gebruikt in bytes|node, nodepool|
|node_memory_rss_percentage|Yes|RSS-percentage geheugen|Percentage|Gemiddeld|RSS-geheugen van container gebruikt in procenten|node, nodepool|
|node_memory_working_set_bytes|Yes|Geheugen werkset bytes|Bytes|Gemiddeld|Geheugen van container-werkset dat wordt gebruikt in bytes|node, nodepool|
|node_memory_working_set_percentage|Yes|Percentage geheugen-werksets|Percentage|Gemiddeld|Geheugen van container-werkset gebruikt in procenten|node, nodepool|
|node_network_in_bytes|Yes|Netwerk in bytes|Bytes|Gemiddeld|Netwerk ontvangen bytes|node, nodepool|
|node_network_out_bytes|Yes|Netwerk van bytes|Bytes|Gemiddeld|Verzonden bytes in het netwerk|node, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Mislukte aanvragen|Aantal|Totaal|Haalt de beschikbare logboeken voor aangepaste resourceproviders op|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen van de aangepaste provider|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AvailableCapacity|Yes|Beschikbare capaciteit|Bytes|Gemiddeld|De beschikbare capaciteit in bytes tijdens de rapportageperiode.|Geen dimensies|
|BytesUploadedToCloud|Yes|Geüploade cloud bytes (apparaat)|Bytes|Gemiddeld|Het totale aantal bytes dat tijdens de rapportageperiode naar Azure wordt geüpload vanaf een apparaat.|Geen dimensies|
|BytesUploadedToCloudPerShare|Yes|Geüploade cloud bytes (share)|Bytes|Gemiddeld|Het totale aantal bytes dat in de rapportageperiode vanuit een share naar Azure wordt geüpload.|Delen|
|CloudReadThroughput|Yes|Doorvoer voor downloaden in de cloud|BytesPerSecond|Gemiddeld|De doorvoer voor het downloaden van de cloud naar Azure tijdens de rapportageperiode.|Geen dimensies|
|CloudReadThroughputPerShare|Yes|Doorvoer voor downloaden in de cloud (share)|BytesPerSecond|Gemiddeld|De downloaddoorvoer naar Azure vanaf een share tijdens de rapportageperiode.|Delen|
|CloudUploadThroughput|Yes|Doorvoer voor uploaden in de cloud|BytesPerSecond|Gemiddeld|De clouduploaddoorvoer naar Azure tijdens de rapportageperiode.|Geen dimensies|
|CloudUploadThroughputPerShare|Yes|Clouduploaddoorvoer (share)|BytesPerSecond|Gemiddeld|De uploaddoorvoer naar Azure vanaf een share tijdens de rapportageperiode.|Delen|
|HyperVMemoryUtilization|Yes|Edge Compute - geheugengebruik|Percentage|Gemiddeld|Hoeveelheid RAM in gebruik|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Edge Compute - CPU-percentage|Percentage|Gemiddeld|Percentage CPU-gebruik|InstanceName|
|NICReadThroughput|Yes|Leesdoorvoer (netwerk)|BytesPerSecond|Gemiddeld|De leesdoorvoer van de netwerkinterface op het apparaat in de rapportageperiode voor alle volumes in de gateway.|InstanceName|
|NICWriteThroughput|Yes|Schrijfdoorvoer (netwerk)|BytesPerSecond|Gemiddeld|De schrijfdoorvoer van de netwerkinterface op het apparaat in de rapportageperiode voor alle volumes in de gateway.|InstanceName|
|TotalCapacity|Yes|Totale capaciteit|Bytes|Gemiddeld|Totale capaciteit|Geen dimensies|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Gegevensactiva gemaakt|Count|Maximum|Aantal gemaakte gegevensactiva|DataAssetName|
|PipelineCount|Yes|Gemaakte pijplijnen|Count|Maximum|Aantal gemaakte pijplijnen|PipelineName|
|ProposalCount|Yes|Gemaakte voorstellen|Count|Maximum|Aantal gemaakte voorstellen|ProposalName|
|ScriptCount|Yes|Gemaakte scripts|Count|Maximum|Aantal gemaakte scripts|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Mislukte runs|Aantal|Totaal||pipelineName, activityName|
|SuccessfulRuns|Yes|Geslaagde runs|Aantal|Totaal||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Metrische gegevens voor geannuleerde activiteiten|Aantal|Totaal||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Yes|Metrische gegevens voor mislukte activiteiten|Aantal|Totaal||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Yes|Metrische gegevens voor het slagen van de activiteit|Aantal|Totaal||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Yes|Totale factorygrootte (GB-eenheid)|Count|Maximum||Geen dimensies|
|IntegrationRuntimeAvailableMemory|Yes|Beschikbaar geheugen voor Integratieruntime|Bytes|Gemiddeld||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Yes|Aantal beschikbare knooppunt voor Integratieruntime|Count|Gemiddeld||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Duur van de integratieruntimewachtrij|Seconden|Gemiddeld||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|CPU-gebruik van Integration Runtime|Percentage|Gemiddeld||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Yes|Lengte van de integratieruntimewachtrij|Count|Gemiddeld||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Maximale toegestane factorygrootte (GB-eenheid)|Count|Maximum||Geen dimensies|
|MaxAllowedResourceCount|Yes|Maximaal aantal toegestane entiteiten|Count|Maximum||Geen dimensies|
|PipelineCancelledRuns|Yes|Metrische gegevens voor geannuleerde pijplijn-runs|Aantal|Totaal||FailureType, Name|
|PipelineElapsedTimeRuns|Yes|Metrische gegevens voor het gebruik van verstreken tijdpijplijnen|Aantal|Totaal||RunId, Naam|
|PipelineFailedRuns|Yes|Metrische gegevens voor mislukte pijplijn-runs|Aantal|Totaal||FailureType, Name|
|PipelineSucceededRuns|Yes|Metrische gegevens voor het slagen van pijplijn worden uitgevoerd|Aantal|Totaal||FailureType, Name|
|ResourceCount|Yes|Totaal aantal entiteiten|Count|Maximum||Geen dimensies|
|SSISIntegrationRuntimeStartCancel|Yes|Metrische startgegevens voor SSIS Integration Runtime geannuleerd|Aantal|Totaal||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Metrische gegevens voor starten van SSIS-integratieruntime mislukt|Aantal|Totaal||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Yes|Metrische gegevens voor het starten van de SSIS-integratieruntime geslaagd|Aantal|Totaal||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStintegration|Yes|Metrische gegevens voor vastgelopen SSIS Integration Runtime stoppen|Aantal|Totaal||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Yes|Metrische gegevens voor het stoppen van de SSIS-integratieruntime geslaagd|Aantal|Totaal||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Yes|Metrische gegevens voor uitvoering van SSIS-pakket geannuleerd|Aantal|Totaal||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Yes|Metrische gegevens voor mislukte uitvoering van SSIS-pakket|Aantal|Totaal||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Yes|Metrische gegevens voor de uitvoering van het SSIS-pakket geslaagd|Aantal|Totaal||IntegrationRuntimeName|
|TriggerCancelledRuns|Yes|Metrische gegevens voor geannuleerde triggers|Aantal|Totaal||Naam, FailureType|
|TriggerFailedRuns|Yes|Metrische gegevens voor mislukte triggers worden uitgevoerd|Aantal|Totaal||Naam, FailureType|
|TriggerSucceededRuns|Yes|Metrische gegevens voor de trigger geslaagd|Aantal|Totaal||Naam, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Geannuleerde AU-tijd|Seconden|Totaal|Totale AU-tijd voor geannuleerde taken.|Geen dimensies|
|JobAUEndedFailure|Yes|Mislukte AU-tijd|Seconden|Totaal|Totale AU-tijd voor mislukte taken.|Geen dimensies|
|JobAUEndedSuccess|Yes|Geslaagde AU-tijd|Seconden|Totaal|Totale AU-tijd voor geslaagde taken.|Geen dimensies|
|JobEndedCancelled|Yes|Geannuleerde taken|Aantal|Totaal|Aantal geannuleerde taken.|Geen dimensies|
|JobEndedFailure|Yes|Mislukte taken|Aantal|Totaal|Aantal mislukte taken.|Geen dimensies|
|JobEndedSuccess|Yes|Geslaagde taken|Aantal|Totaal|Aantal geslaagde taken.|Geen dimensies|
|JobStage|Yes|Taken in fase|Aantal|Totaal|Aantal taken in elke fase.|Geen dimensies|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|DataRead|Yes|Gelezen gegevens|Bytes|Totaal|Totale hoeveelheid gegevens die uit het account is gelezen.|Geen dimensies|
|Gegevensgeschreven|Yes|Geschreven gegevens|Bytes|Totaal|Totale hoeveelheid gegevens die naar het account wordt geschreven.|Geen dimensies|
|ReadRequests|Yes|Aanvragen lezen|Aantal|Totaal|Aantal aanvragen voor het lezen van gegevens voor het account.|Geen dimensies|
|TotalStorage|Yes|Totale opslagruimte|Bytes|Maximum|Totale hoeveelheid gegevens die is opgeslagen in het account.|Geen dimensies|
|WriteRequests|Yes|Schrijfaanvragen|Aantal|Totaal|Aantal schrijfaanvragen voor gegevens naar het account.|Geen dimensies|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Ontvangen momentopnamen van mislukte share|Count|Count|Aantal ontvangen momentopnamen van share mislukt in het account|Geen dimensies|
|FailedShareSynchronizations|Yes|Verzonden momentopnamen van mislukte share|Count|Count|Aantal verzonden momentopnamen van share mislukt in het account|Geen dimensies|
|ShareCount|Yes|Verzonden shares|Count|Maximum|Aantal verzonden shares in het account|Sharenaam|
|ShareSubscriptionCount|Yes|Ontvangen shares|Count|Maximum|Aantal ontvangen shares in het account|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|Ontvangen momentopnamen van share geslaagd|Count|Count|Aantal ontvangen momentopnamen van share geslaagd in het account|Geen dimensies|
|SucceededShareSynchronizations|Yes|Verzonden share geslaagd momentopnamen|Count|Count|Aantal verzonden share-momentopnamen dat is geslaagd in het account|Geen dimensies|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_connections|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|Geen dimensies|
|backup_storage_used|Yes|Gebruikte back-upopslag|Bytes|Gemiddeld|Gebruikte back-upopslag|Geen dimensies|
|connections_failed|Yes|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|io_consumption_percent|Yes|IO-percentage|Percentage|Gemiddeld|IO-percentage|Geen dimensies|
|memory_percent|Yes|Geheugen percentage|Percentage|Gemiddeld|Geheugen percentage|Geen dimensies|
|network_bytes_egress|Yes|Netwerk uit|Bytes|Totaal|Netwerk uit via actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Yes|Netwerk in|Bytes|Totaal|Netwerk in via actieve verbindingen|Geen dimensies|
|seconds_behind_master|Yes|Replicatievertraging in seconden|Count|Maximum|Replicatievertraging in seconden|Geen dimensies|
|serverlog_storage_limit|Yes|Opslaglimiet voor serverlogboek|Bytes|Maximum|Opslaglimiet voor serverlogboek|Geen dimensies|
|serverlog_storage_percent|Yes|Opslag percentage serverlogboek|Percentage|Gemiddeld|Opslag percentage serverlogboek|Geen dimensies|
|serverlog_storage_usage|Yes|Gebruikte serverlogboekopslag|Bytes|Gemiddeld|Gebruikte serverlogboekopslag|Geen dimensies|
|storage_limit|Yes|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen dimensies|
|storage_percent|Yes|Opslag percentage|Percentage|Gemiddeld|Opslag percentage|Geen dimensies|
|storage_used|Yes|Gebruikte opslag|Bytes|Gemiddeld|Gebruikte opslag|Geen dimensies|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Afgebroken verbindingen|Aantal|Totaal|Afgebroken verbindingen|Geen dimensies|
|active_connections|Yes|Actieve verbindingen|Count|Maximum|Actieve verbindingen|Geen dimensies|
|backup_storage_used|Yes|Gebruikte back-upopslag|Bytes|Maximum|Gebruikte back-upopslag|Geen dimensies|
|cpu_percent|Yes|CPU-percentage van host|Percentage|Maximum|CPU-percentage van host|Geen dimensies|
|io_consumption_percent|Yes|IO-percentage|Percentage|Maximum|IO-percentage|Geen dimensies|
|memory_percent|Yes|Percentage hostgeheugen|Percentage|Maximum|Percentage hostgeheugen|Geen dimensies|
|network_bytes_egress|Yes|Hostnetwerk uit|Bytes|Totaal|Hostnetwerk-egress in bytes|Geen dimensies|
|network_bytes_ingress|Yes|Hostnetwerk in|Bytes|Totaal|Netwerkingressie hosten in bytes|Geen dimensies|
|Query's|Yes|Query's|Aantal|Totaal|Query's|Geen dimensies|
|replication_lag|Yes|Replicatievertraging in seconden|Seconden|Maximum|Replicatievertraging in seconden|Geen dimensies|
|storage_limit|Yes|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen dimensies|
|storage_percent|Yes|Opslag percentage|Percentage|Maximum|Opslag percentage|Geen dimensies|
|storage_used|Yes|Gebruikte opslag|Bytes|Maximum|Gebruikte opslag|Geen dimensies|
|total_connections|Yes|Totaal aantal verbindingen|Aantal|Totaal|Totaal aantal verbindingen|Geen dimensies|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_connections|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|Geen dimensies|
|backup_storage_used|Yes|Back-upopslag gebruikt|Bytes|Gemiddeld|Back-upopslag gebruikt|Geen dimensies|
|connections_failed|Yes|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|io_consumption_percent|Yes|IO-percentage|Percentage|Gemiddeld|IO-percentage|Geen dimensies|
|memory_percent|Yes|Geheugen percentage|Percentage|Gemiddeld|Geheugen percentage|Geen dimensies|
|network_bytes_egress|Yes|Netwerk uit|Bytes|Totaal|Netwerk uit via actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Yes|Netwerk in|Bytes|Totaal|Netwerk in via actieve verbindingen|Geen dimensies|
|seconds_behind_master|Yes|Replicatievertraging in seconden|Count|Maximum|Replicatievertraging in seconden|Geen dimensies|
|serverlog_storage_limit|Yes|Opslaglimiet voor serverlogboek|Bytes|Maximum|Opslaglimiet voor serverlogboek|Geen dimensies|
|serverlog_storage_percent|Yes|Percentage serverlogboekopslag|Percentage|Gemiddeld|Opslag percentage serverlogboek|Geen dimensies|
|serverlog_storage_usage|Yes|Gebruikte serverlogboekopslag|Bytes|Gemiddeld|Gebruikte serverlogboekopslag|Geen dimensies|
|storage_limit|Yes|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen dimensies|
|storage_percent|Yes|Opslag percentage|Percentage|Gemiddeld|Opslag percentage|Geen dimensies|
|storage_used|Yes|Gebruikte opslag|Bytes|Gemiddeld|Gebruikte opslag|Geen dimensies|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_connections|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|Geen dimensies|
|backup_storage_used|Yes|Gebruikte back-upopslag|Bytes|Gemiddeld|Gebruikte back-upopslag|Geen dimensies|
|connections_failed|Yes|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|connections_succeeded|Yes|Geslaagd verbindingen|Aantal|Totaal|Geslaagd verbindingen|Geen dimensies|
|cpu_credits_consumed|Yes|Verbruikt CPU-tegoed|Count|Gemiddeld|Totaal aantal tegoeden dat door de databaseserver is verbruikt|Geen dimensies|
|cpu_credits_remaining|Yes|Resterend CPU-tegoed|Count|Gemiddeld|Totaal aantal tegoeden dat beschikbaar is voor burst|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|disk_queue_depth|Yes|Schijfwachtrijdiepte|Count|Gemiddeld|Aantal openstaande I/O-bewerkingen op de gegevensschijf|Geen dimensies|
|iops|Yes|IOPS|Count|Gemiddeld|IO-bewerkingen per seconde|Geen dimensies|
|maximum_used_transactionIDs|Yes|Maximaal aantal gebruikte transactie-ID's|Count|Gemiddeld|Maximaal aantal gebruikte transactie-ID's|Geen dimensies|
|memory_percent|Yes|Geheugen-percentage|Percentage|Gemiddeld|Geheugen-percentage|Geen dimensies|
|network_bytes_egress|Yes|Netwerk uit|Bytes|Totaal|Netwerk uit via actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Yes|Netwerk in|Bytes|Totaal|Netwerk in via actieve verbindingen|Geen dimensies|
|read_iops|Yes|IOPS lezen|Count|Gemiddeld|Aantal I/O-leesbewerkingen voor gegevensschijven per seconde|Geen dimensies|
|read_throughput|Yes|Doorvoer bytes per seconde lezen|Count|Gemiddeld|Bytes die per seconde van de gegevensschijf worden gelezen tijdens de bewakingsperiode|Geen dimensies|
|storage_free|Yes|Gratis opslag|Bytes|Gemiddeld|Gratis opslag|Geen dimensies|
|storage_percent|Yes|Opslag percentage|Percentage|Gemiddeld|Opslag percentage|Geen dimensies|
|storage_used|Yes|Gebruikte opslag|Bytes|Gemiddeld|Gebruikte opslag|Geen dimensies|
|txlogs_storage_used|Yes|Transactielogboekopslag gebruikt|Bytes|Gemiddeld|Gebruikte opslag voor transactielogboek|Geen dimensies|
|write_iops|Yes|IOPS schrijven|Count|Gemiddeld|Aantal I/O-schrijfbewerkingen voor gegevensschijven per seconde|Geen dimensies|
|write_throughput|Yes|Geschreven doorvoer bytes per seconde|Count|Gemiddeld|Bytes geschreven per seconde naar de gegevensschijf tijdens de bewakingsperiode|Geen dimensies|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_connections|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|ServerName|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|ServerName|
|iops|Yes|IOPS|Count|Gemiddeld|I/O-bewerkingen per seconde|ServerName|
|memory_percent|Yes|Geheugen percentage|Percentage|Gemiddeld|Geheugen percentage|ServerName|
|network_bytes_egress|Yes|Netwerk uit|Bytes|Totaal|Netwerk uit via actieve verbindingen|ServerName|
|network_bytes_ingress|Yes|Netwerk in|Bytes|Totaal|Netwerk in via actieve verbindingen|ServerName|
|storage_percent|Yes|Opslag percentage|Percentage|Gemiddeld|Opslag percentage|ServerName|
|storage_used|Yes|Gebruikte opslag|Bytes|Gemiddeld|Gebruikte opslag|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_connections|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|Geen dimensies|
|backup_storage_used|Yes|Gebruikte back-upopslag|Bytes|Gemiddeld|Back-upopslag gebruikt|Geen dimensies|
|connections_failed|Yes|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|io_consumption_percent|Yes|IO-percentage|Percentage|Gemiddeld|IO-percentage|Geen dimensies|
|memory_percent|Yes|Geheugen-percentage|Percentage|Gemiddeld|Geheugen-percentage|Geen dimensies|
|network_bytes_egress|Yes|Netwerk uit|Bytes|Totaal|Netwerk uit via actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Yes|Netwerk in|Bytes|Totaal|Netwerk in via actieve verbindingen|Geen dimensies|
|pg_replica_log_delay_in_bytes|Yes|Max Lag Across Replicas|Bytes|Maximum|Vertraging in bytes van de replica met de meeste vertraging|Geen dimensies|
|pg_replica_log_delay_in_seconds|Yes|Replicavertraging|Seconden|Maximum|Replicavertraging in seconden|Geen dimensies|
|serverlog_storage_limit|Yes|Opslaglimiet voor serverlogboek|Bytes|Maximum|Opslaglimiet voor serverlogboek|Geen dimensies|
|serverlog_storage_percent|Yes|Percentage serverlogboekopslag|Percentage|Gemiddeld|Percentage serverlogboekopslag|Geen dimensies|
|serverlog_storage_usage|Yes|Gebruikte serverlogboekopslag|Bytes|Gemiddeld|Gebruikte serverlogboekopslag|Geen dimensies|
|storage_limit|Yes|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen dimensies|
|storage_percent|Yes|Opslag percentage|Percentage|Gemiddeld|Opslag percentage|Geen dimensies|
|storage_used|Yes|Gebruikte opslag|Bytes|Gemiddeld|Gebruikte opslag|Geen dimensies|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_connections|Yes|Actieve verbindingen|Count|Gemiddeld|Actieve verbindingen|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|iops|Yes|IOPS|Count|Gemiddeld|IO-bewerkingen per seconde|Geen dimensies|
|memory_percent|Yes|Geheugen percentage|Percentage|Gemiddeld|Geheugen percentage|Geen dimensies|
|network_bytes_egress|Yes|Netwerk uit|Bytes|Totaal|Netwerk uit via actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Yes|Netwerk in|Bytes|Totaal|Netwerk in via actieve verbindingen|Geen dimensies|
|storage_percent|Yes|Opslag percentage|Percentage|Gemiddeld|Opslag percentage|Geen dimensies|
|storage_used|Yes|Gebruikte opslag|Bytes|Gemiddeld|Gebruikte opslag|Geen dimensies|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|aangevraagd gebruikspercentage|Percentage|Gemiddeld|aangevraagd gebruikspercentage|Geen dimensies|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|C2D-berichten verlaten|Aantal|Totaal|Aantal cloud-naar-apparaat-berichten dat is verlaten door het apparaat|Geen dimensies|
|c2d.commands.egress.complete.success|Yes|Leveringen van C2D-berichten voltooid|Aantal|Totaal|Aantal cloud-naar-apparaat-berichtleveringen dat is voltooid door het apparaat|Geen dimensies|
|c2d.commands.egress.reject.success|Yes|C2D-berichten geweigerd|Aantal|Totaal|Aantal cloud-naar-apparaat-berichten dat is afgewezen door het apparaat|Geen dimensies|
|c2d.methods.failure|Yes|Mislukte aanroepen van directe methoden|Aantal|Totaal|Het aantal mislukte aanroepen van de directe methode.|Geen dimensies|
|c2d.methods.requestSize|Yes|Aanvraaggrootte van aanroepen van directe methoden|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van alle geslaagde aanvragen voor directe methoden.|Geen dimensies|
|c2d.methods.responseSize|Yes|Antwoordgrootte van aanroepen van directe methoden|Bytes|Gemiddeld|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties op directe methoden.|Geen dimensies|
|c2d.methods.success|Yes|Geslaagde aanroepen van directe methoden|Aantal|Totaal|Het aantal geslaagde aanroepen van directe methoden.|Geen dimensies|
|c2d.twin.read.failure|Yes|Mislukte dubbele leesingen van back-end|Aantal|Totaal|Het aantal mislukte door de back-end geïnitieerde dubbele lees lezen.|Geen dimensies|
|c2d.twin.read.size|Yes|Antwoordgrootte van dubbele leesingen van back-end|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van alle geslaagde door de back-end geïnitieerde dubbele lees lezen.|Geen dimensies|
|c2d.twin.read.success|Yes|Geslaagde dubbele lees leest van back-end|Aantal|Totaal|Het aantal geslaagde door de back-end geïnitieerde dubbele lees leest.|Geen dimensies|
|c2d.twin.update.failure|Yes|Mislukte tweelingupdates van back-end|Aantal|Totaal|Het aantal mislukte, door de back-end geïnitieerde tweelingupdates.|Geen dimensies|
|c2d.twin.update.size|Yes|Grootte van dubbele updates van back-end|Bytes|Gemiddeld|De gemiddelde, minimum- en maximale grootte van alle geslaagde, door de back-end geïnitieerde tweelingupdates.|Geen dimensies|
|c2d.twin.update.success|Yes|Geslaagde dubbele updates van back-end|Aantal|Totaal|Het aantal geslaagde, door de back-end geïnitieerde tweelingupdates.|Geen dimensies|
|C2DMessagesExpired|Yes|C2D-berichten verlopen|Aantal|Totaal|Aantal verlopen cloud-naar-apparaat-berichten|Geen dimensies|
|Configuraties|Yes|Metrische configuratiegegevens|Aantal|Totaal|Metrische gegevens voor configuratiebewerkingen|Geen dimensies|
|connectedDeviceCount|Yes|Verbonden apparaten|Count|Gemiddeld|Aantal apparaten dat is verbonden met uw IoT-hub|Geen dimensies|
|d2c.endpoints.egress.builtIn.events|Yes|Routering: berichten die worden bezorgd bij berichten/gebeurtenissen|Aantal|Totaal|Het aantal keren dat IoT Hub met succes bezorgde berichten naar het ingebouwde eindpunt (berichten/gebeurtenissen) routeren.|Geen dimensies|
|d2c.endpoints.egress.eventHubs|Yes|Routering: berichten die worden bezorgd bij Event Hub|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van berichten naar Event Hub-eindpunten.|Geen dimensies|
|d2c.endpoints.egress.serviceBusQueues|Yes|Routering: berichten die aan Service Bus wachtrij worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub met succes bezorgde berichten naar Service Bus wachtrij-eindpunten routeren.|Geen dimensies|
|d2c.endpoints.egress.serviceBusTopics|Yes|Routering: berichten die worden bezorgd Service Bus onderwerp|Aantal|Totaal|Het aantal keren dat IoT Hub met succes bezorgde berichten naar Service Bus eindpunten van het onderwerp.|Geen dimensies|
|d2c.endpoints.egress.storage|Yes|Routering: berichten die aan de opslag worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub met succes bezorgde berichten naar opslag eindpunten routeren.|Geen dimensies|
|d2c.endpoints.egress.storage.blobs|Yes|Routering: blobs die aan de opslag worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van geleverde blobs naar opslag eindpunten.|Geen dimensies|
|d2c.endpoints.egress.storage.bytes|Yes|Routering: gegevens die aan de opslag worden geleverd|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub routering die aan opslag eindpunten wordt geleverd.|Geen dimensies|
|d2c.endpoints.latency.builtIn.events|Yes|Routering: berichtlatentie voor berichten/gebeurtenissen|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen ingressie van berichten IoT Hub telemetrieberichten in het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen dimensies|
|d2c.endpoints.latency.eventHubs|Yes|Routering: berichtlatentie voor Event Hub|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het in- en IoT Hub van berichten naar een Event Hub-eindpunt.|Geen dimensies|
|d2c.endpoints.latency.serviceBusQueues|Yes|Routering: berichtlatentie voor Service Bus wachtrij|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het ingressiebericht naar IoT Hub en het binnenlaten van een telemetriebericht in Service Bus eindpunt van de wachtrij.|Geen dimensies|
|d2c.endpoints.latency.serviceBusTopics|Yes|Routering: berichtlatentie voor Service Bus onderwerp|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het ingressiebericht naar IoT Hub en het binnenlaten van telemetrie-berichten naar Service Bus eindpunt van een onderwerp.|Geen dimensies|
|d2c.endpoints.latency.storage|Yes|Routering: berichtlatentie voor opslag|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het ingressiebericht naar IoT Hub en het binnenlaten van telemetrie-berichten naar een opslag-eindpunt.|Geen dimensies|
|d2c.telemetry.egress.dropped|Yes|Routering: telemetrieberichten die zijn uitgevallen |Aantal|Totaal|Het aantal keren dat berichten zijn weggevallen door IoT Hub routering vanwege inlopende eindpunten. Deze waarde telt niet mee voor berichten die aan de terugvalroute worden geleverd, omdat daar geen uitgevallen berichten worden afgeleverd.|Geen dimensies|
|d2c.telemetry.egress.fallback|Yes|Routering: berichten bezorgd bij terugval|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van bezorgde berichten naar het eindpunt dat is gekoppeld aan de terugvalroute.|Geen dimensies|
|d2c.telemetry.egress.invalid|Yes|Routering: telemetrieberichten zijn niet compatibel|Aantal|Totaal|Het aantal keren dat IoT Hub berichten niet kon verzenden vanwege een incompatibiliteit met het eindpunt. Deze waarde omvat geen nieuwe proberen.|Geen dimensies|
|d2c.telemetry.egress.orphaned|Yes|Routering: zwevende telemetrieberichten |Aantal|Totaal|Het aantal keren dat berichten zwevend waren IoT Hub routering omdat ze niet overeen kwamen met routeringsregels (inclusief de terugvalregel). |Geen dimensies|
|d2c.telemetry.egress.success|Yes|Routering: bezorgde telemetrieberichten|Aantal|Totaal|Het aantal keren dat berichten met succes aan alle eindpunten zijn afgeleverd met behulp van IoT Hub routering. Als een bericht wordt doorgeleid naar meerdere eindpunten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren aan hetzelfde eindpunt wordt geleverd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen dimensies|
|d2c.telemetry.ingress.allProtocol|Yes|Verzendpogingen voor telemetriebericht|Aantal|Totaal|Aantal apparaat-naar-cloud-telemetrieberichten dat is verzonden naar uw IoT-hub|Geen dimensies|
|d2c.telemetry.ingress.sendThrottle|Yes|Aantal beperkingsfouten|Aantal|Totaal|Aantal beperkingsfouten vanwege vertragingen in de doorvoer van apparaten|Geen dimensies|
|d2c.telemetry.ingress.success|Yes|Verzonden telemetrieberichten|Aantal|Totaal|Aantal apparaat-naar-cloud-telemetrieberichten dat is verzonden naar uw IoT-hub|Geen dimensies|
|d2c.twin.read.failure|Yes|Mislukte dubbele lees lezen van apparaten|Aantal|Totaal|Het aantal mislukte door het apparaat geïnitieerde dubbele lees lezen.|Geen dimensies|
|d2c.twin.read.size|Yes|Antwoordgrootte van dubbele lees-uitingen van apparaten|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van alle geslaagde door het apparaat geïnitieerde dubbele lees lezen.|Geen dimensies|
|d2c.twin.read.success|Yes|Geslaagde dubbele lees lezen van apparaten|Aantal|Totaal|Het aantal geslaagde door het apparaat geïnitieerde dubbele leesingen.|Geen dimensies|
|d2c.twin.update.failure|Yes|Mislukte tweelingupdates van apparaten|Aantal|Totaal|Het aantal mislukte door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|d2c.twin.update.size|Yes|Grootte van dubbele updates van apparaten|Bytes|Gemiddeld|De gemiddelde, minimum- en maximumgrootte van alle geslaagde door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|d2c.twin.update.success|Yes|Geslaagde dubbele updates van apparaten|Aantal|Totaal|Het aantal geslaagde door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|dailyMessageQuotaUsed|Yes|Totaal aantal gebruikte berichten|Count|Maximum|Het totale aantal berichten dat vandaag is gebruikt|Geen dimensies|
|deviceDataUsage|Yes|Totaal gebruik van apparaatgegevens|Bytes|Totaal|Bytes overgedragen naar en van apparaten die zijn verbonden met IotHub|Geen dimensies|
|deviceDataUsageV2|Yes|Totaal gebruik van apparaatgegevens (preview)|Bytes|Totaal|Bytes overgedragen naar en van apparaten die zijn verbonden met IotHub|Geen dimensies|
|devices.connectedDevices.allProtocol|Yes|Verbonden apparaten (afgeschaft) |Aantal|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen dimensies|
|devices.totalDevices|Yes|Totaal aantal apparaten (afgeschaft)|Aantal|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen dimensies|
|EventGridDeliveries|Yes|Event Grid leveringen|Aantal|Totaal|Het aantal gebeurtenissen IoT Hub gepubliceerd naar Event Grid. Gebruik de dimensie Resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie EventType toont het type gebeurtenis ( https://aka.ms/ioteventgrid) .|Resultaat, EventType|
|EventGridLatency|Yes|Event Grid latentie|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) vanaf het moment dat de IoT Hub-gebeurtenis werd gegenereerd tot het moment dat de gebeurtenis werd gepubliceerd naar Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenistypen. Gebruik de dimensie EventType om de latentie van een specifiek type gebeurtenis te bekijken.|EventType|
|jobs.cancelJob.failure|Yes|Mislukte taakonderdrukkingen|Aantal|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen dimensies|
|jobs.cancelJob.success|Yes|Geslaagde taakonderzegging|Aantal|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen dimensies|
|jobs.completed|Yes|Voltooide taken|Aantal|Totaal|Het aantal voltooide taken.|Geen dimensies|
|jobs.createDirectMethodJob.failure|Yes|Mislukte creatie van methode-aanroeptaken|Aantal|Totaal|Het aantal mislukte aanroeptaken voor directe methoden.|Geen dimensies|
|jobs.createDirectMethodJob.success|Yes|Geslaagde creatie van methode-aanroeptaken|Aantal|Totaal|Het aantal geslaagde aanroeptaken voor directe methoden.|Geen dimensies|
|jobs.createTwinUpdateJob.failure|Yes|Mislukte creatie van dubbelupdatetaken|Aantal|Totaal|Het aantal mislukte aanmaaktaken van dubbelupdates.|Geen dimensies|
|jobs.createTwinUpdateJob.success|Yes|Geslaagde creatie van dubbelupdatetaken|Aantal|Totaal|Het aantal geslaagde tweelingupdatetaken.|Geen dimensies|
|jobs.failed|Yes|Mislukte taken|Aantal|Totaal|Het aantal mislukte taken.|Geen dimensies|
|jobs.listJobs.failure|Yes|Mislukte aanroepen voor lijsttaken|Aantal|Totaal|Het aantal mislukte aanroepen voor lijsttaken.|Geen dimensies|
|jobs.listJobs.success|Yes|Geslaagde aanroepen voor lijsttaken|Aantal|Totaal|Het aantal geslaagde aanroepen voor lijsttaken.|Geen dimensies|
|jobs.queryJobs.failure|Yes|Mislukte taakquery's|Aantal|Totaal|Het aantal mislukte aanroepen naar querytaken.|Geen dimensies|
|jobs.queryJobs.success|Yes|Geslaagde taakquery's|Aantal|Totaal|Het aantal geslaagde aanroepen naar querytaken.|Geen dimensies|
|RoutingDataSizeInBytesDelivered|Yes|Berichtgrootte routeringslevering in bytes (preview)|Bytes|Totaal|De totale grootte in bytes aan berichten die door IoT Hub aan een eindpunt worden geleverd. U kunt de dimensies EndpointName en EndpointType gebruiken om de grootte van de berichten weer te geven in bytes die aan uw verschillende eindpunten worden geleverd. De metrische waarde neemt toe voor elk bericht dat wordt bezorgd, inclusief of het bericht aan meerdere eindpunten wordt geleverd of als het bericht meerdere keren aan hetzelfde eindpunt wordt geleverd.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Leveringen routeren (preview)|Aantal|Totaal|Het aantal keren dat IoT Hub berichten aan alle eindpunten probeerde te leveren met behulp van routering. Als u het aantal geslaagde of mislukte pogingen wilt zien, gebruikt u de dimensie Resultaat. Gebruik de dimensie FailureReasonCategory om de reden van de fout te zien, zoals ongeldig, uitgevallen of zwevend. U kunt ook de dimensies EndpointName en EndpointType gebruiken om te begrijpen hoeveel berichten aan uw verschillende eindpunten zijn afgeleverd. De metrische waarde wordt voor elke bezorgingspoging met één verhoogd, inclusief of het bericht aan meerdere eindpunten wordt geleverd of als het bericht meerdere keren aan hetzelfde eindpunt wordt geleverd.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Yes|Routeringsleveringslatentie (preview)|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het ingressiebericht IoT Hub telemetriebericht in een eindpunt. U kunt de dimensies EndpointName en EndpointType gebruiken om inzicht te krijgen in de latentie voor uw verschillende eindpunten.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Yes|aangevraagd gebruikspercentage|Percentage|Gemiddeld|aangevraagd gebruikspercentage|Geen dimensies|
|totalDeviceCount|Yes|Totaal aantal apparaten|Count|Gemiddeld|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen dimensies|
|twinQueries.failure|Yes|Mislukte dubbelquery's|Aantal|Totaal|Het aantal mislukte dubbelquery's.|Geen dimensies|
|twinQueries.resultSize|Yes|Resultaatgrootte van dubbelquery's|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van de resultaatgrootte van alle geslaagde tweelingquery's.|Geen dimensies|
|twinQueries.success|Yes|Geslaagde dubbelquery's|Aantal|Totaal|Het aantal geslaagde dubbelquery's.|Geen dimensies|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|C2D-berichten verlaten|Aantal|Totaal|Aantal cloud-naar-apparaat-berichten dat is verlaten door het apparaat|Geen dimensies|
|c2d.commands.egress.complete.success|Yes|Leveringen van C2D-berichten voltooid|Aantal|Totaal|Aantal cloud-naar-apparaat-berichtleveringen dat is voltooid door het apparaat|Geen dimensies|
|c2d.commands.egress.reject.success|Yes|C2D-berichten geweigerd|Aantal|Totaal|Aantal cloud-naar-apparaat-berichten dat is afgewezen door het apparaat|Geen dimensies|
|c2d.methods.failure|Yes|Mislukte aanroepen van directe methoden|Aantal|Totaal|Het aantal mislukte aanroepen van directe methoden.|Geen dimensies|
|c2d.methods.requestSize|Yes|Aanvraaggrootte van aanroepen van directe methoden|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van alle geslaagde aanvragen voor directe methoden.|Geen dimensies|
|c2d.methods.responseSize|Yes|Antwoordgrootte van aanroepen van directe methoden|Bytes|Gemiddeld|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties op directe methoden.|Geen dimensies|
|c2d.methods.success|Yes|Geslaagde aanroepen van directe methoden|Aantal|Totaal|Het aantal geslaagde aanroepen van directe methoden.|Geen dimensies|
|c2d.twin.read.failure|Yes|Mislukte dubbele lees-uit-back-end|Aantal|Totaal|Het aantal mislukte door de back-end geïnitieerde dubbele lees lezen.|Geen dimensies|
|c2d.twin.read.size|Yes|Antwoordgrootte van dubbele lees-uit-back-end|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van alle geslaagde door de back-end geïnitieerde dubbele lees lezen.|Geen dimensies|
|c2d.twin.read.success|Yes|Geslaagde dubbele lees-uit-back-end|Aantal|Totaal|Het aantal geslaagde door de back-end geïnitieerde dubbele lees lezen.|Geen dimensies|
|c2d.twin.update.failure|Yes|Mislukte tweelingupdates van back-end|Aantal|Totaal|Het aantal mislukte, door de back-end geïnitieerde tweelingupdates.|Geen dimensies|
|c2d.twin.update.size|Yes|Grootte van dubbele updates van back-end|Bytes|Gemiddeld|De gemiddelde, minimum- en maximale grootte van alle geslaagde, door de back-end geïnitieerde tweelingupdates.|Geen dimensies|
|c2d.twin.update.success|Yes|Geslaagde tweelingupdates van back-end|Aantal|Totaal|Het aantal geslaagde, door de back-end geïnitieerde tweelingupdates.|Geen dimensies|
|C2DMessagesExpired|Yes|C2D-berichten verlopen|Aantal|Totaal|Aantal verlopen cloud-naar-apparaat-berichten|Geen dimensies|
|Configuraties|Yes|Metrische configuratiegegevens|Aantal|Totaal|Metrische gegevens voor configuratiebewerkingen|Geen dimensies|
|connectedDeviceCount|No|Verbonden apparaten|Count|Gemiddeld|Aantal apparaten dat is verbonden met uw IoT-hub|Geen dimensies|
|d2c.endpoints.egress.builtIn.events|Yes|Routering: berichten die worden bezorgd bij berichten/gebeurtenissen|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van bezorgde berichten naar het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen dimensies|
|d2c.endpoints.egress.eventHubs|Yes|Routering: berichten die worden geleverd aan Event Hub|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van bezorgde berichten naar Event Hub-eindpunten.|Geen dimensies|
|d2c.endpoints.egress.serviceBusQueues|Yes|Routering: berichten die aan Service Bus wachtrij worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub met succes bezorgde berichten naar Service Bus wachtrij-eindpunten routeren.|Geen dimensies|
|d2c.endpoints.egress.serviceBusTopics|Yes|Routering: berichten bezorgd bij Service Bus Onderwerp|Aantal|Totaal|Het aantal keren dat IoT Hub met succes bezorgde berichten naar Service Bus onderwerp-eindpunten routeren.|Geen dimensies|
|d2c.endpoints.egress.storage|Yes|Routering: berichten die aan de opslag worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van bezorgde berichten naar opslag-eindpunten.|Geen dimensies|
|d2c.endpoints.egress.storage.blobs|Yes|Routering: blobs die aan de opslag worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van geleverde blobs naar opslag-eindpunten.|Geen dimensies|
|d2c.endpoints.egress.storage.bytes|Yes|Routering: gegevens die aan de opslag worden geleverd|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub aan opslag eindpunten worden geleverd.|Geen dimensies|
|d2c.endpoints.latency.builtIn.events|Yes|Routering: berichtlatentie voor berichten/gebeurtenissen|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het ingressiebericht naar IoT Hub en het binnenvallen van telemetrieberichten naar het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen dimensies|
|d2c.endpoints.latency.eventHubs|Yes|Routering: berichtlatentie voor Event Hub|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het in- en IoT Hub van berichten naar een Event Hub-eindpunt.|Geen dimensies|
|d2c.endpoints.latency.serviceBusQueues|Yes|Routering: berichtlatentie voor Service Bus wachtrij|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen het ingressiebericht naar IoT Hub en de ingress van telemetrie naar Service Bus eindpunt van een wachtrij.|Geen dimensies|
|d2c.endpoints.latency.serviceBusTopics|Yes|Routering: berichtlatentie voor Service Bus Onderwerp|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen ingressie van berichten IoT Hub telemetriebericht in een Service Bus-eindpunt.|Geen dimensies|
|d2c.endpoints.latency.storage|Yes|Routering: berichtlatentie voor opslag|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen ingressie van berichten IoT Hub telemetriebericht in een opslag-eindpunt.|Geen dimensies|
|d2c.telemetry.egress.dropped|Yes|Routering: telemetrieberichten die zijn weggevallen |Aantal|Totaal|Het aantal keren dat berichten zijn weggevallen door IoT Hub vanwege inlopende eindpunten. Deze waarde telt niet de berichten die aan de terugvalroute worden geleverd, omdat daar geen uitgevallen berichten worden afgeleverd.|Geen dimensies|
|d2c.telemetry.egress.fallback|Yes|Routering: berichten die aan terugval worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub doorsturen van bezorgde berichten naar het eindpunt dat is gekoppeld aan de terugvalroute.|Geen dimensies|
|d2c.telemetry.egress.invalid|Yes|Routering: telemetrieberichten die niet compatibel zijn|Aantal|Totaal|Het aantal keren IoT Hub routering geen berichten kon leveren vanwege een incompatibiliteit met het eindpunt. Deze waarde omvat geen nieuwe proberen.|Geen dimensies|
|d2c.telemetry.egress.orphaned|Yes|Routering: zwevende telemetrieberichten |Aantal|Totaal|Het aantal keren dat berichten zijn zweven door IoT Hub routering omdat ze niet overeenkomen met routeringsregels (inclusief de terugvalregel). |Geen dimensies|
|d2c.telemetry.egress.success|Yes|Routering: geleverde telemetrieberichten|Aantal|Totaal|Het aantal keren dat berichten aan alle eindpunten zijn afgeleverd met behulp van IoT Hub routering. Als een bericht wordt doorgeleid naar meerdere eindpunten, wordt deze waarde voor elke geslaagde levering met één verhoogd. Als een bericht meerdere keren aan hetzelfde eindpunt wordt geleverd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen dimensies|
|d2c.telemetry.ingress.allProtocol|Yes|Verzendpogingen voor telemetriebericht|Aantal|Totaal|Het aantal apparaat-naar-cloud-telemetrieberichten dat is verzonden naar uw IoT-hub|Geen dimensies|
|d2c.telemetry.ingress.sendThrottle|Yes|Aantal beperkingsfouten|Aantal|Totaal|Aantal beperkingsfouten als gevolg van vertragingen in de doorvoer van apparaten|Geen dimensies|
|d2c.telemetry.ingress.success|Yes|Verzonden telemetrieberichten|Aantal|Totaal|Aantal apparaat-naar-cloud-telemetrieberichten verzonden naar uw IoT-hub|Geen dimensies|
|d2c.twin.read.failure|Yes|Mislukte dubbele lees-uitingen van apparaten|Aantal|Totaal|Het aantal mislukte door het apparaat geïnitieerde dubbele lees lezen.|Geen dimensies|
|d2c.twin.read.size|Yes|Antwoordgrootte van dubbele lees- en antwoordgrootte van apparaten|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van alle geslaagde door het apparaat geïnitieerde dubbele lees- en leesstappen.|Geen dimensies|
|d2c.twin.read.success|Yes|Geslaagde dubbele lees lezen van apparaten|Aantal|Totaal|Het aantal geslaagde door het apparaat geïnitieerde dubbele lees lezen.|Geen dimensies|
|d2c.twin.update.failure|Yes|Mislukte tweelingupdates van apparaten|Aantal|Totaal|Het aantal mislukte door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|d2c.twin.update.size|Yes|Grootte van dubbele updates van apparaten|Bytes|Gemiddeld|De gemiddelde, minimum- en maximumgrootte van alle geslaagde door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|d2c.twin.update.success|Yes|Geslaagde tweelingupdates vanaf apparaten|Aantal|Totaal|Het aantal geslaagde door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|dailyMessageQuotaUsed|Yes|Totaal aantal gebruikte berichten|Count|Maximum|Het totale aantal berichten dat vandaag is gebruikt|Geen dimensies|
|deviceDataUsage|Yes|Totaal gebruik van apparaatgegevens|Bytes|Totaal|Bytes overgedragen naar en van apparaten die zijn verbonden met IotHub|Geen dimensies|
|deviceDataUsageV2|Yes|Totaal gebruik van apparaatgegevens (preview)|Bytes|Totaal|Bytes overgedragen naar en van apparaten die zijn verbonden met IotHub|Geen dimensies|
|devices.connectedDevices.allProtocol|Yes|Verbonden apparaten (afgeschaft) |Aantal|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen dimensies|
|devices.totalDevices|Yes|Totaal aantal apparaten (afgeschaft)|Aantal|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen dimensies|
|EventGridDeliveries|Yes|Event Grid leveringen|Aantal|Totaal|Het aantal gebeurtenissen IoT Hub gepubliceerd naar Event Grid. Gebruik de dimensie Resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie EventType toont het type gebeurtenis ( https://aka.ms/ioteventgrid) .|Resultaat, EventType|
|EventGridLatency|Yes|Event Grid latentie|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) vanaf het moment dat de IoT Hub-gebeurtenis werd gegenereerd tot toen de gebeurtenis werd gepubliceerd naar Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenistypen. Gebruik de dimensie EventType om de latentie van een specifiek type gebeurtenis te bekijken.|EventType|
|jobs.cancelJob.failure|Yes|Mislukte taakonderdrukkingen|Aantal|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen dimensies|
|jobs.cancelJob.success|Yes|Geslaagde taakonderdrukkingen|Aantal|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen dimensies|
|jobs.completed|Yes|Voltooide taken|Aantal|Totaal|Het aantal voltooide taken.|Geen dimensies|
|jobs.createDirectMethodJob.failure|Yes|Mislukte creatie van methode-aanroeptaken|Aantal|Totaal|Het aantal mislukte aanroeptaken voor directe methoden.|Geen dimensies|
|jobs.createDirectMethodJob.success|Yes|Geslaagde creatie van methode-aanroeptaken|Aantal|Totaal|Het aantal geslaagde aanroeptaken voor directe methoden.|Geen dimensies|
|jobs.createTwinUpdateJob.failure|Yes|Mislukte creatie van dubbelupdatetaken|Aantal|Totaal|Het aantal mislukte taken voor het maken van tweelingupdates.|Geen dimensies|
|jobs.createTwinUpdateJob.success|Yes|Geslaagde creatie van dubbelupdatetaken|Aantal|Totaal|Het aantal geslaagde tweelingupdatetaken.|Geen dimensies|
|jobs.failed|Yes|Mislukte taken|Aantal|Totaal|Het aantal mislukte taken.|Geen dimensies|
|jobs.listJobs.failure|Yes|Mislukte aanroepen voor lijsttaken|Aantal|Totaal|Het aantal mislukte aanroepen voor lijsttaken.|Geen dimensies|
|jobs.listJobs.success|Yes|Geslaagde aanroepen voor lijsttaken|Aantal|Totaal|Het aantal geslaagde aanroepen voor lijsttaken.|Geen dimensies|
|jobs.queryJobs.failure|Yes|Mislukte taakquery's|Aantal|Totaal|Het aantal mislukte aanroepen voor querytaken.|Geen dimensies|
|jobs.queryJobs.success|Yes|Geslaagde taakquery's|Aantal|Totaal|Het aantal geslaagde aanroepen voor querytaken.|Geen dimensies|
|RoutingDataSizeInBytesDelivered|Yes|Berichtgrootte routering in bytes (preview)|Bytes|Totaal|De totale grootte in bytes van berichten die door IoT Hub aan een eindpunt worden geleverd. U kunt de dimensies EndpointName en EndpointType gebruiken om de grootte van de berichten weer te geven in bytes die aan uw verschillende eindpunten worden geleverd. De metrische waarde neemt toe voor elk bericht dat wordt bezorgd, inclusief of het bericht aan meerdere eindpunten wordt geleverd of als het bericht meerdere keren aan hetzelfde eindpunt wordt geleverd.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Leveringen routeren (preview)|Aantal|Totaal|Het aantal keren dat IoT Hub berichten aan alle eindpunten probeerde te leveren met behulp van routering. Als u het aantal geslaagde of mislukte pogingen wilt zien, gebruikt u de dimensie Resultaat. Gebruik de dimensie FailureReasonCategory om de reden van de fout te zien, zoals ongeldig, uitgevallen of zwevend. U kunt ook de dimensies EndpointName en EndpointType gebruiken om te begrijpen hoeveel berichten er aan uw verschillende eindpunten zijn geleverd. De metrische waarde wordt voor elke bezorgingspoging met één verhoogd, inclusief als het bericht aan meerdere eindpunten wordt geleverd of als het bericht meerdere keren aan hetzelfde eindpunt wordt geleverd.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Yes|Routeringsleveringslatentie (preview)|Milliseconden|Gemiddeld|De gemiddelde latentie (milliseconden) tussen ingressie van berichten IoT Hub telemetriebericht in een eindpunt. U kunt de dimensies EndpointName en EndpointType gebruiken om inzicht te krijgen in de latentie voor uw verschillende eindpunten.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Totaal aantal apparaten|Count|Gemiddeld|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen dimensies|
|twinQueries.failure|Yes|Mislukte dubbelquery's|Aantal|Totaal|Het aantal mislukte dubbelquery's.|Geen dimensies|
|twinQueries.resultSize|Yes|Resultaatgrootte van dubbelquery's|Bytes|Gemiddeld|Het gemiddelde, minimum en maximum van de resultaatgrootte van alle geslaagde tweelingquery's.|Geen dimensies|
|twinQueries.success|Yes|Geslaagde dubbelquery's|Aantal|Totaal|Het aantal geslaagde dubbelquery's.|Geen dimensies|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Attestation-pogingen|Aantal|Totaal|Aantal pogingen tot apparaatattest|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments|Yes|Toegewezen apparaten|Aantal|Totaal|Aantal apparaten dat is toegewezen aan een IoT-hub|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Registratiepogingen|Aantal|Totaal|Aantal pogingen tot apparaatregistratie|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|API-aanvragen|Aantal|Totaal|Het aantal API-aanvragen voor Digital Twins, schrijven, verwijderen en querybewerkingen uitvoeren.|Bewerking, Verificatie, Protocol, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|Foutpercentage API-aanvragen|Percentage|Gemiddeld|Het percentage API-aanvragen dat de service ontvangt voor uw exemplaar die een interne foutcode (500) retourneren voor Digital Twins-, schrijf-, verwijder- en querybewerkingen.|Bewerking, verificatie, protocol|
|ApiRequestsLatency|Yes|Latentie van API-aanvragen|Milliseconden|Gemiddeld|De reactietijd voor API-aanvragen, dat wil zeggen van het moment waarop de aanvraag wordt ontvangen door Azure Digital Twins totdat de service een geslaagd/mislukt resultaat verzendt voor Digital Twins lees-, schrijf-, verwijder- en querybewerkingen.|Bewerking, Verificatie, Protocol, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Yes|Facturerings-API-bewerkingen|Aantal|Totaal|Factureringsgegevens voor het aantal API-aanvragen voor de Azure Digital Twins service.|MeterId|
|BillingMessagesProcessed|Yes|Verwerkte factureringsberichten|Aantal|Totaal|Factureringsgegevens voor het aantal berichten dat vanuit de Azure Digital Twins naar externe eindpunten wordt verzonden.|MeterId|
|BillingQueryUnits|Yes|Query-eenheden voor facturering|Aantal|Totaal|Het aantal query-eenheden, een intern berekende meting van het resourcegebruik van de service, dat wordt gebruikt om query's uit te voeren.|MeterId|
|IngressEvents|Yes|Ingress-gebeurtenissen|Aantal|Totaal|Het aantal binnenkomende telemetriegebeurtenissen in Azure Digital Twins.|Resultaat|
|IngressEventsFailureRate|Yes|Foutpercentage voor ingressgebeurtenissen|Percentage|Gemiddeld|Het percentage binnenkomende telemetriegebeurtenissen waarvoor de service een interne foutcode (500) retourneert.|Geen dimensies|
|IngressEventsLatency|Yes|Latentie van ingressgebeurtenissen|Milliseconden|Gemiddeld|De tijd vanaf het moment waarop een gebeurtenis aankomt tot wanneer deze gereed is om te worden Azure Digital Twins, waarna de service een geslaagd/mislukt resultaat verzendt.|Resultaat|
|ModelCount|Yes|Aantal modellen|Aantal|Totaal|Totaal aantal modellen in de Azure Digital Twins-instantie. Gebruik deze metrische waarde om te bepalen of u de servicelimiet nadert voor het maximum aantal modellen dat per exemplaar is toegestaan.|Geen dimensies|
|Routering|Yes|Berichten gerouteerd|Aantal|Totaal|Het aantal berichten dat wordt doorgeleid naar een Azure-eindpuntservice, zoals Event Hub, Service Bus of Event Grid.|EndpointType, Resultaat|
|RoutingFailureRate|Yes|Aantal routeringsfout|Percentage|Gemiddeld|Het percentage gebeurtenissen dat resulteert in een fout omdat deze worden doorgeleid van Azure Digital Twins naar een Azure-eindpuntservice zoals Event Hub, Service Bus of Event Grid.|EndpointType|
|RoutingLatency|Yes|Routeringslatentie|Milliseconden|Gemiddeld|De tijd die is verstreken tussen een gebeurtenis die wordt gerouteerd van Azure Digital Twins naar wanneer deze wordt geplaatst in de Azure-eindpuntservice, zoals Event Hub, Service Bus of Event Grid.|EndpointType, Resultaat|
|TwinCount|Yes|Aantal tweelingen|Aantal|Totaal|Totaal aantal tweelingen in het Azure Digital Twins exemplaar. Gebruik deze metrische waarde om te bepalen of u de servicelimiet nadert voor het maximum aantal tweelingen dat per exemplaar is toegestaan.|Geen dimensies|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Regio toegevoegd|Count|Count|Regio toegevoegd|Region|
|AutoscaleMaxThroughput|No|Maximale doorvoer automatisch schalen|Count|Maximum|Maximale doorvoer automatisch schalen|DatabaseName, CollectionName|
|AvailableStorage|No|(afgeschaft) Beschikbare opslag|Bytes|Totaal|'Beschikbare opslag' wordt eind september 2023 Azure Monitor verwijderd. Cosmos DB de opslaggrootte van de verzameling is nu onbeperkt. De enige beperking is dat de opslaggrootte voor elke logische partitiesleutel 20 GB is. U kunt PartitionKeyStatistics in het diagnostische logboek inschakelen om het opslagverbruik voor de bovenste partitiesleutels te weten. Raadpleeg dit document voor Cosmos DB informatie over https://docs.microsoft.com/azure/cosmos-db/concepts-limits opslagquota. Na de afschaffing worden de resterende waarschuwingsregels die nog zijn gedefinieerd op de afgeschafte metriek automatisch uitgeschakeld na de afschaffingsdatum.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|No|Sluitingen van Cassandra-verbindingen|Aantal|Totaal|Aantal Cassandra-verbindingen dat is gesloten, gerapporteerd met een granulariteit van 1 minuut|Regio, SluitingReason|
|CassandraConnectorAvgReplicationLatency|No|Gemiddelde replicatielatentie voor Cassandra-connector|Milliseconden|Gemiddeld|Gemiddelde replicatielatentie voor Cassandra-connector|Geen dimensies|
|CassandraConnectorReplicationHealthStatus|No|Replicatiestatus van Cassandra-connector|Count|Count|Replicatiestatus van Cassandra-connector|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|No|Cassandra Keyspace gemaakt|Count|Count|Cassandra Keyspace gemaakt|Resourcename |
|CassandraKeyspaceDelete|No|Cassandra Keyspace verwijderd|Count|Count|Cassandra Keyspace verwijderd|Resourcename |
|CassandraKeyspaceThroughputUpdate|No|Doorvoer cassandra-keyspace bijgewerkt|Count|Count|Doorvoer cassandra-keyspace bijgewerkt|Resourcename |
|CassandraKeyspaceUpdate|No|Cassandra Keyspace bijgewerkt|Count|Count|Cassandra Keyspace bijgewerkt|Resourcename |
|CassandraRequestCharges|No|Cassandra-aanvraagkosten|Aantal|Totaal|Verbruikte AANVRAAG's voor Cassandra-aanvragen|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-aanvragen|Count|Count|Aantal Cassandra-aanvragen|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Cassandra-tabel gemaakt|Count|Count|Cassandra-tabel gemaakt|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tabel verwijderd|Count|Count|Cassandra-tabel verwijderd|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Doorvoer cassandra-tabel bijgewerkt|Count|Count|Doorvoer cassandra-tabel bijgewerkt|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tabel bijgewerkt|Count|Count|Cassandra-tabel bijgewerkt|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Account gemaakt|Count|Count|Account gemaakt|Geen dimensies|
|DataUsage|No|Gegevensgebruik|Bytes|Totaal|Totaal gegevensgebruik gerapporteerd met een granulariteit van 5 minuten|CollectionName, DatabaseName, Region|
|DedicatedGatewayRequests|Yes|DedicatedGatewayRequests|Count|Count|Aanvragen bij de toegewezen gateway|DatabaseName, CollectionName, CacheExercised, OperationName, Region|
|DeleteAccount|Yes|Account verwijderd|Count|Count|Account verwijderd|Geen dimensies|
|DocumentCount|No|Aantal documenten|Aantal|Totaal|Totaal aantal gerapporteerde documenten met een granulariteit van 5 minuten|CollectionName, DatabaseName, Region|
|DocumentQuota|No|Documentquotum|Bytes|Totaal|Totaal opslagquotum gerapporteerd met een granulariteit van 5 minuten|CollectionName, DatabaseName, Region|
|GremlinDatabaseCreate|No|Gremlin-database gemaakt|Count|Count|Gremlin-database gemaakt|Resourcename |
|GremlinDatabaseDelete|No|Gremlin-database verwijderd|Count|Count|Gremlin-database verwijderd|Resourcename |
|GremlinDatabaseThroughputUpdate|No|Doorvoer gremlin-database bijgewerkt|Count|Count|Doorvoer gremlin-database bijgewerkt|Resourcename |
|GremlinDatabaseUpdate|No|Gremlin-database bijgewerkt|Count|Count|Gremlin-database bijgewerkt|Resourcename |
|GremlinGraphCreate|No|Gremlin Graph gemaakt|Count|Count|Gremlin Graph gemaakt|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin Graph verwijderd|Count|Count|Gremlin Graph verwijderd|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin Graph-doorvoer bijgewerkt|Count|Count|Gremlin Graph-doorvoer bijgewerkt|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin Graph bijgewerkt|Count|Count|Gremlin Graph bijgewerkt|ResourceName, ChildResourceName, |
|IndexUsage|No|Indexgebruik|Bytes|Totaal|Totaal indexgebruik gerapporteerd met een granulariteit van 5 minuten|CollectionName, DatabaseName, Region|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Bytes|Gemiddeld|Grootte van de vermeldingen die zijn verwijderd uit de geïntegreerde cache|CacheType, Regio|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Percentage|Gemiddeld|Treffers in cache voor geïntegreerde caches|CacheType, Regio|
|IntegratedCacheSize|No|IntegratedCacheSize|Bytes|Gemiddeld|Grootte van de geïntegreerde caches voor toegewezen gatewayaanvragen|CacheType, Regio|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Count|Gemiddeld|Aantal vermeldingen verwijderd uit de geïntegreerde cache vanwege een verlopen TTL|CacheType, Regio|
|MetadataRequests|No|Aanvragen voor metagegevens|Count|Count|Aantal aanvragen voor metagegevens. Cosmos DB onderhoudt het verzamelen van systeemmetagegevens voor elk account, waarmee u gratis verzamelingen, databases, enzovoort en hun configuraties kunt opsnoemen.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionCreate|No|Mongo-verzameling gemaakt|Count|Count|Mongo-verzameling gemaakt|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-verzameling verwijderd|Count|Count|Mongo-verzameling verwijderd|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Doorvoer mongo-verzameling bijgewerkt|Count|Count|Doorvoer mongo-verzameling bijgewerkt|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-verzameling bijgewerkt|Count|Count|Mongo-verzameling bijgewerkt|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-database verwijderd|Count|Count|Mongo-database verwijderd|Resourcename |
|MongoDatabaseThroughputUpdate|No|Mongo-databasedoorvoer bijgewerkt|Count|Count|Mongo-databasedoorvoer bijgewerkt|Resourcename |
|MongoDBDatabaseCreate|No|Mongo-database gemaakt|Count|Count|Mongo-database gemaakt|Resourcename |
|MongoDBDatabaseUpdate|No|Mongo-database bijgewerkt|Count|Count|Mongo-database bijgewerkt|Resourcename |
|MongoRequestCharge|Yes|Mongo-aanvraagkosten|Aantal|Totaal|Verbruikte Mongo-aanvraageenheden|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|Yes|Mongo-aanvragen|Count|Count|Aantal Mongo-aanvragen|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|No|(afgeschaft) Mongo-aanvraagsnelheid|CountPerSecond|Gemiddeld|Aantal Mongo-aanvragen per seconde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|No|(afgeschaft) Aanvraagsnelheid mongo verwijderen|CountPerSecond|Gemiddeld|Mongo-aanvraag verwijderen per seconde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|No|(afgeschaft) Mongo-aanvraagfrequentie invoegen|CountPerSecond|Gemiddeld|Aantal Mongo-invoeging per seconde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|No|(afgeschaft) Aanvraagsnelheid van Mongo-query|CountPerSecond|Gemiddeld|Mongo-queryaanvraag per seconde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|No|(afgeschaft) Aanvraagsnelheid van Mongo-update|CountPerSecond|Gemiddeld|Mongo-updateaanvraag per seconde|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|No|Genormaliseerd RU-verbruik|Percentage|Maximum|Maximaal RU-verbruikspercentage per minuut|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|No|Ingerichte doorvoer|Count|Maximum|Ingerichte doorvoer|DatabaseName, CollectionName|
|RegioFailover|Yes|Regio mislukt|Count|Count|Regio mislukt|Geen dimensies|
|RemoveRegion|Yes|Regio verwijderd|Count|Count|Regio verwijderd|Region|
|ReplicationLatency|Yes|P99-replicatielatentie|Milliseconden|Gemiddeld|P99-replicatielatentie in bron- en doelregio's voor geo-ingeschakeld account|SourceRegion, TargetRegion|
|ServerSideLatency|No|Latentie aan serverzijde|Milliseconden|Gemiddeld|Latentie aan serverzijde|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Beschikbaarheid van de service|Percentage|Gemiddeld|Beschikbaarheid van accountaanvragen op één uur, dag of maand granulariteit|Geen dimensies|
|SqlContainerCreate|No|Sql-container gemaakt|Count|Count|Sql-container gemaakt|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|Sql-container verwijderd|Count|Count|SQL-container verwijderd|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Sql Container-doorvoer bijgewerkt|Count|Count|Sql Container-doorvoer bijgewerkt|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL-container bijgewerkt|Count|Count|SQL-container bijgewerkt|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|Sql Database gemaakt|Count|Count|Sql Database gemaakt|Resourcename |
|SqlDatabaseDelete|No|Sql Database verwijderd|Count|Count|Sql Database verwijderd|Resourcename |
|SqlDatabaseThroughputUpdate|No|Sql Database-doorvoer bijgewerkt|Count|Count|Sql Database-doorvoer bijgewerkt|Resourcename |
|SqlDatabaseUpdate|No|SQL Database bijgewerkt|Count|Count|SQL Database bijgewerkt|Resourcename |
|TableTableCreate|No|AzureTable-tabel gemaakt|Count|Count|AzureTable-tabel gemaakt|Resourcename |
|TableTableDelete|No|AzureTable-tabel verwijderd|Count|Count|AzureTable-tabel verwijderd|Resourcename |
|TableTableThroughputUpdate|No|Doorvoer AzureTable-tabel bijgewerkt|Count|Count|Doorvoer van AzureTable-tabel bijgewerkt|Resourcename |
|TableTableUpdate|No|AzureTable-tabel bijgewerkt|Count|Count|AzureTable-tabel bijgewerkt|Resourcename |
|TotalRequests|Yes|Totaal aantal aanvragen|Count|Count|Aantal aanvragen dat is ingediend|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|Yes|Totaal aantal aanvraageenheden|Aantal|Totaal|Verbruikte aanvraageenheden|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|Yes|Accountsleutels bijgewerkt|Count|Count|Accountsleutels bijgewerkt|Keytype|
|UpdateAccountNetworkSettings|Yes|Accountnetwerkinstellingen bijgewerkt|Count|Count|Accountnetwerkinstellingen bijgewerkt|Geen dimensies|
|UpdateAccountReplicationSettings|Yes|Accountreplicatie-instellingen bijgewerkt|Count|Count|Accountreplicatie-instellingen bijgewerkt|Geen dimensies|
|UpdateDiagnosticsSettings|No|Diagnostische instellingen voor account bijgewerkt|Count|Count|Diagnostische instellingen voor account bijgewerkt|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Geavanceerde filterevaluaties|Aantal|Totaal|Totaal aantal geavanceerde filters geëvalueerd voor gebeurtenisabonnementen voor dit onderwerp.|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Dead Lettered-gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen met in dead letter geschreven gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Leveringsgebeurtenissen mislukt|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet kan worden levering aan dit gebeurtenisabonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Yes|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen geleverd aan dit gebeurtenisabonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duur van doelverwerking|Milliseconden|Gemiddeld|Verwerkingsduur van doel in milliseconden|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Uitgevallen gebeurtenissen|Aantal|Totaal|Totaal aantal uitgevallen gebeurtenissen dat overeenkomt met dit gebeurtenisabonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat is afgestemd op dit gebeurtenisabonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Het totale aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Onderwerp, ErrorType, Error|
|PublishSuccessCount|Yes|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Onderwerp|
|PublishSuccessLatencyInMs|Yes|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen dimensies|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Dead Lettered-gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen met in dead letter geschreven gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DeadLetterReason|
|DeliveryAttemptFailCount|No|Leveringsgebeurtenissen mislukt|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet kan worden levering aan dit gebeurtenisabonnement|Error, ErrorType|
|DeliverySuccessCount|Yes|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen geleverd aan dit gebeurtenisabonnement|Geen dimensies|
|DestinationProcessingDurationInMs|No|Duur van doelverwerking|Milliseconden|Gemiddeld|Duur van de doelverwerking in milliseconden|Geen dimensies|
|DroppedEventCount|Yes|Uitgevallen gebeurtenissen|Aantal|Totaal|Totaal aantal uitgevallen gebeurtenissen dat overeenkomt met dit gebeurtenisabonnement|DropReason|
|MatchedEventCount|Yes|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat is afgestemd op dit gebeurtenisabonnement|Geen dimensies|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Het totale aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|ErrorType, Error|
|PublishSuccessCount|Yes|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishSuccessLatencyInMs|Yes|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen dimensies|
|Niet-overeenkomendeEventCount|Yes|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenisabonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Dead Lettered-gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen met in dead letter geschreven gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Leveringsgebeurtenissen mislukt|Aantal|Totaal|Totaal aantal gebeurtenissen kan niet worden levering aan dit gebeurtenisabonnement|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen geleverd aan dit gebeurtenisabonnement|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duur van doelverwerking|Milliseconden|Gemiddeld|Duur van de doelverwerking in milliseconden|EventSubscriptionName|
|DroppedEventCount|Yes|Uitgevallen gebeurtenissen|Aantal|Totaal|Totaal aantal uitgevallen gebeurtenissen dat overeenkomt met dit gebeurtenisabonnement|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat is afgestemd op dit gebeurtenisabonnement|EventSubscriptionName|
|PublishFailCount|Yes|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Het totale aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|ErrorType, Error|
|PublishSuccessCount|Yes|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishSuccessLatencyInMs|Yes|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen dimensies|
|Niet-overeenkomendeEventCount|Yes|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenisabonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Geavanceerde filterevaluaties|Aantal|Totaal|Totaal aantal geavanceerde filters geëvalueerd voor gebeurtenisabonnementen voor dit onderwerp.|EventSubscriptionName|
|DeadLetteredCount|Yes|Dead Lettered-gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen met in dead letter geschreven gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Leveringsgebeurtenissen mislukt|Aantal|Totaal|Totaal aantal gebeurtenissen kan niet worden levering aan dit gebeurtenisabonnement|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen geleverd aan dit gebeurtenisabonnement|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Verwerkingsduur doel|Milliseconden|Gemiddeld|Verwerkingsduur van doel in milliseconden|EventSubscriptionName|
|DroppedEventCount|Yes|Uitgevallen gebeurtenissen|Aantal|Totaal|Totaal aantal uitgevallen gebeurtenissen dat overeenkomt met dit gebeurtenisabonnement|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat is afgestemd op dit gebeurtenisabonnement|EventSubscriptionName|
|PublishFailCount|Yes|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Het totale aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|ErrorType, Error|
|PublishSuccessCount|Yes|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|Niet-overeenkomendeEventCount|Yes|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenisabonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Geavanceerde filterevaluaties|Aantal|Totaal|Totaal aantal geavanceerde filters geëvalueerd voor gebeurtenisabonnementen voor dit onderwerp.|EventSubscriptionName|
|DeadLetteredCount|Yes|Dead Lettered-gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen met in dead letter geschreven gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Leveringsgebeurtenissen mislukt|Aantal|Totaal|Totaal aantal gebeurtenissen kan dit gebeurtenisabonnement niet leveren|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen geleverd aan dit gebeurtenisabonnement|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Verwerkingsduur doel|Milliseconden|Gemiddeld|Verwerkingsduur van doel in milliseconden|EventSubscriptionName|
|DroppedEventCount|Yes|Uitgevallen gebeurtenissen|Aantal|Totaal|Totaal aantal uitgevallen gebeurtenissen dat overeenkomt met dit gebeurtenisabonnement|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat is afgestemd op dit gebeurtenisabonnement|EventSubscriptionName|
|PublishFailCount|Yes|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Het totale aantal gebeurtenissen dat niet kan worden gepubliceerd naar dit onderwerp|ErrorType, Error|
|PublishSuccessCount|Yes|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen gepubliceerd naar dit onderwerp|Geen dimensies|
|PublishSuccessLatencyInMs|Yes|Latentie bij publiceren van succes|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen dimensies|
|Niet-overeenkomendeEventCount|Yes|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenisabonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Geavanceerde filterevaluaties|Aantal|Totaal|Totaal aantal geavanceerde filters geëvalueerd voor gebeurtenisabonnementen voor dit onderwerp.|EventSubscriptionName|
|DeadLetteredCount|Yes|Dead Lettered-gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen met in dead letter geschreven gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Leveringsgebeurtenissen mislukt|Aantal|Totaal|Totaal aantal gebeurtenissen kan dit gebeurtenisabonnement niet leveren|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen geleverd aan dit gebeurtenisabonnement|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Verwerkingsduur doel|Milliseconden|Gemiddeld|Duur van de doelverwerking in milliseconden|EventSubscriptionName|
|DroppedEventCount|Yes|Uitgevallen gebeurtenissen|Aantal|Totaal|Totaal aantal uitgevallen gebeurtenissen dat overeenkomt met dit gebeurtenisabonnement|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat is afgestemd op dit gebeurtenisabonnement|EventSubscriptionName|
|PublishFailCount|Yes|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Het totale aantal gebeurtenissen dat niet kan worden gepubliceerd naar dit onderwerp|ErrorType, Error|
|PublishSuccessCount|Yes|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen gepubliceerd naar dit onderwerp|Geen dimensies|
|PublishSuccessLatencyInMs|Yes|Latentie bij publiceren van succes|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen dimensies|
|Niet-overeenkomendeEventCount|Yes|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenisabonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Count|Gemiddeld|Totaal aantal actieve verbindingen voor Microsoft.EventHub.||
|AvailableMemory|No|Beschikbaar geheugen|Percentage|Maximum|Beschikbaar geheugen voor het Event Hub-cluster als een percentage van het totale geheugen.|Rol|
|CaptureBacklog|No|Backlog vastleggen.|Aantal|Totaal|Backlog vastleggen voor Microsoft.EventHub.||
|Vastgelegdebytes|No|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor Microsoft.EventHub.||
|CapturedMessages|No|Vastgelegde berichten.|Aantal|Totaal|Vastgelegde berichten voor Microsoft.EventHub.||
|VerbindingenSluiten|No|Gesloten verbindingen.|Count|Gemiddeld|Verbindingen gesloten voor Microsoft.EventHub.||
|VerbindingenOpend|No|Geopende verbindingen.|Count|Gemiddeld|Verbindingen geopend voor Microsoft.EventHub.||
|CPU|No|CPU|Percentage|Maximum|CPU-gebruik voor het Event Hub-cluster als percentage|Rol|
|Inkomendebytes|Yes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor Microsoft.EventHub.||
|IncomingMessages|Yes|Binnenkomende berichten|Aantal|Totaal|Binnenkomende berichten voor Microsoft.EventHub.||
|IncomingRequests|Yes|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor Microsoft.EventHub.||
|Uitgaandebytes|Yes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor Microsoft.EventHub.||
|OutgoingMessages|Yes|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor Microsoft.EventHub.||
|QuotaExceededErrors|No|Fouten met overschreden quota.|Aantal|Totaal|Quotumoverschrijdingsfouten voor Microsoft.EventHub.|OperationResult|
|ServerErrors|No|Serverfouten.|Aantal|Totaal|Serverfouten voor Microsoft.EventHub.|OperationResult|
|Grootte|No|Grootte|Bytes|Gemiddeld|Grootte van een EventHub in bytes.|Rol|
|SuccessfulRequests|No|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen voor Microsoft.EventHub.|OperationResult|
|ThrottledRequests|No|Beperkte aanvragen.|Aantal|Totaal|Beperkt aantal aanvragen voor Microsoft.EventHub.|OperationResult|
|UserErrors|No|Gebruikersfouten.|Aantal|Totaal|Gebruikersfouten voor Microsoft.EventHub.|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Count|Gemiddeld|Totaal aantal actieve verbindingen voor Microsoft.EventHub.||
|CaptureBacklog|No|Backlog vastleggen.|Aantal|Totaal|Backlog vastleggen voor Microsoft.EventHub.|EntityName|
|Vastgelegdebytes|No|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor Microsoft.EventHub.|EntityName|
|CapturedMessages|No|Vastgelegde berichten.|Aantal|Totaal|Vastgelegde berichten voor Microsoft.EventHub.|EntityName|
|VerbindingenSluiten|No|Gesloten verbindingen.|Count|Gemiddeld|Verbindingen gesloten voor Microsoft.EventHub.|EntityName|
|VerbindingenOpend|No|Geopende verbindingen.|Count|Gemiddeld|Verbindingen geopend voor Microsoft.EventHub.|EntityName|
|EHABL|Yes|Backlogberichten archiveren (afgeschaft)|Aantal|Totaal|Event Hub-archiefberichten in achterstand voor een naamruimte (afgeschaft)||
|EHAMBS|Yes|Archiefberichtdoorvoer (afgeschaft)|Bytes|Totaal|Gearchiveerde berichtdoorvoer van Event Hub in een naamruimte (afgeschaft)||
|EHAMSGS|Yes|Berichten archiveren (afgeschaft)|Aantal|Totaal|Gearchiveerde Event Hub-berichten in een naamruimte (afgeschaft)||
|EHINBYTES|Yes|Binnenkomende bytes (afgeschaft)|Bytes|Totaal|Binnenkomende berichtdoorvoer van Event Hub voor een naamruimte (afgeschaft)||
|EHINMBS|Yes|Binnenkomende bytes (verouderd) (afgeschaft)|Bytes|Totaal|Binnenkomende berichtdoorvoer van Event Hub voor een naamruimte. Deze metrische gegevens zijn afgeschaft. Gebruik in plaats daarvan metrische gegevens voor binnenkomende bytes (afgeschaft)||
|EHINMSGS|Yes|Binnenkomende berichten (afgeschaft)|Aantal|Totaal|Totaal aantal binnenkomende berichten voor een naamruimte (afgeschaft)||
|EHOUTBYTES|Yes|Uitgaande bytes (afgeschaft)|Bytes|Totaal|Uitgaande berichtdoorvoer van Event Hub voor een naamruimte (afgeschaft)||
|EHOUTMBS|Yes|Uitgaande bytes (verouderd) (afgeschaft)|Bytes|Totaal|Uitgaande berichtdoorvoer van Event Hub voor een naamruimte. Deze metrische gegevens zijn afgeschaft. Gebruik in plaats daarvan metrische gegevens voor uitgaande bytes (afgeschaft)||
|EHOUTMSGS|Yes|Uitgaande berichten (afgeschaft)|Aantal|Totaal|Totaal aantal uitgaande berichten voor een naamruimte (afgeschaft)||
|FAILREQ|Yes|Mislukte aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal mislukte aanvragen voor een naamruimte (afgeschaft)||
|Inkomendebytes|Yes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor Microsoft.EventHub.|EntityName|
|IncomingMessages|Yes|Binnenkomende berichten|Aantal|Totaal|Binnenkomende berichten voor Microsoft.EventHub.|EntityName|
|IncomingRequests|Yes|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor Microsoft.EventHub.|EntityName|
|INMSGS|Yes|Binnenkomende berichten (verouderd) (afgeschaft)|Aantal|Totaal|Totaal aantal binnenkomende berichten voor een naamruimte. Deze metrische gegevens zijn afgeschaft. Gebruik in plaats daarvan metrische gegevens voor binnenkomende berichten (afgeschaft)||
|INREQS|Yes|Binnenkomende aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal binnenkomende verzendaanvragen voor een naamruimte (afgeschaft)||
|INTERR|Yes|Interne serverfouten (afgeschaft)|Aantal|Totaal|Totaal aantal interne serverfouten voor een naamruimte (afgeschaft)||
|MISCERR|Yes|Andere fouten (afgeschaft)|Aantal|Totaal|Totaal aantal mislukte aanvragen voor een naamruimte (afgeschaft)||
|Uitgaandebytes|Yes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor Microsoft.EventHub.|EntityName|
|OutgoingMessages|Yes|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor Microsoft.EventHub.|EntityName|
|OUTMSGS|Yes|Uitgaande berichten (verouderd) (afgeschaft)|Aantal|Totaal|Totaal aantal uitgaande berichten voor een naamruimte. Deze metrische gegevens zijn afgeschaft. Gebruik in plaats daarvan metrische gegevens voor uitgaande berichten (afgeschaft)||
|QuotaExceededErrors|No|Fouten met overschreden quota.|Aantal|Totaal|Quotumoverschrijdingsfouten voor Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|No|Serverfouten.|Aantal|Totaal|Serverfouten voor Microsoft.EventHub.|EntityName, OperationResult|
|Grootte|No|Grootte|Bytes|Gemiddeld|Grootte van een EventHub in bytes.|EntityName|
|SuccessfulRequests|No|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen voor Microsoft.EventHub.|EntityName, OperationResult|
|SUCCREQ|Yes|Geslaagde aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal geslaagde aanvragen voor een naamruimte (afgeschaft)||
|SVRBSY|Yes|Server bezet-fouten (afgeschaft)|Aantal|Totaal|Totaal aantal server bezet-fouten voor een naamruimte (afgeschaft)||
|ThrottledRequests|No|Beperkte aanvragen.|Aantal|Totaal|Beperkt aantal aanvragen voor Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|No|Gebruikersfouten.|Aantal|Totaal|Gebruikersfouten voor Microsoft.EventHub.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Gecategoriseerde gatewayaanvragen|Aantal|Totaal|Aantal gatewayaanvragen per categorie (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Gatewayaanvragen|Aantal|Totaal|Aantal gatewayaanvragen|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST proxy Consumer RequestThroughput|CountPerSecond|Totaal|Aantal consumentenaanvragen voor Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Yes|Niet-geslaagde aanvragen van REST-proxy consumer|CountPerSecond|Totaal|Uitzonderingen voor consumentenaanvraag|Machine, onderwerp|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|REST-proxy Consumer RequestLatency|Milliseconden|Gemiddeld|Berichtlatentie in een consumentenaanvraag via Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|Yes|REST proxy Consumer Request Backlog|Milliseconden|Gemiddeld|Wachtrijlengte voor consumer-REST-proxy|Machine, onderwerp|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST proxy Producer MessageThroughput|CountPerSecond|Totaal|Aantal producentberichten via Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|REST proxy Consumer MessageThroughput|CountPerSecond|Totaal|Aantal consumentenberichten via Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.OpenConnections|Yes|REST proxy ConcurrentConnections|Aantal|Totaal|Aantal gelijktijdige verbindingen via Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST proxy Producer RequestThroughput|CountPerSecond|Totaal|Aantal producentaanvragen voor Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Yes|Mislukte aanvragen rest proxy producer|CountPerSecond|Totaal|Uitzonderingen voor producer-aanvragen|Machine, onderwerp|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|REST-proxy producer RequestLatency|Milliseconden|Gemiddeld|Berichtlatentie in een producentaanvraag via Kafka REST-proxy|Machine, onderwerp|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Yes|Producentaanvraagachterstand REST-proxy|Milliseconden|Gemiddeld|Lengte van de producer-REST-proxywachtrij|Machine, onderwerp|
|NumActiveWorkers|Yes|Aantal actieve werknemers|Count|Maximum|Aantal actieve werknemers|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|De beschikbaarheidsfrequentie van de service.|Geen dimensies|
|CosmosDbCollectionSize|Yes|Cosmos DB verzamelingsgrootte|Bytes|Totaal|De grootte van de back-Cosmos DB verzameling, in bytes.|Geen dimensies|
|CosmosDbIndexSize|Yes|Cosmos DB indexgrootte|Bytes|Totaal|De grootte van de back-Cosmos DB index van de verzameling, in bytes.|Geen dimensies|
|CosmosDbRequestCharge|Yes|Cosmos DB RU-gebruik controleren|Aantal|Totaal|Het RU-gebruik van aanvragen voor de back-Cosmos DB.|Bewerking, ResourceType|
|CosmosDbRequests|Yes|Service Cosmos DB aanvragen|Count|Sum|Het totale aantal aanvragen dat wordt ingediend bij de Cosmos DB.|Bewerking, ResourceType|
|CosmosDbThrottleRate|Yes|Service Cosmos DB vertragingssnelheid|Count|Sum|Het totale aantal 429 reacties van de Cosmos DB.|Bewerking, ResourceType|
|IoTConnectorDeviceEvent|Yes|Aantal binnenkomende berichten|Count|Sum|Het totale aantal berichten dat vóór normalisatie is ontvangen door de Azure IoT Connector for FHIR.|Bewerking, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Gemiddelde latentie fase normaliseren|Milliseconden|Gemiddeld|De gemiddelde tijd tussen de opnametijd van een gebeurtenis en de tijd dat de gebeurtenis wordt verwerkt voor normalisatie.|Bewerking, ConnectorName|
|IoTConnectorMeasurement|Yes|Aantal metingen|Count|Sum|Het aantal genormaliseerde waardemetingen dat is ontvangen door de FHIR-conversiefase van de Azure IoT Connector for FHIR.|Bewerking, ConnectorName|
|IoTConnectorMeasurementGroup|Yes|Aantal berichtgroepen|Count|Sum|Het totale aantal unieke groeperingen van metingen voor type, apparaat, patiënt en geconfigureerde tijdsperiode die is gegenereerd door de FHIR-conversiefase.|Bewerking, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Gemiddelde latentie in groepsfase|Milliseconden|Gemiddeld|De periode tussen het moment waarop de IoT Connector apparaatgegevens heeft ontvangen en wanneer de gegevens worden verwerkt door de FHIR-conversiefase.|Bewerking, ConnectorName|
|IoTConnectorNormalizedEvent|Yes|Aantal genormaliseerde berichten|Count|Sum|Het totale aantal in kaart gebrachte genormaliseerde waarden dat is uitgevoerd vanuit de normalisatiefase van de Azure IoT Connector for FHIR.|Bewerking, ConnectorName|
|IoTConnectorTotalErrors|Yes|Totaal aantal fouten|Count|Sum|Het totale aantal fouten dat is vastgelegd door de Azure IoT Connector for FHIR|Naam, Bewerking, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors|Yes|Servicefouten|Count|Sum|Het totale aantal interne serverfouten dat door de service is gegenereerd.|Protocol, Authentication, Operation, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Yes|Servicelatentie|Milliseconden|Gemiddeld|De reactielatentie van de service.|Protocol, Authentication, Operation, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|Count|Sum|Het totale aantal aanvragen dat door de service is ontvangen.|Protocol, Verificatie, Bewerking, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Totaalaantal fouten|Count|Sum|Het totale aantal interne serverfouten dat door de service is aangetroffen.|Protocol, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Totale latentie|Milliseconden|Gemiddeld|De reactielatentie van de service.|Protocol|
|TotalRequests|Yes|Totaal aantal aanvragen|Count|Sum|Het totale aantal aanvragen dat door de service is ontvangen.|Protocol|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Gemiddeld CPU-gebruik|Percentage|Gemiddeld|Totaal gemiddeld percentage van het virtuele CPU-gebruik met een interval van één minuut. Het totale aantal virtuele CPU is gebaseerd op de door de gebruiker geconfigureerde waarde in de SKU-definitie. Verder filter kan worden toegepast op basis van RoleName gedefinieerd in SKU.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Gemiddeld CPU-gebruik|Percentage|Gemiddeld|Totaal gemiddeld percentage van virtueel CPU-gebruik met een interval van één minuut. Het totale aantal virtuele CPU is gebaseerd op de door de gebruiker geconfigureerde waarde in de SKU-definitie. Verder filter kan worden toegepast op basis van RoleName gedefinieerd in SKU.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Drempelwaarde voor metrische gegevens|Count|Gemiddeld|De geconfigureerde drempelwaarde voor automatisch schalen wanneer automatisch schalen is ingesteld.|MetricTriggerRule|
|Waargenomen capaciteit|Yes|Waargenomen capaciteit|Count|Gemiddeld|De capaciteit die is gerapporteerd aan automatische schaalschalen wanneer deze wordt uitgevoerd.||
|ObservedMetricValue|Yes|Waargenomen metrische waarde|Count|Gemiddeld|De waarde die wordt berekend door automatisch schalen wanneer deze wordt uitgevoerd|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Gestarte schaalacties|Aantal|Totaal|De richting van de schaalbewerking.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Beschikbaarheid|Percentage|Gemiddeld|Percentage voltooide beschikbaarheidstests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|No|Beschikbaarheidstests|Count|Count|Aantal beschikbaarheidstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Yes|Duur beschikbaarheidstest|Milliseconden|Gemiddeld|Duur beschikbaarheidstest|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Yes|Verbindingstijd van het netwerk voor het laden van pagina's|Milliseconden|Gemiddeld|Tijd tussen de aanvraag van de gebruiker en de netwerkverbinding. Bevat DNS-zoek- en transportverbinding.|Geen dimensies|
|browserTimings/processingDuration|Yes|Verwerkingstijd van client|Milliseconden|Gemiddeld|Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM wordt geladen. Asynsynsyntische aanvragen worden mogelijk nog verwerkt.|Geen dimensies|
|browserTimings/receiveDuration|Yes|Reactietijd ontvangen|Milliseconden|Gemiddeld|Tijd tussen de eerste en laatste bytes, of tot de verbinding wordt verbroken.|Geen dimensies|
|browserTimings/sendDuration|Yes|Aanvraagtijd verzenden|Milliseconden|Gemiddeld|Tijd tussen de netwerkverbinding en het ontvangen van de eerste byte.|Geen dimensies|
|browserTimings/totalDuration|Yes|Laadtijd browserpagina|Milliseconden|Gemiddeld|Tijd van gebruikersaanvraag tot DOM, stylesheets, scripts en afbeeldingen worden geladen.|Geen dimensies|
|afhankelijkheden/aantal|No|Afhankelijkheidsoproepen|Count|Count|Aantal aanroepen van de toepassing naar externe resources.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|afhankelijkheden/duur|Yes|Afhankelijkheidsduur|Milliseconden|Gemiddeld|Duur van aanroepen van de toepassing naar externe resources.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|afhankelijkheden/mislukt|No|Fouten bij afhankelijkheidsoproepen|Count|Count|Aantal mislukte afhankelijkheidsoproepen van de toepassing naar externe resources.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|uitzonderingen/browser|No|Browseruitzonderingen|Count|Count|Aantal niet-geseede uitzonderingen in de browser.|cloud/roleName|
|uitzonderingen/aantal|Yes|Uitzonderingen|Count|Count|Gecombineerd aantal van alle niet-beladen uitzonderingen.|cloud/roleName, cloud/roleInstance, client/type|
|uitzonderingen/server|No|Server-uitzonderingen|Count|Count|Aantal niet-beladen uitzonderingen dat is opgeslagen in de servertoepassing.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Yes|Paginaweergaven|Count|Count|Aantal paginaweergaven.|operation/synthetische, cloud/roleName|
|pageViews/duration|Yes|Laadtijd paginaweergave|Milliseconden|Gemiddeld|Laadtijd paginaweergave|operation/synthetische, cloud/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Uitzonderingspercentage|CountPerSecond|Gemiddeld|Aantal verwerkte en niet-verwerkte uitzonderingen dat wordt gerapporteerd aan windows, inclusief .NET-uitzonderingen en niet-mande uitzonderingen die worden geconverteerd naar .NET-uitzonderingen.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Beschikbaar geheugen|Bytes|Gemiddeld|Fysiek geheugen onmiddellijk beschikbaar voor toewijzing aan een proces of voor systeemgebruik.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Yes|CPU verwerken|Percentage|Gemiddeld|Het percentage verstreken tijd dat alle procesthreads de processor gebruikten om instructies uit te voeren. Dit kan variëren tussen 0 en 100. Deze metrische gegevens geven alleen de prestaties van het w3wp-proces aan.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|I/O-snelheid verwerken|BytesPerSecond|Gemiddeld|Totaal aantal bytes per seconde gelezen en geschreven naar bestanden, netwerk en apparaten.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processortijd|Percentage|Gemiddeld|Het percentage tijd dat de processor besteedt aan niet-inactieve threads.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Persoonlijke bytes verwerken|Bytes|Gemiddeld|Geheugen wordt exclusief toegewezen aan de processen van de bewaakte toepassing.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Yes|Uitvoeringstijd http-aanvraag|Milliseconden|Gemiddeld|Uitvoeringstijd van de meest recente aanvraag.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-aanvragen in de toepassingswachtrij|Count|Gemiddeld|Lengte van de aanvraagwachtrij van de toepassing.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Yes|HTTP-aanvraagsnelheid|CountPerSecond|Gemiddeld|Snelheid van alle aanvragen voor de toepassing per seconde van ASP.NET.|cloud/roleInstance|
|aanvragen/aantal|No|Serveraanvragen|Count|Count|Aantal voltooide HTTP-aanvragen.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|aanvragen/duur|Yes|Reactietijd van server|Milliseconden|Gemiddeld|Tijd tussen het ontvangen van een HTTP-aanvraag en het afronden van het verzenden van het antwoord.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|aanvragen/mislukt|No|Mislukte aanvragen|Count|Count|Aantal HTTP-aanvragen dat is gemarkeerd als mislukt. In de meeste gevallen zijn dit aanvragen met een antwoordcode >= 400 en niet gelijk aan 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|aanvragen/snelheid|No|Serveraanvraagsnelheid|CountPerSecond|Gemiddeld|Aantal serveraanvragen per seconde|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Yes|Traceringen|Count|Count|Aantal traceerdocuments|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|c2d.commands.failure|Yes|Mislukte aanroepen van opdrachten|Aantal|Totaal|Het aantal mislukte opdrachtaanvragen dat is geïnitieerd vanuit IoT Central|Geen dimensies|
|c2d.commands.requestSize|Yes|Aanvraaggrootte van opdrachtvocations|Bytes|Totaal|Aanvraaggrootte van alle opdrachtaanvragen die zijn geïnitieerd vanuit IoT Central|Geen dimensies|
|c2d.commands.responseSize|Yes|Antwoordgrootte van opdrachtvocations|Bytes|Totaal|Antwoordgrootte van alle opdrachtreacties die zijn geïnitieerd vanuit IoT Central|Geen dimensies|
|c2d.commands.success|Yes|Geslaagde aanroepen van opdrachten|Aantal|Totaal|Het aantal geslaagde opdrachtaanvragen dat is gestart vanuit IoT Central|Geen dimensies|
|c2d.property.read.failure|Yes|Mislukte apparaat eigenschap leest uit IoT Central|Aantal|Totaal|Het aantal mislukte eigenschapslezen dat is geïnitieerd vanuit IoT Central|Geen dimensies|
|c2d.property.read.success|Yes|Geslaagde apparaat eigenschap leest uit IoT Central|Aantal|Totaal|Het aantal geslaagde eigenschapslezen dat is gestart vanuit IoT Central|Geen dimensies|
|c2d.property.update.failure|Yes|Updates van apparaat eigenschap mislukt van IoT Central|Aantal|Totaal|Het aantal mislukte updates van eigenschappen dat is gestart vanuit IoT Central|Geen dimensies|
|c2d.property.update.success|Yes|Geslaagde updates van apparaat-eigenschappen vanuit IoT Central|Aantal|Totaal|Het aantal geslaagde updates van eigenschappen dat is gestart vanuit IoT Central|Geen dimensies|
|connectedDeviceCount|No|Totaal aantal verbonden apparaten|Count|Gemiddeld|Aantal apparaten dat is verbonden met IoT Central|Geen dimensies|
|d2c.property.read.failure|Yes|Mislukte apparaat-eigenschap leest van apparaten|Aantal|Totaal|Het aantal mislukte eigenschappen dat wordt gelezen, geïnitieerd vanaf apparaten|Geen dimensies|
|d2c.property.read.success|Yes|Geslaagde apparaat eigenschap leest van apparaten|Aantal|Totaal|Het aantal geslaagde eigenschapslezen geïnitieerd vanaf apparaten|Geen dimensies|
|d2c.property.update.failure|Yes|Updates van apparaat eigenschap mislukt vanaf apparaten|Aantal|Totaal|Het aantal mislukte updates van eigenschappen die vanaf apparaten zijn gestart|Geen dimensies|
|d2c.property.update.success|Yes|Geslaagde updates van apparaat-eigenschappen vanaf apparaten|Aantal|Totaal|Het aantal geslaagde eigenschapsupdates dat vanaf apparaten is gestart|Geen dimensies|
|d2c.telemetry.ingress.allProtocol|Yes|Totaal aantal verzendpogingen telemetriebericht|Aantal|Totaal|Aantal apparaat-naar-cloud-telemetrieberichten dat is verzonden naar de IoT Central toepassing|Geen dimensies|
|d2c.telemetry.ingress.success|Yes|Totaal verzonden telemetrieberichten|Aantal|Totaal|Aantal apparaat-naar-cloud-telemetrieberichten verzonden naar de IoT Central toepassing|Geen dimensies|
|dataExport.error|Yes|Fouten bij het exporteren van gegevens|Aantal|Totaal|Aantal fouten dat is aangetroffen bij het exporteren van gegevens|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Yes|Gefilterde berichten voor gegevensexport|Aantal|Totaal|Aantal berichten dat is doorgegeven via filters in gegevensexport|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.received|Yes|Ontvangen gegevensexportberichten|Aantal|Totaal|Aantal berichten dat binnenkomende is in gegevensexport, voordat er wordt gefilterd en verrijkt|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|Yes|Geschreven gegevensexportberichten|Aantal|Totaal|Aantal berichten dat naar een bestemming wordt geschreven|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.statusChange|Yes|Statuswijziging gegevensexport|Aantal|Totaal|Aantal statuswijzigingen|exportId, exportDisplayName, destinationId, destinationDisplayName, status|
|deviceDataUsage|Yes|Totaal gebruik van apparaatgegevens|Bytes|Totaal|Bytes die worden overgebracht naar en van apparaten die zijn verbonden met IoT Central toepassing|Geen dimensies|
|provisionedDeviceCount|No|Totaal aantal inrichtende apparaten|Count|Gemiddeld|Aantal apparaten dat is ingericht in IoT Central toepassing|Geen dimensies|

## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|No|Algemene beschikbaarheid van service|Percentage|Gemiddeld|Beschikbaarheid van serviceaanvragen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Totaal aantal treffers service-API|Count|Count|Het totale aantal treffers van de service-API|ActivityType, ActivityName|
|ServiceApiLatency|No|Algehele latentie van service-API|Milliseconden|Gemiddeld|Algemene latentie van service-API-aanvragen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totaal aantal service-API-resultaten|Count|Count|Aantal totale service-API-resultaten|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Algemene beschikbaarheid van de kluis|Percentage|Gemiddeld|Beschikbaarheid van kluisaanvragen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Algehele verzadiging van kluis|Percentage|Gemiddeld|Gebruikte kluiscapaciteit|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Totaal aantal service-API-treffers|Count|Count|Het totale aantal treffers van service-API|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Algehele latentie van service-API|Milliseconden|Gemiddeld|Algemene latentie van service-API-aanvragen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totaal aantal service-API-resultaten|Count|Count|Aantal totale service-API-resultaten|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Totaal aantal CPU-kernen in een verbonden cluster|Aantal|Totaal|Totaal aantal CPU-kernen in een verbonden cluster||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Aantal batch-blobs|Count|Gemiddeld|Het aantal gegevensbronnen in een geaggregeerde batch voor opname.|Database|
|BatchDuration|Yes|Batchduur|Seconden|Gemiddeld|De duur van de aggregatiefase in de opnamestroom.|Database|
|BatchesProcessed|Yes|Verwerkte batches|Aantal|Totaal|Het aantal batches dat is geaggregeerd voor opname. Batchtype: of de batch de batchtijd, de gegevensgrootte of het aantal bestanden heeft bereikt dat is ingesteld door batchingbeleid|Database, SealReason|
|BatchSize|Yes|Batchgrootte|Bytes|Gemiddeld|Niet-gecomprimeerde verwachte gegevensgrootte in een geaggregeerde batch voor opname.|Database|
|BlobsDropped|Yes|Blobs uitgevallen|Aantal|Totaal|Het aantal blobs dat permanent wordt afgewezen door een onderdeel.|Database, ComponentType, ComponentName|
|BlobsProcessed|Yes|Verwerkte blobs|Aantal|Totaal|Het aantal blobs dat door een onderdeel wordt verwerkt.|Database, ComponentType, ComponentName|
|BlobsReceived|Yes|Ontvangen blobs|Aantal|Totaal|Het aantal blobs dat door een onderdeel van de invoerstroom wordt ontvangen.|Database, ComponentType, ComponentName|
|Cache-utilization|Yes|Cachegebruik|Percentage|Gemiddeld|Gebruiksniveau in het clusterbereik|Geen dimensies|
|ContinuousExportMaxLatenessMinutes|Yes|Max. latente continue export|Count|Maximum|De vertraging (in minuten) die wordt gerapporteerd door de continue exporttaken in het cluster|Geen dimensies|
|ContinuousExportNumOfRecordsExported|Yes|Continue export – aantal geëxporteerde records|Aantal|Totaal|Aantal geëxporteerde records, ten uitvoer gebracht voor elk opslagartefact dat is geschreven tijdens de exportbewerking|ContinuousExportName, Database|
|ContinuousExportPendingCount|Yes|Aantal continue export in behandeling|Count|Maximum|Het aantal in behandeling zijnde continue exporttaken dat gereed is voor uitvoering|Geen dimensies|
|ContinuousExportResult|Yes|Resultaat van continue export|Count|Count|Geeft aan of continue export is geslaagd of mislukt|ContinuousExportName, Result, Database|
|CPU|Yes|CPU|Percentage|Gemiddeld|Cpu-gebruiksniveau|Geen dimensies|
|DiscoveryLatency|Yes|Detectielatentie|Seconden|Gemiddeld|Gerapporteerd door gegevensverbindingen (indien aanwezig). Tijd in seconden vanaf het moment waarop een bericht in de enqueu wordt opgevraagd of de gebeurtenis wordt gemaakt totdat het wordt ontdekt door de gegevensverbinding. Deze tijd is niet opgenomen in de Azure Data Explorer totale opnameduur.|ComponentType, ComponentName|
|EventsDropped|Yes|Gebeurtenissen die zijn uitgevallen|Aantal|Totaal|Het aantal gebeurtenissen dat permanent is uitgevallen door de gegevensverbinding. Er wordt een metrische gegevens over opnameresultaat met een reden voor de fout verzonden.|ComponentType, ComponentName|
|EventsProcessed|Yes|Verwerkte gebeurtenissen|Aantal|Totaal|Aantal gebeurtenissen dat door het cluster is verwerkt|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Yes|Verwerkte gebeurtenissen (voor Event/IoT Hubs)|Aantal|Totaal|Het aantal gebeurtenissen dat door het cluster wordt verwerkt bij het opnemen van gebeurtenissen/IoT Hub|EventStatus|
|EventsReceived|Yes|Ontvangen gebeurtenissen|Aantal|Totaal|Het aantal gebeurtenissen dat door de gegevensverbinding wordt ontvangen.|ComponentType, ComponentName|
|ExportUtilization|Yes|Exportgebruik|Percentage|Maximum|Exportgebruik|Geen dimensies|
|IngestionLatencyInSeconds|Yes|Opnamelatentie|Seconden|Gemiddeld|Latentie van opgenomen gegevens, vanaf het moment dat de gegevens in het cluster zijn ontvangen, totdat er query's op kunnen worden uitgevoerd. De opnamelatentieperiode is afhankelijk van het opnamescenario.|Geen dimensies|
|IngestionResult|Yes|Opnameresultaat|Aantal|Totaal|Aantal opnamebewerkingen|IngestionResultDetails|
|Opname-utilisatie|Yes|Opnamegebruik|Percentage|Gemiddeld|Verhouding van gebruikte opnamesleuven in het cluster|Geen dimensies|
|OpnameVolumeInMB|Yes|Opnamevolume|Bytes|Totaal|Totale volume van opgenomen gegevens in het cluster|Database|
|InstanceCount|Yes|Aantal instanties|Count|Gemiddeld|Totaal aantal exemplaren|Geen dimensies|
|Keepalive|Yes|Keep alive|Count|Gemiddeld|Statuscontrole geeft aan dat het cluster reageert op query's|Geen dimensies|
|MaterializedViewAgeMinutes|Yes|Materialized View Age|Count|Gemiddeld|De materialized weergaveleeftijd in minuten|Database, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Materialized View Data Loss|Count|Maximum|Geeft mogelijk gegevensverlies aan in de ge materialiseerde weergave|Database, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Yes|Materialized View Extents Rebuild|Count|Gemiddeld|Aantal herbouwen van omvang|Database, MaterializedViewName|
|MaterializedViewHealth|Yes|Status van ge materialiseerde weergave|Count|Gemiddeld|De status van de ge materialiseerde weergave (1 voor in orde, 0 voor niet-in orde)|Database, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|Materialized View Records In Delta|Count|Gemiddeld|Het aantal records in het niet-materialiseerde deel van de weergave|Database, MaterializedViewName|
|MaterializedViewResult|Yes|Resultaat van materialized weergave|Count|Gemiddeld|Het resultaat van het materialisatieproces|Database, MaterializedViewName, Resultaat|
|QueryDuration|Yes|Queryduur|Milliseconden|Gemiddeld|Duur van query's in seconden|QueryStatus|
|QueryResult|No|Queryresultaat|Count|Count|Totaal aantal query's.|QueryStatus|
|QueueLength|Yes|Wachtrijlengte|Count|Gemiddeld|Aantal berichten in behandeling in de wachtrij van een onderdeel.|ComponentType|
|QueueOldestMessage|Yes|Oudste bericht van wachtrij|Count|Gemiddeld|Tijd in seconden vanaf het moment waarop het oudste bericht in de wachtrij is ingevoegd.|ComponentType|
|ReceivedDataSizeBytes|Yes|Ontvangen gegevensgrootte bytes|Bytes|Gemiddeld|De grootte van de gegevens die door de gegevensverbinding worden ontvangen. Dit is de grootte van de gegevensstroom of van de onbewerkte gegevensgrootte, indien opgegeven.|ComponentType, ComponentName|
|StageLatency|Yes|Faselatentie|Seconden|Gemiddeld|Cumulatieve tijd vanaf het moment dat een bericht wordt ontdekt totdat het wordt ontvangen door het rapportageonderdeel voor verwerking (detectietijd wordt ingesteld wanneer het bericht wordt in de wachtrij geplaatst voor opname of wanneer het wordt ontdekt door de gegevensverbinding).|Database, ComponentType|
|SteamingIngestRequestRate|Yes|Aanvraagsnelheid streamingopname|Count|RateRequestsPerSecond|Aanvraagsnelheid streaming-opname (aanvragen per seconde)|Geen dimensies|
|StreamingIngestDataRate|Yes|Gegevenssnelheid streamingopname|Count|Gemiddeld|Opnamesnelheid van streaminggegevens (MB per seconde)|Geen dimensies|
|StreamingIngestDuration|Yes|Duur streamingopname|Milliseconden|Gemiddeld|Duur van streaming-opname in milliseconden|Geen dimensies|
|StreamingIngestResults|Yes|Resultaat streamingopname|Count|Gemiddeld|Resultaat streaming-opname|Resultaat|
|TotalNumberOfConcurrentQueries|Yes|Totaal aantal gelijktijdige query's|Count|Maximum|Totaal aantal gelijktijdige query's|Geen dimensies|
|TotalNumberOfExtents|Yes|Totaal aantal extents|Aantal|Totaal|Totaal aantal gegevensreten|Geen dimensies|
|TotalNumberOfThrottledCommands|Yes|Totaal aantal beperkt opdrachten|Aantal|Totaal|Totaal aantal beperkt opdrachten|Commandtype|
|TotalNumberOfThrottledQueries|Yes|Totaal aantal beperkt query's|Count|Maximum|Totaal aantal beperkt query's|Geen dimensies|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Actielatentie |Seconden|Gemiddeld|Latentie van voltooide werkstroomacties.|Geen dimensies|
|ActiesCompleted|Yes|Acties voltooid |Aantal|Totaal|Aantal voltooide werkstroomacties.|Geen dimensies|
|ActionsFailed|Yes|Acties zijn mislukt |Aantal|Totaal|Het aantal werkstroomacties is mislukt.|Geen dimensies|
|ActionsSkipped|Yes|Acties overgeslagen |Aantal|Totaal|Het aantal werkstroomacties dat is overgeslagen.|Geen dimensies|
|ActionsStarted|Yes|Acties gestart |Aantal|Totaal|Aantal gestarte werkstroomacties.|Geen dimensies|
|ActionsSucceeded|Yes|Acties geslaagd |Aantal|Totaal|Het aantal werkstroomacties is geslaagd.|Geen dimensies|
|ActionSuccessLatency|Yes|Latentie bij succes van actie |Seconden|Gemiddeld|Latentie van geslaagd werkstroomacties.|Geen dimensies|
|ActionThrottledEvents|Yes|Gebeurtenissen die zijn beperkt door acties|Aantal|Totaal|Aantal werkstroomactie heeft gebeurtenissen beperkt..|Geen dimensies|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Geheugengebruik van connector voor Integratieserviceomgeving|Percentage|Gemiddeld|Geheugengebruik van connector voor integratieserviceomgeving.|Geen dimensies|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Processorgebruik van connector voor Integratieserviceomgeving|Percentage|Gemiddeld|Processorgebruik van connector voor integratieserviceomgeving.|Geen dimensies|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Werkstroomgeheugengebruik voor Integratieserviceomgeving|Percentage|Gemiddeld|Werkstroomgeheugengebruik voor integratieserviceomgeving.|Geen dimensies|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Werkstroomprocessorgebruik voor Integratieserviceomgeving|Percentage|Gemiddeld|Werkstroomprocessorgebruik voor integratieserviceomgeving.|Geen dimensies|
|RunFailurePercentage|Yes|Percentage mislukte run|Percentage|Totaal|Percentage mislukte werkstroomtaken.|Geen dimensies|
|RunLatency|Yes|Latentie uitvoeren|Seconden|Gemiddeld|Latentie van voltooide werkstroom uitgevoerd.|Geen dimensies|
|RunsCancelled|Yes|Runs Cancelled|Aantal|Totaal|Aantal werkstroom uitgevoerd geannuleerd.|Geen dimensies|
|RunsCompleted|Yes|Voltooide runs|Aantal|Totaal|Aantal voltooide werkstroom uitgevoerd.|Geen dimensies|
|RunsFailed|Yes|Kan niet worden uitgevoerd|Aantal|Totaal|Aantal mislukte werkstroom uitgevoerd.|Geen dimensies|
|RunsStarted|Yes|Gestarte runs|Aantal|Totaal|Aantal gestarte werkstroomtaken.|Geen dimensies|
|RunsSucceeded|Yes|Runs Succeeded|Aantal|Totaal|Aantal werkstroomtaken is geslaagd.|Geen dimensies|
|RunStartThrottledEvents|Yes|Gebeurtenissen die zijn beperkt bij het starten van de start|Aantal|Totaal|Aantal gebeurtenissen die zijn beperkt door het starten van de werkstroom.|Geen dimensies|
|RunSuccessLatency|Yes|Latentie van het uitvoeren van een succes|Seconden|Gemiddeld|Latentie van geslaagd werkstroom wordt uitgevoerd.|Geen dimensies|
|RunThrottledEvents|Yes|Vertragingsgebeurtenissen uitvoeren|Aantal|Totaal|Aantal werkstroomactie- of trigger-vertragingsgebeurtenissen.|Geen dimensies|
|TriggerFireLatency|Yes|Brandlatentie activeren |Seconden|Gemiddeld|Latentie van activerende werkstroomtriggers.|Geen dimensies|
|TriggerLatency|Yes|Triggerlatentie |Seconden|Gemiddeld|Latentie van voltooide werkstroomtriggers.|Geen dimensies|
|TriggersCompleted|Yes|Triggers voltooid |Aantal|Totaal|Aantal voltooide werkstroomtriggers.|Geen dimensies|
|TriggersFailed|Yes|Triggers zijn mislukt |Aantal|Totaal|Het aantal werkstroomtriggers is mislukt.|Geen dimensies|
|TriggersFired|Yes|Triggers die zijn afgelost |Aantal|Totaal|Het aantal werkstroomtriggers dat is activeren.|Geen dimensies|
|TriggersKipped|Yes|Triggers overgeslagen|Aantal|Totaal|Het aantal werkstroomtriggers dat is overgeslagen.|Geen dimensies|
|TriggersStarted|Yes|Triggers gestart |Aantal|Totaal|Aantal gestarte werkstroomtriggers.|Geen dimensies|
|TriggersSucceeded|Yes|Triggers geslaagd |Aantal|Totaal|Het aantal werkstroomtriggers is geslaagd.|Geen dimensies|
|TriggerSuccessLatency|Yes|Latentie van triggersucces |Seconden|Gemiddeld|Latentie van geslaagd werkstroomtriggers.|Geen dimensies|
|TriggerThrottledEvents|Yes|Gebeurtenissen die zijn beperkt activeren|Aantal|Totaal|Aantal gebeurtenissen die zijn beperkt door de werkstroomtrigger.|Geen dimensies|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Actielatentie |Seconden|Gemiddeld|Latentie van voltooide werkstroomacties.|Geen dimensies|
|ActiesCompleted|Yes|Acties voltooid |Aantal|Totaal|Aantal voltooide werkstroomacties.|Geen dimensies|
|ActionsFailed|Yes|Acties mislukt |Aantal|Totaal|Het aantal werkstroomacties is mislukt.|Geen dimensies|
|ActionsSkipped|Yes|Acties overgeslagen |Aantal|Totaal|Het aantal werkstroomacties dat is overgeslagen.|Geen dimensies|
|ActionsStarted|Yes|Acties gestart |Aantal|Totaal|Aantal gestarte werkstroomacties.|Geen dimensies|
|ActionsSucceeded|Yes|Acties geslaagd |Aantal|Totaal|Aantal werkstroomacties is geslaagd.|Geen dimensies|
|ActionSuccessLatency|Yes|Latentie bij succes van actie |Seconden|Gemiddeld|Latentie van geslaagd werkstroomacties.|Geen dimensies|
|ActionThrottledEvents|Yes|Gebeurtenissen die zijn beperkt door acties|Aantal|Totaal|Aantal gebeurtenissen die zijn beperkt door de werkstroomactie.|Geen dimensies|
|BillableActionExecutions|Yes|Factureerbare uitvoeringen van acties|Aantal|Totaal|Het aantal uitvoeringen van werkstroomactie dat wordt gefactureerd.|Geen dimensies|
|BillableTriggerExecutions|Yes|Factureerbare triggeruitvoeringen|Aantal|Totaal|Het aantal uitvoeringen van werkstroomtriggers dat wordt gefactureerd.|Geen dimensies|
|BillingUsageNativeOperation|Yes|Factureringsgebruik voor uitvoeringen van native bewerking|Aantal|Totaal|Het aantal uitvoeringen van native bewerking dat wordt gefactureerd.|Geen dimensies|
|BillingUsageStandardConnector|Yes|Factureringsgebruik voor uitvoeringen van standaardconnector|Aantal|Totaal|Het aantal uitvoeringen van de standaardconnector dat wordt gefactureerd.|Geen dimensies|
|BillingUsageStorageConsumption|Yes|Factureringsgebruik voor uitvoeringen van opslagverbruik|Aantal|Totaal|Het aantal uitvoeringen van opslagverbruik dat wordt gefactureerd.|Geen dimensies|
|RunFailurePercentage|Yes|Percentage mislukte run|Percentage|Totaal|Het percentage werkstroom uitgevoerd is mislukt.|Geen dimensies|
|RunLatency|Yes|Latentie uitvoeren|Seconden|Gemiddeld|Latentie van voltooide werkstroom uitgevoerd.|Geen dimensies|
|RunsCancelled|Yes|Runs Cancelled|Aantal|Totaal|Aantal werkstroom uitgevoerd geannuleerd.|Geen dimensies|
|RunsCompleted|Yes|Voltooide runs|Aantal|Totaal|Aantal voltooide werkstroomtaken.|Geen dimensies|
|RunsFailed|Yes|Kan niet worden uitgevoerd|Aantal|Totaal|Het aantal werkstroomtaken is mislukt.|Geen dimensies|
|RunsStarted|Yes|Gestarte runs|Aantal|Totaal|Aantal gestarte werkstroomtaken.|Geen dimensies|
|RunsSucceeded|Yes|Runs Succeeded|Aantal|Totaal|Aantal werkstroomtaken is geslaagd.|Geen dimensies|
|RunStartThrottledEvents|Yes|Gebeurtenissen met vertraging bij starten uitvoeren|Aantal|Totaal|Aantal gebeurtenissen die zijn beperkt door het starten van de werkstroom.|Geen dimensies|
|RunSuccessLatency|Yes|Latentie van succes uitvoeren|Seconden|Gemiddeld|Latentie van geslaagd werkstroom uitgevoerd.|Geen dimensies|
|RunThrottledEvents|Yes|Vertragingsgebeurtenissen uitvoeren|Aantal|Totaal|Aantal werkstroomactie of trigger beperkt gebeurtenissen.|Geen dimensies|
|TotalBillableExecutions|Yes|Totaal aantal factureerbare uitvoeringen|Aantal|Totaal|Het aantal werkstroomuitvoeringen dat wordt gefactureerd.|Geen dimensies|
|TriggerFireLatency|Yes|Brandlatentie activeren |Seconden|Gemiddeld|Latentie van activerende werkstroomtriggers.|Geen dimensies|
|TriggerLatency|Yes|Triggerlatentie |Seconden|Gemiddeld|Latentie van voltooide werkstroomtriggers.|Geen dimensies|
|TriggersCompleted|Yes|Triggers voltooid |Aantal|Totaal|Aantal voltooide werkstroomtriggers.|Geen dimensies|
|TriggersFailed|Yes|Triggers zijn mislukt |Aantal|Totaal|Het aantal werkstroomtriggers is mislukt.|Geen dimensies|
|TriggersFired|Yes|Triggers zijn activeren |Aantal|Totaal|Het aantal werkstroomtriggers dat is activeren.|Geen dimensies|
|TriggersKipped|Yes|Triggers overgeslagen|Aantal|Totaal|Het aantal werkstroomtriggers dat is overgeslagen.|Geen dimensies|
|TriggersStarted|Yes|Triggers gestart |Aantal|Totaal|Aantal gestarte werkstroomtriggers.|Geen dimensies|
|TriggersSucceeded|Yes|Triggers geslaagd |Aantal|Totaal|Het aantal werkstroomtriggers is geslaagd.|Geen dimensies|
|TriggerSuccessLatency|Yes|Latentie van triggersucces |Seconden|Gemiddeld|Latentie van geslaagd werkstroomtriggers.|Geen dimensies|
|TriggerThrottledEvents|Yes|Trigger Throttled Events|Aantal|Totaal|Het aantal werkstroomtriggers dat is beperkt.|Geen dimensies|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Actieve kernen|Yes|Actieve kernen|Count|Gemiddeld|Aantal actieve kernen|Scenario, ClusterName|
|Actieve knooppunten|Yes|Actieve knooppunten|Count|Gemiddeld|Aantal knooppunten. Dit zijn de knooppunten die actief een taak uitvoeren.|Scenario, ClusterName|
|Aangevraagde runs annuleren|Yes|Aangevraagde runs annuleren|Aantal|Totaal|Aantal runs waarbij annuleren is aangevraagd voor deze werkruimte. Aantal wordt bijgewerkt wanneer een annuleringsaanvraag is ontvangen voor een run.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Geannuleerde runs|Yes|Geannuleerde runs|Aantal|Totaal|Aantal runs geannuleerd voor deze werkruimte. Aantal wordt bijgewerkt wanneer een run is geannuleerd.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Voltooide runs|Yes|Voltooide runs|Aantal|Totaal|Het aantal voltooide runs voor deze werkruimte. Aantal wordt bijgewerkt wanneer een run is voltooid en de uitvoer is verzameld.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Cpu-gebruik|Yes|Cpu-gebruik|Count|Gemiddeld|Percentage gebruik op een CPU-knooppunt. Gebruik wordt gerapporteerd met intervallen van één minuut.|Scenario, runId, NodeId, ClusterName|
|Fouten|Yes|Fouten|Aantal|Totaal|Het aantal runfouten in deze werkruimte. Aantal wordt bijgewerkt wanneer er een fout wordt aangetroffen bij de run.|Scenario|
|Mislukte runs|Yes|Mislukte runs|Aantal|Totaal|Het aantal mislukte runs voor deze werkruimte. Aantal wordt bijgewerkt wanneer een run mislukt.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Runs worden uitgevoerd|Yes|Runs worden uitgevoerd|Aantal|Totaal|Het aantal runs dat de status 'Finalizing' voor deze werkruimte heeft bereikt. Aantal wordt bijgewerkt wanneer een uitvoering is voltooid, maar de uitvoerverzameling nog wordt uitgevoerd.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Count|Gemiddeld|Percentage geheugengebruik op een GPU-knooppunt. Gebruik wordt gerapporteerd met intervallen van één minuut.|Scenario, runId, NodeId, DeviceId, ClusterName|
|Gpu-utilisatie|Yes|Gpu-utilisatie|Count|Gemiddeld|Percentage gebruik op een GPU-knooppunt. Gebruik wordt gerapporteerd met intervallen van één minuut.|Scenario, runId, NodeId, DeviceId, ClusterName|
|Niet-actieve kernen|Yes|Niet-actieve kernen|Count|Gemiddeld|Aantal niet-actieve kernen|Scenario, ClusterName|
|Niet-actieve knooppunten|Yes|Niet-actieve knooppunten|Count|Gemiddeld|Aantal niet-actieve knooppunten. Niet-actieve knooppunten zijn de knooppunten waarop geen taken worden uitgevoerd, maar die indien beschikbaar een nieuwe taak kunnen accepteren.|Scenario, ClusterName|
|Kernen verlaten|Yes|Kernen verlaten|Count|Gemiddeld|Aantal verlatende kernen|Scenario, ClusterName|
|Knooppunten verlaten|Yes|Knooppunten verlaten|Count|Gemiddeld|Aantal knooppunten dat de knooppunten verlaat. De knooppunten die de taak verlaten, zijn de knooppunten die net klaar zijn met het verwerken van een taak en die de status Niet-actief krijgen.|Scenario, ClusterName|
|Model implementeren is mislukt|Yes|Model implementeren is mislukt|Aantal|Totaal|Aantal modelimplementaties dat is mislukt in deze werkruimte|Scenario, StatusCode|
|Model implementeren gestart|Yes|Model implementeren gestart|Aantal|Totaal|Aantal modelimplementaties gestart in deze werkruimte|Scenario|
|Model implementeren geslaagd|Yes|Model implementeren geslaagd|Aantal|Totaal|Aantal modelimplementaties dat is geslaagd in deze werkruimte|Scenario|
|Modelregister is mislukt|Yes|Modelregister is mislukt|Aantal|Totaal|Aantal modelregistraties dat is mislukt in deze werkruimte|Scenario, StatusCode|
|Modelregister geslaagd|Yes|Modelregister geslaagd|Aantal|Totaal|Aantal modelregistraties dat is geslaagd in deze werkruimte|Scenario|
|Niet reagerende runs|Yes|Niet reagerende runs|Aantal|Totaal|Het aantal runs dat niet reageert op deze werkruimte. Aantal wordt bijgewerkt wanneer een run de status Reageert niet heeft.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Niet gestarte runs|Yes|Niet gestarte runs|Aantal|Totaal|Aantal runs met de status Niet gestart voor deze werkruimte. Aantal wordt bijgewerkt wanneer een aanvraag wordt ontvangen om een run te maken, maar de gegevens van de run nog niet zijn ingevuld. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Preempted Cores|Yes|Preempted Cores|Count|Gemiddeld|Aantal bestaande kernen|Scenario, ClusterName|
|Verwijderde knooppunten|Yes|Verwijderde knooppunten|Count|Gemiddeld|Het aantal verwijderde knooppunten. Deze knooppunten zijn de knooppunten met lage prioriteit die worden verwijderd uit de beschikbare knooppuntgroep.|Scenario, ClusterName|
|Runs voorbereiden|Yes|Runs voorbereiden|Aantal|Totaal|Het aantal runs dat wordt voorbereid voor deze werkruimte. Aantal wordt bijgewerkt wanneer een run de status Voorbereiden krijgt terwijl de run-omgeving wordt voorbereid.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Inrichtings uitgevoerd|Yes|Inrichtings uitgevoerd|Aantal|Totaal|Het aantal runs dat wordt ingericht voor deze werkruimte. Aantal wordt bijgewerkt wanneer een run wacht op het maken of inrichten van het rekendoel.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|In de wachtrij geplaatste runs|Yes|In de wachtrij geplaatste runs|Aantal|Totaal|Het aantal runs dat in de wachtrij voor deze werkruimte staat. Aantal wordt bijgewerkt wanneer een run in de wachtrij wordt geplaatst in het rekendoel. Kan optreden wanneer u wacht totdat de vereiste rekenknooppunten gereed zijn.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Percentage quotumgebruik|Yes|Percentage quotumgebruik|Count|Gemiddeld|Percentage van het gebruikte quotum|Scenario, ClusterName, VmFamilyName, VmPriority|
|Gestarte runs|Yes|Gestarte runs|Aantal|Totaal|Het aantal runs dat wordt uitgevoerd voor deze werkruimte. Aantal wordt bijgewerkt wanneer de run wordt gestart op de vereiste resources.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Start-runs|Yes|Start-runs|Aantal|Totaal|Aantal gestarte runs voor deze werkruimte. Aantal wordt bijgewerkt nadat de aanvraag voor het maken van run- en run-informatie, zoals de run-id, is ingevuld|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Totaal aantal kernen|Yes|Totaal aantal kernen|Count|Gemiddeld|Aantal totale kernen|Scenario, ClusterName|
|Totaal aantal knooppunten|Yes|Totaal aantal knooppunten|Count|Gemiddeld|Het totale aantal knooppunten. Dit totaal omvat enkele actieve knooppunten, niet-actieve knooppunten, niet-actieve knooppunten, niet-goed geconfigureerde knooppunten, verlatende knooppunten|Scenario, ClusterName|
|Onbruikbaar kernen|Yes|Onbruikbaar kernen|Count|Gemiddeld|Aantal onbruikbaar kernen|Scenario, ClusterName|
|Onbruikbaar knooppunten|Yes|Onbruikbaar knooppunten|Count|Gemiddeld|Aantal onbruikbaar knooppunten. Onbereikbare knooppunten werken niet vanwege een onoplosbaar probleem. Deze knooppunten worden door Azure gerecycled.|Scenario, ClusterName|
|Waarschuwingen|Yes|Waarschuwingen|Aantal|Totaal|Aantal waarschuwingen voor uitvoeren in deze werkruimte. Aantal wordt bijgewerkt wanneer een run een waarschuwing tegenkomt.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Beschikbaarheid van de API's|ApiCategory, ApiName|
|Gebruik|No|Gebruik|Count|Count|Aantal API-aanroepen|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Aantal activa|Count|Gemiddeld|Hoeveel assets zijn er al gemaakt in het huidige Media Service-account|Geen dimensies|
|AssetQuota|Yes|Activumquotum|Count|Gemiddeld|Hoeveel assets zijn toegestaan voor het huidige Media Service-account|Geen dimensies|
|AssetQuotaUsedPercentage|Yes|Percentage gebruikte activumquota|Percentage|Gemiddeld|Percentage gebruikte activa in het huidige Media Service-account|Geen dimensies|
|ChannelsAndLiveEventsCount|Yes|Aantal livegebeurtenissen|Count|Gemiddeld|Het totale aantal livegebeurtenissen in het huidige Media Services-account|Geen dimensies|
|ContentKeyPolicyCount|Yes|Aantal beleid voor inhoudssleutels|Count|Gemiddeld|Hoeveel beleidsregels voor inhoudssleutels zijn al gemaakt in het huidige Media Service-account|Geen dimensies|
|ContentKeyPolicyQuota|Yes|Quotum voor beleid voor inhoudssleutels|Count|Gemiddeld|Hoeveel inhoudssleutels zijn toegestaan voor het huidige Media Service-account|Geen dimensies|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Percentage gebruikt quotum voor beleid voor inhoudssleutels|Percentage|Gemiddeld|Percentage gebruikte inhoudssleutelbeleid in huidig Media Service-account|Geen dimensies|
|MaxChannelsAndLiveEventsCount|Yes|Maximum aantal livegebeurtenissen|Count|Maximum|Het maximum aantal livegebeurtenissen dat is toegestaan in het huidige Media Services-account|Geen dimensies|
|MaxRunningChannelsAndLiveEventsCount|Yes|Maximaal quotum voor het uitvoeren van livegebeurtenissen|Count|Maximum|Het maximum aantal livegebeurtenissen dat is toegestaan in het huidige Media Services-account|Geen dimensies|
|RunningChannelsAndLiveEventsCount|Yes|Aantal livegebeurtenissen uitvoeren|Count|Gemiddeld|Het totale aantal livegebeurtenissen dat wordt uitgevoerd in het huidige Media Services-account|Geen dimensies|
|StreamingPolicyCount|Yes|Aantal streamingbeleid|Count|Gemiddeld|Hoeveel streamingbeleidsregels zijn er al gemaakt in het huidige Media Service-account|Geen dimensies|
|StreamingPolicyQuota|Yes|Quotum voor streamingbeleid|Count|Gemiddeld|Hoeveel streamingbeleidsregels zijn toegestaan voor het huidige Media Service-account|Geen dimensies|
|StreamingPolicyQuotaUsedPercentage|Yes|Percentage gebruikt quotum voor streamingbeleid|Percentage|Gemiddeld|Percentage gebruikt streamingbeleid in huidig mediaserviceaccount|Geen dimensies|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Bitrate voor opnemen van livegebeurtenissen|BitsPerSecond|Gemiddeld|De binnenkomende bitrate die is opgenomen voor een livegebeurtenis, in bits per seconde.|TrackName|
|IngestDriftValue|Yes|Driftwaarde voor opnemen van livegebeurtenissen|Seconden|Maximum|Afwijking tussen de tijdstempel van de opgenomen inhoud en de systeemklok, gemeten in seconden per minuut. Een waarde die niet nul geeft aan dat de opgenomen inhoud langzamer dan systeemklok tijd aankomt.|TrackName|
|IngestLastTimestamp|Yes|Laatste tijdstempel van opname van livegebeurtenis|Milliseconden|Maximum|Laatste tijdstempel opgenomen voor een livegebeurtenis.|TrackName|
|LiveOutputLastTimestamp|Yes|Tijdstempel van laatste uitvoer|Milliseconden|Maximum|Tijdstempel van het laatste fragment dat is geüpload naar de opslag voor de uitvoer van een livegebeurtenis.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|CPU|Yes|CPU-gebruik|Percentage|Gemiddeld|CPU-gebruik voor Premium Streaming-eindpunten. Deze gegevens zijn niet beschikbaar voor standaardstreaming-eindpunten.|Geen dimensies|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid Egress-gegevens, in bytes.|OutputFormat|
|EgressBandwidth|No|Bandbreedte voor uit te gaan|BitsPerSecond|Gemiddeld|Bandbreedte voor uit te gaan in bits per seconde.|Geen dimensies|
|Aanvragen|Yes|Aanvragen|Aantal|Totaal|Aanvragen naar een streaming-eindpunt.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|End-to-end latentie voor succes|Milliseconden|Gemiddeld|De gemiddelde latentie voor geslaagde aanvragen in milliseconden.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Active Rendering-sessies|Count|Gemiddeld|Totaal aantal actieve renderingsessies|SessionType, SDKVersion|
|AssetsGeconverteerd|Yes|Geconverteerde assets|Aantal|Totaal|Totaal aantal geconverteerd assets|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Gemaakte ankers|Aantal|Totaal|Aantal gemaakte ankers|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Ankers verwijderd|Aantal|Totaal|Aantal verwijderde ankers|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Query's voor Ankers|Aantal|Totaal|Aantal Spatial Anchors opgevraagd|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Ankers bijgewerkt|Aantal|Totaal|Aantal ankers bijgewerkt|DeviceFamily, SDKVersion|
|PosesFound|Yes|Gevonden houdingen|Aantal|Totaal|Aantal geretourneerde poses|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Yes|Totaal aantal dagelijkse ankers|Count|Gemiddeld|Totaal aantal ankers - dagelijks|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Toegewezen poolgrootte|Bytes|Gemiddeld|Inrichtende grootte van deze pool|Geen dimensies|
|VolumePoolAllocatedToVolumeThroughput|Yes|Door pool toegewezen doorvoer|BytesPerSecond|Gemiddeld|Som van de doorvoer van alle volumes die tot de groep behoren|Geen dimensies|
|VolumePoolAllocatedUsed|Yes|Pool toegewezen aan volumegrootte|Bytes|Gemiddeld|Toegewezen gebruikte grootte van de pool|Geen dimensies|
|VolumePoolProvisionedThroughput|Yes|Inrichten van doorvoer voor de pool|BytesPerSecond|Gemiddeld|Inrichten van doorvoer van deze pool|Geen dimensies|
|VolumePoolTotalLogicalSize|Yes|Verbruikte poolgrootte|Bytes|Gemiddeld|Som van de logische grootte van alle volumes die tot de groep behoren|Geen dimensies|
|VolumePoolTotalSnapshotSize|Yes|Totale grootte van momentopname voor de pool|Bytes|Gemiddeld|Som van de grootte van de momentopname van alle volumes in deze pool|Geen dimensies|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Gemiddelde leeslatentie|Milliseconden|Gemiddeld|Gemiddelde leeslatentie in milliseconden per bewerking|Geen dimensies|
|AverageWriteLatency|Yes|Gemiddelde schrijflatentie|Milliseconden|Gemiddeld|Gemiddelde schrijflatentie in milliseconden per bewerking|Geen dimensies|
|VolumeBackupActive|Yes|Is volumeback-up tijdelijk?|Count|Gemiddeld|Is het back-upbeleid voor het volume opgeschort? 0 zo ja, 1 als dat niet zo is.|Geen dimensies|
|ProgrammaVolumeLogicalBackupBytes|Yes|Bytes voor volumeback-up|Bytes|Gemiddeld|Er is een back-up gemaakt van het totale aantal bytes voor dit volume.|Geen dimensies|
|VolumeOperationComplete|Yes|Is de volumeback-upbewerking voltooid?|Count|Gemiddeld|Is de laatste volumeback-up of herstelbewerking voltooid? 1 zo ja, 0 als dat niet zo is.|Geen dimensies|
|VolumeOperationTransferredBytes|Yes|Volumeback-up laatst overgedragen bytes|Bytes|Gemiddeld|Het totale aantal bytes dat is overgedragen voor de laatste back-up- of herstelbewerking.|Geen dimensies|
|VolumeProtected|Yes|Is volumeback-up ingeschakeld|Count|Gemiddeld|Is back-up ingeschakeld voor het volume? 1 zo ja, 0 als dat niet zo is.|Geen dimensies|
|OtherThroughput|Yes|Andere doorvoer|BytesPerSecond|Gemiddeld|Andere doorvoer (die niet is gelezen of geschreven) in bytes per seconde|Geen dimensies|
|ReadIops|Yes|IOPS lezen|CountPerSecond|Gemiddeld|In-/uitbewerkingen per seconde lezen|Geen dimensies|
|ReadThroughput|Yes|Doorvoer lezen|BytesPerSecond|Gemiddeld|Leesdoorvoer in bytes per seconde|Geen dimensies|
|TotalThroughput|Yes|Totale doorvoer|BytesPerSecond|Gemiddeld|Som van alle doorvoer in bytes per seconde|Geen dimensies|
|VolumeAllocatedSize|Yes|Toegewezen volumegrootte|Bytes|Gemiddeld|De inrichtende grootte van een volume|Geen dimensies|
|VolumeConsumedSizePercentage|Yes|Percentage verbruikte volumegrootte|Percentage|Gemiddeld|Het percentage van het verbruikte volume, inclusief momentopnamen.|Geen dimensies|
|VolumeLogicalSize|Yes|Volume verbruikte grootte|Bytes|Gemiddeld|Logische grootte van het volume (gebruikte bytes)|Geen dimensies|
|VolumeSnapshotSize|Yes|Grootte van momentopname van volume|Bytes|Gemiddeld|Grootte van alle momentopnamen in volume|Geen dimensies|
|WriteIops|Yes|IOPS schrijven|CountPerSecond|Gemiddeld|In-/uitbewerkingen per seconde schrijven|Geen dimensies|
|WriteThroughput|Yes|Schrijfdoorvoer|BytesPerSecond|Gemiddeld|Schrijfdoorvoer in bytes per seconde|Geen dimensies|
|XregionReplicationHealthy|Yes|Is de status van volumereplicatie in orde?|Count|Gemiddeld|Voorwaarde van de relatie, 1 of 0.|Geen dimensies|
|XregionReplicationLagTime|Yes|Vertragingstijd voor volumereplicatie|Seconden|Gemiddeld|De hoeveelheid tijd in seconden waarmee de gegevens op de mirror achterblijven op de bron.|Geen dimensies|
|XregionReplicationLastTransferDuration|Yes|Duur van laatste overdracht voor volumereplicatie|Seconden|Gemiddeld|De hoeveelheid tijd in seconden die nodig was voor de laatste overdracht.|Geen dimensies|
|XregionReplicationLastTransferSize|Yes|Grootte van laatste overdracht voor volumereplicatie|Bytes|Gemiddeld|Het totale aantal bytes dat is overgedragen als onderdeel van de laatste overdracht.|Geen dimensies|
|XregionReplicationRelationshipProgress|Yes|Voortgang van volumereplicatie|Bytes|Gemiddeld|Totale hoeveelheid gegevens die is overgedragen voor de huidige overdrachtsbewerking.|Geen dimensies|
|XregionReplicationRelationshipTransferring|Yes|Wordt volumereplicatie overgeplaatst?|Count|Gemiddeld|Of de status van de volumereplicatie 'overdracht' is.|Geen dimensies|
|XregionReplicationTotalTransferBytes|Yes|Totale overdracht volumereplicatie|Bytes|Gemiddeld|Cumulatieve bytes overgedragen voor de relatie.|Geen dimensies|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway totale tijd|Milliseconden|Gemiddeld|De gemiddelde tijd die het kost om een aanvraag te verwerken en de reactie erop te sturen. Dit wordt berekend als gemiddelde van het interval vanaf het moment waarop Application Gateway de eerste byte van een HTTP-aanvraag ontvangt tot het moment waarop de verzendbewerking voor het antwoord is voltooien. Het is belangrijk te weten dat dit meestal de Application Gateway verwerkingstijd omvat, de tijd dat de aanvraag- en antwoordpakketten via het netwerk worden gestuurd en de tijd die de back-endserver nodig had om te reageren.|Listener|
|AvgRequestCountPerHealthyHost|No|Aanvragen per minuut per host met een goede gezondheid|Count|Gemiddeld|Gemiddeld aantal aanvragen per minuut per goede back-endhost in een groep|BackendSettingsPool|
|BackendConnectTime|No|Verbindingstijd back-en-back-en|Milliseconden|Gemiddeld|Tijd besteed aan het tot stand brengen van een verbinding met een back-mailserver|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Reactietijd van eerste back-eind byte|Milliseconden|Gemiddeld|Tijdsinterval tussen het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de eerste byte van de antwoordheader, wat de verwerkingstijd van de back-endserver nakomt|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Reactietijd van laatste back-eind byte|Milliseconden|Gemiddeld|Tijdsinterval tussen het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord-body|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Antwoordstatus van back-end|Aantal|Totaal|Het aantal HTTP-antwoordcodes dat is gegenereerd door de back-endleden. Dit omvat geen responscodes die door de Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Web Application Firewall regeldistributie geblokkeerde aanvragen|Aantal|Totaal|Web Application Firewall distributie van regels voor geblokkeerde aanvragen|RuleGroup, RuleId|
|BlockedReqCount|Yes|Web Application Firewall aantal geblokkeerde aanvragen|Aantal|Totaal|Web Application Firewall aantal geblokkeerde aanvragen|Geen dimensies|
|BytesReceived|Yes|Ontvangen bytes|Bytes|Totaal|Het totale aantal bytes dat is ontvangen door de Application Gateway van de clients|Listener|
|BytesSent|Yes|Verzonden bytes|Bytes|Totaal|Het totale aantal bytes dat door de Application Gateway naar de clients|Listener|
|CapacityUnits|No|Huidige capaciteitseenheden|Count|Gemiddeld|Verbruikte capaciteitseenheden|Geen dimensies|
|ClientRtt|No|Client RTT|Milliseconden|Gemiddeld|Gemiddelde retourtijd tussen clients en Application Gateway. Deze metrische gegevens geven aan hoe lang het duurt om verbindingen tot stand te brengen en bevestigingen te retourneren|Listener|
|ComputeUnits|No|Huidige rekeneenheden|Count|Gemiddeld|Verbruikte rekeneenheden|Geen dimensies|
|Cpu-utilisatie|No|CPU-gebruik|Percentage|Gemiddeld|Huidig CPU-gebruik van de Application Gateway|Geen dimensies|
|CurrentConnections|Yes|Huidige verbindingen|Aantal|Totaal|Aantal huidige verbindingen tot stand gebracht met Application Gateway|Geen dimensies|
|EstimatedBilledCapacityUnits|No|Geschatte gefactureerde capaciteitseenheden|Count|Gemiddeld|Geschatte capaciteitseenheden die in rekening worden gebracht|Geen dimensies|
|FailedRequests|Yes|Mislukte aanvragen|Aantal|Totaal|Aantal mislukte aanvragen dat Application Gateway heeft ingediend|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Vaste factureerbare capaciteitseenheden|Count|Gemiddeld|Minimale capaciteitseenheden die in rekening worden gebracht|Geen dimensies|
|HealthyHostCount|Yes|Goed aantal host|Count|Gemiddeld|Aantal gezonde back-endhosts|BackendSettingsPool|
|MatchedCount|Yes|Web Application Firewall totale regeldistributie|Aantal|Totaal|Web Application Firewall totale regeldistributie voor het binnenkomende verkeer|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Nieuwe verbindingen per seconde|CountPerSecond|Gemiddeld|Nieuwe verbindingen per seconde tot stand gebracht met Application Gateway|Geen dimensies|
|ResponseStatus|Yes|Antwoordstatus|Aantal|Totaal|Http-antwoordstatus geretourneerd door Application Gateway|HttpStatusGroup|
|Doorvoer|No|Doorvoer|BytesPerSecond|Gemiddeld|Aantal bytes per seconde dat de Application Gateway heeft gebruikt|Geen dimensies|
|TlsProtocol|Yes|Client-TLS-protocol|Aantal|Totaal|Het aantal TLS- en niet-TLS-aanvragen dat is geïnitieerd door de client die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocoldistributie wilt weergeven, filtert u op het dimensie-TLS-protocol.|Listener, TlsProtocol|
|TotalRequests|Yes|Totaal aantal aanvragen|Aantal|Totaal|Aantal geslaagde aanvragen dat Application Gateway heeft ingediend|BackendSettingsPool|
|UnhealthyHostCount|Yes|Aantal niet in orde host|Count|Gemiddeld|Aantal beschadigde back-endhosts|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Aantal treffers toepassingsregels|Aantal|Totaal|Aantal keer dat toepassingsregels zijn geraakt|Status, Reden, Protocol|
|DataProcessed|Yes|Verwerkte gegevens|Bytes|Totaal|Totale hoeveelheid gegevens die door deze firewall wordt verwerkt|Geen dimensies|
|FirewallHealth|Yes|Firewall-status|Percentage|Gemiddeld|Geeft de algehele status van deze firewall aan|Status, Reden|
|NetworkRuleHit|Yes|Aantal treffers in netwerkregels|Aantal|Totaal|Aantal keer dat netwerkregels zijn geraakt|Status, Reden, Protocol|
|SNATPortUtilization|Yes|SNAT-poortgebruik|Percentage|Gemiddeld|Percentage uitgaande SNAT-poorten dat momenteel in gebruik is|Protocol|
|Doorvoer|No|Doorvoer|BitsPerSecond|Gemiddeld|Doorvoer verwerkt door deze firewall|Geen dimensies|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|pingmesh|No|Communicatiestatus van Bastion|Count|Gemiddeld|Communicatiestatus geeft 1 aan als alle communicatie goed is en 0 als het slecht is.||
|Sessies|No|Aantal sessies|Aantal|Totaal|Aantal sessies voor bastion. Weergeven in som en per exemplaar.|host|
|totaal|Yes|Totaal geheugen|Count|Gemiddeld|Totale geheugenstatistieken.|host|
|usage_user|No|Gebruikte CPU|Count|Gemiddeld|Statistieken over CPU-gebruik.|cpu, host|
|protocollen|Yes|Gebruikt geheugen|Count|Gemiddeld|Statistieken over geheugengebruik.|host|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure binnenkomen|Geen dimensies|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure verlaten|Geen dimensies|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|QueryVolume|No|Queryvolume|Aantal|Totaal|Aantal query's dat wordt gebruikt voor een DNS-zone|Geen dimensies|
|RecordSetCapacityUtilization|No|Capaciteitsgebruik recordset|Percentage|Maximum|Percentage van de recordsetcapaciteit die wordt gebruikt door een DNS-zone|Geen dimensies|
|RecordSetCount|No|Aantal recordset|Count|Maximum|Aantal recordsets in een DNS-zone|Geen dimensies|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|Arp-beschikbaarheid|Percentage|Gemiddeld|ARP-beschikbaarheid van MSEE naar alle peers.|PeeringType, Peer|
|BgpAvailability|Yes|BGP-beschikbaarheid|Percentage|Gemiddeld|BGP-beschikbaarheid van MSEE naar alle peers.|PeeringType, Peer|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure binnenkomen|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure verlaten|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure binnenkomen|PeeredCircuitsKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure verlaten|PeeredCircuitsKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Gemiddeld|Ingress-bits van gegevens die per seconde zijn uitgevallen|Geen dimensies|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Gemiddeld|Uitgevallen gegevensbits per seconde|Geen dimensies|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure binnenkomen|Geen dimensies|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure verlaten|Geen dimensies|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure binnenkomen|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure verlaten|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Aantal routes dat wordt geadverteerd naar peer (preview)|Count|Maximum|Aantal routes geadverteerd naar peer met ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Aantal routes dat is geleerd van peer (preview)|Count|Maximum|Aantal routes dat is geleerd van peer door ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|CPU-gebruik|Count|Gemiddeld|CPU-gebruik van de ExpressRoute-gateway|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Frequentie van routes wijzigen (preview)|Aantal|Totaal|Frequentie van routes wijzigen in ExpressRoute-gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Aantal VM's in de Virtual Network (preview)|Count|Maximum|Aantal VM's in de Virtual Network|Geen dimensies|
|ExpressRouteGatewayPacketsPerSecond|No|Pakketten per seconde|CountPerSecond|Gemiddeld|Aantal pakketten van ExpressRoute-gateway|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Count|Gemiddeld|Beheerderstoestand van de poort|Koppeling|
|LineProtocol|Yes|LineProtocol|Count|Gemiddeld|Status van het regelprotocol van de poort|Koppeling|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure binnenkomen|Koppeling|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Gemiddeld|Bits die per seconde in Azure verlaten|Koppeling|
|RxLightLevel|Yes|RxLightLevel|Count|Gemiddeld|Rx Light-niveau in dBm|Link, Lane|
|TxLightLevel|Yes|TxLightLevel|Count|Gemiddeld|Tx-lichtniveau in dBm|Link, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Percentage back-end-status|Percentage|Gemiddeld|Het percentage geslaagde statustests van de HTTP/S-proxy naar back-enden|Back-end, BackendPool|
|BackendRequestCount|Yes|Aantal back-end-aanvragen|Aantal|Totaal|Het aantal aanvragen dat is verzonden van de HTTP/S-proxy naar back-enden|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Yes|Latentie van back-en-aanvraag|Milliseconden|Gemiddeld|De tijd die wordt berekend op basis van het moment waarop de aanvraag is verzonden door de HTTP/S-proxy naar de back-eind totdat de HTTP/S-proxy de laatste antwoord-byte van de back-end heeft ontvangen|Back-end|
|BillableResponseSize|Yes|Factureerbare antwoordgrootte|Bytes|Totaal|Het aantal factureerbare bytes (minimaal 2 KB per aanvraag) dat als antwoorden van HTTP/S-proxy naar clients wordt verzonden.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Aantal aanvragen|Aantal|Totaal|Het aantal clientaanvragen dat wordt ingediend door de HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Aanvraaggrootte|Bytes|Totaal|Het aantal bytes dat wordt verzonden als aanvragen van clients naar de HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Antwoordgrootte|Bytes|Totaal|Het aantal bytes verzonden als antwoorden van HTTP/S-proxy naar clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Totale latentie|Milliseconden|Gemiddeld|De tijd die is berekend op basis van het moment waarop de clientaanvraag is ontvangen door de HTTP/S-proxy totdat de client de laatste antwoord-byte van de HTTP/S-proxy heeft bevestigd|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall aantal aanvragen|Aantal|Totaal|Het aantal clientaanvragen dat door de Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ToegewezenSnatPorts|No|Toegewezen SNAT-poorten|Count|Gemiddeld|Totaal aantal SNAT-poorten dat binnen een bepaalde periode is toegewezen|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Aantal byte's|Bytes|Totaal|Totaal aantal verzonden bytes binnen een bepaalde periode|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Yes|Status van de statustest|Count|Gemiddeld|Gemiddelde Load Balancer status van de statustest per tijdsduur|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Aantal pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen een bepaalde periode|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Yes|Aantal SNAT-verbindingen|Aantal|Totaal|Totaal aantal nieuwe SNAT-verbindingen dat binnen een bepaalde periode is gemaakt|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|SYN-aantal|Aantal|Totaal|Totaal aantal SYN-pakketten verzonden binnen een bepaalde periode|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|No|Gebruikte SNAT-poorten|Count|Gemiddeld|Totaal aantal SNAT-poorten dat binnen een bepaalde periode wordt gebruikt|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Beschikbaarheid van gegevenspad|Count|Gemiddeld|Gemiddelde Load Balancer beschikbaarheid van gegevenspaden per tijdsduur|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bytes|Bytes|Totaal|Totaal aantal verzonden bytes binnen een bepaalde periode|Protocol, richting|
|DatapathAvailability|Yes|Beschikbaarheid van gegevenspad (preview)|Count|Gemiddeld|NAT Gateway Datapath Availability|Geen dimensies|
|PacketCount|Yes|Pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen een bepaalde periode|Protocol, richting|
|PacketDropCount|Yes|Verwijderde pakketten|Aantal|Totaal|Aantal uitgevallen pakketten|Geen dimensies|
|SNATConnectionCount|Yes|Aantal SNAT-verbindingen|Aantal|Totaal|Totaal aantal gelijktijdige actieve verbindingen|Protocol, ConnectionState|
|TotalConnectionCount|Yes|Totaal aantal SNAT-verbindingen|Aantal|Totaal|Totaal aantal actieve SNAT-verbindingen|Protocol|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Ontvangen bytes|Bytes|Totaal|Het aantal bytes dat de netwerkinterface heeft ontvangen|Geen dimensies|
|BytesSentRate|Yes|Verzonden bytes|Bytes|Totaal|Aantal bytes dat de netwerkinterface heeft verzonden|Geen dimensies|
|PacketsReceivedRate|Yes|Ontvangen pakketten|Aantal|Totaal|Aantal pakketten dat de netwerkinterface heeft ontvangen|Geen dimensies|
|PacketsSentRate|Yes|Verzonden pakketten|Aantal|Totaal|Aantal pakketten dat de netwerkinterface heeft verzonden|Geen dimensies|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Gem. retourtijd (ms) (klassiek)|Milliseconden|Gemiddeld|Gemiddelde retourtijd van het netwerk (ms) voor connectiviteitscontroletests die zijn verzonden tussen bron en doel|Geen dimensies|
|ChecksFailedPercent|Yes|Percentage mislukte controles|Percentage|Gemiddeld|% van connectiviteitscontroles mislukt|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|Aantal tests mislukt (klassiek)|Percentage|Gemiddeld|Het aantal controletests voor connectiviteit is mislukt|Geen dimensies|
|RoundTripTimeMs|Yes|Round-Trip (ms)|Milliseconden|Gemiddeld|Retourtijd in milliseconden voor de connectiviteitscontroles|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Yes|Testresultaat|Count|Gemiddeld|Testresultaat verbindingsmonitor|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Bandbreedte van gateway P2S|BytesPerSecond|Gemiddeld|Gemiddelde punt-naar-site-bandbreedte van een gateway in bytes per seconde|Geen dimensies|
|P2SConnectionCount|Yes|Aantal P2S-verbindingen|Aantal|Totaal|Aantal point-to-site-verbindingen van een gateway|Protocol|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Queryvolume|Aantal|Totaal|Aantal query's dat wordt Privé-DNS zone|Geen dimensies|
|RecordSetCapacityUtilization|No|Capaciteitsgebruik recordset|Percentage|Maximum|Percentage recordsetcapaciteit dat wordt gebruikt door een Privé-DNS zone|Geen dimensies|
|RecordSetCount|Yes|Aantal recordset|Count|Maximum|Aantal recordsets in een Privé-DNS zone|Geen dimensies|
|VirtualNetworkLinkCapacityUtilization|No|Virtual Network capaciteitsgebruik koppelen|Percentage|Maximum|Percentage van Virtual Network Link-capaciteit die wordt gebruikt door een Privé-DNS zone|Geen dimensies|
|VirtualNetworkLinkCount|Yes|Virtual Network aantal koppelingskoppelingen|Count|Maximum|Aantal virtuele netwerken dat is gekoppeld aan Privé-DNS zone|Geen dimensies|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Virtual Network van registratiekoppeling capaciteitsgebruik|Percentage|Maximum|Percentage Virtual Network koppeling met automatische registratiecapaciteit die wordt gebruikt door een Privé-DNS zone|Geen dimensies|
|VirtualNetworkWithRegistrationLinkCount|Yes|Virtual Network registratiekoppelingen|Count|Maximum|Aantal virtuele netwerken dat is gekoppeld aan Privé-DNS zone met automatische registratie ingeschakeld|Geen dimensies|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Bytes In|Aantal|Totaal|Totaal aantal uit bytes|PrivateEndpointId|
|PEBytesOut|Yes|Bytes uit|Aantal|Totaal|Totaal aantal uit bytes|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Bytes In|Aantal|Totaal|Totaal aantal uit bytes|PrivateLinkServiceId|
|PLSBytesOut|Yes|Uit bytes|Aantal|Totaal|Totaal aantal uit bytes|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|Gebruik van Nat-poorten|Percentage|Gemiddeld|Gebruik van Nat-poorten|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Aantal byte's|Bytes|Totaal|Totaal aantal verzonden bytes binnen een bepaalde periode|Poort, Richting|
|BytesDroppedDDos|Yes|Inkomende bytes die zijn uit DDoS gevallen|BytesPerSecond|Maximum|Inkomende bytes die zijn uitgevallen met DDoS|Geen dimensies|
|BytesForwardedDDoS|Yes|Inkomende bytes doorgestuurde DDoS|BytesPerSecond|Maximum|Inkomende bytes doorgestuurde DDoS|Geen dimensies|
|BytesInDDos|Yes|Inkomende bytes DDoS|BytesPerSecond|Maximum|Inkomende bytes DDoS|Geen dimensies|
|DDoSTriggerSYNPackets|Yes|Inkomende SYN-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende SYN-pakketten om DDoS-beperking te activeren|Geen dimensies|
|DDoSTriggerTCPPackets|Yes|Inkomende TCP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende TCP-pakketten om DDoS-beperking te activeren|Geen dimensies|
|DDoSTriggerUDPPackets|Yes|Inkomende UDP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende UDP-pakketten om DDoS-beperking te activeren|Geen dimensies|
|IfUnderDDoSAttack|Yes|Onder DDoS-aanval of niet|Count|Maximum|Onder DDoS-aanval of niet|Geen dimensies|
|PacketCount|Yes|Aantal pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen een bepaalde periode|Poort, Richting|
|PacketsDroppedDDoS|Yes|Inkomende pakketten die zijn uitgevallen bij DDoS|CountPerSecond|Maximum|Inkomende pakketten die zijn uitgevallen bij DDoS|Geen dimensies|
|PakkettenForwardedDDoS|Yes|Inkomende pakketten doorgestuurde DDoS|CountPerSecond|Maximum|Inkomende pakketten doorgestuurde DDoS|Geen dimensies|
|PacketsInDDoS|Yes|Inkomende pakketten DDoS|CountPerSecond|Maximum|Inkomende pakketten DDoS|Geen dimensies|
|SynCount|Yes|SYN-aantal|Aantal|Totaal|Totaal aantal SYN-pakketten verzonden binnen een bepaalde periode|Poort, richting|
|TCPBytesDroppedDDoS|Yes|Inkomende TCP-bytes uitgevallen DDoS|BytesPerSecond|Maximum|Inkomende TCP-bytes uitgevallen DDoS|Geen dimensies|
|TCPBytesForwardedDDoS|Yes|Inkomende TCP-bytes doorgestuurde DDoS|BytesPerSecond|Maximum|Inkomende TCP-bytes doorgestuurde DDoS|Geen dimensies|
|TCPBytesInDDoS|Yes|DDoS voor binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS voor binnenkomende TCP-bytes|Geen dimensies|
|TCPPacketsDroppedDDos|Yes|Inkomende TCP-pakketten die zijn uitgevallen bij DDoS|CountPerSecond|Maximum|Inkomende TCP-pakketten die zijn uitgevallen bij DDoS|Geen dimensies|
|TCPPacketsForwardedDDos|Yes|Inkomende TCP-pakketten doorgestuurde DDoS|CountPerSecond|Maximum|Inkomende TCP-pakketten doorgestuurde DDoS|Geen dimensies|
|TCPPacketsInDDos|Yes|DDoS voor inkomende TCP-pakketten|CountPerSecond|Maximum|DDoS voor inkomende TCP-pakketten|Geen dimensies|
|UDPBytesDroppedDDoS|Yes|Inkomende UDP-bytes die zijn uitgevallen door DDoS|BytesPerSecond|Maximum|Inkomende UDP-bytes die zijn uit DDoS gevallen|Geen dimensies|
|UDPBytesForwardedDDoS|Yes|Inkomende UDP bytes doorgestuurde DDoS|BytesPerSecond|Maximum|Inkomende UDP bytes doorgestuurde DDoS|Geen dimensies|
|UDPBytesInDDoS|Yes|DDoS voor inkomende UDP-bytes|BytesPerSecond|Maximum|DDoS voor inkomende UDP-bytes|Geen dimensies|
|UDPPacketsDroppedDDos|Yes|Inkomende UDP-pakketten die zijn uitgevallen DDoS|CountPerSecond|Maximum|Inkomende UDP-pakketten die zijn uitgevallen door DDoS|Geen dimensies|
|UDPPacketsForwardedDDoS|Yes|Inkomende UDP-pakketten doorgestuurde DDoS|CountPerSecond|Maximum|Inkomende UDP-pakketten doorgestuurde DDoS|Geen dimensies|
|UDPPacketsInDDos|Yes|DDoS voor inkomende UDP-pakketten|CountPerSecond|Maximum|DDoS voor inkomende UDP-pakketten|Geen dimensies|
|VipAvailability|Yes|Beschikbaarheid van gegevenspad|Count|Gemiddeld|Gemiddelde beschikbaarheid van IP-adressen per tijdsduur|Poort|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Eindpuntstatus per eindpunt|Count|Maximum|1 als de teststatus van een eindpunt 'Ingeschakeld' is, 0 anders.|EndpointName|
|QpsByEndpoint|Yes|Query's op eindpunt geretourneerd|Aantal|Totaal|Aantal keren dat Traffic Manager eindpunt is geretourneerd in het opgegeven tijdsbestek|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gateway S2S-bandbreedte|BytesPerSecond|Gemiddeld|Gemiddelde site-naar-site-bandbreedte van een gateway in bytes per seconde|Geen dimensies|
|ExpressRouteGatewayCountOfRoutesAdvertexpressToPeer|Yes|Aantal routes dat naar peer is geadverteerd (preview)|Count|Maximum|Aantal routes dat per ExpressRouteGateway wordt geadverteerd naar peer|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Aantal routes dat is geleerd van peer (preview)|Count|Maximum|Count Of Routes Learned From Peer by ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|CPU-gebruik|Count|Gemiddeld|CPU-gebruik van de ExpressRoute-gateway|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Frequentie van routes wijzigen (preview)|Aantal|Totaal|Frequentie van routes wijzigen in ExpressRoute-gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Aantal VM's in de Virtual Network (preview)|Count|Maximum|Aantal VM's in de Virtual Network|Geen dimensies|
|ExpressRouteGatewayPacketsPerSecond|No|Pakketten per seconde|CountPerSecond|Gemiddeld|Aantal pakketten van ExpressRoute-gateway|roleInstance|
|P2SBandwidth|Yes|P2S-bandbreedte van gateway|BytesPerSecond|Gemiddeld|Gemiddelde punt-naar-site-bandbreedte van een gateway in bytes per seconde|Geen dimensies|
|P2SConnectionCount|Yes|Aantal P2S-verbindingen|Count|Maximum|Aantal point-to-site-verbindingen van een gateway|Protocol|
|TunnelAverageBandwidth|Yes|Tunnelbandbreedte|BytesPerSecond|Gemiddeld|Gemiddelde bandbreedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Uitgaande bytes in tunnel|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDroptSMismatch|Yes|Uitgaande niet-overeenkomende TS-pakketten door tunnel|Aantal|Totaal|Aantal uitgaande niet-overeenkomende pakketten uit traffic selector in tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Uitgaande pakketten in tunnel|Aantal|Totaal|Aantal uitgaande pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Totaal aantal inkomende bytes|Bytes|Totaal|Inkomende bytes in een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkomende niet-overeenkomende TS-pakketten door tunnel|Aantal|Totaal|Aantal inkomende niet-overeenkomende pakketten uit traffic selector in tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Ingresspakketten tunnelen|Aantal|Totaal|Aantal inkomende pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|NAT-toewijzingen tunnelen|Aantal|Totaal|Aantal toewijzingen voor een NAT-regel in een tunnel|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|No|Genateerde bytes tunnelen|Bytes|Totaal|Aantal bytes dat in een tunnel is genateerd door een NAT-regel |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|No|Nageleverde pakketten tunnelen|Aantal|Totaal|Aantal pakketten dat is genateerd in een tunnel door een NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|No|NAT-stromen tunnelen|Aantal|Totaal|Aantal NAT-stromen in een tunnel per stroomtype en NAT-regel|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Nat-pakket uitvalt in tunnel|Aantal|Totaal|Aantal NAT-pakketten in een tunnel dat is uitgevallen op basis van droptype en NAT-regel|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|Omgekeerde NAT-bytes tunnelen|Bytes|Totaal|Aantal bytes dat in een tunnel is omgekeerd door een NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Omgekeerde NAT-pakketten tunnelen|Aantal|Totaal|Aantal pakketten in een tunnel dat is omgekeerd nageteerd door een NAT-regel|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Retourtijd voor pings naar een VM|Milliseconden|Gemiddeld|Retourtijd voor pings verzonden naar een doel-VM|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Mislukte pings naar een VM|Percentage|Gemiddeld|Percentage mislukte pings naar totaal aantal verzonden pings van een doel-VM|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|PeeringBeschikbaarheid|Yes|BGP-beschikbaarheid|Percentage|Gemiddeld|BGP-beschikbaarheid tussen VirtualRouter en externe peers|Peer|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gateway S2S-bandbreedte|BytesPerSecond|Gemiddeld|Gemiddelde site-naar-site-bandbreedte van een gateway in bytes per seconde|Geen dimensies|
|TunnelAverageBandwidth|Yes|Tunnelbandbreedte|BytesPerSecond|Gemiddeld|Gemiddelde bandbreedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Uitgaande bytes in tunnel|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDroptSMismatch|Yes|Uitgaande niet-overeenkomende TS-pakketten door tunnel|Aantal|Totaal|Aantal uitgaande niet-overeenkomende pakketten uit traffic selector in tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Uitgaande pakketten in tunnel|Aantal|Totaal|Aantal uitgaande pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Totaal aantal inkomende bytes|Bytes|Totaal|Inkomende bytes in een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkomende niet-overeenkomende TS-pakketten door tunnel|Aantal|Totaal|Aantal inkomende niet-overeenkomende pakketten uit traffic selector in tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Ingresspakketten tunnelen|Aantal|Totaal|Aantal inkomende pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|NAT-toewijzingen tunnelen|Aantal|Totaal|Aantal toewijzingen voor een NAT-regel in een tunnel|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|No|Genateerde bytes tunnelen|Bytes|Totaal|Aantal bytes dat in een tunnel is genateerd door een NAT-regel |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|No|Nageleverde pakketten tunnelen|Aantal|Totaal|Aantal pakketten dat is geNAT in een tunnel door een NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|No|TUNNEL NAT-stromen|Aantal|Totaal|Aantal NAT-stromen in een tunnel per stroomtype en NAT-regel|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Nat-pakket uitvalt in tunnel|Aantal|Totaal|Aantal NAT-pakketten in een tunnel dat is uitgevallen op basis van het type drop en de NAT-regel|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|Omgekeerde NAT-bytes tunnelen|Bytes|Totaal|Aantal bytes dat in een tunnel is omgekeerd door een NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Omgekeerde NAT-pakketten tunnelen|Aantal|Totaal|Aantal pakketten in een tunnel dat is omgekeerd nageteerd door een NAT-regel|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Inkomende|Yes|Binnenkomende berichten|Aantal|Totaal|Het aantal geslaagde API-aanroepen verzenden. |Geen dimensies|
|incoming.all.failedrequests|Yes|Alle binnenkomende mislukte aanvragen|Aantal|Totaal|Totaal aantal inkomende mislukte aanvragen voor een Notification Hub|Geen dimensies|
|incoming.all.requests|Yes|Alle binnenkomende aanvragen|Aantal|Totaal|Totaal aantal binnenkomende aanvragen voor een Notification Hub|Geen dimensies|
|incoming.scheduled|Yes|Geplande pushmeldingen verzonden|Aantal|Totaal|Geplande pushmeldingen geannuleerd|Geen dimensies|
|incoming.scheduled.cancel|Yes|Geplande pushmeldingen geannuleerd|Aantal|Totaal|Geplande pushmeldingen geannuleerd|Geen dimensies|
|installation.all|Yes|Bewerkingen voor installatiebeheer|Aantal|Totaal|Bewerkingen voor installatiebeheer|Geen dimensies|
|installation.delete|Yes|Installatiebewerkingen verwijderen|Aantal|Totaal|Installatiebewerkingen verwijderen|Geen dimensies|
|installation.get|Yes|Installatiebewerkingen uitvoeren|Aantal|Totaal|Installatiebewerkingen uitvoeren|Geen dimensies|
|installation.patch|Yes|Patchinstallatiebewerkingen|Aantal|Totaal|Patchinstallatiebewerkingen|Geen dimensies|
|installation.upsert|Yes|Installatiebewerkingen maken of bijwerken|Aantal|Totaal|Installatiebewerkingen maken of bijwerken|Geen dimensies|
|notificationhub.pushes|Yes|Alle uitgaande meldingen|Aantal|Totaal|Alle uitgaande meldingen van de Notification Hub|Geen dimensies|
|outgoing.allpns.badorexpiredchannel|Yes|Fouten met een beschadigd of verlopen kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het kanaal/token/registrationId in de registratie is verlopen of ongeldig is.|Geen dimensies|
|outgoing.allpns.channelerror|Yes|Kanaalfouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het kanaal ongeldig is, is niet gekoppeld aan de juiste app die is beperkt of verlopen.|Geen dimensies|
|outgoing.allpns.invalidpayload|Yes|Nettoladingfouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS een fout met een slechte nettolading heeft geretourneerd.|Geen dimensies|
|outgoing.allpns.pnserror|Yes|Externe systeemfouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat er een probleem is met de communicatie met de PNS (geen verificatieproblemen).|Geen dimensies|
|outgoing.allpns.success|Yes|Geslaagde meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|outgoing.apns.badchannel|Yes|APNS-fout met slecht kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het token ongeldig is (APNS-statuscode: 8).|Geen dimensies|
|outgoing.apns.expiredchannel|Yes|APNS Expired Channel-fout|Aantal|Totaal|Het aantal token dat ongeldig is gemaakt door het APNS-feedbackkanaal.|Geen dimensies|
|outgoing.apns.invalidcredentials|Yes|APNS-autorisatiefouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|outgoing.apns.invalidnotificationsize|Yes|FOUT APNS ongeldige meldingsgrootte|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de nettolading te groot is (APNS-statuscode: 7).|Geen dimensies|
|outgoing.apns.pnserror|Yes|APNS-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten bij de communicatie met APNS.|Geen dimensies|
|outgoing.apns.success|Yes|APNS-geslaagde meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|outgoing.gcm.authenticationerror|Yes|GCM-verificatiefouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd, worden de referenties geblokkeerd of de SenderId is niet correct geconfigureerd in de app (GCM-resultaat: MismatchedSenderId).|Geen dimensies|
|outgoing.gcm.badchannel|Yes|Fout met GCM-kanaalfout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registrationId in de registratie niet is herkend (GCM-resultaat: Ongeldige registratie).|Geen dimensies|
|outgoing.gcm.expiredchannel|Yes|Fout met verlopen GCM-kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registrationId in de registratie is verlopen (GCM-resultaat: Niet geregistreerd).|Geen dimensies|
|outgoing.gcm.invalidcredentials|Yes|GCM-autorisatiefouten (ongeldige referenties)|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|outgoing.gcm.invalidnotificationformat|Yes|Ongeldige GCM-meldingsindeling|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload niet correct is opgemaakt (GCM-resultaat: InvalidDataKey of InvalidTtl).|Geen dimensies|
|outgoing.gcm.invalidnotificationsize|Yes|Fout met ongeldige GCM-meldingsgrootte|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de nettolading te groot is (GCM-resultaat: MessageTooBig).|Geen dimensies|
|outgoing.gcm.pnserror|Yes|GCM-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten bij de communicatie met GCM.|Geen dimensies|
|outgoing.gcm.success|Yes|Geslaagde GCM-meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|outgoing.gcm.throttled|Yes|Meldingen met GCM-vertraging|Aantal|Totaal|Het aantal pushes dat is mislukt omdat GCM deze app heeft beperkt (GCM-statuscode: 501-599 of resultaat:Niet beschikbaar).|Geen dimensies|
|outgoing.gcm.wrongchannel|Yes|Fout met verkeerde GCM-kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registrationId in de registratie niet is gekoppeld aan de huidige app (GCM-resultaat: InvalidPackageName).|Geen dimensies|
|outgoing.mpns.authenticationerror|Yes|MPNS-verificatiefouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|outgoing.mpns.badchannel|Yes|Fout met MPNS-kanaalfout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de ChannelURI in de registratie niet is herkend (MPNS-status: 404 niet gevonden).|Geen dimensies|
|outgoing.mpns.channeldisconnected|Yes|Verbinding met MPNS-kanaal verbroken|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de ChannelURI in de registratie is verbroken (MPNS-status: 412 niet gevonden).|Geen dimensies|
|outgoing.mpns.dropped|Yes|MpNS-meldingen die zijn uitgevallen|Aantal|Totaal|Het aantal pushes dat is weggevallen door MPNS (MPNS-antwoordheader: X-NotificationStatus: QueueFull of Suppressed).|Geen dimensies|
|outgoing.mpns.invalidcredentials|Yes|Ongeldige MPNS-referenties|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|outgoing.mpns.invalidnotificationformat|Yes|Ongeldige MPNS-meldingsindeling|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de nettolading van de melding te groot is.|Geen dimensies|
|outgoing.mpns.pnserror|Yes|MPNS-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten bij de communicatie met MPNS.|Geen dimensies|
|outgoing.mpns.success|Yes|MPNS Geslaagde meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|outgoing.mpns.throttled|Yes|MPNS-beperkt meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat MPNS deze app aanvraagt (WNS MPNS: 406 Not Acceptable).|Geen dimensies|
|outgoing.wns.authenticationerror|Yes|WNS-verificatiefouten|Aantal|Totaal|De melding wordt niet bezorgd vanwege fouten die communiceren met ongeldige referenties of een onjuist token in Windows Live.|Geen dimensies|
|outgoing.wns.badchannel|Yes|Fout met WNS-kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de ChannelURI in de registratie niet is herkend (WNS-status: 404 niet gevonden).|Geen dimensies|
|outgoing.wns.channeldisconnected|Yes|Verbinding met WNS-kanaal verbroken|Aantal|Totaal|De melding is verbroken omdat de ChannelURI in de registratie is beperkt (WNS-antwoordheader: X-WNS-DeviceConnectionStatus: disconnected).|Geen dimensies|
|outgoing.wns.channelthrottled|Yes|WNS-kanaal beperkt|Aantal|Totaal|De melding is uitgevallen omdat de ChannelURI in de registratie is beperkt (WNS-antwoordheader: X-WNS-NotificationStatus:channelThrottled).|Geen dimensies|
|outgoing.wns.dropped|Yes|Meldingen over uitgevallen WNS|Aantal|Totaal|De melding is verbroken omdat de ChannelURI in de registratie is beperkt (X-WNS-NotificationStatus: is verbroken, maar niet X-WNS-DeviceConnectionStatus: verbroken).|Geen dimensies|
|outgoing.wns.expiredchannel|Yes|Fout met verlopen WNS-kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de ChannelURI is verlopen (WNS-status: 410 Verdwenen).|Geen dimensies|
|outgoing.wns.invalidcredentials|Yes|WNS-autorisatiefouten (ongeldige referenties)|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd. (Windows Live herkent de referenties niet.|Geen dimensies|
|outgoing.wns.invalidnotificationformat|Yes|Ongeldige WNS-meldingsindeling|Aantal|Totaal|De indeling van de melding is ongeldig (WNS-status: 400). Houd er rekening mee dat WNS niet alle ongeldige nettoladingen weigert.|Geen dimensies|
|outgoing.wns.invalidnotificationsize|Yes|Fout met ongeldige WNS-meldingsgrootte|Aantal|Totaal|De nettolading van de melding is te groot (WNS-status: 413).|Geen dimensies|
|outgoing.wns.invalidtoken|Yes|WNS-autorisatiefouten (ongeldig token)|Aantal|Totaal|Het token dat wordt verstrekt aan WNS is ongeldig (WNS-status: 401 Niet geautoriseerd).|Geen dimensies|
|outgoing.wns.pnserror|Yes|WNS-fouten|Aantal|Totaal|Melding niet bezorgd vanwege fouten die communiceren met WNS.|Geen dimensies|
|outgoing.wns.success|Yes|Geslaagde WNS-meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|outgoing.wns.throttled|Yes|Meldingen met WNS-vertraging|Aantal|Totaal|Het aantal pushes dat is mislukt omdat WNS deze app aanvraagt (WNS-status: 406 Niet acceptabel).|Geen dimensies|
|outgoing.wns.tokenproviderunreachable|Yes|WNS-autorisatiefouten (onbereikbaar)|Aantal|Totaal|Windows Live is niet bereikbaar.|Geen dimensies|
|outgoing.wns.wrongtoken|Yes|WNS-autorisatiefouten (onjuist token)|Aantal|Totaal|Het token dat wordt verstrekt aan WNS is geldig, maar voor een andere toepassing (WNS-status: 403 Verboden). Dit kan gebeuren als de ChannelURI in de registratie is gekoppeld aan een andere app. Controleer of de client-app is gekoppeld aan dezelfde app waarvan de referenties zich in de Notification Hub hebben.|Geen dimensies|
|registration.all|Yes|Registratiebewerkingen|Aantal|Totaal|Het aantal geslaagde registratiebewerkingen (het maken van updates van query's en verwijderingen). |Geen dimensies|
|registration.create|Yes|Bewerkingen voor het maken van registraties|Aantal|Totaal|Het aantal geslaagde registraties.|Geen dimensies|
|registration.delete|Yes|Registratiebewerkingen verwijderen|Aantal|Totaal|Het aantal geslaagde registratie-verwijderingen.|Geen dimensies|
|registration.get|Yes|Leesbewerkingen voor registratie|Aantal|Totaal|Het aantal geslaagde registratiequery's.|Geen dimensies|
|registration.update|Yes|Registratie-updatebewerkingen|Aantal|Totaal|Het aantal geslaagde registratie-updates.|Geen dimensies|
|scheduled.pending|Yes|Geplande meldingen in behandeling|Aantal|Totaal|Geplande meldingen in behandeling|Geen dimensies|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Average_% beschikbaar geheugen|Yes|% beschikbaar geheugen|Count|Gemiddeld|Average_% beschikbaar geheugen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% beschikbare wisselruimte|Yes|% beschikbare wisselruimte|Count|Gemiddeld|Average_% beschikbare wisselruimte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% vastgelegde bytes in gebruik|Yes|% van het aantal vastgelegde bytes in gebruik|Count|Gemiddeld|Average_% vastgelegde bytes in gebruik|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tijd|Yes|% DPC-tijd|Count|Gemiddeld|Average_% DPC-tijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% gratis inodes|Yes|% gratis inodes|Count|Gemiddeld|Average_% gratis inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% vrije ruimte|Yes|% vrije ruimte|Count|Gemiddeld|Average_% vrije ruimte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% niet-actieve tijd|Yes|Percentage niet-actieve tijd|Count|Gemiddeld|Average_% niet-actieve tijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% onderbrekingstijd|Yes|% onderbrekingstijd|Count|Gemiddeld|Average_% onderbrekingstijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO-wachttijd|Yes|% I/O-wachttijd|Count|Gemiddeld|Average_% IO-wachttijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Van tijd|Yes|% Tijd in de tijd|Count|Gemiddeld|Average_% Mooie tijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Yes|% tijd met bevoegdheden|Count|Gemiddeld|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processortijd|Yes|Percentage processortijd|Count|Gemiddeld|Average_% processortijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% gebruikt inodes|Yes|% gebruikte inodes|Count|Gemiddeld|Average_% gebruikt inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% gebruikt geheugen|Yes|% gebruikt geheugen|Count|Gemiddeld|Average_% gebruikt geheugen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% gebruikte ruimte|Yes|% gebruikte ruimte|Count|Gemiddeld|Average_% gebruikte ruimte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% gebruikte wisselruimte|Yes|% gebruikte wisselruimte|Count|Gemiddeld|Average_% gebruikte wisselruimte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% gebruikerstijd|Yes|% gebruikerstijd|Count|Gemiddeld|Average_% gebruikerstijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available mb|Yes|Beschikbare megabytes|Count|Gemiddeld|Average_Available mb|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available mb geheugen|Yes|Beschikbaar geheugen (MB)|Count|Gemiddeld|Average_Available mb geheugen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available mbs wisselen|Yes|Beschikbare mbt wisselen|Count|Gemiddeld|Average_Available mbs wisselen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Gelezen bytes per seconde|Yes|Gem. schijf sec/lezen|Count|Gemiddeld|Average_Avg. Gelezen bytes per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Schijf sec/overdracht|Yes|Gemiddeld aantal schijven per seconde per overdracht|Count|Gemiddeld|Average_Avg. Schijf sec/overdracht|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. voor de fysieke schijf|Yes|Gemiddeld aantal schijven per seconde/schrijven|Count|Gemiddeld|Average_Avg. voor de fysieke schijf|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes ontvangen per seconde|Yes|Ontvangen bytes per seconde|Count|Gemiddeld|Average_Bytes ontvangen per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes verzonden per seconde|Yes|Verzonden bytes per seconde|Count|Gemiddeld|Average_Bytes verzonden per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totaal per seconde|Yes|Totaal aantal bytes per seconde|Count|Gemiddeld|Average_Bytes totaal per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current schijfwachtrijlengte|Yes|Wachtrijlengte huidige schijf|Count|Gemiddeld|Average_Current schijfwachtrijlengte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bytes per seconde lezen|Yes|Gelezen bytes per seconde op schijf|Count|Gemiddeld|Average_Disk bytes per seconde lezen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lees-/sec.|Yes|Lees- en tijdsdes per seconde|Count|Gemiddeld|Average_Disk lees-/sec.|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk overdrachten per seconde|Yes|Schijfoverdrachten per seconde|Count|Gemiddeld|Average_Disk per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bytes per seconde schrijven|Yes|Geschreven bytes per seconde schijf|Count|Gemiddeld|Average_Disk bytes per seconde schrijven|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk per seconde|Yes|Schrijf- en schrijf sec.|Count|Gemiddeld|Average_Disk per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Yes|Gratis megabytes|Count|Gemiddeld|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiek geheugen|Yes|Vrij fysiek geheugen|Count|Gemiddeld|Average_Free fysiek geheugen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free in pagineringsbestanden|Yes|Vrije ruimte in wisselbestanden|Count|Gemiddeld|Average_Free in pagineringsbestanden|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtueel geheugen|Yes|Vrij virtueel geheugen|Count|Gemiddeld|Average_Free virtueel geheugen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical aantal schijf bytes per seconde|Yes|Bytes per seconde logische schijf|Count|Gemiddeld|Average_Logical aantal schijf bytes per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page lees-/sec.|Yes|Pagina's lezen per seconde|Count|Gemiddeld|Average_Page lees-/sec.|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page per seconde|Yes|Pagina-schrijf-/sec.|Count|Gemiddeld|Average_Page per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per seconde|Yes|Pagina's per seconde|Count|Gemiddeld|Average_Pages per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Yes|Pct Privileged Time|Count|Gemiddeld|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct gebruikerstijd|Yes|Pct-gebruikerstijd|Count|Gemiddeld|Average_Pct gebruikerstijd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical aantal schijf bytes per seconde|Yes|Bytes per seconde fysieke schijf|Count|Gemiddeld|Average_Physical aantal schijf bytes per seconde|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Processen|Count|Gemiddeld|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor wachtrijlengte|Yes|Lengte processorwachtrij|Count|Gemiddeld|Average_Processor wachtrijlengte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size opgeslagen in wisselbestanden|Yes|Grootte opgeslagen in pagineringsbestanden|Count|Gemiddeld|Average_Size opgeslagen in wisselbestanden|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Yes|Totaal aantal bytes|Count|Gemiddeld|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ontvangen bytes|Yes|Totaal aantal ontvangen bytes|Count|Gemiddeld|Average_Total ontvangen bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total verzonden bytes|Yes|Totaal verzonden bytes|Count|Gemiddeld|Average_Total verzonden bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total-aanrijdingen|Yes|Totaal aantal aanrijdingen|Count|Gemiddeld|Average_Total-aanrijdingen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ontvangen pakketten|Yes|Totaal aantal ontvangen pakketten|Count|Gemiddeld|Average_Total ontvangen pakketten|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total verzonden pakketten|Yes|Totaal verzonden pakketten|Count|Gemiddeld|Average_Total verzonden pakketten|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx-fouten|Yes|Totaal aantal RX-fouten|Count|Gemiddeld|Average_Total Rx-fouten|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx-fouten|Yes|Totaal aantal TX-fouten|Count|Gemiddeld|Average_Total Tx-fouten|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Uptime|Count|Gemiddeld|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used mbs wisselruimte|Yes|Gebruikte MBytes-wisselruimte|Count|Gemiddeld|Average_Used mbs wisselruimte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used geheugenbytes|Yes|Gebruikte geheugenbytes|Count|Gemiddeld|Average_Used geheugenbytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used geheugenbytes|Yes|Geheugenbytes gebruikt|Count|Gemiddeld|Average_Used geheugenbytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Yes|Gebruikers|Count|Gemiddeld|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual gedeeld geheugen|Yes|Virtueel gedeeld geheugen|Count|Gemiddeld|Average_Virtual gedeeld geheugen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Gebeurtenis|Yes|Gebeurtenis|Count|Gemiddeld|Gebeurtenis|Bron, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Hartslag|Yes|Hartslag|Aantal|Totaal|Hartslag|Computer, OSType, Version, SourceComputerId|
|Bijwerken|Yes|Bijwerken|Count|Gemiddeld|Bijwerken|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Aantal egress-verkeer|BitsPerSecond|Gemiddeld|Snelheid van het verkeer in bits per seconde|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Snelheid van het ingress-verkeer|BitsPerSecond|Gemiddeld|Snelheid van ingressverkeer in bits per seconde|ConnectionId, SessionIp, TrafficClass|
|SessieBeschikbaarheid|Yes|Sessiebeschikbaarheid|Count|Gemiddeld|Beschikbaarheid van de peeringsessie|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Latentie van voorvoegsel|Milliseconden|Gemiddeld|Latentie van mediaan voorvoegsel|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Geheugen (Gen1)|Bytes|Gemiddeld|Geheugen. Bereik van 0-3 GB voor A1, 0-5 GB voor A2, 0-10 GB voor A3, 0-25 GB voor A4, 0-50 GB voor A5 en 0-100 GB voor A6. Alleen ondersteund voor Power BI Embedded resources van de eerste generatie.|Geen dimensies|
|memory_thrashing_metric|Yes|Geheugen thrashing (gegevenssets) (Gen1)|Percentage|Gemiddeld|Gemiddelde geheugenbeperking. Alleen ondersteund voor Power BI Embedded resources van de eerste generatie.|Geen dimensies|
|qpu_high_utilization_metric|Yes|Hoog QPU-gebruik (Gen1)|Aantal|Totaal|Hoog QPU-gebruik in laatste minuut, 1 voor hoog QPU-gebruik, anders 0. Alleen ondersteund voor Power BI Embedded resources van de eerste generatie.|Geen dimensies|
|QueryDuration|Yes|Queryduur (gegevenssets) (Gen1)|Milliseconden|Gemiddeld|DAX-queryduur in laatste interval. Alleen ondersteund voor Power BI Embedded resources van de eerste generatie.|Geen dimensies|
|QueryPoolJobQueueLength|Yes|Wachtrijlengte van querypools (gegevenssets) (Gen1)|Count|Gemiddeld|Het aantal taken in de wachtrij van de querythreadpool. Alleen ondersteund voor Power BI Embedded resources van de eerste generatie.|Geen dimensies|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Scan geannuleerd|Aantal|Totaal|Geeft het aantal geannuleerde scans aan.||
|ScanCompleted|Yes|Scan voltooid|Aantal|Totaal|Geeft aan dat het aantal scans is voltooid.||
|ScanFailed|Yes|Scannen is mislukt|Aantal|Totaal|Geeft aan dat het aantal scans is mislukt.||
|ScanTimeTaken|Yes|Scantijd|Seconden|Totaal|Geeft de totale scantijd in seconden aan.||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/naamruimten

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Aantal|Totaal|Totaal aantal ActiveConnections voor Microsoft.Relay.|EntityName|
|ActiveListeners|No|ActiveListeners|Aantal|Totaal|Totaal aantal ActiveListeners voor Microsoft.Relay.|EntityName|
|BytesTransferred|Yes|BytesTransferred|Bytes|Totaal|Totaal aantal bytesvertaald voor Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Aantal|Totaal|ClientError op ListenerConnections voor Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Aantal|Totaal|ServerError op ListenerConnections voor Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-Success|No|ListenerConnections-Success|Aantal|Totaal|Geslaagde ListenerConnections voor Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Aantal|Totaal|Totaal aantal ListenerConnections voor Microsoft.Relay.|EntityName|
|ListenerDisconnects|No|ListenerDisconnects|Aantal|Totaal|Totaal aantal listenersverbinding voor Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Aantal|Totaal|ClientError op SenderConnections voor Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Aantal|Totaal|ServerError op SenderConnections voor Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|No|SenderConnections-Success|Aantal|Totaal|Geslaagde afzenderVerbindingen voor Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Aantal|Totaal|Totaal aantal aanvragen voor AfzenderVerbindingen voor Microsoft.Relay.|EntityName|
|SenderDisconnects|No|SenderDisconnects|Aantal|Totaal|Totaal aantal afzendersVerbinding voor Microsoft.Relay.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Latentie|Yes|Latentiegegevens voor HTTP-binnenkomende aanvragen|Count|Gemiddeld|Latentiegegevens voor HTTP-binnenkomende aanvragen|Methode, Naamruimte, RequestRegion, ResourceType, Microsoft.SubscriptionId|
|Verkeer|Yes|Verkeer|Count|Gemiddeld|HTTP-verkeer|RequestRegion, StatusCode, StatusCodeClass, ResourceType, Namespace, Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Zoeklatentie|Seconden|Gemiddeld|Gemiddelde zoeklatentie voor de zoekservice|Geen dimensies|
|SearchQueriesPerSecond|Yes|Zoekquery's per seconde|CountPerSecond|Gemiddeld|Zoekquery's per seconde voor de zoekservice|Geen dimensies|
|ThrottledSearchQueriesPercentage|Yes|Percentage beperkt zoekquery's|Percentage|Gemiddeld|Percentage zoekquery's dat is beperkt voor de zoekservice|Geen dimensies|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Aantal|Totaal|Totaal aantal actieve verbindingen voor Microsoft.ServiceBus.||
|ActiveMessages|No|Aantal actieve berichten in een wachtrij/onderwerp.|Count|Gemiddeld|Aantal actieve berichten in een wachtrij/onderwerp.|EntityName|
|VerbindingenSluiten|No|Gesloten verbindingen.|Count|Gemiddeld|Verbindingen gesloten voor Microsoft.ServiceBus.|EntityName|
|VerbindingenOpenen|No|Geopende verbindingen.|Count|Gemiddeld|Verbindingen geopend voor Microsoft.ServiceBus.|EntityName|
|CPUXNS|No|CPU (afgeschaft)|Percentage|Maximum|Metrische gegevens voor CPU-gebruik van Service Bus Premium-naamruimte. Deze metrische gegevens zijn afgeschaft. Gebruik in plaats daarvan de metrische CPU-gegevens (NamespaceCpuUsage).|Replica|
|DeadletteredMessages|No|Aantal berichten met in wachtrij/onderwerp geplaatste berichten in een wachtrij.|Count|Gemiddeld|Aantal berichten met in wachtrij/onderwerp geplaatste berichten in een wachtrij.|EntityName|
|IncomingMessages|Yes|Binnenkomende berichten|Aantal|Totaal|Binnenkomende berichten voor Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Yes|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor Microsoft.ServiceBus.|EntityName|
|Berichten|No|Aantal berichten in een wachtrij/onderwerp.|Count|Gemiddeld|Aantal berichten in een wachtrij/onderwerp.|EntityName|
|NaamruimteCpuUsage|No|CPU|Percentage|Maximum|Metrische gegevens voor CPU-gebruik van Service Bus Premium-naamruimte.|Replica|
|NaamruimteMemoryUsage|No|Geheugengebruik|Percentage|Maximum|Metrische gegevens voor geheugengebruik van Service Bus Premium-naamruimte.|Replica|
|OutgoingMessages|Yes|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor Microsoft.ServiceBus.|EntityName|
|ScheduledMessages|No|Aantal geplande berichten in een wachtrij/onderwerp.|Count|Gemiddeld|Aantal geplande berichten in een wachtrij/onderwerp.|EntityName|
|ServerErrors|No|Serverfouten.|Aantal|Totaal|Serverfouten voor Microsoft.ServiceBus.|EntityName, OperationResult|
|Grootte|No|Grootte|Bytes|Gemiddeld|Grootte van een wachtrij/onderwerp in bytes.|EntityName|
|SuccessfulRequests|No|Geslaagde aanvragen|Aantal|Totaal|Totaal aantal geslaagde aanvragen voor een naamruimte|EntityName, OperationResult|
|ThrottledRequests|No|Beperkte aanvragen.|Aantal|Totaal|Beperkt aantal aanvragen voor Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|No|Gebruikersfouten.|Aantal|Totaal|Gebruikersfouten voor Microsoft.ServiceBus.|EntityName, OperationResult|
|WSXNS|No|Geheugengebruik (afgeschaft)|Percentage|Maximum|Metrische gegevens over geheugengebruik van Service Bus Premium-naamruimte. Deze metrische gegevens zijn afgeschaft. Gebruik in plaats daarvan de metrische gegevens geheugengebruik (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Count|Gemiddeld|Werkelijk CPU-gebruik in kernen|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Bytes|Gemiddeld|Werkelijk geheugengebruik in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ToegewezenCpu|No|ToegewezenCpu|Count|Gemiddeld|Cpu die is toegewezen aan deze container in de kernen van een container|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Toegewezenmemory|No|Toegewezenmemory|Bytes|Gemiddeld|Geheugen toegewezen aan deze container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Count|Gemiddeld|Status van Service Fabric Mesh toepassing|ApplicationName, Status|
|ContainerStatus|No|ContainerStatus|Count|Gemiddeld|Status van de container in Service Fabric Mesh toepassing|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|Cpu-gebruik|No|Cpu-gebruik|Percentage|Gemiddeld|Gebruik van CPU voor deze container als percentage van ToegewezenCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Percentage|Gemiddeld|Gebruik van CPU voor deze container als percentage van ToegewezenCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Count|Gemiddeld|Aantal containers opnieuw opstarten in Service Fabric Mesh toepassing|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Count|Gemiddeld|Status van een servicereplica in Service Fabric Mesh toepassing|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Count|Gemiddeld|Status van een service in Service Fabric Mesh toepassing|ApplicationName, Status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Aantal verbindingen|Count|Maximum|De hoeveelheid gebruikersverbinding.|Eindpunt|
|InboundTraffic|Yes|Binnenkomend verkeer|Bytes|Totaal|Het inkomende verkeer van de service|Geen dimensies|
|MessageCount|Yes|Aantal berichten|Aantal|Totaal|De totale hoeveelheid berichten.|Geen dimensies|
|Uitgaande verkeersverkeer|Yes|Uitgaand verkeer|Bytes|Totaal|Het uitgaande verkeer van de service|Geen dimensies|
|SystemErrors|Yes|Systeemfouten|Percentage|Maximum|Het percentage systeemfouten|Geen dimensies|
|UserErrors|Yes|Gebruikersfouten|Percentage|Maximum|Het percentage gebruikersfouten|Geen dimensies|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|InboundTraffic|Yes|Binnenkomend verkeer|Bytes|Totaal|Het binnenkomende verkeer van de service|Geen dimensies|
|Uitgaande verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|Het uitgaande verkeer van de service|Geen dimensies|
|TotalConnectionCount|Yes|Aantal verbindingen|Count|Maximum|De hoeveelheid gebruikersverbinding.|Geen dimensies|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Gemiddeld CPU-percentage|Percentage|Gemiddeld|Gemiddeld CPU-percentage|Geen dimensies|
|io_bytes_read|Yes|Gelezen I/O-bytes|Bytes|Gemiddeld|Gelezen I/O-bytes|Geen dimensies|
|io_bytes_written|Yes|Geschreven IO-bytes|Bytes|Gemiddeld|Geschreven IO-bytes|Geen dimensies|
|io_requests|Yes|Aantal I/O-aanvragen|Count|Gemiddeld|Aantal I/O-aanvragen|Geen dimensies|
|reserved_storage_mb|Yes|Gereserveerde opslagruimte|Count|Gemiddeld|Gereserveerde opslagruimte|Geen dimensies|
|storage_space_used_mb|Yes|Gebruikte opslagruimte|Count|Gemiddeld|Gebruikte opslagruimte|Geen dimensies|
|virtual_core_count|Yes|Aantal virtuele kernen|Count|Gemiddeld|Aantal virtuele kernen|Geen dimensies|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|active_queries|Yes|Actieve query's|Aantal|Totaal|Actieve query's voor alle workloadgroepen. Alleen van toepassing op datawarehouses.|Geen dimensies|
|allocated_data_storage|Yes|Toegewezen gegevensruimte|Bytes|Gemiddeld|Toegewezen gegevensopslag. Niet van toepassing op datawarehouses.|Geen dimensies|
|app_cpu_billed|Yes|Cpu van app gefactureerd|Aantal|Totaal|Cpu van app gefactureerd. Is van toepassing op serverloze databases.|Geen dimensies|
|app_cpu_percent|Yes|CPU-percentage van app|Percentage|Gemiddeld|CPU-percentage van app. Is van toepassing op serverloze databases.|Geen dimensies|
|app_memory_percent|Yes|Percentage app-geheugen|Percentage|Gemiddeld|Percentage app-geheugen. Is van toepassing op serverloze databases.|Geen dimensies|
|base_blob_size_bytes|Yes|Grootte van basis-blobopslag|Bytes|Maximum|Grootte van basis-blobopslag. Is van toepassing op Hyperscale-databases.|Geen dimensies|
|blocked_by_firewall|Yes|Geblokkeerd door firewall|Aantal|Totaal|Geblokkeerd door firewall|Geen dimensies|
|cache_hit_percent|Yes|Percentage treffers in cache|Percentage|Maximum|Percentage treffers in cache. Is alleen van toepassing op datawarehouses.|Geen dimensies|
|cache_used_percent|Yes|Percentage gebruikt cachegeheugen|Percentage|Maximum|Percentage gebruikt cachegeheugen. Alleen van toepassing op datawarehouses.|Geen dimensies|
|connection_failed|Yes|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|connection_successful|Yes|Geslaagde verbindingen|Aantal|Totaal|Geslaagde verbindingen|Geen dimensies|
|cpu_limit|Yes|CPU-limiet|Count|Gemiddeld|CPU-limiet. Is van toepassing op vCore-databases.|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|cpu_used|Yes|CPU gebruikt|Count|Gemiddeld|CPU gebruikt. Is van toepassing op vCore-databases.|Geen dimensies|
|Impasse|Yes|Impasses|Aantal|Totaal|Deadlocks. Niet van toepassing op datawarehouses.|Geen dimensies|
|diff_backup_size_bytes|Yes|Differentiële back-upopslaggrootte|Bytes|Maximum|Cumulatieve differentiële back-upopslaggrootte. Is van toepassing op vCore-databases. Niet van toepassing op Hyperscale-databases.|Geen dimensies|
|dtu_consumption_percent|Yes|DTU-percentage|Percentage|Gemiddeld|DTU-percentage. Is van toepassing op DTU-databases.|Geen dimensies|
|dtu_limit|Yes|DTU-limiet|Count|Gemiddeld|DTU-limiet. Is van toepassing op DTU-databases.|Geen dimensies|
|dtu_used|Yes|DTU gebruikt|Count|Gemiddeld|DTU gebruikt. Is van toepassing op DTU-databases.|Geen dimensies|
|dwu_consumption_percent|Yes|DWU-percentage|Percentage|Maximum|DWU-percentage. Alleen van toepassing op datawarehouses.|Geen dimensies|
|dwu_limit|Yes|DWU-limiet|Count|Maximum|DWU-limiet. Alleen van toepassing op datawarehouses.|Geen dimensies|
|dwu_used|Yes|DWU gebruikt|Count|Maximum|DWU gebruikt. Is alleen van toepassing op datawarehouses.|Geen dimensies|
|full_backup_size_bytes|Yes|Volledige grootte van back-upopslag|Bytes|Maximum|Cumulatieve opslaggrootte voor volledige back-up. Is van toepassing op vCore-databases. Niet van toepassing op Hyperscale-databases.|Geen dimensies|
|local_tempdb_usage_percent|Yes|Lokaal tempdb-percentage|Percentage|Gemiddeld|Lokaal tempdb-percentage. Is alleen van toepassing op datawarehouses.|Geen dimensies|
|log_backup_size_bytes|Yes|Opslaggrootte logboekback-up|Bytes|Maximum|Cumulatieve opslaggrootte voor logboekback-ups. Is van toepassing op vCore- en Hyperscale-databases.|Geen dimensies|
|log_write_percent|Yes|IO-percentage logboek|Percentage|Gemiddeld|IO-percentage voor logboeken. Niet van toepassing op datawarehouses.|Geen dimensies|
|memory_usage_percent|Yes|Geheugenpercentage|Percentage|Maximum|Geheugenpercentage. Alleen van toepassing op datawarehouses.|Geen dimensies|
|physical_data_read_percent|Yes|Gegevens-I/O-percentage|Percentage|Gemiddeld|Gegevens-I/O-percentage|Geen dimensies|
|queued_queries|Yes|Query's in de wachtrij|Aantal|Totaal|Query's in de wachtrij voor alle workloadgroepen. Alleen van toepassing op datawarehouses.|Geen dimensies|
|sessions_percent|Yes|Percentage sessies|Percentage|Gemiddeld|Percentage sessies. Niet van toepassing op datawarehouses.|Geen dimensies|
|snapshot_backup_size_bytes|Yes|Opslaggrootte voor back-up van momentopname|Bytes|Maximum|Cumulatieve opslaggrootte voor momentopnamen. Is van toepassing op Hyperscale-databases.|Geen dimensies|
|sqlserver_process_core_percent|Yes|SQL Server percentage proceskernen|Percentage|Maximum|CPU-gebruik als een percentage van het SQL DB-proces. Niet van toepassing op datawarehouses.|Geen dimensies|
|sqlserver_process_memory_percent|Yes|SQL Server van het procesgeheugen|Percentage|Maximum|Geheugengebruik als een percentage van het SQL DB-proces. Niet van toepassing op datawarehouses.|Geen dimensies|
|opslag|Yes|Gebruikte gegevensruimte|Bytes|Maximum|Gebruikte gegevensruimte. Niet van toepassing op datawarehouses.|Geen dimensies|
|storage_percent|Yes|Percentage gebruikte gegevensruimte|Percentage|Maximum|Percentage gebruikte gegevensruimte. Niet van toepassing op datawarehouses of hyperscale-databases.|Geen dimensies|
|tempdb_data_size|Yes|Tempdb-gegevensbestandsgrootte kilobytes|Count|Maximum|Ruimte die wordt gebruikt in tempdb-gegevensbestanden in kilobytes. Niet van toepassing op datawarehouses.|Geen dimensies|
|tempdb_log_size|Yes|Tempdb-logboekbestandsgrootte kilobytes|Count|Maximum|Ruimte die wordt gebruikt in het tempdb-transactielogboekbestand in kilobytes. Niet van toepassing op datawarehouses.|Geen dimensies|
|tempdb_log_used_percent|Yes|Tempdb Percent Log Used|Percentage|Maximum|Percentage gebruikte ruimte in tempdb-transactielogboekbestand. Niet van toepassing op datawarehouses.|Geen dimensies|
|wlg_active_queries|Yes|Actieve query's voor workloadgroep|Aantal|Totaal|Actieve query's binnen de werkbelastinggroep. Is alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Time-outs voor query's voor workloadgroep|Aantal|Totaal|Query's die een time-out hebben voor de werkbelastinggroep. Is alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Toewijzing van werkbelastinggroep per systeem percentage|Percentage|Maximum|Toegewezen percentage resources ten opzichte van het hele systeem per werkbelastinggroep. Is alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Toewijzing van werkbelastinggroep per resourcelimiet|Percentage|Maximum|Toegewezen percentage resources ten opzichte van de opgegeven limiet voor resources per werkbelastinggroep. Is alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Effectief limietresource-percentage|Percentage|Maximum|Een harde limiet voor het percentage resources dat is toegestaan voor de werkbelastinggroep, rekening houdend met het effectieve minimumresourcepercentage dat is toegewezen voor andere workloadgroepen. Alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Effectief minimum resource-percentage|Percentage|Maximum|Minimaal percentage resources dat is gereserveerd en geïsoleerd voor de werkbelastinggroep, rekening houdend met het minimale serviceniveau. Alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Query's in de wachtrij voor workloadgroep|Aantal|Totaal|Query's in de wachtrij binnen de werkbelastinggroep. Alleen van toepassing op datawarehouses.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Percentage werkpersoneel|Percentage|Gemiddeld|Percentage werkpersoneel. Niet van toepassing op datawarehouses.|Geen dimensies|
|xtp_storage_percent|Yes|In-Memory OLTP-opslag percentage|Percentage|Gemiddeld|In-Memory OLTP-opslag percentage. Niet van toepassing op datawarehouses.|Geen dimensies|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Toegewezen gegevensruimte|Bytes|Gemiddeld|Toegewezen gegevensruimte|Geen dimensies|
|allocated_data_storage_percent|Yes|Percentage toegewezen gegevensruimte|Percentage|Maximum|Percentage toegewezen gegevensruimte|Geen dimensies|
|cpu_limit|Yes|CPU-limiet|Count|Gemiddeld|CPU-limiet. Is van toepassing op elastische pools op basis van vCore.|Geen dimensies|
|cpu_percent|Yes|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|Geen dimensies|
|cpu_used|Yes|CPU gebruikt|Count|Gemiddeld|CPU gebruikt. Is van toepassing op elastische pools op basis van vCore.|Geen dimensies|
|database_allocated_data_storage|No|Toegewezen gegevensruimte|Bytes|Gemiddeld|Toegewezen gegevensruimte|DatabaseResourceId|
|database_cpu_limit|No|CPU-limiet|Count|Gemiddeld|CPU-limiet|DatabaseResourceId|
|database_cpu_percent|No|CPU-percentage|Percentage|Gemiddeld|CPU-percentage|DatabaseResourceId|
|database_cpu_used|No|CPU gebruikt|Count|Gemiddeld|CPU gebruikt|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU-percentage|Percentage|Gemiddeld|DTU-percentage|DatabaseResourceId|
|database_eDTU_used|No|eDTU gebruikt|Count|Gemiddeld|eDTU gebruikt|DatabaseResourceId|
|database_log_write_percent|No|IO-percentage logboek|Percentage|Gemiddeld|IO-percentage logboek|DatabaseResourceId|
|database_physical_data_read_percent|No|Gegevens-I/O-percentage|Percentage|Gemiddeld|Gegevens-I/O-percentage|DatabaseResourceId|
|database_sessions_percent|No|Percentage sessies|Percentage|Gemiddeld|Percentage sessies|DatabaseResourceId|
|database_storage_used|No|Gebruikte gegevensruimte|Bytes|Gemiddeld|Gebruikte gegevensruimte|DatabaseResourceId|
|database_workers_percent|No|Percentage werkpersoneel|Percentage|Gemiddeld|Percentage werkpersoneel|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU-percentage|Percentage|Gemiddeld|DTU-percentage. Is van toepassing op elastische pools op basis van DTU.|Geen dimensies|
|eDTU_limit|Yes|eDTU-limiet|Count|Gemiddeld|eDTU-limiet. Is van toepassing op elastische pools op basis van DTU.|Geen dimensies|
|eDTU_used|Yes|eDTU gebruikt|Count|Gemiddeld|eDTU gebruikt. Is van toepassing op elastische pools op basis van DTU.|Geen dimensies|
|log_write_percent|Yes|IO-percentage logboek|Percentage|Gemiddeld|IO-percentage logboek|Geen dimensies|
|physical_data_read_percent|Yes|Gegevens-I/O-percentage|Percentage|Gemiddeld|Gegevens-I/O-percentage|Geen dimensies|
|sessions_percent|Yes|Percentage sessies|Percentage|Gemiddeld|Percentage sessies|Geen dimensies|
|sqlserver_process_core_percent|Yes|SQL Server percentage proceskernen|Percentage|Maximum|CPU-gebruik als een percentage van het SQL DB-proces. Is van toepassing op elastische pools.|Geen dimensies|
|sqlserver_process_memory_percent|Yes|SQL Server van het procesgeheugen|Percentage|Maximum|Geheugengebruik als een percentage van het SQL DB-proces. Is van toepassing op elastische pools.|Geen dimensies|
|storage_limit|Yes|Maximale grootte van gegevens|Bytes|Gemiddeld|Maximale grootte van gegevens|Geen dimensies|
|storage_percent|Yes|Percentage gebruikte gegevensruimte|Percentage|Gemiddeld|Percentage gebruikte gegevensruimte|Geen dimensies|
|storage_used|Yes|Gebruikte gegevensruimte|Bytes|Gemiddeld|Gebruikte gegevensruimte|Geen dimensies|
|tempdb_data_size|Yes|Tempdb-gegevensbestandsgrootte kilobytes|Count|Maximum|Ruimte die wordt gebruikt in tempdb-gegevensbestanden in kilobytes.|Geen dimensies|
|tempdb_log_size|Yes|Tempdb-logboekbestandsgrootte kilobytes|Count|Maximum|Ruimte die wordt gebruikt in het tempdb-transactielogboekbestand in kilobytes.|Geen dimensies|
|tempdb_log_used_percent|Yes|Tempdb Percent Log Used|Percentage|Maximum|Percentage gebruikte ruimte in tempdb-transactielogboekbestand|Geen dimensies|
|workers_percent|Yes|Percentage werkpersoneel|Percentage|Gemiddeld|Percentage werkpersoneel|Geen dimensies|
|xtp_storage_percent|Yes|In-Memory OLTP-opslag percentage|Percentage|Gemiddeld|In-Memory OLTP-opslag percentage|Geen dimensies|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens. Dit aantal omvat zowel het uit- als Azure Storage van de externe client in Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Yes|Gebruikte capaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door het opslagaccount. Voor standaardopslagaccounts is dit de som van de capaciteit die wordt gebruikt door blob, table, file en queue. Voor Premium Storage-accounts en Blob Storage-accounts is dit hetzelfde als BlobCapacity of FileCapacity.|Geen dimensies|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|
|BlobCapacity|No|Blobcapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de opslagaccounts Blob service in bytes.|BlobType, Laag|
|BlobCount|No|Aantal blobs|Count|Gemiddeld|Het aantal blobobjecten dat is opgeslagen in het opslagaccount.|BlobType, Laag|
|BlobProvisionedSize|No|In de blob inrichtende grootte|Bytes|Gemiddeld|De hoeveelheid opslag die is ingericht in de opslagaccounts Blob service in bytes.|BlobType, Laag|
|ContainerCount|Yes|Aantal blobcontainers|Count|Gemiddeld|Het aantal containers in het opslagaccount.|Geen dimensies|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens. Dit aantal omvat zowel het uit- als Azure Storage van de externe client in Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|IndexCapacity|No|Indexcapaciteit|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door Azure Data Lake Storage Gen2 hiërarchische index.|Geen dimensies|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De gemiddelde end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, verificatie|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, verificatie|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication, FileShare|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens. Dit aantal omvat zowel het uit- als het Azure Storage van de externe client. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie, FileShare|
|FileCapacity|No|Bestandscapaciteit|Bytes|Gemiddeld|De hoeveelheid bestandsopslag die wordt gebruikt door het opslagaccount.|Bestandsshare|
|FileCount|No|Aantal bestanden|Count|Gemiddeld|Het aantal bestanden in het opslagaccount.|Bestandsshare|
|FileShareCapacityQuota|No|Capaciteitsquotum bestandsdeel|Bytes|Gemiddeld|De bovengrens voor de hoeveelheid opslag die kan worden gebruikt door Azure Files Service in bytes.|Bestandsshare|
|FileShareCount|No|Aantal bestandsdeels|Count|Gemiddeld|Het aantal bestands shares in het opslagaccount.|Geen dimensies|
|FileShareProvisionedIOPS|No|In een bestands share inrichtende IOPS|Bytes|Gemiddeld|Het basislijnnummer van de inrichtende IOPS voor de Premium-bestands share in het opslagaccount voor Premium-bestanden. Dit aantal wordt berekend op basis van de ingerichte grootte (quotum) van de sharecapaciteit.|Bestandsshare|
|FileShareSnapshotCount|No|Aantal momentopnamen van bestands share|Count|Gemiddeld|Het aantal momentopnamen dat aanwezig is op de share in de Files Service van het opslagaccount.|Bestandsshare|
|FileShareSnapshotSize|No|Grootte van momentopname van bestands share|Bytes|Gemiddeld|De hoeveelheid opslag die wordt gebruikt door de momentopnamen in de bestandsservice van het opslagaccount in bytes.|Bestandsshare|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De gemiddelde end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie, FileShare|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie, FileShare|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, verificatie|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens. Dit nummer omvat zowel het uit- als het Azure Storage van de externe client. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, verificatie|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|QueueCapacity|Yes|Wachtrijcapaciteit|Bytes|Gemiddeld|De hoeveelheid Queue Storage die wordt gebruikt door het opslagaccount.|Geen dimensies|
|QueueCount|Yes|Aantal wachtrijen|Count|Gemiddeld|Het aantal wachtrijen in het opslagaccount.|Geen dimensies|
|QueueMessageCount|Yes|Aantal wachtrij-berichten|Count|Gemiddeld|Het aantal niet-verkende wachtrijberichten in het opslagaccount.|Geen dimensies|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Beschikbaarheid|Yes|Beschikbaarheid|Percentage|Gemiddeld|Het beschikbaarheidspercentage voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, verificatie|
|Uitgaand verkeer|Yes|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens. Dit nummer omvat zowel het uit- als het Azure Storage van de externe client. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|Inkomend verkeer|Yes|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingressgegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, verificatie|
|SuccessE2ELatency|Yes|Success E2E Latency|Milliseconden|Gemiddeld|De gemiddelde end-to-endlatentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|SuccessServerLatency|Yes|Geslaagde serverlatentie|Milliseconden|Gemiddeld|De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie|
|TableCapacity|Yes|Tabelcapaciteit|Bytes|Gemiddeld|De hoeveelheid Table Storage die wordt gebruikt door het opslagaccount.|Geen dimensies|
|TableCount|Yes|Aantal tabel|Count|Gemiddeld|Het aantal tabellen in het opslagaccount.|Geen dimensies|
|TableEntityCount|Yes|Aantal tabelentiteiten|Count|Gemiddeld|Het aantal tabelentiteiten in het opslagaccount.|Geen dimensies|
|Transacties|Yes|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen antwoorden.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Totaal aantal client-IOPS|Count|Gemiddeld|De snelheid van clientbestandsbewerkingen die door de cache worden verwerkt.|Geen dimensies|
|ClientLatency|Yes|Gemiddelde clientlatentie|Milliseconden|Gemiddeld|Gemiddelde latentie van clientbestandsbewerkingen naar de cache.|Geen dimensies|
|ClientLockIOPS|Yes|IOPS voor clientvergrendeling|CountPerSecond|Gemiddeld|Vergrendelingsbewerkingen voor clientbestand per seconde.|Geen dimensies|
|ClientMetadataReadIOPS|Yes|Lees-IOPS voor clientmetagegevens|CountPerSecond|Gemiddeld|De snelheid van clientbestandsbewerkingen die naar de cache worden verzonden, met uitzondering van leesbewerkingen van gegevens, die de permanente status niet wijzigen.|Geen dimensies|
|ClientMetadataWriteIOPS|Yes|IOPS voor schrijven van clientmetagegevens|CountPerSecond|Gemiddeld|De snelheid van clientbestandsbewerkingen die naar de cache worden verzonden, met uitzondering van schrijfbewerkingen van gegevens, die de permanente status wijzigen.|Geen dimensies|
|ClientReadIOPS|Yes|Lees-IOPS voor client|CountPerSecond|Gemiddeld|Leesbewerkingen van client per seconde.|Geen dimensies|
|ClientReadThroughput|Yes|Gemiddelde leesdoorvoer in cache|BytesPerSecond|Gemiddeld|Leessnelheid van gegevensoverdracht door client.|Geen dimensies|
|ClientWriteIOPS|Yes|IOPS voor client schrijven|CountPerSecond|Gemiddeld|Schrijfbewerkingen per seconde voor de client.|Geen dimensies|
|ClientWriteThroughput|Yes|Gemiddelde schrijfdoorvoer van cache|BytesPerSecond|Gemiddeld|Schrijfsnelheid van gegevensoverdracht door client.|Geen dimensies|
|StorageTargetAsyncWriteThroughput|Yes|StorageTarget Asynchronous Write Throughput|BytesPerSecond|Gemiddeld|De snelheid die de Cache asynchroon schrijft gegevens naar een bepaald StorageTarget. Dit zijn opportunistische schrijf- die clients niet blokkeren.|StorageTarget|
|StorageTargetFillThroughput|Yes|Doorvoer opslagdoel opvullen|BytesPerSecond|Gemiddeld|De snelheid die de cache gebruikt om gegevens uit StorageTarget te lezen voor het afhandelen van een cache-misser.|StorageTarget|
|StorageTargetHealth|Yes|Status van opslagdoel|Count|Gemiddeld|Booleaanse resultaten van de connectiviteitstest tussen de cache- en opslagdoelen.|Geen dimensies|
|StorageTargetIOPS|Yes|Totaal aantal opslagdoel-IOPS|Count|Gemiddeld|De snelheid van alle bestandsbewerkingen die de cache naar een bepaald StorageTarget verzendt.|StorageTarget|
|StorageTargetLatency|Yes|StorageTarget-latentie|Milliseconden|Gemiddeld|De gemiddelde retourlatentie van alle bestandsbewerkingen die de cache verzendt naar een partriculair StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|StorageTarget Metadata Read IOPS|CountPerSecond|Gemiddeld|De snelheid van bestandsbewerkingen die de permanente status niet wijzigen, en met uitzondering van de leesbewerking, die de cache naar een bepaald StorageTarget verzendt.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|StorageTarget Metadata Write IOPS|CountPerSecond|Gemiddeld|De snelheid van bestandsbewerkingen die de permanente status wijzigen en de schrijfbewerking uitsluiten, die de cache naar een bepaald StorageTarget verzendt.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|StorageTarget Read Ahead Throughput|BytesPerSecond|Gemiddeld|De snelheid die de cache op een opportunistische manier gebruikt om gegevens uit het StorageTarget te lezen.|StorageTarget|
|StorageTargetReadIOPS|Yes|StorageTarget Read IOPS|CountPerSecond|Gemiddeld|De snelheid van de leesbewerkingen van bestanden die de cache naar een bepaald StorageTarget verzendt.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|OpslagDoel synchrone schrijfdoorvoer|BytesPerSecond|Gemiddeld|De snelheid die de cache synchroon schrijft gegevens naar een bepaald StorageTarget. Dit zijn schrijf schrijft die ervoor zorgen dat clients blokkeren.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|StorageTarget Total Read Throughput|BytesPerSecond|Gemiddeld|De totale snelheid die door de cache wordt gebruikt om gegevens uit een bepaald StorageTarget te lezen.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|StorageTarget Total Write Throughput|BytesPerSecond|Gemiddeld|De totale snelheid die de cache gegevens schrijft naar een bepaald StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|StorageTarget Write IOPS|Count|Gemiddeld|De snelheid van de schrijfbewerkingen voor bestanden die de cache naar een bepaald StorageTarget verzendt.|StorageTarget|
|Uptime|Yes|Uptime|Count|Gemiddeld|Booleaanse resultaten van de connectiviteitstest tussen het cache- en bewakingssysteem.|Geen dimensies|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Resultaat synchronisatiesessie|Count|Gemiddeld|Metrische waarde die een waarde van 1 registreert telkens als het server-eindpunt een synchronisatiesessie met het cloud-eindpunt voltooit|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestandsgrootte overgedragen voor synchronisatiesessies|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecallComputedSuccessRate|Yes|Slagingspercentage voor in cloudlagen terughalen|Percentage|Gemiddeld|Percentage van alle geslaagde terugroepen|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Yes|In cloudlagen terughalen grootte per toepassing|Bytes|Totaal|Grootte van gegevens die door de toepassing zijn ingeroepen|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Terugroepgrootte van cloudopslaglagen|Bytes|Totaal|Grootte van de gegevens die zijn ingeroepen|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Terughalen van cloudopslaglagen|Bytes|Totaal|Totale grootte van gegevens die door de server zijn ingeroepen|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Terugroepdoorvoer in cloudlagen|BytesPerSecond|Gemiddeld|Grootte van de doorvoer voor het terughalen van gegevens|SyncGroupName, ServerName|
|StorageSyncServerBeat|Yes|Onlinestatus van server|Count|Maximum|Metriek die elke keer een waarde van 1 registreert als de opnieuw gemigreerde server een heartbeat registreert met het cloud-eindpunt|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Bestanden worden niet gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ServerBeat|Yes|Onlinestatus van server|Count|Maximum|Metriek die een waarde van 1 registreert telkens als de opnieuw gemigreerde server een heartbeat registreert met het cloud-eindpunt|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Terughalen van cloudopslaglagen|Bytes|Totaal|Totale grootte van gegevens die door de server zijn ingeroepen|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestandsgrootte overgedragen voor synchronisatiesessies|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Bestanden worden niet gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestandsgrootte overgedragen voor synchronisatiesessies|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Bestanden worden niet gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Mislukte functieaanvragen|Aantal|Totaal|Mislukte functieaanvragen|LogicalName, PartitionId|
|AMLCalloutInputEvents|Yes|Functiegebeurtenissen|Aantal|Totaal|Functiegebeurtenissen|LogicalName, PartitionId|
|AMLCalloutRequests|Yes|Functieaanvragen|Aantal|Totaal|Functieaanvragen|LogicalName, PartitionId|
|ConversionErrors|Yes|Fouten bij gegevensconversie|Aantal|Totaal|Fouten bij gegevensconversie|LogicalName, PartitionId|
|DeserializationError|Yes|Invoerdeserialisatiefouten|Aantal|Totaal|Invoerdeserialisatiefouten|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Yes|Gebeurtenissen die niet op volgorde zijn|Aantal|Totaal|Gebeurtenissen die niet op volgorde zijn|LogicalName, PartitionId|
|EarlyInputEvents|Yes|Vroege invoergebeurtenissen|Aantal|Totaal|Vroege invoergebeurtenissen|LogicalName, PartitionId|
|Fouten|Yes|Runtimefouten|Aantal|Totaal|Runtimefouten|LogicalName, PartitionId|
|InputEventBytes|Yes|Invoergebeurtenis-bytes|Bytes|Totaal|Invoergebeurtenis-bytes|LogicalName, PartitionId|
|InputEvents|Yes|Invoergebeurtenissen|Aantal|Totaal|Invoergebeurtenissen|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Yes|Teruggeslagen invoergebeurtenissen|Count|Maximum|Teruggeslagen invoergebeurtenissen|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Yes|Ontvangen invoerbronnen|Aantal|Totaal|Ontvangen invoerbronnen|LogicalName, PartitionId|
|LateInputEvents|Yes|Gebeurtenissen voor late invoer|Aantal|Totaal|Gebeurtenissen voor late invoer|LogicalName, PartitionId|
|OutputEvents|Yes|Uitvoergebeurtenissen|Aantal|Totaal|Uitvoergebeurtenissen|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Watermerkvertraging|Seconden|Maximum|Watermerkvertraging|LogicalName, PartitionId|
|ProcessCPUUsagePercentage|Yes|CPU-gebruik (preview)|Percentage|Maximum|CPU-gebruik (preview)|LogicalName, PartitionId|
|Resource-utilization|Yes|Su % gebruik|Percentage|Maximum|Su % gebruik|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces
|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Verwerkte gegevens (bytes)|Bytes|Totaal|Hoeveelheid gegevens verwerkt door query's|Geen dimensies|
|BuiltinSqlPoolLoginAttempts|No|Aanmeldingspogingen|Aantal|Totaal|Aantal aanmeldingspogingen dat is geslaagd of mislukt|Resultaat|
|BuiltinSqlPoolRequestsEnded|No|Aanvragen beëindigd|Aantal|Totaal|Aantal aanvragen dat is geslaagd, mislukt of geannuleerd|Resultaat|
|IntegrationActivityRunsEnded|No|Activiteits uitgevoerd beëindigd|Aantal|Totaal|Aantal integratieactiviteiten dat is geslaagd, mislukt of geannuleerd|Result, FailureType, Activity, ActivityType, Pipeline|
|IntegrationPipelineRunsEnded|No|Pijplijn-runs beëindigd|Aantal|Totaal|Aantal integratiepijplijn-runs dat is geslaagd, mislukt of geannuleerd|Resultaat, FailureType, Pijplijn|
|IntegrationTriggerRunsEnded|No|Trigger-runs beëindigd|Aantal|Totaal|Aantal integratietriggers dat is geslaagd, mislukt of geannuleerd|Resultaat, FailureType, Trigger|
|SQLStreamingBackloggedInputEventSources|No|Vastgelegde invoergebeurtenissen (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Het aantal bronnen van invoergebeurtenissen dat wordt geregistreerd.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingConversionErrors|No|Fouten bij gegevensconversie (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Het aantal uitvoergebeurtenissen dat niet kan worden geconverteerd naar het verwachte uitvoerschema. Foutbeleid kan worden gewijzigd in 'Neerzetten' om gebeurtenissen te laten vallen die in dit scenario voorkomen.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingDeserializationError|No|Invoerdeserialisatiefouten (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Het aantal invoergebeurtenissen dat niet kan worden gedeserialiseerd.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingEarlyInputEvents|No|Vroege invoergebeurtenissen (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Aantal invoergebeurtenissen welke toepassingstijd in een vroeg stadium wordt beschouwd in vergelijking met de aankomsttijd, volgens het beleid voor vroege aankomst.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventBytes|No|Bytes invoergebeurtenis (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Hoeveelheid gegevens ontvangen door de streaming-taak, in bytes. Dit kan worden gebruikt om te controleren of er gebeurtenissen naar de invoerbron worden verzonden.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEvents|No|Invoergebeurtenissen (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Aantal invoergebeurtenissen.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventsSourcesPerSecond|No|Ontvangen invoerbronnen (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Aantal bronnen van invoergebeurtenissen per seconde.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingLateInputEvents|No|Late invoergebeurtenissen (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Aantal invoergebeurtenissen waarbij de toepassingstijd wordt beschouwd als te laat in vergelijking met de aankomsttijd, volgens het beleid voor te late aankomst.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutOfOrderEvents|No|Gebeurtenissen die niet in de volgorde van de bestelling zijn (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Het aantal Event Hub-gebeurtenissen (geseraliseerde berichten) dat is ontvangen door de Event Hub-invoeradapter, dat buiten de volgorde is ontvangen die zijn uitgevallen of een aangepaste tijdstempel hebben gekregen, op basis van het beleid voor het orden van gebeurtenissen.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputEvents|No|Uitvoergebeurtenissen (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Aantal uitvoergebeurtenissen.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputWatermarkDelaySeconds|No|Watermerkvertraging (preview)|Count|Maximum|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Uitvoer watermerk vertraging in seconden.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingResourceUtilization|No|Resourcegebruik (preview)|Percentage|Maximum|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west.
 Resourcegebruik uitgedrukt als een percentage. Hoog gebruik geeft aan dat de taak bijna het maximum aantal toegewezen resources gebruikt.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingRuntimeErrors|No|Runtimefouten (preview)|Aantal|Totaal|Dit is een voorbeeld van metrische gegevens die beschikbaar zijn in VS - oost, Europa - west. Totaal aantal fouten met betrekking tot queryverwerking (exclusief fouten die zijn gevonden tijdens het opnemen van gebeurtenissen of het uitvoeren van resultaten).|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|toegewezen vCores|Count|Maximum|Toegewezen vCores voor een Apache Spark pool|SubmitterId|
|BigDataPoolAllocatedMemory|No|Toegewezen geheugen (GB)|Count|Maximum|Toegewezen geheugen voor Apach Spark-pool (GB)|SubmitterId|
|BigDataPoolApplicationsActive|No|Actieve Apache Spark toepassingen|Count|Maximum|Totaal aantal actieve Apache Spark pooltoepassingen|JobState|
|BigDataPoolApplicationsEnded|No|Beëindigde Apache Spark toepassingen|Aantal|Totaal|Aantal toepassingen Apache Spark pool is beëindigd|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Actieve query's|Aantal|Totaal|De actieve query's. Als u deze niet-gefilterde en niet-gefilterde metrische gegevens gebruikt, worden alle actieve query's weergegeven die op het systeem worden uitgevoerd|IsUserDefined|
|AdaptiveCacheHitPercent|No|Percentage treffers in adaptieve cache|Percentage|Maximum|Meet hoe goed workloads gebruikmaken van de adaptieve cache. Gebruik deze metrische waarde met het metrische percentage treffers in de cache om te bepalen of u wilt schalen voor extra capaciteit of workloads opnieuw wilt uitvoeren om de cache te schalen|Geen dimensies|
|AdaptiveCacheUsedPercent|No|Percentage gebruikt voor adaptieve cache|Percentage|Maximum|Meet hoe goed workloads gebruikmaken van de adaptieve cache. Gebruik deze metrische waarde met de metrische percentages die in de cache zijn gebruikt om te bepalen of u wilt schalen voor extra capaciteit of werkbelastingen opnieuw wilt uitvoeren om de cache te schalen|Geen dimensies|
|Verbindingen|Yes|Verbindingen|Aantal|Totaal|Aantal totaal aantal aanmeldingen voor de SQL-pool|Resultaat|
|ConnectionsBlockedByFirewall|No|Verbindingen geblokkeerd door firewall|Aantal|Totaal|Aantal verbindingen dat is geblokkeerd door firewallregels. Bekijk het toegangsbeheerbeleid voor uw SQL-pool opnieuw en controleer deze verbindingen als het aantal hoog is|Geen dimensies|
|CPUPercent|No|Percentage cpu-gebruik|Percentage|Maximum|CPU-gebruik voor alle knooppunten in de SQL-pool|Geen dimensies|
|DWULimit|No|DWU-limiet|Count|Maximum|Serviceniveaudoelstelling van de SQL-pool|Geen dimensies|
|DWUUsed|No|DWU gebruikt|Count|Maximum|Vertegenwoordigt een weergave op hoog niveau van het gebruik in de SQL-pool. Gemeten op dwu-limiet * DWU-percentage|Geen dimensies|
|DWUUsedPercent|No|Percentage gebruikt DWU|Percentage|Maximum|Vertegenwoordigt een weergave op hoog niveau van het gebruik in de SQL-pool. Gemeten door het maximum te nemen tussen het CPU-percentage en het gegevens-I/O-percentage|Geen dimensies|
|LocalTempDBUsedPercent|No|Lokaal tempdb-percentage gebruikt|Percentage|Maximum|Lokaal tempdb-gebruik voor alle rekenknooppunten: waarden worden elke vijf minuten uitgezonden|Geen dimensies|
|MemoryUsedPercent|No|Percentage gebruikt geheugen|Percentage|Maximum|Geheugengebruik voor alle knooppunten in de SQL-pool|Geen dimensies|
|QueuedQueries|No|Query's in de wachtrij|Aantal|Totaal|Cumulatief aantal aanvragen in de wachtrij nadat de maximale gelijktijdigheidslimiet is bereikt|IsUserDefined|
|WLGActiveQueries|No|Actieve query's voor workloadgroep|Aantal|Totaal|De actieve query's binnen de werkbelastinggroep. Als u deze niet-gefilterde en niet-gefilterde metrische gegevens gebruikt, worden alle actieve query's weergegeven die op het systeem worden uitgevoerd|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Time-outs voor query's voor workloadgroep|Aantal|Totaal|Query's voor de workloadgroep die een time-out hebben gehad. Query-time-outs die door deze metrische gegevens worden gerapporteerd, zijn alleen nadat de query is gestart met uitvoeren (dit omvat geen wachttijd vanwege vergrendeling of resource-wachttijden)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Toewijzing van workloadgroep op maximaal resource-percentage|Percentage|Maximum|Geeft het toewijzingspercentage weer van resources ten opzichte van de effectieve limiet voor het resourcepercentage per werkbelastinggroep. Deze metrische gegevens bieden het effectieve gebruik van de werkbelastinggroep|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Toewijzing van werkbelastinggroep per systeem percentage|Percentage|Maximum|Het toewijzingspercentage van resources ten opzichte van het hele systeem|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Effectief limietresource-percentage|Percentage|Maximum|Het effectieve limietresource-percentage voor de werkbelastinggroep. Als er andere workloadgroepen zijn met min_percentage_resource > 0, wordt effective_cap_percentage_resource evenredig verlaagd|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Effectief minimum resource-percentage|Percentage|Maximum|De instelling voor het effectieve minimumresourcepercentage is toegestaan, rekening houden met het serviceniveau en de instellingen van de werkbelastinggroep. De effectieve min_percentage_resource kunnen hoger worden aangepast op lagere serviceniveaus|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Query's in de wachtrij voor workloadgroep|Aantal|Totaal|Cumulatief aantal aanvragen in de wachtrij nadat de maximale gelijktijdigheidslimiet is bereikt|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Ontvangen bytes voor ingress|Bytes|Totaal|Aantal gelezen bytes uit alle gebeurtenisbronnen|Geen dimensies|
|IngressReceivedInvalidMessages|Yes|Ingress heeft ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten dat is gelezen uit alle gebeurtenisbronnen van Event Hub of IoT Hub|Geen dimensies|
|IngressReceivedMessages|Yes|Ontvangen berichten voor ingress|Aantal|Totaal|Aantal berichten dat is gelezen uit alle gebeurtenisbronnen van Event Hub of IoT Hub|Geen dimensies|
|IngressReceivedMessagesCountLag|Yes|Vertraging aantal ontvangen berichten voor ingress|Count|Gemiddeld|Verschil tussen het volgnummer van het laatste enqueued-bericht in de gebeurtenisbronpartitie en het aantal berichten dat wordt verwerkt bij ingress|Geen dimensies|
|IngressReceivedMessagesTimeLag|Yes|Vertraging in ontvangen berichten voor ingress|Seconden|Maximum|Verschil tussen het moment dat het bericht in de gebeurtenisbron in de enqueu wordt opgevraagd en de tijd dat het wordt verwerkt bij ingress|Geen dimensies|
|IngressStoredBytes|Yes|Ingress Stored Bytes|Bytes|Totaal|Totale grootte van gebeurtenissen die zijn verwerkt en beschikbaar zijn voor query|Geen dimensies|
|IngressStoredEvents|Yes|Opgeslagen gebeurtenissen voor ingress|Aantal|Totaal|Aantal platgegroeide gebeurtenissen dat is verwerkt en beschikbaar is voor query|Geen dimensies|
|WarmStorageMaxProperties|Yes|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor S1/S2 SKU en het maximum aantal eigenschappen dat is toegestaan door Warm Store voor PAYG SKU|Geen dimensies|
|WarmStorageUsedProperties|Yes|Gebruikte eigenschappen voor warme opslag |Count|Maximum|Aantal eigenschappen dat door de omgeving wordt gebruikt voor S1/S2 SKU en aantal eigenschappen dat wordt gebruikt door Warm Store voor PAYG SKU|Geen dimensies|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Ontvangen bytes voor ingress|Bytes|Totaal|Aantal gelezen bytes uit de gebeurtenisbron|Geen dimensies|
|IngressReceivedInvalidMessages|Yes|Ingress heeft ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten dat uit de gebeurtenisbron is gelezen|Geen dimensies|
|IngressReceivedMessages|Yes|Ontvangen berichten voor ingress|Aantal|Totaal|Aantal berichten dat uit de gebeurtenisbron is gelezen|Geen dimensies|
|IngressReceivedMessagesCountLag|Yes|Vertraging aantal ontvangen berichten voor ingress|Count|Gemiddeld|Verschil tussen het volgnummer van het laatste enqueued-bericht in de gebeurtenisbronpartitie en het aantal berichten dat wordt verwerkt bij ingress|Geen dimensies|
|IngressReceivedMessagesTimeLag|Yes|Vertraging in ontvangen berichten voor ingress|Seconden|Maximum|Verschil tussen het moment dat het bericht in de gebeurtenisbron in de enqueu wordt opgevraagd en de tijd dat het wordt verwerkt bij ingress|Geen dimensies|
|IngressStoredBytes|Yes|Ingress Stored Bytes|Bytes|Totaal|Totale grootte van gebeurtenissen die zijn verwerkt en beschikbaar zijn voor query|Geen dimensies|
|IngressStoredEvents|Yes|Opgeslagen gebeurtenissen voor ingress|Aantal|Totaal|Aantal platgegroeide gebeurtenissen dat is verwerkt en beschikbaar is voor query|Geen dimensies|
|WarmStorageMaxProperties|Yes|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor S1/S2 SKU en het maximum aantal eigenschappen dat is toegestaan door Warm Store voor PAYG SKU|Geen dimensies|
|WarmStorageUsedProperties|Yes|Gebruikte eigenschappen voor warme opslag |Count|Maximum|Aantal eigenschappen dat door de omgeving wordt gebruikt voor S1/S2 SKU en aantal eigenschappen dat wordt gebruikt door Warm Store voor PAYG SKU|Geen dimensies|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|Gelezen bytes op de schijf|Yes|Gelezen bytes op de schijf|Bytes|Totaal|Totale schijfdoorvoer vanwege leesbewerkingen gedurende de voorbeeldperiode.|Geen dimensies|
|Leesbewerkingen op de schijf/seconde|Yes|Leesbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|Het gemiddelde aantal I/O-leesbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen een variabele grootte kunnen hebben.|Geen dimensies|
|Geschreven bytes op de schijf|Yes|Geschreven bytes op de schijf|Bytes|Totaal|Totale schijfdoorvoer als gevolg van schrijfbewerkingen gedurende de voorbeeldperiode.|Geen dimensies|
|Schrijfbewerkingen op de schijf/seconde|Yes|Schrijfbewerkingen op de schijf/seconde|CountPerSecond|Gemiddeld|Het gemiddelde aantal I/O-schrijfbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen een variabele grootte kunnen hebben.|Geen dimensies|
|DiskReadBytesPerSecond|Yes|Gelezen bytes per seconde op schijf|BytesPerSecond|Gemiddeld|Gemiddelde schijfdoorvoer vanwege leesbewerkingen gedurende de voorbeeldperiode.|Geen dimensies|
|DiskReadLatency|Yes|Leeslatentie schijf|Milliseconden|Gemiddeld|Totale leeslatentie. De som van de leeslatentie van het apparaat en de kernel.|Geen dimensies|
|DiskReadOperations|Yes|Leesbewerkingen voor schijven|Aantal|Totaal|Het aantal I/O-leesbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen een variabele grootte kunnen hebben.|Geen dimensies|
|DiskWriteBytesPerSecond|Yes|Geschreven bytes per seconde schijf|BytesPerSecond|Gemiddeld|Gemiddelde schijfdoorvoer vanwege schrijfbewerkingen gedurende de voorbeeldperiode.|Geen dimensies|
|DiskWriteLatency|Yes|Schrijflatentie op schijf|Milliseconden|Gemiddeld|Totale schrijflatentie. De som van de schrijflatentie van het apparaat en de kernel.|Geen dimensies|
|DiskWriteOperations|Yes|Schrijfbewerkingen voor schijven|Aantal|Totaal|Het aantal I/O-schrijfbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen een variabele grootte kunnen hebben.|Geen dimensies|
|MemoryActive|Yes|Geheugen actief|Bytes|Gemiddeld|De hoeveelheid geheugen die de VM in de afgelopen tijd heeft gebruikt. Dit is het 'waar'-aantal hoeveel geheugen de VM momenteel nodig heeft. Bovendien kan ongebruikt geheugen worden verwisseld of op een ballon worden uitgevoerd zonder dat dit van invloed is op de prestaties van de gast.|Geen dimensies|
|MemoryGranted|Yes|Geheugen verleend|Bytes|Gemiddeld|De hoeveelheid geheugen die door de host aan de VM is verleend. Geheugen wordt pas aan de host verleend als het één keer is verwisseld en geheugen kan worden verwisseld of als de VMkernel het geheugen nodig heeft.|Geen dimensies|
|MemoryUsed|Yes|Geheugen gebruikt|Bytes|Gemiddeld|De hoeveelheid machinegeheugen die wordt gebruikt door de virtuele machine.|Geen dimensies|
|Netwerk in|Yes|Netwerk in|Bytes|Totaal|Totale netwerkdoorvoer voor ontvangen verkeer.|Geen dimensies|
|Netwerk uit|Yes|Netwerk uit|Bytes|Totaal|Totale netwerkdoorvoer voor verzonden verkeer.|Geen dimensies|
|NetworkInBytesPerSecond|Yes|Netwerk in bytes per seconde|BytesPerSecond|Gemiddeld|Gemiddelde netwerkdoorvoer voor ontvangen verkeer.|Geen dimensies|
|NetworkOutBytesPerSecond|Yes|Bytes/sec. netwerk|BytesPerSecond|Gemiddeld|Gemiddelde netwerkdoorvoer voor verzonden verkeer.|Geen dimensies|
|Percentage CPU|Yes|Percentage CPU|Percentage|Gemiddeld|Het CPU-gebruik. Deze waarde wordt gerapporteerd met 100% voor alle processorkernen op het systeem. Een voorbeeld: een tweeweg-VM die 50% van een systeem met vier kernen gebruikt, maakt volledig gebruik van twee kernen.|Geen dimensies|
|PercentageCpuReady|Yes|Percentage CPU gereed|Milliseconden|Totaal|De tijd die gereed is, is de tijd die nodig is om te wachten tot cpu(s) beschikbaar zijn in het afgelopen update-interval.|Geen dimensies|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Actieve aanvragen (afgeschaft)|Aantal|Totaal|Actieve aanvragen|Exemplaar|
|AverageResponseTime|Yes|Gemiddelde reactietijd (afgeschaft)|Seconden|Gemiddeld|De gemiddelde tijd die de front-end nodig heeft om aanvragen te verwerken, in seconden.|Exemplaar|
|BytesReceived|Yes|Gegevens in|Bytes|Totaal|De gemiddelde binnenkomende bandbreedte die wordt gebruikt voor alle front-ends, in MiB.|Exemplaar|
|BytesSent|Yes|Gegevens uit|Bytes|Totaal|De gemiddelde binnenkomende bandbreedte die wordt gebruikt voor alle front-end, in MiB.|Exemplaar|
|CpuPercentage|Yes|CPU-percentage|Percentage|Gemiddeld|De gemiddelde CPU die wordt gebruikt voor alle exemplaren van de front-end.|Exemplaar|
|DiskQueueLength|Yes|Lengte van de schijfwachtrij|Count|Gemiddeld|Het gemiddelde aantal lees- en schrijfaanvragen dat in de wachtrij voor opslag is geplaatst. Een wachtrijlengte met hoge schijf is een indicatie van een app die mogelijk wordt vertraagd vanwege overmatige schijf-I/O.|Exemplaar|
|Http101|Yes|HTTP 101|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode 101.|Exemplaar|
|Http2xx|Yes|Http 2xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 200 maar < 300.|Exemplaar|
|Http3xx|Yes|Http 3xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 300 maar < 400.|Exemplaar|
|Http401|Yes|HTTP 401|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 401-statuscode.|Exemplaar|
|Http403|Yes|HTTP 403|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 403-statuscode.|Exemplaar|
|Http404|Yes|HTTP 404|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 404-statuscode.|Exemplaar|
|Http406|Yes|HTTP 406|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 406-statuscode.|Exemplaar|
|Http4xx|Yes|Http 4xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 400 maar < 500.|Exemplaar|
|Http5xx|Yes|HTTP-serverfouten|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 500 maar < 600.|Exemplaar|
|HttpQueueLength|Yes|Lengte http-wachtrij|Count|Gemiddeld|Het gemiddelde aantal HTTP-aanvragen dat in de wachtrij moest worden geplaatst voordat ze werden voltooid. Een hoge of toenemende HTTP-wachtrijlengte is een symptoom van een plan dat zwaar wordt belast.|Exemplaar|
|HttpResponseTime|Yes|Reactietijd|Seconden|Gemiddeld|De tijd die de front-end nodig heeft om aanvragen te verwerken, in seconden.|Exemplaar|
|LargeAppServicePlanInstances|Yes|Large App Service Plan Workers|Count|Gemiddeld|Large App Service Plan Workers|Geen dimensies|
|MediumAppServicePlanInstances|Yes|Gemiddelde App Service Plan Workers|Count|Gemiddeld|Gemiddelde App Service Plan Workers|Geen dimensies|
|MemoryPercentage|Yes|Geheugenpercentage|Percentage|Gemiddeld|Het gemiddelde geheugen dat wordt gebruikt voor alle exemplaren van de front-end.|Exemplaar|
|Aanvragen|Yes|Aanvragen|Aantal|Totaal|Het totale aantal aanvragen, ongeacht de resulterende HTTP-statuscode.|Exemplaar|
|SmallAppServicePlanInstances|Yes|Kleine App Service Plan Workers|Count|Gemiddeld|Kleine App Service Plan Workers|Geen dimensies|
|TotalFrontEnds|Yes|Totaal aantal front-ends|Count|Gemiddeld|Totaal aantal front-ends|Geen dimensies|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|CPU-percentage|Percentage|Gemiddeld|De gemiddelde CPU die wordt gebruikt voor alle exemplaren van de werkgroep.|Exemplaar|
|MemoryPercentage|Yes|Geheugenpercentage|Percentage|Gemiddeld|Het gemiddelde geheugen dat wordt gebruikt voor alle exemplaren van de werkgroep.|Exemplaar|
|WorkersAvailable|Yes|Beschikbare werksters|Count|Gemiddeld|Beschikbare werksters|Geen dimensies|
|WorkersTotal|Yes|Totaal aantal werknemers|Count|Gemiddeld|Totaal aantal werknemers|Geen dimensies|
|WorkersUsed|Yes|Gebruikte werksters|Count|Gemiddeld|Gebruikte werksters|Geen dimensies|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Gegevens in|Bytes|Totaal|De gemiddelde binnenkomende bandbreedte die wordt gebruikt voor alle exemplaren van het plan.|Exemplaar|
|BytesSent|Yes|Gegevens uit|Bytes|Totaal|De gemiddelde uitgaande bandbreedte die wordt gebruikt voor alle exemplaren van het plan.|Exemplaar|
|CpuPercentage|Yes|CPU-percentage|Percentage|Gemiddeld|De gemiddelde CPU die wordt gebruikt voor alle exemplaren van het plan.|Exemplaar|
|DiskQueueLength|Yes|Lengte van de schijfwachtrij|Count|Gemiddeld|Het gemiddelde aantal lees- en schrijfaanvragen dat in de wachtrij voor opslag is geplaatst. Een hoge wachtrijlengte voor schijven is een indicatie van een app die mogelijk wordt vertraagd vanwege overmatige schijf-I/O.|Exemplaar|
|HttpQueueLength|Yes|Lengte http-wachtrij|Count|Gemiddeld|Het gemiddelde aantal HTTP-aanvragen dat in de wachtrij moest worden geplaatst voordat ze werden voltooid. Een hoge of toenemende HTTP-wachtrijlengte is een symptoom van een plan dat zwaar wordt belast.|Exemplaar|
|MemoryPercentage|Yes|Geheugenpercentage|Percentage|Gemiddeld|Het gemiddelde geheugen dat wordt gebruikt voor alle exemplaren van het plan.|Exemplaar|
|SocketInboundAll|Yes|SocketInboundAll|Count|Gemiddeld|SocketInboundAll|Exemplaar|
|SocketLoopback|Yes|SocketLoopback|Count|Gemiddeld|SocketLoopback|Exemplaar|
|SocketOutboundAll|Yes|SocketOutboundAll|Count|Gemiddeld|SocketOutboundAll|Exemplaar|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Count|Gemiddeld|SocketOutboundEstablished|Exemplaar|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Count|Gemiddeld|SocketOutboundTimeWait|Exemplaar|
|TcpCloseWait|Yes|Wachten op TCP-sluiten|Count|Gemiddeld|Wachten op TCP-sluiten|Exemplaar|
|TcpClosing|Yes|TCP-afsluiting|Count|Gemiddeld|TCP-afsluiting|Exemplaar|
|TcpEstablished|Yes|TCP tot stand gebracht|Count|Gemiddeld|TCP tot stand gebracht|Exemplaar|
|TcpFinWait1|Yes|TCP Fin Wacht 1|Count|Gemiddeld|TCP Fin Wacht 1|Exemplaar|
|TcpFinWait2|Yes|TCP Fin Wacht 2|Count|Gemiddeld|TCP Fin Wacht 2|Exemplaar|
|TcpLastAck|Yes|TCP Last Ack|Count|Gemiddeld|TCP Last Ack|Exemplaar|
|TcpSynReceived|Yes|TCP Syn ontvangen|Count|Gemiddeld|TCP Syn ontvangen|Exemplaar|
|TcpSynSent|Yes|TCP Syn verzonden|Count|Gemiddeld|TCP Syn verzonden|Exemplaar|
|TcpTimeWait|Yes|TCP-tijd wachten|Count|Gemiddeld|TCP-tijd wachten|Exemplaar|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Verbindingen|Count|Gemiddeld|Het aantal begrensde sockets dat in de sandbox bestaat (w3wp.exe en de onderliggende processen). Een gebonden socket wordt gemaakt door bind()/connect() API's aan te roepen en blijft totdat deze socket wordt gesloten met CloseHandle()/closesocket().|Exemplaar|
|AverageMemoryWorkingSet|Yes|Gemiddelde geheugen-werkset|Bytes|Gemiddeld|De gemiddelde hoeveelheid geheugen die door de app wordt gebruikt, in megabytes (MiB).|Exemplaar|
|AverageResponseTime|Yes|Gemiddelde reactietijd (afgeschaft)|Seconden|Gemiddeld|De gemiddelde tijd die de app nodig heeft om aanvragen te verwerken, in seconden.|Exemplaar|
|BytesReceived|Yes|Gegevens in|Bytes|Totaal|De hoeveelheid binnenkomende bandbreedte die door de app wordt gebruikt, in MiB.|Exemplaar|
|BytesSent|Yes|Gegevens uit|Bytes|Totaal|De hoeveelheid uitgaande bandbreedte die door de app wordt gebruikt, in MiB.|Exemplaar|
|CpuTime|Yes|CPU-tijd|Seconden|Totaal|De hoeveelheid CPU die door de app wordt verbruikt, in seconden. Voor meer informatie over deze metrische gegevens. Niet van toepassing op Azure Functions. Zie https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-tijd versus CPU-percentage).|Exemplaar|
|CurrentAssemblies|Yes|Huidige assemblies|Count|Gemiddeld|Het huidige aantal assemblies dat is geladen in alle AppDomains in deze toepassing.|Exemplaar|
|FileSystemUsage|Yes|Bestandssysteemgebruik|Bytes|Gemiddeld|Percentage van het quotum van het bestandssysteem dat door de app wordt verbruikt.|Geen dimensies|
|FunctionExecutionCount|Yes|Aantal uitvoeringen van functies|Aantal|Totaal|Aantal uitvoeringen van functies. Alleen aanwezig voor Azure Functions.|Exemplaar|
|FunctionExecutionUnits|Yes|Functie-uitvoeringseenheden|Aantal|Totaal|Functie-uitvoeringseenheden. Alleen aanwezig voor Azure Functions.|Exemplaar|
|Gen0Collections|Yes|Gen 0-garbageverzamelingen|Aantal|Totaal|Het aantal keren dat de objecten van de 0-generatie garbage worden verzameld sinds het begin van het app-proces. GC's van de hogere generatie bevatten alle GC's van de lagere generatie.|Exemplaar|
|Gen1Collections|Yes|Gen 1-garbageverzamelingen|Aantal|Totaal|Het aantal keren dat de objecten van de eerste generatie worden garbagecontainers verzameld sinds het begin van het app-proces. GC's van de hogere generatie bevatten alle GC's van de lagere generatie.|Exemplaar|
|Gen2Collections|Yes|Gen 2 Garbage Collections|Aantal|Totaal|Het aantal keren dat de objecten van de tweede generatie garbage worden verzameld sinds het begin van het app-proces.|Exemplaar|
|Behandelt|Yes|Aantal ingangen|Count|Gemiddeld|Het totale aantal grepen dat momenteel door het app-proces wordt geopend.|Exemplaar|
|HealthCheckStatus|Yes|Status van de statuscontrole|Count|Gemiddeld|Status van de statuscontrole|Exemplaar|
|Http101|Yes|HTTP 101|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode 101.|Exemplaar|
|Http2xx|Yes|HTTP 2xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 200 maar < 300.|Exemplaar|
|Http3xx|Yes|HTTP 3xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 300 maar < 400.|Exemplaar|
|Http401|Yes|HTTP 401|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 401-statuscode.|Exemplaar|
|Http403|Yes|HTTP 403|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 403-statuscode.|Exemplaar|
|Http404|Yes|HTTP 404|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 404-statuscode.|Exemplaar|
|Http406|Yes|HTTP 406|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 406-statuscode.|Exemplaar|
|Http4xx|Yes|Http 4xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 400 maar < 500.|Exemplaar|
|Http5xx|Yes|HTTP-serverfouten|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 500 maar < 600.|Exemplaar|
|HttpResponseTime|Yes|Reactietijd|Seconden|Gemiddeld|De tijd die de app nodig heeft om aanvragen te verwerken, in seconden.|Exemplaar|
|IoOtherBytesPerSecond|Yes|I/O andere bytes per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces bytes uit geeft aan I/O-bewerkingen die geen gegevens bevatten, zoals besturingsbewerkingen.|Exemplaar|
|IoOtherOperationsPerSecond|Yes|IO andere bewerkingen per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces I/O-bewerkingen uit geeft die geen lees- of schrijfbewerkingen zijn.|Exemplaar|
|IoReadBytesPerSecond|Yes|IO-bytes per seconde lezen|BytesPerSecond|Totaal|De snelheid waarmee het app-proces bytes leest uit I/O-bewerkingen.|Exemplaar|
|IoReadOperationsPerSecond|Yes|IO-leesbewerkingen per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces lees-I/O-bewerkingen uit geeft.|Exemplaar|
|IoWriteBytesPerSecond|Yes|IO-schrijf bytes per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces bytes schrijft naar I/O-bewerkingen.|Exemplaar|
|IoWriteOperationsPerSecond|Yes|IO-schrijfbewerkingen per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces schrijf-I/O-bewerkingen uit geeft.|Exemplaar|
|MemoryWorkingSet|Yes|Geheugen-werkset|Bytes|Gemiddeld|De huidige hoeveelheid geheugen die door de app wordt gebruikt, in MiB.|Exemplaar|
|PrivateBytes|Yes|Persoonlijke bytes|Bytes|Gemiddeld|Persoonlijke bytes is de huidige grootte, in bytes, van het geheugen dat door het app-proces is toegewezen en dat niet kan worden gedeeld met andere processen.|Exemplaar|
|Aanvragen|Yes|Aanvragen|Aantal|Totaal|Het totale aantal aanvragen, ongeacht de resulterende HTTP-statuscode.|Exemplaar|
|RequestsInApplicationQueue|Yes|Aanvragen in de toepassingswachtrij|Count|Gemiddeld|Het aantal aanvragen in de wachtrij voor de toepassingsaanvraag.|Exemplaar|
|Threads|Yes|Aantal threads|Count|Gemiddeld|Het aantal threads dat momenteel actief is in het app-proces.|Exemplaar|
|TotalAppDomains|Yes|Totaal aantal app-domeinen|Count|Gemiddeld|Het huidige aantal AppDomains dat in deze toepassing is geladen.|Exemplaar|
|TotalAppDomainsUnloaded|Yes|Totaal aantal verwijderde app-domeinen|Count|Gemiddeld|Het totale aantal AppDomains dat is verwijderd sinds het begin van de toepassing.|Exemplaar|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Verbindingen|Count|Gemiddeld|Het aantal gebonden sockets dat in de sandbox bestaat (w3wp.exe en de onderliggende processen). Er wordt een gebonden socket gemaakt door bind()/connect() API's aan te roepen en blijft totdat deze socket wordt gesloten met CloseHandle()/closesocket().|Exemplaar|
|AverageMemoryWorkingSet|Yes|Gemiddelde geheugen-werkset|Bytes|Gemiddeld|De gemiddelde hoeveelheid geheugen die door de app wordt gebruikt, in megabytes (MiB).|Exemplaar|
|AverageResponseTime|Yes|Gemiddelde reactietijd (afgeschaft)|Seconden|Gemiddeld|De gemiddelde tijd die de app nodig heeft om aanvragen te verwerken, in seconden.|Exemplaar|
|BytesReceived|Yes|Gegevens in|Bytes|Totaal|De hoeveelheid binnenkomende bandbreedte die door de app wordt gebruikt, in MiB.|Exemplaar|
|BytesSent|Yes|Gegevens uit|Bytes|Totaal|De hoeveelheid uitgaande bandbreedte die door de app wordt gebruikt, in MiB.|Exemplaar|
|CpuTime|Yes|CPU-tijd|Seconden|Totaal|De hoeveelheid CPU die door de app wordt verbruikt, in seconden. Voor meer informatie over deze metrische gegevens. Niet van toepassing op Azure Functions. Zie https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-tijd versus CPU-percentage).|Exemplaar|
|CurrentAssemblies|Yes|Huidige assemblies|Count|Gemiddeld|Het huidige aantal assemblies dat in alle AppDomains in deze toepassing is geladen.|Exemplaar|
|FileSystemUsage|Yes|Bestandssysteemgebruik|Bytes|Gemiddeld|Percentage van het quotum van het bestandssysteem dat door de app wordt verbruikt.|Geen dimensies|
|FunctionExecutionCount|Yes|Aantal uitvoeringen van functies|Aantal|Totaal|Aantal uitvoeringen van functies|Exemplaar|
|FunctionExecutionUnits|Yes|Functie-uitvoeringseenheden|Aantal|Totaal|Functie-uitvoeringseenheden|Exemplaar|
|Gen0Collections|Yes|Gen 0-garbageverzamelingen|Aantal|Totaal|Het aantal keren dat de objecten van de 0-generatie garbage worden verzameld sinds het begin van het app-proces. GC's van de hogere generatie bevatten alle GC's van de lagere generatie.|Exemplaar|
|Gen1Collections|Yes|Gen 1 Garbage Collections|Aantal|Totaal|Het aantal keren dat de objecten van de eerste generatie garbage worden verzameld sinds het begin van het app-proces. GC's van de hogere generatie bevatten alle GC's van de lagere generatie.|Exemplaar|
|Gen2Collections|Yes|Gen 2 Garbage Collections|Aantal|Totaal|Het aantal keren dat de objecten van de tweede generatie garbage worden verzameld sinds het begin van het app-proces.|Exemplaar|
|Behandelt|Yes|Aantal ingangen|Count|Gemiddeld|Het totale aantal grepen dat momenteel door het app-proces wordt geopend.|Exemplaar|
|HealthCheckStatus|Yes|Status van de statuscontrole|Count|Gemiddeld|Status van de statuscontrole|Exemplaar|
|Http101|Yes|HTTP 101|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode 101.|Exemplaar|
|Http2xx|Yes|HTTP 2xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 200 maar < 300.|Exemplaar|
|Http3xx|Yes|Http 3xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 300 maar < 400.|Exemplaar|
|Http401|Yes|HTTP 401|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 401-statuscode.|Exemplaar|
|Http403|Yes|HTTP 403|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 403-statuscode.|Exemplaar|
|Http404|Yes|HTTP 404|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 404-statuscode.|Exemplaar|
|Http406|Yes|HTTP 406|Aantal|Totaal|Het aantal aanvragen dat resulteert in HTTP 406-statuscode.|Exemplaar|
|Http4xx|Yes|Http 4xx|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 400 maar < 500.|Exemplaar|
|Http5xx|Yes|HTTP-serverfouten|Aantal|Totaal|Het aantal aanvragen dat resulteert in een HTTP-statuscode = 500 maar < 600.|Exemplaar|
|HttpResponseTime|Yes|Reactietijd|Seconden|Gemiddeld|De tijd die de app nodig heeft om aanvragen te verwerken, in seconden.|Exemplaar|
|IoOtherBytesPerSecond|Yes|IO andere bytes per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces bytes uit geeft aan I/O-bewerkingen die geen gegevens bevatten, zoals besturingsbewerkingen.|Exemplaar|
|IoOtherOperationsPerSecond|Yes|IO andere bewerkingen per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces I/O-bewerkingen uit geeft die geen lees- of schrijfbewerkingen zijn.|Exemplaar|
|IoReadBytesPerSecond|Yes|IO-bytes per seconde lezen|BytesPerSecond|Totaal|De snelheid waarmee het app-proces bytes leest uit I/O-bewerkingen.|Exemplaar|
|IoReadOperationsPerSecond|Yes|IO-leesbewerkingen per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces lees-I/O-bewerkingen uit geeft.|Exemplaar|
|IoWriteBytesPerSecond|Yes|IO-bytes per seconde schrijven|BytesPerSecond|Totaal|De snelheid waarmee het app-proces bytes schrijft naar I/O-bewerkingen.|Exemplaar|
|IoWriteOperationsPerSecond|Yes|IO-schrijfbewerkingen per seconde|BytesPerSecond|Totaal|De snelheid waarmee het app-proces schrijf-I/O-bewerkingen uit geeft.|Exemplaar|
|MemoryWorkingSet|Yes|Geheugen-werkset|Bytes|Gemiddeld|De huidige hoeveelheid geheugen die door de app wordt gebruikt, in MiB.|Exemplaar|
|PrivateBytes|Yes|Persoonlijke bytes|Bytes|Gemiddeld|Persoonlijke bytes is de huidige grootte, in bytes, van het geheugen dat door het app-proces is toegewezen en dat niet kan worden gedeeld met andere processen.|Exemplaar|
|Aanvragen|Yes|Aanvragen|Aantal|Totaal|Het totale aantal aanvragen, ongeacht de resulterende HTTP-statuscode.|Exemplaar|
|RequestsInApplicationQueue|Yes|Aanvragen in de toepassingswachtrij|Count|Gemiddeld|Het aantal aanvragen in de wachtrij voor de toepassingsaanvraag.|Exemplaar|
|Threads|Yes|Aantal threads|Count|Gemiddeld|Het aantal threads dat momenteel actief is in het app-proces.|Exemplaar|
|TotalAppDomains|Yes|Totaal aantal app-domeinen|Count|Gemiddeld|Het huidige aantal AppDomains dat in deze toepassing is geladen.|Exemplaar|
|TotalAppDomainsUnloaded|Yes|Totaal aantal verwijderde app-domeinen|Count|Gemiddeld|Het totale aantal AppDomains dat is verwijderd sinds het begin van de toepassing.|Exemplaar|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|Metrisch|Exporteerbaar via diagnostische instellingen?|Weergavenaam van metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|---|
|BytesSent|Yes|Gegevens uit|Bytes|Totaal|BytesSent|Exemplaar|
|FunctionErrors|Yes|FunctionErrors|Aantal|Totaal|FunctionErrors|Exemplaar|
|FunctionHits|Yes|FunctionHits|Aantal|Totaal|FunctionHits|Exemplaar|
|SiteErrors|Yes|SiteErrors|Aantal|Totaal|SiteErrors|Exemplaar|
|SiteHits|Yes|SiteHits|Aantal|Totaal|SiteHits|Exemplaar|

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over metrische gegevens in Azure Monitor](../data-platform.md)
- [Waarschuwingen maken op basis van metrische gegevens](../alerts/alerts-overview.md)
- [Metrische gegevens exporteren naar opslag, Event Hub of Log Analytics](../essentials/platform-logs-overview.md)
