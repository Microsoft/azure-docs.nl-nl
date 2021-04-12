---
title: Ontwerp overwegingen voor Azure Virtual Machine Scale Sets
description: Meer informatie over de ontwerp overwegingen voor uw Azure-Virtual Machine Scale Sets. Functies van schaal sets vergelijken met VM-functies.
keywords: virtuele Linux-machine, schaal sets voor virtuele machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774963"
---
# <a name="design-considerations-for-scale-sets"></a>Ontwerp overwegingen voor schaal sets
In dit artikel worden ontwerp overwegingen voor Virtual Machine Scale Sets beschreven. Raadpleeg [Virtual Machine Scale sets Overview](./overview.md)voor meer informatie over wat virtual machine Scale sets zijn.

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Wanneer moet u schaal sets gebruiken in plaats van virtuele machines?
In het algemeen zijn schaal sets handig voor het implementeren van een infra structuur met hoge Beschik baarheid, waarbij een set computers vergelijk bare configuratie heeft. Sommige functies zijn echter alleen beschikbaar in schaal sets, terwijl andere functies alleen beschikbaar zijn in Vm's. Als u een weloverwogen beslissing wilt nemen over het gebruik van elke technologie, moet u eerst een aantal veelgebruikte functies bekijken die beschikbaar zijn in schaal sets, maar niet op Vm's:

### <a name="scale-set-specific-features"></a>Schaal sets-specifieke functies

