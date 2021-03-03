---
title: Hybride Azure Active Directory-gekoppelde apparaten handmatig configureren | Microsoft Docs
description: Leer hoe u hybride Azure Active Directory-gekoppelde apparaten handmatig kunt configureren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f346b997b5e0c785d066ce3a1edaab8cbea10212
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644116"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Zelfstudie: Hybride Azure Active Directory-gekoppelde apparaten handmatig configureren

Met apparaatbeheer in Azure Active Directory (Azure AD) kunt u ervoor zorgen dat gebruikers toegang tot uw resources hebben vanaf apparaten die voldoen aan uw normen voor beveiliging en naleving. Zie [Wat is apparaatbeheer in Azure Active Directory?](overview.md) voor meer informatie.

> [!TIP]
> Als het gebruik van Azure Active Directory Connect een optie voor u is, raadpleegt u de gerelateerde zelfstudies voor [beheerde](hybrid-azuread-join-managed-domains.md) of [federatieve](hybrid-azuread-join-federated-domains.md) domeinen. Met behulp van Azure Active Directory Connect kunt u de configuratie van hybride Azure AD-koppeling aanzienlijk vereenvoudigen.

Als u een on-premises Active Directory-omgeving hebt en u uw domein-gekoppelde apparaten aan Azure AD wilt koppelen, kunt u dit doen door hybride Azure AD-gekoppelde apparaten te configureren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Hybride Azure AD-deelname handmatig configureren
> * Een serviceverbindingspunt configureren
> * Claimuitgifte instellen
> * Downlevel Windows-apparaten inschakelen
> * Gekoppelde apparaten verifiëren
> * Problemen met uw implementatie oplossen

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u bekend bent met:

* [Inleiding tot apparaatbeheer in Azure Active Directory](./overview.md)
* [De implementatie van uw hybride Azure Active Directory-deelname plannen](hybrid-azuread-join-plan.md)
* [De hybride Azure AD-deelname van uw apparaten beheren](hybrid-azuread-join-control.md)

Voordat u hybride Azure AD-gekoppelde apparaten gaat inschakelen in uw organisatie, moet u ervoor zorgen dat:

* U een up-to-date versie van Azure AD Connect uitvoert.
* Azure AD Connect de computerobjecten heeft gesynchroniseerd van de apparaten die u hybride Azure AD-gekoppeld wilt maken. Als de computerobjecten bij specifieke organisatie-eenheden (OE) horen, moeten deze OE’s worden geconfigureerd voor synchronisatie in Azure AD Connect.

Azure AD Connect:

* Behoudt de koppeling tussen het computeraccount in uw on-premises Active Directory-instantie (AD) en het apparaatobject in Azure AD.
* Schakelt andere apparaatgerelateerde functies in, zoals Windows Hello voor Bedrijven.

Zorg ervoor dat de volgende URL’s toegankelijk zijn vanaf computers in uw organisatienetwerk om computers te kunnen registreren bij Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* De STS van uw organisatie (voor federatieve domeinen), moet worden opgenomen in de instellingen van het lokale intranet van de gebruiker

> [!WARNING]
> Als uw organisatie proxyservers gebruikt die SSL-verkeer onderscheppen voor scenario's zoals preventie van gegevensverlies of beperkingen voor Azure AD-tenants, moet u ervoor zorgen dat verkeer naar 'https://device.login.microsoftonline.com ' wordt uitgesloten van TLS break-and-inspect. Als u 'https://device.login.microsoftonline.com ' niet uitsluit, kan dit problemen geven met de verificatie van clientcertificaten, die op hun beurt weer problemen veroorzaken met apparaatregistratie en voorwaardelijke toegang op basis van apparaten.

Als uw organisatie gebruik wil gaan maken van naadloze eenmalige aanmelding, moet de volgende URL bereikbaar zijn vanaf de computers binnen uw organisatie. De URL moet ook worden toegevoegd aan de lokale intranetzone van de gebruiker.

* `https://autologon.microsoftazuread-sso.com`

