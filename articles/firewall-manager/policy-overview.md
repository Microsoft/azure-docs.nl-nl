---
title: Overzicht van Azure Firewall Manager-beleid
description: Meer informatie over Azure Firewall Manager-beleidsregels
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: victorh
ms.openlocfilehash: 73a07af0fa98adf66d6104f1ab545d31a0cfd6d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95802028"
---
# <a name="azure-firewall-manager-policy-overview"></a>Overzicht van Azure Firewall Manager-beleid

Firewallbeleid is een Azure-resource die NAT-, netwerk-, toepassingsregelverzamelingen bevat en instellingen voor Bedreigingsinformatie. Het is een globale resource die kan worden gebruikt in verschillende Azure Firewall-exemplaren in Beveiligde virtuele hubs en virtuele netwerken voor hubs. Beleidsregels werken in verschillende regio’s en abonnementen.

![Azure Firewall Manager-beleid](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Beleid maken en koppelen

Beleid kan op verschillende manieren worden gemaakt en beheerd, waaronder de Azure-portal, REST API, sjablonen, Azure PowerShell en CLI.

U kunt ook bestaande regels van Azure Firewall migreren met behulp van de portal of Azure PowerShell om beleid te maken. Zie [Azure Firewall-configuraties migreren naar Azure Firewall-beleid](migrate-to-policy.md) voor meer informatie. 

Beleid kan worden gekoppeld aan een of meer virtuele hubs of VNET’s. De firewall kan zich in elk abonnement bevinden dat is gekoppeld aan uw account, en in elke regio.

## <a name="hierarchical-policies"></a>Hiërarchische beleidsregels

Beleid kan helemaal nieuw worden gemaakt of worden overgenomen van bestaand beleid. Overname stelt DevOps in staat om lokaal firewallbeleid te maken boven op het basisbeleid dat verplicht is in de organisatie.

Beleid dat is gemaakt met niet-leeg bovenliggend beleid, nemen alle regelverzamelingen van het bovenliggende beleid over. Netwerkregelverzamelingen die zijn overgenomen van bovenliggend beleid, krijgen altijd prioriteit boven netwerkregelverzamelingen die zijn gedefinieerd als onderdeel van nieuw beleid. Dezelfde logica is ook van toepassing op toepassingsregelverzamelingen. Netwerkregelverzamelingen worden echter altijd verwerkt vóór toepassingsregelverzamelingen, ongeacht overname.

De modus voor Bedreigingsinformatie wordt ook overgenomen van het bovenliggende beleid. U kunt de modus voor Bedreigingsinformatie instellen op een andere waarde om dit gedrag te negeren, maar u kunt de modus niet uitschakelen. U kunt de instelling alleen maar overschrijven met een striktere waarde. Als het bovenliggende beleid bijvoorbeeld is ingesteld op **Alleen waarschuwen**, kunt u dit lokale beleid configureren naar **Waarschuwen en weigeren**.

Net zoals de modus voor Bedreigingsinformatie wordt ook de acceptatielijst van Bedreigingsinformatie overgenomen van het bovenliggende beleid. Via het onderliggende beleid kunnen extra IP-adressen worden toegevoegd aan de acceptatielijst.

NAT-regelverzamelingen worden niet overgenomen omdat ze specifiek zijn voor een bepaalde firewall.

Met overname worden alle wijzigingen in het bovenliggende beleid automatisch toegepast op het bijbehorende onderliggende firewallbeleid.

## <a name="traditional-rules-and-policies"></a>Traditionele regels en beleidsregels

Azure Firewall biedt ondersteuning voor zowel traditionele regels als beleidsregels. In de volgende tabel worden beleidsregels en regels vergeleken:


| Onderwerp | Beleid  | Regels |
| ------- | ------- | ----- |
|Contains     |NAT-, netwerk-, en toepassingsregels, aangepaste DNS- en DNS-proxyinstellingen, IP-groepen, en instellingen voor Bedreigingsinformatie (inclusief acceptatielijst)|NAT-, netwerk-, en toepassingsregels, aangepaste DNS- en DNS-proxyinstellingen, IP-groepen, en instellingen voor Bedreigingsinformatie (inclusief acceptatielijst)|
|Beschermt     |Virtuele hubs en virtuele netwerken|Alleen virtuele netwerken|
|Portalervaring     |Centraal beheer met behulp van Firewall Manager|Zelfstandige firewallervaring|
|Ondersteuning voor meerdere firewalls     |Firewall-beleid is een afzonderlijke resource die kan worden gebruikt in verschillende firewalls|Handmatig regels exporteren en importeren, of beheeroplossingen van derden gebruiken |
|Prijzen     |Gefactureerd op basis van firewallkoppeling. Zie [Prijzen](#pricing).|Gratis|
|Ondersteunde implementatiemechanismen     |Portal, REST API, sjablonen, Azure PowerShell en CLI|Portal, REST API, sjablonen, PowerShell en CLI. |

## <a name="pricing"></a>Prijzen

Beleid wordt gefactureerd op basis van firewallkoppelingen. Beleid met nul of één firewallkoppeling is gratis. Beleid met meerdere firewallkoppelingen wordt gefactureerd tegen een vast bedrag. Zie [Prijzen voor Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u wilt leren hoe u een Azure-firewall implementeert, raadpleegt u [Zelfstudie: uw cloudnetwerk beveiligen met Azure Firewall Manager met behulp van de Azure-portal](secure-cloud-network.md).
