---
title: Een Azure Site Recovery-kluis verplaatsen naar een andere regio
description: Hierin wordt beschreven hoe u een Recovery Services-kluis (Azure Site Recovery) verplaatst naar een andere Azure-regio
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87422637"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Een Recovery Services-kluis en Azure Site Recovery-configuratie verplaatsen naar een andere Azure-regio

Er zijn verschillende scenario's denkbaar waarin u uw bestaande Azure-resources wellicht wilt verplaatsen naar een andere regio. Bijvoorbeeld om redenen van beheersbaarheid of governance of bij fusies of overnames van bedrijven. Een van de gerelateerde resources die u mogelijk wilt verplaatsen wanneer u uw virtuele Azure-machines verplaatst, is de configuratie voor herstel na een noodgeval. 

Er is geen eersteklas manier om een bestaande configuratie voor herstel na een noodgeval van de ene naar de andere regio te verplaatsen. Dit komt doordat u uw doelregio hebt geconfigureerd op basis van de bronregio van de VM. Wanneer u besluit om de bronregio te wijzigen, kunnen de bestaande configuraties van de doelregio niet opnieuw worden gebruikt en moet deze opnieuw worden ingesteld. In dit artikel wordt het stapsgewijze proces beschreven voor het opnieuw configureren van de instellingen voor herstel na een noodgeval en het verplaatsen ervan naar een andere regio.

In dit document doet u het volgende:

> [!div class="checklist"]
> * Vereisten voor de verplaatsing verifiëren.
> * De resources identificeren die door Azure Site Recovery zijn gebruikt.
> * Replicatie uitschakelen.
> * De resources verwijderen.
> * Site Recovery instellen op basis van de nieuwe bronregio voor de VM's.

> [!IMPORTANT]
> Er is momenteel geen eersteklas manier om een Recovery Services-kluis en de configuratie voor herstel na een noodgeval kant-en-klaar te verplaatsen naar een andere regio. Dit artikel begeleidt u bij het uitschakelen van de replicatie en het instellen ervan in de nieuwe regio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de configuratie voor herstel na een noodgeval verwijdert en verwijdert voordat u probeert de virtuele Azure-machines naar een andere regio te verplaatsen. 

  > [!NOTE]
  > Als uw nieuwe doelregio voor de virtuele Azure-machine hetzelfde is als de doelregio voor herstel na een noodgeval, kunt u de bestaande configuratie voor replicatie gebruiken en verplaatsen. Volg de stappen in [Azure IaaS-VM's verplaatsen naar een andere Azure-regio](azure-to-azure-tutorial-migrate.md).

- Zorg ervoor dat u een weloverwogen beslissing neemt en dat belanghebbenden hiervan op de hoogte zijn. Uw virtuele machine wordt niet beschermd tegen noodgevallen totdat de virtuele machine is verplaatst.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>De resources identificeren die door Azure Site Recovery zijn gebruikt
U wordt aangeraden deze stap uit te voeren voordat u verdergaat met de volgende stap. Het is eenvoudiger om de relevante resources te identificeren terwijl de VM's worden gerepliceerd.

Voor elke virtuele machine van Azure die wordt gerepliceerd, gaat u naar **Beveiligde items** > **Gerepliceerde items** > **Eigenschappen** en identificeert u de volgende resources:

- Doelresourcegroep
- Cacheopslagaccount
- Doelopslagaccount (in het geval van een virtuele Azure-machine met een onbeheerde schijf) 
- Doelnetwerk


## <a name="disable-the-existing-disaster-recovery-configuration"></a>De bestaande configuratie voor herstel na een noodgeval uitschakelen

1. Ga naar de Recovery Services-kluis.
2. Klik in **Beveiligde items** > **Gerepliceerde items** met de rechtermuisknop op de computer en selecteer **Replicatie uitschakelen**.
3. Herhaal deze stap voor alle virtuele machines die u wilt verplaatsen.

> [!NOTE]
> De mobiliteitsservice wordt niet van de beveiligde servers verwijderd. U moet deze handmatig verwijderen. Als u van plan bent om de server opnieuw te beveiligen, kunt u het verwijderen van de mobiliteitsservice overslaan.

## <a name="delete-the-resources"></a>De resources verwijderen

1. Ga naar de Recovery Services-kluis.
2. Selecteer **Verwijderen**.
3. Verwijder alle andere resources die u [eerder hebt geïdentificeerd](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Virtuele Azure-machines verplaatsen naar de nieuwe doelregio

Volg de stappen in deze artikelen naar behoefte om virtuele Azure-machines te verplaatsen naar de doelregio:

- [Virtuele Azure-machines verplaatsen naar een andere regio](azure-to-azure-tutorial-migrate.md)
- [Virtuele Azure-machines verplaatsen naar beschikbaarheidszones](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Site Recovery instellen op basis van de nieuwe bronregio voor de VM's

Configureer herstel na een noodgeval voor de virtuele Azure-machines die zijn verplaatst naar de nieuwe regio door de stappen in [Herstel na een noodgeval instellen voor virtuele Azure-machines](azure-to-azure-tutorial-enable-replication.md) uit te voeren.
