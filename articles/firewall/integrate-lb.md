---
title: Azure Firewall integreren met Azure Standard Load Balancer
description: U kunt een Azure Firewall integreren in een virtueel netwerk met een Azure Standard Load Balancer (openbaar of intern).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505901"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure Firewall integreren met Azure Standard Load Balancer

U kunt een Azure Firewall integreren in een virtueel netwerk met een Azure Standard Load Balancer (openbaar of intern). 

Het ontwerp dat de voorkeur heeft, is het integreren van een interne load balancer met uw Azure-firewall omdat dit een veel eenvoudiger ontwerp is. U kunt een openbare load balancer gebruiken als u er al een hebt geïmplementeerd en u deze wilt behouden. U moet echter wel rekening houden met een bekend probleem met asymmetrische routering dat de functionaliteit van het scenario met een openbare load balancer kan verstoren.

Zie Wat is Azure Load Balancer? voor [Azure Load Balancer informatie over Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Openbare load balancer

Met een openbare load balancer wordt de load balancer geïmplementeerd met een openbaar front-end-IP-adres.

### <a name="asymmetric-routing"></a>Asymmetrisch routeren

Asymmetrische routering is waar een pakket één pad naar de bestemming neemt en een ander pad neemt wanneer het terugkeert naar de bron. Dit probleem treedt op wanneer een subnet een standaardroute heeft die naar het privé-IP-adres van de firewall gaat en u een openbare route load balancer. In dit geval wordt het binnenkomende load balancer ontvangen via het openbare IP-adres, maar loopt het retourpad via het privé-IP-adres van de firewall. Omdat de firewall stateful is, wordt het retourpakket niet meer ontvangen omdat de firewall niet op de hoogte is van een dergelijke tot stand gebrachte sessie.

### <a name="fix-the-routing-issue"></a>Het routeringsprobleem oplossen

Wanneer u een Azure Firewall implementeert in een subnet, moet u een standaardroute maken voor het subnet om pakketten door te sturen via het privé-IP-adres van de firewall dat zich in het AzureFirewallSubnet bevindt. Zie Zelfstudie: Implementatie en configuratie van Azure Firewall met [behulp van de Azure Portal.](tutorial-firewall-deploy-portal.md#create-a-default-route)

Wanneer u de firewall introduceert in uw load balancer scenario, wilt u dat uw internetverkeer binnen komt via het openbare IP-adres van uw firewall. Van hieraf past de firewall de firewallregels toe en nat de pakketten op het openbare IP-adres van uw load balancer van uw netwerk. Hier treedt het probleem op. Pakketten komen binnen op het openbare IP-adres van de firewall, maar keren terug naar de firewall via het privé-IP-adres (met behulp van de standaardroute).
Om dit probleem te voorkomen, maakt u een extra hostroute voor het openbare IP-adres van de firewall. Pakketten die naar het openbare IP-adres van de firewall gaan, worden via internet gerouteerd. Hiermee voorkomt u dat de standaardroute naar het privé-IP-adres van de firewall wordt gebruikt.

![Asymmetrisch routeren](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Voorbeeld van routetabel

De volgende routes zijn bijvoorbeeld voor een firewall op openbaar IP-adres 20.185.97.136 en privé-IP-adres 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Routetabel](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Voorbeeld van NAT-regel

In het volgende voorbeeld zet een NAT-regel RDP-verkeer om naar de firewall op 20.185.97.136 naar de load balancer op 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![NAT-regel](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Statuscontroles

Vergeet niet dat u een webservice moet hebben die wordt uitgevoerd op de hosts in de load balancer-pool als u TCP-statustests gebruikt voor poort 80- of HTTP/HTTPS-tests.

## <a name="internal-load-balancer"></a>Interne load balancer

Met een interne load balancer wordt de load balancer geïmplementeerd met een privé-front-end-IP-adres.

Er is geen asymmetrisch routeringsprobleem met dit scenario. De inkomende pakketten komen aan bij het openbare IP-adres van de firewall, worden omgezet in het privé-IP-adres van de load balancer en keren vervolgens terug naar het privé-IP-adres van de firewall met hetzelfde retourpad.

U kunt dit scenario dus implementeren zoals in het openbare load balancer, maar zonder de firewallroute voor de host van het openbare IP-adres.

De virtuele machines in de back-endpool kunnen uitgaande internetverbinding hebben via de Azure Firewall. Configureer een door de gebruiker gedefinieerde route op het subnet van de virtuele machine met de firewall als de volgende hop.


## <a name="additional-security"></a>Aanvullende beveiliging

Als u de beveiliging van uw scenario met load balanced verder wilt verbeteren, kunt u netwerkbeveiligingsgroepen (NSG's) gebruiken.

U kunt bijvoorbeeld een NSG maken op het back-endsubnet waar de virtuele machines met load-balanced zich bevinden. Sta binnenkomend verkeer toe dat afkomstig is van het IP-adres/de poort van de firewall.

![Netwerkbeveiligingsgroep](media/integrate-lb/nsg-01.png)

Zie Beveiligingsgroepen voor meer informatie [over NSG's.](../virtual-network/network-security-groups-overview.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md).