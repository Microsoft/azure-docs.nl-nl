---
title: 'Scenario: Azure Firewall aangepaste route ring voor virtuele WAN'
titleSuffix: Azure Virtual WAN
description: Scenario's voor het direct routeren van verkeer tussen VNets, maar het gebruik van Azure Firewall voor VNet->Internet/Branch en branch naar VNet-verkeer stromen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: d083607782f96744ecbd7d23976f77ee53fec49d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94515566"
---
# <a name="scenario-azure-firewall---custom"></a>Scenario: Azure Firewall-aangepast

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om verkeer tussen VNets rechtstreeks te routeren, maar Azure Firewall voor VNet-naar-Internet/Branch-en vertakking-naar-VNet-verkeer.

## <a name="design"></a><a name="design"></a>Ontwerp

Als u wilt weten hoeveel route tabellen er nodig zijn, kunt u een verbindings matrix maken waarbij elke cel aangeeft of een bron (rij) kan communiceren met een doel (kolom). De connectiviteits matrix in dit scenario is trivial, maar is consistent met andere scenario's, maar we kunnen er nog steeds kijken.

**Verbindings matrix**

| Van           | Aan:      | *VNets*      | *Vertakkingen*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **VNets**      |   &#8594;|    Direct    |     AzFW      |     AzFW     |
| **Vertakkingen**   |   &#8594;|    AzFW      |    Direct     |    Direct    |

In de vorige tabel vertegenwoordigt een ' directe ' directe connectiviteit tussen twee verbindingen zonder het verkeer dat de Azure Firewall in virtuele WAN gaat passeren, en ' AzFW ' geeft aan dat de stroom de Azure Firewall doorloopt. Omdat er twee verschillende verbindings patronen in de matrix zijn, hebben we twee route tabellen nodig die als volgt worden geconfigureerd:

* Virtuele netwerken:
  * Gekoppelde route tabel: **RT_VNet**
  * Door geven aan route tabellen: **RT_VNet**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**

> [!NOTE]
> U kunt een afzonderlijk virtueel WAN-exemplaar maken met één beveiligde virtuele hub in elke regio, en vervolgens kunt u elk virtueel WAN met elkaar verbinden via site-naar-site-VPN.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

In dit scenario wilt u verkeer routeren via de Azure Firewall voor VNet-naar-Internet-, VNet-naar-vertakking-of vertakkings-naar-VNet-verkeer, maar graag direct voor VNet-naar-VNet-verkeer. Als u Azure Firewall Manager hebt gebruikt, worden de route-instellingen automatisch ingevuld in de **standaard route tabel**. Privé verkeer is van toepassing op VNet en filialen, Internet verkeer is van toepassing op 0.0.0.0/0.

VPN-, ExpressRoute-en gebruikers-VPN-verbindingen worden gezamenlijk vertakkingen genoemd en aan dezelfde (standaard) route tabel gekoppeld. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen. Als u dit scenario wilt configureren, voert u de volgende stappen uit:

1. Een aangepaste route tabel maken **RT_VNet**.
1. Maak een route om VNet-naar-Internet en VNet-naar-Branch: 0.0.0.0/0 te activeren met de volgende hop die naar Azure Firewall wijst. In het gedeelte doorgifte kunt u ervoor zorgen dat VNets zijn geselecteerd die ervoor zorgen dat er meer specifieke routes zijn, waardoor het VNet-naar-VNet direct-verkeers stroom wordt toegestaan.

   * In **Association:** Selecteer VNets die het doel van VNets bereikt volgens de routes van deze route tabel.
   * Bij **doorgifte:** Selecteer VNets die de VNets door geven aan deze route tabel. met andere woorden, meer specifieke routes worden door gegeven aan deze route tabel, waardoor direct verkeer tussen VNet en VNet wordt gegarandeerd.

1. Voeg een geaggregeerde statische route voor VNets toe aan de **standaard route tabel** om de vertakking-naar-VNet-stroom via de Azure firewall te activeren.

   * Houd er rekening mee dat branches zijn gekoppeld aan en worden door gegeven aan de standaard route tabel.
   * Vertakkingen worden niet door gegeven aan RT_VNet route tabel. Dit zorgt voor de overdracht van het VNet-naar-vertakkings verkeer via de Azure Firewall.

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals weer gegeven in **afbeelding 1**.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Afbeelding 1":::

> [!NOTE]
> De virtuele WAN-hubs en de verbonden virtuele netwerken moeten zich in dezelfde Azure-regio bevinden.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
* Zie [virtuele hub-route ring configureren](how-to-virtual-hub-routing.md)voor meer informatie over het configureren van virtuele hub-route ring.
