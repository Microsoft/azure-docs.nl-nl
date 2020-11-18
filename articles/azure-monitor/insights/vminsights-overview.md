---
title: " Wat is Azure Monitor voor VM's?"
description: Overzicht van Azure Monitor voor VM's, waarmee de status en prestaties van de virtuele Azure-machines worden bewaakt en automatisch toepassings onderdelen en hun afhankelijkheden worden gedetecteerd en toegewezen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: e5eaf2d7075ca09aeb3cfaa2dfea81fd0f8d65ad
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685304"
---
# <a name="overview-of-azure-monitor-for-vms"></a>Overzicht van Azure Monitor voor VM's

Azure Monitor voor VM's bewaakt de prestaties en de status van uw virtuele machines en virtuele-machine schaal sets, met inbegrip van de processen en afhankelijkheden van andere resources. Het kan helpen om voorspel bare prestaties en beschik baarheid van essentiële toepassingen te leveren door prestatie knelpunten en netwerk problemen te identificeren en u te helpen begrijpen of een probleem is gerelateerd aan andere afhankelijkheden.

Azure Monitor voor VM's ondersteunt Windows-en Linux-besturings systemen op het volgende:

- Azure-VM's
- Virtuele-machineschaalsets van Azure
- Hybride virtuele machines die zijn verbonden met Azure Arc
- On-premises virtuele machines
- Virtuele machines die worden gehost in een andere cloud omgeving
  

Azure Monitor voor VM's slaat de gegevens op in Azure Monitor logboeken, zodat IT krachtige aggregatie en filters kan leveren en gegevens trends in de loop van de tijd kan analyseren. U kunt deze gegevens rechtstreeks vanuit de virtuele machine weer geven in één VM of u kunt Azure Monitor gebruiken om een geaggregeerde weer gave van meerdere Vm's te leveren.

![Het oogpunt van Virtual Machine Insights in het Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prijzen
Er zijn geen directe kosten voor Azure Monitor voor VM's, maar er worden kosten in rekening gebracht voor de activiteiten in de Log Analytics-werk ruimte. Op basis van de prijzen die worden gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/), wordt Azure monitor voor VM's gefactureerd voor:

- Gegevens die zijn opgenomen van agents en die zijn opgeslagen in de werk ruimte.
- Status gegevens verzameld van gast status (preview-versie)
- Waarschuwings regels gebaseerd op logboek-en status gegevens.
- Meldingen die worden verzonden vanuit waarschuwings regels.

De logboek grootte is afhankelijk van de teken reeks lengte van prestatie meter items en kan toenemen met het aantal logische schijven en netwerk adapters dat aan de virtuele machine is toegewezen. Als u Servicetoewijzing al gebruikt, ziet u alleen de aanvullende prestatie gegevens die worden verzonden naar het `InsightsMetrics` gegevens type Azure monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Azure Monitor voor VM's configureren
De stappen voor het configureren van Azure Monitor voor VM's zijn als volgt. Volg elke koppeling voor gedetailleerde richt lijnen voor elke stap:

- [Log Analytics-werk ruimte maken.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights-oplossing toevoegen aan de werk ruimte.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installeer agents op virtuele machines en virtuele-machine schaal sets die moeten worden bewaakt.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Volgende stappen

- Zie [Azure monitor voor VM's implementeren](vminsights-enable-overview.md) voor vereisten en methoden voor het inschakelen van bewaking voor uw virtuele machines.