Bovendien moet de volgende instelling zijn ingeschakeld in de zone Lokaal intranet van de gebruiker: ‘Statusbalkupdates via scripts toestaan’.

Als uw organisatie beheerde (niet-federatieve) installatie met on-premises Active Directory gebruikt en niet Active Directory Federation Services (ADFS) gebruikt om te federeren met Azure AD, is hybride Azure AD-koppeling in Windows 10 afhankelijk van de synchronisatie van computerobjecten in Active Directory met Azure AD. Zorg ervoor dat organisatie-eenheden die de computerobjecten bevatten die hybride Azure AD-gekoppeld moeten worden, zijn ingeschakeld voor synchronisatie in de synchronisatieconfiguratie van Azure AD Connect.

Voor Windows 10-apparaten met versie 1703 of eerder: als uw organisatie toegang tot internet via een uitgaande proxy vereist, moet u WPAD (Web Proxy Auto-Discovery) implementeren om Windows 10-computers in staat te stellen apparaten te registreren bij Azure AD.

Vanaf Windows 10 1803 probeert een apparaat het koppelen van de hybride Azure AD te voltooien via de gesynchroniseerde computer of het gesynchroniseerde apparaat, zelfs als het via AD FS koppelen van de hybride Azure AD wordt uitgevoerd vanaf een apparaat in een federatief domein mislukt en Azure AD Connect is geconfigureerd om de computer-/apparaatobjecten te synchroniseren met Azure AD.

Om te controleren of het apparaat toegang heeft tot de bovenstaande Microsoft-resources onder het systeemaccount, kunt u het script [Test Device Registration Connectivity](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) gebruiken.

## <a name="verify-configuration-steps"></a>Configuratiestappen controleren

U kunt hybride Azure AD-gekoppelde apparaten configureren voor verschillende soorten Windows-apparaatplatforms. Dit onderwerp biedt de vereiste stappen voor alle gebruikelijke configuratiescenario’s.  

In de volgende tabel staat een overzicht van de stappen die vereist zijn voor uw scenario:  

| Stappen | Actueel Windows en wachtwoord-hashsynchronisatie | Actueel Windows en federatie | Downlevel Windows |
| :--- | :---: | :---: | :---: |
| Serviceverbindingspunt configureren | ![Selecteren][1] | ![Selecteren][1] | ![Selecteren][1] |
| Claimuitgifte instellen |     | ![Selecteren][1] | ![Selecteren][1] |
| Niet-Windows 10-apparaten inschakelen |       |        | ![Selecteren][1] |
| Gekoppelde apparaten verifiëren | ![Selecteren][1] | ![Selecteren][1] | ![Selecteren][1] |

## <a name="configure-a-service-connection-point"></a>Een serviceverbindingspunt configureren

Uw apparaten gebruiken een SCP-object (serviceverbindingspunt) tijdens de registratie om Azure AD-tenantgegevens te ontdekken. In uw on-premises instantie van Active Directory (AD) moet het SCP-object voor de hybride Azure AD-gekoppelde apparaten bestaan in de partitie voor de configuratienaamgevingscontext van de forest van de computer. Er is slechts één configuratienaamgevingscontext per forest. In een Active Directory-configuratie met meerdere forests moet het serviceverbindingspunt bestaan in alle forests die domein-gekoppelde computers bevatten.

U kunt de cmdlet [**Get-ADRootDSE**](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee617246(v=technet.10)) gebruiken om de configuratienaamgevingscontext van uw forest op te halen.  

Voor een forest met de Active Directory-domeinnaam *fabrikam.com* is de configuratienaamgevingscontext:

`CN=Configuration,DC=fabrikam,DC=com`

In uw forest bevindt het SCP-object voor de automatische registratie van domein-gekoppelde apparaten zich op:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Afhankelijk van hoe u Azure AD Connect hebt geïmplementeerd, is het SCP-object mogelijk al geconfigureerd.
U kunt het bestaan van het object verifiëren en de detectiewaarden ophalen met behulp van het volgende Windows PowerShell-script:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

