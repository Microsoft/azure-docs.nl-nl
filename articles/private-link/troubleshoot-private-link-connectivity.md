---
title: Problemen met de connectiviteit van Azure Private Link oplossen
description: Stapsgewijze instructies voor het vaststellen van de connectiviteit van particuliere koppelingen
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95544306"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Problemen met de connectiviteit van Azure Private Link oplossen

In dit artikel vindt u stapsgewijze richt lijnen voor het valideren en diagnosticeren van connectiviteit voor uw persoonlijke instellingen van Azure.

Met de persoonlijke Azure-koppeling kunt u Azure Platform as a Service-Services (PaaS) openen, zoals Azure Storage, Azure Cosmos DB en Azure SQL Database, en Azure hostende klanten-of partner services via een persoonlijk eind punt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de service gaat over het micro soft backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen. U kunt ook uw eigen persoonlijke koppelings service in uw virtuele netwerk maken en deze privé leveren aan uw klanten.

U kunt uw service inschakelen die wordt uitgevoerd achter de standaardlaag van Azure Load Balancer voor toegang tot persoonlijke koppelingen. Consumenten van uw service kunnen een persoonlijk eind punt in hun virtuele netwerk maken en dit koppelen aan deze service om het privé te benaderen.

Hier volgen de verbindings scenario's die beschikbaar zijn met een persoonlijke koppeling:

- Virtueel netwerk uit dezelfde regio
- Regionaal peered virtuele netwerken
- Wereld wijd gekoppelde virtuele netwerken
- On-premises klant via VPN-of Azure ExpressRoute-circuits

## <a name="deployment-troubleshooting"></a>Problemen met implementatie oplossen

Lees de informatie over het [uitschakelen van netwerk beleid op de privé koppelings service](./disable-private-link-service-network-policy.md) voor het oplossen van problemen waarbij u het bron-IP-adres niet kunt selecteren in het subnet van uw keuze voor uw persoonlijke koppelings service.

Zorg ervoor dat de instelling **privateLinkServiceNetworkPolicies** is uitgeschakeld voor het subnet waarmee u het bron-IP-adres selecteert.

## <a name="diagnose-connectivity-problems"></a>Verbindings problemen vaststellen

Als u verbindings problemen ondervindt met het instellen van uw persoonlijke koppelingen, lees dan deze stappen om ervoor te zorgen dat alle gebruikelijke configuraties naar verwachting worden uitgevoerd.

1. Bekijk de configuratie van de persoonlijke koppeling door te bladeren door de resource.

    a. Ga naar het **persoonlijke koppelings centrum**.

      ![Persoonlijk koppelings centrum](./media/private-link-tsg/private-link-center.png)

    b. Selecteer **privé link services** in het linkerdeel venster.

      ![Services voor persoonlijke koppelingen](./media/private-link-tsg/private-link-service.png)

    c. Filter en selecteer de persoonlijke koppelings service die u wilt diagnosticeren.

    d. Controleer de verbindingen van het privé-eind punt.
     - Zorg ervoor dat het persoonlijke eind punt waarvan u verbinding zoekt, wordt vermeld met een **goedgekeurde** verbindings status.
     - Als de status **in behandeling** is, selecteert u deze en keurt u deze goed.

       ![Verbindingen met privé-eind punten](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Ga naar het persoonlijke eind punt waarvan u verbinding maakt door de naam te selecteren. Zorg ervoor dat de verbindings status wordt weer gegeven als **goedgekeurd**.

       ![Overzicht van de verbinding met het persoonlijke eind punt](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Nadat beide zijden zijn goedgekeurd, kunt u de connectiviteit opnieuw proberen.

    e. Controleer de **alias** op het tabblad **overzicht** en de **resource-id** op het tabblad **Eigenschappen** .
     - Zorg ervoor dat de gegevens van de **alias** en de **bron-id** overeenkomen met de **alias** en de **resource-id** die u gebruikt om een persoonlijk eind punt te maken voor deze service.

       ![Alias gegevens verifiëren](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Informatie over de resource-ID controleren](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Bekijk de **zichtbaarheids** informatie op het tabblad **overzicht** .
     - Zorg ervoor dat uw abonnement onder het **zichtbaarheids** bereik valt.

       ![Zichtbaarheids gegevens controleren](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Bekijk de gegevens van de **Load Balancer** op het tabblad **overzicht** .
     - U kunt naar de load balancer gaan door de load balancer koppeling te selecteren.

       ![Gegevens van de Load Balancer controleren](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Zorg ervoor dat de load balancer-instellingen zijn geconfigureerd volgens uw verwachtingen.
       - Controleer de **frontend-IP-configuratie**.
       - Controleer de **back-end-Pools**.
       - Controleer de **taakverdelings regels**.

       ![load balancer eigenschappen controleren](./media/private-link-tsg/pls-ilb-properties.png)

     - Zorg ervoor dat de load balancer werkt volgens de vorige instellingen.
       - Selecteer een virtuele machine in een ander subnet dan het subnet waar de back-end-pool van load balancer beschikbaar is.
       - Probeer toegang te krijgen tot de load balancer front-end vanaf de vorige VM.
       - Als de verbinding wordt gemaakt met de back-end-pool op basis van de regels voor taak verdeling, is uw load balancer operationeel.
       - U kunt ook de load balancer metriek door Azure Monitor bekijken om te zien of gegevens via de load balancer stromen.

1. Gebruik [Azure monitor](../azure-monitor/overview.md) om te zien of gegevens stromen.

    a. Selecteer **metrische gegevens** op de bron van de persoonlijke koppelings service.
     - Selecteer **bytes in** of **uitgaande bytes**.
     - Controleer of de gegevens stroom lopen wanneer u verbinding probeert te maken met de persoonlijke koppelings service. Er wordt een vertraging van ongeveer 10 minuten verwacht.

       ![De metrische gegevens van de service voor persoonlijke koppelingen controleren](./media/private-link-tsg/pls-metrics.png)

1. Neem contact op met het [ondersteunings](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) team van Azure als uw probleem nog steeds niet is opgelost en er nog een probleem is met de verbinding.

## <a name="next-steps"></a>Volgende stappen

 * [Een service voor persoonlijke koppelingen maken](./create-private-link-service-cli.md)
 * [Probleemoplossings gids voor Azure private Endpoint](troubleshoot-private-endpoint-connectivity.md)