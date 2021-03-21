---
title: 'Zelf studie: een on-premises app-toepassings proxy toevoegen in Azure Active Directory'
description: Azure Active Directory (Azure AD) heeft een Application Proxy-service waarmee gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. In deze zelfstudie wordt uitgelegd hoe u uw omgeving voorbereidt voor gebruik van Application Proxy. Vervolgens wordt de Azure-portal gebruikt om een on-premises toepassing toe te voegen aan uw Azure AD-tenant.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 6a7f50268a09ae451b1e9dda2ca354ded31efb68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200754"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory

Azure Active Directory (Azure AD) heeft een Application Proxy-service waarmee gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. Zie [Wat is app proxy?](what-is-application-proxy.md)voor meer informatie over toepassings proxy. In deze zelfstudie wordt uw omgeving voorbereid voor gebruik van Application Proxy. Zodra uw omgeving gereed is, gebruikt u de Azure-portal om een on-premises toepassing toe te voegen aan uw Azure AD-tenant. 

:::image type="content" source="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png" alt-text="Overzichts diagram toepassings proxy" lightbox="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png":::

Voordat u aan de slag gaat, moet u ervoor zorgen dat u bekend bent met de concepten voor app-beheer en **Single Sign-On (SSO)** . Bekijk de volgende koppelingen:
- [Quickstartreeks over app-beheer in Azure AD](view-applications-portal.md)
- [Wat is single Sign-On (SSO)?](what-is-single-sign-on.md)

Connectors zijn een belangrijk onderdeel van Application Proxy. Zie [Informatie over connectors voor de Azure AD-toepassingsproxy](application-proxy-connectors.md) voor meer informatie over connectors.

Deze zelfstudie:

> [!div class="checklist"]
> * Opent poorten voor uitgaand verkeer en verschaft toegang tot specifieke URL 's
> * Installeert de connector op uw Windows-server en registreert deze bij Application Proxy
> * Controleert of de connector juist is geïnstalleerd en geregistreerd
> * Voegt een toepassing toe aan uw Azure AD-tenant
> * Controleert of een testgebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

## <a name="prerequisites"></a>Vereisten

Als u een on-premises toepassing wilt toevoegen aan Azure AD, hebt u het volgende nodig:

