---
title: De Azure Site Recovery process server bewaken
description: In dit artikel wordt beschreven hoe u Azure Site Recovery proces server bewaken die wordt gebruikt voor nood herstel van de virtuele VMware-machines/fysieke servers
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86134740"
---
# <a name="monitor-the-process-server"></a>De proces server bewaken

In dit artikel wordt beschreven hoe u de [site Recovery](site-recovery-overview.md) process server bewaken.

- De proces server wordt gebruikt bij het instellen van herstel na nood gevallen van on-premises VMware-Vm's en fysieke servers naar Azure.
- Standaard wordt de proces server uitgevoerd op de configuratie server. Het wordt standaard geïnstalleerd wanneer u de configuratie server implementeert.
- Desgewenst kunt u extra, scale-out proces servers implementeren en zo een groter aantal gerepliceerde machines en grotere volumes van replicatie verkeer schalen en verwerken.

Meer [informatie](vmware-physical-azure-config-process-server-overview.md) over de rol en implementatie van proces servers.

## <a name="monitoring-overview"></a>Bewakingsoverzicht

Omdat de proces server zoveel rollen heeft, met name bij het opslaan van gegevens in de cache, compressie en overdracht naar Azure, is het belang rijk om de status van de proces server voortdurend te controleren.

Er zijn een aantal situaties die de prestaties van de proces server meestal beïnvloeden. Problemen die invloed hebben op prestaties, hebben een trapsgewijs effect op de status van de virtuele machine, waarbij de proces server en de gerepliceerde machines uiteindelijk naar een kritieke status worden gepusht. Het gaat om de volgende situaties:

- Een groot aantal Vm's maakt gebruik van een proces server, het nadert of overschrijdt de aanbevolen beperkingen.
- Vm's die de proces server gebruiken, hebben een hoog verloop tempo.
- De netwerk doorvoer tussen Vm's en de proces server is niet voldoende voor het uploaden van replicatie gegevens naar de proces server.
- De netwerk doorvoer tussen de proces server en Azure is niet voldoende voor het uploaden van replicatie gegevens van de proces server naar Azure.

Al deze problemen kunnen van invloed zijn op de Recovery Point Objective (RPO) van Vm's. 

**Waarom?** Omdat voor het genereren van een herstel punt voor een VM, moeten alle schijven op de virtuele machine een gemeen schappelijk punt hebben. Als één schijf een hoog verloop snelheid heeft, de replicatie traag is of de proces server niet optimaal is, is dit van invloed op hoe efficiënt herstel punten worden gemaakt.

## <a name="monitor-proactively"></a>Proactief bewaken

Om problemen met de proces server te voor komen, is het belang rijk om het volgende te doen:

- Inzicht in specifieke vereisten voor proces servers met behulp van [capaciteit en schaal richtlijnen](site-recovery-plan-capacity-vmware.md#capacity-considerations)en zorg ervoor dat de proces servers worden geïmplementeerd en uitgevoerd volgens de aanbevelingen.
- Bewaak waarschuwingen en los problemen op wanneer ze optreden, zodat de proces servers efficiënt worden uitgevoerd.


## <a name="process-server-alerts"></a>Waarschuwingen van proces server

De proces server genereert een aantal status waarschuwingen die in de volgende tabel worden samenvatten.

**Waarschuwingstype** | **Details**
--- | ---
![In orde][green] | De proces server is verbonden en in orde.
![Waarschuwing][yellow] | CPU-gebruik > 80% gedurende de afgelopen 15 minuten
![Waarschuwing][yellow] | Geheugen gebruik > 80% gedurende de afgelopen 15 minuten
![Waarschuwing][yellow] | Beschik bare ruimte in de cachemap < 30% gedurende de afgelopen 15 minuten
![Waarschuwing][yellow] | Site Recovery bewaakt elke vijf minuten in behandeling/uitgaande gegevens en schat dat de gegevens in de cache van de proces server niet binnen 30 minuten naar Azure kunnen worden geüpload.
![Waarschuwing][yellow] | Services van de proces server worden niet uitgevoerd in de afgelopen 15 minuten
![Kritiek][red] | CPU-gebruik > 95% gedurende de afgelopen 15 minuten
![Kritiek][red] | Geheugen gebruik > 95% gedurende de afgelopen 15 minuten
![Kritiek][red] | Beschik bare ruimte in de cachemap < 25% gedurende de afgelopen 15 minuten
![Kritiek][red] | Site Recovery bewaakt elke vijf minuten in behandeling/uitgaande gegevens en schat dat de gegevens in de cache van de proces server niet binnen 45 minuten naar Azure kunnen worden geüpload.
![Kritiek][red] | Geen heartbeat van de proces server gedurende 15 minuten.

![Tabel sleutel](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> De algemene status van de proces server is gebaseerd op de slechtste waarschuwing die wordt gegenereerd.



## <a name="monitor-process-server-health"></a>Status van proces server bewaken

U kunt de status van uw proces servers als volgt bewaken: 

1. Als u de replicatie status en-status van een gerepliceerde machine en van de proces server wilt controleren, klikt u in de kluis > **gerepliceerde items** op de computer die u wilt bewaken.
2. In **replicatie status** kunt u de status van de VM controleren. Klik op de status om in te zoomen op fout Details.

    ![Status van de proces server in het VM-dash board](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. In de status van de **proces server** kunt u de status van de proces server bewaken. Inzoomen voor meer informatie.

    ![Details van de proces server in het VM-dash board](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. De status kan ook worden bewaakt met behulp van de grafische weer gave op de VM-pagina.
    - Een scale-out proces server wordt oranje gemarkeerd als er waarschuwingen zijn gekoppeld, en rood als er kritieke problemen zijn. 
    - Als de proces server wordt uitgevoerd in de standaard implementatie op de configuratie server, wordt de configuratie server dienovereenkomstig gemarkeerd.
    - Als u wilt inzoomen, klikt u op de configuratie server of de proces server. Noteer alle problemen en eventuele aanbevelingen voor herstel.

U kunt ook proces servers in de kluis bewaken onder **site Recovery-infra structuur**. Klik in **uw site Recovery-infra structuur beheren** op **configuratie servers**. Selecteer de configuratie server die aan de proces server is gekoppeld en zoom in op de details van de proces server.


## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt met de proces servers, volgt u de [richt lijnen voor probleem oplossing](vmware-physical-azure-troubleshoot-process-server.md)
- Als u meer hulp nodig hebt, kunt u uw vraag plaatsen op de [pagina micro soft Q&een vraag voor Azure site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
