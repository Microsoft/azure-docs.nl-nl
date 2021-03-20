---
title: Azure-Vm's opnieuw beveiligen naar de primaire regio met Azure Site Recovery | Microsoft Docs
description: Hierin wordt beschreven hoe u Azure-Vm's na een failover opnieuw beveiligt, de secundaire naar de primaire regio met behulp van Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 3b9edab6e908b4506a92c78aa8f3f53277b9c17b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91360868"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Virtuele Azure-machines waarvoor failover naar de primaire regio is uitgevoerd, opnieuw beveiligen

Wanneer u virtuele Azure-machines van de ene regio naar de andere gebruikt via [Azure site Recovery](site-recovery-overview.md), wordt de virtuele machine in de secundaire regio opgestart, in een [niet](site-recovery-failover.md) - **beveiligde** status. Als u een failback wilt uitvoeren voor de virtuele machines naar de primaire regio, voert u de volgende taken uit:

1. Beveilig de virtuele machines in de secundaire regio opnieuw zodat ze repliceren naar de primaire regio.
1. Nadat de beveiliging is voltooid en de virtuele machines zijn gerepliceerd, kunt u een failover uitvoeren van de secundaire naar de primaire regio.

## <a name="prerequisites"></a>Vereisten

- De VM-failover van de primaire naar de secundaire regio moet worden doorgevoerd.
- De primaire doel site moet beschikbaar zijn en u moet toegang hebben tot of resources kunnen maken in die regio.

## <a name="reprotect-a-vm"></a>Een virtuele machine opnieuw beveiligen

