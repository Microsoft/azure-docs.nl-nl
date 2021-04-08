---
title: Traffic Manager implementeren om werk belastingen van Azure VMware-oplossingen te verdelen
description: Meer informatie over het integreren van Traffic Manager met de Azure VMware-oplossing om de werk belastingen van toepassingen te verdelen over meerdere eind punten in verschillende regio's.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988591"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Traffic Manager implementeren om werk belastingen van Azure VMware-oplossingen te verdelen

In dit artikel wordt stapsgewijs uitgelegd hoe u [azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) integreert met de Azure VMware-oplossing. Het integratie saldo van toepassings werkbelastingen over meerdere eind punten. In dit artikel worden ook de stappen beschreven voor het configureren van Traffic Manager voor het omleiden van verkeer tussen drie [Azure-toepassing gateway](../application-gateway/overview.md) die verschillende Azure VMware-oplossings regio's omspannen. 

De gateways hebben Azure VMware-oplossing virtuele machines (Vm's) geconfigureerd als leden van de back-end-groep om de binnenkomende Layer 7-aanvragen te verdelen. Zie [Azure-toepassing gateway gebruiken voor het beveiligen van uw web-apps op de Azure VMware-oplossing](protect-azure-vmware-solution-with-application-gateway.md) voor meer informatie.

In het diagram ziet u hoe Traffic Manager taak verdeling biedt voor de toepassingen op het DNS-niveau tussen regionale eind punten. De gateways hebben leden van de back-end-groep die zijn geconfigureerd als IIS-servers en waarnaar wordt verwezen als externe eind punten van de Azure VMware-oplossing. Verbinding via het virtuele netwerk tussen de twee privécloud-regio's maakt gebruik van een ExpressRoute-gateway.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Architectuur diagram van de integratie van Traffic Manager met de Azure VMware-oplossing" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Voordat u begint, moet u eerst de [vereisten](#prerequisites) door nemen en vervolgens de procedures door lopen om:

> [!div class="checklist"]
> * Controleer de configuratie van uw toepassings gateways en het NSX-T-segment
> * Uw Traffic Manager-profiel maken
> * Externe eind punten toevoegen aan uw Traffic Manager profiel

## <a name="prerequisites"></a>Vereisten

- Drie Vm's geconfigureerd als micro soft IIS-servers die worden uitgevoerd in verschillende Azure VMware-oplossings regio's: 
   - VS - west
   - Europa -west
   - VS-Oost (on-premises) 

- Een toepassings gateway met externe eind punten in de hierboven vermelde Azure VMware-oplossings regio's.

- Host met Internet connectiviteit voor verificatie. 

- Een [NSX-T-netwerk segment dat is gemaakt in een Azure VMware-oplossing](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>De configuratie van de toepassings gateways controleren

In de volgende stappen wordt de configuratie van uw toepassings gateways gecontroleerd.

1. Selecteer in de Azure Portal **toepassings gateways** om een lijst met uw huidige toepassings gateways weer te geven:

   - AVS-GW-WUS
   - AVS-GW-EUS (on-premises)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Scherm afbeelding van de pagina Toepassings gateway met een lijst met geconfigureerde toepassings gateways." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Selecteer een van de eerder geïmplementeerde toepassings gateways. 

   Er wordt een venster geopend met verschillende informatie over de toepassings gateway. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Scherm afbeelding van de pagina Toepassings gateway met details van de geselecteerde toepassings gateway." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Selecteer **back-endservers** om de configuratie van een van de back-endservers te controleren. U ziet één lid van een VM-back-endadresgroep dat is geconfigureerd als een webserver met het IP-adres 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Scherm opname van pagina voor het bewerken van de back-endserver met het doel-IP-adres gemarkeerd.":::

1. Controleer de configuratie van de leden van de andere toepassings gateway en de back-endadresgroep. 

## <a name="verify-the-nsx-t-segment-configuration"></a>De NSX-T-segment configuratie controleren

In de volgende stappen wordt de configuratie van het NSX-T-segment in de Azure VMware-oplossings omgeving gecontroleerd.

1. Selecteer **segmenten** om uw geconfigureerde segmenten weer te geven.  U ziet contoso-segment1 die is verbonden met Contoso-T01 gateway, een flexibele router van laag 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Scherm opname van segment profielen in NSX-T-beheer." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Selecteer **laag-1 gateways** om een lijst met laag-1-gateways met het aantal gekoppelde segmenten weer te geven. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Scherm opname van het gateway-adres van het geselecteerde segment.":::    

1. Selecteer het segment dat is gekoppeld aan contoso-T01. Er wordt een venster geopend met de logische interface die is geconfigureerd op de laag-01-router. Deze fungeert als een gateway naar de virtuele machine van de back-endadresgroep die is verbonden met het segment.

1. Selecteer de virtuele machine in de vSphere-client om de details ervan weer te geven. 

   >[!NOTE]
   >Het IP-adres komt overeen met het VM-back-endadresgroep dat is geconfigureerd als webserver uit de voor gaande sectie: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Scherm opname van de VM-Details in de vSphere-client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecteer de virtuele machine en selecteer vervolgens **acties > instellingen bewerken** om de verbinding met het NSX-T-segment te controleren.

## <a name="create-your-traffic-manager-profile"></a>Uw Traffic Manager-profiel maken

1. Meld u aan bij [Azure Portal](https://rc.portal.azure.com/#home). Selecteer **Traffic Manager profielen** onder **Azure-Services > netwerk**.

2. Selecteer **+ toevoegen** om een nieuw Traffic Manager profiel te maken.
 
3. Geef de volgende informatie op en selecteer **maken**:

   - Profielnaam
   - Routerings methode (gebruik [gewogen](../traffic-manager/traffic-manager-routing-methods.md)
   - Abonnement
   - Resourcegroep

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Externe eind punten toevoegen aan het Traffic Manager profiel

1. Selecteer het Traffic Manager profiel in het deel venster Zoek resultaten, selecteer **eind punten** en vervolgens **+ toevoegen**.

1. Voer de vereiste gegevens in voor elk van de externe eind punten in de verschillende regio's en selecteer vervolgens **toevoegen**: 
   - Type
   - Naam
   - FQDN-naam (Fully Qualified Domain Name) of IP
   - Gewicht (wijs een gewicht van 1 toe aan elk eind punt). 

   Eenmaal gemaakt, worden alle drie de Program ma's in het Traffic Manager profiel weer gegeven. De monitor status van alle drie moet **online** zijn.

3. Selecteer **overzicht** en kopieer de URL onder **DNS-naam**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Scherm afbeelding met een Traffic Manager overzicht van eind punten waarbij de DNS-naam is gemarkeerd." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Plak de URL van de DNS-naam in een browser. De scherm opname toont het verkeer dat wordt omgeleid naar de Europa-west regio.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Scherm afbeelding van het browser venster met het verkeer dat wordt gerouteerd naar Europa-west."::: 

5. Vernieuw de browser. De scherm opname toont het verkeer dat wordt omgeleid naar een andere set back-end-groeps leden in de regio vs-West.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Scherm afbeelding van het browser venster met verkeer dat wordt gerouteerd naar VS-West."::: 

6. Vernieuw de browser opnieuw. De scherm opname toont het verkeer dat rechtstreeks naar de definitieve set van de back-end-groeps leden op locatie wordt gestuurd.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Scherm afbeelding van het browser venster met verkeer dat wordt gerouteerd naar on-premises.":::

## <a name="next-steps"></a>Volgende stappen

Nu u Azure Traffic Manager hebt geïntegreerd met de Azure VMware-oplossing, kunt u het volgende weten:

- [Gebruik Azure-toepassing gateway op de Azure VMware-oplossing](protect-azure-vmware-solution-with-application-gateway.md).
- [Routerings methoden Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).
- Hiermee worden [taakverdelings Services in azure gecombineerd](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Het [meten van Traffic manager prestaties](../traffic-manager/traffic-manager-performance-considerations.md).
