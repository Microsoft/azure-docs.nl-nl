---
title: 'Een computer verbinden met een virtueel netwerk met behulp van P2S: certificaat verificatie: Azure Portal klassiek'
titleSuffix: Azure VPN Gateway
description: Een klassieke, punt-naar-site-VPN Gateway verbinding maken met behulp van de Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94657071"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Een punt-naar-site-verbinding configureren met behulp van verificatie via een certificaat (klassiek)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

In dit artikel wordt beschreven hoe u een VNet maakt met een punt-naar-site-verbinding. U maakt dit VNet met het klassieke implementatie model met behulp van de Azure Portal. Deze configuratie maakt gebruik van certificaten, zelfondertekende door een certificeringsinstantie uitgegeven, om de client te verifiëren waarmee verbinding wordt gemaakt. U kunt deze configuratie ook maken met een ander implementatieprogramma of -model, met behulp van de opties die worden beschreven in de volgende artikelen:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

U gebruikt een punt-naar-site-VPN-gateway (P2S) om vanaf een afzonderlijke clientcomputer een beveiligde verbinding te maken met uw virtuele netwerk. Point-to-site-verbindingen zijn nuttig als u verbinding wilt maken met uw VNet vanaf een externe locatie. Een P2S-VPN is een uitstekende oplossing in plaats van een site-naar-site-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer.

> [!IMPORTANT]
> Het klassieke implementatiemodel ondersteunt alleen Windows VPN-clients en maakt gebruik van het SSTP-protocol (Secure Socket Tunneling Protocol), een VPN-protocol op basis van SSL. Voor de ondersteuning van niet-Windows VPN-clients, moet uw VNet worden gemaakt met het implementatiemodel van Resource Manager. Het Resource Manager-implementatiemodel biedt naast ondersteuning voor SSTP ook ondersteuning voor het VPN-type IKEv2. Zie [Informatie over P2S-verbindingen](point-to-site-about.md) voor meer informatie.
>
>

