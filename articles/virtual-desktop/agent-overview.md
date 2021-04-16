---
title: Aan de slag met de Windows Virtual Desktop Agent
description: Een overzicht van de Windows Virtual Desktop agent en updateprocessen.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 529a86712994aae91a554589d383cc748f79d07f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520102"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Aan de slag met de Windows Virtual Desktop Agent

In het Windows Virtual Desktop Service-framework zijn er drie hoofdonderdelen: de Extern bureaublad client, de service en de virtuele machines. Deze virtuele machines staan in het klantabonnement waar de Windows Virtual Desktop agent en agent bootloader zijn geïnstalleerd. De agent fungeert als de tussenliggende machine tussen de service en de virtuele machines, waardoor connectiviteit mogelijk wordt. Als u dus problemen ondervindt met de installatie, update of configuratie van de agent, kunnen uw virtuele machines geen verbinding maken met de service. De agent bootloader is het uitvoerbare bestand dat de agent laadt. 

In dit artikel vindt u een kort overzicht van de agentinstallatie- en updateprocessen.

>[!NOTE]
>Deze documentatie is niet voor de FSLogix-agent of de Extern bureaublad Clientagent.


## <a name="initial-installation-process"></a>Eerste installatieproces

De Windows Virtual Desktop agent wordt in eerste instantie op een van de twee manieren geïnstalleerd. Als u virtuele machines (VM's) in de Azure Portal en Azure Marketplace, worden de agent en agent bootloader automatisch geïnstalleerd. Als u VM's inrichten met Behulp van PowerShell, moet u de agent en agent bootloader .msi-bestanden handmatig downloaden bij het maken van een [Windows Virtual Desktop-hostgroep met PowerShell.](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) Zodra de agent is geïnstalleerd, worden de Windows Virtual Desktop stack naast elkaar en de Genève Monitoring-agent geïnstalleerd. Het side-by-side stack-onderdeel is vereist voor gebruikers om veilig omgekeerde server-naar-client-verbindingen tot stand te brengen. De Bewakingsagent van Genève bewaakt de status van de agent. Al deze onderdelen zijn essentieel voor een goede end-to-end-gebruikersconnectiviteit.

>[!IMPORTANT]
>Als u de Windows Virtual Desktop-agent, side-by-side-stack en Genève-bewakingsagent wilt installeren, moet u alle URL's deblokkeren die worden vermeld in de lijst Vereiste [URL.](safe-url-list.md#virtual-machines) Het deblokkeren van deze URL's is vereist om de Windows Virtual Desktop gebruiken.

## <a name="agent-update-process"></a>Agent-updateproces

De Windows Virtual Desktop-service werkt de agent bij wanneer een update beschikbaar komt. Agentupdates kunnen nieuwe functionaliteit of oplossingen voor eerdere problemen bevatten. U moet altijd de nieuwste stabiele versie van de agent hebben geïnstalleerd, zodat uw VM's de connectiviteit of beveiliging niet verliezen. Zodra de eerste versie van de Windows Virtual Desktop-agent is geïnstalleerd, vraagt de agent regelmatig een query uit op de Windows Virtual Desktop-service om te bepalen of er een nieuwere versie van de agent, stack of bewakingscomponent beschikbaar is. Als er al een nieuwere versie van een van de onderdelen is geïmplementeerd, wordt het bijgewerkte onderdeel automatisch geïnstalleerd door het vluchtsysteem.

Nieuwe versies van de agent worden met regelmatige tussenpozen in weekperioden geïmplementeerd voor alle Azure-abonnementen. Deze updateperioden worden 'vluchten' genoemd. Wanneer een vlucht gebeurt, ziet u mogelijk dat VM's in uw hostgroep de agentupdate op verschillende tijdstippen ontvangen. Alle VM-agents in alle abonnementen worden aan het einde van de implementatieperiode bijgewerkt. Het Windows Virtual Desktop flightingsysteem verbetert de betrouwbaarheid van de service door de stabiliteit en kwaliteit van de agentupdate te garanderen.


Andere belangrijke zaken waar u rekening mee moet houden:

- Omdat VM's in uw hostgroep op verschillende tijdstippen agentupdates kunnen ontvangen, moet u het verschil kunnen zien tussen vluchtproblemen en mislukte agentupdates. Als u naar de gebeurtenislogboeken voor uw VM gaat op **Logboeken** Windows Logs Application en een gebeurtenis met het label  >    >   'ID 3277' ziet, betekent dit dat de agentupdate niet werkt. Als u deze gebeurtenis niet ziet, is de VM in een andere vlucht en wordt deze later bijgewerkt.
- Wanneer de Genève Monitoring-agent wordt bijgewerkt naar de nieuwste versie, wordt de oude Taak Van GenèveTask gevonden en uitgeschakeld voordat een nieuwe taak voor de nieuwe bewakingsagent wordt gemaakt. De eerdere versie van de bewakingsagent wordt niet verwijderd als de meest recente versie van de bewakingsagent een probleem heeft dat moet worden teruggehaald naar de eerdere versie om op te lossen. Als er een probleem is met de nieuwste versie, wordt de oude bewakingsagent opnieuw ingeschakeld om door te gaan met het leveren van bewakingsgegevens. Alle versies van de monitor die eerder zijn dan de laatste versie die u hebt geïnstalleerd vóór de update, worden verwijderd van uw VM.
- Uw VM behoudt drie versies van de side-by-side stack tegelijk. Hierdoor is snel herstel mogelijk als er iets misgaat met de update. De vroegste versie van de stack wordt verwijderd uit de VM wanneer de stack wordt bijgewerkt.

De update van de agent duurt normaal gesproken 2-3 minuten op een nieuwe VM en mag er niet toe leiden dat uw VM de verbinding verliest of wordt afgesloten. Dit updateproces is van toepassing op Windows Virtual Desktop (klassiek) en de nieuwste versie van Windows Virtual Desktop met Azure Resource Manager.

## <a name="next-steps"></a>Volgende stappen

Nu u een beter inzicht hebt in de Windows Virtual Desktop agent, zijn hier enkele resources die u kunnen helpen:

- Als u problemen ondervindt met de agent of connectiviteit, raadpleegt u de probleemoplossingsgids [Windows Virtual Desktop agentproblemen.](troubleshoot-agent.md)
