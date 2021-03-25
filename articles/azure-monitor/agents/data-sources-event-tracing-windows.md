---
title: Het verzamelen van Event Tracing for Windows gebeurtenissen (ETW) voor analyse Azure Monitor-logboeken
description: Meer informatie over het verzamelen van Event Tracing for Windows (ETW) voor analyse in Azure Monitor Logboeken.
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 73135d95a56dc03790b3b7368a276ebfc99275fd
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025064"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Het verzamelen van Event Tracing for Windows gebeurtenissen (ETW) voor analyse Azure Monitor-logboeken

*Event Tracing for Windows (etw)* biedt een mechanisme voor instrumentatie van toepassingen in gebruikers modus en stuur Programma's voor kernelmodus. De Log Analytics-agent wordt gebruikt voor het [verzamelen van Windows-gebeurtenissen](./data-sources-windows-events.md) die worden geschreven naar de beheer-en operationele etw- [kanalen](/windows/win32/wes/eventmanifestschema-channeltype-complextype). Het is echter af en toe nood zakelijk om andere gebeurtenissen vast te leggen en te analyseren, zoals die naar het analytische kanaal zijn geschreven.  

## <a name="event-flow"></a>Gebeurtenis stroom

Als u op het [manifest gebaseerde etw-gebeurtenissen](/windows/win32/etw/about-event-tracing#types-of-providers) wilt verzamelen voor analyse in azure monitor logboeken, moet u de [Azure Diagnostics-extensie](./diagnostics-extension-overview.md) voor Windows (WAD) gebruiken. In dit scenario fungeert de diagnostische uitbrei ding als de ETW-consument, waarbij gebeurtenissen naar Azure Storage (tabellen) worden geschreven als een tussenliggende opslag. Hier wordt het opgeslagen in een tabel met de naam **WADETWEventTable**. Log Analytics worden de tabel gegevens vervolgens verzameld uit Azure Storage en gepresenteerd als een tabel met de naam **ETWEvent**.

![Gebeurtenis stroom](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>ETW-logboek verzameling configureren

### <a name="step-1-locate-the-correct-etw-provider"></a>Stap 1: Zoek de juiste ETW-provider

Gebruik een van de volgende opdrachten om de ETW-providers op een Windows-bron systeem te inventariseren.

Opdracht regel:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Desgewenst kunt u deze Power shell-uitvoer door sluizen naar Out-Gridview om te helpen bij het navigeren.

Noteer de naam en GUID van de ETW-provider die wordt uitgelijnd op het analyse logboek of het debug-logbestand dat wordt weer gegeven in de Logboeken, of op de module waarvoor u gebeurtenis gegevens wilt verzamelen.

### <a name="step-2-diagnostics-extension"></a>Stap 2: uitbrei ding van diagnostische gegevens

Controleer of de *Windows diagnostische uitbrei ding* is [geïnstalleerd](./diagnostics-extension-windows-install.md#install-with-azure-portal) op alle bron systemen.

### <a name="step-3-configure-etw-log-collection"></a>Stap 3: ETW-logboek verzameling configureren

1. Navigeer naar de Blade **Diagnostische instellingen** van de virtuele machine

2. Selecteer het tabblad **Logboeken**

3. Schuif omlaag en schakel de optie **gebeurtenissen tracering voor Windows (etw) in voor** de ![ scherm opname van diagnostische instellingen](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Stel de provider-GUID of provider klasse in op basis van de provider waarvoor u de verzameling configureert

5. Stel het [**logboek niveau**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session) zo nodig in

6. Klik op het weglatings teken naast de opgegeven provider en klik op **configureren**

7. Zorg ervoor dat de **standaard doel tabel** is ingesteld op **etweventtable**

8. Zo nodig een [**trefwoord filter**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) instellen

9. De provider-en logboek instellingen opslaan

Zodra overeenkomende gebeurtenissen zijn gegenereerd, moet u beginnen met het weer geven van de ETW-gebeurtenissen die worden weer gegeven in de **WADetweventtable** -tabel in azure Storage. U kunt Azure Storage Explorer gebruiken om dit te bevestigen.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Stap 4: Log Analytics verzameling opslag accounts configureren

Volg [deze instructies](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) voor het verzamelen van de logboeken van Azure Storage. Zodra de gegevens van de ETW-gebeurtenis zijn geconfigureerd, moeten deze worden weer gegeven in Log Analytics onder de tabel **ETWEvent** .

## <a name="next-steps"></a>Volgende stappen
- [Aangepaste velden](../logs/custom-fields.md) gebruiken voor het maken van een structuur in uw etw-gebeurtenissen
- Meer informatie over [logboek query's](../logs/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.