---
title: DHCP voor Azure VMware-oplossing beheren
description: Meer informatie over het maken en beheren van DHCP voor uw Azure VMware-oplossing privécloud.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97708548"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>DHCP voor Azure VMware-oplossing beheren

Voor toepassingen en werk belastingen die worden uitgevoerd in een privécloud-omgeving zijn DHCP-services vereist voor IP-adres toewijzingen.  In dit artikel wordt beschreven hoe u op twee manieren DHCP kunt maken en beheren in de Azure VMware-oplossing:

- Als u NSX-T gebruikt om uw DHCP-server te hosten, moet u [een DHCP-server maken](#create-a-dhcp-server) en [deze doorsturen naar die server](#create-dhcp-relay-service). Wanneer u de DHCP-server maakt, voegt u ook een netwerk segment toe en geeft u het DHCP IP-adres bereik op.   

- Als u een externe DHCP-server van derden in uw netwerk gebruikt, moet u [een DHCP-doorgifteservice maken](#create-dhcp-relay-service). Wanneer u een relay maakt naar een DHCP-server, of u nu NSX-T of een derde partij gebruikt om uw DHCP-server te hosten, moet u het DHCP IP-adres bereik opgeven.

>[!IMPORTANT]
>DHCP werkt niet voor virtuele machines (Vm's) op het VMware HCX L2-netwerk wanneer de DHCP-server zich in het on-premises Data Center bevindt.  NSX blokkeert standaard alle DHCP-aanvragen van het door lopen van de L2-stretch. Zie de procedure [DHCP-aanvragen verzenden naar de on-premises DHCP-server](#send-dhcp-requests-to-the-on-premises-dhcp-server) voor de oplossing.


## <a name="create-a-dhcp-server"></a>Een DHCP-server maken

Als u NSX-T wilt gebruiken om uw DHCP-server te hosten, maakt u een DHCP-server. Vervolgens voegt u een netwerk segment toe en geeft u het DHCP IP-adres bereik op.

1. Selecteer in NSX-T Manager de optie **netwerken**  >  **DHCP** en selecteer vervolgens **server toevoegen**.

1. Selecteer **DHCP** voor het **server type**, geef de server naam en het IP-adres op en selecteer vervolgens **Opslaan**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer **gateways van laag 1**, selecteer het verticale beletsel teken op de laag-1-gateway en selecteer vervolgens **bewerken**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="te gebruiken gateway selecteren" border="true":::

1. Selecteer **geen IP-toewijzings set** om een subnet toe te voegen.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="een subnet toevoegen" border="true":::

1. Selecteer bij **type** de optie **DHCP local server**. 
   
1. Selecteer voor de **DHCP-server** **standaard DHCP** en selecteer vervolgens **Opslaan**.

1. Selecteer opnieuw **Opslaan** en selecteer vervolgens **bewerken sluiten**.

### <a name="add-a-network-segment"></a>Een netwerksegment toevoegen

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>DHCP Relay-service maken

Als u een externe DHCP-server van derden wilt gebruiken, moet u een DHCP-Relay-service maken. U geeft ook het DHCP IP-adres bereik op in NSX-T-beheer. 

1. Selecteer in NSX-T Manager de optie **netwerken**  >  **DHCP** en selecteer vervolgens **server toevoegen**.

1. Selecteer **DHCP-Relay** voor het **server type**, geef de server naam en het IP-adres op en selecteer vervolgens **Opslaan**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP Relay-service maken" border="true":::

1. Selecteer **gateways van laag 1**, selecteer het verticale beletsel teken op de laag-1-gateway en selecteer vervolgens **bewerken**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="gateway voor laag 1 bewerken" border="true":::

1. Selecteer **geen IP-toewijzings set** om de toewijzing van IP-adressen te definiëren.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="toewijzing van IP-adres bewerken" border="true":::

1. Selecteer bij **type** de optie **DHCP-server**. 
   
1. Selecteer voor de **DHCP-server** **DHCP-Relay** en selecteer vervolgens **Opslaan**.

1. Selecteer opnieuw **Opslaan** en selecteer vervolgens **bewerken sluiten**.


## <a name="specify-the-dhcp-ip-address-range"></a>Het DHCP IP-adres bereik opgeven

1. Selecteer **Netwerken** > **Segmenten** in de NSX-T Manager. 
   
1. Selecteer de verticale beletsel tekens op de segment naam en selecteer **bewerken**.
   
1. Selecteer **subnetten instellen** om het DHCP IP-adres voor het subnet op te geven. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="netwerk segmenten" border="true":::
      
1. Wijzig zo nodig het IP-adres van de gateway en voer het IP van het DHCP-bereik in. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="subnetten bewerken" border="true":::
      
1. Selecteer **Toep assen** en vervolgens **Opslaan**. Aan het segment wordt een DHCP-Server groep toegewezen.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP-Server groep toegewezen aan segment" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>DHCP-aanvragen verzenden naar de on-premises DHCP-server

Als u DHCP-aanvragen wilt verzenden van uw Azure VMware-oplossing-Vm's op het uitgebreide L2-segment naar de on-premises DHCP-server, maakt u een profiel voor een beveiligings segment. 

1. Meld u aan bij uw on-premises vCenter en selecteer onder **Home** de optie **HCX**.

1. Selecteer **netwerk uitbreiding** onder **Services**.

1. Selecteer de netwerk extensie die u wilt ondersteunen van DHCP-aanvragen van een Azure VMware-oplossing naar on-premises. 

1. Noteer de naam van het doelnet-netwerk.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Scherm opname van een netwerk extensie in VMware vSphere-client" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Selecteer in de Azure VMware-oplossing NSX-T Manager **netwerk**  >    >  **segment profielen**. 

1. Selecteer **segment profiel toevoegen** en vervolgens **segment beveiliging**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Scherm afbeelding van het toevoegen van een segment profiel in NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Geef een naam en een label op en stel vervolgens de wissel knop voor het **BPDU-filter** in op aan en alle DHCP wordt in-of uitgeschakeld.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Scherm opname waarin het BPDU-filter is in-of uitgeschakeld en de DHCP-in-/uitschakelen" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Verwijder alle MAC-adressen, indien aanwezig, onder de **acceptatie lijst BPDU-filter**.  Selecteer vervolgens **Opslaan**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Scherm opname van de MAC-adressen in de lijst met toegestane BPDU-filters":::

1. Voer onder **netwerk**  >  **segmenten**  >  **segmenten** in het zoek gebied de naam van het definitie netwerk in.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Scherm afbeelding van het filter veld voor netwerk > segmenten":::

1. Selecteer de verticale beletsel tekens op de segment naam en selecteer **bewerken**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Scherm afbeelding van de knop bewerken voor het segment" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Wijzig de **segment beveiliging** in het segment profiel dat u eerder hebt gemaakt.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Scherm opname van het beveiligings veld segment" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [host onderhoud en levenscyclus beheer](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).