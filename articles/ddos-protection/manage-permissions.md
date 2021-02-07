---
title: Machtigingen voor Azure DDoS Protection Plan
description: Meer informatie over het beheren van machtigingen in een beveiligings plan.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806253"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>DDoS Protection plannen beheren: machtigingen en beperkingen

Een DDoS-beschermings plan werkt in verschillende regio's en abonnementen. Hetzelfde abonnement kan worden gekoppeld aan virtuele netwerken vanuit andere abonnementen in verschillende regio's, binnen uw Tenant. Het abonnement waaraan het plan is gekoppeld, maakt de maandelijks terugkerende factuur voor het plan en overschrijding kosten voor het geval het aantal beveiligde open bare IP-adressen groter is dan 100. Zie [prijs informatie](https://azure.microsoft.com/pricing/details/ddos-protection/)voor meer informatie over DDoS prijzen.

## <a name="prerequisites"></a>Vereisten

- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md)maken.

## <a name="permissions"></a>Machtigingen

Als u wilt werken met DDoS-beveiligings plannen, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Bewerking                                            | Name                                     |
| ---------                                         | -------------                            |
| Micro soft. Network/ddosProtectionPlans/lezen        | Een DDoS-beschermings plan lezen              |
| Micro soft. Network/ddosProtectionPlans/schrijven       | Een DDoS-beschermings plan maken of bijwerken  |
| Micro soft. Network/ddosProtectionPlans/verwijderen      | Een DDoS-beschermings plan verwijderen            |
| Micro soft. Network/ddosProtectionPlans/samen voegen/actie | Lid worden van een DDoS-beschermings plan              |

Als u DDoS-beveiliging wilt inschakelen voor een virtueel netwerk, moet aan uw account ook de juiste acties worden toegewezen [voor virtuele netwerken](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Azure Policy

Het is niet nodig om meer dan één abonnement te maken voor de meeste organisaties. Een plan kan niet worden verplaatst tussen abonnementen. Als u het abonnement wilt wijzigen, moet u het bestaande schema verwijderen en een nieuw plan maken.

Voor klanten die verschillende abonnementen hebben en die ervoor willen zorgen dat één plan wordt geïmplementeerd in hun Tenant voor kosten beheer, kunt u Azure Policy gebruiken om het [maken van Azure DDoS Protection standaard plannen te beperken](https://aka.ms/ddosrestrictplan). Met dit beleid wordt het maken van DDoS-abonnementen geblokkeerd, tenzij het abonnement eerder als een uitzonde ring is gemarkeerd. In dit beleid wordt ook een lijst weer gegeven met alle abonnementen waarvoor een DDoS-abonnement is geïmplementeerd, maar die niet moeten worden gemarkeerd als niet-compatibel.


## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelfstudies voor meer informatie over het weergeven en configureren van telemetrie voor uw DDoS-beschermingsplan.

> [!div class="nextstepaction"]
> [DDoS-beschermingstelemetrie bekijken en configureren](telemetry.md)