![Punt-naar-site-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Instellingen en vereisten

### <a name="requirements"></a>Vereisten

Voor Point-to-site-verificatie verbindingen voor certificaten zijn de volgende items vereist. In dit artikel vindt u stappen die u kunnen helpen bij het maken van deze.

* Een dynamische VPN-gateway.
* De openbare sleutel (CER-bestand) voor een basiscertificaat dat is geüpload naar Azure. Deze sleutel wordt beschouwd als een vertrouwd certificaat en wordt gebruikt voor verificatie.
* Op basis van het basiscertificaat wordt een clientcertificaat gegenereerd voor installatie op elke clientcomputer die wordt verbonden. Dit certificaat wordt gebruikt voor clientverificatie.
* Op elke clientcomputer die wordt verbonden, moet bovendien een VPN-clientconfiguratiepakket worden gegenereerd en geïnstalleerd. Het clientconfiguratiepakket configureert de systeemeigen VPN-client die zich al op het systeem bevindt met de benodigde gegevens om verbinding te maken met het VNet.

Punt-naar-site-verbindingen hebben geen VPN-apparaat of on-premises openbaar IP-adres nodig. De VPN-verbinding is gemaakt via SSTP (Secure Socket Tunneling Protocol). Wij ondersteunen SSTP versies 1.0, 1.1 en 1.2 aan de serverzijde. De client besluit welke versie moet worden gebruikt. Voor Windows 8.1 en hoger, gebruikt SSTP standaard 1.2.

Zie [about Point-to-site connections](point-to-site-about.md) en de [Veelgestelde vragen](#faq)voor meer informatie.

### <a name="example-settings"></a>Voorbeeldinstellingen

Gebruik de volgende waarden om een testomgeving te maken of verwijs ernaar om meer inzicht te krijgen in de voorbeelden in dit artikel:

* **Resource groep:** TestRG
* **VNet-naam:** VNet1
* **Adres ruimte:** 192.168.0.0/16 <br>In dit voorbeeld gebruiken we slechts één adresruimte. U kunt meer dan één adresruimte voor uw VNet hebben.
* **Subnetnaam:** FrontEnd
* **Adres bereik van subnet:** 192.168.1.0/24
* **Gatewaysubnet**: 10.11.255.0/27
* **Regio:** (US) US - oost
* **Adres ruimte van client:** 172.16.201.0/24 <br> VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de opgegeven pool.
* **Verbindings type**: Selecteer **punt-naar-site**.
* **GatewaySubnet-adres bereik (CIDR-blok):** 192.168.200.0/24

Controleer eerst of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Een virtueel netwerk maken

Als u al beschikt over een VNet, controleert u of de instellingen compatibel zijn met het ontwerp van de VPN-gateway. Let vooral op eventuele subnetten die met andere netwerken overlappen.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Een VPN-gateway maken

1. Navigeer naar het VNet dat u hebt gemaakt.
1. Selecteer op de pagina VNet onder instellingen de optie **Gateway**. Op de pagina **Gateway** kunt u de gateway voor het virtuele netwerk weer geven. Dit virtuele netwerk heeft nog geen gateway. Klik **hier om een verbinding en een gateway toe te voegen**.
1. Selecteer op de pagina **een VPN-verbinding en gateway configureren** de volgende instellingen:

   * Verbindingstype: punt-naar-site-verbinding
   * Adres ruimte van client: Voeg het IP-adres bereik toe van waaruit de VPN-clients een IP-adres ontvangen wanneer er verbinding wordt gemaakt. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding maakt.
1. Laat het selectie vakje voor **een gateway op dit moment niet configureren** ongedaan. We gaan een gateway maken.
1. Selecteer onder aan de pagina **volgende: Gateway >**.
1. Selecteer op het tabblad **Gateway** de volgende waarden:

   * **Grootte:** De grootte is de gateway-SKU voor de gateway van uw virtuele netwerk. In de Azure-portal is de standaard-SKU ingesteld op **Standaard**. Zie [about VPN gateway Settings](vpn-gateway-about-vpn-gateway-settings.md#gwsku)(Engelstalig) voor meer informatie over Gateway-sku's.
   * **Routerings type:** U moet **dynamisch** selecteren voor een punt-naar-site-configuratie. Statische route ring werkt niet.
   * **Gateway-subnet:** Dit veld is al ingevuld. U kunt de naam niet wijzigen. Als u de naam probeert te wijzigen met behulp van Power shell of een andere manier, werkt de gateway niet goed.
   * **Adres bereik (CIDR-blok):** Hoewel het mogelijk is een gateway-subnet te maken als/29, raden we u aan een groter subnet te maken dat meer adressen bevat door Mini maal/28 of/27 te selecteren. Hierdoor beschikt u over genoeg adressen voor eventuele aanvullende configuraties. Als u met gatewaysubnetten werkt, vermijd dan om een netwerkbeveiligingsgroep (NSG) te koppelen aan het gatewaysubnet. Een koppeling van een netwerkbeveiligingsgroep naar dit subnet zorgt er mogelijk voor dat uw VPN-gateway niet werkt zoals verwacht.
1. Selecteer **Beoordelen en maken** om de instellingen te valideren.
1. Wanneer de validatie is geslaagd, selecteert u **maken**. Een VPN-gateway wordt binnen maximaal 45 minuten voltooid. De daadwerkelijke instelduur hangt af van de gateway-SKU die u selecteert.

## <a name="create-certificates"></a><a name="generatecerts"></a>Certificaten maken

Certificaten worden in Azure gebruikt om VPN-clients voor punt-naar-site-VPN's te verifiëren. U uploadt de informatie van de openbare sleutel over het basiscertificaat naar Azure. De openbare sleutel wordt dan beschouwd als *vertrouwd*. Clientcertificaten moeten worden gegenereerd op basis van het vertrouwde basiscertificaat en geïnstalleerd op elke clientcomputer. Dit gebeurt in het certificaatarchief Certificates-Current User\Personal\Certificates. Het certificaat wordt gebruikt om de client te verifiëren bij het maken van verbinding met het VNet. 

Als u zelfondertekende certificaten gebruikt, moeten ze worden gemaakt met behulp van specifieke parameters. U kunt een zelfondertekend certificaat maken met behulp van de instructies voor [PowerShell en Windows 10](vpn-gateway-certificates-point-to-site.md) of [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Het is belangrijk dat u de stappen volgt in deze instructies als u zelfondertekende basiscertificaten gebruikt en clientcertificaten uit het zelfondertekende basiscertificaat genereert. Anders zijn de certificaten die u maakt niet compatibel met P2S-verbindingen en treedt er een verbindingsfout op.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>De openbare sleutel (.cer) voor het basiscertificaat verkrijgen

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Een clientcertificaat genereren

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Het CER-bestand van het basiscertificaat uploaden

Wanneer de gateway is gemaakt, uploadt u het CER-bestand (dat de informatie over de openbare sleutel bevat) voor een vertrouwd basiscertificaat voor de Azure-server. Uploadt de persoonlijke sleutel voor het basiscertificaat niet. Nadat u het certificaat hebt geüpload, kan Azure daarmee clients met een geïnstalleerd clientcertificaat (gemaakt op basis van het vertrouwde basiscertificaat) verifiëren. Indien nodig kunt u later aanvullende vertrouwde basiscertificaatbestanden uploaden (maximaal 20).

1. Navigeer naar het virtuele netwerk dat u hebt gemaakt.
1. Onder **instellingen** selecteert u **punt-naar-site-verbindingen**.
1. Selecteer **certificaat beheren**.
1. Selecteer **Uploaden**.
1. Selecteer in het deel venster **een certificaat uploaden** het mappictogram en navigeer naar het certificaat dat u wilt uploaden.
1. Selecteer **Uploaden**.
1. Nadat het certificaat is geüpload, kunt u het weer geven op de pagina certificaat beheren. Mogelijk moet u **vernieuwen** selecteren om het certificaat dat u zojuist hebt geüpload weer te geven.

## <a name="configure-the-client"></a>De client configureren

Als u verbinding wilt maken met een VNet met behulp van een punt-naar-site-VPN, moet op elke client een pakket worden geïnstalleerd om de systeemeigen Windows VPN-client te configureren. Met het configuratiepakket wordt de systeemeigen Windows VPN-client geconfigureerd met de instellingen die nodig zijn om verbinding te maken met het virtuele netwerk.

U kunt hetzelfde configuratiepakket voor de VPN-client gebruiken op elke clientcomputer, mits de versie overeenkomt met de architectuur van de client. Zie [about Point-to-site connections](point-to-site-about.md) en de [Veelgestelde vragen](#faq)voor de lijst met ondersteunde client besturingssystemen.

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Een configuratiepakket voor VPN-clients genereren en installeren

1. Navigeer naar de instellingen voor **punt-naar-site-verbindingen** voor uw VNet.
1. Selecteer boven aan de pagina het Download pakket dat overeenkomt met het besturings systeem van de client waarop het wordt geïnstalleerd:

   * Selecteer voor 64-bits clients **VPN-client (64-bits)**.
   * Selecteer voor 32-bits clients **VPN-client (32-bits)**.

1. Azure genereert een pakket met de specifieke instellingen die de client nodig heeft. Telkens wanneer u wijzigingen aanbrengt in het VNet of de gateway, moet u een nieuw client configuratie pakket downloaden en installeren op uw client computers.
1. Nadat het pakket is gegenereerd, selecteert u **downloaden**.
1. Installeer het client configuratie pakket op uw client computer. Als u een SmartScreen-pop-upvenster ziet met de melding dat uw PC door Windows is beveiligd, selecteert u **meer info** en selecteert u vervolgens **toch uitvoeren**. U kunt het pakket ook opslaan en op andere clientcomputers installeren.

### <a name="install-a-client-certificate"></a>Een clientcertificaat installeren

Voor deze oefening, wanneer u het client certificaat hebt gegenereerd, is het automatisch op uw computer geïnstalleerd. Als u een P2S-verbinding wilt maken vanaf een andere client computer dan die waarmee de client certificaten worden gegenereerd, moet u het gegenereerde client certificaat op die computer installeren.

Wanneer u een clientcertificaat installeert, hebt u het wachtwoord nodig dat is gemaakt tijdens het exporteren van het clientcertificaat. Normaal gesproken kunt u het certificaat installeren door erop dubbel te klikken. Zie [Een geëxporteerd clientcertificaat installeren](vpn-gateway-certificates-point-to-site.md#install) voor meer informatie.

## <a name="connect-to-your-vnet"></a>Verbinding maken met uw VNet

>[!NOTE]
>U moet beheerdersrechten hebben op de clientcomputer waarmee u de verbinding tot stand brengt.
>

1. Ga op de client computer naar VPN-instellingen.
1. Selecteer de VPN-verbinding die u hebt gemaakt. Als u de voorbeeld instellingen hebt gebruikt, krijgt de verbinding de naam **groep TestRG VNet1**.
1. Selecteer **Verbinding maken**.
1. Selecteer **verbinding maken** in het vak Windows Azure Virtual Network. Als er een pop-upbericht over het certificaat wordt weer gegeven, selecteert u **door gaan** met verhoogde bevoegdheden en **Ja** om de configuratie wijzigingen te accepteren.
1. Wanneer de verbinding is geslaagd, ziet u een **verbonden** melding.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>De VPN-verbinding controleren

1. Controleer of uw VPN-verbinding actief is. Open een opdrachtprompt met verhoogde bevoegdheid op de clientcomputer en voer **ipconfig/all** uit.
1. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen, is een van de adressen binnen het adresbereik van de punt-naar-site-verbinding dat u hebt opgegeven tijdens het maken van het VNet. De resultaten moeten er als volgt uitzien:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Vertrouwde basiscertificaten toevoegen of verwijderen

U kunt vertrouwde basiscertificaat toevoegen in en verwijderen uit Azure. Wanneer u een basiscertificaat verwijdert, kunnen clients met een certificaat dat is gegenereerd op basis van dat basiscertificaat niet meer worden geverifieerd en geen verbinding maken. Om deze clients opnieuw te kunnen verifiëren en verbinding te laten maken, moet u een nieuw clientcertificaat installeren dat is gegenereerd op basis van een basiscertificaat dat in Azure wordt vertrouwd.

### <a name="add-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat toevoegen

U kunt Maxi maal 20 vertrouwde. cer-bestanden van het basis certificaat toevoegen aan Azure met hetzelfde proces dat u hebt gebruikt om het eerste vertrouwde basis certificaat toe te voegen.

### <a name="remove-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat verwijderen

1. Selecteer **certificaat beheren** in de sectie **punt-naar-site-verbindingen** van de pagina voor uw VNet.
1. Selecteer het beletsel teken naast het certificaat dat u wilt verwijderen en selecteer vervolgens **verwijderen**.

## <a name="to-revoke-a-client-certificate"></a>Een clientcertificaat intrekken

U kunt zo nodig een clientcertificaat intrekken. Met de certificaatintrekkingslijst kunt u selectief punt-naar-site-verbinding weigeren op basis van afzonderlijke clientcertificaten. Deze methode wijkt af van het verwijderen van een vertrouwd basiscertificaat. Als u een vertrouwd .cer-basiscertificaat uit Azure verwijdert, wordt de toegang ingetrokken voor alle clientcertificaten die zijn gegenereerd/ondertekend door het ingetrokken basiscertificaat. Als u in plaats van een basiscertificaat een clientcertificaat intrekt, kunt u de certificaten die zijn gegenereerd op basis van het basiscertificaat, blijven gebruiken voor verificatie van de punt-naar-site-verbinding.

De algemene procedure is het basiscertificaat te gebruiken om de toegang te beheren op het team- of organisatieniveau, terwijl u ingetrokken clientcertificaten gebruikt voor nauwkeuriger toegangsbeheer bij afzonderlijke gebruikers.

U kunt een clientcertificaat intrekken door de vingerafdruk toe te voegen aan de intrekkingslijst.

1. Haal de vingerafdruk voor het clientcertificaat op. Zie voor meer informatie [De vingerafdruk van een certificaat ophalen](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Kopieer de gegevens naar een teksteditor en verwijder de spaties, zodat u een doorlopende tekenreeks overhoudt.
1. Navigeer naar **punt-naar-site-VPN-verbinding** en selecteer vervolgens **certificaat beheren**.
1. Selecteer **Intrekkingslijst** om de pagina **Intrekkingslijst** te openen.
1. Plak in **Vingerafdruk** de vingerafdruk van het certificaat als een doorlopende regel tekst, zonder spaties.
1. Selecteer **+ toevoegen aan lijst** om de vinger afdruk toe te voegen aan de CERTIFICAATINTREKKINGSLIJST (CRL).

Wanneer het bijwerken is voltooid, kan het certificaat niet langer worden gebruikt om verbinding te maken. Clients die verbinding proberen te maken met behulp van het certificaat, ontvangen een bericht waarin wordt gemeld dat het certificaat niet meer geldig is.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie.

* Zie [Overzicht van VM-netwerken in Azure en onder Linux](../virtual-machines/network-overview.md) voor meer informatie over netwerken en virtuele Linux-machines.

* Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshoot Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).