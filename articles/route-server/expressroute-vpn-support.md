---
title: Over Azure route server (preview) ondersteunt voor ExpressRoute en Azure VPN
description: Meer informatie over hoe Azure route server communiceert met ExpressRoute en Azure VPN-gateways.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679971"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Over Azure route server (preview)-ondersteuning voor ExpressRoute en Azure VPN

Azure route server ondersteunt niet alleen virtuele netwerk apparaten van derden (NVA) die worden uitgevoerd op Azure, maar ook naadloos integreert met ExpressRoute en Azure VPN-gateways. Het is niet nodig om de BGP-peering tussen de gateway en de Azure route server te configureren of te beheren. U kunt route uitwisseling tussen de gateway en Azure route server inschakelen met een eenvoudige [configuratie wijziging](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> Azure route server (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="how-does-it-work"></a>Hoe werkt het?

Wanneer u een Azure route server implementeert in combi natie met een ExpressRoute-gateway en een NVA in een virtueel netwerk, worden de routes die worden ontvangen van de NVA-en ExpressRoute-gateway tussen elkaar niet door de Azure route server door gegeven. Zodra u de route Exchange ExpressRoute hebt ingeschakeld, worden de routes van elkaar door de NVA.

Bijvoorbeeld in het volgende diagram:

* Het SDWAN-apparaat wordt ontvangen van Azure route server de route van ' on-premises 2 ', die is verbonden met ExpressRoute, samen met de route van het virtuele netwerk.

* De ExpressRoute-Gateway ontvangt de route van ' on-premises 1 ', die is verbonden met het SDWAN-apparaat, samen met de route van het virtuele netwerk van Azure route server.

    ![Diagram met ExpressRoute geconfigureerd met route server.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

U kunt het SDWAN-apparaat ook vervangen door Azure VPN gateway. Omdat Azure VPN-gateway en ExpressRoute volledig worden beheerd, hoeft u alleen de route uitwisseling in te scha kelen voor de twee on-premises netwerken om met elkaar te communiceren.

> [!IMPORTANT] 
> Azure VPN-gateway moet worden geconfigureerd in de modus [**actief-actief**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) .
>

![Diagram met de ExpressRoute-en VPN-gateway die is geconfigureerd met de route server.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure route server](route-server-faq.md).
- Meer informatie over het [configureren van Azure route server](quickstart-configure-route-server-powershell.md).
- Meer informatie over [Azure ExpressRoute en Azure VPN-samen werking](../expressroute/expressroute-howto-coexist-resource-manager.md).
