---
title: 'Azure VPN Gateway: maken van & configuratie bestanden voor VPN-client installeren-P2S RADIUS-verbindingen'
description: Windows-, OS X-en Linux VPN-client configuratie bestanden maken voor verbindingen die gebruikmaken van RADIUS-verificatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91440055"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>VPN-client configuratie bestanden maken en installeren voor P2S RADIUS-verificatie

Als u verbinding wilt maken met een virtueel netwerk via punt-naar-site (P2S), moet u het client apparaat configureren waarmee u verbinding maakt. U kunt P2S VPN-verbindingen maken op Windows-, OS X-en Linux-client apparaten. 

Wanneer u RADIUS-verificatie gebruikt, zijn er meerdere verificatie opties: gebruikers naam/wacht woord verificatie, certificaat verificatie en andere verificatie typen. De configuratie van de VPN-client verschilt voor elk type verificatie. Als u de VPN-client wilt configureren, gebruikt u client configuratie bestanden die de vereiste instellingen bevatten. Dit artikel helpt u bij het maken en installeren van de configuratie van de VPN-client voor het RADIUS-verificatie type dat u wilt gebruiken.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

De configuratie werk stroom voor P2S RADIUS-verificatie is als volgt:

1. [Stel de Azure VPN-gateway in voor P2S-connectiviteit](point-to-site-how-to-radius-ps.md).
2. [Stel uw RADIUS-server in voor verificatie](point-to-site-how-to-radius-ps.md#radius). 
3. **Verkrijg de VPN-client configuratie voor de verificatie optie van uw keuze en gebruik deze om de VPN-client in te stellen** (dit artikel).
4. [Voltooi uw P2S-configuratie en maak verbinding](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Als er wijzigingen zijn aangebracht in de punt-naar-site-VPN-configuratie nadat u het VPN-client configuratie profiel hebt gegenereerd, zoals het type VPN-protocol of het verificatie type, moet u een nieuwe VPN-client configuratie genereren en installeren op de apparaten van uw gebruikers.
>
>

Als u de secties in dit artikel wilt gebruiken, moet u eerst bepalen welk type verificatie u wilt gebruiken: gebruikers naam/wacht woord, certificaat of andere verificatie typen. Elke sectie bevat stappen voor Windows, OS X en Linux (beperkte stappen op dit moment).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Gebruikers naam/wachtwoord verificatie

U kunt gebruikers naam-en wachtwoord verificatie configureren voor gebruik van Active Directory of Active Directory niet gebruiken. Zorg er bij beide scenario's voor dat alle Connect-gebruikers gebruikers naam/wacht woord hebben die kunnen worden geverifieerd via RADIUS.

Wanneer u de verificatie van gebruikers naam/wacht woord configureert, kunt u alleen een configuratie maken voor het verificatie protocol EAP-MSCHAPv2 gebruikers naam en wacht woord. In de opdrachten, `-AuthenticationMethod` is `EapMSChapv2` .

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. configuratie bestanden voor VPN-clients genereren

U kunt de configuratie bestanden voor de VPN-client genereren met behulp van de Azure Portal, of door gebruik te maken van Azure PowerShell.

#### <a name="azure-portal"></a>Azure Portal

1. Navigeer naar de gateway van het virtuele netwerk.
2. Klik op **punt-naar-site-configuratie**.
3. Klik op **VPN-client downloaden**.
4. Selecteer de client en vul alle gevraagde informatie in.
5. Klik op **downloaden** om het zip-bestand te genereren.
6. Het zip-bestand wordt gedownload, meestal naar de map down loads.

#### <a name="azure-powershell"></a>Azure PowerShell

VPN-client configuratie bestanden genereren voor gebruik met gebruikers naam/wacht woord authenticatie. U kunt de configuratie bestanden voor de VPN-client genereren met behulp van de volgende opdracht:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Als u de opdracht uitvoert, wordt een koppeling geretourneerd. Kopieer en plak de koppeling naar een webbrowser om **VpnClientConfiguration.zip** te downloaden. Pak het bestand uit om de volgende mappen weer te geven: 
 
* **WindowsAmd64** en **WindowsX86**: deze mappen bevatten respectievelijk de Windows 64-bits en 32-bits installatie pakketten. 
* **Algemeen**: deze map bevat algemene informatie die u gebruikt om uw eigen VPN-client configuratie te maken. U hebt deze map niet nodig voor configuraties met gebruikers naam-en wachtwoord verificatie.
* **Mac**: als u IKEv2 hebt geconfigureerd tijdens het maken van de gateway van het virtuele netwerk, ziet u een map met de naam **Mac** die een **mobileconfig** -bestand bevat. U kunt dit bestand gebruiken om Mac-clients te configureren.

Als u al client configuratie bestanden hebt gemaakt, kunt u deze ophalen met behulp van de- `Get-AzVpnClientConfiguration` cmdlet. Maar als u wijzigingen aanbrengt in uw P2S VPN-configuratie, zoals het type VPN-protocol of het verificatie type, wordt de configuratie niet automatisch bijgewerkt. U moet de `New-AzVpnClientConfiguration` cmdlet uitvoeren om een nieuwe configuratie-down load te maken.

Als u eerder gegenereerde client configuratie bestanden wilt ophalen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. VPN-clients configureren

U kunt de volgende VPN-clients configureren:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux met strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Setup van Windows VPN-client

U kunt hetzelfde configuratie pakket voor de VPN-client gebruiken op elke Windows-client computer, mits de versie overeenkomt met de architectuur van de client. Raadpleeg de [Veelgestelde vragen](vpn-gateway-vpn-faq.md#P2S)voor de lijst met ondersteunde client besturingssystemen.

Gebruik de volgende stappen om de systeem eigen Windows VPN-client te configureren voor verificatie van certificaten:

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies het **VpnClientSetupAmd64** Installer-pakket voor een 64-bits processor architectuur. Kies het **VpnClientSetupX86** Installer-pakket voor een 32-bits processor architectuur. 
2. Dubbel klik op het pakket om het te installeren. Als er een pop-upvenster van SmartScreen wordt weer gegeven, selecteert u **meer info**  >  **toch uitvoeren**.
3. Blader op de client computer naar **netwerk instellingen** en selecteer **VPN**. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Setup van Mac OS X-VPN-client

1. Selecteer het **VpnClientSetup mobileconfig** -bestand en verzend het naar elk van de gebruikers. U kunt e-mail of een andere methode gebruiken.

2. Zoek het bestand **mobileconfig** op de Mac.

   ![Locatie van het mobileconfig-bestand](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Optionele stap: als u een aangepaste DNS wilt opgeven, voegt u de volgende regels toe aan het **mobileconfig** -bestand:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Dubbel klik op het profiel dat u wilt installeren en selecteer **door gaan**. De profiel naam is hetzelfde als de naam van het virtuele netwerk.

   ![Installatie bericht](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Selecteer **door gaan** om de afzender van het profiel te vertrouwen en door te gaan met de installatie.

   ![Bevestigingsbericht](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Tijdens de installatie van het profiel hebt u de optie om de gebruikers naam en het wacht woord op te geven voor VPN-verificatie. Het is niet verplicht deze gegevens in te voeren. Als u dat wel doet, wordt de informatie opgeslagen en automatisch gebruikt wanneer u een verbinding initieert. Selecteer **installeren** om door te gaan.

   ![Gebruikers naam en wacht woord in de vakken voor VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Voer een gebruikers naam en wacht woord in voor de bevoegdheden die vereist zijn om het profiel op uw computer te installeren. Selecteer **OK**.

   ![Gebruikers naam en wacht woord in de vakken voor profiel installatie](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Nadat het profiel is geïnstalleerd, wordt het weer gegeven in het dialoog venster **profielen** . U kunt dit dialoog venster ook later in **systeem voorkeuren** openen.

   ![Het dialoog venster profielen](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Als u toegang wilt krijgen tot de VPN-verbinding, opent u het dialoog venster **netwerk** in **systeem voorkeuren**.

   ![Pictogrammen in systeem voorkeuren](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. De VPN-verbinding wordt weer gegeven als **IkeV2-VPN**. U kunt de naam wijzigen door het **mobileconfig** -bestand bij te werken.

    ![Details voor de VPN-verbinding](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Selecteer **verificatie-instellingen**. Selecteer in de lijst de optie **gebruikers naam** en voer uw referenties in. Als u eerder de referenties hebt opgegeven, wordt de **gebruikers naam** automatisch gekozen in de lijst en worden de gebruikers naam en het wacht woord vooraf ingevuld. Selecteer **OK** om de instellingen op te slaan.

    ![Scherm opname waarin de vervolg keuzelijst verificatie-instellingen wordt weer gegeven en ' gebruikers naam ' is geselecteerd.](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Klik in het dialoog venster **netwerk** op **Toep assen** om de wijzigingen op te slaan. Selecteer **verbinding maken** om de verbinding te initiëren.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Setup van Linux VPN-client via strongSwan

De volgende instructies zijn gemaakt via strongSwan 5.5.1 op Ubuntu 17.0.4. De werkelijke controles kunnen afwijken, afhankelijk van uw versie van Linux en strongSwan.

1. Open de **Terminal** om **strongswan** en de bijbehorende netwerk beheerder te installeren door de opdracht in het voor beeld uit te voeren. Als er een fout melding wordt weer gegeven die betrekking heeft op `libcharon-extra-plugins` , vervangt u deze door `strongswan-plugin-eap-mschapv2` .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecteer het pictogram **netwerk beheerder** (pijl-omhoog en pijl-omlaag) en selecteer **verbindingen bewerken**.

   ![De selectie ' verbindingen bewerken ' in netwerk beheer](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Selecteer de knop **toevoegen** om een nieuwe verbinding te maken.

   ![Knop toevoegen voor een verbinding](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecteer **IPSec/IKEv2 (strongswan)** in de vervolg keuzelijst en selecteer vervolgens **maken**. In deze stap kunt u de naam van de verbinding wijzigen.

   ![Het verbindings type selecteren](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Open het **VpnSettings.xml** -bestand in de map **Algemeen** van de gedownloade client configuratie bestanden. Zoek het label `VpnServer` en kopieer de naam, beginnend bij `azuregateway` en eindigend met `.cloudapp.net` .

   ![Inhoud van het VpnSettings.xml bestand](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Plak deze naam in het **adres** veld van uw nieuwe VPN-verbinding in de sectie **Gateway** . Selecteer vervolgens het mappictogram aan het einde van het veld **certificaat** , blader naar de **algemene** map en selecteer het **VpnServerRoot** -bestand.
7. Selecteer in de sectie **client** van de verbinding **EAP** voor **verificatie** en voer uw gebruikers naam en wacht woord in. Mogelijk moet u aan de rechter kant het vergrendelings pictogram selecteren om deze informatie op te slaan. Selecteer vervolgens **Opslaan**.

   ![Verbindings instellingen bewerken](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecteer het pictogram **netwerk beheerder** (pijl-omhoog en pijl-omlaag) en beweeg de muis aanwijzer over **VPN-verbindingen**. U ziet de VPN-verbinding die u hebt gemaakt. Als u de verbinding wilt initiëren, selecteert u deze.

   ![VPN RADIUS-verbinding in netwerk beheer](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Verificatie via certificaat
 
U kunt VPN-client configuratie bestanden maken voor RADIUS-certificaat authenticatie die gebruikmaakt van het EAP-TLS-protocol. Normaal gesp roken wordt een door een onderneming uitgegeven certificaat gebruikt voor de verificatie van een gebruiker voor VPN. Zorg ervoor dat alle gebruikers met een verbinding een certificaat op hun apparaten hebben geïnstalleerd en dat de RADIUS-server het certificaat kan valideren.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

In de opdrachten, `-AuthenticationMethod` is `EapTls` . Tijdens de verificatie van het certificaat valideert de client de RADIUS-server door het certificaat te valideren. `-RadiusRootCert` is het CER-bestand dat het basis certificaat bevat dat wordt gebruikt voor het valideren van de RADIUS-server.

Elk VPN-client apparaat vereist een geïnstalleerd client certificaat. Soms heeft een Windows-apparaat meerdere client certificaten. Tijdens de verificatie kan dit resulteren in een pop-upvenster waarin alle certificaten worden weer gegeven. De gebruiker moet vervolgens het certificaat kiezen dat u wilt gebruiken. Het juiste certificaat kan worden uitgefilterd door het basis certificaat op te geven waaraan het client certificaat moet worden gekoppeld. 

`-ClientRootCert` is het CER-bestand dat het basis certificaat bevat. Dit is een optionele para meter. Als het apparaat waarmee u verbinding wilt maken slechts één client certificaat heeft, hoeft u deze para meter niet op te geven.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. configuratie bestanden voor VPN-clients genereren

Genereer VPN-client configuratie bestanden voor gebruik met verificatie via certificaat. U kunt de configuratie bestanden voor de VPN-client genereren met behulp van de volgende opdracht:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Als u de opdracht uitvoert, wordt een koppeling geretourneerd. Kopieer en plak de koppeling naar een webbrowser om VpnClientConfiguration.zip te downloaden. Pak het bestand uit om de volgende mappen weer te geven:

* **WindowsAmd64** en **WindowsX86**: deze mappen bevatten respectievelijk de Windows 64-bits en 32-bits installatie pakketten. 
* **GenericDevice**: deze map bevat algemene informatie die wordt gebruikt voor het maken van uw eigen VPN-client configuratie.

Als u al client configuratie bestanden hebt gemaakt, kunt u deze ophalen met behulp van de- `Get-AzVpnClientConfiguration` cmdlet. Maar als u wijzigingen aanbrengt in uw P2S VPN-configuratie, zoals het type VPN-protocol of het verificatie type, wordt de configuratie niet automatisch bijgewerkt. U moet de `New-AzVpnClientConfiguration` cmdlet uitvoeren om een nieuwe configuratie-down load te maken.

Als u eerder gegenereerde client configuratie bestanden wilt ophalen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. VPN-clients configureren

U kunt de volgende VPN-clients configureren:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (ondersteund, er zijn nog geen artikel stappen)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Setup van Windows VPN-client

1. Selecteer een configuratie pakket en installeer het op het client apparaat. Kies het **VpnClientSetupAmd64** Installer-pakket voor een 64-bits processor architectuur. Kies het **VpnClientSetupX86** Installer-pakket voor een 32-bits processor architectuur. Als er een pop-upvenster van SmartScreen wordt weer gegeven, selecteert u **meer info**  >  **toch uitvoeren**. U kunt het pakket ook opslaan en op andere clientcomputers installeren.
2. Voor elke client is een client certificaat vereist voor verificatie. Installeer het client certificaat. Zie [client certificaten voor punt-naar-site voor](vpn-gateway-certificates-point-to-site.md)meer informatie over client certificaten. Zie [een certificaat installeren op Windows-clients](point-to-site-how-to-vpn-client-install-azure-cert.md)voor meer informatie over het installeren van een certificaat dat is gegenereerd.
3. Blader op de client computer naar **netwerk instellingen** en selecteer **VPN**. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Setup van Mac OS X-VPN-client

U moet een afzonderlijk profiel maken voor elk Mac-apparaat dat verbinding maakt met het virtuele Azure-netwerk. Dit komt doordat voor deze apparaten het gebruikers certificaat voor verificatie moet worden opgegeven in het profiel. De **algemene** map bevat alle informatie die is vereist voor het maken van een profiel:

* **VpnSettings.xml** bevat belang rijke instellingen zoals het server adres en het tunnel type.
* **VpnServerRoot. CER** bevat het basis certificaat dat is vereist voor het valideren van de VPN-gateway tijdens de configuratie van de P2S-verbinding.
* **RadiusServerRoot. CER** bevat het basis certificaat dat is vereist voor het valideren van de RADIUS-server tijdens de verificatie.

Gebruik de volgende stappen om de systeem eigen VPN-client te configureren op een Mac voor verificatie van certificaten:

1. Importeer de basis certificaten **VpnServerRoot** en **RadiusServerRoot** naar uw Mac. Kopieer elk bestand naar uw Mac, dubbel klik erop en selecteer vervolgens **toevoegen**.

   ![Het VpnServerRoot-certificaat toevoegen](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Het RadiusServerRoot-certificaat toevoegen](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Voor elke client is een client certificaat vereist voor verificatie. Installeer het client certificaat op het client apparaat.
3. Open het dialoog venster **netwerk** onder **netwerk voorkeuren**. Selecteer **+** deze optie om een nieuw VPN-client verbindings profiel te maken voor een P2S-verbinding met het virtuele Azure-netwerk.

   De waarde van de **Interface** is **VPN** en de waarde van het **VPN-type** is **IKEv2**. Geef een naam op voor het profiel in het vak **service naam** en selecteer vervolgens **maken** om het VPN-client verbindings profiel te maken.

   ![Informatie over de interface en de service naam](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Kopieer de waarde van de **VpnServer** -code uit het **VpnSettings.xml** -bestand in de **algemene** map. Plak deze waarde in het vak **server adres** en **externe ID** van het profiel. Laat het vak **lokale id** leeg.

   ![Server gegevens](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Selecteer **verificatie-instellingen** en selecteer **certificaat**. 

   ![Verificatie-instellingen](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klik op **selecteren** om het certificaat te kiezen dat u voor verificatie wilt gebruiken.

   ![Een certificaat voor authenticatie selecteren](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Kies een identiteit** om een lijst met certificaten weer te geven waaruit u kunt kiezen. Selecteer het juiste certificaat en selecteer vervolgens **door gaan**.

   ![Lijst ' een identiteit kiezen '](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Geef in het vak **lokale id** de naam van het certificaat op (uit stap 6). In dit voor beeld is het **ikev2Client.com**. Selecteer vervolgens de knop **Toep assen** om de wijzigingen op te slaan.

   ![Vak lokale ID](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Selecteer in het dialoog venster **netwerk** de optie **Toep assen** om alle wijzigingen op te slaan. Selecteer vervolgens **verbinding maken** om de P2S-verbinding met het virtuele Azure-netwerk te starten.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Werken met andere verificatie typen of-protocollen

Als u een ander verificatie type wilt gebruiken (bijvoorbeeld OTP) of als u een ander verificatie protocol wilt gebruiken (zoals PEAP-MSCHAPv2 in plaats van EAP-MSCHAPv2), moet u uw eigen VPN-client configuratie profiel maken. Als u het profiel wilt maken, hebt u informatie nodig, zoals het IP-adres van de gateway van het virtuele netwerk, het tunnel type en routes met Split-tunnels. U kunt deze informatie ophalen met behulp van de volgende stappen:

1. Gebruik de `Get-AzVpnClientConfiguration` cmdlet om de VPN-client configuratie voor EapMSChapv2 te genereren.

2. Pak het VpnClientConfiguration.zip-bestand uit en zoek naar de map **GenericDevice** . Negeer de mappen die de Windows-installatie Programma's bevatten voor 64-bits en 32-bits-architecturen.
 
3. De map **GenericDevice** bevat een XML-bestand met de naam **VpnSettings**. Dit bestand bevat alle vereiste gegevens:

   * **VpnServer**: FQDN van de Azure VPN-gateway. Dit is het adres waarmee de client verbinding maakt.
   * **VpnType**: tunnel type dat u gebruikt om verbinding te maken.
   * **Routes**: routes die u in uw profiel moet configureren zodat alleen verkeer dat afhankelijk is van het virtuele Azure-netwerk, via de P2S-tunnel wordt verzonden.
   
   De map **GenericDevice** bevat ook een CER-bestand met de naam **VpnServerRoot**. Dit bestand bevat het basis certificaat dat is vereist voor het valideren van de Azure VPN-gateway tijdens de installatie van de P2S-verbinding. Installeer het certificaat op alle apparaten die verbinding maken met het virtuele Azure-netwerk.

## <a name="next-steps"></a>Volgende stappen

Ga terug naar het artikel om [uw P2S-configuratie te volt ooien](point-to-site-how-to-radius-ps.md).

Zie [problemen met Azure Point-to-site-verbindingen oplossen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)voor informatie over het oplossen van P2S.