De uitvoer **$scp.Keywords** toont de Azure AD-tenantgegevens. Hier volgt een voorbeeld:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Als het serviceverbindingspunt niet bestaat, kunt u dit maken door de cmdlet `Initialize-ADSyncDomainJoinedComputerSync` uit te voeren op uw Azure AD Connect-server. Om deze cmdlet te kunnen uitvoeren, zijn referenties van een ondernemingsbeheerder nodig.  

De cmdlet:

* Maakt het serviceverbindingspunt in de Active Directory-forest waarmee Azure AD Connect verbonden is.
* Vereist dat u de parameter `AdConnectorAccount` opgeeft. Dit account is in Azure AD Connect als het account voor de Active Directory-connector geconfigureerd.


Het volgende script toont een voorbeeld van hoe de cmdlet kan worden gebruikt. In dit script vereist `$aadAdminCred = Get-Credential` dat u een gebruikersnaam typt. U moet de gebruikersnaam in de UPN-indeling (User Principal Name) opgeven (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

De cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

* Maakt gebruik van de Active Directory PowerShell-module en Azure AD DS-hulpprogramma's (Azure Active Directory Domain Services). Deze hulpprogramma's maken op hun beurt gebruik van Active Directory Web Services dat wordt uitgevoerd op een domeincontroller. Active Directory Web Services wordt ondersteund op domeincontrollers waarop Windows Server 2008 R2 of hoger wordt uitgevoerd.
* Wordt alleen ondersteund door MSOnline PowerShell-module versie 1.1.166.0. Gebruik deze [koppeling](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0) om deze module te downloaden.
* Als de AD DS-hulpprogramma's niet zijn geïnstalleerd, mislukt `Initialize-ADSyncDomainJoinedComputerSync`. U kunt de AD DS-hulpprogramma’s installeren via Serverbeheer onder **Functies** > **Remote Server Administration Tools** > **Hulpprogramma's voor functiebeheer**.

Voor domeincontrollers waarop Windows Server 2008 of een eerdere versie wordt uitgevoerd, gebruikt u het volgende script om het serviceverbindingspunt te maken. In een configuratie met meerdere forests gebruikt u het volgende script om het serviceverbindingspunt te maken in elke forest waarin computers bestaan:

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

In het voorgaande script is `$verifiedDomain = "contoso.com"` een tijdelijke aanduiding. Vervang deze door een van uw geverifieerde domeinnamen in Azure AD. U moet eigenaar van het domein zijn om dit te kunnen gebruiken.

Zie [Een aangepaste domeinnaam toevoegen aan Azure Active Directory](../fundamentals/add-custom-domain.md) voor meer informatie over geverifieerde domeinnamen.

Om een lijst met uw geverifieerde bedrijfsdomeinen op te halen, kunt u de cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain) gebruiken.

