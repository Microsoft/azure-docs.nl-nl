---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: e2be62180907e94401548774b3403db0f36caca3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488461"
---
Het plaatsen van Vm's in één regio vermindert de fysieke afstand tussen de exemplaren. Als u ze in één beschikbaarheids zone plaatst, worden deze ook fysiek dichter bij elkaar gebracht. Naarmate de Azure-footprint groeit, kan één beschikbaarheids zone echter meerdere fysieke data centers omvatten, wat kan leiden tot een netwerk latentie die van invloed is op uw toepassing. 

Als u virtuele machines zo dicht mogelijk wilt ophalen, moet u deze implementeren in een proximity-plaatsings groep.

Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Proximity-plaatsings groepen zijn handig voor werk belastingen waarbij lage latentie een vereiste is.


- Lage latentie tussen zelfstandige Vm's.
- Lage latentie tussen virtuele machines in één beschikbaarheidsset of een schaalset voor virtuele machines. 
- Lage latentie tussen zelfstandige Vm's, Vm's in meerdere beschikbaarheids sets of meerdere schaal sets. U kunt meerdere reken bronnen in één plaatsings groep hebben om een toepassing met meerdere lagen samen te brengen. 
- Lage latentie tussen meerdere toepassings lagen met verschillende typen hardware. U kunt bijvoorbeeld de back-end uitvoeren met de M-serie in een beschikbaarheidsset en de front-end op een exemplaar van de D-serie, in een schaalset, in één proximity-plaatsings groep.


