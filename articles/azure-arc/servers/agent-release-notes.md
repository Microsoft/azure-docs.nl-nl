---
title: Wat is er nieuw in de agent voor servers met Azure Arc ingeschakeld
description: Dit artikel bevat opmerkingen bij de release voor servers agent voor Azure Arc ingeschakeld. Voor veel van de samen vattingen vindt u koppelingen naar meer informatie.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 4b7fd97d5b7e1cddc1d78e0d24205890e74c45c1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722518"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Wat is er nieuw in de agent voor servers met Azure Arc ingeschakeld

De Azure-servers die zijn verbonden met de computer agent, ontvangen voortdurend verbeteringen. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten

## <a name="december-2020"></a>December 2020

Versie: 1,3

### <a name="new-feature"></a>Nieuwe functie

Ondersteuning toegevoegd voor Windows Server 2008 R2

### <a name="fixed"></a>Opgelost

Er is een probleem opgelost waardoor de aangepaste script extensie op Linux niet kan worden geïnstalleerd.

## <a name="november-2020"></a>November 2020

Versie: 1,2

### <a name="fixed"></a>Opgelost

Probleem opgelost waarbij de proxy configuratie kan worden verwijderd na een upgrade van op RPM gebaseerde distributies.

## <a name="october-2020"></a>Oktober 2020

Versie: 1.1

### <a name="fixed"></a>Opgelost

- Vast proxy script voor het afhandelen van een andere bestands locatie voor GC-daemon-eenheden.
- Betrouw baarheid van GuestConfig-agent wordt gewijzigd.
- Ondersteuning voor de GuestConfig-agent voor US Gov-Virginia regio.
- Berichten van de extensie rapport van de GuestConfig-agent worden uitgebreid in geval van storingen.

## <a name="september-2020"></a>September 2020

Versie: 1,0 (algemene Beschik baarheid)

### <a name="plan-for-change"></a>Plan voor wijziging

- Ondersteuning voor preview-agents (alle versies ouder dan 1,0) wordt verwijderd in een toekomstige service-update.
- Ondersteuning voor terugval eindpunt verwijderd `.azure-automation.net` . Als u een proxy hebt, moet u het eind punt toestaan `*.his.arc.azure.com` .
- Als de verbonden machine agent is geïnstalleerd op een virtuele machine die wordt gehost in azure, kunnen VM-extensies niet worden geïnstalleerd of gewijzigd vanaf de bron van de Arc ingeschakelde servers. Dit is om te voor komen dat er conflicterende extensie bewerkingen worden uitgevoerd vanuit de **micro soft. Compute** -en **micro soft. HybridCompute** -resource van de virtuele machine. Gebruik de **micro soft. Compute** -resource voor de machine voor alle extensie bewerkingen.
- De naam van het gast configuratie proces is gewijzigd, van *GCD* naar *gcad* in Linux en *gcservice* op *gcarcservice* in Windows.

### <a name="new-feature"></a>Nieuwe functie

- De `azcmagent logs` optie voor het verzamelen van informatie voor ondersteuning is toegevoegd.
- De `azcmagent license` optie is toegevoegd om de gebruiksrecht overeenkomst weer te geven.
- De `azcmagent show --json` optie is toegevoegd aan de status van de uitvoer agent in een gemakkelijk te analyseren indeling.
- `azcmagent show`De markering is toegevoegd aan de uitvoer om aan te geven of de server zich op een virtuele machine bevindt die wordt gehost in Azure.
- `azcmagent disconnect --force-local-only`Optie toegevoegd om het opnieuw instellen van de status van de lokale agent toe te staan wanneer de Azure-service niet kan worden bereikt.
- De `azcmagent connect --cloud` optie is toegevoegd ter ondersteuning van andere Clouds. In deze release wordt alleen Azure ondersteund door de service op het moment van de agent release.
- De agent is gelokaliseerd in door Azure ondersteunde talen.

### <a name="fixed"></a>Opgelost

- Verbeteringen in de connectiviteits controle.
- Er is een probleem opgelost met proxyserver instellingen die verloren zijn gegaan bij het upgraden van de agent op Linux.
- Opgeloste problemen bij het installeren van de agent op de server met Windows Server 2012 R2.
- Verbeteringen in betrouw baarheid van extensie-installatie

## <a name="august-2020"></a>Augustus 2020

Versie: 0,11

- Deze release heeft eerder aangekondigde ondersteuning voor Ubuntu 20,04. Omdat sommige Azure VM-extensies geen ondersteuning bieden voor Ubuntu 20,04, wordt ondersteuning voor deze versie van Ubuntu verwijderd.

- Verbeteringen van de betrouw baarheid van uitbreidings implementaties.

### <a name="known-issues"></a>Bekende problemen

Als u een oudere versie van de Linux-agent gebruikt en deze is geconfigureerd voor het gebruik van een proxy server, moet u na de upgrade de instelling van de proxy server opnieuw configureren. U kunt dit doen `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Volgende stappen

Lees voordat u Arc-servers evalueert of inschakelt op meerdere hybride machines het artikel [Overzicht van Connected Machine Agent](agent-overview.md) om inzicht te krijgen in de vereisten, de technische details van de agent en de implementatiemethoden.