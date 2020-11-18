---
title: 'Een on-premises netwerk verbinden met een virtueel Azure-netwerk: site-naar-site-VPN (klassiek): Portal | Microsoft Docs'
description: Een IPSec-verbinding maken vanaf uw on-premises netwerk met een klassiek virtueel Azure-netwerk via het openbare internet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 346536f5797841a850ef97dc4667110eafa721a4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656953"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Een site-naar-site-verbinding maken met behulp van Azure Portal (klassiek)

In dit artikel leest u hoe u Azure Portal gebruikt om een site-naar-site-VPN-gatewayverbinding te maken vanaf uw on-premises netwerk naar het VNet. De stappen in dit artikel zijn van toepassing op het klassieke implementatie model en zijn niet van toepassing op het huidige implementatie model Resource Manager. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Een site-naar-site-VPN-gatewayverbinding wordt gebruikt om een on-premises netwerk via een IPsec-/IKE-VPN-tunnel (IKEv1 of IKEv2) te verbinden met een virtueel Azure-netwerk. Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie [Overzicht van VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN-gateways.

![Diagram: cross-premises site-naar-site-VPN-gatewayverbinding](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Controleer voordat u met de configuratie begint, of aan de volgende criteria is voldaan:

* U hebt vastgesteld dat u wilt werken met het klassieke implementatiemodel. Als u wilt werken met het Resource Manager-implementatiemodel, gaat u naar [Een site-naar-site-verbinding maken (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Het is raadzaam om het Resource Manager-implementatie model te gebruiken, omdat het klassieke model verouderd is.
* U hebt een compatibel VPN-apparaat nodig en iemand die dit kan configureren. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md) voor meer informatie over compatibele VPN-apparaten en -apparaatconfiguratie.
* Controleer of u een extern gericht openbaar IPv4-adres voor het VPN-apparaat hebt.
* Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken. Wanneer u deze configuratie maakt, moet u de IP-adresbereikvoorvoegsels opgeven die Azure naar uw on-premises locatie doorstuurt. Geen van de subnetten van uw on-premises netwerk kan overlappen met de virtuele subnetten waarmee u verbinding wilt maken.
* Power shell is vereist om de gedeelde sleutel op te geven en de VPN-gateway verbinding te maken. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Voorbeeld van configuratiewaarden voor deze oefening

In de voorbeelden in dit artikel worden de volgende waarden gebruikt. U kunt deze waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel. Wanneer u werkt met IP-adres waarden voor adres ruimte, kunt u het beste met de netwerk beheerder instemmen om overlappende adres ruimten te voor komen, wat van invloed kan zijn op route ring. Vervang in dat geval de IP-adres waarden met uw eigen als u een werkende verbinding wilt maken.

* **Resource groep:** TestRG1
* **VNet-naam:** TestVNet1
* **Adres ruimte:** 10.11.0.0/16
* **Subnetnaam:** FrontEnd
* **Adres bereik van subnet:** 10.11.0.0/24
* **Gatewaysubnet**: 10.11.255.0/27
* **Regio:** (US) US - oost
* **Lokale sitenaam:** Site2
* **Clientadresruimte:** de adresruimte op uw on-premises site.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Een virtueel netwerk maken

Als u een virtueel netwerk maakt om te gebruiken met een S2S-verbinding, moet u ervoor zorgen dat de opgegeven adresruimten niet overlappen met een of meer van de clientadresruimten voor de lokale sites waarmee u verbinding wilt maken. Als u overlappende subnetten hebt, werkt de verbinding mogelijk niet goed.

* Als u al beschikt over een VNet, controleert u of de instellingen compatibel zijn met het ontwerp van de VPN-gateway. Let vooral op eventuele subnetten die met andere netwerken overlappen.

* Als u nog geen virtueel netwerk hebt, maakt u er een. De schermafbeeldingen dienen alleen als voorbeeld. Zorg dat u de waarden vervangt door die van uzelf.

### <a name="to-create-a-virtual-network"></a>Een virtueel netwerk maken

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>De site en gateway configureren

### <a name="to-configure-the-site"></a>De site configureren

De lokale site verwijst doorgaans naar uw on-premises locatie. Het bevat het IP-adres van het VPN-apparaat waarmee u verbinding gaat maken en de IP-adresbereiken die via de VPN-gateway naar het VPN-apparaat worden gerouteerd.

1. Selecteer op de pagina voor uw VNet onder **instellingen** de optie **site-naar-site-verbindingen**.
1. Op de pagina site-naar-site-verbindingen selecteert u **+ toevoegen**.
1. Op de pagina **een VPN-verbinding en gateway configureren** voor het **verbindings type**, moet **u site-naar-site** geselecteerd laten. Voor deze oefening moet u een combi natie van de [voorbeeld waarden](#values) en uw eigen waarden gebruiken.

   * **IP-adres voor de VPN-gateway:** dit is het openbare IP-adres van het VPN-apparaat voor uw on-premises netwerk. Voor het VPN-apparaat is een openbaar IPv4-adres vereist. Geef een geldig openbaar IP-adres op voor het VPN-apparaat waarmee u verbinding wilt maken. Het moet bereikbaar zijn voor Azure. Als u het IP-adres van het VPN-apparaat niet kent, kunt u altijd een tijdelijke aanduiding invoegen (zolang deze maar de indeling van een geldig openbaar IP-adres heeft) en dit later wijzigen.

   * **Clientadresruimte:** vermeld de IP-adresbereiken die u via deze gateway naar het lokale on-premises netwerk wilt routeren. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de hier opgegeven bereiken niet overlappen met bereiken van andere netwerken waarmee uw virtuele netwerk is verbonden of met de adresbereiken van het virtuele netwerk zelf.
1. Klik onder aan de pagina op niet controleren + maken. Selecteer in plaats daarvan **volgende: Gateway>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>De gateway van het virtuele netwerk configureren

1. Selecteer op de pagina **Gateway** de volgende waarden:

   * **Grootte:** Dit is de gateway-SKU die u gebruikt om uw virtuele netwerk gateway te maken. Klassieke VPN-gateways gebruiken de oude (verouderde) gateway-SKU's. Zie [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Werken met virtuele netwerkgateway-SKU's (oude SKU's)) voor meer informatie over de oude gateway-SKU's. U kunt voor deze oefening **standaard** selecteren.

   * **Routerings type:** Selecteer het routerings type voor uw gateway. Dit staat ook bekend als het VPN-type. Het is belang rijk dat u het juiste type selecteert, omdat u de gateway niet van het ene naar het andere type kunt converteren. Het VPN-apparaat moet compatibel zijn met het routingtype dat u selecteert. Zie [over VPN gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md#vpntype)voor meer informatie over het routerings type. Mogelijk ziet u artikelen die verwijzen naar de VPN-typen RouteBased en PolicyBased. Het type Dynamisch komt overeen met RouteBased, en het type Vast komt overeen met PolicyBased. Normaal gesp roken wilt u dynamische route ring.

   * **Gateway-subnet:** De grootte van het gateway-subnet dat u opgeeft, is afhankelijk van de configuratie van de VPN-gateway die u wilt maken. Hoewel het mogelijk is om een gatewaysubnet van slechts /29 te maken, wordt minimaal /27 of /28 aanbevolen. U maakt dan een groter subnet dat meer adressen bevat. Met een groter gatewaysubnet beschikt u over voldoende IP-adressen voor mogelijke toekomstige configuraties.

1. Selecteer onder aan de pagina **controleren + maken** om uw instellingen te valideren. Selecteer **maken** om te implementeren. Het kan 45 minuten duren om een virtuele netwerk gateway te maken, afhankelijk van de gateway-SKU die u hebt geselecteerd.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Uw VPN-apparaat configureren

Voor site-naar-site-verbindingen met een on-premises netwerk is een VPN-apparaat vereist. In deze stap configureert u het VPN-apparaat. Bij het configureren van uw VPN-apparaat hebt u de volgende waarden nodig:

* Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft wanneer u uw site-naar-site-VPN-verbinding maakt. In onze voorbeelden gebruiken we een eenvoudige gedeelde sleutel. We raden u aan een complexere sleutel te genereren.
* Het openbare IP-adres van de gateway van uw virtuele netwerk. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Waarden ophalen

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>De verbinding maken

> [!NOTE]
> Voor het klassieke implementatie model is deze stap niet beschikbaar in de Azure Portal of via Azure Cloud Shell. U moet de versie van de Service Management (SM) van de Azure PowerShell-cmdlets lokaal vanaf uw bureau blad gebruiken.
>

In deze stap gebruikt u de waarden uit de vorige stappen om de gedeelde sleutel in te stellen en de verbinding te maken. De sleutel die u instelt, moet dezelfde sleutel zijn die is gebruikt bij de configuratie van het VPN-apparaat.

1. Stel de gedeelde sleutel in en maak de verbinding.

   * Wijzig de waarde-VNetName en de LocalNetworkSiteName-waarde. Gebruik voor een naam die spaties bevat, enkele aanhalingstekens rond de waarde.
   * De '-SharedKey ' is een waarde die u genereert en geeft vervolgens op. In het voor beeld hebben we ' abc123 ' gebruikt, maar u kunt (en) een complexere waarde genereren. Het is van belang dat de waarde die u hier opgeeft, dezelfde waarde is die u hebt opgegeven bij het configureren van het VPN-apparaat.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Wanneer de verbinding is gemaakt, is het resultaat: **Status: geslaagd**.

## <a name="verify-your-connection"></a><a name="verify"></a>De verbinding controleren

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Als u verbindingsproblemen ondervindt, raadpleegt u de sectie **Problemen oplossen** van de inhoudsopgave in het linkerdeelvenster.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Een VPN-gateway opnieuw instellen

Het opnieuw instellen van een Azure VPN-gateway is handig als u cross-premises VPN-connectiviteit verliest in een of meer Site-to-Site VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways. Zie [Een VPN-gateway opnieuw instellen](./reset-gateway.md#resetclassic) voor de stappen.

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Een gateway-SKU wijzigen

Zie [het formaat van een gateway-SKU](vpn-gateway-about-SKUS-legacy.md#classicresize)wijzigen voor meer informatie over het aanpassen van een gateway-SKU.

## <a name="next-steps"></a>Volgende stappen

* Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie.
* Zie [over geforceerde](vpn-gateway-about-forced-tunneling.md)tunneling voor meer informatie over geforceerde tunneling.