---
title: 'Scenario: aangepaste isolatie voor virtuele netwerken en vertakkingen'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: Voorkom dat geselecteerde VNets en vertakkingen elkaar kunnen bereiken"
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99408480"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Scenario: aangepaste isolatie voor virtuele netwerken en vertakkingen

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In een aangepast isolatie scenario voor beide virtuele netwerken (VNets) en filialen is het doel om te voor komen dat een specifieke set VNets een andere set VNets bereikt. Vertakkingen (VPN/er/gebruikers-VPN) zijn ook toegestaan om bepaalde sets van VNets te bereiken.

We introduceren ook de extra vereiste die Azure Firewall moet de vertakking controleren naar Vnet en vertakking naar vnet-verkeer, maar **niet**  op Vnet naar vnet-verkeer.  

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="design"></a><a name="design"></a>Ontwerp

Als u wilt weten hoeveel route tabellen er nodig zijn, kunt u een verbindings matrix maken. In dit scenario ziet het er als volgt uit, waarbij elke cel aangeeft of een bron (rij) kan communiceren met een doel (kolom):

| Van | Aan:| *Blue VNets* | *Rode VNets* | *Rode vertakkingen*| *Blauwe vertakkingen*| 
|---|---|---|---|---|---|
| **Blue VNets** |   &#8594;|   Direct     |           |   |  AzFW|
| **Rode VNets**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Rode vertakkingen**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Blauwe vertakkingen**| &#8594;| AzFW  |   |Direct   | Direct

Elk van de cellen in de vorige tabel beschrijft of een virtuele WAN-verbinding (de ' aan ' kant van de stroom, de rijkoppen) communiceert met een bestemming (de ' aan '-zijde van de stroom, de kolom koppen cursief). **Direct** impliceert dat het verkeer rechtstreeks via Virtual WAN loopt terwijl **AzFW** impliceert dat het verkeer wordt geïnspecteerd door Azure firewall voordat het wordt doorgestuurd naar de bestemming. Een lege vermelding betekent dat de stroom wordt geblokkeerd tijdens de installatie.

In dit geval zijn de twee route tabellen voor de VNets vereist om het doel van VNet-isolatie te verzorgen zonder Azure Firewall in het pad. Deze route tabellen worden dan **RT_BLUE** en **RT_RED** aangeroepen.

Daarnaast moeten vertakkingen altijd zijn gekoppeld aan de  **standaard** route tabel. Om ervoor te zorgen dat verkeer van en naar de vertakkingen wordt geïnspecteerd door Azure Firewall, voegen we statische routes toe in de **standaard**- **RT_RED** en **RT_BLUE** route tabellen die verkeer naar Azure firewall sturen en netwerk regels instellen om gewenst verkeer toe te staan. We zorgen er ook voor dat de vertakkingen **niet** worden door gegeven aan **RT_BLUE** en **RT_RED**.

Als gevolg hiervan is dit het laatste ontwerp:

* Blauwe virtuele netwerken:
  * Gekoppelde route tabel: **RT_BLUE**
  * Door geven aan route tabellen: **RT_BLUE**
* Rode virtuele netwerken:
  * Gekoppelde route tabel: **RT_RED**
  * Door geven aan route tabellen: **RT_RED** 
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**
* Statische routes:
    * **Standaard route tabel**: Virtual Network adres ruimten met de volgende hop Azure firewall
    * **RT_RED**: 0.0.0.0/0 met volgende hop Azure firewall
    * **RT_BLUE**: 0.0.0.0/0 met volgende hop Azure firewall
* Firewall-netwerk regels:
    * **Regel** **bron voorvoegsel** toestaan: blauw Branch-adres prefix **voor** voegsels: Blue VNet-voor voegsels 
    * **Regel**  **bron voorvoegsel** toestaan: rood vertakkings adres prefix **voor** voegsels: rode Vnet-voor voegsels

