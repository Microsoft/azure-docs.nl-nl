---
title: 'Verbinding maken met een VNet met behulp van P2S VPN-& certificaat verificatie: Portal'
titleSuffix: Azure VPN Gateway
description: Verbind Windows-, macOS-en Linux-clients veilig met een virtueel Azure-netwerk met behulp van P2S en zelfondertekende of door certificerings instanties uitgegeven certificaten. In dit artikel wordt gebruikgemaakt van Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 1c6dad28ada14151b9a1cca0da490e38972ad54d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379162"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configureer een punt-naar-site-VPN-verbinding met een VNet met behulp van systeem eigen Azure-certificaat verificatie: Azure Portal

Dit artikel helpt u bij het veilig verbinden van afzonderlijke clients met Windows, Linux of macOS naar een Azure-VNet. P2S-verbindingen zijn nuttig als u verbinding wilt maken met uw VNet vanaf een externe locatie, bijvoorbeeld als u ook thuis werkt of op een congres verbinding wilt maken. U kunt P2S ook in plaats van een site-naar-site-VPN gebruiken wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Punt-naar-site-verbindingen hebben geen VPN-apparaat of openbaar IP-adres nodig. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol) of IKEv2. Voor meer informatie over punt-naar-site-VPN leest u [About Point-to-Site VPN](point-to-site-about.md) (Over punt-naar-site-VPN).

:::image type="content" source="./media\vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Verbinding maken tussen een computer en een Azure VNet-Point-to-site-verbindings diagram":::

