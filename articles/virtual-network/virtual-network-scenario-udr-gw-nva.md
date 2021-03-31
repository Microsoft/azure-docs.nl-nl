---
title: Hybride verbinding met een toepassing met twee lagen | Microsoft Docs
description: Meer informatie over het implementeren van virtuele apparaten en UDR voor het maken van een toepassings omgeving met meerdere lagen in azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1d2dde4e77a39b114f721cd6d2be250141984e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231706"
---
# <a name="virtual-appliance-scenario"></a>Scenario voor virtueel apparaat
Een veelvoorkomend scenario tussen een grotere Azure-klant is de nood zaak om een toepassing met twee lagen te bieden die beschikbaar is op internet, terwijl toegang tot de back-uplaag wordt toegestaan vanuit een on-premises Data Center. Dit document begeleidt u door een scenario met behulp van door de gebruiker gedefinieerde routes (UDR), een VPN Gateway en virtuele netwerk apparaten om een omgeving met twee lagen te implementeren die voldoet aan de volgende vereisten:

* Webtoepassing moet alleen toegankelijk zijn vanaf het open bare Internet.
* Webserver die als host fungeert voor de toepassing moet toegang hebben tot een back-end-toepassings server.
* Al het verkeer van Internet naar de webtoepassing moet via een virtueel apparaat in de firewall gaan. Dit virtuele apparaat wordt alleen gebruikt voor Internet verkeer.
* Al het verkeer dat naar de toepassings server gaat, moet een virtueel apparaat voor een firewall hebben. Dit virtuele apparaat wordt gebruikt voor toegang tot de back-end-server en toegang vanaf het on-premises netwerk via een VPN Gateway.
* Beheerders moeten de virtuele firewall apparaten kunnen beheren vanaf hun lokale computers, door gebruik te maken van een virtueel apparaat van de tweede firewall dat uitsluitend wordt gebruikt voor beheer doeleinden.

Dit is een standaard perimeter netwerk (ook wel bekend als DMZ) met een DMZ en een beveiligd netwerk. Dit scenario kan worden gemaakt in azure met behulp van Nsg's, firewall virtuele apparaten of een combi natie van beide. In de volgende tabel ziet u enkele van de voor-en nadelen tussen Nsg's en firewall Virtual Appliances.

|  | Voordelen | Nadelen |
| --- | --- | --- |
| **NSG** |Geen kosten. <br/>Geïntegreerd in azure RBAC. <br/>Regels kunnen worden gemaakt in Azure Resource Manager sjablonen. |De complexiteit kan verschillen in grotere omgevingen. |
| **Firewall** |Volledige controle over het gegevens vlak. <br/>Centraal beheer via firewall-console. |Kosten van het firewall apparaat. <br/>Niet geïntegreerd met Azure RBAC. |

De onderstaande oplossing maakt gebruik van virtuele firewall apparaten voor het implementeren van een perimeter netwerk (DMZ)/Protected-netwerk scenario.

## <a name="considerations"></a>Overwegingen
U kunt de hierboven beschreven omgeving in azure implementeren met behulp van verschillende functies die momenteel beschikbaar zijn.

