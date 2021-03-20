---
title: Overzicht van een scenario voor herstel na nood geval van Oracle in uw Azure-omgeving | Microsoft Docs
description: Een scenario voor herstel na nood gevallen voor een Oracle Database 12c-data base in uw Azure-omgeving
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 68b5b9dfd205628c9d7c430df4c0230267752e01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669952"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Herstel na nood geval voor een Oracle Database 12c-data base in een Azure-omgeving

## <a name="assumptions"></a>Aannames

- U hebt een goed idee van het ontwerp van Oracle Data Guard en Azure-omgevingen.


## <a name="goals"></a>Doelstellingen
- Ontwerp de topologie en configuratie die voldoen aan de vereisten voor nood herstel (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: primaire en DR-sites op Azure

Een klant heeft een Oracle-data base die is ingesteld op de primaire site. Een DR-site bevindt zich in een andere regio. De klant gebruikt Oracle Data Guard voor snelle herstel tussen deze sites. De primaire site heeft ook een secundaire Data Base voor rapportage en andere doel einden. 

### <a name="topology"></a>Topologie

Hier volgt een overzicht van de installatie van Azure:

- Twee sites (een primaire site en een DR-site)
- Twee virtuele netwerken
- Twee Oracle-data bases met Data Guard (primair en stand-by)
- Twee Oracle-data bases met een gouden Gate of Data Guard (alleen primaire site)
- Twee toepassings Services, één primair en één op de DR-site
- Een *beschikbaarheidsset* die wordt gebruikt voor de data base-en toepassings service op de primaire site
- Eén JumpBox op elke site, die de toegang tot het particuliere netwerk beperkt en alleen aanmelding door een beheerder toestaat
- Een JumpBox, toepassings service, data base en VPN-gateway op afzonderlijke subnetten
- NSG afgedwongen op toepassings-en database subnetten

![Diagram waarin de primaire en DR-sites op Azure worden weer gegeven.](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: primaire site on-premises en DR-site op Azure

Een klant heeft een on-premises Oracle-data base-installatie (primaire site). Een DR-site bevindt zich in Azure. Oracle Data Guard wordt gebruikt voor snelle herstel tussen deze sites. De primaire site heeft ook een secundaire Data Base voor rapportage en andere doel einden. 

Er zijn twee benaderingen voor deze installatie.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Benadering 1: rechtstreekse verbindingen tussen on-premises en Azure, waardoor open TCP-poorten op de firewall zijn vereist 

We raden geen directe verbindingen aan omdat ze de TCP-poorten beschikbaar maken voor de buiten wereld.

#### <a name="topology"></a>Topologie

Hier volgt een samen vatting van de installatie van Azure:

- Eén DR-site 
- Eén virtueel netwerk
- Eén Oracle-data base met Data Guard (actief)
- Eén toepassings service op de DR-site
- Een JumpBox, waarmee de toegang wordt beperkt tot het particuliere netwerk en waarmee alleen aanmelding door een beheerder wordt toegestaan
- Een JumpBox, toepassings service, data base en VPN-gateway op afzonderlijke subnetten
- NSG afgedwongen op toepassings-en database subnetten
- Een NSG-beleid/regel voor het toestaan van binnenkomende TCP-poort 1521 (of een door de gebruiker gedefinieerde poort)
- Een NSG-beleid/regel voor het beperken van alleen het IP-adres/de lokale adressen (DB of toepassing) voor toegang tot het virtuele netwerk

![Diagram waarin de directe verbindingen tussen on-premises en Azure worden weer gegeven, waarbij open TCP-poorten op de firewall zijn vereist.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Benadering 2: site-naar-site-VPN
Site-naar-site-VPN is een betere benadering. Zie [een virtueel netwerk maken met een site-naar-site-VPN-verbinding met behulp van CLI](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)voor meer informatie over het instellen van een VPN.

#### <a name="topology"></a>Topologie

Hier volgt een samen vatting van de installatie van Azure:

- Eén DR-site 
- Eén virtueel netwerk 
- Eén Oracle-data base met Data Guard (actief)
- Eén toepassings service op de DR-site
- Een JumpBox, waarmee de toegang wordt beperkt tot het particuliere netwerk en waarmee alleen aanmelding door een beheerder wordt toegestaan
- Een JumpBox, toepassings service, data base en VPN-gateway bevinden zich op verschillende subnetten
- NSG afgedwongen op toepassings-en database subnetten
- Site-naar-site-VPN-verbinding tussen on-premises en Azure

![Scherm afbeelding van de pagina met de DR-topologie](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Meer artikelen

- [Een Oracle-data base ontwerpen en implementeren in azure](oracle-design.md)
- [Oracle Data Guard configureren](configure-oracle-dataguard.md)
- [Een Oracle Golden-Gate configureren](configure-oracle-golden-gate.md)
- [Oracle-back-up en-herstel](./oracle-overview.md)


## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: Maxi maal beschik bare Vm's maken](../../linux/create-cli-complete.md)
- [Azure CLI-voor beelden van VM-implementatie verkennen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)