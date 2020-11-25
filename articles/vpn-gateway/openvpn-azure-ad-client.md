---
title: 'VPN Gateway: VPN-client voor P2S OpenVPN-protocol verbindingen: Azure AD-verificatie'
description: Informatie over het configureren van een VPN-client om verbinding te maken met een virtueel netwerk met behulp van punt-naar-site-VPN en Azure Active Directory-verificatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 7c2c1930b8f801db7f70baa5b713a641606be644
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019771"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory verificatie: een VPN-client configureren voor P2S OpenVPN-protocol verbindingen

Dit artikel helpt u bij het configureren van een VPN-client om verbinding te maken met een virtueel netwerk met behulp van punt-naar-site-VPN en Azure Active Directory-verificatie. Voordat u verbinding kunt maken met en verifiëren met Azure AD, moet u eerst uw Azure AD-Tenant configureren. Zie [een Azure AD-Tenant configureren](openvpn-azure-ad-tenant.md)voor meer informatie.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>Werken met client profielen

Als u verbinding wilt maken, moet u de Azure VPN-client downloaden en een VPN-client profiel configureren op elke computer die verbinding wil maken met het VNet. U kunt een client profiel maken op een computer, het exporteren en vervolgens importeren naar extra computers.

### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://go.microsoft.com/fwlink/?linkid=2117554) om de Azure VPN-client te downloaden. Controleer of de Azure VPN-client toestemming heeft om op de achtergrond te worden uitgevoerd. Volg de onderstaande stappen om de machtiging te controleren/in te scha kelen:

1. Ga naar Start en selecteer instellingen > privacy > achtergrond-apps.
2. Zorg ervoor dat onder achtergrond-apps **de functie apps op de achtergrond uitvoeren** is ingeschakeld.
3. Onder Kies welke apps op de achtergrond kunnen worden uitgevoerd, schakelt u de instellingen voor de Azure VPN-client in **op aan**.

  ![hebt](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Een client profiel op basis van een certificaat maken

Wanneer u werkt met een op een certificaat gebaseerd profiel, moet u ervoor zorgen dat de juiste certificaten zijn geïnstalleerd op de client computer. Zie [client certificaten installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)voor meer informatie over certificaten.

  ![certificaat](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Een RADIUS-client profiel maken

  ![RADIUS](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Het server geheim kan worden geëxporteerd in het P2S VPN-client profiel.  Instructies voor het exporteren van een client profiel vindt u [hier](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Een client profiel exporteren en distribueren

Wanneer u een werk profiel hebt en dit moet worden gedistribueerd naar andere gebruikers, kunt u het exporteren met behulp van de volgende stappen:

1. Markeer het VPN-client profiel dat u wilt exporteren, selecteer de **...** en selecteer vervolgens **exporteren**.

    ![Scherm opname van de pagina ' Azure VPN-client ', met het weglatings teken geselecteerd en ' export ' gemarkeerd.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecteer de locatie waar u dit profiel wilt opslaan, behoud de bestands naam en selecteer vervolgens **Opslaan** om het XML-bestand op te slaan.

    ![exporteren](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![Scherm afbeelding met de geselecteerde knop toevoegen en de actie importeren gemarkeerd in de linkerbenedenhoek van het venster.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen** terwijl het bestand is geselecteerd.

    ![Scherm opname van het geselecteerde profiel x m l-bestand.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![Scherm afbeelding met de markering ' verbindings naam ' en de knop Opslaan geselecteerd.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![Scherm afbeelding met de geselecteerde VPN-knop en ' verbinding maken '.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![importeren](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een client profiel verwijderen

1. Selecteer de weglatings tekens naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![Scherm afbeelding met de geselecteerde optie voor het weglatings teken en verwijderen.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Een verbinding maken

1. Selecteer op de pagina **+** en vervolgens **+ toevoegen**.

    ![Scherm afbeelding met de geselecteerde knop toevoegen.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Vul de verbindings gegevens in. Als u niet zeker weet wat de waarden zijn, neemt u contact op met de beheerder. Nadat u de waarden hebt ingevuld, selecteert u **Opslaan**.

    ![Scherm opname van de weer gegeven eigenschappen van de VPN-verbinding en de knop Opslaan geselecteerd.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![Scherm afbeelding met de geselecteerde knop verbinding maken.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecteer de juiste referenties en selecteer vervolgens **door gaan**.

    ![Scherm opname van de gemarkeerde voorbeeld referenties en de knop door gaan geselecteerd.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![verbinding](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Automatisch verbinding maken

Met deze stappen kunt u de verbinding configureren om automatisch verbinding te maken met Always.

1. Selecteer op de start pagina voor uw VPN-client de optie **VPN-instellingen**.

    ![Scherm opname van de VPN-start pagina met ' VPN-instellingen ' geselecteerd.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecteer **Ja** in het dialoog venster apps overschakelen.

    ![Scherm opname van ' hebt u bedoeld om te scha kelen tussen apps? ' dialoog venster met de knop Ja geselecteerd.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Controleer of de verbinding die u wilt instellen nog niet is verbonden en selecteer vervolgens het profiel en schakel het selectie vakje **automatisch verbinding maken** in.

    ![Scherm opname van het venster instellingen, waarbij het selectie vakje automatisch verbinding maken is ingeschakeld.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecteer **verbinding maken** om de VPN-verbinding te initiëren.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Problemen met de verbinding vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer de **..** . naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![Scherm afbeelding van het weglatings teken en "diagnose geselecteerd".](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![Scherm opname waarin de pagina eigenschappen van de verbinding wordt weer gegeven en ' diagnose uitvoeren ' is geselecteerd.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![Scherm opname van het dialoog venster ' laten u aangemeld ' met een werk-of school account geselecteerd.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![vaststellen](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Veelgestelde vragen

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Wordt de Azure VPN-client ondersteund met de Windows FIPS-modus?

Ja, met de [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) -hotfix.

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hoe kan ik DNS-achtervoegsels toevoegen aan de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** en de Tags toevoegen

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hoe kan ik aangepaste DNS-servers toevoegen aan de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** en de Tags toevoegen

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> De OpenVPN Azure AD-client maakt gebruik van NRPT-vermeldingen (DNS Name Resolution Policy Table), wat betekent dat de DNS-servers niet worden vermeld onder de uitvoer van `ipconfig /all` . Raadpleeg [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy?preserve-view=true&view=win10-ps) in Power shell om uw DNS-instellingen in gebruik te bevestigen.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hoe kan ik aangepaste routes toevoegen aan de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** en de Tags toevoegen

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Hoe kan ik blok keren (uitsluiten) routes van de VPN-client?

U kunt het gedownloade XML-profiel bestand wijzigen **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** en de Tags toevoegen

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Kan ik het profiel vanuit een opdracht regel prompt importeren?

U kunt het profiel vanuit een opdracht regel prompt importeren door het gedownloade **azurevpnconfig.xml** -bestand in de map **%userprofile%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** te plaatsen en de volgende opdracht uit te voeren:

```
azurevpn -i azurevpnconfig.xml 
```
Gebruik de schakel optie **-f** ook als u wilt afdwingen dat het import proces wordt gebruikt


## <a name="next-steps"></a>Volgende stappen

Zie [een Azure Active Directory-Tenant maken voor P2S open VPN-verbindingen die gebruikmaken van Azure AD-verificatie](openvpn-azure-ad-tenant.md)voor meer informatie.