---
title: 'Scenario: VNets isoleren'
titleSuffix: Azure Virtual WAN
description: Scenario's voor het isoleren van VNets
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92424742"
---
# <a name="scenario-isolating-vnets"></a>Scenario: VNets isoleren

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om te voor komen dat VNets andere kunnen bereiken. Dit staat bekend als het isoleren van VNets. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="design"></a><a name="design"></a>Ontwerp

In dit scenario blijft de werk belasting binnen een bepaald VNet geïsoleerd en kan deze niet communiceren met andere VNets. De VNets zijn echter vereist om alle branches te bereiken (VPN, er en gebruikers-VPN). Als u wilt weten hoeveel route tabellen er nodig zijn, kunt u een verbindings matrix maken. In dit scenario ziet het eruit als in de volgende tabel, waarbij elke cel aangeeft of een bron (rij) kan communiceren met een doel (kolom):

| Van |   Tot |  *VNets* | *Vertakkingen* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;| Direct |   Direct    |
| Vertakkingen   | &#8594;|  Direct  |   Direct    |

Elk van de cellen in de vorige tabel beschrijft of een virtuele WAN-verbinding (de ' aan '-zijde van de stroom, de rijkoppen) communiceert met een voor voegsel van de bestemming (de ' aan ' kant van de stroom, de kolom koppen cursief). In dit scenario zijn er geen firewalls of virtuele netwerk apparaten, waardoor communicatie rechtstreeks via Virtual WAN (dus het woord ' direct ' in de tabel) verloopt.

Deze verbindings matrix bevat ons twee verschillende rijtypen, die worden omgezet in twee route tabellen. Virtuele WAN heeft al een standaard route tabel, dus er is een andere route tabel vereist. In dit voor beeld noemen we de route tabel **RT_VNET**.

VNets wordt gekoppeld aan deze route tabel van **RT_VNET** . Omdat de verbinding met branches vereist is, moeten vertakkingen worden door gegeven aan **RT_VNET** (anders zou de VNets de vertakkings voorvoegsels niet kunnen leren). Aangezien de vertakkingen altijd zijn gekoppeld aan de standaard route tabel, moet VNets worden door gegeven aan de standaard route tabel. Als gevolg hiervan is dit het laatste ontwerp:

* Virtuele netwerken:
  * Gekoppelde route tabel: **RT_VNET**
  * Door geven aan route tabellen: **standaard**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **RT_VNET** en **standaard**

U ziet dat omdat alleen vertakkingen worden door gegeven aan de route tabel **RT_VNET**, dat zijn de enige voor Voegsels die VNets leert en niet die van andere VNets.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Als u dit scenario wilt configureren, voert u de volgende stappen uit:

1. Maak een aangepaste route tabel in elke hub. In het voor beeld is de route tabel **RT_VNet**. Zie [virtuele hub-route ring configureren voor meer informatie over](how-to-virtual-hub-routing.md)het maken van een route tabel. Zie [about virtuele hub Routing](about-virtual-hub-routing.md)voor meer informatie over route tabellen.
2. Wanneer u de **RT_VNet** route tabel maakt, configureert u de volgende instellingen:

   * **Koppeling**: Selecteer de VNets die u wilt isoleren.
   * **Doorgifte**: Selecteer de optie voor vertakkingen, die vertakkingen (VPN/er/P2S)-verbindingen geven, worden routes door gegeven aan deze route tabel.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Geïsoleerde VNets":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
