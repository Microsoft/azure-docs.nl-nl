---
title: NSX-netwerk onderdelen configureren in azure VMware-oplossing
description: Meer informatie over het gebruik van de Azure VMware-oplossings console voor het configureren van NSX-T-netwerk segmenten.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: dbed29fb1063b78386f9ec1e2ee00d9c685a944e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417315"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>NSX-netwerk onderdelen configureren in azure VMware-oplossing

Een persoonlijke cloud van Azure VMware-oplossing wordt standaard geleverd met NSX-T als een door software gedefinieerde netwerk (SDDC). Het wordt vooraf ingericht met een NSX-T-laag-0-gateway in de modus actief/actief en een standaard NSX-T-laag in de modus actief/stand-by.  Met deze gateways kunt u de segmenten (logische switches) verbinden en East-West en North-South connectiviteit bieden. 

Nadat de privécloud van de Azure VMware-oplossing is geïmplementeerd, kunt u de benodigde NSX-T-objecten configureren via de Azure VMware-oplossings console onder **werkbelasting netwerken**.  De-console biedt de vereenvoudigde weer gave van NSX-T-bewerkingen die dagelijks door een VMware-beheerder moeten worden uitgevoerd en die zijn gericht op gebruikers die niet bekend zijn met NSX-T.  

U hebt vier opties om NSX-T-onderdelen te configureren in de Azure VMware-oplossingen console:
- **Segmenten** : Maak segmenten die in NSX-T-beheer en vCenter worden weer gegeven.
- **DHCP** : een DHCP-server of DHCP-Relay maken als u van plan bent DHCP te gebruiken.
- **Poort spiegeling** -poort spiegeling maken om netwerk problemen op te lossen.
- **DNS** : Maak een DNS-doorstuur server voor het verzenden van DNS-aanvragen naar een aangewezen DNS-servers voor oplossing.  

>[!NOTE]
>U hebt nog steeds toegang tot de NSX-T-beheer console, waar u de gevermelde geavanceerde instellingen en andere NSX-T-functies kunt gebruiken. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Scherm afbeelding met vier opties in de Azure VMware-oplossings console voor het configureren van NSX-T.":::