> [!NOTE]
> Omdat alle vertakkingen moeten worden gekoppeld aan de standaard route tabel, en om te worden door gegeven aan dezelfde set routerings tabellen, hebben alle vertakkingen hetzelfde connectiviteits profiel. Met andere woorden, het concept rood/blauw voor VNets kan niet worden toegepast op branches. Om aangepaste route ring voor branches te halen, kunnen we echter verkeer van de vertakkingen door sturen naar Azure Firewall.

> [!NOTE]
> Azure Firewall worden standaard geen verkeer in een model met een vertrouwens relatie geweigerd. Als er geen expliciete regel voor **toestaan** is die overeenkomt met het geinspectete pakket, wordt het pakket door Azure firewall verwijderd.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.



## <a name="workflow"></a><a name="architecture"></a>Werkstroom

In **afbeelding 1** zijn er blauw en rood VNets, evenals vertakkingen die toegang hebben tot de blauwe of rode VNets.

* Blue-verbonden VNets kunnen elkaar bereiken en kunnen alle Blue branches (VPN/er/P2S)-verbindingen bereiken. In het diagram is de blauwe vertakking de site-naar-site-VPN-site.
* Een rood verbonden VNets kan elkaar bereiken en kan alle rode branches (VPN/er/P2S)-verbindingen bereiken. In het diagram zijn de rode vertakkingen de punt-naar-site-VPN-gebruikers.

Houd rekening met de volgende stappen bij het instellen van route ring.

1. Maak twee aangepaste route tabellen in de Azure Portal, **RT_BLUE** en **RT_RED** om het verkeer naar deze VNets aan te passen.
2. Pas voor de route tabel **RT_BLUE** de volgende instellingen toe om ervoor te zorgen dat blauwe VNets de adres voorvoegsels van alle andere Blue VNets leren.:
   * **Koppeling**: Selecteer alle Blue VNets.
   * **Doorgifte**: Selecteer alle Blue VNets.
3. Herhaal dezelfde stappen voor **RT_RED** route tabel voor rode VNets.
4. Richt een Azure Firewall in voor virtueel WAN. Zie [configuring Azure firewall in Virtual WAN hub](howto-firewall.md)(Engelstalig) voor meer informatie over Azure firewall in de virtuele WAN-hub.
5. Voeg een statische route toe aan de **standaard** route tabel van de virtuele hub waarbij al het verkeer dat bestemd is voor de Vnet-adres ruimten (blauw en rood), wordt omgeleid naar Azure firewall. Met deze stap zorgt u ervoor dat alle pakketten van uw vertakkingen worden verzonden naar Azure Firewall voor inspectie.
    * Voor beeld: voor **voegsel voor bestemming**: 10.0.0.0/24 **volgende hop**: Azure firewall
    >[!NOTE]
    > Deze stap kan ook worden uitgevoerd via firewall Manager door de optie ' beveiligd privé verkeer ' te selecteren. Hiermee wordt een route toegevoegd voor alle RFC1918 privé IP-adressen die van toepassing zijn op VNets en branches. U moet hand matig toevoegen in branches of virtuele netwerken die niet compatibel zijn met RFC1918. 

6. Voeg een statische route toe aan **RT_RED** en **RT_BLUE** alle verkeer naar Azure firewall omleiden. Met deze stap zorgt u ervoor dat VNets niet rechtstreeks toegang heeft tot branches. Deze stap kan niet worden uitgevoerd via firewall Manager, omdat deze virtuele netwerken niet zijn gekoppeld aan de standaard route tabel.
    * Voor beeld: voor **voegsel voor bestemming**: 0.0.0.0/0 **Next Hop**: Azure firewall

    > [!NOTE]
    > Route ring wordt uitgevoerd met de langste voorvoegsel overeenkomst (LPM). Als gevolg hiervan hebben de statische routes van 0.0.0.0/0 **geen** voor keur boven de exacte voor voegsels die voor komen in **BLUE_RT** en **RED_RT**. Als gevolg hiervan wordt het intra Vnet-verkeer niet door Azure Firewall gecontroleerd.

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals in de afbeelding hieronder wordt weer gegeven.

**Afbeelding 1** 
 [ ![ Afbeelding 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
