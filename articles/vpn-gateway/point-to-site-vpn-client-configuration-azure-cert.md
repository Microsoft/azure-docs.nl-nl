---
title: 'P2S-configuratie bestanden voor VPN-client maken & installeren: certificaat verificatie'
titleSuffix: Azure VPN Gateway
description: Windows-, Linux-, Linux-, strongSwan-en macOS X VPN-client configuratie bestanden maken en installeren voor P2S-certificaat authenticatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94556097"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Configuratie bestanden voor de VPN-client maken en installeren voor P2S-configuraties voor systeemeigen Azure-certificaatverificatie

Configuratie bestanden voor VPN-clients zijn opgenomen in een zip-bestand. Configuratie bestanden bieden de instellingen die zijn vereist voor een systeem eigen Windows, Mac IKEv2-VPN of Linux-clients om verbinding te maken met een virtueel netwerk via punt-naar-site-verbindingen die gebruikmaken van systeem eigen Azure-certificaat verificatie.

Client configuratie bestanden zijn specifiek voor de VPN-configuratie voor het virtuele netwerk. Als er wijzigingen zijn aangebracht in de punt-naar-site-VPN-configuratie nadat u de configuratie bestanden voor de VPN-client hebt gegenereerd, zoals het type VPN-protocol of het verificatie type, moet u nieuwe VPN-client configuratie bestanden voor uw gebruikers apparaten genereren.

* Voor meer informatie over point-to-site-verbindingen leest u [About Point-to-Site VPN](point-to-site-about.md) (Over point-to-site-VPN).
* Zie [Configure openvpn for P2S](vpn-gateway-howto-openvpn.md) (Engelstalig) en [Configure openvpn clients](vpn-gateway-howto-openvpn-clients.md)(Engelstalig) voor openvpn instructies.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Configuratie bestanden voor VPN-clients genereren

