---
title: 'Een verbinding maken tussen VNets: klassiek: Azure Portal'
description: Verbinding maken met virtuele Azure-netwerken met behulp van Power shell en het Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103217"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Een VNet-naar-VNet-verbinding configureren (klassiek)

Dit artikel helpt u bij het maken van een VPN-gateway verbinding tussen virtuele netwerken. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagram van de klassieke VNet-naar-VNet-architectuur":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

De stappen in dit artikel zijn van toepassing op het klassieke implementatie model en de Azure Portal. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Klassieke](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [VNets verbinden in verschillende implementatie modellen](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Over VNet-naar-VNet-verbindingen

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (VNet-naar-VNet) in het klassieke implementatie model met behulp van een VPN-gateway is vergelijkbaar met het verbinden van een virtueel netwerk met een on-premises site locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden.

De VNets die u verbindt, kunnen zich in verschillende abonnementen en verschillende regio's bevindt. U kunt VNet combi neren met VNet-communicatie met configuraties met meerdere locaties. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagram waarin verbindingen worden weer gegeven":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Waarom virtuele netwerken koppelen?

U wilt virtuele netwerken wellicht koppelen om de volgende redenen:

* **Geografische redundantie en aanwezigheid tussen regio's**

  * U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
  * Met Azure Load Balancer en de clustering technologie van micro soft of derden kunt u een werk belasting met hoge Beschik baarheid instellen met georedundantie over meerdere Azure-regio's. Een belangrijk voorbeeld hiervan is het instellen van SQL Always On met beschikbaarheidsgroepen verspreid over meerdere Azure-regio's.
* **Regionale toepassingen met meerdere lagen met een sterke isolatie grens**

  * Binnen dezelfde regio kunt u multi-tier-toepassingen met meerdere VNets die zijn verbonden met een sterke isolatie en beveiligde communicatie tussen lagen instellen.
* **Cross-abonnement, communicatie tussen organisaties in azure**

  * Als u meerdere Azure-abonnementen hebt, kunt u werk belastingen van verschillende abonnementen samen met een beveiligde verbinding tussen virtuele netwerken verbinden.
  * Voor ondernemingen of service providers kunt u communicatie tussen organisaties met beveiligde VPN-technologie in azure inschakelen.

Zie voor meer informatie over verbindingen tussen VNets de [Aandachtspunten bij VNet-naar-VNet](#faq) aan het einde van dit artikel.

## <a name="prerequisites"></a>Vereisten

We gebruiken de portal voor de meeste stappen, maar u moet Power shell gebruiken om de verbindingen tussen de VNets te maken. U kunt de verbindingen niet maken met behulp van de Azure Portal omdat er geen manier is om de gedeelde sleutel op te geven in de portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Planning

Het is belang rijk om te bepalen welke bereiken u gaat gebruiken voor het configureren van uw virtuele netwerken. Voor deze configuratie moet u ervoor zorgen dat geen van uw VNet-bereiken met elkaar overlappen, of met een van de lokale netwerken waarmee ze verbinding maken.

### <a name="vnets"></a><a name="vnet"></a>VNets

Voor deze oefening gebruiken we de volgende voorbeeld waarden:

**Waarden voor TestVNet1**

Naam: TestVNet1<br>
Adres ruimte: 10.11.0.0/16, 10.12.0.0/16 (optioneel)<br>
Subnetnaam: standaard<br>
Subnetadresbereik: 10.11.0.0/24<br>
Resource groep: ClassicRG<br>
Locatie: VS - oost<br>
GatewaySubnet: 10.11.1.0/27

**Waarden voor TestVNet4**

Naam: TestVNet4<br>
Adres ruimte: 10.41.0.0/16, 10.42.0.0/16 (optioneel)<br>
Subnetnaam: standaard<br>
Subnetadresbereik: 10.41.0.0/24<br>
Resource groep: ClassicRG<br>
Locatie: VS - west<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Verbindingen

In de volgende tabel ziet u een voor beeld van hoe u verbinding maakt met uw VNets. Gebruik de bereiken alleen als richt lijn. Noteer de bereiken voor uw virtuele netwerken. U hebt deze informatie nodig voor latere stappen.

In dit voor beeld maakt TestVNet1 verbinding met een lokale netwerk site die u maakt met de naam ' VNet4Local '. De instellingen voor VNet4Local bevatten de adres voorvoegsels voor TestVNet4.
De lokale site voor elk VNet is het andere VNet. De volgende voorbeeld waarden worden gebruikt voor de configuratie:

**Voorbeeld**

| Virtual Network | Adresruimte | Locatie | Maakt verbinding met de lokale netwerk site |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |VS - oost |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |VS - west |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Virtuele netwerken maken

In deze stap maakt u twee klassieke virtuele netwerken: TestVNet1 en TestVNet4. Als u dit artikel als oefening gebruikt, gebruikt u de [voorbeeld waarden](#vnet).

**Houd bij het maken van uw VNets de volgende instellingen in acht:**

* **Virtual Network adres ruimten** : Geef op de pagina adres ruimten Virtual Network het adres bereik op dat u wilt gebruiken voor het virtuele netwerk. Dit zijn de dynamische IP-adressen die worden toegewezen aan de Vm's en andere rolinstanties die u op dit virtuele netwerk implementeert.<br>De adres ruimten die u selecteert, mogen niet overlappen met de adres ruimten voor een van de andere VNets of on-premises locaties waarmee dit VNet verbinding maakt.

* **Locatie** : wanneer u een virtueel netwerk maakt, koppelt u dit aan een Azure-locatie (regio). Als u bijvoorbeeld wilt dat uw virtuele machines die worden geïmplementeerd in uw virtueel netwerk zich fysiek in VS West bevinden, selecteert u die locatie. U kunt de locatie die is gekoppeld aan het virtuele netwerk niet wijzigen nadat u het hebt gemaakt.

**Nadat u uw VNets hebt gemaakt, kunt u de volgende instellingen toevoegen:**

* **Adres ruimte** : er is geen extra adres ruimte vereist voor deze configuratie, maar u kunt extra adres ruimte toevoegen nadat u het VNet hebt gemaakt.

* **Subnetten** : extra subnetten zijn niet vereist voor deze configuratie, maar mogelijk wilt u uw virtuele machines in een subnet hebben dat gescheiden is van uw andere rolinstanties.

* **DNS-servers** : Voer de naam en het IP-adres van de DNS-server in. Met deze instelling wordt geen DNS-server gemaakt. U kunt hiermee de DNS-servers opgeven die u wilt gebruiken voor de naamomzetting voor dit virtuele netwerk.

### <a name="to-create-a-classic-virtual-network"></a>Een klassiek virtueel netwerk maken

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Sites en gateways configureren

Azure gebruikt de instellingen die zijn opgegeven op elke lokale netwerk site om te bepalen hoe verkeer tussen de VNets moet worden gerouteerd. Elk VNet moet verwijzen naar het respectieve lokale netwerk waarnaar u verkeer wilt door sturen. U bepaalt de naam die u wilt gebruiken om naar elke lokale netwerk site te verwijzen. Het is raadzaam om een beschrijvende naam te gebruiken.

TestVNet1 maakt bijvoorbeeld verbinding met een lokale netwerk site die u maakt met de naam ' VNet4Local '. De instellingen voor VNet4Local bevatten de adres voorvoegsels voor TestVNet4.

Houd er wel op dat de lokale site voor elk VNet het andere VNet is.

| Virtual Network | Adresruimte | Locatie | Maakt verbinding met de lokale netwerk site |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |VS - oost |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |VS - west |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Een site configureren

De lokale site verwijst doorgaans naar uw on-premises locatie. Het bevat het IP-adres van het VPN-apparaat waarmee u verbinding gaat maken en de IP-adresbereiken die via de VPN-gateway naar het VPN-apparaat worden gerouteerd.

1. Selecteer op de pagina voor uw VNet onder **instellingen** de optie **site-naar-site-verbindingen**.
1. Op de pagina site-naar-site-verbindingen selecteert u **+ toevoegen**.
1. Op de pagina **een VPN-verbinding en gateway configureren** voor het **verbindings type**, moet **u site-naar-site** geselecteerd laten.

   * **IP-adres voor de VPN-gateway:** dit is het openbare IP-adres van het VPN-apparaat voor uw on-premises netwerk. Voor deze oefening kunt u een dummy adres plaatsen omdat u het IP-adres voor de VPN-gateway voor de andere site nog niet hebt. Bijvoorbeeld 5.4.3.2. Wanneer u de gateway voor het andere VNet later hebt geconfigureerd, kunt u deze waarde aanpassen.

   * **Adres ruimte van client:** De IP-adresbereiken weer geven die u via deze gateway naar het andere VNet wilt omleiden. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de hier opgegeven bereiken niet overlappen met bereiken van andere netwerken waarmee uw virtuele netwerk is verbonden of met de adresbereiken van het virtuele netwerk zelf.
1. Klik onder aan de pagina op niet controleren + maken. Selecteer in plaats daarvan **volgende: Gateway>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Een gateway voor een virtueel netwerk configureren

1. Selecteer op de pagina **Gateway** de volgende waarden:

   * **Grootte:** Dit is de gateway-SKU die u gebruikt om uw virtuele netwerk gateway te maken. Klassieke VPN-gateways gebruiken de oude (verouderde) gateway-SKU's. Zie [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Werken met virtuele netwerkgateway-SKU's (oude SKU's)) voor meer informatie over de oude gateway-SKU's. U kunt voor deze oefening **standaard** selecteren.

   * **Routerings type:** Selecteer het routerings type voor uw gateway. Dit staat ook bekend als het VPN-type. Het is belang rijk dat u het juiste type selecteert, omdat u de gateway niet van het ene naar het andere type kunt converteren. Het VPN-apparaat moet compatibel zijn met het routingtype dat u selecteert. Zie [over VPN gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md#vpntype)voor meer informatie over het routerings type. Mogelijk ziet u artikelen die verwijzen naar de VPN-typen RouteBased en PolicyBased. Het type Dynamisch komt overeen met RouteBased, en het type Vast komt overeen met PolicyBased. Selecteer voor deze configuratie **dynamische**.

   * **Gateway-subnet:** De grootte van het gateway-subnet dat u opgeeft, is afhankelijk van de configuratie van de VPN-gateway die u wilt maken. Hoewel het mogelijk is om een gatewaysubnet van slechts /29 te maken, wordt minimaal /27 of /28 aanbevolen. U maakt dan een groter subnet dat meer adressen bevat. Met een groter gatewaysubnet beschikt u over voldoende IP-adressen voor mogelijke toekomstige configuraties.

1. Selecteer onder aan de pagina **controleren + maken** om uw instellingen te valideren. Selecteer **maken** om te implementeren. Het kan 45 minuten duren om een virtuele netwerk gateway te maken, afhankelijk van de gateway-SKU die u hebt geselecteerd.
1. U kunt door gaan met de volgende stap tijdens het maken van deze gateway.

### <a name="configure-testvnet4-settings"></a>TestVNet4-instellingen configureren

Herhaal de stappen voor het [maken van een site en gateway](#localsite) voor het configureren van TestVNet4 en het vervangen van de waarden wanneer dit nodig is. Als u dit doet als oefening, gebruikt u de [voorbeeld waarden](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Lokale sites bijwerken

Nadat de gateways voor het virtuele netwerk zijn gemaakt voor beide VNets, moet u de eigenschappen van de lokale site aanpassen voor het IP-adres van de **VPN-gateway**.

|VNet-naam|Verbonden site|IP-adres van de gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|IP-adres van de VPN-gateway voor TestVNet4|
|TestVNet4|VNet1Local|IP-adres van de VPN-gateway voor TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Deel 1: het open bare IP-adres van de gateway van het virtuele netwerk ophalen

1. Navigeer naar uw VNet door naar de **resource groep** te gaan en het virtuele netwerk te selecteren.
1. Ga op de pagina voor uw virtuele netwerk naar het deel venster **Essentials** aan de rechter kant en zoek het **IP-adres** van de gateway en Kopieer naar het klem bord.

### <a name="part-2---modify-the-local-site-properties"></a>Deel 2: de eigenschappen van de lokale site wijzigen

1. Onder site-naar-site-verbindingen selecteert u de verbinding. Bijvoorbeeld SiteVNet4.
1. Selecteer **lokale site bewerken** op de pagina **Eigenschappen** voor de site-naar-site-verbinding.
1. Plak in het veld **IP-adres van VPN-gateway** het IP-adres van de VPN-gateway dat u in de vorige sectie hebt gekopieerd.
1. Selecteer **OK**.
1. Het veld wordt bijgewerkt in het systeem. U kunt deze methode ook gebruiken om extra IP-adressen toe te voegen die u wilt omleiden naar deze site.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Deel 3: herhaalde stappen voor het andere VNet

Herhaal de stappen voor TestVNet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Configuratie waarden ophalen

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Verbindingen maken

Wanneer alle vorige stappen zijn voltooid, kunt u de vooraf gedeelde IPsec/IKE-sleutels instellen en de verbinding maken. Deze reeks stappen maakt gebruik van Power shell. VNet-naar-VNet-verbindingen voor het klassieke implementatie model kunnen niet worden geconfigureerd in de Azure Portal omdat de gedeelde sleutel niet kan worden opgegeven in de portal.

In de voor beelden ziet u dat de gedeelde sleutel precies hetzelfde is. De gedeelde sleutel moet altijd overeenkomen. Zorg ervoor dat u de waarden in deze voor beelden vervangt door de exacte namen voor uw VNets en lokale netwerk sites.

1. Maak de verbinding tussen TestVNet1 en TestVNet4. Zorg ervoor dat u de waarden wijzigt.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Maak de verbinding tussen TestVNet4 en TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Wacht totdat de verbindingen zijn geïnitialiseerd. Zodra de gateway is geïnitialiseerd, is de status geslaagd.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>Veelgestelde vragen en overwegingen

Deze overwegingen zijn van toepassing op klassieke virtuele netwerken en klassieke virtuele netwerk gateways.

* De virtuele netwerken kunnen zich in dezelfde of verschillende abonnementen bevindt.
* De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-regio's (locaties) bevinden.
* Een Cloud service of een taakverdelings eindpunt kan geen virtuele netwerken omvatten, zelfs niet als ze met elkaar zijn verbonden.
* Voor het verbinden van meerdere virtuele netwerken hoeven geen VPN-apparaten te worden gebruikt.
* VNet-naar-VNet biedt ondersteuning voor het verbinden van virtuele netwerken van Azure. Het biedt geen ondersteuning voor het verbinden van virtuele machines of Cloud Services die niet zijn geïmplementeerd in een virtueel netwerk.
* VNet-naar-VNet vereist dynamische routerings gateways. Azure static-routerings gateways worden niet ondersteund.
* U kunt virtuele-netwerkverbindingen tegelijk gebruiken met multi-site-VPN’s. Er zijn Maxi maal 10 VPN-tunnels voor een VPN-gateway van een virtueel netwerk die verbinding maakt met andere virtuele netwerken of on-premises sites.
* De adresruimten van de virtuele netwerken en on-premises lokale netwerksites mogen elkaar niet overlappen. Door overlappende adres ruimten wordt het maken van virtuele netwerken of het uploaden van netcfg-configuratie bestanden mislukt.
* Redundante tunnels tussen een paar virtuele netwerken worden niet ondersteund.
* Alle VPN-tunnels voor het VNet, met inbegrip van P2S Vpn's, delen de beschik bare band breedte voor de VPN-gateway en dezelfde SLA voor de VPN-gateway in Azure.
* VNet-naar-VNet-verkeer wordt verplaatst over de Azure-backbone.

## <a name="next-steps"></a>Volgende stappen

Controleer uw verbindingen. Zie [een VPN gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).
