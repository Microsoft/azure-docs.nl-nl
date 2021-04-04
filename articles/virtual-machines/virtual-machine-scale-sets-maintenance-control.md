---
title: Overzicht van onderhouds beheer voor upgrades van besturingssysteem installatie kopieën op virtuele-machine schaal sets van Azure
description: Meer informatie over hoe u kunt bepalen wanneer automatische installatie kopieën van besturings systemen worden geïmplementeerd naar uw schaal sets voor virtuele Azure-machines met behulp van onderhouds beheer.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532639"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Voor beeld: onderhouds beheer voor virtuele-machine schaal sets van Azure 

[Automatische upgrades van besturingssysteem installatie kopieën](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) voor uw schaal sets voor virtuele machines beheren met onderhouds beheer.

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op besturingssysteem schijven in uw schaal sets voor virtuele machines met een eenvoudiger en meer voorspel bare ervaring. 

Onderhouds configuraties werken in abonnementen en resource groepen.

De volledige werkstroom is beschikbaar voor de volgende stappen: 
- Een onderhouds configuratie maken.
- Een schaalset voor virtuele machines koppelen aan een onderhouds configuratie.
- Automatische upgrades van besturings systemen inschakelen.

> [!IMPORTANT]
> Onderhouds beheer voor installatie kopieën van besturings systemen op virtuele-machine schaal sets van Azure is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="limitations"></a>Beperkingen

- Vm's moeten zich in een schaalset bestellen.
- De gebruiker moet toegang hebben tot de **resource bijdrager** .
- De duur van het onderhoud moet vijf uur of langer zijn in de onderhouds configuratie.
- Terugkeer patroon van onderhoud moet worden ingesteld op ' dag ' in de onderhouds configuratie


## <a name="management-options"></a>Beheer opties

U kunt onderhouds configuraties maken en beheren met een van de volgende opties:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw virtuele-machine schaal sets van Azure met behulp van onderhouds beheer en Power shell.

> [!div class="nextstepaction"]
> [Beheer van virtuele-machine schaal sets onderhoud met behulp van Power shell](virtual-machine-scale-sets-maintenance-control-powershell.md)