![Lijst met domeinen van bedrijf](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Claimuitgifte instellen

In een gefedereerde Azure AD-configuratie zijn apparaten afhankelijk van AD FS of een on-premises federatieve service van een Microsoft-partner om te verifiëren bij Azure AD. Apparaten verifiëren om een toegangstoken te verkrijgen voor registratie bij de Azure Active Directory Device Registration Service (Azure DRS).

Actuele Windows-apparaten verifiëren met behulp van Geïntegreerde Windows-verificatie bij een actief WS-Trust-eindpunt (versie 1.3 of 2005) dat wordt gehost door de on-premises federatieve service.

Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eindpunten inschakelen
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Zowel **adfs/services/trust/2005/windowstransport** als **adfs/services/trust/13/windowstransport** dienen alleen te worden ingeschakeld als naar intranet gerichte eindpunten en mogen NIET beschikbaar worden gemaakt als naar extranet gerichte eindpunten via de webtoepassingsproxy. Zie voor meer informatie over het uitschakelen van WS-Trust Windows eindpunten [Disable WS-Trust Windows endpoints on the proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Onder **Service** > **Eindpunten** in de AD FS-beheerconsole kunt u zien welke eindpunten zijn ingeschakeld.

> [!NOTE]
>Als u AD FS niet als uw on-premises federatieve service hebt, volgt u de instructies van uw leverancier om te verzekeren dat ze WS-Trust 1.3- of WS-Trust 2005-eindpunten ondersteunen en dat deze via het MEX-bestand (Metadata Exchange) worden gepubliceerd.

Om de apparaatregistratie te kunnen voltooien, moeten de volgende claims bestaan in het token dat door Azure DRS wordt ontvangen. Azure DRS maakt aan de hand van enkele van deze gegevens een apparaatobject in Azure AD. Azure AD Connect gebruikt deze gegevens vervolgens om het zojuist gemaakte apparaatobject on-premises te koppelen aan het computeraccount.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Als u meer dan één geverifieerde domeinnaam hebt, moet u de volgende claim voor computers opgeven:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Als u al een ImmutableID-claim (bijvoorbeeld met `mS-DS-ConsistencyGuid` of een ander attribuut als de bronwaarde voor ImmutableID) uitgeeft, moet u één overeenkomende claim voor computers opgeven:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

In de volgende secties vindt u informatie over:

* De waarden die elke claim moet hebben.
* Hoe een definitie eruit zou zien in AD FS.

De definitie helpt u te verifiëren dat de waarden aanwezig zijn of dat u ze moet maken.

> [!NOTE]
> Als u AD FS niet gebruikt voor uw on-premises federatieve service, volgt u de instructies van uw leverancier om de toepasselijke configuratie te maken voor het uitgeven van deze claims.

### <a name="issue-account-type-claim"></a>Accounttypeclaim uitgeven

De claim `http://schemas.microsoft.com/ws/2012/01/accounttype` moet de waarde **DJ** bevatten, die het apparaat identificeert als een domein-gekoppelde computer. In AD FS kunt u een uitgiftetransformatieregel toevoegen die er als volgt uitziet:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>objectGUID van het on-premises computeraccount uitgeven

De claim `http://schemas.microsoft.com/identity/claims/onpremobjectguid` moet de **objectGUID**-waarde van het on-premises computeraccount bevatten. In AD FS kunt u een uitgiftetransformatieregel toevoegen die er als volgt uitziet:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>objectSID van het on-premises computeraccount uitgeven

De claim `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` moet de **objectSid**-waarde van het on-premises computeraccount bevatten. In AD FS kunt u een uitgiftetransformatieregel toevoegen die er als volgt uitziet:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>issuerID voor de computer uitgeven bij meerdere geverifieerde domeinnamen in Azure AD

De claim `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` moet de URI (Uniform Resource Identifier) bevatten van een van de geverifieerde domeinnamen die verbinding maken met de on-premises federatieve service (AD FS of partner) die het token uitgeeft. In AD FS kunt u uitgiftetransformatieregels toevoegen die eruitzien als hieronder, in die specifieke volgorde, na de bovenstaande regels. Er is één regel nodig om de regel expliciet voor gebruikers uit te geven. In de volgende regels wordt een eerste regel toegevoegd waarmee verificatie van gebruikers ten opzichte van computers wordt geïdentificeerd.

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

In de voorgaande claim is `<verified-domain-name>` een tijdelijke aanduiding. Vervang deze door een van uw geverifieerde domeinnamen in Azure AD. Gebruik bijvoorbeeld `Value = "http://contoso.com/adfs/services/trust/"`.

Zie [Een aangepaste domeinnaam toevoegen aan Azure Active Directory](../fundamentals/add-custom-domain.md) voor meer informatie over geverifieerde domeinnamen.  

Om een lijst met uw geverifieerde bedrijfsdomeinen op te halen, kunt u de cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain) gebruiken.

![Lijst met domeinen van bedrijf](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>ImmutableID uitgeven voor de computer wanneer er een voor gebruikers bestaat (bijvoorbeeld met mS-DS-ConsistencyGuid als bron voor ImmutableID)

De claim `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` moet een geldige waarde bevatten voor computers. In AD FS kunt u als volgt een uitgiftetransformatieregel maken:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Hulpscript om de AD FS-uitgiftetransformatieregels te maken

Het volgende script helpt u bij het maken van de eerder beschreven uitgiftetransformatieregels.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Opmerkingen

* Dit script voegt de regels aan de bestaande regels toe. Voer het script niet tweemaal uit, anders wordt de verzameling regels tweemaal toegevoegd. Zorg dat er geen overeenkomende regels voor deze claims bestaan (onder de overeenkomende voorwaarden) voordat u het script nogmaals uitvoert.
* Als u meerdere geverifieerde domeinnamen hebt (zoals wordt weergegeven in de Azure AD-portal of via de cmdlet **Get-MsolDomain**), stelt u **$multipleVerifiedDomainNames** in het script in op **$true**. Zorg ook dat u een bestaande **issuerID**-claim verwijdert die mogelijk door Azure AD Connect of via een andere methode is gemaakt. Hier is een voorbeeld voor deze regel:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Als u al een **ImmutableID**-claim voor gebruikersaccounts hebt uitgegeven, stelt u **$immutableIDAlreadyIssuedforUsers** in het script op de waarde **$true** in.

## <a name="enable-windows-down-level-devices"></a>Downlevel Windows-apparaten inschakelen

Als sommige van uw domein-gekoppelde apparaten downlevel Windows-apparaten zijn, moet u:

* Een beleid in Azure AD instellen om gebruikers in staat te stellen apparaten te registreren.
* Uw on-premises federatieve service configureren voor claimuitgifte om Geïntegreerde Windows-verificatie (IWA) te ondersteunen voor apparaatregistratie.
* Het eindpunt voor Azure AD-apparaatverificatie toevoegen aan de zone Lokaal intranet om certificaatprompts te vermijden wanneer het apparaat wordt geverifieerd.
* Downlevel Windows-apparaten beheren.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Een beleid in Azure AD instellen om gebruikers in staat te stellen apparaten te registreren

Om downlevel Windows-apparaten te registreren, moet u ervoor zorgen dat de instelling is ingeschakeld die gebruikers in staat stelt apparaten te registreren in Azure AD. In de Azure-portal vindt u deze instelling onder **Azure Active Directory** > **Gebruikers en groepen** > **Apparaatinstellingen**.

Het volgende beleid moet zijn ingesteld op **Alle**: **Gebruikers mogen hun apparaten met Azure AD registreren**

![De knop Alles waarmee gebruikers apparaten kunnen registreren](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>De on-premises federatieve service configureren

Uw on-premises federatieve service moet de uitgifte van de claims **authenticationmethod** en **wiaormultiauthn** ondersteunen wanneer een verificatieaanvraag wordt ontvangen bij de Azure AD Relying Party, die een resource_params-parameter met de volgende gecodeerde waarde bevat:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Wanneer een dergelijke aanvraagt wordt ontvangen, moet de on-premises federatieve service de gebruiker verifiëren met behulp van Geïntegreerde Windows-verificatie: Als de verificatie is gelukt, moet de service de volgende twee claims uitgeven:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

In AD FS moet u een uitgiftetransformatieregel toevoegen die wordt doorgegeven via de verificatiemethode. Ga als volgt te werk om deze regel toe te voegen:

1. Ga in de AD FS-beheerconsole naar **AD FS** > **Vertrouwensrelaties** > **Relying Party-vertrouwensrelaties**.
1. Klik met de rechtermuisknop op het object Relying Party-vertrouwensrelatie van het identiteitsplatform van Microsoft Office 365 en selecteer vervolgens **Claimregels bewerken**.
1. Selecteer op het tabblad **Uitgiftetransformatieregels** de optie **Regel toevoegen**.
1. Selecteer in de sjabloonlijst **Claimregel** de optie **Claim verzenden met een aangepaste regel**.
1. Selecteer **Next**.
1. Typ **Auth Method Claim Rule** in het vak **Naam claimregel**.
1. Voer in het vak **Claimregel** de volgende regel in:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Voer de volgende PowerShell-opdracht uit op de federatieserver. Vervang **\<RPObjectName\>** door de naam van het Relying Party-object voor het object Relying Party-vertrouwensrelatie van Azure AD. Dit object heet meestal **Identiteitsplatform van Microsoft Office 365**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Het Azure AD-apparaatverificatie-eindpunt toevoegen aan de zone Lokaal intranet

Om certificaatprompts te vermijden wanneer gebruikers van geregistreerde apparaten zich willen verifiëren bij Azure AD, kunt u een beleid naar uw domein-gekoppelde apparaten pushen om de volgende URL toe te voegen aan de zone Lokaal intranet in Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Downlevel Windows-apparaten beheren

Als u downlevel Windows-apparaten wilt registreren, dient u een Windows Installer-pakket (.msi) in het Downloadcentrum te downloaden en installeren. Zie voor meer informatie de sectie [Gecontroleerde validatie van hybride Azure AD-deelname op Windows-apparaten op het lagere niveau](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Gekoppelde apparaten verifiëren

Hier volgen drie manieren om de status van het apparaat te zoeken en te controleren:

### <a name="locally-on-the-device"></a>Lokaal op het apparaat

1. Open Windows PowerShell.
2. Voer `dsregcmd /status` in.
3. Controleer of zowel **AzureAdJoined** als **DomainJoined** zijn ingesteld op **JA**.
4. U kunt de **DeviceId** gebruiken en de status van de service vergelijken met behulp van de Azure-portal of PowerShell.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

1. Ga naar de apparatenpagina met behulp van een [directe link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informatie over het vinden van een apparaat vindt u in [How to manage device identities using the Azure portal](./device-management-azure-portal.md#manage-devices) (Apparaat-id's beheren met de Azure-portal).
3. Als er **In behandeling** in de kolom **Geregistreerd** staat, is de hybride Azure AD-koppeling niet voltooid. In federatieve omgevingen kan dit alleen gebeuren als het registreren niet is gelukt en AAD Connect is geconfigureerd voor het synchroniseren van de apparaten.
4. Als er een **datum/tijd** in de kolom **Geregistreerd** staat, is de hybride Azure AD-koppeling wel voltooid.

### <a name="using-powershell"></a>PowerShell gebruiken

Controleer de registratiestatus van apparaten in uw Azure-tenant met behulp van **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** . Deze cmdlet bevindt zich in de [Azure Active Directory PowerShell-module](/powershell/azure/active-directory/install-msonlinev1).

Wanneer u de cmdlet **Get-MSolDevice** gebruikt om de servicedetails te controleren:

- Moet er een object bestaan met de **apparaat-id** die overeenkomt met de id op de Windows-client.
- Is de waarde voor **DeviceTrustType** **Toegevoegd aan domein**. Deze instelling is equivalent aan de status **Toegevoegd aan hybride Azure AD** op de pagina **Apparaten** in de Azure AD-portal.
- Voor apparaten die met Voorwaardelijke toegang worden gebruikt, is de waarde voor **Ingeschakeld** **Waar** en voor **DeviceTrustLevel** **Beheerd**.

1. Open Windows PowerShell als beheerder.
2. Voer `Connect-MsolService` in om verbinding te maken met uw Azure-tenant.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Tel alle hybride Azure AD-gekoppelde apparaten (met uitzondering van die met de status **In behandeling** )

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Tel alle hybride Azure AD-gekoppelde apparaten met de status **In behandeling**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Maak een lijst van alle hybride Azure AD-gekoppelde apparaten

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Maak een lijst van alle hybride Azure AD-gekoppelde apparaten met de status **In behandeling**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>De details van één apparaat weergeven:

1. Voer `get-msoldevice -deviceId <deviceId>` in (dit is de **DeviceId** die lokaal van het apparaat wordt opgehaald).
2. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Als er problemen zijn met het voltooien van de hybride Azure AD-koppeling voor domein-gekoppelde Windows-apparaten, raadpleegt u:

- [Problemen met apparaten oplossen met behulp van de dsregcmd-opdracht](./troubleshoot-device-dsregcmd.md)
- [Problemen met hybride Azure Active Directory-gekoppelde apparaten oplossen](troubleshoot-hybrid-join-windows-current.md)
- [Problemen oplossen met hybride Azure Active Directory-gekoppelde, downlevel apparaten](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot apparaatbeheer in Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png