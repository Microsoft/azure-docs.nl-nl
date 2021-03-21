---
title: Toegangs beheer op basis van rollen beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u Azure RBAC (op rollen gebaseerd toegangs beheer) toepast om Azure Site Recovery toegang te beheren.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d3e1334f513e8ac587d639758d83ce080c5b4ab9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516899"
---
# <a name="manage-site-recovery-access-with-azure-role-based-access-control-azure-rbac"></a>Site Recovery toegang beheren met Azure op rollen gebaseerd toegangs beheer (Azure RBAC)

Met op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) hebt u verfijnd toegang tot Azure. Met behulp van Azure RBAC kunt u verantwoordelijkheden binnen uw team scheiden en alleen specifieke toegangs machtigingen verlenen aan gebruikers als dat nodig is om specifieke taken uit te voeren.

Azure Site Recovery biedt drie ingebouwde rollen om Site Recovery beheer bewerkingen te beheren. Meer informatie over [ingebouwde rollen van Azure](../role-based-access-control/built-in-roles.md)

* [Site Recovery-inzender](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - deze rol bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te kunnen beheren. Een gebruiker met deze rol kan echter geen Recovery Services-kluis maken of verwijderen, noch toegangsrechten aan andere gebruikers toewijzen. Deze rol is het meest geschikt voor nood herstel beheerders die herstel na nood gevallen voor toepassingen of volledige organisaties kunnen inschakelen en beheren.
* [Site Recovery-operator](../role-based-access-control/built-in-roles.md#site-recovery-operator): deze rol heeft machtigingen om failover- en failback-bewerkingen uit te voeren en te beheren. Een gebruiker met deze rol kan de replicatie niet in-of uitschakelen, kluizen maken of verwijderen, een nieuwe infra structuur registreren of toegangs rechten toewijzen aan andere gebruikers. Deze rol is het meest geschikt voor een nood herstel operator die virtuele machines of toepassingen kan uitvoeren wanneer deze door eigen aren van toepassingen en IT-beheerders wordt aangeraden in een werkelijke of gesimuleerde nood situatie, zoals een DR-analyse. De herstel oplossing van de nood geval kan de DR-operator opnieuw beveiligen en failback uitvoeren voor de virtuele machines.
* [Site Recovery-lezer](../role-based-access-control/built-in-roles.md#site-recovery-reader) - deze rol heeft machtigingen voor het weergeven van alle beheerbewerkingen van Site Recovery. Deze rol is het meest geschikt voor IT-leidinggevenden die de huidige status van de beveiliging kunnen controleren en indien nodig ondersteunings tickets moeten activeren.

Als u uw eigen rollen wilt definiëren voor nog meer controle, raadpleegt u [aangepaste rollen bouwen](../role-based-access-control/custom-roles.md) in Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Benodigde machtigingen voor het inschakelen van replicatie voor nieuwe virtuele machines
Wanneer een nieuwe virtuele machine wordt gerepliceerd naar Azure met behulp van Azure Site Recovery, worden de toegangs niveaus van de bijbehorende gebruiker gevalideerd om ervoor te zorgen dat de gebruiker over de vereiste machtigingen beschikt om de Azure-resources te gebruiken die aan Site Recovery zijn verstrekt.

Om replicatie voor een nieuwe virtuele machine in te scha kelen, moet een gebruiker beschikken over:
* Machtiging voor het maken van een virtuele machine in de geselecteerde resource groep
* Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
* Machtiging voor het schrijven naar het geselecteerde opslag account

Een gebruiker heeft de volgende machtigingen nodig om de replicatie van een nieuwe virtuele machine te volt ooien.

> [!IMPORTANT]
>Zorg ervoor dat relevante machtigingen worden toegevoegd volgens het implementatie model (Resource Manager/klassiek) dat wordt gebruikt voor de implementatie van resources.

> [!NOTE]
> Als u replicatie inschakelt voor een virtuele Azure-machine en u Site Recovery wilt toestaan om updates te beheren, terwijl u replicatie inschakelt, kunt u ook een nieuw Automation-account maken. in dat geval hebt u toestemming nodig om een Automation-account te maken in hetzelfde abonnement als de kluis.

| **Resourcetype** | **Implementatiemodel** | **Machtiging** |
| --- | --- | --- |
| Compute | Resource Manager | Micro soft. Compute/Availability sets/lezen |
|  |  | Micro soft. Compute/informatie/lezen |
|  |  | Micro soft. Compute/informatie/schrijven |
|  |  | Micro soft. Compute/informatie/verwijderen |
|  | Klassiek | Micro soft. ClassicCompute/domainName/lezen |
|  |  | Micro soft. ClassicCompute/domein-en schrijf bewerkingen |
|  |  | Micro soft. ClassicCompute/domainName/verwijderen |
|  |  | Micro soft. ClassicCompute/informatie/lezen |
|  |  | Micro soft. ClassicCompute/informatie/schrijven |
|  |  | Micro soft. ClassicCompute/informatie/verwijderen |
| Netwerk | Resource Manager | Micro soft. Network/networkInterfaces/lezen |
|  |  | Micro soft. Network/networkInterfaces/schrijven |
|  |  | Micro soft. Network/networkInterfaces/verwijderen |
|  |  | Micro soft. Network/networkInterfaces/samen voegen/actie |
|  |  | Micro soft. Network/virtualNetworks/lezen |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klassiek | Micro soft. ClassicNetwork/virtualNetworks/lezen |
|  |  | Micro soft. ClassicNetwork/virtualNetworks/deelname/actie |
| Storage | Resource Manager | Micro soft. Storage/Storage accounts/lezen |
|  |  | Micro soft. Storage/Storage accounts/listkeys ophalen/Action |
|  | Klassiek | Micro soft. ClassicStorage/Storage accounts/lezen |
|  |  | Micro soft. ClassicStorage/Storage accounts/Listkeys ophalen/Action |
| Resourcegroep | Resource Manager | Micro soft. resources/implementaties/* |
|  |  | Micro soft. resources/abonnementen/resourceGroups/lezen |

Overweeg het gebruik van de [ingebouwde rollen](../role-based-access-control/built-in-roles.md) ' virtual machine contributor ' en ' klassieke virtuele machine contributor ' voor respectievelijk Resource Manager en klassieke implementatie modellen.

## <a name="next-steps"></a>Volgende stappen
* [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): aan de slag met Azure RBAC in de Azure Portal.
* Meer informatie over het beheren van toegang met:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure-CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Problemen met Azure RBAC oplossen](../role-based-access-control/troubleshooting.md): suggesties ophalen voor het oplossen van veelvoorkomende problemen.
