---
title: Traffic Manager implementeren om de werk belastingen van de Azure VMware-oplossing (AVS) te verdelen
description: Informatie over het integreren van Traffic Manager met de Azure VMware-oplossing (AVS) om de werk belastingen van toepassingen te verdelen over meerdere eind punten in verschillende regio's.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: ed74bb0dfc533abadd50af32afc06c9cb4106193
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874279"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Traffic Manager implementeren om de werk belastingen van de Azure VMware-oplossing (AVS) te verdelen

In dit artikel wordt uitgelegd hoe u Traffic Manager integreert met de Azure VMware-oplossing (AVS) om de werk belastingen van toepassingen te verdelen over meerdere eind punten. We kijken naar een scenario waarin Traffic Manager verkeer omleidt tussen drie toepassings gateways die verschillende AVS-regio's bespannen: VS-West, Europa-west en on-premises in VS-Oost. 

Azure Traffic Manager is een op DNS gebaseerd verkeer load balancer waarmee u verkeer optimaal kunt distribueren naar Services in wereld wijde Azure-regio's. Er wordt taak verdeling toegepast voor het toepassings verkeer voor zowel Azure actieve werk belastingen als externe open bare eind punten. Zie [Wat is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) voor meer informatie over Traffic Manager.

Controleer de [vereisten](#prerequisites) eerst. vervolgens gaan we de procedures door lopen om:

> [!div class="checklist"]
> * De configuratie van uw toepassings gateways controleren
> * De configuratie van het NSX-T-segment controleren
> * Uw Traffic Manager-profiel maken
> * Externe eind punten toevoegen aan uw Traffic Manager profiel

## <a name="topology"></a>Topologie

Zoals in de volgende afbeelding wordt weer gegeven, biedt Azure Traffic Manager taak verdeling voor de toepassingen op het DNS-niveau tussen regionale eind punten. De toepassings gateways hebben leden van de back-end-groep geconfigureerd als IIS-servers en verwijzen naar externe AVS-eind punten.

Verbinding via het virtuele netwerk tussen de twee de privécloud-regio's van de Cloud, VS-West en Europa-west, en een on-premises server in VS-Oost, maakt gebruik van een ExpressRoute-gateway.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagram van de architectuur van Traffic Manager integratie met de Azure VMware-oplossing" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::
 
## <a name="prerequisites"></a>Vereisten

- Drie virtuele machines die zijn geconfigureerd als micro soft IIS-servers die worden uitgevoerd in verschillende AVS-regio's: VS-West, Europa-west en on-premises. 

- Een toepassings gateway met externe eind punten in de VS West, Europa-west en on-premises.

- Host met Internet connectiviteit voor verificatie. 

## <a name="verify-configuration-of-your-application-gateways"></a>De configuratie van uw toepassings gateways controleren

[Azure-toepassing gateway](https://azure.microsoft.com/services/application-gateway/) is een laag 7-webverkeer Load Balancer waarmee u verkeer naar uw webtoepassingen kunt beheren. Zie [Wat is Azure-toepassing gateway?](../application-gateway/overview.md) voor meer informatie over Application Gateway. 

In dit scenario worden drie Application Gateway-exemplaren geconfigureerd als externe AVS-eind punten. De toepassings gateways hebben AVS virtuele machines geconfigureerd als leden van de back-end-groep om de binnenkomende Layer 7-aanvragen te verdelen. (Zie [Azure-toepassing gateway gebruiken voor het beveiligen van uw web-apps op de Azure VMware-oplossing](protect-azure-vmware-solution-with-application-gateway.md)(Engelstalig) voor meer informatie over het configureren van Application Gateway met virtuele AVS-machines als back-endservers.  

De volgende stappen verifiëren de juiste configuratie van uw toepassings gateways.

1. Open de Azure Portal en selecteer **toepassings gateways** om een lijst met uw huidige toepassings gateways weer te geven. 

    Voor dit scenario hebben we drie toepassings gateways geconfigureerd:
    - AVS-GW-WUS
    - AVS-GW-EUS (on-premises)
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Scherm afbeelding van de pagina Toepassings gateway met een lijst met geconfigureerde toepassings gateways." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Selecteer een van de eerder geïmplementeerde toepassings gateways. Er wordt een venster geopend met verschillende informatie over de toepassings gateway. Selecteer **back-endservers** om de configuratie van een van de back-endservers te controleren.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Scherm afbeelding van de pagina Toepassings gateway met details van de geselecteerde toepassings gateway." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. In dit geval ziet u één lid van een back-endadresgroep van een virtuele machine die is geconfigureerd als een webserver met het IP-adres 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Scherm opname van pagina voor het bewerken van de back-endserver met het doel-IP-adres gemarkeerd.":::

    U kunt ook de configuratie van de andere toepassings gateways en back-endadresgroep controleren. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>De configuratie van het NSX-T-segment controleren

Netwerk segmenten die zijn gemaakt in NSX-T-beheer worden gebruikt als netwerken voor virtuele machines in vCenter. Zie de zelf studie [een NSX-T-netwerk segment maken in azure VMware-oplossing (AVS)](tutorial-nsx-t-network-segment.md)voor meer informatie.

In ons scenario wordt een NSX-T-segment geconfigureerd in de AVS-omgeving waar de virtuele machine van de back-end-pool is aangesloten.

1. Selecteer **segmenten** om uw geconfigureerde segmenten weer te geven. In dit geval ziet u dat contoso-segment1 is verbonden met Contoso-T01-gateway, een flexibele router van laag 1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Scherm opname van segment profielen in NSX-T-beheer." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

2. Selecteer **laag-1 gateways** om een lijst met uw laag-1-gateways met het aantal gekoppelde segmenten weer te geven. Selecteer het segment dat is gekoppeld aan contoso-T01. Er wordt een venster geopend met de logische interface die is geconfigureerd op de laag-01-router. Dit fungeert als gateway naar de virtuele machine van de back-endadresgroep-groep die is verbonden met het segment.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Scherm opname van het gateway-adres van het geselecteerde segment.":::    

3. Selecteer de virtuele machine in de VM vSphere-client om de details ervan weer te geven. Houd er rekening mee dat het IP-adres overeenkomt met wat we in stap 3 van de vorige sectie hebben gezien: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Scherm opname van de VM-Details in de VSphere-client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecteer de virtuele machine en klik vervolgens op **acties > instellingen bewerken** om de verbinding met het NSX-T-segment te controleren.

## <a name="create-your-traffic-manager-profile"></a>Uw Traffic Manager-profiel maken

1. Meld u aan bij [Azure Portal](https://rc.portal.azure.com/#home). Selecteer **Traffic Manager profielen** onder **Azure-Services > netwerk**.

2. Selecteer **+ toevoegen** om een nieuw Traffic Manager profiel te maken.
 
3. Profiel naam opgeven, routerings methode (we gebruiken gewogen in dit scenario; Zie [Traffic Manager routerings methoden](../traffic-manager/traffic-manager-routing-methods.md)), abonnement en resource groep en selecteer **maken**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Externe eind punten toevoegen aan het Traffic Manager profiel

1. Selecteer het Traffic Manager profiel in het deel venster Zoek resultaten, selecteer **eind punten** en vervolgens **+ toevoegen**.

2. Voer de vereiste gegevens in: type, naam, FQDN-naam (Fully Qualified Domain Name) of IP, en gewicht (in dit scenario wordt er een gewicht van 1 toegewezen aan elk eind punt). Selecteer **Toevoegen**. Hiermee maakt u het externe eind punt. De monitor status moet **online** zijn. Herhaal dezelfde stappen om twee meer externe eind punten te maken, één in een andere regio en de andere on-premises. Zodra u deze hebt gemaakt, worden alle drie in het Traffic Manager profiel weer gegeven. de status van alle drie moet **online** zijn.

3. Selecteer **Overzicht**. Kopieer de URL onder **DNS-naam**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Scherm afbeelding met een Traffic Manager overzicht van eind punten waarbij de DNS-naam is gemarkeerd." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Plak de URL van de DNS-naam in een browser. De volgende scherm afbeelding toont het verkeer dat wordt omgeleid naar de Europa-west regio.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Scherm afbeelding van het browser venster met het verkeer dat wordt gerouteerd naar Europa-west."::: 

5. Vernieuw de browser. De volgende scherm afbeelding toont het verkeer dat nu wordt omgeleid naar een andere set back-end-groeps leden in de regio vs-West.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Scherm afbeelding van het browser venster met verkeer dat wordt gerouteerd naar VS-West."::: 

6. Vernieuw de browser opnieuw. De volgende scherm afbeelding toont het verkeer dat nu wordt doorgestuurd naar de laatste set back-end-groeps leden on-premises.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Scherm afbeelding van het browser venster met verkeer dat wordt gerouteerd naar on-premises.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Azure-toepassing gateway gebruiken op de Azure VMware-oplossing (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Methoden voor het doorsturen van Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)
- [Services voor taak verdeling combi neren in azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Prestaties meten Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md)