* **Virtueel netwerk (VNet)** . Een Azure-VNet fungeert op vergelijk bare wijze als een on-premises netwerk en kan worden gesegmenteerd in een of meer subnetten voor verkeer isolatie en een schei ding van de problemen.
* **Virtueel apparaat**. Verschillende partners bieden virtuele toepassingen op de Azure Marketplace die kunnen worden gebruikt voor de drie firewalls die hierboven worden beschreven. 
* Door de **gebruiker gedefinieerde routes (UDR)**. Route tabellen kunnen Udr's bevatten die door Azure-netwerken worden gebruikt om de stroom van pakketten binnen een VNet te beheren. Deze route tabellen kunnen worden toegepast op subnetten. Een van de nieuwste functies in Azure is de mogelijkheid om een route tabel toe te passen op de GatewaySubnet, waardoor al het verkeer dat binnenkomt in de Azure VNet kan worden doorgestuurd van een hybride verbinding naar een virtueel apparaat.
* **Door sturen via IP**. De Azure-netwerk engine stuurt standaard alleen pakketten naar virtuele netwerk interface kaarten (Nic's) als het IP-adres van het pakket doel overeenkomt met het IP-adres van de NIC. Als een UDR definieert dat een pakket moet worden verzonden naar een bepaald virtueel apparaat, wordt dat pakket door de Azure-netwerk engine verwijderd. Om ervoor te zorgen dat het pakket wordt geleverd aan een virtuele machine (in dit geval een virtueel apparaat) dat niet de daad werkelijke bestemming voor het pakket is, moet u door sturen via IP inschakelen voor het virtuele apparaat.
* **Netwerk beveiligings groepen (nsg's)**. In het onderstaande voor beeld wordt geen gebruik gemaakt van Nsg's, maar u kunt Nsg's die worden toegepast op de subnetten en/of Nic's in deze oplossing, gebruiken om het verkeer in en uit deze subnetten en Nic's verder te filteren.

![IPv6-connectiviteit](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

In dit voor beeld is er een abonnement met het volgende:

* 2 resource groepen, worden niet weer gegeven in het diagram. 
  * **ONPREMRG**. Bevat alle bronnen die nodig zijn voor het simuleren van een on-premises netwerk.
  * **AZURERG**. Bevat alle bronnen die nodig zijn voor de virtuele Azure-netwerk omgeving. 
* Een VNet met de naam **onpremvnet** gebruikt voor het simuleren van een on-premises Data Center, gesegmenteerd zoals hieronder wordt weer gegeven.
  * **onpremsn1**. Subnet met een virtuele machine (VM) met Ubuntu voor het simuleren van een on-premises server.
  * **onpremsn2**. Subnet met een virtuele machine met Ubuntu voor het nabootsen van een on-premises computer die wordt gebruikt door een beheerder.
* Er is één virtueel firewall-apparaat met de naam **OPFW** op **onpremvnet** gebruikt om een tunnel naar **azurevnet** te onderhouden.
* Een VNet met de naam **azurevnet** , gesegmenteerd zoals hieronder wordt weer gegeven.
  * **azsn1**. Subnet met externe firewall gebruikt uitsluitend voor de externe firewall. Al het Internet verkeer wordt via dit subnet geleverd. Dit subnet bevat alleen een NIC die is gekoppeld aan de externe firewall.
  * **azsn2**. Front-end-subnet als host voor een VM die wordt uitgevoerd als een webserver die toegankelijk is via internet.
  * **azsn3**. Back-end-subnet dat als host fungeert voor een virtuele machine waarop een back-end-toepassings server wordt uitgevoerd die wordt gebruikt door de front-endwebserver.
  * **azsn4**. Het beheer subnet wordt uitsluitend gebruikt om beheer toegang te bieden tot alle virtuele firewall apparaten. Dit subnet bevat alleen een NIC voor elk virtueel apparaat in de firewall dat in de oplossing wordt gebruikt.
  * **GatewaySubnet**. Azure Hybrid Connection-subnet vereist voor ExpressRoute en VPN Gateway om connectiviteit tussen Azure VNets en andere netwerken mogelijk te maken. 
* Het **azurevnet** -netwerk bevat drie virtuele firewall apparaten. 
  * **AZF1**. Externe firewall blootgesteld aan het open bare Internet door gebruik te maken van een open bare IP-adres resource in Azure. U moet ervoor zorgen dat u een sjabloon hebt van de Marketplace of rechtstreeks van de leverancier van uw apparaat, die voorziet in een virtueel apparaat met drie NIC'S.
  * **AZF2**. Interne firewall gebruikt voor het beheren van verkeer tussen **azsn2** en **azsn3**. Dit is ook een virtueel apparaat met drie NIC'S.
  * **AZF3**. Beheer firewall toegankelijk voor beheerders vanuit het on-premises Data Center en verbonden met een beheer subnet dat wordt gebruikt voor het beheren van alle firewall apparaten. U kunt twee virtuele NIC-sjablonen vinden in Marketplace of een directe aanvraag aanvragen bij de leverancier van uw apparaat.

## <a name="user-defined-routing-udr"></a>Door de gebruiker gedefinieerde route ring (UDR)
Elk subnet in azure kan worden gekoppeld aan een UDR-tabel die wordt gebruikt om te definiëren hoe verkeer dat in dat subnet wordt gestart, wordt doorgestuurd. Als er geen Udr's zijn gedefinieerd, gebruikt Azure de standaard routes om verkeer van het ene naar het andere subnet te laten stromen. Ga naar wat zijn door de [gebruiker gedefinieerde routes en door sturen via IP](virtual-networks-udr-overview.md)om meer inzicht te krijgen in udr's.

Om ervoor te zorgen dat de communicatie wordt uitgevoerd via het juiste firewall apparaat, op basis van de bovenstaande vereisten, moet u de volgende route tabel maken met Udr's in **azurevnet**.

### <a name="azgwudr"></a>azgwudr
In dit scenario wordt het enige verkeer dat van on-premises naar Azure stroomt, gebruikt om de firewalls te beheren door verbinding te maken met **AZF3** en dat verkeer via de interne firewall, **AZF2**. Daarom is er slechts één route nodig in de **GatewaySubnet** , zoals hieronder wordt weer gegeven.

| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Staat on-premises verkeer toe om beheer Firewall **AZF3** te bereiken |

### <a name="azsn2udr"></a>azsn2udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Hiermee staat u toe dat verkeer naar het back-end-subnet met de toepassings server wordt gehost via **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Hiermee staat u toe dat alle andere verkeer via **AZF1** wordt doorgestuurd |

### <a name="azsn3udr"></a>azsn3udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Hiermee staat u toe dat verkeer **azsn2** van de app-server naar de webserver via **AZF2** |

U moet ook route tabellen maken voor de subnetten in **onpremvnet** om het on-premises Data Center te simuleren.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Hiermee staat u toe dat verkeer **onpremsn2** via **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Doel | Volgende hop | Uitleg |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Hiermee wordt verkeer naar het ondersteunde subnet in azure toegestaan via **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Hiermee staat u toe dat verkeer **onpremsn1** via **OPFW** |

## <a name="ip-forwarding"></a>Doorsturen via IP
UDR en door sturen via IP zijn functies die u in combi natie kunt gebruiken om virtuele apparaten toe te staan om de verkeers stroom in een Azure-VNet te beheren.  Een virtueel apparaat is niets meer dan een virtuele machine waarop een toepassing wordt uitgevoerd om netwerkverkeer op een bepaalde manier af te handelen, zoals een firewall of een NAT-apparaat.

Deze VM op het virtuele apparaat moet in staat zijn om binnenkomend verkeer te ontvangen dat niet is geadresseerd aan zichzelf. Om met een VM verkeer te kunnen ontvangen dat is geadresseerd aan andere bestemmingen, moet u Doorsturen via IP inschakelen voor die VM. Dit is een Azure-instelling, niet een instelling in het gastbesturingssysteem. Het virtuele apparaat moet nog steeds een bepaald type toepassing uitvoeren om het binnenkomende verkeer te verwerken en dit op de juiste wijze te routeren.

Zie [Wat zijn door de gebruiker gedefinieerde routes en door sturen via IP](virtual-networks-udr-overview.md)voor meer informatie over het door sturen van IP-adressen.

Stel dat u de volgende installatie in een Azure-vnet hebt:

* Het subnet **onpremsn1** bevat een virtuele machine met de naam **onpremvm1**.
* Het subnet **onpremsn2** bevat een virtuele machine met de naam **onpremvm2**.
* Een virtueel apparaat met de naam **OPFW** is verbonden met **onpremsn1** en **onpremsn2**.
* Een door de gebruiker gedefinieerde route die is gekoppeld aan **onpremsn1** geeft aan dat alle verkeer naar **onpremsn2** moet worden verzonden naar **OPFW**.

Als **onpremvm1** probeert een verbinding met **onpremvm2** tot stand te brengen, wordt het UDR gebruikt en wordt het verkeer naar **OPFW** verzonden als de volgende hop. Houd er rekening mee dat de daad werkelijke pakket bestemming niet wordt gewijzigd, de melding **onpremvm2** is de bestemming. 

Als door sturen via IP is ingeschakeld voor **OPFW**, worden de pakketten verwijderd door de logica van het virtuele Azure-netwerk, omdat alleen pakketten kunnen worden verzonden naar een virtuele machine als het IP-adres van de virtuele machine het doel is voor het pakket.

Met door sturen via IP worden de pakketten door de logica van het virtuele Azure-netwerk doorgestuurd naar OPFW, zonder dat het oorspronkelijke doel adres wordt gewijzigd. **OPFW** moet de pakketten afhandelen en bepalen wat er ermee moet worden gedaan.

Voor het bovenstaande scenario moet u door sturen via IP inschakelen op de Nic's voor **OPFW**, **AZF1**, **AZF2** en **AZF3** die worden gebruikt voor route ring (alle nic's behalve de netwerk interface kaarten die zijn gekoppeld aan het subnet van het beheer). 

## <a name="firewall-rules"></a>Firewallregels
Zoals hierboven beschreven, wordt door sturen via IP alleen gegarandeerd dat pakketten worden verzonden naar de virtuele apparaten. Uw apparaat moet nog bepalen wat u met deze pakketten moet doen. In het bovenstaande scenario moet u de volgende regels maken in uw apparaten:

### <a name="opfw"></a>OPFW
OPFW vertegenwoordigt een on-premises apparaat dat de volgende regels bevat:

* **Route**: alle verkeer naar 10.0.0.0/16 (**azurevnet**) moet worden verzonden via tunnel **ONPREMAZURE**.
* **Beleid**: alle bidirectionele verkeer tussen **port2** en **ONPREMAZURE** toestaan.

### <a name="azf1"></a>AZF1
AZF1 vertegenwoordigt een virtueel Azure-apparaat dat de volgende regels bevat:

* **Beleid**: alle bidirectionele verkeer tussen **port1** en **port2** toestaan.

### <a name="azf2"></a>AZF2
AZF2 vertegenwoordigt een virtueel Azure-apparaat dat de volgende regels bevat:

* **Route**: alle verkeer naar 10.0.0.0/16 (**onpremvnet**) moet worden verzonden naar het IP-adres van de Azure-gateway (bijvoorbeeld 10.0.0.1) via **port1**.
* **Beleid**: alle bidirectionele verkeer tussen **port1** en **port2** toestaan.

## <a name="network-security-groups-nsgs"></a>Netwerkbeveiligingsgroepen (NSG's)
In dit scenario worden Nsg's niet gebruikt. U kunt echter Nsg's Toep assen op elk subnet om inkomend en uitgaand verkeer te beperken. U kunt bijvoorbeeld de volgende NSG-regels Toep assen op het externe FW-subnet.

**Binnenkomende**

* Sta alle TCP-verkeer van Internet toe aan poort 80 op elke virtuele machine in het subnet.
* Alle andere verkeer van Internet weigeren.

**Verzendt**

* Alle verkeer naar Internet weigeren.

## <a name="high-level-steps"></a>Stappen op hoog niveau
Als u dit scenario wilt implementeren, volgt u de stappen op hoog niveau hieronder.

1. Meld u aan bij uw Azure-abonnement.
2. Als u een VNet wilt implementeren om het on-premises netwerk te simuleren, richt u de resources in die deel uitmaken van **ONPREMRG**.
3. Richt de resources in die deel uitmaken van **AZURERG**.
4. Richt de tunnel in van **onpremvnet** naar **azurevnet**.
5. Wanneer alle resources zijn ingericht, meldt u zich aan bij **onpremvm2** en ping 10.0.3.101 om de connectiviteit tussen **onpremsn2** en **azsn3** te testen.

