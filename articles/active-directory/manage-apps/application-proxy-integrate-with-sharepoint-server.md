---
title: Externe toegang inschakelen voor share point-Azure AD-toepassingsproxy
description: Behandelt de basis principes voor het integreren van een on-premises share Point-server met Azure AD-toepassingsproxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c318c539b1c09761ed81e7602808e415fdaf8b80
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658176"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Externe toegang tot SharePoint inschakelen met Azure AD-toepassingsproxy

In deze stapsgewijze hand leiding wordt uitgelegd hoe u een on-premises share point-Farm integreert met Azure Active Directory (Azure AD)-toepassings proxy.

## <a name="prerequisites"></a>Vereisten

Als u de configuratie wilt uitvoeren, hebt u de volgende resources nodig:
- Een SharePoint 2013-farm of nieuwer.
- Een Azure AD-Tenant met een abonnement dat toepassings proxy bevat. Meer informatie over [Azure AD-abonnementen en-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).
- Een [aangepast, geverifieerd domein](../fundamentals/add-custom-domain.md) in de Azure AD-Tenant.
- On-premises Active Directory gesynchroniseerd met Azure AD Connect, waarmee gebruikers [zich kunnen aanmelden bij Azure](../hybrid/plan-connect-user-signin.md).
- Een toepassings proxy connector is geïnstalleerd en wordt uitgevoerd op een computer binnen het bedrijfs domein.

Voor het configureren van share point met Application proxy zijn twee Url's vereist:
- Een externe URL, zichtbaar voor eind gebruikers en bepaald in azure AD. Deze URL kan gebruikmaken van een aangepast domein. Meer informatie over het [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md).
- Een interne URL, alleen bekend in het bedrijfs domein en nooit rechtstreeks gebruikt.

> [!IMPORTANT]
> Volg deze aanbevelingen voor de interne URL om ervoor te zorgen dat de koppelingen correct worden toegewezen:
> - HTTPS gebruiken.
> - Gebruik geen aangepaste poorten.
> - Maak in de corporate Domain Name System (DNS) een host (A) die verwijst naar de share point WFE (of load balancer) en niet een alias (CName).

In dit artikel worden de volgende waarden gebruikt:
- Interne URL: `https://sharepoint`
- Externe URL: `https://spsites-demo1984.msappproxy.net/`
- Account voor de groep van toepassingen voor de share point-webtoepassing: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Stap 1: een toepassing configureren in azure AD die gebruikmaakt van toepassings proxy

In deze stap maakt u een toepassing in uw Azure Active Directory-Tenant die gebruikmaakt van de toepassings proxy. U stelt de externe URL in en geeft de interne URL op die u later in share point gebruikt.

