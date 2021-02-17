---
title: Gaststatus van Azure Monitor voor VM's (preview)
description: Overzicht van de status functie in Azure Monitor voor VM's, inclusief hoe u de status van uw virtuele machines kunt weer geven en hoe u waarschuwingen ontvangt wanneer een virtuele machine een slechte status krijgt.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609729"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>Gaststatus van Azure Monitor voor VM's (preview)
Met Azure Monitor voor VM's-gast status kunt u de status van virtuele machines weer geven op basis van een set prestatie metingen die regel matig worden steek proeven vanuit het gast besturingssysteem. U kunt de status van alle virtuele machines in een abonnement of resource groep snel controleren, inzoomen op de gedetailleerde status van een bepaalde virtuele machine of proactief worden gewaarschuwd wanneer een virtuele machine een slechte status krijgt. 

## <a name="enable-virtual-machine-health"></a>Status van de virtuele machine inschakelen
Zie [Azure monitor voor VM's gast status inschakelen (preview)](vminsights-health-enable.md) voor meer informatie over het inschakelen van de functie gast status en het voorbereiden van virtuele machines.

## <a name="pricing"></a>Prijzen
Er zijn geen directe kosten voor de functie gast status, maar er zijn kosten verbonden aan de opname en opslag van status gegevens in de werk ruimte Log Analytics. Alle gegevens worden opgeslagen in de tabel *HealthStateChangeEvent* . Zie [gebruik en kosten beheren met Azure monitor logboeken](../platform/manage-cost-storage.md) voor meer informatie over de prijs modellen en de kosten.

## <a name="view-virtual-machine-health"></a>Status van de virtuele machine weer geven
In de kolom Status van de **gast-VM** op de pagina **aan** de slag krijgt u een snel overzicht van de status van elke virtuele machine in een bepaald abonnement of resource groep. De huidige status van elke virtuele machine wordt weer gegeven terwijl pictogrammen voor elke groep het aantal virtuele machines weer geven dat zich momenteel in elke status in die groep bevindt.

[![Pagina aan de slag met de status van de gast-VM](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitors
Klik op de status van een virtuele machine om de gedetailleerde status van elk van de monitors weer te geven. Elke monitor meet de status van een bepaald onderdeel. De algemene status van de virtuele machine wordt bepaald door de status van de afzonderlijke monitors. 

![Monitors-voor beeld](media/vminsights-health-overview/monitors.png)

De volgende tabel bevat de aggregatie-en eenheids monitors die momenteel beschikbaar zijn voor elke virtuele machine. 

| Monitor | Type | Description |
|:---|:---|:---|
| CPU-gebruik | Eenheid | Percentage gebruik van de processor. |
| Bestandssystemen | Samenvoegen | Cumulatieve status van alle bestands systemen op een Linux-VM. |
| Bestandssysteem  | Samenvoegen | Status van elk afzonderlijk bestands systeem op een Linux-VM. De naam van de monitor is de naam van het bestands systeem. |
| Beschikbare ruimte | Eenheid | Percentage beschik bare ruimte op het bestands systeem. |
| Logische schijven | Samenvoegen | Cumulatieve status van alle logische schijven op een Windows-VM. |
| Logische schijf  | Samenvoegen | Status van elke afzonderlijke logische schijf op een Windows-VM. De naam van de monitor is de naam van de schijf. |
| Geheugen | Samenvoegen | Cumulatieve status van het geheugen op de virtuele machine. |
| Beschikbaar geheugen | Eenheid | Beschik bare mega bytes op de VM. |

## <a name="health-states"></a>Statussen
Elk bewaakt elke minuut elk van de bemonsterings waarden en vergelijkt de voorbeeld waarden met de criteria voor elke status. Als de criteria voor een bepaalde status waar zijn, wordt de monitor ingesteld op die staat. Als geen van de criteria waar is, wordt de monitor ingesteld op de status in orde. Gegevens worden vanaf de agent verzonden naar Azure Monitor om de drie minuten of onmiddellijk als er een status wijziging is.

Elke monitor heeft een lookback-venster en analyseert alle voor beelden die in die tijd zijn verzameld. Een monitor kan bijvoorbeeld een lookback-venster van 240 seconden bevatten voor het zoeken naar de maximum waarde van ten minste twee bemonsterings waarden, maar niet meer dan de laatste 3. Terwijl waarden worden bemonsterd met een regel matig tempo, kan het aantal in een bepaald venster enigszins variëren als er onderbrekingen optreden in de agent bewerking.

Elk bewaakte monitors hebben de mogelijke statussen in de volgende tabel. deze worden in één en op een bepaald moment weer gegeven. Wanneer een monitor wordt geïnitialiseerd, wordt de status in orde.

| Status | Description |
|:---|:---|
| In orde  | De monitor overschrijdt momenteel de drempel waarde voor waarschuwingen of kritiek. |
| Waarschuwing  | De monitor heeft de drempel waarde voor waarschuwingen (indien gedefinieerd) overschreden. |
| Kritiek | De monitor heeft de kritieke drempel waarde (indien gedefinieerd) overschreden. |
| Onbekend  | Er zijn onvoldoende gegevens verzameld om de status te bepalen. |
| Uitgeschakeld | De monitor is momenteel uitgeschakeld. |
| Geen     | Monitor is zojuist gestart en nog niet geëvalueerd of het bewaakte object bestaat niet meer. |



Er zijn twee soorten monitors, zoals in de volgende tabel wordt weer gegeven.

| Monitor | Description |
|:---|:---|
| Unit-monitor | Meet een bepaald aspect van een resource of toepassing. Dit kan het controleren van een prestatiemeteritem zijn om de prestaties van de resource of de beschikbaarheid ervan te bepalen. |
| Aggregaatmonitor | Groepeert meerdere monitors om één samengevoegde status te bieden. Een aggregaatmonitor kan één of meer unit-monitors en andere aggregaatmonitors bevatten. |


  
### <a name="health-rollup-policy"></a>Status totaliserings beleid
De status van een virtuele machine wordt bepaald door de status van de totalisatie van elk van de bijbehorende eenheden en aggregatie monitors. Elke aggregaatmonitor maakt gebruik van een slechtste status totaliserings beleid waarbij de status van de aggregaatmonitor overeenkomt met de status van de onderliggende monitor met de slechtste status.  

In het volgende voor beeld bevindt de monitor voor **beschik bare ruimte** zich in een kritieke status die rollen tot de aggregaties voor het exemplaar en vervolgens naar **bestands systemen**. Hoewel het **CPU-gebruik** in een waarschuwings status, wordt de virtuele machine ingesteld op kritiek, omdat dit de slechtste status is. Als de beschik bare ruimte terugkeert naar een status in orde, wordt de status van de virtuele machine gewijzigd in een waarschuwing omdat het CPU-gebruik vervolgens de slechtste status van de monitor wordt.

![Voor beeld van status rollup](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Details van de monitor
Selecteer een monitor om de details ervan weer te geven, inclusief de volgende tabbladen.

**Overzicht** bevat een beschrijving van de monitor, de laatste keer dat deze werd geëvalueerd en waarden die worden bemonsterd om de huidige status te bepalen. Het aantal steek proeven kan variëren op basis van de definitie van de monitor en de volatiliteit van de waarden.

[![Overzicht van monitor Details](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Geschiedenis** bevat de geschiedenis van status wijzigingen voor de monitor. U kunt de status wijzigingen voor weer gave-waarden uitvouwen om de status te bepalen. Klik op **weer gave-configuratie** die is toegepast om de configuratie van de monitor weer te geven op het moment dat de status is gewijzigd.



[![Geschiedenis van details controleren](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Configuratie
De configuratie van de monitor voor de geselecteerde virtuele machine weer geven en wijzigen. Zie [bewaking configureren in azure monitor voor VM's gast status (preview)](vminsights-health-enable.md) voor meer informatie.

[![Configuratie details controleren](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Volgende stappen

- [Gast status inschakelen in Azure Monitor voor VM's en onboard agents.](vminsights-health-enable.md)
- [Configureer monitors met behulp van de Azure Portal.](vminsights-health-configure.md)
- [Configureer monitors met behulp van regels voor het verzamelen van gegevens.](vminsights-health-configure-dcr.md)