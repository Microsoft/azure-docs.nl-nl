---
title: Een Traffic Manager implementeren om de werkbelastingen Azure VMware Solution verdelen
description: Meer informatie over het integreren Traffic Manager met Azure VMware Solution om toepassingsworkloads te verdelen over meerdere eindpunten in verschillende regio's.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 029bb9512bd19effd1c7aeb5104c7bb6d7ccdca5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876918"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Een Traffic Manager implementeren om de werkbelastingen Azure VMware Solution verdelen

In dit artikel worden de stappen beschreven voor het integreren [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) met Azure VMware Solution. De integratie balancert toepassingsworkloads over meerdere eindpunten. In dit artikel worden ook de stappen beschreven voor [](../application-gateway/overview.md) het configureren van Traffic Manager om verkeer tussen drie Azure Application Gateway verschillende regio's Azure VMware Solution omspannen. 

Voor de gateways Azure VMware Solution virtuele machines (VM's) geconfigureerd als leden van de back-endpool om de binnenkomende laag 7-aanvragen te laden. Zie Use [Azure Application Gateway to protect your web apps on Azure VMware Solution (Gebruik Azure Application Gateway om uw web-apps](protect-azure-vmware-solution-with-application-gateway.md) te beveiligen op Azure VMware Solution

Het diagram laat zien hoe Traffic Manager taakverdeling biedt voor de toepassingen op DNS-niveau tussen regionale eindpunten. De gateways hebben back-endpoolleden geconfigureerd als IIS-servers en er wordt naar verwezen als Azure VMware Solution externe eindpunten. De verbinding via het virtuele netwerk tussen de twee privécloudregio's maakt gebruik van een ExpressRoute-gateway.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Architectuurdiagram van de Traffic Manager integratie met Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Voordat u begint, bekijkt u [eerst de Vereisten.](#prerequisites) Vervolgens doorlopen we de procedures voor het volgende:

> [!div class="checklist"]
> * De configuratie van uw toepassingsgateways en het NSX-T-segment controleren
> * Uw Traffic Manager maken
> * Externe eindpunten toevoegen aan uw Traffic Manager profiel

## <a name="prerequisites"></a>Vereisten

- Drie VM's die zijn geconfigureerd als Microsoft IIS-servers die worden uitgevoerd in Azure VMware Solution regio's: 
   - VS - west
   - Europa -west
   - VS - oost (on-premises) 

- Een toepassingsgateway met externe eindpunten in de Azure VMware Solution hierboven genoemde regio's.

- Host met internetverbinding voor verificatie. 

- Een [NSX-T-netwerksegment dat is gemaakt in Azure VMware Solution](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>De configuratie van uw toepassingsgateway controleren

Met de volgende stappen controleert u de configuratie van uw toepassingsgateways.

1. Selecteer in Azure Portal **toepassingsgateways een** lijst met uw huidige toepassingsgateways:

   - AVS-GW-WUS
   - AVS-GW-EUS (on-premises)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Schermopname van de pagina Application Gateway met een lijst met geconfigureerde toepassingsgateways." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Selecteer een van uw eerder geïmplementeerde toepassingsgateways. 

   Er wordt een venster geopend met verschillende informatie over de toepassingsgateway. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Schermopname van de pagina Application Gateway met details van de geselecteerde toepassingsgateway." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Selecteer **Back-endpools** om de configuratie van een van de back-endpools te controleren. U ziet dat één lid van de back-endpool van de VM is geconfigureerd als een webserver met het IP-adres 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Schermopname van de pagina Back-end-pool bewerken met het doel-IP-adres gemarkeerd.":::

1. Controleer de configuratie van de andere toepassingsgateways en leden van de back-endpool. 

## <a name="verify-the-nsx-t-segment-configuration"></a>De NSX-T-segmentconfiguratie controleren

Met de volgende stappen wordt de configuratie van het NSX-T-segment in de Azure VMware Solution gecontroleerd.

1. Selecteer **Segmenten** om de geconfigureerde segmenten te bekijken.  Contoso-segment1 is verbonden met de gateway Contoso-T01, een flexibele router op laag 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Schermopname van segmentprofielen in NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Selecteer **Gateways op laag 1 voor** een lijst met Tier-1-gateways met het aantal gekoppelde segmenten. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Schermopname van het gatewayadres van het geselecteerde segment.":::    

1. Selecteer het segment dat is gekoppeld aan Contoso-T01. Er wordt een venster geopend met de logische interface die is geconfigureerd op de router Tier-01. Het fungeert als een gateway naar de VM die lid is van de back-endpool die is verbonden met het segment.

1. Selecteer in de vSphere-client de VM om de details ervan weer te geven. 

   >[!NOTE]
   >Het IP-adres komt overeen met het lid van de back-endpool van de VM dat is geconfigureerd als een webserver uit de vorige sectie: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Schermopname met VM-details in de vSphere-client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecteer de VM en selecteer vervolgens **ACTIES > Instellingen bewerken** om de verbinding met het NSX-T-segment te controleren.

## <a name="create-your-traffic-manager-profile"></a>Uw Traffic Manager maken

1. Meld u aan bij [Azure Portal](https://rc.portal.azure.com/#home). Selecteer **onder Azure Services > Networking** de Traffic Manager **profielen**.

2. Selecteer **+ Toevoegen om** een nieuw Traffic Manager maken.
 
3. Geef de volgende informatie op en selecteer vervolgens **Maken:**

   - Profielnaam
   - Routeringsmethode (gebruik [gewogen](../traffic-manager/traffic-manager-routing-methods.md)
   - Abonnement
   - Resourcegroep

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Externe eindpunten toevoegen aan het Traffic Manager profiel

1. Selecteer het Traffic Manager in het deelvenster met zoekresultaten, selecteer **Eindpunten** en vervolgens **+ Toevoegen.**

1. Voer voor elk van de externe eindpunten in de verschillende regio's de vereiste gegevens in en selecteer **vervolgens Toevoegen:** 
   - Type
   - Naam
   - Fully Qualified Domain Name (FQDN) of IP
   - Gewicht (wijs een gewicht van 1 toe aan elk eindpunt). 

   Na het maken worden alle drie de Traffic Manager in het profiel. De monitorstatus van alle drie moet **Online zijn.**

3. Selecteer **Overzicht** en kopieer de URL onder **DNS-naam**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Schermopname van een Traffic Manager eindpunt met DNS-naam gemarkeerd." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Plak de URL van de DNS-naam in een browser. In de schermopname ziet u verkeer dat naar de Europa - west leidt.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Schermopname van het browservenster met verkeer dat naar de Europa - west."::: 

5. Vernieuw de browser. In de schermopname ziet u verkeer dat wordt omleiding naar een andere set back-endpoolleden in de regio VS - west.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Schermopname van browservenster met verkeer dat is gerouteerd naar VS - west."::: 

6. Vernieuw uw browser opnieuw. In de schermopname ziet u verkeer dat naar de uiteindelijke set back-endpoolleden on-premises wordt doorver leiden.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Schermopname van het browservenster met verkeer dat naar on-premises is gerouteerd.":::

## <a name="next-steps"></a>Volgende stappen

Nu u de integratie van Azure Traffic Manager met Azure VMware Solution hebt behandeld, kunt u het volgende leren:

- [Gebruik Azure Application Gateway op Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)
- [Methoden voor het doorsturen van Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)
- [Taakverdelingsservices combineren in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Prestaties Traffic Manager meten](../traffic-manager/traffic-manager-performance-considerations.md)
