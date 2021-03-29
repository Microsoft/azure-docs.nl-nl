---
title: Problemen met het bewaken van virtueel bureau blad van Windows-Azure oplossen
description: Problemen oplossen met Azure Monitor voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: dda58868432248fe93a9fbc83d1e538dfc9b61ba
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709443"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Problemen met Azure Monitor voor virtueel bureau blad van Windows oplossen

In dit artikel vindt u bekende problemen en oplossingen voor veelvoorkomende problemen in Azure Monitor voor virtueel bureau blad van Windows.

## <a name="issues-with-configuration-and-setup"></a>Problemen met configuratie en installatie

Als de configuratie werkmap niet goed werkt om Setup te automatiseren, kunt u deze bronnen gebruiken om uw omgeving hand matig in te stellen:

- Als u Diagnostische gegevens hand matig wilt inschakelen of toegang wilt krijgen tot de Log Analytics-werk ruimte, raadpleegt [u Windows diagnostische gegevens over virtueel bureau blad naar log Analytics](diagnostics-log-analytics.md)
- Zie [log Analytics virtuele-machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md)als u de log Analytics-extensie hand matig wilt installeren op een sessiehost.
- Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte in te stellen.
- Zie [prestatie meter items configureren](../azure-monitor/agents/data-sources-performance-counters.md)om prestatie meter items toe te voegen, te verwijderen of te bewerken.
- Zie [gegevens bronnen van Windows-gebeurtenis logboeken met log Analytics agent verzamelen voor informatie](../azure-monitor/agents/data-sources-windows-events.md)over het configureren van Windows-gebeurtenis logboeken voor een log Analytics-werk ruimte.

## <a name="my-data-isnt-displaying-properly"></a>Mijn gegevens worden niet correct weer gegeven

Als uw gegevens niet goed worden weer gegeven, controleert u de volgende algemene oplossingen:

- Zorg er eerst voor dat u op de juiste wijze hebt ingesteld met de configuratie werkmap, zoals beschreven in [gebruik Azure monitor voor virtuele Windows-Bureau bladen om uw implementatie te controleren](azure-monitor.md). Als er items of gebeurtenissen ontbreken, worden de bijbehorende gegevens niet weer gegeven in de Azure Portal.
- Controleer uw toegangs machtigingen & Neem contact op met de resource-eigen aren om ontbrekende machtigingen aan te vragen. iedereen die Windows virtueel bureau blad bewaakt, vereist de volgende machtigingen:
    - Lees toegang tot de Azure-abonnementen die uw virtueel bureau blad-resources van Windows bevatten
    - Lees toegang tot de resource groepen van het abonnement die uw Windows Virtual Desktop-sessie hosts bevatten 
    - Lees toegang tot de Log Analytics werk ruimten die u gebruikt
- Mogelijk moet u uitgaande poorten in de firewall van uw server openen zodat Azure Monitor en Log Analytics gegevens naar de portal kunnen verzenden. Raadpleeg de volgende artikelen voor meer informatie over hoe u dit moet doen:
      - [Uitgaande poorten Azure Monitor](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics firewall vereisten](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Ziet u geen gegevens uit de recente activiteit? Mogelijk wilt u 15 minuten wachten en de feed vernieuwen. Azure Monitor heeft een latentie periode van 15 minuten voor het invullen van de logboek gegevens. Zie [gegevens opname tijd vastleggen in azure monitor](../azure-monitor/logs/data-ingestion-time.md)voor meer informatie.

Als u geen gegevens mist, maar uw gegevens nog steeds niet goed worden weer gegeven, is er mogelijk een probleem in de query of de gegevens bronnen. Bekijk [bekende problemen en beperkingen](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Ik wil Azure Monitor aanpassen voor het virtuele bureau blad van Windows

Azure Monitor voor virtueel bureau blad van Windows maakt gebruik van Azure Monitor-werkmappen. Met werkmappen kunt u een kopie van de sjabloon virtuele Windows-bureau blad-werkmap opslaan en uw eigen aanpassingen door voeren.

Met behulp van aangepaste werkmap sjablonen worden niet automatisch updates van de product groep toegepast. Zie voor meer informatie [problemen oplossen op basis van een werkmap](../azure-monitor/insights/troubleshoot-workbooks.md) en het [overzicht van werkmappen](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Ik kan de gegevens niet interpreteren

Meer informatie over de gegevens termen vindt u in de [woorden lijst Azure monitor voor het virtuele bureau blad van Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>De benodigde gegevens zijn niet beschikbaar

Als u meer prestatie meter items of Windows-gebeurtenis logboeken wilt bewaken, kunt u ze inschakelen voor het verzenden van diagnostische gegevens naar uw Log Analytics-werk ruimte en deze in de diagnostische gegevens van de host te controleren **: host browser**. 

- Zie [prestatie meter items configureren](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters) voor meer informatie over het toevoegen van prestatie meter items
- Zie [Windows-gebeurtenis logboeken configureren](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs) voor meer informatie over het toevoegen van Windows-gebeurtenissen.

Kunt u geen gegevens punt vinden om te helpen bij het vaststellen van een probleem? Stuur ons feedback.

- Zie [probleemoplossings overzicht, feedback en ondersteuning voor virtueel bureau blad van Windows](troubleshoot-set-up-overview.md)voor meer informatie over het geven van feedback.
- U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Hieronder vindt u problemen en beperkingen voor het oplossen van het volgende:

- U kunt slechts één hostgroep per keer bewaken. 
- Als u de favoriete instellingen wilt opslaan, moet u een aangepaste sjabloon van de werkmap opslaan. Met aangepaste sjablonen worden niet automatisch updates van de product groep aangenomen.
- In de configuratie werkmap worden soms fouten over mislukte query's weer gegeven wanneer u uw selecties laadt. Vernieuw de query, voer indien nodig de selectie opnieuw in en de fout moet zichzelf oplossen. 
- Sommige fout berichten worden niet op een gebruiks vriendelijke manier aangeduid en niet alle fout berichten worden beschreven in de documentatie.
- Het prestatie meter item voor het totale aantal sessies kan aantal sessies met een klein aantal overschrijdingen en uw totale sessies worden mogelijk boven uw maximum aantal sessies weer gegeven.
- Aantal beschik bare sessies komt niet overeen met het schaal beleid voor de hostgroep.   
- Ziet u tegen strijdige of onverwachte verbindings tijden? Hoewel zeldzame gevallen kan de voltooiings gebeurtenis van een verbinding ontbreken en kan dit van invloed zijn op bepaalde visuele elementen en metrische gegevens.
- Tijd om verbinding te maken omvat de tijd die gebruikers nodig hebben om hun referenties in te voeren. Dit heeft betrekking op de ervaring, maar in sommige gevallen kunnen onjuiste pieken worden weer gegeven. 
    

## <a name="next-steps"></a>Volgende stappen

Als u niet zeker weet hoe u de gegevens kunt interpreteren of meer wilt weten over algemene termen, raadpleegt u de [Azure monitor voor de Windows-woorden lijst voor virtueel bureau blad](azure-monitor-glossary.md). Zie [Azure monitor voor Windows virtueel bureau blad gebruiken voor het controleren van uw implementatie](azure-monitor.md)voor meer informatie over het instellen en gebruiken van Azure monitor voor virtueel bureau blad van Windows.
