---
title: Verkeer tussen Application Gateway-en back-end-Pools beveiligen
titleSuffix: Azure Virtual WAN
description: Scenario's voor het routeren van beveiligde verkeer dat wordt gereist via een toepassings gateway die is geïmplementeerd in een spoke-VNet dat is verbonden met een beveiligde virtuele WAN-hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315462"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Scenario: beveiligd verkeer tussen Application Gateway en back-end-Pools

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario voert het verkeer van de gebruiker Azure in via een toepassings gateway die is geïmplementeerd in een spoke-VNet dat is verbonden met een beveiligde virtuele WAN-hub (virtuele WAN-hub met een Azure Firewall). Het doel is om de Azure Firewall in de beveiligde virtuele hub te gebruiken om verkeer tussen de toepassings gateway en de back-endservers te controleren.

Dit scenario bevat twee specifieke ontwerp patronen, afhankelijk van het feit of de toepassings gateway en de back-endservers zich in hetzelfde VNet bevinden, of verschillende VNets.

* **Scenario 1:** De toepassings gateway en de back-end-Pools bevinden zich in hetzelfde virtuele netwerk dat is gekoppeld aan een virtuele WAN-hub (afzonderlijke subnetten).
* **Scenario 2:** De toepassings gateway en de back-end-Pools bevinden zich in verschillende virtuele netwerken die zijn gekoppeld aan een virtuele WAN-hub.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Scenario 1-hetzelfde VNet

In dit scenario bevinden de toepassings gateway en de back-end-pool zich in hetzelfde virtuele netwerk dat is gekoppeld aan een virtuele WAN-hub (afzonderlijke subnetten).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagram voor scenario 1." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Werkstroom

Op dit moment worden routes die worden geadverteerd vanuit de tabel virtuele WAN-route naar spoke virtuele netwerken toegepast op het gehele virtuele netwerk en niet op de subnetten van het spoke-VNet. Als gevolg hiervan zijn door de gebruiker gedefinieerde routes nodig om dit scenario in te scha kelen. Zie [Virtual Network aangepaste routes](../virtual-network/virtual-networks-udr-overview.md#user-defined)voor informatie over door de gebruiker gedefinieerde routes (UDR).


1. Selecteer in Azure Firewall Manager op het virtuele spoke-netwerk met de toepassings gateway en de back-endservers **beveiligde Internet verkeer inschakelen** en **beveiligd privé verkeer inschakelen**.
1. Configureer door de gebruiker gedefinieerde routes (Udr's) op het subnet van de toepassings gateway.

   * Geef de volgende UDR op om ervoor te zorgen dat de toepassings gateway rechtstreeks verkeer kan verzenden naar het Internet:

     * **Adres voorvoegsel:** 0.0.0.0.0/0
     * **Volgende hop:** Browser

   * Om ervoor te zorgen dat de toepassings gateway verkeer kan verzenden naar de back-end-groep via Azure Firewall in de virtuele WAN-hub, geeft u de volgende UDR op:

      * **Adres voorvoegsel:** Subnet van de back-end-groep (10.2.0.0/24)
      * **Volgende hop:** Azure Firewall particulier IP-adres

1. Een door de gebruiker gedefinieerde route (UDR) configureren op het subnet van de back-end-groep.

   * **Adres voorvoegsel:** Application Gateway subnet
   * **Volgende hop:** Azure Firewall particulier IP-adres

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Scenario 2-verschillende VNets

In dit scenario bevinden toepassings gateway-en back-endservers zich in verschillende virtuele netwerken die zijn gekoppeld aan een virtuele WAN-hub.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagram voor scenario 2." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Werkstroom

Op dit moment worden routes die worden geadverteerd vanuit de tabel virtuele WAN-route naar spoke virtuele netwerken toegepast op het gehele virtuele netwerk en niet op de subnetten van het spoke-VNet. Als gevolg hiervan zijn door de gebruiker gedefinieerde routes nodig om dit scenario in te scha kelen. Zie [Virtual Network aangepaste routes](../virtual-network/virtual-networks-udr-overview.md#user-defined)voor informatie over door de gebruiker gedefinieerde routes (UDR).

1. Selecteer in **Azure firewall Manager** **beveiligde Internet verkeer inschakelen** en **beveiligd privé verkeer inschakelen** op beide spoke-virtuele netwerken.

1. Configureer door de gebruiker gedefinieerde routes (Udr's) op het subnet van de toepassings gateway. Geef de volgende UDR op om ervoor te zorgen dat de toepassings gateway rechtstreeks verkeer kan verzenden naar het Internet:

   * **Adres voorvoegsel:** 0.0.0.0.0/0
   * **Volgende hop:** Browser

## <a name="next-steps"></a>Volgende stappen

* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
* Zie [Virtual Network Custom routes](../virtual-network/virtual-networks-udr-overview.md#user-defined)voor meer informatie over door de gebruiker gedefinieerde routes.
* Zie voor meer informatie over virtuele WAN-beveiligde virtuele hubs [beveiligde virtuele hubs](../firewall-manager/secured-virtual-hub.md).
