---
title: Wire data-oplossing in Azure Monitor | Microsoft Docs
description: Wiregegevens zijn gegevens van het netwerk en de prestaties van computers met Log Analytics agents. Netwerkgegevens worden gecombineerd met uw logboekgegevens om te helpen bij het correleren van gegevens.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732020"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Wire Data 2.0 (preview)-oplossing in Azure Monitor (buiten gebruik gesteld)

![Symbool Wire Data](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>De oplossing voor Wiregegevens is vervangen door de [VM Insights](../vm/vminsights-overview.md) -en [servicetoewijzing oplossing](../vm/service-map.md).  Beide gebruiken de Log Analytics agent en de afhankelijkheids agent voor het verzamelen van netwerk verbindings gegevens in Azure Monitor.
>
>Ondersteuning voor Wire data-oplossingen loopt af op **31 maart 2022**.  Tot de datum van beëindiging kunnen bestaande klanten die gebruikmaken van de Wire Data 2.0 (preview)-oplossing deze blijven gebruiken.
>
>Nieuwe en bestaande klanten moeten de [VM Insights](../vm/vminsights-enable-overview.md) -of [servicetoewijzing-oplossing](../vm/service-map.md)installeren.  De gegevensset die ze verzamelen, is vergelijkbaar met de gegevensset Wire Data 2.0 (preview).  VM Insights bevat de Servicetoewijzing gegevensset, samen met aanvullende prestatie gegevens en-functies voor analyse. Beide aanbiedingen hebben [verbindingen met Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options).
 

Wiregegevens zijn geconsolideerde netwerk-en prestatie gegevens die zijn verzameld van met Windows verbonden en Linux verbonden computers met de Log Analytics-agent, met inbegrip van de gegevens die worden bewaakt door Operations Manager in uw omgeving. Netwerkgegevens worden gecombineerd met uw andere logboekgegevens om te helpen bij het correleren van gegevens.

Naast de Log Analytics-agent maakt de Wire data-oplossing gebruik van micro soft-afhankelijkheids agenten die u installeert op computers in uw IT-infra structuur. Agents voor afhankelijkheden controleren netwerkgegevens die worden verzonden naar en van uw computers voor netwerkniveaus 2-3 in het [OSI-model](https://en.wikipedia.org/wiki/OSI_model), met inbegrip van de verschillende gebruikte protocollen en poorten. Gegevens worden vervolgens verzonden naar Azure Monitor met behulp van agents.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migreren naar Azure Monitor VM Insights of Servicetoewijzing

In veel gevallen zien we dat klanten vaak zowel Wire Data 2.0 (preview) als  [VM Insights](../vm/vminsights-overview.md) of [servicetoewijzing oplossing](../vm/service-map.md) al op dezelfde virtuele machines hebben ingeschakeld.  Dit betekent dat u de vervangings aanbieding op uw virtuele machine hebt ingeschakeld.  U kunt [de oplossing voor Wire data 2.0 (preview) gewoon verwijderen uit uw log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Als u virtuele machines hebt waarop alleen Wire Data 2.0 (preview-versie) is ingeschakeld, kunt u de virtuele machines op de [VM-inzichten](../vm/vminsights-enable-overview.md) of [servicetoewijzing oplossing](../vm/service-map.md) uitbrengen en vervolgens [de oplossing Wire data 2.0 (preview) verwijderen van de log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migreer uw query's naar de tabel VMConnection vanuit Azure Monitor VM Insights

### <a name="agents-providing-data"></a>Agents die gegevens leveren

#### <a name="wire-data-20-query"></a>Wire Data 2.0 query

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>De query voor de Servicetoewijzing van de VM

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>IP-adressen van de agents die gegevens leveren

#### <a name="wire-data-20-query"></a>Wire Data 2.0 query

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>De query voor de Servicetoewijzing van de VM

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Alle uitgaande communicatie per extern IP-adres

#### <a name="wire-data-20-query"></a>Wire Data 2.0 query

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>De query voor de Servicetoewijzing van de VM

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Ontvangen bytes per protocol naam

#### <a name="wire-data-20-query"></a>Wire Data 2.0 query

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>De query voor de Servicetoewijzing van de VM

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Hoeveelheid netwerk verkeer (in bytes) per proces

#### <a name="wire-data-20-query"></a>Wire Data 2.0 query

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>De query voor de Servicetoewijzing van de VM

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Meer voor beelden van query's

Raadpleeg de documentatie voor het zoeken naar de [Logboeken](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) van de VM en de informatie over de waarschuwing over de [VM-inzichten](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) voor meer voorbeeld query's.

## <a name="uninstall-wire-data-20-solution"></a>Wire Data 2.0 oplossing verwijderen

Als u Wire Data 2.0 wilt verwijderen, moet u de oplossing verwijderen van uw Log Analytics werk ruimte (n).  Dit leidt tot het volgende:

* het Wire Gegevensbeheer Pack wordt verwijderd van de virtuele machines die zijn verbonden met de werk ruimte 
* het gegevens type Wire data wordt niet meer weer gegeven in uw werk ruimte

Volg [deze instructies](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) voor het verwijderen van de Wire data-oplossing.

>[!NOTE]
>Als u de Servicetoewijzing-of de VM Insights-oplossing in uw werk ruimte hebt, wordt de management pack niet verwijderd, omdat deze oplossingen ook deze management pack gebruiken.

### <a name="wire-data-20-management-packs"></a>Management Packs Wire Data 2.0

Wanneer Wire Data wordt geactiveerd in een Log Analytics-werkruimte, wordt een management pack van 300 kB verzonden naar alle Windows-servers in die werkruimte. Als u System Center Operations Manager-agents in een [verbonden beheergroep](../agents/om-agents.md) gebruikt, wordt het management pack Afhankelijkheidsmonitor geïmplementeerd vanuit System Center Operations Manager. Als de agents rechtstreeks zijn verbonden, Azure Monitor levert de management pack.

De naam van het management pack is Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Het management pack wordt geschreven naar: %Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. De gegevensbron waarvan het management pack gebruikmaakt is: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>De afhankelijkheids agent verwijderen

>[!NOTE]
>Als u van plan bent om Wiregegevens te vervangen door Servicetoewijzing of virtuele machine Insights, moet u de afhankelijkheids agent niet verwijderen.

Gebruik de volgende secties om u te helpen de afhankelijkheids agent te verwijderen.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>De afhankelijkheids agent in Windows verwijderen

Een beheerder kan de afhankelijkheids agent voor Windows verwijderen via het configuratie scherm.

Een beheerder kan ook%Programfiles%\Microsoft dependency Agent\Uninstall.exe uitvoeren om de afhankelijkheids agent te verwijderen.

### <a name="uninstall-the-dependency-agent-on-linux"></a>De afhankelijkheids agent in Linux verwijderen

Als u de afhankelijkheids agent van Linux volledig wilt verwijderen, moet u de agent zelf en de connector verwijderen, die automatisch wordt geïnstalleerd met de agent. U kunt beide verwijderen met behulp van de volgende opdracht:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>De oplossing Wire Data 2.0 gebruiken

Op de pagina **Overzicht** voor uw Log Analytics-werkruimte in de Azure Portal klikt u op de tegel **Wire Data 2.0** om het dashboard van Wire Data te openen. Het dashboard bevat de blades in de volgende tabel. Elke blade bevat maximaal tien items die overeenkomen met de criteria voor het opgegeven bereik en de duur van die blade. U kunt zoeken in logboeken waarmee alle records worden geretourneerd door onderaan in de blade te klikken op **Alles bekijken** of door te klikken op de koptekst van de blade.

| **Blade** | **Beschrijving** |
| --- | --- |
| Agents waarmee het netwerkverkeer wordt vastgelegd | Toont het aantal agents dat netwerkverkeer vastlegt en geeft de eerste 10 computers weer die het meeste verkeer vastleggen. Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Klik op een computer in de lijst om zoeken in logboeken uit te voeren waarmee het totale aantal vastgelegde bytes wordt geretourneerd. |
| Lokale subnetten | Toont het aantal lokale subnetten dat door agents is gedetecteerd.  Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, waarmee alle subnetten worden weergegeven met het aantal bytes dat via elk daarvan is verzonden. Klik op een subnet in de lijst om zoeken in logboeken uit te voeren waarmee het totale aantal via het subnet verzonden bytes wordt geretourneerd. |
| Protocollen op toepassingsniveau | Toont het aantal protocollen op toepassingsniveau in gebruik, zoals gedetecteerd door agents. Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Klik op een protocol om zoeken in logboeken uit te voeren waarmee het totale aantal met het protocol verzonden bytes wordt geretourneerd. |

![Wire Data-dashboard](./media/wire-data/wire-data-dash.png)

U kunt de blade **Agents waarmee het netwerkverkeer wordt vastgelegd** gebruiken om te bepalen hoeveel netwerkbandbreedte wordt verbruikt door computers. Met deze blade kunt u gemakkelijk de _drukst bezette_ computer in uw omgeving terugvinden. Deze computers kunnen overbelast zijn, zich abnormaal gedragen of meer netwerkbronnen dan normaal gebruiken.

![Scherm afbeelding van de agents die de Blade netwerk verkeer vastleggen in het Wire Data 2.0 dash board met de netwerk bandbreedte die door elke computer wordt gebruikt.](./media/wire-data/log-search-example01.png)

Op soortgelijke wijze kunt u de blade **Lokale subnetten** gebruiken om te bepalen hoeveel netwerkverkeer via uw subnetten plaatsvindt. Gebruikers definiëren vaak subnetten rondom essentiële gebieden voor hun toepassingen. Deze blade geeft een beeld van die gebieden.

![Scherm opname van de Blade lokale subnetten in het Wire Data 2.0 dash board met de netwerk bandbreedte die wordt gebruikt door een LocalSubnet.](./media/wire-data/log-search-example02.png)

De blade **Protocollen op toepassingsniveau** is handig omdat u hiermee kunt vaststellen welke protocollen er worden gebruikt. U verwacht bijvoorbeeld dat SSH niet in uw netwerkomgeving wordt gebruikt. Aan de hand van de informatie in de blade kunt u dan snel vaststellen of die verwachting al dan niet is uitgekomen.

![Scherm opname van de Blade protocollen op toepassings niveau in het Wire Data 2.0 dash board met de netwerk bandbreedte die wordt gebruikt door elk protocol.](./media/wire-data/log-search-example03.png)

Het is ook handig om te weten als protocolverkeer gedurende een bepaalde periode toe- of afneemt. Als bijvoorbeeld de hoeveelheid gegevens die wordt verzonden door een toepassing toeneemt, is dat misschien iets waarmee u rekening wilt houden.

## <a name="input-data"></a>Invoergegevens

Wire Data verzamelt metagegevens over netwerkverkeer met behulp van de agents die u hebt ingeschakeld. Elke agent verzendt ongeveer elke 15 seconden gegevens.

## <a name="output-data"></a>Uitvoergegevens

Een record met het type _WireData_ is gemaakt voor elk type invoergegevens. WireData-records hebben eigenschappen die worden weergegeven in de volgende tabel:

| Eigenschap | Beschrijving |
|---|---|
| Computer | De naam van de computer waar gegevens zijn verzameld |
| TimeGenerated | Tijd van de record |
| LocalIP | IP-adres van de lokale computer |
| SessionState | Verbonden of verbroken |
| ReceivedBytes | Hoeveelheid ontvangen bytes |
| ProtocolName | Naam van het gebruikte netwerkprotocol |
| IPVersion | IP-versie |
| Richting | Binnenkomend of uitgaand |
| MaliciousIP | IP-adres van een bekende schadelijke bron |
| Severity | Ernst van mogelijke malware |
| RemoteIPCountry | Land/regio van het externe IP-adres |
| ManagementGroupName | Naam van de Operations Manager-beheergroep |
| SourceSystem | Bron waar gegevens zijn verzameld |
| SessionStartTime | Starttijd van de sessie |
| SessionEndTime | Eindtijd van de sessie |
| LocalSubnet | Subnet waar gegevens zijn verzameld |
| LocalPortNumber | Nummer van de lokale poort |
| RemoteIP | Extern IP-adres gebruikt door de externe computer |
| RemotePortNumber | Poortnummer gebruikt door het externe IP-adres |
| SessionID | Een unieke waarde die de communicatiesessie tussen twee IP-adressen aangeeft |
| SentBytes | Aantal verzonden bytes |
| TotalBytes | Totaal aantal bytes dat tijdens de sessie is verzonden |
| ApplicationProtocol | Type van het gebruikte netwerkprotocol   |
| ProcessID | Windows-proces-id |
| ProcessName | Pad en bestandsnaam van het proces |
| RemoteIPLongitude | Waarde IP-lengtegraad |
| RemoteIPLatitude | Waarde IP-breedtegraad |

## <a name="next-steps"></a>Volgende stappen

- Zie [VM Insights implementeren](./vminsights-enable-overview.md) voor vereisten en methoden voor het inschakelen van bewaking voor uw virtuele machines.
- [Zoeken in een logboek](../logs/log-query-overview.md) om gedetailleerde zoekrecords van Wire Data te bekijken.
