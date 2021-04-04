---
title: 'Scenario: aangepaste isolatie voor VNets'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: hiermee voor komt u dat geselecteerde VNets elkaar kunnen bereiken"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92461818"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenario: aangepaste isolatie voor VNets

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In een aangepast isolatie scenario voor VNets is het doel om te voor komen dat specifieke sets van VNets een andere set VNets kunnen bereiken. De VNets zijn echter vereist om alle branches te bereiken (VPN/er/gebruikers-VPN). Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="design"></a><a name="design"></a>Ontwerp

Als u wilt weten hoeveel route tabellen er nodig zijn, kunt u een verbindings matrix maken. In dit scenario ziet het er als volgt uit, waarbij elke cel aangeeft of een bron (rij) kan communiceren met een doel (kolom):

| Van | Aan:| *Blue VNets* | *Rode VNets* | *Vertakkingen*|
|---|---|---|---|---|
| **Blue VNets** |   &#8594;|   Direct     |           |  Direct |
| **Rode VNets**  |   &#8594;|              |   Direct  |  Direct |
| **Vertakkingen**   |   &#8594;|   Direct     |   Direct  |  Direct |

Elk van de cellen in de vorige tabel beschrijft of een virtuele WAN-verbinding (de ' aan ' kant van de stroom, de rijkoppen) communiceert met een bestemming (de ' aan '-zijde van de stroom, de kolom koppen cursief). In dit scenario zijn er geen firewalls of virtuele netwerk apparaten, waardoor communicatie rechtstreeks via Virtual WAN (dus het woord ' direct ' in de tabel) verloopt.

Het aantal verschillende rijtypen is het aantal route tabellen dat in dit scenario nodig is. In dit geval zijn er drie route route tabellen die we **RT_BLUE** en **RT_RED** aanroepen voor de virtuele netwerken, en de **standaard** voor de vertakkingen. Houd er rekening mee dat de branches altijd moeten worden gekoppeld aan de standaard routerings tabel.

De vertakkingen moeten de voor voegsels van zowel de rode als de blauwe VNets leren, dus alle VNets moeten worden door gegeven aan de standaard waarde (ook **RT_BLUE** of **RT_RED**). Blauw en rood VNets moeten de vertakkings voorvoegsels leren kennen, zodat vertakkingen worden door gegeven aan zowel route tabellen **RT_BLUE** als **RT_RED** . Als gevolg hiervan is dit het laatste ontwerp:

* Blauwe virtuele netwerken:
  * Gekoppelde route tabel: **RT_BLUE**
  * Door geven aan route tabellen: **RT_BLUE** en **standaard**
* Rode virtuele netwerken:
  * Gekoppelde route tabel: **RT_RED**
  * Door geven aan route tabellen: **RT_RED** en **standaard**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **RT_BLUE**, **RT_RED** en **standaard**

> [!NOTE]
> Omdat alle vertakkingen moeten worden gekoppeld aan de standaard route tabel, en om te worden door gegeven aan dezelfde set routerings tabellen, hebben alle vertakkingen hetzelfde connectiviteits profiel. Met andere woorden, het concept rood/blauw voor VNets kan niet worden toegepast op branches.

> [!NOTE]
> Als uw virtuele WAN wordt geïmplementeerd in meerdere regio's, moet u de **RT_BLUE** en **RT_RED** route tabellen maken in elke hub, en routes van elke VNet-verbinding moeten worden door gegeven aan de route tabellen in elke virtuele hub met behulp van door gegeven labels.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="workflow"></a><a name="architecture"></a>Werkstroom

In **afbeelding 1** zijn er blauwe en rode VNet-verbindingen.

* Blue-verbonden VNets kunnen elkaar bereiken en alle vertakkingen (VPN/er/P2S) bereiken.
* Rode VNets kan elkaar bereiken en alle filialen (VPN/er/P2S)-verbindingen bereiken.

Houd rekening met de volgende stappen bij het instellen van route ring.

1. Maak twee aangepaste route tabellen in de Azure Portal, **RT_BLUE** en **RT_RED**.
2. Voor route tabel **RT_BLUE** voor de volgende instellingen:
   * **Koppeling**: Selecteer alle Blue VNets.
   * **Doorgifte**: voor vertakkingen selecteert u de optie voor branches, die vertakkingen (VPN/er/P2S)-verbindingen geven, worden routes door gegeven aan deze route tabel.
3. Herhaal dezelfde stappen voor **RT_RED** route tabel voor rode VNets en vertakkingen (VPN/er/P2S).

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals in de onderstaande afbeelding wordt weer gegeven

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="afbeelding 1":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