- Wanneer u de configuratie van de schaalset hebt opgegeven, kunt u de *capaciteits* eigenschap bijwerken om meer vm's parallel te implementeren. Dit proces is beter dan het schrijven van een script om de implementatie van veel afzonderlijke Vm's parallel te organiseren.
- U kunt [Azure automatisch schalen gebruiken om een schaalset te schalen](./virtual-machine-scale-sets-autoscale-overview.md) , maar niet de afzonderlijke vm's.
- U kunt de [schaalset-vm's](/rest/api/compute/virtualmachinescalesets/reimage) , maar [niet de afzonderlijke vm's](/rest/api/compute/virtualmachines), opnieuw instellen.
- U kunt virtuele machines met schaal sets [overinrichten](#overprovisioning) voor meer betrouw baarheid en snellere implementatie tijden. U kunt afzonderlijke Vm's niet overzetten, tenzij u aangepaste code schrijft om deze actie uit te voeren.
- U kunt een [upgrade beleid](./virtual-machine-scale-sets-upgrade-scale-set.md) opgeven zodat upgrades op virtuele machines in uw schaalset eenvoudig kunnen worden geïmplementeerd. Met afzonderlijke Vm's moet u zelf updates organiseren.

### <a name="vm-specific-features"></a>VM-specifieke functies

Sommige functies zijn momenteel alleen beschikbaar in Vm's:

- U kunt een installatie kopie van een afzonderlijke virtuele machine vastleggen, maar niet van een virtuele machine in een schaalset.
- U kunt een afzonderlijke VM migreren van systeem eigen schijven naar Managed disks, maar u kunt geen VM-exemplaren in een schaalset migreren.
- U kunt open bare IPv6-IP-adressen toewijzen aan afzonderlijke virtuele netwerk interface kaarten (Nic's), maar kunnen dit niet doen voor VM-exemplaren in een schaalset. U kunt open bare IPv6-IP-adressen toewijzen aan load balancers vóór afzonderlijke Vm's of schaal sets Vm's.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Schaal sets met Azure Managed Disks
Schaal sets kunnen worden gemaakt met [Azure Managed disks](../virtual-machines/managed-disks-overview.md) in plaats van traditionele Azure-opslag accounts. Managed Disks bieden de volgende voor delen:
- U hoeft geen set van Azure Storage-accounts vooraf te maken voor de virtuele machines met schaal sets.
- U kunt [gekoppelde gegevens schijven](virtual-machine-scale-sets-attached-disks.md) definiëren voor de virtuele machines in uw schaalset.
- Schaal sets kunnen worden geconfigureerd voor [ondersteuning van Maxi maal 1.000 vm's in een set](virtual-machine-scale-sets-placement-groups.md). 

Als u een bestaande sjabloon hebt, kunt u [de sjabloon ook bijwerken om Managed disks te gebruiken](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Door de gebruiker beheerde opslag
Een schaalset die niet is gedefinieerd met Azure Managed Disks, is afhankelijk van door de gebruiker gemaakte opslag accounts voor het opslaan van de besturingssysteem schijven van de virtuele machines in de set. Een verhouding van 20 Vm's per opslag account of minder wordt aanbevolen om maximale IO te halen en ook te profiteren van _overinrichting_ (zie hieronder). Het is ook raadzaam om de begin tekens van de namen van opslag accounts over het alfabet te verdelen. Dit helpt bij het verdelen van de belasting over verschillende interne systemen. 


## <a name="overprovisioning"></a>Overmatige inrichting
Schaal sets worden op dit moment standaard ingesteld op het ' overinrichten ' van Vm's. Wanneer overprovisioning is ingeschakeld, draait de schaalset feitelijk meer virtuele machines uit dan u hebt gevraagd. vervolgens worden de extra Vm's verwijderd zodra het aangevraagde aantal Vm's is ingericht. Bij een overinrichting worden de succes percentages van de inrichting verbeterd en wordt de implementatie tijd verminderd. Er worden geen kosten in rekening gebracht voor de extra Vm's en ze tellen niet mee voor uw quotum limieten.

Bij het overinrichten worden de succes percentages van het inrichten verbeterd, kan dit leiden tot verwarrende gedragingen voor een toepassing die niet is ontworpen voor het afhandelen van extra Vm's die worden weer gegeven en die vervolgens weer geven. Als u het overinrichten wilt uitschakelen, moet u ervoor zorgen dat u de volgende teken reeks in uw sjabloon hebt: `"overprovision": "false"` . Meer informatie vindt u in de [rest API documentatie van de schaalset](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Als uw schaalset gebruikmaakt van door de gebruiker beheerde opslag en u het overinrichten uitschakelt, kunt u meer dan 20 Vm's per opslag account hebben, maar dit wordt niet aanbevolen om te beginnen met 40 om de oorzaak van de IO-prestaties. 

## <a name="limits"></a>Limieten
Een schaalset die is gebouwd op een Marketplace-installatie kopie (ook wel een platform installatie kopie genoemd) en die is geconfigureerd voor het gebruik van Azure Managed Disks ondersteunt een capaciteit van Maxi maal 1.000 Vm's. Als u uw schaalset configureert voor ondersteuning van meer dan 100 Vm's, werken niet alle scenario's hetzelfde (bijvoorbeeld taak verdeling). Zie [werken met grote schaal sets voor virtuele machines](virtual-machine-scale-sets-placement-groups.md)voor meer informatie. 

Een schaalset die is geconfigureerd met door de gebruiker beheerde opslag accounts, is momenteel beperkt tot 100 Vm's (en 5 opslag accounts worden aanbevolen voor deze schaal).

Een schaalset die is gebouwd op een aangepaste installatie kopie (één door u gebouwd), kan een capaciteit hebben van Maxi maal 600 Vm's wanneer deze is geconfigureerd met Azure Managed disks. Als de schaalset is geconfigureerd met door de gebruiker beheerde opslag accounts, moet de virtuele harde schijf van het besturings systeem in één opslag account worden gemaakt. Als gevolg hiervan is het Maxi maal aanbevolen aantal Vm's in een schaalset die is gebouwd op een aangepaste installatie kopie en door de gebruiker beheerde opslag, 20. Als u het overinrichten uitschakelt, kunt u Maxi maal 40 gaan.

Voor meer Vm's dan deze limieten toestaan, moet u meerdere schaal sets implementeren, zoals in [deze sjabloon](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/)wordt weer gegeven.
