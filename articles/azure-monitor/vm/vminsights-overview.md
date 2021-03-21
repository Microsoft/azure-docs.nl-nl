---
title: Wat is VM Insights?
description: Overzicht van VM Insights, waarmee de status en prestaties van de virtuele Azure-machines worden bewaakt en automatisch toepassings onderdelen en hun afhankelijkheden worden gedetecteerd en toegewezen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046411"
---
# <a name="overview-of-vm-insights"></a>Overzicht van VM Insights

Met VM Insights worden de prestaties en de status van uw virtuele machines en virtuele-machine schaal sets gecontroleerd, met inbegrip van de processen en afhankelijkheden van andere resources. Het kan helpen om voorspel bare prestaties en beschik baarheid van essentiële toepassingen te leveren door prestatie knelpunten en netwerk problemen te identificeren en u te helpen begrijpen of een probleem is gerelateerd aan andere afhankelijkheden.

VM Insights ondersteunt Windows-en Linux-besturings systemen op de volgende machines:

- Azure-VM's
- Virtuele-machineschaalsets van Azure
- Hybride virtuele machines die zijn verbonden met Azure Arc
- On-premises virtuele machines
- Virtuele machines die worden gehost in een andere cloud omgeving
  

De gegevens van de virtuele machine worden opgeslagen in Azure Monitor logboeken, waardoor IT krachtige aggregatie en filters kan leveren en gegevens trends in de loop van de tijd kan analyseren. U kunt deze gegevens rechtstreeks vanuit de virtuele machine weer geven in één VM of u kunt Azure Monitor gebruiken om een geaggregeerde weer gave van meerdere Vm's te leveren.

![Het oogpunt van Virtual Machine Insights in het Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prijzen
Er zijn geen directe kosten voor VM-inzichten, maar u betaalt voor de activiteiten in de werk ruimte Log Analytics. Op basis van de prijzen die worden gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/), wordt voor VM Insights de volgende kosten in rekening gebracht:

- Gegevens die zijn opgenomen van agents en die zijn opgeslagen in de werk ruimte.
- Status gegevens verzameld van gast status (preview-versie)
- Waarschuwings regels gebaseerd op logboek-en status gegevens.
- Meldingen die worden verzonden vanuit waarschuwings regels.

De logboek grootte is afhankelijk van de teken reeks lengte van prestatie meter items en kan toenemen met het aantal logische schijven en netwerk adapters dat aan de virtuele machine is toegewezen. Als u Servicetoewijzing al gebruikt, ziet u alleen de extra prestatie gegevens die worden verzonden naar het `InsightsMetrics` gegevens type Azure monitor.


## <a name="configuring-vm-insights"></a>VM Insights configureren
De stappen voor het configureren van VM Insights zijn als volgt. Volg elke koppeling voor gedetailleerde richt lijnen voor elke stap:

- [Log Analytics-werk ruimte maken.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights-oplossing toevoegen aan de werk ruimte.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installeer agents op virtuele machines en virtuele-machine schaal sets die moeten worden bewaakt.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Volgende stappen

- Zie [VM Insights implementeren](./vminsights-enable-overview.md) voor vereisten en methoden voor het inschakelen van bewaking voor uw virtuele machines.