1. Klik in **kluis**  >  **gerepliceerde items** met de rechter muisknop op de virtuele machine waarvoor een failover is uitgevoerd en selecteer **opnieuw beveiligen**. De richting voor opnieuw beveiligen moet van secundair naar primair worden weer gegeven.

   ![Scherm afbeelding toont een virtuele machine met een context menu waarvoor opnieuw beveiligen is geselecteerd.](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Bekijk de resource groep-, netwerk-, opslag-en beschikbaarheids sets. Klik vervolgens op **OK**. Als er resources zijn gemarkeerd als nieuw, worden ze gemaakt als onderdeel van het proces voor opnieuw beveiligen.
1. De taak voor opnieuw beveiligen zaait de doel site met de meest recente gegevens. Nadat de taak is voltooid, vindt er Delta replicatie plaats. Daarna kunt u een failover uitvoeren naar de primaire site. Met de optie aanpassen kunt u het opslag account selecteren of het netwerk dat u wilt gebruiken tijdens het opnieuw beveiligen.

   ![Optie aanpassen](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Instellingen voor opnieuw beveiligen aanpassen

U kunt de volgende eigenschappen van de doel-VM aanpassen tijdens het opnieuw beveiligen.

![Aanpassen](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Eigenschap |Notities  |
|---------|---------|
|Doelresourcegroep | Wijzig de doel resource groep waarin de virtuele machine is gemaakt. Als onderdeel van de herbeveiliging wordt de doel-VM verwijderd. U kunt een nieuwe resource groep kiezen voor het maken van de virtuele machine na een failover. |
|Virtueel doelnetwerk | Het doelnet werk kan niet worden gewijzigd tijdens de taak beveiliging opnieuw beveiligen. Als u het netwerk wilt wijzigen, voert u de netwerk toewijzing opnieuw uit. |
|Doel opslag (secundaire virtuele machine maakt geen gebruik van beheerde schijven) | U kunt het opslag account dat door de virtuele machine wordt gebruikt, wijzigen na een failover. |
|Beheerde replica schijven (secundaire virtuele machine maakt gebruik van beheerde schijven) | Site Recovery maakt met replica beheerde schijven in de primaire regio om de beheerde schijven van de secundaire virtuele machine te spie gelen. |
|Cacheopslag | U kunt een cache-opslag account opgeven dat moet worden gebruikt tijdens de replicatie. Standaard wordt er een nieuwe cache opslag account gemaakt, als deze nog niet bestaat. |
|Beschikbaarheidsset | Als de virtuele machine in de secundaire regio deel uitmaakt van een beschikbaarheidsset, kunt u een beschikbaarheidsset kiezen voor de doel-VM in de primaire regio. Site Recovery zoekt standaard naar de bestaande beschikbaarheidsset in de primaire regio en gebruikt deze. Tijdens het aanpassen kunt u een nieuwe beschikbaarheidsset opgeven. |

### <a name="what-happens-during-reprotection"></a>Wat gebeurt er tijdens het opnieuw beveiligen?

Standaard gebeurt het volgende:

1. Er wordt een cache-opslag account gemaakt in de regio waarin de virtuele machine waarvoor een failover is uitgevoerd, is gestart.
1. Als het doel-opslag account (het oorspronkelijke opslag account in de primaire regio) niet bestaat, wordt er een nieuwe gemaakt. De naam van het toegewezen opslag account is de naam van het opslag account dat wordt gebruikt door de secundaire virtuele machine, met als achtervoegsel `asr` .
1. Als uw virtuele machine beheerde schijven gebruikt, worden beheerde replica schijven in de primaire regio gemaakt voor het opslaan van de gegevens die worden gerepliceerd vanaf de schijven van de secundaire virtuele machine.
1. Als de beschik baarheid van de doel groep niet bestaat, wordt er een nieuwe gemaakt als onderdeel van de taak voor opnieuw beveiligen, indien nodig. Als u de instellingen voor opnieuw beveiligen hebt aangepast, wordt de geselecteerde set gebruikt.

Wanneer u een taak voor opnieuw beveiligen start en de doel-VM bestaat, gebeurt het volgende:

1. De doel-VM is uitgeschakeld als deze wordt uitgevoerd.
1. Als de virtuele machine beheerde schijven gebruikt, wordt er een kopie van de oorspronkelijke schijf met een `-ASRReplica` achtervoegsel gemaakt. De oorspronkelijke schijven worden verwijderd. De `-ASRReplica` kopieën worden gebruikt voor replicatie.
1. Als de virtuele machine gebruikmaakt van niet-beheerde schijven, worden de gegevens schijven van de doel-VM losgekoppeld en gebruikt voor replicatie. Er wordt een kopie van de besturingssysteem schijf gemaakt en aangesloten op de VM. De oorspronkelijke besturingssysteem schijf wordt losgekoppeld en gebruikt voor replicatie.
1. Alleen wijzigingen tussen de bron schijf en de doel schijf worden gesynchroniseerd. De verschillen worden berekend door de schijven te vergelijken en vervolgens te worden overgedragen. Hieronder ziet u de geschatte tijd voor het volt ooien van de herbeveiliging.
1. Nadat de synchronisatie is voltooid, wordt de Delta replicatie gestart en wordt er een herstel punt gemaakt in overeenstemming met het replicatie beleid.

Wanneer u een taak voor opnieuw beveiligen start en de doel-VM en schijven niet bestaan, gebeurt het volgende:

1. Als de virtuele machine gebruikmaakt van beheerde schijven, worden replica-schijven gemaakt met het `-ASRReplica` achtervoegsel. De `-ASRReplica` kopieën worden gebruikt voor replicatie.
1. Als de virtuele machine gebruikmaakt van niet-beheerde schijven, worden er replica schijven gemaakt in het doel-opslag account.
1. De gehele schijven worden gekopieerd van de regio waarvoor een failover is uitgevoerd naar de nieuwe doel regio.
1. Nadat de synchronisatie is voltooid, wordt de Delta replicatie gestart en wordt er een herstel punt gemaakt in overeenstemming met het replicatie beleid.

#### <a name="estimated-time-to-do-the-reprotection"></a>Geschatte tijd voor het uitvoeren van de herbeveiliging

In de meeste gevallen repliceert Azure Site Recovery de volledige gegevens niet naar de bron regio.
De volgende voor waarden bepalen hoeveel gegevens worden gerepliceerd:

1. Als de gegevens van de bron-VM worden verwijderd, beschadigd of ontoegankelijk zijn, zoals een wijziging/verwijdering van een resource groep, treedt er tijdens het opnieuw beveiligen een volledige initiële replicatie op omdat er geen gegevens beschikbaar zijn in de bron regio die moet worden gebruikt.
1. Als de gegevens van de bron-VM toegankelijk zijn, worden alleen verschillen berekend door de schijven te vergelijken en vervolgens te worden overgedragen. Controleer de onderstaande tabel om de geschatte tijd op te halen.

|Voorbeeld situatie | Benodigde tijd voor opnieuw beveiligen |
|---|---|
|Bron regio heeft 1 VM met 1 TB standaard schijf.<br/>Er worden slechts 127 GB gegevens gebruikt en de rest van de schijf is leeg.<br/>Schijf type is standaard met een doorvoer snelheid van 60 MBps.<br/>Er zijn geen gegevens gewijzigd na de failover.| Geschatte tijd: 60-90 minuten.<br/> Tijdens het opnieuw beveiligen wordt de controlesom van alle gegevens door Site Recovery gevuld. Dit werkt op 45MBps, dus de totale tijd die het duurt 127 GB/45 MBps, ongeveer 45 minuten.<br/>Er is enige overhead tijd nodig om Site Recovery automatisch te schalen, ongeveer 20-30 minuten. |
|Bron regio heeft 1 VM met 1 TB standaard schijf.<br/>Er worden slechts 127 GB gegevens gebruikt en de rest van de schijf is leeg.<br/>Schijf type is standaard met een doorvoer snelheid van 60 MBps.<br/>45 GB gegevens wijzigingen na een failover.| Geschatte tijd: 2,5-3 uur.<br/> Tijdens het opnieuw beveiligen wordt de controlesom van alle gegevens door Site Recovery gevuld. Dit werkt op 45MBps, dus de totale tijd die het duurt 127 GB/45 MBps, ongeveer 45 minuten.<br/>De overdrachts snelheid is ongeveer 16% van de door Voer, of 9.6 MBps. Daarom wordt de overdrachts tijd voor het Toep assen van wijzigingen van 45 GB met 45 GB/9.6 MBps, ongeveer 80 minuten.<br/>Er is enige overhead tijd nodig om Site Recovery automatisch te schalen, ongeveer 20-30 minuten. |
|Bron regio heeft 1 VM met 1 TB standaard schijf.<br/>Er worden slechts 20 GB gegevens gebruikt en de rest van de schijf is leeg.<br/>Schijf type is standaard met een doorvoer snelheid van 60 MBps.<br/>De eerste gegevens op de schijf direct na een failover zijn 15 GB. Er zijn 5 GB gegevens gewijzigd na de failover. De totale ingevulde gegevens zijn dus 20 GB.| Geschatte tijd: 1-1,5 uur.<br/>Omdat de gegevens die op de schijf zijn ingevuld, kleiner zijn dan 10% van de grootte van de schijf, voeren we een volledige initiële replicatie uit.<br/> De overdrachts snelheid is ongeveer 16% van de door Voer, of 9.6 MBps. Daarom wordt de overdrachts tijd voor het Toep assen van de wijzigingen van 20 GB van 20 GB/9.6 MBps, ongeveer 36 minuten.<br/>Er is enige overhead tijd nodig om Site Recovery automatisch te schalen, ongeveer 20-30 minuten. |
|Bron regio heeft 1 VM met 1 TB Premium-schijf.<br/>Er worden slechts 127 GB gegevens gebruikt en de rest van de schijf is leeg.<br/>Schijf type is Premium met een doorvoer snelheid van 200 MBps.<br/>Er zijn geen gegevens gewijzigd na de failover.| Geschatte tijd: 2 uur.<br/>Tijdens het opnieuw beveiligen wordt de controlesom van alle gegevens door Site Recovery gevuld. Dit werkt op 25MBps (Maxi maal 16% van de schijf doorvoer), dus de totale tijd die deze duurt 127 GB/25 MBps, ongeveer 87 minuten.<br/>Er is enige overhead tijd nodig om Site Recovery automatisch te schalen, ongeveer 20-30 minuten. |
|Bron regio heeft 1 VM met 1 TB Premium-schijf.<br/>Er worden slechts 127 GB gegevens gebruikt en de rest van de schijf is leeg.<br/>Schijf type is Premium met een doorvoer snelheid van 200 MBps.<br/>45 GB gegevens wijzigingen na een failover.| Geschatte tijd: 2,5-3 uur.<br/>Tijdens het opnieuw beveiligen wordt de controlesom van alle gegevens door Site Recovery gevuld. Dit werkt op 25MBps (Maxi maal 16% van de schijf doorvoer), dus de totale tijd die deze duurt 127 GB/25 MBps, ongeveer 87 minuten.</br>De overdrachts snelheid is ongeveer 16% van de door Voer, of 32MBps. Daarom wordt de overdrachts tijd voor het Toep assen van de wijzigingen van 45 GB van 45 GB/32 MBps, ongeveer 24 minuten.<br/>Er is enige overhead tijd nodig om Site Recovery automatisch te schalen, ongeveer 20-30 minuten. |
|Bron regio heeft 1 VM met 1 TB Premium-schijf.<br/>Er worden slechts 20 GB gegevens gebruikt en de rest van de schijf is leeg.<br/>Schijf type is Premium met een doorvoer snelheid van 200 MBps.<br/>De eerste gegevens op de schijf direct na een failover zijn 15 GB. Er zijn 5 GB gegevens gewijzigd na de failover. De totale ingevulde gegevens zijn dus 20 GB| Geschatte tijd: 30-45 minuten.<br/>Omdat de gegevens die op de schijf zijn ingevuld, kleiner zijn dan 10% van de grootte van de schijf, voeren we een volledige initiële replicatie uit.<br/>De overdrachts snelheid is ongeveer 16% van de door Voer, of 32MBps. Daarom wordt de overdrachts tijd voor het Toep assen van de wijzigingen van 20 GB van 20 GB/32 MBps, ongeveer 11 minuten.<br/>Er is enige overhead tijd nodig om Site Recovery automatisch te schalen, ongeveer 20-30 minuten |

Wanneer de virtuele machine opnieuw wordt beveiligd na een failback naar de primaire regio (als de virtuele machine opnieuw wordt beschermd vanuit de primaire regio naar een DR-regio), worden de doel-VM en de bijbehorende NIC ('s) verwijderd.

Wanneer de virtuele machine opnieuw wordt beveiligd vanuit de DR-regio naar de primaire regio, worden de primaire virtuele machine van Erstwhile en de bijbehorende NIC (s) niet verwijderd.

## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine is beveiligd, kunt u een failover initiëren. Met de failover wordt de virtuele machine in de secundaire regio afgesloten en wordt de virtuele machine in de primaire regio gemaakt en opgestart, met een korte downtime tijdens dit proces. U kunt het beste een geschikte tijd kiezen voor dit proces en u voert een testfailover uit voordat u een volledige failover naar de primaire site initieert. [Meer informatie](site-recovery-failover.md) over Azure site Recovery failover.
