---
title: 'Scenario: verkeer routeren via een virtueel netwerk apparaat (NVA)'
titleSuffix: Azure Virtual WAN
description: Verkeer routeren via een NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519793"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenario: Verkeer routeren via een NVA

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit NVA-scenario is het doel om verkeer te routeren via een NVA (virtueel netwerk apparaat) voor vertakking naar VNet en VNet naar vertakking. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

> [!NOTE]
> Als u al een configuratie hebt met routes die ouder zijn dan de nieuwe functies [voor het configureren van virtuele-hub-route ring](how-to-virtual-hub-routing.md) beschikbaar, gebruikt u de stappen in deze artikelen:
>* [Azure Portal artikel](virtual-wan-route-table-nva-portal.md)
>* [Power shell-artikel](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Ontwerp

In dit scenario gebruiken we de naamgevings Conventie:

* ' NVA VNets ' voor virtuele netwerken waarin gebruikers een NVA hebben geïmplementeerd en andere virtuele netwerken hebben verbonden als spokes (VNet 2 en VNet 4 in de **afbeelding 2** verderop in het artikel).
* ' NVA spokes ' voor virtuele netwerken die zijn verbonden met een NVA VNet (VNet 5, VNet 6, VNet 7 en VNet 8 in de **afbeelding 2** verderop in het artikel).
* ' Non-NVA VNets ' voor virtuele netwerken die zijn verbonden met een virtueel WAN zonder NVA of een andere VNets die hieraan zijn gekoppeld (VNet 1 en VNet 3 in de **afbeelding 2** verderop in het artikel).
* Hubs voor door micro soft beheerde virtuele WAN-hubs, waarbij NVA VNets zijn verbonden. NVA spoke VNets hoeft niet te zijn verbonden met virtuele WAN-hubs, alleen op NVA VNets.

De volgende verbindings matrix bevat een overzicht van de stromen die in dit scenario worden ondersteund:

**Verbindings matrix**

| Van             | Aan:|   *NVA-spokes*|*NVA VNets*|*Niet-NVA-VNets*|*Vertakkingen*|
|---|---|---|---|---|---|
| **NVA-spokes**   | &#8594; | Meer dan NVA VNet | Peering | Meer dan NVA VNet | Meer dan NVA VNet |
| **NVA VNets**    | &#8594; | Peering | Direct | Direct | Direct |
| **Niet-NVA-VNets**| &#8594; | Meer dan NVA VNet | Direct | Direct | Direct |
| **Vertakkingen**     | &#8594; | Meer dan NVA VNet | Direct | Direct | Direct |

Elk van de cellen in de verbindings matrix beschrijft hoe een VNet of vertakking (de ' van ' kant van de stroom, de rijkoppen in de tabel) communiceert met een doel-VNet of vertakking (de ' aan ' kant van de stroom, de kolom koppen in cursief in de tabel). ' Direct ' betekent dat de connectiviteit systeem eigen wordt ondersteund door Virtual WAN, ' peering ' betekent dat de connectiviteit wordt verschaft door een User-Defined route in het VNet, "via NVA VNet" betekent dat de connectiviteit de NVA implementeert die in het NVA VNet is geïmplementeerd. Overweeg de volgende:

* NVA-spokes worden niet beheerd door virtuele WAN. Als gevolg hiervan worden de mechanismen waarmee ze zullen communiceren met andere VNets of vertakkingen door de gebruiker onderhouden. Connectiviteit met het NVA VNet wordt gegeven door een VNet-peering en een standaard route naar 0.0.0.0/0 die verwijst naar de NVA als volgende hop moet verbinding met internet hebben, andere spokes en vertakkingen
* NVA VNets kent hun eigen NVA-spokes, maar niet over NVA-spokes die zijn verbonden met andere NVA VNets. In de afbeelding 2 verderop in dit artikel weet VNet 2 bijvoorbeeld over VNet 5 en VNet 6, maar niet over andere spokes, zoals VNet 7 en VNet 8. Er is een statische route vereist om andere voor voegsels van spokes te injecteren in NVA VNets
* Daarnaast hebben vertakkingen en niet-NVA VNets geen NVA-spoke, omdat NVA spokes geen verbinding hebben met virtuele WAN-hubs. Als gevolg hiervan zijn er ook statische routes nodig.

Als rekening wordt gehouden met de NVA-spokes worden niet beheerd door virtuele WAN, worden in alle andere rijen hetzelfde connectiviteits patroon weer gegeven. Als gevolg hiervan wordt een enkele route tabel (de standaard instelling):

* Virtuele netwerken (niet-hub-VNets en gebruikers-hub-VNets):
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**

In dit scenario moeten we echter nadenken over welke statische routes moeten worden geconfigureerd. Elke statische route heeft twee onderdelen: een deel van de virtuele WAN-hub geeft de virtuele WAN-onderdelen aan die verbinding moeten maken voor elke spoke, en een andere in die specifieke verbinding die verwijst naar het concreet IP-adres dat is toegewezen aan de NVA (of op een load balancer vóór meerdere Nva's). in **afbeelding 1** ziet u het volgende:

**Afbeelding 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Afbeelding 1":::

De statische routes die we nodig hebben in de standaard tabel om verkeer te verzenden naar de NVA-spokes achter de NVA VNet, zijn als volgt:

| Description | Routetabel | Statische route              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Standaard     | 10.2.0.0/16-> eastusconn |
| VNet 4       | Standaard     | 10.4.0.0/16-> weconn     |

Het virtuele WAN weet nu naar welke verbinding de pakketten moeten worden verzonden, maar de verbinding moet weten wat er moet gebeuren wanneer deze pakketten worden ontvangen: hier worden de verbindings route tabellen gebruikt. Hier gaan we de kortere voor voegsels (/24 in plaats van de meer/16) gebruiken om ervoor te zorgen dat deze routes de voor keur hebben boven routes die worden geïmporteerd uit de NVA VNets (VNet 2 en VNet 4):

| Description | Verbinding | Statische route            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0.5 |

Nu NVA VNets, niet-NVA VNets en filialen weten hoe u alle NVA-spokes kunt bereiken. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

In **afbeelding 2** zijn er twee hubs; **Hub1** en **Hub2**.

* **Hub1** en **Hub2** zijn rechtstreeks verbonden met NVA VNets **VNet 2** en **VNet 4**.

* **Vnet 5** en **vnet 6** zijn gekoppeld aan **vnet 2**.

* **Vnet 7** en **vnet 8** zijn gekoppeld aan **vnet 4**.

* **VNets 5, 6, 7, 8** zijn indirecte spokes, niet rechtstreeks verbonden met een virtuele hub.

**Afbeelding 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Afbeelding 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Als u route ring via NVA wilt instellen, kunt u overwegen de volgende stappen uit te voeren:

1. Identificeer de NVA spoke VNet-verbinding. In **afbeelding 2** zijn dit **VNet 2-verbinding (Eastusconn)** en **vnet 4-verbinding (weconn)**.

   Zorg ervoor dat er Udr's zijn ingesteld:
   * Van VNet 5 en VNet 6 tot VNet 2 NVA IP
   * Van VNet 7 en VNet 8 tot VNet 4 NVA IP 
   
   U hoeft niet rechtstreeks verbinding te maken met VNets 5, 6, 7 en 8 naar de virtuele hubs. Zorg ervoor dat Nsg's in VNets 5, 6, 7, 8 verkeer toestaan voor Branch (VPN/er/P2S) of VNets die is verbonden met hun externe VNets. VNets 5, 6 moet er bijvoorbeeld voor zorgen dat Nsg's verkeer toestaat voor on-premises adres voorvoegsels en VNets 7, 8 die zijn verbonden met de externe hub 2.

Virtual WAN biedt geen ondersteuning voor een scenario waarbij VNets 5, 6 verbinding maakt met de virtuele hub en communiceert via VNet 2 NVA IP; Daarom moet u verbinding maken tussen VNets 5, 6 en VNet2, en ook VNet 7, 8 tot VNet 4.

2. Voeg een geaggregeerde statische route vermelding toe voor de standaard route tabel van VNets 2, 5, 6 naar hub 1.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Voorbeeld":::

3. Configureer een statische route voor VNets 5, 6 in de virtuele netwerk verbinding van VNet 2. Zie [virtuele hub-route ring](how-to-virtual-hub-routing.md#routing-configuration)voor het instellen van routerings configuratie voor een virtuele netwerk verbinding.

4. Voeg een geaggregeerde statische route vermelding toe voor de standaard route tabel van VNets 4, 7, 8 naar hub 1.

5. Herhaal stap 2, 3 en 4 voor de standaard route tabel van hub 2.

Dit heeft tot gevolg dat de routerings configuratie verandert, zoals wordt weer gegeven in **afbeelding 3** hieronder.

**Afbeelding 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Afbeelding 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