Zie [about Point-to-site VPN](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site-VPN. Als u deze configuratie wilt maken met behulp van de Azure PowerShell, raadpleegt u [een punt-naar-site-VPN configureren met behulp van Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Vereisten

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Voorbeeldwaarden

U kunt de volgende waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel:

* **VNet-naam:** VNet1
* **Adres ruimte:** 10.1.0.0/16<br>In dit voorbeeld gebruiken we slechts één adresruimte. U kunt meer dan één adresruimte voor uw VNet hebben.
* **Subnetnaam:** FrontEnd
* **Adres bereik van subnet:** 10.1.0.0/24
* **Abonnement:** controleer of u het juiste abonnement hebt in het geval u er meer dan één hebt.
* **Resourcegroep:** TestRG1
* **Locatie:** VS - oost
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Naam van de virtuele netwerk gateway:** VNet1GW
* **Gatewaytype:** VPN
* **VPN-type:** Op route gebaseerd
* **Openbare IP-adresnaam:** VNet1GWpip
* **Verbindings type:** Punt-naar-site
* **Client-adres groep:** 172.16.201.0/24<br>VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de clientadresgroep.

## <a name="virtual-network"></a><a name="createvnet"></a>Virtueel netwerk

In deze sectie gaat u een virtueel netwerk maken.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Gateway van een virtueel netwerk

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

>[!NOTE]
>De basis gateway-SKU biedt geen ondersteuning voor IKEv2-of RADIUS-verificatie. Als u van plan bent Mac-clients verbinding te laten maken met uw virtuele netwerk, moet u de basis-SKU niet gebruiken.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>Certificaten genereren

Certificaten worden in Azure gebruikt om clients te verifiëren die verbinding willen maken met een VNet met behulp van een punt-naar-site-VPN-verbinding. Als u beschikt over het basiscertificaat, [uploadt](#uploadfile) u de gegevens van de openbare sleutel naar Azure. Het basiscertificaat wordt vervolgens als 'vertrouwd' beschouwd door Azure voor verbinding met het virtuele netwerk via P2S. U kunt ook clientcertificaten genereren op basis van het vertrouwde basiscertificaat en deze vervolgens op elke clientcomputer installeren. Het clientcertificaat wordt gebruikt om de client te verifiëren bij het maken van verbinding met het VNet.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Een basis certificaat genereren

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Client certificaten genereren

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Clientadresgroep

De clientadrespool bestaat uit een privé-IP-adresbereik dat u opgeeft. De clients die dynamisch verbinding maken via een punt-naar-site-VPN-verbinding krijgen een IP-adres uit dit bereik. Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken. Als u meerdere protocollen configureert en SSTP een van de protocollen is, wordt de geconfigureerde adres groep op dezelfde manier verdeeld tussen de geconfigureerde protocollen.

1. Nadat de virtuele netwerkgateway is gemaakt, gaat u naar de sectie **Instellingen** van de pagina van de virtuele netwerkgateway. Selecteer bij **instellingen** de optie **punt-naar-site-configuratie**. Selecteer **nu configureren** om de configuratie pagina te openen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="De pagina punt-naar-site-configuratie" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Voeg op de pagina **punt-naar-site-configuratie** in het vak **adres groep** het privé-IP-adres bereik toe dat u wilt gebruiken. VPN-clients ontvangen dynamisch een IP-adres uit het bereik dat u opgeeft. Het minimale subnetmasker is 29 bits voor actief/passief en 28 bits voor actieve/actieve configuratie.
1. Ga door naar de volgende sectie voor het configureren van verificatie-en tunnel typen.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Verificatie-en tunnel typen

In deze sectie configureert u het verificatie type en het tunnel type. Als u het **Tunnel Type** of **verificatie type** niet ziet op de pagina **punt-naar-site-configuratie** , wordt de basis-SKU gebruikt voor uw gateway. De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie. Als u deze instellingen wilt gebruiken, moet u de gateway verwijderen en opnieuw maken met behulp van een andere gateway-SKU.

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tunneltype

Selecteer op de pagina **punt-naar-site-configuratie** het tunnel type. De tunnel opties zijn OpenVPN, SSTP en IKEv2.

* De strongSwan-client op Android en Linux en de systeemeigen IKEv2 VPN-client op iOS en OS x gebruiken alleen de IKEv2-tunnel om verbinding te maken.
* Windows-clients proberen eerst IKEv2 en als dat niet het geval is, gaan ze terug naar SSTP.
* U kunt de OpenVPN-client gebruiken om verbinding te maken met het tunnel type OpenVPN.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Verificatie type

Selecteer voor **verificatie type** **Azure-certificaat**.


## <a name="root-certificate-data"></a><a name="uploadfile"></a>Basis certificaat gegevens

In deze sectie uploadt u de gegevens van het open bare basis certificaat naar Azure. Als het uploaden is voltooid, kan Azure daarmee clients met een geïnstalleerd clientcertificaat (gemaakt op basis van het vertrouwde basiscertificaat) verifiëren.

1. Certificaten worden toegevoegd aan de pagina **Punt-naar-site-configuratie**, in de sectie **Basiscertificaat**.
1. Zorg ervoor dat u het basiscertificaat hebt geëxporteerd als een Base-64 encoded X.509 (.cer)-bestand. U moet het certificaat in deze indeling exporteren, zodat u het certificaat met een teksteditor kunt openen.
1. Open het certificaat met een teksteditor zoals Kladblok. Zorg er bij het kopiëren van de certificaatgegevens voor dat u de tekst als één ononderbroken regel kopieert zonder regelterugloop of regelinvoer. Mogelijk moet u de weergave in de teksteditor wijzigen naar Symbool weergeven/Alle tekens weergeven om de regelterugloop en regelinvoer te zien. Kopieer alleen het volgende gedeelte als één ononderbroken regel:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Certificaat gegevens" border="false":::
1. Plak de certificaatgegevens in het veld **Gegevens van openbaar certificaat**. Geef het certificaat een **naam** en selecteer vervolgens **Opslaan**. U kunt maximaal 20 vertrouwde basiscertificaten toevoegen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Certificaat gegevens plakken" border="false":::
1. Selecteer **Opslaan** boven aan de pagina om alle configuratie-instellingen op te slaan.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Configuratie opslaan" border="false":::

## <a name="client-certificate"></a><a name="installclientcert"></a>Client certificaat

Als u een P2S-verbinding wilt maken vanaf een andere clientcomputer dan de computer die u gebruikt om de clientcertificaten te genereren, moet u een clientcertificaat installeren. Wanneer u een clientcertificaat installeert, hebt u het wachtwoord nodig dat is gemaakt tijdens het exporteren van het clientcertificaat.

Zorg ervoor dat het certificaat is geëxporteerd als een PFX-bestand, samen met de volledige certificaatketen (dit is de standaardinstelling). Anders zijn de gegevens van het basiscertificaat niet aanwezig op de clientcomputer en kan de client niet worden geverifieerd.

Zie voor de installatiestappen [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Een clientcertificaat installeren).

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Configuratie pakket voor de VPN-client

VPN-clients moeten worden geconfigureerd met client configuratie-instellingen. Het configuratie pakket voor de VPN-client bevat bestanden met de instellingen voor het configureren van VPN-clients om verbinding te maken met een VNet via een P2S-verbinding.

Zie [VPN-client configuratie bestanden maken en installeren voor systeem eigen Azure-certificaat verificatie P2S](point-to-site-vpn-client-configuration-azure-cert.md)voor de stappen voor het genereren en installeren van configuratie bestanden voor VPN-clients.

## <a name="connect-to-azure"></a><a name="connect"></a>Verbinding maken met Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Verbinding maken vanaf een Windows-VPN-client

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Verbinding maken vanaf een Mac-VPN-client

Ga in het dialoog venster netwerk naar het client profiel dat u wilt gebruiken, geef de instellingen op in het [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)en selecteer vervolgens **verbinding maken**.

Controleer de [installatie-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) voor gedetailleerde instructies. Als u problemen ondervindt bij het verbinding maken, controleert u of de gateway van het virtuele netwerk geen basis-SKU gebruikt. Basis-SKU wordt niet ondersteund voor Mac-clients.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="Mac VPN-client verbinding" border="false":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>De verbinding verifiëren

Deze instructies zijn van toepassing op Windows-clients.

1. Als u wilt controleren of uw VPN-verbinding actief is, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u *ipconfig/all* in.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen is een van de adressen binnen het adresbereik van de punt-naar-site-VPN-clientadresgroep die u hebt opgegeven in uw configuratie. De resultaten zijn vergelijkbaar met het volgende voorbeeld:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

Deze instructies zijn van toepassing op Windows-clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Controleer of het configuratiepakket voor de VPN-client is gegenereerd nadat de IP-adressen van de DNS-server zijn opgegeven voor het VNet. Als u de IP-adressen van de DNS-server hebt bijgewerkt, genereert en installeert u een nieuw configuratiepakket voor de VPN-client.

* Gebruik de opdracht 'ipconfig' om het IPv4-adres te controleren dat is toegewezen aan de ethernetadapter op de computer waarmee u de verbinding tot stand brengt. Als het IP-adres zich binnen het adresbereik bevindt van het VNet waarmee u verbinding maakt of binnen het adresbereik van uw VPNClientAddressPool, wordt dit een overlappende adresruimte genoemd. Als uw adresruimte op deze manier overlapt, kan het netwerkverkeer Azure niet bereiken en blijft het in het lokale netwerk.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Vertrouwde basiscertificaten toevoegen of verwijderen

U kunt vertrouwde basiscertificaat toevoegen in en verwijderen uit Azure. Wanneer u een basiscertificaat verwijdert, kunnen clients met een certificaat dat is gegenereerd op basis van dat basiscertificaat niet worden geverifieerd, en kunnen ze dus geen verbinding maken. Als u wilt dat clients kunnen worden geverifieerd en verbinding kunnen maken, moet u een nieuw clientcertificaat installeren dat is gegenereerd op basis van een basiscertificaat dat wordt vertrouwd (is geüpload) in Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat toevoegen

U kunt maximaal 20 vertrouwde .cer-basiscertificaatbestanden toevoegen aan Azure. Zie voor instructies de sectie [Een vertrouwd basiscertificaat uploaden](#uploadfile) in dit artikel.

### <a name="to-remove-a-trusted-root-certificate"></a>Een vertrouwd basiscertificaat verwijderen

1. Als u een vertrouwd basiscertificaat wilt verwijderen, gaat u naar de pagina **Punt-naar-site-configuratie** voor uw virtuele netwerkgateway.
1. Zoek in de sectie **Basiscertificaat** van de pagina het certificaat dat u wilt verwijderen.
1. Selecteer het beletsel teken naast het certificaat en selecteer vervolgens verwijderen.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Een clientcertificaat intrekken

U kunt clientcertificaten intrekken. Met de certificaatintrekkingslijst kunt u selectief punt-naar-site-verbinding weigeren op basis van afzonderlijke clientcertificaten. Dit is anders van het verwijderen van een vertrouwd basiscertificaat. Als u een vertrouwd .cer-basiscertificaat uit Azure verwijdert, wordt de toegang ingetrokken voor alle clientcertificaten die zijn gegenereerd/ondertekend door het ingetrokken basiscertificaat. Als u in plaats van een basiscertificaat een clientcertificaat intrekt, kunt u de certificaten die zijn gegenereerd op basis van het basiscertificaat, blijven gebruiken voor verificatie.

De algemene procedure is het basiscertificaat te gebruiken om de toegang te beheren op het team- of organisatieniveau, terwijl u ingetrokken clientcertificaten gebruikt voor nauwkeuriger toegangsbeheer bij afzonderlijke gebruikers.

### <a name="revoke-a-client-certificate"></a>Een clientcertificaat intrekken

U kunt een clientcertificaat intrekken door de vingerafdruk toe te voegen aan de intrekkingslijst.

1. Haal de vingerafdruk voor het clientcertificaat op. Zie [de vinger afdruk van een certificaat ophalen](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)voor meer informatie.
1. Kopieer de gegevens naar een teksteditor en verwijder alle spaties, zodat u een doorlopende tekenreeks overhoudt.
1. Navigeer naar de pagina **Punt-naar-site-configuratie** van de virtuele netwerkgateway. Dit is de pagina die u ook hebt gebruikt voor het [uploaden van een vertrouwd basiscertificaat](#uploadfile).
1. In het gedeelte **Ingetrokken certificaten** voert u een beschrijvende naam in voor het certificaat (dit hoeft niet de CN van het certificaat te zijn).
1. Kopieer en plak de vingerafdruk naar het veld **Vingerafdruk**.
1. De vingerafdruk wordt gevalideerd en automatisch toegevoegd aan de intrekkingslijst. Er wordt een bericht op het scherm weergegeven met de melding dat de lijst wordt bijgewerkt. 
1. Wanneer het bijwerken is voltooid, kan het certificaat niet langer worden gebruikt om verbinding te maken. Clients die verbinding proberen te maken met het certificaat, ontvangen een bericht waarin wordt gemeld dat het certificaat niet meer geldig is.

## <a name="point-to-site-faq"></a><a name="faq"></a>Veelgestelde vragen over punt-naar-site

Deze sectie bevat informatie over veelgestelde vragen over punt-naar-site-configuraties. U kunt ook de [Veelgestelde vragen over VPN gateway](vpn-gateway-vpn-faq.md) bekijken voor meer informatie over VPN gateway.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).