Voordat u begint, moet u ervoor zorgen dat alle gebruikers met een verbinding een geldig certificaat hebben geïnstalleerd op het apparaat van de gebruiker. Zie [een client certificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor meer informatie over het installeren van een client certificaat.

U kunt client configuratie bestanden genereren met behulp van Power shell of met behulp van de Azure Portal. Beide methoden retour neren hetzelfde zip-bestand. Pak het bestand uit om de volgende mappen weer te geven:

* **WindowsAmd64** en **WindowsX86**, die respectievelijk de Windows 32-bits en 64-bits installatie pakketten bevatten. Het **WindowsAmd64** Installer-pakket is voor alle ondersteunde 64-bits Windows-clients, niet alleen AMD.
* **Algemeen**, dat algemene informatie bevat die wordt gebruikt voor het maken van uw eigen VPN-client configuratie. De algemene map wordt opgegeven als IKEv2 of SSTP + IKEv2 is geconfigureerd op de gateway. Als alleen SSTP is geconfigureerd, is de generieke map niet aanwezig.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Bestanden genereren met behulp van de Azure Portal

1. Navigeer in het Azure Portal naar de virtuele netwerk gateway voor het virtuele netwerk waarmee u verbinding wilt maken.
1. Selecteer op de pagina virtuele netwerk gateway de optie **punt-naar-site-configuratie**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="De VPN-client downloaden":::
1. Selecteer aan de bovenkant van de pagina punt-naar-site-configuratie de optie **VPN-client downloaden**. Het duurt enkele minuten voordat het client configuratie pakket is gegenereerd.
1. Uw browser geeft aan dat een zip-bestand voor de client configuratie beschikbaar is. Deze heeft dezelfde naam als uw gateway. Pak het bestand uit om de mappen weer te geven.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Bestanden genereren met behulp van Power shell

1. Bij het genereren van configuratie bestanden voor VPN-clients is de waarde '-EapTls '. Genereer de configuratie bestanden voor de VPN-client met de volgende opdracht:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Kopieer de URL naar uw browser om het zip-bestand te downloaden en pak het bestand uit om de mappen weer te geven.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 U moet de systeemeigen IKEv2 VPN-client handmatig configureren op elke Mac die verbinding met Azure zal maken. Azure biedt geen mobileconfig-bestand voor verificatie van systeemeigen Azure-certificaten. De **algemene** bevat alle informatie die u nodig hebt voor configuratie. Als u de map Generic niet ziet in uw download, is IKEv2 waarschijnlijk niet geselecteerd als tunneltype. Houd er rekening mee dat de basis-SKU VPN gateway geen ondersteuning biedt voor IKEv2. Genereer het zip-bestand opnieuw om de map Generic te verkrijgen nadat IKEv2 is geselecteerd.<br>De map Generic bevat de volgende bestanden:

* **VpnSettings.xml**, dat belang rijke instellingen bevat, zoals het server adres en het tunnel type. 
* **VpnServerRoot. CER**, dat het basis certificaat bevat dat is vereist voor het valideren van de Azure VPN gateway tijdens de configuratie van de P2S-verbinding.

Voer de volgende stappen uit om de systeem eigen VPN-client te configureren voor verificatie via een certificaat. U moet deze stappen uitvoeren op elke Mac die verbinding maakt met Azure:

1. Importeer het basis certificaat van de **VpnServerRoot** naar uw Mac. U kunt dit doen door het bestand te kopiëren naar uw Mac en erop te dubbel klikken. Selecteer **toevoegen** om te importeren.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="Scherm opname toont de pagina certificaten":::
  
    >[!NOTE]
    >Als u dubbelklikt op het certificaat, wordt het dialoog venster **toevoegen** niet weer gegeven, maar wordt het certificaat in de juiste opslag geïnstalleerd. U kunt het certificaat controleren in de sleutel voor aanmelding bij de categorie certificaten.
    >
  
1. Controleer of u een client certificaat hebt geïnstalleerd dat is uitgegeven door het basis certificaat dat u hebt geüpload naar Azure wanneer u P2S-instellingen hebt geconfigureerd. Dit wijkt af van de VPNServerRoot die u in de vorige stap hebt geïnstalleerd. Het client certificaat wordt gebruikt voor verificatie en is vereist. Raadpleeg [Certificaten genereren](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) voor meer informatie over het genereren van certificaten. Raadpleeg [Een clientcertificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md) voor meer informatie over het installeren van een clientcertificaat.
1. Open het dialoog venster **netwerk** onder **netwerk voorkeuren** en selecteer **+** om een nieuw VPN-client VERBINDINGS profiel te maken voor een P2S-verbinding met het virtuele Azure-netwerk.

   De **Interface** waarde is ' VPN ' en de waarde van het **VPN-type** is ' IKEv2 '. Geef een naam op voor het profiel in het veld **service naam** en selecteer vervolgens **maken** om het VPN-client verbindings profiel te maken.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="Scherm afbeelding toont het netwerk venster met de optie om een interface te selecteren, selecteer VPN-type en voer een service naam in":::
1. Kopieer de waarde van de **VpnServer** -code uit het **VpnSettings.xml** -bestand in de **algemene** map. Plak deze waarde in de velden **server adres** en **externe ID** van het profiel.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="Scherm afbeelding toont de server gegevens.":::
1. Selecteer **verificatie-instellingen** en selecteer **certificaat**. Selecteer voor **Catalina** de optie **geen** en vervolgens **certificaat**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="Scherm opname toont verificatie-instellingen.":::

   Selecteer voor Catalina de optie **geen** en vervolgens **certificaat**. **Selecteer** het juiste certificaat:
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Scherm afbeelding toont het netwerk venster waarvoor geen verificatie-instellingen en certificaat zijn geselecteerd.":::

1. Klik op **selecteren...** om het client certificaat te kiezen dat u voor verificatie wilt gebruiken. Dit is het certificaat dat u in stap 2 hebt geïnstalleerd.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Scherm afbeelding toont het netwerk venster met verificatie-instellingen, waarin u een certificaat kunt selecteren.":::
1. **Kies een identiteit** om een lijst met certificaten weer te geven waaruit u kunt kiezen. Selecteer het juiste certificaat en selecteer vervolgens **door gaan**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="Scherm afbeelding toont het dialoog venster een identiteit kiezen, waarin u het juiste certificaat kunt selecteren.":::

1. Geef in het veld **lokale id** de naam van het certificaat op (uit stap 6). In dit voorbeeld is het `ikev2Client.com`. Selecteer vervolgens **Toep assen** om de wijzigingen op te slaan.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="Scherm opname wordt toegepast.":::
1. Selecteer in het dialoog venster **netwerk** de optie **Toep assen** om alle wijzigingen op te slaan. Selecteer vervolgens **verbinding maken** om de P2S-verbinding met het virtuele Azure-netwerk te starten.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>StrongSwan installeren

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Certificaten genereren

Als u nog geen certificaten hebt gegenereerd, gebruikt u de volgende stappen:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Installeren en configureren

De volgende instructies zijn gemaakt op Ubuntu 18.0.4. Ubuntu 16.0.10 biedt geen ondersteuning voor strongSwan-GUI. Als u Ubuntu 16.0.10 wilt gebruiken, moet u de [opdracht regel](#linuxinstallcli)gebruiken. De onderstaande voor beelden komen mogelijk niet overeen met de schermen die u ziet, afhankelijk van uw versie van Linux en strongSwan.

1. Open de **Terminal** om **strongswan** en de bijbehorende netwerk beheerder te installeren door de opdracht in het voor beeld uit te voeren.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Selecteer **instellingen** en selecteer vervolgens **netwerk**. Selecteer de **+** knop voor het maken van een nieuwe verbinding.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Scherm afbeelding toont de pagina netwerk verbindingen.":::

1. Selecteer **IPSec/IKEv2 (strongswan)** in het menu en dubbel klik op.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Scherm afbeelding toont de pagina VPN toevoegen.":::

1. Voeg op de pagina **VPN toevoegen** een naam toe voor uw VPN-verbinding.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Scherm afbeelding toont een verbindings type.":::
1. Open het **VpnSettings.xml** -bestand in de **algemene** map die zich in de gedownloade client configuratie bestanden bevindt. Zoek het label **VpnServer** en kopieer de naam, te beginnen met ' azuregateway ' en eindigt met '. cloudapp.net '.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="De scherm opname bevat gegevens kopiëren.":::
1. Plak de naam in het veld **adres** van de nieuwe VPN-verbinding in de sectie **Gateway** . Selecteer vervolgens het mappictogram aan het einde van het veld **certificaat** , blader naar de **algemene** map en selecteer het **VpnServerRoot** -bestand.
1. Selecteer in het gedeelte **client** van de verbinding voor **verificatie** de optie **certificaat/persoonlijke sleutel**. Voor het **certificaat** en de **persoonlijke sleutel** kiest u het certificaat en de persoonlijke sleutel die u eerder hebt gemaakt. Selecteer bij **Opties** **een intern IP-adres aanvragen**. Selecteer vervolgens **toevoegen**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Scherm afbeelding toont een intern IP-adres.":::

1. Zet de verbinding **aan**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Scherm afbeelding toont een kopie.":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>StrongSwan installeren

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Certificaten genereren

Als u nog geen certificaten hebt gegenereerd, gebruikt u de volgende stappen:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installeren en configureren

1. Down load het VPNClient-pakket van Azure Portal.
1. Pak het bestand uit.
1. Kopieer of verplaats de **VpnServerRoot. CER** uit de **algemene** map naar **/etc/IPSec.d/cacerts**.
1. Kopieer of verplaats **CP client. p12** naar **/etc/IPSec.d/private/**. Dit bestand is het client certificaat voor de VPN-gateway.
1. Open het **VpnSettings.xml** -bestand en kopieer de `<VpnServer>` waarde. In de volgende stap gaat u deze waarde gebruiken.
1. Wijzig de waarden in het onderstaande voor beeld en voeg het voor beeld toe aan de **/etc/IPSec.conf** -configuratie.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Voeg de volgende waarden toe aan **/etc/IPSec.Secrets**.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Voer de volgende opdrachten uit:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Volgende stappen

Ga terug naar het oorspronkelijke artikel waarvan u bezig was en voltooi de configuratie van uw P2S.

* [Configuratie stappen voor Power shell](vpn-gateway-howto-point-to-site-rm-ps.md).
* [Configuratie stappen Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
