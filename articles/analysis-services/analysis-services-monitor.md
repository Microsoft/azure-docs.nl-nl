---
title: Metrische gegevens van Azure Analysis Services server bewaken | Microsoft Docs
description: Meer informatie over hoe Analysis Services Azure Metrics Explorer gebruikt, een gratis hulp programma in de portal, waarmee u de prestaties en de status van uw servers kunt bewaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1cc517ac3c903930eddb95a4813a8146cae2ec2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582669"
---
# <a name="monitor-server-metrics"></a>Metrische servergegevens bewaken

Analysis Services biedt metrische gegevens in azure Metrics Explorer, een gratis hulp programma in de portal, waarmee u de prestaties en de status van uw servers kunt bewaken. Bewaak bijvoorbeeld het geheugen-en CPU-gebruik, het aantal client verbindingen en het Resource verbruik van de query. Analysis Services gebruikt hetzelfde bewakings raamwerk als de meeste andere Azure-Services. Zie aan de slag [met Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md)voor meer informatie.

Als u meer uitgebreide diagnostische gegevens wilt uitvoeren, de prestaties wilt bijhouden en trends wilt identificeren voor meerdere service resources in een resource groep of abonnement, gebruikt u [Azure monitor](../azure-monitor/overview.md). Azure Monitor (Service) kan resulteren in een factureer bare service.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Meet gegevens voor een Analysis Services server bewaken

