---
title: Capaciteit en prestaties oplossing in Azure Monitor | Microsoft Docs
description: Gebruik de Capaciteit en prestaties oplossing in monitor om inzicht te krijgen in de capaciteit van uw Hyper-V-servers.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: aa08b9f5db5e6371bcd20b473a0c755db3199e7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704290"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Virtuele Hyper-V-machine capaciteit plannen met de Capaciteit en prestaties oplossing (afgeschaft)

![Capaciteit en prestaties-symbool](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> De Capaciteit en prestaties oplossing is afgeschaft.  Klanten die de oplossing al hebben geïnstalleerd, kunnen deze blijven gebruiken, maar Capaciteit en prestaties kunnen niet worden toegevoegd aan nieuwe werk ruimten.

U kunt de Capaciteit en prestaties-oplossing in monitor gebruiken om inzicht te krijgen in de capaciteit van uw Hyper-V-servers. De oplossing biedt inzicht in uw Hyper-V-omgeving door het totale gebruik (CPU, geheugen en schijf) van de hosts en de virtuele machines die worden uitgevoerd op die Hyper-V-hosts weer te geven. Metrische gegevens worden verzameld voor de CPU, het geheugen en de schijven op alle hosts en de Vm's die erop worden uitgevoerd.

De oplossing:

-   Geeft hosts weer met het hoogste en laagste CPU-en geheugen gebruik
-   Toont Vm's met het hoogste en laagste CPU-en geheugen gebruik
-   Toont Vm's met het hoogste en laagste aantal IOPS en het gebruik van de door Voer
-   Hier ziet u welke Vm's worden uitgevoerd op welke hosts
-   Hiermee worden de belangrijkste schijven met hoge door Voer, IOPS en latentie in gedeelde cluster volumes weer gegeven
- Hiermee kunt u aanpassen en filteren op basis van groepen

> [!NOTE]
> De vorige versie van de Capaciteit en prestaties oplossing met de naam capaciteits beheer vereist zowel System Center Operations Manager als System Center Virtual Machine Manager. Deze afhankelijkheden zijn niet van toepassing op deze bijgewerkte oplossing.


## <a name="connected-sources"></a>Verbonden bronnen

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Beschrijving |
|---|---|---|
| [Windows-agents](../agents/agent-windows.md) | Ja | De oplossing verzamelt informatie over capaciteits-en prestatie gegevens van Windows-agents. |
| [Linux-agents](../vm/quick-collect-linux-computer.md) | Nee    | De oplossing verzamelt geen gegevens over de capaciteits-en prestatie gegevens van direct Linux-agents.|
| [SCOM-beheer groep](../agents/om-agents.md) | Ja |De oplossing verzamelt capaciteits-en prestatie gegevens van agents in een verbonden SCOM-beheer groep. Er is geen rechtstreekse verbinding van de SCOM-agent naar Log Analytics vereist.|
| [Azure Storage-account](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Nee | Azure Storage omvat geen capaciteits-en prestatie gegevens.|

## <a name="prerequisites"></a>Vereisten

- Windows-of Operations Manager-agents moeten worden geïnstalleerd op Windows Server 2012-of hoger Hyper-V-hosts, niet op virtuele machines.


## <a name="configuration"></a>Configuratie

Voer de volgende stap uit om de Capaciteit en prestaties oplossing toe te voegen aan uw werk ruimte.

- Voeg de Capaciteit en prestaties oplossing toe aan uw Log Analytics-werk ruimte met behulp van het proces beschreven in [log Analytics-oplossingen toevoegen van de Oplossingengalerie](./solutions.md).

## <a name="management-packs"></a>Management packs

Als uw SCOM-beheer groep is verbonden met uw Log Analytics-werk ruimte, worden de volgende Management Packs geïnstalleerd in SCOM wanneer u deze oplossing toevoegt. Er is geen configuratie of onderhoud van deze management packs vereist.

- Micro soft. intelligence packs. CapacityPerformance

De gebeurtenis 1201 lijkt op:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Wanneer de Capaciteit en prestaties oplossing wordt bijgewerkt, wordt het versie nummer gewijzigd.

Zie [Operations Manager koppelen aan Log Analytics](../agents/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="using-the-solution"></a>De oplossing gebruiken

Wanneer u de Capaciteit en prestaties oplossing aan uw werk ruimte toevoegt, wordt de Capaciteit en prestaties toegevoegd aan het overzichts dashboard. In deze tegel wordt het aantal actieve Hyper-V-hosts en het aantal actieve virtuele machines weer gegeven dat voor de geselecteerde tijds periode is gecontroleerd.

![Capaciteit en prestaties tegel](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Gebruik controleren

Klik op de tegel Capaciteit en prestaties om het Capaciteit en prestaties dash board te openen. Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat maximaal tien items die overeenkomen de criteria voor het opgegeven bereik en het tijdsbereik van die kolom. U kunt een zoekactie die alle records retourneert, uitvoeren op het logboek door onderaan in de kolom te klikken op **Alles bekijken** of door te klikken op de kolomkoptekst.

- **Hosts**
    - **CPU-gebruik** van de host Toont een grafische trend van het CPU-gebruik van hostcomputers en een lijst van hosts, op basis van de geselecteerde tijds periode. Beweeg de muis aanwijzer over het lijn diagram om details weer te geven voor een specifiek punt in de tijd. Klik op de grafiek om meer details te bekijken in zoeken in Logboeken. Klik op een hostnaam om zoeken in Logboeken te openen en Details van CPU-teller weer te geven voor gehoste Vm's.
    - **Geheugen gebruik** van de host Toont een grafische trend van het geheugen gebruik van hostcomputers en een lijst van hosts, op basis van de geselecteerde tijds periode. Beweeg de muis aanwijzer over het lijn diagram om details weer te geven voor een specifiek punt in de tijd. Klik op de grafiek om meer details te bekijken in zoeken in Logboeken. Klik op een hostnaam om zoeken in Logboeken te openen en Details van geheugen teller weer te geven voor gehoste Vm's.
- **Virtual Machines**
    - **CPU-gebruik van VM** Toont een grafische trend van het CPU-gebruik van virtuele machines en een lijst met virtuele machines, op basis van de geselecteerde tijds periode. Beweeg de muis aanwijzer over het lijn diagram om details weer te geven voor een specifiek punt in de tijd voor de top 3 van virtuele machines. Klik op de grafiek om meer details te bekijken in zoeken in Logboeken. Klik op een VM-naam om zoeken in Logboeken te openen en Bekijk geaggregeerde CPU-teller Details voor de virtuele machine.
    - **Geheugen gebruik** van de VM Toont een grafische trend van het geheugen gebruik van virtuele machines en een lijst met virtuele machines, op basis van de geselecteerde tijds periode. Beweeg de muis aanwijzer over het lijn diagram om details weer te geven voor een specifiek punt in de tijd voor de top 3 van virtuele machines. Klik op de grafiek om meer details te bekijken in zoeken in Logboeken. Klik op een VM-naam om zoeken in Logboeken te openen en de details van het geaggregeerde geheugen item voor de virtuele machine weer te geven.
    - **Totale aantal IOPS-schijven van virtuele machine** Toont een grafische trend van de totale schijf-IOPS voor virtuele machines en een lijst met virtuele machines met de IOPS voor elke, op basis van de geselecteerde tijds periode. Beweeg de muis aanwijzer over het lijn diagram om details weer te geven voor een specifiek punt in de tijd voor de top 3 van virtuele machines. Klik op de grafiek om meer details te bekijken in zoeken in Logboeken. Klik op een VM-naam om zoeken in Logboeken te openen en Bekijk de details van de verzamelde schijf-IOPS-teller voor de virtuele machine.
    - **Totale schijf doorvoer VM** Toont een grafische trend van de totale schijf doorvoer voor virtuele machines en een lijst met virtuele machines met de totale schijf doorvoer voor elk, op basis van de geselecteerde tijds periode. Beweeg de muis aanwijzer over het lijn diagram om details weer te geven voor een specifiek punt in de tijd voor de top 3 van virtuele machines. Klik op de grafiek om meer details te bekijken in zoeken in Logboeken. Klik op de naam van een virtuele machine om zoeken in Logboeken te openen en Bekijk de details van de totale schijf doorvoer capaciteit voor de virtuele machine.
- **Geclusterde gedeelde volumes**
    - **Totale door Voer** Toont de som van zowel lees-als schrijf bewerkingen op geclusterde gedeelde volumes.
    - **Totaal aantal IOPS** Toont de som van invoer/uitvoer-bewerkingen per seconde op geclusterde gedeelde volumes.
    - **Totale latentie** Toont de totale latentie op geclusterde gedeelde volumes.
- **Host-dichtheid** De bovenste tegel toont het totale aantal hosts en de virtuele machines die beschikbaar zijn voor de oplossing. Klik op de bovenste tegel om meer details weer te geven in zoeken in Logboeken. Bevat ook een lijst met alle hosts en het aantal virtuele machines die worden gehost. Klik op een host om in te zoomen op de VM. Dit resulteert in een zoek opdracht in een logboek.


![dash board hosts-Blade](./media/capacity-performance/dashboard-hosts.png)

![Blade virtuele dash board-machines](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Prestaties evalueren

Productie computing omgevingen verschillen aanzienlijk van de ene organisatie naar de andere. Daarnaast kunnen de workloads voor capaciteit en prestaties afhankelijk zijn van de manier waarop uw virtuele machines worden uitgevoerd en wat u het normaal beschouwt. Specifieke procedures voor het meten van de prestaties zijn waarschijnlijk niet van toepassing op uw omgeving. Daarom is meer algemene, prescriptieve richt lijnen beter geschikt voor hulp. Micro soft publiceert een aantal voor Schriften die u kunt gebruiken om prestaties te meten.

Als samen vatting van de oplossing worden gegevens over capaciteit en prestaties verzameld uit verschillende bronnen, waaronder prestatie meter items. Gebruik deze capaciteits-en prestatie gegevens die in verschillende Opper vlakken in de oplossing worden gepresenteerd en Vergelijk uw resultaten met de [meet prestaties op het Hyper-V-](https://www.microsoft.com/en-us/download/details.aspx?id=56495) artikel. Hoewel het artikel enige tijd geleden is gepubliceerd, zijn de metrische gegevens, overwegingen en richt lijnen nog steeds geldig. Het artikel bevat koppelingen naar andere nuttige bronnen.


## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel bevat voor beelden van zoek opdrachten in het logboek voor de capaciteits-en prestatie gegevens die door deze oplossing worden verzameld en berekend.


| Query | Beschrijving |
|:--- |:--- |
| Alle geheugen configuraties van host | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en CounterName = = "host assigned Memory MB" &#124; vat MB = AVG (CounterValue) door INSTANCENAME |
| Alle VM-geheugen configuraties | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en CounterName = = "aan VM toegewezen geheugen MB" &#124; een samen vatting van MB = Gem (CounterValue) door INSTANCENAME |
| Uitsplitsing van de totale schijf-IOPS op alle Vm's | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en (CounterName = = "VHD lees/s" of CounterName = = "VHD writes/s") &#124; samenvat AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1U), CounterName, INSTANCENAME |
| Uitsplitsing van de totale door Voer van de schijf over alle Vm's | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en (CounterName = = "VHD Read MB/s" of CounterName = = "VHD write MB/s") &#124; samenvatten AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1U), CounterName, INSTANCENAME |
| Uitsplitsing van de totale IOPS over alle Csv's | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en (CounterName = = "CSV-Lees bewerkingen/s" of CounterName = = "CSV-schrijf bewerkingen/s") &#124; samenvatten AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1U), CounterName, INSTANCENAME |
| Uitsplitsing van de totale door voer over alle Csv's | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en (CounterName = = "CSV-Lees bewerkingen/s" of CounterName = = "CSV-schrijf bewerkingen/s") &#124; samenvatten AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1U), CounterName, INSTANCENAME |
| Uitsplitsing van de totale latentie over alle Csv's | Perf &#124; waarbij ObjectName = = "Capaciteit en prestaties" en (CounterName = = "lees latentie van CSV" of CounterName = = "CSV write latentie") &#124; samenvatten AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1U), CounterName, INSTANCENAME |


## <a name="next-steps"></a>Volgende stappen
* Gebruik [Zoek opdrachten in Logboeken in log Analytics](../logs/log-query-overview.md) om gedetailleerde capaciteit en prestaties gegevens weer te geven.

