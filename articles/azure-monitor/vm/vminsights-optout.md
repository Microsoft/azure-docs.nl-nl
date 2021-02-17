---
title: Bewaking in Azure Monitor voor VM's uitschakelen
description: In dit artikel wordt beschreven hoe u de bewaking van uw virtuele machines in Azure Monitor voor VM's stopt.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612135"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Bewaking van uw Vm's in Azure Monitor voor VM's uitschakelen

Nadat u de bewaking van uw virtuele machines (Vm's) hebt ingeschakeld, kunt u later de bewaking uitschakelen in Azure Monitor voor VM's. In dit artikel wordt beschreven hoe u de bewaking voor een of meer virtuele machines kunt uitschakelen.  

Op dit moment biedt Azure Monitor voor VM's geen ondersteuning voor selectief uitschakelen van VM-bewaking. Uw Log Analytics-werk ruimte kan Azure Monitor voor VM's en andere oplossingen ondersteunen. Er kunnen ook andere bewakings gegevens worden verzameld. Als uw Log Analytics-werk ruimte deze services biedt, moet u weten wat het effect en de methoden van het uitschakelen van de bewaking zijn voordat u begint.

Azure Monitor voor VM's is afhankelijk van de volgende onderdelen voor het leveren van de ervaring:

* Een Log Analytics-werk ruimte die bewakings gegevens van Vm's en andere bronnen opslaat.
* Een verzameling prestatie meter items die in de werk ruimte zijn geconfigureerd. De verzameling werkt de bewakings configuratie bij op alle Vm's die zijn verbonden met de werk ruimte.
* `VMInsights`, een bewakings oplossing die is geconfigureerd in de werk ruimte. Met deze oplossing wordt de bewakings configuratie bijgewerkt op alle Vm's die zijn verbonden met de werk ruimte.
* `MicrosoftMonitoringAgent` en `DependencyAgent` , die Azure VM-extensies zijn. Met deze uitbrei dingen worden gegevens verzameld en verzonden naar de werk ruimte.

Houd rekening met het volgende wanneer u de bewaking van uw Vm's wilt uitschakelen:

* Als u met één virtuele machine hebt geëvalueerd en de vooraf geselecteerde standaard Log Analytics werk ruimte gebruikt, kunt u de bewaking uitschakelen door de afhankelijkheids agent van de VM te verwijderen en de verbinding van de Log Analytics agent met deze werk ruimte te verbreken. Deze methode is geschikt als u van plan bent om de virtuele machine te gebruiken voor andere doel einden en deze later opnieuw wilt verbinden met een andere werk ruimte.
* Als u een bestaande Log Analytics werk ruimte hebt geselecteerd die andere bewakings oplossingen en gegevens verzameling van andere bronnen ondersteunt, kunt u de oplossings onderdelen uit de werk ruimte verwijderen zonder de werk ruimte te onderbreken of te beïnvloeden.  

>[!NOTE]
> Nadat u de oplossings onderdelen uit uw werk ruimte hebt verwijderd, kunt u de prestaties blijven bekijken en gegevens toewijzen voor uw Azure-Vm's. De gegevens worden uiteindelijk niet meer weer gegeven in de weer gaven **prestaties** en **toewijzing** . De optie **inschakelen** is beschikbaar vanaf de geselecteerde Azure VM, zodat u de bewaking in de toekomst opnieuw kunt inschakelen.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Azure Monitor voor VM's volledig verwijderen

Als u nog steeds de Log Analytics-werk ruimte nodig hebt, volgt u deze stappen om Azure Monitor voor VM's volledig te verwijderen. U verwijdert de `VMInsights` oplossing uit de werk ruimte.  

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Wanneer u begint te typen, worden in de lijst suggesties weer geven op basis van uw invoer. Selecteer **Log Analytics**.
3. Selecteer in de lijst met Log Analytics-werk ruimten de werk ruimte die u hebt gekozen bij het inschakelen van Azure Monitor voor VM's.
4. Selecteer aan de linkerkant **oplossingen**.  
5. Selecteer **VMInsights (werkruimte naam)** in de lijst met oplossingen. Selecteer op de pagina **overzicht** voor de oplossing **verwijderen**. Selecteer **Ja** als u wordt gevraagd om te bevestigen.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Bewaking uitschakelen en de werk ruimte blijven gebruiken  

Als uw Log Analytics-werk ruimte nog steeds ondersteuning moet bieden voor de bewaking van andere bronnen, volgt u deze stappen om de bewaking uit te scha kelen voor de virtuele machine die u hebt gebruikt om Azure Monitor voor VM's te evalueren. Voor virtuele machines van Azure verwijdert u de VM-extensie van de afhankelijkheids agent en de Log Analytics agent-VM-extensie voor Windows of Linux rechtstreeks vanuit de virtuele machine. 

>[!NOTE]
>Verwijder de Log Analytics agent niet als: 
>
> * Azure Automation beheert de virtuele machine voor het organiseren van processen of het beheren van configuratie of updates. 
> * Azure Security Center beheert de virtuele machine voor de beveiliging en detectie van bedreigingen. 
>
> Als u de Log Analytics-agent verwijdert, kunt u voor komen dat deze services en oplossingen proactief uw VM beheren. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Selecteer **virtual machines** In het Azure Portal. 
3. Selecteer een VM in de lijst. 
4. Selecteer aan de linkerkant de optie **uitbrei dingen**. Selecteer op de pagina **uitbrei dingen** de optie **DependencyAgent**.
5. Selecteer op de pagina extensie-eigenschappen **verwijderen**.
6. Selecteer op de pagina **uitbrei dingen** de optie **MicrosoftMonitoringAgent**. Selecteer op de pagina extensie-eigenschappen **verwijderen**.  
