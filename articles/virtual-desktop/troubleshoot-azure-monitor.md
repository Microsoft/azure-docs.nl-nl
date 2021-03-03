---
title: Problemen met het bewaken van Windows virtueel bureau blad preview oplossen-Azure
description: Problemen oplossen met Azure Monitor voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709169"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Problemen met Azure Monitor voor virtueel bureau blad van Windows oplossen (preview-versie)

>[!IMPORTANT]
>Azure Monitor voor het virtuele bureau blad van Windows is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel vindt u bekende problemen en oplossingen voor veelvoorkomende problemen in Azure Monitor voor virtueel bureau blad van Windows (preview).

## <a name="issues-with-configuration-and-setup"></a>Problemen met configuratie en installatie

Als de configuratie werkmap niet goed werkt om Setup te automatiseren, kunt u deze bronnen gebruiken om uw omgeving hand matig in te stellen:

- Als u Diagnostische gegevens hand matig wilt inschakelen of toegang wilt krijgen tot de Log Analytics-werk ruimte, raadpleegt [u Windows diagnostische gegevens over virtueel bureau blad naar log Analytics](diagnostics-log-analytics.md)
- Zie [log Analytics virtuele-machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md)als u de log Analytics-extensie hand matig wilt installeren op een host.
- Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte in te stellen.
- Zie [Configuring Performance Counters](../azure-monitor/agents/data-sources-performance-counters.md)om prestatie meter items toe te voegen of te verwijderen.
- Zie [gegevens bronnen van Windows-gebeurtenis logboeken verzamelen met log Analytics agent](../azure-monitor/agents/data-sources-windows-events.md)voor het configureren van gebeurtenissen voor een log Analytics-werk ruimte.

## <a name="my-data-isnt-displaying-properly"></a>Mijn gegevens worden niet correct weer gegeven

Als uw gegevens niet correct worden weer gegeven, controleert u uw configuratie, machtigingen en controleert u of de vereiste IP-adressen zijn gedeblokkeerd. 

- Zorg er eerst voor dat u alle velden in de configuratie werkmap hebt ingevuld, zoals beschreven in [Azure monitor voor Windows Virtual Desktop gebruiken om uw implementatie te controleren](azure-monitor.md). Als er items of gebeurtenissen ontbreken, worden de bijbehorende gegevens niet weer gegeven in de Azure Portal.

- Controleer uw toegangs machtigingen & Neem contact op met de resource-eigen aren om ontbrekende machtigingen aan te vragen. iedereen die Windows virtueel bureau blad bewaakt, vereist de volgende machtigingen:

    - Lees toegang tot de Azure-abonnementen die uw virtueel bureau blad-resources van Windows bevatten
    - Lees toegang tot de resource groepen van het abonnement die uw Windows Virtual Desktop-sessie hosts bevatten 
    - Lees toegang tot de Log Analytics-werk ruimte

- Mogelijk moet u uitgaande poorten in de firewall van uw server openen zodat Azure Monitor gegevens naar de portal kunt verzenden. Zie [uitgaande poorten](../azure-monitor/app/ip-addresses.md). 

- Ziet u geen gegevens uit de recente activiteit? Mogelijk wilt u 15 minuten wachten en de feed vernieuwen. Azure Monitor heeft een latentie periode van 15 minuten voor het invullen van de logboek gegevens. Zie [gegevens opname tijd vastleggen in azure monitor](../azure-monitor/logs/data-ingestion-time.md)voor meer informatie.

Als u geen gegevens mist, maar uw gegevens nog steeds niet goed worden weer gegeven, is er mogelijk een probleem in de query of de gegevens bronnen. Bekijk onze bekende problemen en beperkingen. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Ik wil Azure Monitor aanpassen voor het virtuele bureau blad van Windows

Azure Monitor voor virtueel bureau blad van Windows maakt gebruik van Azure Monitor-werkmappen. Met werkmappen kunt u een kopie van de sjabloon virtuele Windows-bureau blad-werkmap opslaan en uw eigen aanpassingen door voeren.

Met behulp van aangepaste werkmap sjablonen worden niet automatisch updates van de product groep toegepast. Zie voor meer informatie [problemen oplossen op basis van een werkmap](../azure-monitor/insights/troubleshoot-workbooks.md) en het [overzicht van werkmappen](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Ik kan de gegevens niet interpreteren

Meer informatie over de gegevens termen vindt u in de [woorden lijst Azure monitor voor het virtuele bureau blad van Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>De benodigde gegevens zijn niet beschikbaar

Als u meer prestatie meter items of gebeurtenissen wilt bewaken, kunt u ze in staat stellen om naar uw Log Analytics-werk ruimte te verzenden en ze te controleren in diagnostische gegevens van hosts: host browser. 

- Zie [prestatie meter items configureren](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters) voor meer informatie over het toevoegen van prestatie meter items
- Zie [Windows-gebeurtenis logboeken configureren](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs) voor meer informatie over het toevoegen van Windows-gebeurtenissen.

Kunt u geen gegevens punt vinden om te helpen bij het vaststellen van een probleem? Stuur ons feedback.

- Zie [probleemoplossings overzicht, feedback en ondersteuning voor virtueel bureau blad van Windows](troubleshoot-set-up-overview.md)voor meer informatie over het geven van feedback.
- U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) of [ons UserVoice-forum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Dit zijn de problemen en beperkingen die momenteel van pas komen en werken aan het oplossen van:

- U kunt slechts één hostgroep per keer bewaken. 

- Als u de favoriete instellingen wilt opslaan, moet u een aangepaste sjabloon van de werkmap opslaan. Met aangepaste sjablonen worden niet automatisch updates van de product groep aangenomen.

- Sommige fout berichten worden niet op een gebruiks vriendelijke manier aangeduid en niet alle fout berichten worden beschreven in de documentatie.

- Het prestatie meter item voor het totale aantal sessies kan aantal sessies met een klein aantal overschrijdingen en uw totale sessies worden mogelijk boven uw maximum aantal sessies weer gegeven.

- Aantal beschik bare sessies komt niet overeen met het schaal beleid voor de hostgroep. 
    
- In zeldzame gevallen kan de voltooiings gebeurtenis van een verbinding ontbreken. Dit kan invloed hebben op sommige visuele elementen, zoals verbindingen gedurende een bepaalde periode en de verbindings status van de gebruiker.  
    
- De configuratie werkmap ondersteunt alleen het configureren van hosts in dezelfde regio als de resource groep. 

- Tijd om verbinding te maken omvat de tijd die gebruikers nodig hebben om hun referenties in te voeren. Dit heeft betrekking op de ervaring, maar in sommige gevallen kunnen onjuiste pieken worden weer gegeven. 
    

## <a name="next-steps"></a>Volgende stappen

Als u niet zeker weet hoe u de gegevens kunt interpreteren of meer wilt weten over algemene termen, raadpleegt u de [Azure monitor voor de Windows-woorden lijst voor virtueel bureau blad](azure-monitor-glossary.md). Zie [Azure monitor voor Windows virtueel bureau blad gebruiken voor het controleren van uw implementatie](azure-monitor.md)voor meer informatie over het instellen en gebruiken van Azure monitor voor virtueel bureau blad van Windows.