1. Maak de app, zoals wordt beschreven met de volgende instellingen. Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor stapsgewijze instructies.
   * **Interne URL**: interne URL van share point die later in share point wordt ingesteld, zoals `https://sharepoint` .
   * **Verificatie vooraf**: Azure Active Directory
   * **Url's vertalen in kopteksten**: Nee
   * **Url's vertalen in de hoofd tekst van de toepassing**: Nee

   ![Share point als toepassing publiceren](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Nadat de app is gepubliceerd, voert u de volgende stappen uit om de instellingen voor eenmalige aanmelding te configureren:

   1. Selecteer op de toepassingspagina in de Portal de optie **Eenmalige aanmelding**.
   1. Selecteer bij **Modus Eenmalige aanmelding** de optie **Geïntegreerde Windows-verificatie**.
   1. Stel **interne Application SPN** in op de waarde die u eerder hebt ingesteld. Voor dit voor beeld is de waarde `HTTP/sharepoint` .
   1. Selecteer onder **gedelegeerde aanmeldings-id** de meest geschikte optie voor uw Active Directory forest-configuratie. Als u bijvoorbeeld één Active Directory domein in uw forest hebt, selecteert u de **naam van het on-premises SAM-account** (zoals weer gegeven in de volgende scherm afbeelding). Maar als uw gebruikers zich niet in hetzelfde domein bevinden als share point en de Application proxy-connector servers, selecteert u **on-premises User Principal name** (niet weer gegeven in de scherm afbeelding).

   ![Geïntegreerde Windows-verificatie voor eenmalige aanmelding configureren](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. U voltooit het instellen van uw toepassing door naar de sectie **Gebruikers en groepen** te gaan en gebruikers toe te wijzen die toegang krijgen tot deze toepassing. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Stap 2: de share point-webtoepassing configureren

De share point-webtoepassing moet worden geconfigureerd met Kerberos en de juiste alternatieve toegangs toewijzingen voor een juiste werking met Azure AD-toepassingsproxy. Er zijn twee mogelijke opties:

- Een nieuwe webtoepassing maken en alleen de standaard zone gebruiken. Dit is de voorkeurs optie, omdat deze de beste ervaring biedt met share point (bijvoorbeeld de koppelingen in de e-mail waarschuwingen die door share point worden gegenereerd, verwijzen altijd naar de standaard zone).
- Een bestaande webtoepassing uitbreiden om Kerberos te configureren in een niet-standaard zone.

> [!IMPORTANT]
> Ongeacht de zone die wordt gebruikt, moet het account van de toepassings groep van de share point-webtoepassing een domein account zijn voor een juiste werking van Kerberos.

### <a name="provision-the-sharepoint-web-application"></a>De share point-webtoepassing inrichten

- Als u een nieuwe webtoepassing maakt en alleen de standaard zone gebruikt (voorkeurs optie):

    1. Start de **share point-beheer shell** en voer het volgende script uit:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Open de site **Centraal beheer van share point** .
    1. Selecteer **alternatieve toegangs toewijzingen configureren** onder **systeem instellingen**. Het dialoog venster **verzameling alternatieve toegangs toewijzingen** wordt geopend.
    1. Filter de weer gave met de nieuwe webtoepassing en bevestig dat u er ongeveer als volgt uitziet:

       ![Alternatieve toegangs toewijzingen van webtoepassing](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Als u een bestaande webtoepassing uitbreidt naar een nieuwe zone (voor het geval u de standaard zone niet kunt gebruiken):

    1. Start de share point-beheer shell en voer het volgende script uit:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Open de site **Centraal beheer van share point** .
    1. Selecteer **alternatieve toegangs toewijzingen configureren** onder **systeem instellingen**. Het dialoog venster **verzameling alternatieve toegangs toewijzingen** wordt geopend.
    1. Filter de weer gave met de webtoepassing die is uitgebreid en bevestig dat u er ongeveer als volgt uitziet:

        ![Alternatieve toegangs toewijzingen van uitgebreide toepassing](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Zorg ervoor dat de share point-webtoepassing wordt uitgevoerd onder een domein account

Voer de volgende stappen uit om het account te identificeren dat de groep van toepassingen van de share point-webtoepassing uitvoert en te controleren of het een domein account is:

1. Open de site **Centraal beheer van share point** .
1. Ga naar **beveiliging** en selecteer **service accounts configureren**.
1. Selecteer **groep van webtoepassingen-YourWebApplicationName**.

   ![Opties voor het configureren van een service account](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Bevestig dat **Selecteer een account voor dit onderdeel** een domein account retourneert en onthoud het omdat dit in de volgende stap nodig is.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Zorg ervoor dat er een HTTPS-certificaat is geconfigureerd voor de IIS-site van de extranet zone

Omdat de interne URL HTTPS-protocol ( `https://SharePoint/` ) gebruikt, moet er een certificaat worden ingesteld op de site van de Internet Information Services (IIS).

1. Open de Windows Power shell-console.
1. Voer het volgende script uit om een zelfondertekend certificaat te genereren en toe te voegen aan de mijn Store van de computer:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Zelfondertekende certificaten zijn alleen geschikt voor test doeleinden. In productie omgevingen wordt u ten zeerste aangeraden certificaten te gebruiken die zijn uitgegeven door een certificerings instantie.

1. Open de Internet Information Services Manager-console.
1. Vouw de server in de structuur weergave uit, vouw **sites** uit, selecteer de **share point-Aad-proxy** site en selecteer **bindingen**.
1. Selecteer **HTTPS-binding** en selecteer vervolgens **bewerken**.
1. Kies in het veld TLS/SSL-certificaat de optie **share point-** certificaat en selecteer vervolgens **OK**.

U kunt nu extern toegang krijgen tot de share point-site via Azure AD-toepassingsproxy.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Stap 3: beperkte Kerberos-delegering configureren

Gebruikers worden eerst geverifieerd in azure AD en vervolgens naar share point met behulp van Kerberos via de Azure AD-proxy connector. Als u de connector wilt toestaan een Kerberos-token te verkrijgen namens de Azure AD-gebruiker, moet u Kerberos-beperkte overdracht (KCD) configureren met protocol overgang. Zie overzicht van beperkte [Kerberos-delegering](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))voor meer informatie over KCD.

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>De SPN voor het share point-service account instellen

In dit artikel is de interne URL `https://sharepoint` , en dus de service principal name (SPN) `HTTP/sharepoint` . U moet deze waarden vervangen door de waarden die overeenkomen met uw omgeving.
Als u de SPN `HTTP/sharepoint` voor het account van de share point-toepassings groep wilt registreren `Contoso\spapppool` , voert u de volgende opdracht uit vanaf een opdracht prompt als beheerder van het domein:

`setspn -S HTTP/sharepoint Contoso\spapppool`

De `Setspn` opdracht zoekt naar de SPN voordat deze wordt toegevoegd. Als de SPN al bestaat, wordt er een **dubbele SPN-waarde** weer geven. In dat geval kunt u de bestaande SPN verwijderen als deze niet is ingesteld onder het juiste account voor de groep van toepassingen. U kunt controleren of de SPN is toegevoegd door de `Setspn` opdracht uit te voeren met de-L-optie. Zie [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))voor meer informatie over deze opdracht.

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Zorg ervoor dat de connector wordt vertrouwd voor delegering naar de SPN die is toegevoegd aan het account van de share point-toepassings groep

Configureer de KCD zodat de Azure AD-toepassingsproxy-Service gebruikers identiteiten kan delegeren aan het account van de share point-toepassings groep. Configureer KCD zodanig dat de Application Proxy Connector Kerberos-tickets ophaalt voor uw gebruikers die zijn geverifieerd in Azure AD. Deze server geeft de context vervolgens door aan de doel toepassing (share point in dit geval).

Als u de KCD wilt configureren, voert u de volgende stappen uit voor elke connector computer:

1. Meld u aan bij een domein controller als een domein beheerder en Open Active Directory gebruikers en computers.
1. Zoek de computer waarop de Azure AD-proxy connector wordt uitgevoerd. In dit voor beeld is dit de share Point-server zelf.
1. Dubbelklik op de computer en selecteer het tabblad **Delegatie**.
1. Zorg ervoor dat de overdrachts opties zo zijn ingesteld dat **deze computer alleen mag delegeren aan de opgegeven services**. Selecteer vervolgens **elk verificatie protocol gebruiken**.
1. Selecteer de knop **toevoegen** , selecteer **gebruikers of computers** en zoek het account voor de share point-toepassings groep. Bijvoorbeeld: `Contoso\spapppool`.
1. Selecteer in de lijst met Spn's de naam die u eerder hebt gemaakt voor het service account.
1. Selecteer **OK** en selecteer vervolgens **OK** om uw wijzigingen op te slaan.
  
   ![Instellingen voor delegering](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

U bent nu klaar om u aan te melden bij share point met behulp van de externe URL en om te verifiëren met Azure.

## <a name="troubleshoot-sign-in-errors"></a>Fouten bij het aanmelden oplossen

Als aanmelding bij de site niet werkt, kunt u meer informatie krijgen over het probleem in de connector logboeken: Open Logboeken op de computer waarop de connector wordt uitgevoerd, ga naar **toepassingen en services, registreert**  >  **micro soft**  >  **AadApplicationProxy**  >  -**connector** en Inspecteer het **beheer** logboek.

## <a name="next-steps"></a>Volgende stappen

* [Werken met aangepaste domeinen in azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)
* [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)