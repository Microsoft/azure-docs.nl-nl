---
title: Aan de slag met de virtueel-bureaublad agent van Windows
description: Een overzicht van de Windows Virtual Desktop agent en update processen.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: ecc4a5a17186eddd4223715462b14399bdf702df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601887"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Aan de slag met de virtueel-bureaublad agent van Windows

In het Windows Virtual Desktop service Framework bevinden zich drie hoofd onderdelen: de Extern bureaublad-client, de service en de virtuele machines. Deze virtuele machines bevinden zich in het abonnement van de klant waar de virtuele Windows-bureau blad-agent en de bootloader van de agent zijn geïnstalleerd. De agent fungeert als de tussenliggende Communicator tussen de service en de virtuele machines, waardoor connectiviteit mogelijk is. Als u problemen ondervindt met het installeren, bijwerken of configureren van de agent, kan uw virtuele machines daarom geen verbinding maken met de service. De bootloader van de agent is het uitvoer bare bestand dat de agent laadt. 

Dit artikel bevat een kort overzicht van de installatie-en update processen van de agent.

>[!NOTE]
>Deze documentatie is niet voor de FSLogix-agent of de Extern bureaublad-client agent.


## <a name="initial-installation-process"></a>Eerste installatie proces

De virtuele Windows-bureau blad-agent wordt in eerste instantie op een van de volgende twee manieren geïnstalleerd. Als u virtuele machines (Vm's) inricht in Azure Portal en Azure Marketplace, worden de bootloader van agent en agent automatisch geïnstalleerd. Als u Vm's inricht met behulp van Power shell, moet u de agent en de agent-bootloader. msi-bestanden hand matig downloaden bij [het maken van een virtuele Windows-bureaubladclient met Power shell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Wanneer de agent is geïnstalleerd, worden de Vensters naast elkaar en de bewakings agent voor het virtuele bureau blad van Windows ook tegelijkertijd geïnstalleerd. Het stack onderdeel side-by-side is vereist voor gebruikers om veilig reverse server-to-client-verbindingen tot stand te brengen. De bewakings agent van Genève bewaakt de status van de agent. Alle drie deze onderdelen zijn essentieel voor een goede werking van end-to-end-gebruikers connectiviteit.

>[!IMPORTANT]
>Als u de Windows-agent voor virtuele Bureau bladen, side-by-side stack en Genève bewakings agent wilt installeren, moet u de blok kering van alle Url's in de [lijst vereiste URL](safe-url-list.md#virtual-machines)opheffen. Het opheffen van de blok kering van deze Url's is vereist voor het gebruik van de virtueel-bureaublad service van Windows.

## <a name="agent-update-process"></a>Update proces van agent

De Windows Virtual Desktop-service werkt de agent bij wanneer een update beschikbaar wordt. Agent updates kunnen nieuwe functionaliteit of oplossingen voor eerdere problemen bevatten. Zodra de eerste versie van de virtuele bureau blad-agent van Windows is geïnstalleerd, vraagt de agent regel matig de virtueel-bureaublad service van Windows op om te bepalen of er een nieuwere versie van het onderdeel agent, stack of bewaking beschikbaar is. Als er al een nieuwere versie van een van de onderdelen is geïmplementeerd, wordt het bijgewerkte onderdeel automatisch geïnstalleerd.

Nieuwe versies van de agent worden met regel matige intervallen geïmplementeerd in weeklong Peri Oden naar alle Azure-abonnementen. Deze update-Peri Oden worden "vluchten" genoemd. Wanneer er een vlucht optreedt, kunnen de virtuele machines in uw hostgroep de agent update op verschillende tijdstippen ontvangen. Alle VM-agents in alle abonnementen worden bijgewerkt aan het einde van de implementatie periode. Het Windows virtueel bureau blad-vlucht systeem verbetert de betrouw baarheid van de service door de stabiliteit en kwaliteit van de update van de agent te garanderen.


>[!NOTE]
>- Wanneer de agent van Genève de laatste versie bijwerkt, wordt de oude GenevaTask-taak gevonden en uitgeschakeld voordat een nieuwe taak voor de nieuwe bewakings agent wordt gemaakt. De eerdere versie van de bewakings agent wordt niet verwijderd als er een probleem is met de meest recente versie van de bewakings agent, waardoor de eerdere versie moet worden hersteld. Als er een probleem is met de nieuwste versie, wordt de oude bewakings agent opnieuw ingeschakeld om bewakings gegevens te blijven leveren. Alle versies van de monitor die ouder zijn dan de laatste die u vóór de update hebt geïnstalleerd, worden verwijderd uit de virtuele machine.
>- Uw VM houdt drie versies van de side-by-side stack tegelijk. Zo kunt u snel herstel doen als er iets mis gaat met de update. De oudste versie van de stack wordt verwijderd van de VM wanneer de stack wordt bijgewerkt.

Deze update-installatie werkt normaal 2-3 minuten op een nieuwe virtuele machine en zorgt er niet voor dat uw virtuele machine verbinding verliest of wordt afgesloten. Dit update proces is van toepassing op zowel Windows virtueel bureau blad (klassiek) als de nieuwste versie van Windows virtueel bureau blad met Azure Resource Manager.

## <a name="next-steps"></a>Volgende stappen

Nu u een beter inzicht hebt in de Windows Virtual Desktop-Agent, kunt u het volgende doen:

- Als u problemen ondervindt met de agent of de verbinding hebt, raadpleegt u de [hand leiding problemen oplossen met Windows Virtual Desktop agent](troubleshoot-agent.md).