## <a name="prerequisites"></a>Vereisten
Virtuele machines (Vm's) die zijn gemaakt of gemigreerd naar de privécloud van Azure VMware-oplossing, moeten aan een segment zijn gekoppeld. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Een NSX-T-segment maken in de Azure Portal
U kunt een NSX-T-segment maken en configureren vanuit de Azure VMware-oplossings console in de Azure Portal.  Deze segmenten zijn verbonden met de standaard gateway van laag 1 en de werk belastingen op deze segmenten krijgen East-West en North-South connectiviteit. Zodra u het segment hebt gemaakt, wordt het weer gegeven in NSX-T-beheer en vCenter.

>[!NOTE]
>Als u DHCP wilt gebruiken, moet u [een DHCP-server of DHCP-Relay configureren](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) voordat u een NSX-T-segment kunt maken en configureren.

1. Selecteer in uw Azure VMware-oplossing privécloud onder **werkbelasting netwerk** de optie **segmenten**  >  **toevoegen**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Scherm opname waarin wordt getoond hoe een nieuw segment moet worden toegevoegd.":::

1. Geef de Details voor het nieuwe logische segment op.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/create-new-segment-details.png" alt-text="Scherm opname met de details van het nieuwe segment.":::
   
   - **Segment naam** : naam van de logische switch die zichtbaar is in vCenter.
   - **Subnet gateway** -gateway-IP-adres voor het subnet van de logische switch met een subnetmasker. Vm's zijn gekoppeld aan een logische switch en alle virtuele machines die verbinding maken met deze switch, behoren tot hetzelfde subnet.  Daarnaast moeten alle Vm's die aan dit logische segment zijn gekoppeld, een IP-adres van hetzelfde segment bevatten.
   - **DHCP** (optioneel): DHCP-bereiken voor een logisch segment. Een [DHCP-server of DHCP-Relay](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) moet worden geconfigureerd om DHCP te gebruiken voor segmenten.  
   - **Verbonden gateway**  -  *Standaard geselecteerd en alleen-lezen.*  Laag-1-gateway en type segment informatie. 
      - **T1** : naam van de laag-1-gateway in NSX-T-beheer. Een persoonlijke cloud van Azure VMware-oplossing wordt geleverd met een NSX-T-laag-0-gateway in de modus actief/actief en een standaard NSX-T-laag in de modus actief/stand-by.  Segmenten die zijn gemaakt via de Azure VMware-oplossings console, maken alleen verbinding met de standaard-laag-1-gateway, en de werk belastingen van deze segmenten krijgen East-West en North-South verbinding. U kunt alleen meer laag-1-gateways maken via NSX-T-beheer. Laag-1-gateways die zijn gemaakt op basis van de NSX-T-beheer console zijn niet zichtbaar in de Azure VMware-oplossings console. 
      - **Type** overlay-segment dat wordt ondersteund door de Azure VMware-oplossing.

1. Selecteer **OK** om het segment te maken en het aan de laag-1-gateway te koppelen. 

   Het segment is nu zichtbaar in de Azure VMware-oplossings console, NSX-T Manager en vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Een DHCP-server of DHCP-Relay maken in de Azure Portal
U kunt een DHCP-server of-relay rechtstreeks vanuit de Azure VMware-oplossings console maken in de Azure Portal. De DHCP-server of relay maakt verbinding met de laag-1-gateway, die wordt gemaakt wanneer u de Azure VMware-oplossing implementeert. Alle segmenten waarvan u de DHCP-bereiken hebt opgegeven, maken deel uit van deze DHCP.  Nadat u een DHCP-server of DHCP-relay hebt gemaakt, moet u een subnet of bereik op segment niveau definiëren om het te gebruiken.

1. Selecteer in uw Azure VMware-oplossing privécloud onder **werkbelasting netwerk** de optie **DHCP**  >  **toevoegen**.

2. Selecteer **DHCP-server** of **DHCP-Relay** en geef een naam op voor de server of relay en drie IP-adressen. 

   >[!NOTE]
   >Voor DHCP Relay is slechts één IP-adres vereist voor een geslaagde configuratie.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Scherm afbeelding die laat zien hoe u een DHCP-server of DHCP-Relay toevoegt in azure VMware-oplossingen.":::

4. Voltooi de DHCP-configuratie door [DHCP-bereiken op de logische segmenten op te geven](#create-an-nsx-t-segment-in-the-azure-portal) en selecteer vervolgens **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Poort spiegeling configureren in de Azure Portal
U kunt poort spiegeling configureren om netwerk verkeer te bewaken waarbij een kopie van elk pakket wordt doorgestuurd van de ene netwerk switch poort naar een andere. Met deze optie wordt een protocol analyse op de poort geplaatst waarop de gespiegelde gegevens worden ontvangen. Het analyseert het verkeer van een bron, een virtuele machine of een groep virtuele machines, en wordt vervolgens naar een gedefinieerde bestemming verzonden. 

Als u poort spiegeling wilt instellen in de Azure VMware-oplossingen console, voert u de volgende handelingen uit:

* [Stap 1. Bron-en doel-Vm's of VM-groepen maken](#step-1-create-source-and-destination-vms-or-vm-groups) : de bron groep heeft één virtuele machine of meerdere virtuele machines waar het verkeer wordt gespiegeld.

* [Stap 2. Een profiel voor poort spiegeling maken](#step-2-create-a-port-mirroring-profile) : u definieert de richting van het verkeer voor de bron-en doel-VM-groepen.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Stap 1. Bron-en doel-Vm's of VM-groepen maken

In deze stap maakt u een bron-VM-groep en een doel-VM-groep.

1. Selecteer in uw Azure VMware-oplossing privécloud onder **werkbelasting netwerk** de optie **poort spie gelen**  >  **VM-groepen**  >  **toevoegen**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Scherm afbeelding die laat zien hoe u een VM-groep maakt voor poort spiegeling.":::

1. Geef een naam op voor de nieuwe VM-groep, selecteer de gewenste Vm's in de lijst en klik vervolgens op **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Scherm opname van de lijst met virtuele machines die aan de VM-groep moeten worden toegevoegd.":::

1. Herhaal deze stappen om de doel-VM-groep te maken.

### <a name="step-2-create-a-port-mirroring-profile"></a>Stap 2. Een poort spiegel profiel maken

In deze stap definieert u een profiel voor de locatie van de bron-en doel-VM van het verkeer.

>[!NOTE]
>Zorg ervoor dat u zowel de bron-als de doel-VM-groep hebt gemaakt.

1. Selecteer **poort spiegeling**  >  **toevoegen** en geef vervolgens het volgende op:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Scherm afbeelding met de informatie die is vereist voor het poort spiegel profiel.":::

   - **Naam van poort spiegeling** : beschrijvende naam voor het profiel.
   - **Richting** : Selecteer uit ingangs-, uitgangs-of bidirectionele.
   - **Bron** : Selecteer de bron-VM-groep.
   - **Doel** : Selecteer de doel-VM-groep.
   - **Beschrijving** : Voer een beschrijving in voor het spie gelen van de poort.

1. Selecteer **OK** om het profiel te volt ooien. 

   Het profiel en de VM-groepen zijn zichtbaar in de Azure VMware-oplossings console.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Een DNS-doorstuur server configureren in de Azure Portal
U configureert een DNS-doorstuur server waarbij specifieke DNS-aanvragen worden doorgestuurd naar een aangewezen DNS--servers voor oplossing.  Een DNS-doorstuur server is gekoppeld aan een **standaard-DNS-zone** en Maxi maal drie **FQDN-zones**.

>[!TIP]
>U kunt ook de [NSX-T-beheer console gebruiken om een DNS-doorstuur server te configureren](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Als u een DNS-doorstuur server wilt instellen in de Azure VMware-oplossings console, voert u de volgende handelingen uit:

* [Stap 1. Een standaard-DNS-zone en FQDN-zone configureren](#step-1-configure-a-default-dns-zone-and-fqdn-zone) : wanneer een DNS-query wordt ontvangen, vergelijkt een DNS-doorstuur server de domein naam met de domein namen in de FQDN DNS-zone. 

* [Stap 2. DNS-service configureren](#step-2-configure-dns-service) : u configureert de DNS-doorstuur server.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Stap 1. Een standaard-DNS-zone en FQDN-zone configureren
U configureert een standaard-DNS-zone en FQDN-zone om DNS-query's naar de upstream-server te verzenden.  Wanneer een DNS-query wordt ontvangen, vergelijkt de DNS-doorstuur server de domein naam in de query met de domein namen van de FQDN DNS-zones. Als er een overeenkomst wordt gevonden, wordt de query doorgestuurd naar de DNS-servers die zijn opgegeven in de FQDN DNS-zone. Als er geen overeenkomst wordt gevonden, wordt de query doorgestuurd naar de DNS-servers die zijn opgegeven in de standaard-DNS-zone.

>[!NOTE]
>Er moet een standaard-DNS-zone worden gedefinieerd voordat u een FQDN-zone configureert. 

1. Selecteer in uw Azure VMware-oplossing privécloud onder **werkbelasting netwerk** de optie **DNS**  >  **DNS-zones**  >  **toevoegen**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Scherm afbeelding die laat zien hoe DNS-zones en een DNS-service worden toegevoegd.":::

1. Selecteer **standaard-DNS-zone** en geef het volgende op:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Scherm opname waarin wordt getoond hoe een standaard-DNS-zone moet worden toegevoegd.":::

   1. Een naam voor de DNS-zone.

   1. Maxi maal drie IP-adressen van de DNS-server in de indeling van **8.8.8.8**.

1. Selecteer de **zone FQDN** en geef het volgende op:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Scherm opname waarin wordt getoond hoe een FQDN-zone moet worden toegevoegd. ":::

   1. Een naam voor de DNS-zone.

   1. Het FQDN-domein.

   1. Maxi maal drie IP-adressen van de DNS-server in de indeling van **8.8.8.8**.

1. Selecteer **OK** om het toevoegen van de standaard-DNS-zone en DNS-service te volt ooien.

### <a name="step-2-configure-dns-service"></a>Stap 2. DNS-service configureren

1. Selecteer het tabblad **DNS-service** , selecteer **toevoegen** en geef het volgende op:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Scherm afbeelding met de informatie die vereist is voor de DNS-service.":::

   1. Een naam voor de DNS-service.

   1. Voer het IP-adres voor de DNS-service in.

   1. Selecteer de standaard-DNS-zone die u hebt gemaakt op het tabblad DNS-zones.

   1. Selecteer de FQDN-zones die u hebt toegevoegd op het tabblad DNS-zones.

   1. Selecteer het **logboek niveau**.

   >[!TIP]
   >**Tier-1-gateway** is standaard geselecteerd en weerspiegelt de gateway die is gemaakt bij het implementeren van de Azure VMware-oplossing.

1. Selecteer **OK**. 

   De DNS-service is toegevoegd.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Scherm opname van de DNS-service is toegevoegd.":::