* Een [Microsoft Azure AD Premium-abonnement](https://azure.microsoft.com/pricing/details/active-directory)
* Een beheerdersaccount voor de toepassing
* Gebruikersidentiteiten moeten vanuit een on-premises directory worden gesynchroniseerd of rechtstreeks in uw Azure AD-tenants worden gemaakt. Dankzij identiteitssynchronisatie kan Azure AD gebruikers vooraf verifiëren alvorens hen toegang tot gepubliceerde Application Proxy-toepassingen te verlenen, en over de benodigde gebruikers-id-gegevens beschikken om eenmalige aanmelding (SSO) uit te voeren.

### <a name="windows-server"></a>Windows-server

Als u Application Proxy wilt gebruiken, hebt u een Windows-server nodig waarop Windows Server 2012 R2 of hoger wordt uitgevoerd. U installeert de Application Proxy-connector op de server. Deze connectorserver moet verbinding maken met de Application Proxy-service in Azure en met de on-premises toepassingen die u van plan bent te publiceren.

Om een hoge beschikbaarheid in uw productieomgeving te realiseren wordt aangeraden meer dan één Windows-server te gebruiken. Voor deze zelfstudie is één Windows-server toereikend.

> [!IMPORTANT]
> Als u de connector op Windows Server 2019 installeert, moet u HTTP2-protocolondersteuning uitschakelen in het WinHTTP-onderdeel voor beperkte Kerberos-delegering. Dit is standaard uitgeschakeld in eerdere versies van ondersteunde besturingssystemen. U kunt het uitschakelen op Windows Server 2019 door de volgende registersleutel toe te voegen en de server opnieuw te starten. Dit is een machinebrede registersleutel.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp]
> "EnableDefaultHTTP2"=dword:00000000
> ```
>
> De sleutel kan worden ingesteld via PowerShell met de volgende opdracht.
> ```
> Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\' -Name EnableDefaultHTTP2 -Value 0
> ```

#### <a name="recommendations-for-the-connector-server"></a>Aanbevelingen voor de connectorserver

1. Plaats de connectorserver fysiek dicht bij de toepassingsservers om de prestaties tussen de connector en de toepassing te optimaliseren. Zie [verkeers stroom optimaliseren met Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md)voor meer informatie.
1. De connectorserver en de webtoepassingsservers moeten deel uitmaken van hetzelfde Active Directory-domein of zich in vertrouwde domeinen bevinden. De servers moeten zich in hetzelfde domein of in vertrouwde domeinen bevinden om gebruik te kunnen maken van eenmalige aanmelding (SSO) met geïntegreerde Windows-verificatie en beperkte delegatie van Kerberos. Als de connectorserver en de webtoepassingsservers zich in verschillende Active Directory-domeinen bevinden, moet u delegatie op basis van resources gebruiken voor eenmalige aanmelding. Zie [KCD voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) voor meer informatie.

> [!WARNING]
> Als u Azure AD Password Protection Proxy hebt geïmplementeerd, moet u Azure AD Application Proxy en Azure AD Password Protection Proxy niet samen op dezelfde machine installeren. Azure AD Application Proxy en Azure AD Password Protection Proxy installeren verschillende versies van de Azure AD Connect Agent Updater-service. Deze verschillende versies zijn niet compatibel wanneer ze samen op dezelfde machine worden geïnstalleerd.

#### <a name="tls-requirements"></a>TLS-vereisten

Voor de Windows-connectorserver moet TLS 1.2 zijn ingeschakeld voordat u de Application Proxy-connector installeert.

TLS 1.2 inschakelen:

1. Stel de volgende registersleutels in:

   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Start de server opnieuw.

> [!Note]
> Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Deze wijziging wordt doorgevoerd omdat de huidige CA-certificaten niet voldoen aan een van de forumbasislijnvereisten voor CA's/browsers. Raadpleeg [Wijzigingen in Azure TLS-certificaten](../../security/fundamentals/tls-certificate-changes.md) voor meer informatie.

## <a name="prepare-your-on-premises-environment"></a>Bereid uw on-premises omgeving voor

Begin door communicatie met Azure-datacenters in te schakelen om uw omgeving voor te bereiden voor Azure AD Application Proxy. Als het pad een firewall bevat, zorg dan dat deze openstaat. Dankzij een open firewall kan de connector HTTPS-aanvragen (TCP) versturen naar Application Proxy.

> [!IMPORTANT]
> Als de connector voor de Azure Government-cloud installeert, volgt u de [vereisten](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) en [installatiestappen](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Hiervoor moet u de toegang tot een andere set URL's en een extra parameter inschakelen om de installatie uit te voeren.

### <a name="open-ports"></a>Poorten openen

Open de volgende poorten voor **uitgaand** verkeer.

| Poortnummer | Hoe dat wordt gebruikt |
| ----------- | ------------------------------------------------------------ |
| 80          | Het downloaden van certificaatintrekkingslijsten (CRL's) tijdens het valideren van het TLS-/SSL-certificaat |
| 443         | Alle uitgaande communicatie met de Application Proxy-service |

Als met uw firewall verkeer wordt afgedwongen op basis van de herkomst van gebruikers, open dan ook poorten 80 en 443 voor verkeer dat afkomstig is van Windows-services die als netwerkservice worden uitgevoerd.

### <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan

Sta toegang tot de volgende URL's toe:

| URL | Poort | Hoe dat wordt gebruikt |
| ------------------------------------------------------------ | --------- | ------------------------------------------------------------ |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net         | 443/HTTPS | Communicatie tussen de connector en de Application Proxy-cloudservice |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP   | De connector gebruikt deze URL's om certificaten te verifiëren.        |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS | De connector gebruikt deze URL's tijdens het registratieproces. |
| ctldl.windowsupdate.com                                      | 80/HTTP   | De connector gebruikt deze URL tijdens het registratieproces. |

U kunt verbinding maken met &ast; msappproxy.net, &ast; . servicebus.Windows.net en andere url's hierboven als uw firewall of proxy u in staat stelt om toegangs regels te configureren op basis van domein achtervoegsels. Zo niet, dan moet u toegang tot de [Azure IP-bereiken en -servicetags – Openbare cloud](https://www.microsoft.com/download/details.aspx?id=56519) toestaan. die overigens elke week worden bijgewerkt.

### <a name="dns-name-resolution-for-azure-ad-application-proxy-endpoints"></a>DNS-naam omzetting voor Azure AD-toepassingsproxy-eind punten

Open bare DNS-records voor Azure AD-toepassingsproxy-eind punten zijn aaneengeschakelde CNAME-records die naar een record verwijzen. Dit zorgt voor de fout tolerantie en flexibiliteit. Het is gegarandeerd dat de Azure AD-toepassingsproxy-connector altijd toegang krijgt tot hostnamen met de domein achtervoegsels _*. msappproxy.net_ of _*. servicebus.Windows.net_. Tijdens de naam omzetting kunnen CNAME-records echter DNS-records met verschillende hostnamen en achtervoegsels bevatten.  Als gevolg hiervan moet u ervoor zorgen dat het apparaat (afhankelijk van uw Setup-connector server, firewall, uitgaande proxy) alle records in de keten kan omzetten en dat er verbinding kan worden met de omgezette IP-adressen. Omdat de DNS-records in de keten mogelijk van tijd tot tijd worden gewijzigd, kunnen we u geen lijst met DNS-records geven.

## <a name="install-and-register-a-connector"></a>Een connector installeren en registreren

Voor het gebruik van Application Proxy installeert u een connector op elke Windows-server die u gebruikt met de Application Proxy-service. De connector is een agent die de uitgaande verbinding vanaf de on-premises toepassingsservers naar Application Proxy in Azure AD beheert. U kunt een connector installeren op servers waarop ook andere verificatie-agents zijn geïnstalleerd, zoals Azure AD Connect.


De connector installeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als een toepassingsbeheerder van de map die gebruikmaakt van Application Proxy. Als het domein van de tenant bijvoorbeeld contoso.com is, moet de beheerder admin@contoso.com of een andere beheerdersalias in dat domein zijn.
1. Selecteer uw gebruikersnaam in de rechterbovenhoek. Controleer of u bent aangemeld in een directory die gebruikmaakt van Application Proxy. Als u van directory moet veranderen, selecteert u **Schakelen tussen directory’s** en kiest u een directory die gebruikmaakt van Application Proxy.
1. Selecteer **Azure Active Directory** in het navigatiepaneel aan de linkerkant.
1. Selecteer onder **Beheren** de optie **Application Proxy**.
1. Selecteer **Service-connector downloaden**.

    ![Download de service-connector om de servicevoorwaarden te bekijken](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Lees de servicevoorwaarden. Wanneer u klaar bent, selecteert u **Voorwaarden accepteren en downloaden**.
1. Selecteer **Uitvoeren** onderaan het venster om de connector te installeren. Er wordt een installatiewizard geopend.
1. Volg de instructies in de wizard om de service te installeren. Wanneer u wordt gevraagd de connector te registreren voor de Application Proxy voor uw Azure AD-tenant, geeft u uw gegevens als toepassingsbeheerder op.
   
    - Voor Internet Explorer (IE) geldt dat als **Verbeterde beveiliging van Internet Explorer** is ingesteld op **Aan**, het registratiescherm niet wordt weergegeven. Volg de instructies in het foutbericht om toegang te krijgen. Zorg ervoor dat **Verbeterde beveiliging van Internet Explorer** is ingesteld op **Uit**.

### <a name="general-remarks"></a>Algemene opmerkingen

Als u al een connector hebt geïnstalleerd, voert u een nieuwe installatie uit met de meest recente versie. Voor informatie over eerder uitgebrachte versies en welke wijzigingen ze bevatten, raadpleegt u [Application Proxy: releasegeschiedenis van versie](application-proxy-release-version-history.md).

Als u meer dan één Windows-server voor uw on-premises toepassingen wilt, moet u de connector op elke server installeren en registreren. U kunt de connectors onderverdelen in connectorgroepen. Zie [Connectorgroepen](application-proxy-connector-groups.md) voor meer informatie.

Als u connectors in verschillende regio's hebt geïnstalleerd, kunt u het verkeer optimaliseren door de dichtstbijzijnde Cloud service regio van de toepassing te selecteren voor gebruik met elke connector groep. Zie [verkeers stroom optimaliseren met Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md)

Als uw organisatie proxyservers gebruikt om verbinding met internet te maken, moet u ze configureren voor Application Proxy.  Zie [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md) voor meer informatie. 

Zie [Informatie over Azure AD Application Proxy-connectors](application-proxy-connectors.md) voor meer informatie over connectors, capaciteitsplanning en hoe connectors up-to-date blijven.

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Controleer of de connector juist is geïnstalleerd en geregistreerd

U kunt de Azure-portal of uw Windows-server gebruiken om te bevestigen dat er een nieuwe connector correct is geïnstalleerd.

### <a name="verify-the-installation-through-azure-portal"></a>De installatie verifiëren via de Azure-portal

Controleren of de connector juist is geïnstalleerd en geregistreerd:

1. Meld u aan bij uw tenantmap in de [Azure-portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory** in het navigatiepaneel aan de linkerkant en selecteer vervolgens **Application Proxy** in de sectie **Beheren**. Al uw connectors en connectorgroepen worden op deze pagina weergegeven.
1. Bekijk een connector om de details ervan te controleren. De connectors moeten standaard zijn uitgevouwen. Als de connector die u wilt bekijken niet is uitgevouwen, vouwt u de connector uit om de details te bekijken. Een actief groen label geeft aan dat de connector verbinding kan maken met de service. Maar ook al is het label groen, toch kan een netwerkprobleem er nog steeds voor zorgen dat de connector geen berichten ontvangt.

    ![Azure AD Application Proxy-connectors](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Zie [Probleem bij het installeren van de Application Proxy-connector](application-proxy-connector-installation-problem.md) voor meer hulp bij het installeren van een connector.

### <a name="verify-the-installation-through-your-windows-server"></a>De installatie verifiëren via uw Windows-server

Controleren of de connector juist is geïnstalleerd en geregistreerd:

1. Open Windows Services Manager door te klikken op de **Windows**-toets en *services.msc* in te voeren.
1. Controleer of de status van de volgende twee services **Wordt uitgevoerd** is.
   - Met **Microsoft AAD Application Proxy Connector** wordt de connectiviteit mogelijk gemaakt.
   - **Microsoft AAD Application Proxy Connector Updater** is een automatische updateservice. De updater controleert op nieuwe versies van de connector en werkt de connector bij als dat nodig is.

     ![Connectorservices voor toepassingsproxy - schermafbeelding](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Als de status van de services niet **Wordt uitgevoerd** is, klikt u met de rechtermuisknop om elke service te selecteren en kiest u **Starten**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Een on-premises app toevoegen aan Azure AD

Nu u uw omgeving hebt voorbereid en een connector hebt geïnstalleerd, kunt u on-premises toepassingen gaan toevoegen aan Azure AD.  

1. Meld u aan als beheerder in de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Azure Active Directory** in het navigatiepaneel aan de linkerkant.
3. Selecteer **Ondernemingstoepassingen** en selecteer vervolgens **Nieuwe toepassing**.
4. Selecteer de knop **Een on-premises toepassing toevoegen**. Deze ziet u ergens halverwege de pagina in de sectie **On-premises toepassingen**. U kunt ook bovenaan de pagina **Uw eigen toepassing maken** selecteren en vervolgens **Toepassingsproxy configureren voor veilige externe toegang tot een on-premises toepassing** selecteren.
5. Geef in de sectie **Uw eigen on-premises toepassing toevoegen** de volgende informatie over uw toepassing op:

    | Veld  | Beschrijving |
    | :--------------------- | :----------------------------------------------------------- |
    | **Naam** | De naam van de toepassing die wordt weergegeven in My Apps en in de Azure-portal. |
    | **Interne URL** | Dit is de URL voor toegang tot de toepassing vanuit uw particuliere netwerk. U kunt voor het publiceren een specifiek pad opgeven op de back-endserver, terwijl de rest van de server ongepubliceerd blijft. Op deze manier kunt u verschillende sites op dezelfde server als verschillende apps publiceren en elk daarvan een eigen naam en toegangsregels geven.<br><br>Als u een pad publiceert, moet u ervoor zorgen dat dit alle benodigde installatiekopieën, scripts en opmaakmodellen voor uw toepassing bevat. Als uw app zich bijvoorbeeld bevindt op het pad https:\//uwapp/app en gebruikmaakt van installatiekopieën op https:\//uwapp/media, moet u https:\//uwapp/ publiceren als het pad. Deze interne URL hoeft niet de bestemmingspagina te zijn die uw gebruikers te zien krijgen. Zie [Een aangepaste startpagina voor gepubliceerde apps instellen](application-proxy-configure-custom-home-page.md) voor meer informatie. |
    | **Externe URL** | Het adres voor gebruikers om toegang te krijgen tot de app van buiten uw netwerk. Als u het standaarddomein voor Application Proxy niet wilt gebruiken, lees dan de informatie over [aangepaste domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md). |
    | **Verificatie vooraf** | De manier waarop gebruikers door Application Proxy worden geverifieerd voordat ze toegang krijgen tot uw toepassing.<br><br>**Azure Active Directory**: de gebruikers worden omgeleid door Application Proxy zodat ze zich kunnen aanmelden met Azure AD. Hierbij worden hun machtigingen geverifieerd voor de map en de toepassing. Het is raadzaam deze optie standaard ingesteld te houden, zodat u gebruik kunt maken van Azure AD-beveiligingsfuncties zoals voorwaardelijke toegang en meervoudige verificatie. **Azure Active Directory** is vereist voor het bewaken van de toepassing met Microsoft Cloud Application Security.<br><br>**Passthrough**: gebruikers hoeven geen verificatie te doorlopen in Azure AD om toegang te krijgen tot de toepassing. U kunt nog steeds verificatievereisten op de back-end instellen. |
    | **Connectorgroep** | Connectors verwerken de externe toegang tot uw toepassing en met connectorgroepen kunt u connectors en toepassingen indelen per regio, netwerk of doel. Als u nog geen connectorgroepen hebt gemaakt, wordt uw toepassing toegewezen als **Standaard**.<br><br>Als uw toepassing gebruikmaakt van WebSockets om verbinding te maken, moeten alle connectors in de groep versie 1.5.612.0 of hoger hebben. |

6. Configureer zo nodig **aanvullende instellingen**. Voor de meeste toepassingen moet u voor deze instellingen de standaardwaarden behouden. 

    | Veld | Beschrijving |
    | :------------------------------ | :----------------------------------------------------------- |
    | **Toepassingstime-out voor de back-end** | Stel deze waarde alleen in op **Lang** als uw toepassing traag is met verifiëren en verbinding maken. De toepassingstime-out voor de back-end is standaard 85 seconden. Wanneer u de waarde instelt op ‘Lang’, wordt de time-out voor de back-end verlengd tot 180 seconden. |
    | **Alleen-HTTP-cookies gebruiken** | Stel deze waarde in op **Ja** om ervoor te zorgen dat Application Proxy-cookies de HTTPOnly-vlag in de HTTP-antwoordheader bevatten. Als u Extern bureaublad-services gebruikt, stelt u deze waarde in op **Nee**. |
    | **Beveiligde cookies gebruiken**| Stel deze waarde in op **Ja** om cookies te verzenden via een beveiligd kanaal, zoals een versleutelde HTTPS-aanvraag.
    | **Permanente cookies gebruiken**| Houd deze waarde ingesteld op **Nee**. Gebruik deze instelling alleen voor toepassingen die cookies niet kunnen delen tussen processen. Zie [Cookie-instellingen voor toegang tot on-premises toepassingen in Azure Active Directory](./application-proxy-configure-cookie-settings.md) voor meer informatie over cookie-instellingen.
    | **URL's in headers vertalen** | Laat deze waarde op **Ja** staan, tenzij voor uw toepassing de oorspronkelijke host-header in de verificatieaanvraag moet zijn opgenomen. |
    | **URL's vertalen in de hoofdtekst van de toepassing** | Laat deze waarde op **Nee** staan, tenzij u hardcoded HTML-koppelingen naar andere on-premises toepassingen hebt en geen aangepaste domeinen gebruikt. Zie [Vertaling koppelen aan Application Proxy](application-proxy-configure-hard-coded-link-translation.md) voor meer informatie.<br><br>Stel deze waarde in op **Ja** als u van plan bent om deze toepassing te bewaken met Microsoft Cloud App Security (MCAS). Zie [Configuratie van realtime bewaking van toegang tot toepassingen met Microsoft Cloud App Security en Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md) voor meer informatie. |

7. Selecteer **Toevoegen**.

## <a name="test-the-application"></a>De toepassing testen

U kunt nu testen of de toepassing correct is toegevoegd. Hiervoor gaat u in de volgende stappen een gebruikersaccount toevoegen aan de toepassing en u vervolgens proberen aan te melden.

### <a name="add-a-user-for-testing"></a>Een gebruiker toevoegen voor het testen

Controleer voordat u een gebruiker aan de toepassing toevoegt of het gebruikersaccount al machtigingen heeft voor toegang tot de toepassing vanuit het bedrijfsnetwerk.

Een testgebruiker toevoegen:

1. Selecteer **Ondernemingstoepassingen** en selecteer vervolgens de toepassing die u wilt testen.
2. Selecteer **Aan de slag** en selecteer vervolgens **Een gebruiker toewijzen voor de test**.
3. Selecteer onder **Gebruikers en groepen** de optie **Gebruiker toevoegen**.
4. Selecteer onder **Toewijzing toevoegen** de optie **Gebruikers en groepen**. De sectie **Gebruikers en groepen** wordt nu weergegeven.
5. Kies het account dat u wilt toevoegen.
6. Kies **Selecteren** en selecteer vervolgens **Toewijzen**.

### <a name="test-the-sign-on"></a>De aanmelding testen

De aanmelding voor de toepassing testen:

1. Selecteer **Application Proxy** in de toepassing die u wilt testen.
2. Selecteer **Toepassing testen** bovenaan de pagina om een test op de toepassing uit te voeren en om op eventuele configuratieproblemen te controleren.
3. Zorg ervoor dat u eerst de toepassing opent om de aanmelding voor de toepassing te testen en vervolgens het diagnostische rapport downloadt om de hulp te bekijken voor het oplossen van eventuele gedetecteerde problemen.

Zie [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md) voor het oplossen van problemen.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resources die u in deze zelfstudie hebt gemaakt, als u ze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw on-premises omgeving voorbereid om te werken met Application Proxy en hebt u vervolgens de Application Proxy-connector geïnstalleerd en geregistreerd. Vervolgens hebt u een toepassing toegevoegd aan uw Azure AD-tenant. U hebt ook gecontroleerd of een gebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

U hebt het volgende gedaan:
> [!div class="checklist"]
> * Poorten geopend voor uitgaand verkeer en toegang verschaft tot specifieke URL 's
> * De connector geïnstalleerd op uw Windows-server en deze geregistreerd bij Application Proxy
> * Gecontroleerd of de connector juist is geïnstalleerd en geregistreerd
> * Een toepassing toegevoegd aan uw Azure AD-tenant
> * Gecontroleerd of een testgebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

U kunt de toepassing nu gaan configureren voor eenmalige aanmelding. Gebruik de volgende koppeling om een methode voor eenmalige aanmelding te kiezen en om de zelfstudies voor eenmalige aanmelding te vinden.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](sso-options.md#choosing-a-single-sign-on-method)
