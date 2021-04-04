---
title: Centraal beheer van Azure Firewall
description: Meer informatie over Centraal beheer van Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084667"
---
# <a name="azure-firewall-central-management"></a>Centraal beheer van Azure Firewall

Als u meerdere firewalls beheert, weet u dat de firewall regels voortdurend worden gewijzigd, zodat deze moeilijk te synchroniseren zijn. Centrale IT-teams hebben een manier nodig om het base firewall-beleid te definiëren en af te dwingen voor meerdere bedrijfs eenheden. Terzelfder tijd willen DevOps teams hun eigen lokale afgeleide firewall-beleid maken voor een betere flexibiliteit.

Azure Firewall Manager kunt u helpen bij het oplossen van deze problemen.


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Azure Firewall Manager is een beheer service voor netwerk beveiliging die het centrale beveiligings beleid en route beheer biedt voor beveiligings verbindingen in de Cloud. Het maakt het eenvoudig voor IT-teams van ondernemingen om regels van netwerk-en toepassings niveau te definiëren voor het filteren van verkeer op meerdere exemplaren van Azure Firewall. U kunt verschillende Azure-regio's en-abonnementen onderverdelen in hub-en spoke-architecturen voor verkeer governance en beveiliging. Het biedt ook DevOps betere flexibiliteit met afgeleide lokale firewall beveiligings beleidsregels die worden geïmplementeerd in verschillende organisaties.

### <a name="firewall-policy"></a>Firewallbeleid

Een firewall beleid is een Azure-resource die NAT-, netwerk-en toepassings regel verzamelingen en instellingen voor bedreigings informatie bevat. Het is een wereld wijde resource die kan worden gebruikt voor meerdere Azure Firewall instanties in *beveiligde virtuele hubs* en *hub virtuele netwerken*. Beleid kan helemaal nieuw worden gemaakt of worden overgenomen van bestaand beleid. Overname stelt DevOps in staat om lokaal firewallbeleid te maken boven op het basisbeleid dat verplicht is in de organisatie. Beleidsregels werken in verschillende regio’s en abonnementen.
 
U kunt Firewall beleid en-koppelingen maken met Azure Firewall Manager. U kunt echter ook een beleid maken en beheren met behulp van REST API, sjablonen, Azure PowerShell en CLI. Wanneer u een beleid hebt gemaakt, kunt u dit koppelen aan een firewall in een virtuele WAN-hub, waardoor het een *beveiligde virtuele hub* is en/of een firewall in een virtueel netwerk, waardoor IT- *hub Virtual Network*.

### <a name="pricing"></a>Prijzen

Beleid wordt gefactureerd op basis van firewallkoppelingen. Beleid met nul of één firewallkoppeling is gratis. Beleid met meerdere firewallkoppelingen wordt gefactureerd tegen een vast bedrag. Zie [Prijzen voor Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/) voor meer informatie.

## <a name="azure-firewall-management-partners"></a>Azure Firewall beheer partners

De volgende toonaangevende oplossingen van derden ondersteunen Azure Firewall centraal beheer met behulp van standaard-Azure REST Api's. Elk van deze oplossingen heeft zijn eigen unieke kenmerken en functies:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure firewall Manager?](../firewall-manager/overview.md) voor meer informatie over Azure firewall Manager.