1. Selecteer in Azure Portal **metrische gegevens**.

    ![Bewaken in Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Selecteer bij **metrische gegevens** de metrische gegevens die u in uw grafiek wilt gebruiken. 

    ![Bewaak diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metrische Server gegevens

Gebruik deze tabel om te bepalen welke metrische gegevens het meest geschikt zijn voor uw bewakings scenario. In hetzelfde diagram kunnen alleen metrische gegevens van dezelfde eenheid worden weer gegeven.

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Wachtrij lengte van de opdracht pool taak|Count|Gemiddeld|Aantal taken in de wachtrij van de opdracht thread pool.|
|CurrentConnections|Verbinding: huidige verbindingen|Count|Gemiddeld|Het huidige aantal client verbindingen dat tot stand is gebracht.|
|CurrentUserSessions|Huidige gebruikers sessies|Count|Gemiddeld|Het huidige aantal gebruikers sessies dat tot stand is gebracht.|
|mashup_engine_memory_metric|M-engine geheugen|Bytes|Gemiddeld|Geheugen gebruik door mashup-engine processen|
|mashup_engine_qpu_metric|M-engine QPU|Count|Gemiddeld|QPU-gebruik door mashup-engine processen|
|memory_metric|Geheugen|Bytes|Gemiddeld|Geheugen. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|
|memory_thrashing_metric|Geheugenthrashing|Percentage|Gemiddeld|Gemiddeld geheugen overbelasting.|
|CleanerCurrentPrice|Geheugen: huidige prijs opschonen|Count|Gemiddeld|Huidige prijs van geheugen, $/byte/tijd), genormaliseerd naar 1000.|
|CleanerMemoryNonshrinkable|Geheugen: Removal-geheugen kan niet worden verkleind|Bytes|Gemiddeld|Hoeveelheid geheugen (in bytes) die niet wordt verwijderd door de achtergrond opschoning.|
|CleanerMemoryShrinkable|Geheugen: verkleinbaar geheugen|Bytes|Gemiddeld|De hoeveelheid geheugen (in bytes) die wordt verwijderd door de achtergrond opschoning.|
|MemoryLimitHard|Geheugen: vaste geheugen limiet|Bytes|Gemiddeld|Vaste geheugen limiet, van configuratie bestand.|
|MemoryLimitHigh|Geheugen: hoge geheugen limiet|Bytes|Gemiddeld|Hoge geheugen limiet, van configuratie bestand.|
|MemoryLimitLow|Geheugen: lage geheugen limiet|Bytes|Gemiddeld|Limiet voor weinig geheugen, van configuratie bestand.|
|MemoryLimitVertiPaq|Geheugen: VertiPaq-geheugen limiet|Bytes|Gemiddeld|In-Memory limiet, van configuratie bestand.|
|MemoryUsage|Geheugen: geheugen gebruik|Bytes|Gemiddeld|Geheugen gebruik van het Server proces zoals gebruikt bij het berekenen van de prijs voor het schonen van geheugen. Gelijk aan Counter Process\PrivateBytes plus de grootte van gegevens die zijn toegewezen door het geheugen, waarbij elk geheugen wordt genegeerd dat is toegewezen aan of toegewezen door de in-Memory Analytics Engine (VertiPaq) die de geheugen limiet van het engine overschrijdt.|
|private_bytes_metric|Privé-bytes |Bytes|Gemiddeld|De totale hoeveelheid geheugen die het Analysis Services engine proces en de mashup-container processen hebben toegewezen, exclusief geheugen gedeeld met andere processen.|
|virtual_bytes_metric|Virtuele bytes |Bytes|Gemiddeld|De huidige grootte van de virtuele adres ruimte die Analysis Services engine proces en mashup-container processen gebruiken.|
|mashup_engine_private_bytes_metric|M-engine-eigen bytes |Bytes|Gemiddeld|De totale hoeveelheid geheugen-mashup-container processen zijn toegewezen, exclusief geheugen gedeeld met andere processen.|
|mashup_engine_virtual_bytes_metric|M-engine virtuele bytes |Bytes|Gemiddeld|De huidige grootte van de mashup-container processen van de virtuele adres ruimte wordt gebruikt.|
|Quota|Geheugen: quotum|Bytes|Gemiddeld|Huidig geheugen quotum, in bytes. Geheugen quota wordt ook wel een geheugen toekenning of geheugen reservering genoemd.|
|QuotaBlocked|Geheugen: quotum geblokkeerd|Count|Gemiddeld|Het huidige aantal quotum aanvragen dat is geblokkeerd totdat andere geheugen quota zijn vrijgemaakt.|
|VertiPaqNonpaged|Geheugen: VertiPaq niet-wisselbaar|Bytes|Gemiddeld|Geheugen bytes vergrendeld in de werkset voor gebruik door de in-Memory engine.|
|VertiPaqPaged|Geheugen: VertiPaq-pagina|Bytes|Gemiddeld|Bytes van wisselbaar geheugen die in gebruik zijn voor in-Memory gegevens.|
|ProcessingPoolJobQueueLength|Wachtrij lengte van de pool taak wordt verwerkt|Count|Gemiddeld|Het aantal niet-I/O-taken in de wachtrij van de verwer kende thread pool.|
|RowsConvertedPerSec|Verwerken: geconverteerde rijen per seconde|CountPerSecond|Gemiddeld|Het aantal rijen dat tijdens de verwerking is geconverteerd.|
|RowsReadPerSec|Verwerken: gelezen rijen per seconde|CountPerSecond|Gemiddeld|Het aantal rijen dat van alle relationele data bases is gelezen.|
|RowsWrittenPerSec|Verwerken: geschreven rijen per seconde|CountPerSecond|Gemiddeld|Het aantal rijen dat tijdens de verwerking is geschreven.|
|qpu_metric|QPU|Count|Gemiddeld|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|
|QueryPoolBusyThreads|Query's pool bezette threads|Count|Gemiddeld|Het aantal actieve threads in de query thread pool.|
|SuccessfullConnectionsPerSec|Geslaagde verbindingen per seconde|CountPerSecond|Gemiddeld|Snelheid van geslaagde verbindings voltooiingen.|
|CommandPoolBusyThreads|Threads: actieve threads van opdracht pool|Count|Gemiddeld|Het aantal actieve threads in de opdracht thread pool.|
|CommandPoolIdleThreads|Threads: niet-actieve threads van opdracht pool|Count|Gemiddeld|Het aantal niet-actieve threads in de opdracht thread pool.|
|LongParsingBusyThreads|Threads: bezette threads voor lang parseren|Count|Gemiddeld|Het aantal actieve threads in de thread pool voor lang parseren.|
|LongParsingIdleThreads|Threads: niet-actieve threads voor lang parseren|Count|Gemiddeld|Het aantal niet-actieve threads in de thread pool voor lang parseren.|
|LongParsingJobQueueLength|Threads: lengte van taak wachtrij voor lang parseren|Count|Gemiddeld|Aantal taken in de wachtrij van de thread pool voor lang parseren.|
|ProcessingPoolIOJobQueueLength|Threads: lengte van I/O-taak wachtrij voor verwerking van groep|Count|Gemiddeld|Het aantal I/O-taken in de wachtrij van de verwer kende thread pool.|
|ProcessingPoolBusyIOJobThreads|Threads: bezig met verwerken van I/O-taak threads van pool|Count|Gemiddeld|Het aantal threads waarmee I/O-taken worden uitgevoerd in de verwer kende thread pool.|
|ProcessingPoolBusyNonIOThreads|Threads: bezig met het verwerken van niet-I/O-threads van de groep|Count|Gemiddeld|Het aantal threads met niet-I/O-taken in de verwer kende thread pool.|
|ProcessingPoolIdleIOJobThreads|Threads: niet-actieve I/O-taak threads van de groep verwerken|Count|Gemiddeld|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|
|ProcessingPoolIdleNonIOThreads|Threads: niet-I/O-threads van de groep worden verwerkt|Count|Gemiddeld|Het aantal niet-actieve threads in de verwer kende thread pool dat is gereserveerd voor niet-I/O-taken.|
|QueryPoolIdleThreads|Threads: niet-actieve threads van query pool|Count|Gemiddeld|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|
|QueryPoolJobQueueLength|Threads: lengte van taak wachtrij voor query pool|Count|Gemiddeld|Aantal taken in de wachtrij van de query thread pool.|
|ShortParsingBusyThreads|Threads: bezette threads voor kort parseren|Count|Gemiddeld|Het aantal actieve threads in de thread pool voor kort parseren.|
|ShortParsingIdleThreads|Threads: niet-actieve threads voor kort parseren|Count|Gemiddeld|Het aantal niet-actieve threads in de thread pool voor kort parseren.|
|ShortParsingJobQueueLength|Threads: lengte van taak wachtrij voor kort parseren|Count|Gemiddeld|Aantal taken in de wachtrij van de thread pool voor kort parseren.|
|TotalConnectionFailures|Totaal aantal verbindings fouten|Count|Gemiddeld|Totaal aantal mislukte Verbindings pogingen.|
|TotalConnectionRequests|Totaal aantal verbindings aanvragen|Count|Gemiddeld|Totaal aantal verbindings aanvragen. |

## <a name="next-steps"></a>Volgende stappen
[Overzicht van Azure Monitor](../azure-monitor/overview.md)      
[Aan de slag met Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md)      
[Metrische gegevens in Azure Monitor REST API](/rest/api/monitor/metrics)