![Afbeelding voor proximity-plaatsings groepen](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Proximity-plaatsings groepen gebruiken 

Een proximity-plaatsings groep is een nieuw resource type in Azure. U moet er een maken voordat u deze met andere resources kunt gebruiken. Zodra de app is gemaakt, kan deze worden gebruikt met virtuele machines, beschikbaarheids sets of virtuele-machine schaal sets. U geeft een proximity-plaatsings groep op bij het maken van reken resources die de Proximity-groeps-ID opgeven. 

U kunt een bestaande resource ook verplaatsen naar een plaatsings groep voor nabijheid. Wanneer u een resource naar een plaatsings groep verplaatst, moet u de activa eerst stoppen (toewijzing ongedaan maken), omdat deze mogelijk opnieuw wordt geïmplementeerd in een ander Data Center in de regio om te voldoen aan de beperking van de co-locatie. 

In het geval van beschikbaarheids sets en virtuele-machine schaal sets moet u de Proximity-plaatsings groep instellen op het resource niveau in plaats van de afzonderlijke virtuele machines. 

Een proximity-plaatsings groep is een beperking voor de co-locatie in plaats van een vastmakend mechanisme. Het wordt vastgemaakt aan een specifiek Data Center met de implementatie van de eerste resource om het te gebruiken. Wanneer alle resources die gebruikmaken van de Proximity-plaatsings groep zijn gestopt (toewijzing opgeheven) of verwijderd, wordt deze niet langer vastgemaakt. Daarom is het belang rijk dat u bij het gebruik van een proximity-plaatsings groep met meerdere VM-reeksen alle vereiste typen vooraf in een sjabloon opgeeft, of u kunt een implementatie volgorde volgen waarmee u de kans op een geslaagde implementatie kunt verbeteren. Als uw implementatie mislukt, start u de implementatie opnieuw met de VM-grootte die is mislukt als de eerste grootte die moet worden geïmplementeerd.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Wat u kunt verwachten wanneer u proximity-plaatsings groepen gebruikt 
Proximity-plaatsings groepen bieden een co-locatie in hetzelfde Data Center. Omdat proximity-plaatsings groepen echter een extra implementatie beperking vertegenwoordigen, kunnen toewijzings fouten optreden. Er zijn een paar situaties waarin mogelijk toewijzings fouten worden weer geven wanneer proximity-plaatsings groepen worden gebruikt:

- Wanneer u voor de eerste virtuele machine in de plaatsings groep voor nabijheid vraagt, wordt het Data Center automatisch geselecteerd. In sommige gevallen kan een tweede aanvraag voor een andere SKU van de virtuele machine mislukken als deze niet bestaat in dat data centrum. In dit geval wordt een **OverconstrainedAllocationRequest** -fout geretourneerd. U kunt dit voor komen door de volg orde te wijzigen waarin u uw Sku's implementeert of als u beide resources hebt geïmplementeerd met één ARM-sjabloon.
-   In het geval van elastische workloads, waarbij u VM-instanties toevoegt en verwijdert en een proximity-beperking voor de plaatsings groep in uw implementatie kan ertoe leiden dat de aanvraag niet kan voldoen aan de **AllocationFailure** -fout. 
- Het is een andere manier om de virtuele machine te onderbreken (de toewijzing ongedaan te maken) en te starten als dat nodig is. Omdat de capaciteit niet wordt bewaard nadat u een virtuele machine hebt gestopt (de toewijzing ongedaan te maken), kan dit een **AllocationFailure** -fout veroorzaken.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Geplande onderhouds-en proximity-plaatsings groepen

Geplande onderhouds gebeurtenissen, zoals hardware buiten gebruik stellen op een Azure-Data Center, kunnen de uitlijning van resources in proximity-plaatsings groepen beïnvloeden. Resources kunnen worden verplaatst naar een ander Data Center, waardoor de plaatsing en latentie verwachtingen die zijn gekoppeld aan de plaatsings groep van de Proximity, worden verstoord.

### <a name="check-the-alignment-status"></a>De uitlijnings status controleren

U kunt het volgende doen om de uitlijnings status van uw proximity-plaatsings groepen te controleren.


- De positie van de samenplaatsings groep voor proximity-plaatsing kan worden weer gegeven met behulp van de portal, CLI en Power shell.

    -   Wanneer u Power shell gebruikt, kan de status van de co-locatie worden verkregen met behulp van Get-AzProximityPlacementGroup cmdlet, met inbegrip van de optionele para meter-ColocationStatus.

    -   Wanneer u CLI gebruikt, kan de status van de co-locatie worden verkregen met behulp `az ppg show` van de optionele para meter--include-uplocation-status.

- Voor elke proximity-plaatsings groep biedt een eigenschap voor de **status** van een samen vatting het huidige overzicht van de uitlijnings status van de gegroepeerde resources. 

    - **Uitgelijnd**: de resource bevindt zich binnen dezelfde latentie envelop van de plaatsings groep voor nabijheid.

    - **Onbekend**: de toewijzing van ten minste één van de VM-resources is ongedaan gemaakt. Zodra de back-up is gestart, moet de status weer **uitgelijnd** zijn.

    - **Niet uitgelijnd**: ten minste één VM-resource is niet uitgelijnd met de Proximity-plaatsings groep. De specifieke resources die niet zijn uitgelijnd, worden ook afzonderlijk in het gedeelte Membership genoemd

- Voor beschikbaarheids sets vindt u informatie over de uitlijning van afzonderlijke Vm's op de overzichts pagina van de Beschikbaarheidsset.

- Voor schaal sets kan informatie over uitlijning van afzonderlijke instanties worden weer gegeven op het tabblad **instanties** van de pagina **overzicht** voor de schaalset. 


### <a name="re-align-resources"></a>Resources opnieuw uitlijnen 

Als u een proximity-plaatsings groep hebt `Not Aligned` , kunt u de betrokken bronnen stop\deallocate en opnieuw starten. Als de virtuele machine zich in een beschikbaarheidsset of een schaalset bevindt, moeten alle virtuele machines in de beschikbaarheidsset of schaalset eerst worden stopped\deallocated voordat ze opnieuw kunnen worden opgestart.

Als er een toewijzings fout is opgetreden vanwege implementatie beperkingen, moet u mogelijk alle resources in de desbetreffende proximity-plaatsings groep (inclusief de uitgelijnde resources) eerst stop\deallocate en vervolgens opnieuw opstarten om de uitlijning te herstellen.

## <a name="best-practices"></a>Best practices 
- Voor de laagste latentie gebruikt u proximity-plaatsings groepen samen met versneld netwerken. Zie [een virtuele Linux-machine maken met versneld netwerken](../articles/virtual-network/create-vm-accelerated-networking-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [een virtuele Windows-machine maken met versneld netwerken](../articles/virtual-network/create-vm-accelerated-networking-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.
- Implementeer alle VM-grootten in één sjabloon. Als u wilt voor komen dat er op hardware wordt gelandd die geen ondersteuning biedt voor alle VM-Sku's en-groottes die u nodig hebt, moet u alle toepassings lagen in één sjabloon toevoegen, zodat ze allemaal tegelijk worden geïmplementeerd.
- Als u uw implementatie met Power shell, CLI of de SDK bijwerkt, wordt er mogelijk een toewijzings fout weer geven `OverconstrainedAllocationRequest` . In dit geval moet u de toewijzing van alle bestaande Vm's stoppen of ongedaan maken en de volg orde wijzigen in het implementatie script om te beginnen met de VM-SKU/-grootten die zijn mislukt. 
- Wanneer u een bestaande plaatsings groep hergebruikt van waaruit Vm's zijn verwijderd, wacht u totdat de verwijdering volledig is voltooid voordat u virtuele machines toevoegt.
- Als latentie uw eerste prioriteit is, plaatst u Vm's in een proximity-plaatsings groep en de volledige oplossing in een beschikbaarheids zone. Maar als tolerantie uw hoogste prioriteit is, kunt u uw instanties verspreiden over meerdere beschikbaarheids zones (een enkele proximity-plaatsings groep kan geen zones omvatten).