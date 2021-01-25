---
title: Veelgestelde vragen over Azure Active Directory-toepassingsproxy
description: Hier vindt u antwoorden op veelgestelde vragen over het gebruik van Azure AD-toepassingsproxy voor het publiceren van interne, on-premises toepassingen naar externe gebruikers.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: f1a1d43f9bfc960628447a3c20e7dab2ac701dc5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762574"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Veelgestelde vragen over de toepassings proxy van Active Directory (Azure AD)

Op deze pagina vindt u antwoorden op veelgestelde vragen over Azure Active Directory-toepassings proxy (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Azure AD-toepassingsproxy inschakelen

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Welke licentie is vereist voor het gebruik van Azure AD-toepassingsproxy?

Als u Azure AD-toepassingsproxy wilt gebruiken, moet u een Azure AD Premium P1-of P2-licentie hebben. Zie voor meer informatie over licenties [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>Wat gebeurt er met Azure AD-toepassingsproxy in mijn Tenant, als mijn licentie verloopt?
Als uw licentie is verlopen, wordt de toepassings proxy automatisch uitgeschakeld. Uw toepassings gegevens worden Maxi maal één jaar opgeslagen.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Waarom is de knop toepassings proxy inschakelen grijs?

Zorg ervoor dat er ten minste een Azure AD Premium P1-of P2-licentie en een Azure AD-toepassingsproxy-connector is geïnstalleerd. Nadat u uw eerste connector hebt geïnstalleerd, wordt de Azure AD-toepassingsproxy-service automatisch ingeschakeld.

## <a name="connector-configuration"></a>Connector configuratie

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Kunnen Application proxy connector services worden uitgevoerd in een andere gebruikers context dan de standaard instelling?

Nee, dit scenario wordt niet ondersteund. De standaard instellingen zijn:

- Micro soft AAD Application proxy connector-WAPCSvc-Network Service
- Microsoft AAD Application Proxy Connector Updater-WAPCUpdaterSvc-NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>Kan een gast gebruiker met de globale beheerder of de rol toepassings beheerder de connector voor de Tenant (gast) registreren?

Nee, dit is op dit moment niet mogelijk. De registratie poging wordt altijd uitgevoerd op de thuis Tenant van de gebruiker.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Mijn back-endtoepassing wordt gehost op meerdere webservers en vereist persistentie van gebruikers sessies (persistentie). Hoe kan ik een sessie persistentie uitvoeren? 

Zie [hoge Beschik baarheid en taak verdeling voor de connectors en toepassingen van uw toepassings proxy](application-proxy-high-availability-load-balancing.md)voor aanbevelingen.

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Wordt TLS-beëindiging (TLS/HTTPS-inspectie of-versnelling) op verkeer van de connector servers naar Azure ondersteund?

De Application proxy-connector voert verificatie op basis van certificaten uit voor Azure. TLS-beëindiging (TLS/HTTPS-inspectie of-versnelling) verbreekt deze verificatie methode en wordt niet ondersteund. Verkeer van de connector naar Azure moet alle apparaten die TLS-beëindiging uitvoeren, overs Laan.  

### <a name="is-tls-12-required-for-all-connections"></a>Is TLS 1,2 vereist voor alle verbindingen?
Ja. Om onze klanten de allerbeste versleuteling te bieden, beperkt de Application Proxy-service de toegang tot alleen TLS 1.2-protocollen. Deze wijzigingen zijn geleidelijk aan geïmplementeerd en van kracht sinds 31 augustus 2019. Zorg ervoor dat al uw client-server- en browser-server-combinaties zijn bijgewerkt en TLS 1.2 gebruiken om verbonden te blijven met de Application Proxy-service. Dit omvat clients die uw gebruikers gebruiken voor toegang tot toepassingen die worden gepubliceerd via Application Proxy. Zie [Voorbereiden voor TLS 1.2 in Office 365](/microsoft-365/compliance/prepare-tls-1.2-in-office-365) voor handige naslaginformatie en bronnen.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Kan ik een doorstuur proxy apparaat tussen de connector server (s) en de back-end-toepassings server plaatsen?
Ja, dit scenario wordt ondersteund vanaf de connector versie 1.5.1526.0. Zie [werken met bestaande on-premises proxy servers](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Moet ik een toegewezen account maken om de connector te registreren bij Azure AD-toepassingsproxy?

Er is geen reden voor. Een globale beheerder of een beheerders account voor de toepassing wordt gebruikt. De referenties die tijdens de installatie worden ingevoerd, worden niet gebruikt na het registratie proces. In plaats daarvan wordt er een certificaat uitgegeven aan de connector, die wordt gebruikt voor verificatie vanaf dat moment op.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Hoe kan ik de prestaties van de Azure AD-toepassingsproxy-connector controleren?

Er zijn prestatie meter items die samen met de connector worden geïnstalleerd. Als u deze wilt weergeven:  

1. Selecteer **Start**, typ ' perfmon ' en druk op ENTER.
2. Selecteer **prestatie meter** en klik op het groene **+** pictogram.
3. Voeg de **micro soft Aad Application proxy connector** -items toe die u wilt bewaken.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Moet de Azure AD-toepassingsproxy-connector zich in hetzelfde subnet bevinden als de resource?

De connector hoeft zich niet in hetzelfde subnet bevinden. Er is echter een naam omzetting (DNS, hosts-bestand) nodig voor de bron en de benodigde netwerk verbinding (route ring naar de resource, poorten die zijn geopend op de bron, enzovoort). Zie [overwegingen met betrekking tot netwerk topologie bij het gebruik van Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md)voor aanbevelingen.

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>Met welke versies van Windows Server kan ik een connector installeren?

Toepassings proxy vereist Windows Server 2012 R2 of hoger. Er is momenteel een beperking op HTTP2 voor Windows Server 2019. Als u de connector op Windows Server 2019 wilt gebruiken, moet u de volgende register sleutel toevoegen en de server opnieuw opstarten:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
```

## <a name="application-configuration"></a>Toepassingsconfiguratie

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Ik ontvang een fout bericht over een ongeldig certificaat of mogelijk een onjuist wacht woord

Nadat u het SSL-certificaat hebt geüpload, ontvangt u het bericht ' ongeldig certificaat, mogelijk onjuist wacht woord ' in de portal.

Hier volgen enkele tips voor het oplossen van deze fout:
- Controleren op problemen met het certificaat. Installeer het op uw lokale computer. Als u geen problemen ondervindt, is het certificaat goed.
- Zorg ervoor dat het wacht woord geen speciale tekens bevat. Voor het testen moet het wacht woord alleen de tekens 0-9, A-Z en a-z bevatten.
- Als het certificaat is gemaakt met micro soft software Key Storage Provider, moet de RSA-algoritme worden gebruikt.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Wat is de lengte van de standaard waarde en ' lange ' time-out voor back-end? Kan de time-out worden verlengd?

De standaard lengte is 85 seconden. De instelling ' lang ' is 180 seconden. De time-outlimiet kan niet worden uitgebreid.

### <a name="can-a-service-principal-manage-application-proxy-using-powershell-or-microsoft-graph-apis"></a>Kan een Service-Principal toepassings proxy beheren met behulp van Power shell-of Microsoft Graph-Api's?

Nee, dit wordt momenteel niet ondersteund.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Hoe kan ik de landings pagina wijzigen die door mijn toepassing worden geladen?

Op de pagina Toepassings registraties kunt u de URL van de start pagina wijzigen in de gewenste externe URL van de landings blad. De opgegeven pagina wordt geladen wanneer de toepassing wordt gestart vanuit mijn apps of de Office 365-Portal. Zie voor configuratie stappen [een aangepaste start pagina instellen voor gepubliceerde apps met behulp van Azure AD-toepassingsproxy](./application-proxy-configure-custom-home-page.md)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Kunnen alleen IIS-toepassingen worden gepubliceerd? Hoe zit het met webtoepassingen die worden uitgevoerd op niet-Windows webservers? Moet de connector worden geïnstalleerd op een server waarop IIS is geïnstalleerd?

Nee, er is geen IIS-vereiste voor toepassingen die worden gepubliceerd. U kunt webtoepassingen die worden uitgevoerd op servers met uitzonde ring van Windows Server publiceren. Het is echter mogelijk dat u geen vooraf-verificatie kunt gebruiken met een niet-Windows-Server, afhankelijk van of de webserver Negotiate (Kerberos-verificatie) ondersteunt. IIS is niet vereist op de server waarop de connector is geïnstalleerd.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>Kan ik toepassings proxy configureren om de HSTS-header toe te voegen?
Toepassings proxy voegt de header HTTP strict-Trans Port-Security niet automatisch toe aan HTTPS-antwoorden, maar behoudt de header als deze zich in het oorspronkelijke antwoord bevindt dat is verzonden door de gepubliceerde toepassing. Het bewijs van een instelling om deze functionaliteit in te scha kelen, bevindt zich op het schema. Als u geïnteresseerd bent in een preview-versie waarmee u deze kunt toevoegen aan antwoorden, kunt u contact opdoen met aadapfeedback@microsoft.com voor meer informatie.

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Wanneer moet ik de methode PrincipalsAllowedToDelegateToAccount gebruiken bij het instellen van een Kerberos-beperkte overdracht (KCD)?

De methode PrincipalsAllowedToDelegateToAccount wordt gebruikt wanneer connector servers zich in een ander domein van het Web Application Service-account bevinden. Hiervoor is het gebruik van op resources gebaseerde beperkte delegering vereist.
Als de connector servers en het Web Application Service-account zich in hetzelfde domein bevinden, kunt u Active Directory gebruikers en computers gebruiken om de delegatie-instellingen op elk van de connector computer accounts te configureren, zodat ze kunnen worden gedelegeerd naar de SPN van het doel.

Als de connector servers en het Web Application Service-account zich in verschillende domeinen bevinden, wordt delegering op basis van resources gebruikt. De machtigingen voor delegering zijn geconfigureerd op de doel webserver en Web Application Service-account. Deze methode van beperkte delegering is relatief nieuw. De methode is geïntroduceerd in Windows Server 2012, die ondersteuning biedt voor Cross-Domain delegering door de eigenaar van de resource (webservice) te laten bepalen welke computer-en service accounts er kunnen worden gedelegeerd. Er is geen gebruikers interface die u kan helpen bij deze configuratie. hiervoor moet u Power shell gebruiken.
Zie voor meer informatie het technisch document [over Kerberos-beperkte overdracht met toepassings proxy](https://aka.ms/kcdpaper).

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>Werkt NTLM-verificatie met Azure AD-toepassingsproxy?

NTLM-verificatie kan niet worden gebruikt als een methode voor verificatie vooraf of eenmalige aanmelding. NTLM-verificatie kan alleen worden gebruikt wanneer er direct kan worden onderhandeld tussen de client en de gepubliceerde webtoepassing. Het gebruik van NTLM-verificatie zorgt er meestal voor dat een aanmeldings prompt wordt weer gegeven in de browser.

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>Kan ik de aanmeldings-id ' on-premises user principal name ' of ' on-premises SAM-account naam ' gebruiken in het scenario voor eenmalige aanmelding bij B2B-IWA?

Nee, dit is niet mogelijk, omdat een gast gebruiker in azure AD niet over het kenmerk beschikt dat vereist is voor een van de hierboven genoemde aanmeldings identiteiten.

In dit geval is er sprake van een terugval op ' User Principal Name '. Raadpleeg [Grant B2B-gebruikers in azure AD-toegang tot uw on-premises toepassingen](../external-identities/hybrid-cloud-to-on-premises.md)voor meer informatie over het B2B-scenario.

## <a name="pass-through-authentication"></a>Pass-through-verificatie

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Kan ik beleid voor voorwaardelijke toegang gebruiken voor toepassingen die zijn gepubliceerd met Pass-Through-verificatie?

Beleid voor voorwaardelijke toegang wordt alleen afgedwongen voor geslaagde vooraf geverifieerde gebruikers in azure AD. Pass-Through-verificatie activeert geen Azure AD-verificatie, zodat het beleid voor voorwaardelijke toegang niet kan worden afgedwongen. Bij Pass-Through-verificatie moet MFA-beleid worden geïmplementeerd op de on-premises server, indien mogelijk, of door verificatie vooraf in te scha kelen met Azure AD-toepassingsproxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Kan ik een webtoepassing met verificatie vereiste voor client certificaten publiceren?

Nee, dit scenario wordt niet ondersteund omdat het TLS-verkeer door de toepassings proxy wordt beëindigd.  

## <a name="remote-desktop-gateway-publishing"></a>Extern bureaublad-gateway publicatie

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Hoe kan ik Extern bureaublad-gateway publiceren via Azure AD-toepassingsproxy?

Raadpleeg [extern bureaublad publiceren met Azure AD-toepassingsproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Kan ik beperkte Kerberos-delegering (single Sign-On geïntegreerde Windows-verificatie) gebruiken in het Extern bureaublad-gateway-publicatie scenario?

Nee, dit scenario wordt niet ondersteund.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Mijn gebruikers gebruiken Internet Explorer 11 niet en het scenario voor verificatie vooraf werkt niet. Wordt dit verwacht?

Ja, dit wordt verwacht. Voor het scenario vóór verificatie is een ActiveX-besturings element vereist, wat niet wordt ondersteund in browsers van derden.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Wordt de Extern bureaublad-webclient (HTML5) ondersteund?

Ja, dit scenario is momenteel beschikbaar als open bare preview. Raadpleeg [extern bureaublad publiceren met Azure AD-toepassingsproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Nadat ik het scenario voor verificatie vooraf heb geconfigureerd, heb ik dan de eerste keer een verificatie van de gebruiker uitvoeren: eerst op het aanmeld formulier van Azure AD en vervolgens op het aanmeld formulier RDWeb. Wordt dit verwacht? Hoe kan ik deze beperken tot één aanmelding?

Ja, dit wordt verwacht. Als de computer van de gebruiker is toegevoegd aan Azure AD, wordt de gebruiker automatisch aangemeld bij Azure AD. De gebruiker hoeft alleen referenties op te geven in het RDWeb-aanmeld formulier.

## <a name="sharepoint-publishing"></a>Share point-publicatie

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Hoe kan ik share point publiceren via Azure AD-toepassingsproxy?

Raadpleeg [externe toegang tot share point inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Kan ik de mobiele share point-app (iOS/Android) gebruiken om toegang te krijgen tot een gepubliceerde share Point-server?

De [mobiele share point-app](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) ondersteunt momenteel geen Azure Active Directory vooraf-verificatie.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publicatie van Active Directory Federation Services (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Kan ik Azure AD-toepassingsproxy gebruiken als AD FS proxy (zoals de Web Application proxy)?

Nee. Azure AD-toepassingsproxy is ontworpen voor gebruik met Azure AD en voldoet niet aan de vereisten om als AD FS proxy te fungeren.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Werkt WebSocket ondersteuning voor andere toepassingen dan QlikSense?

WebSocket-protocol ondersteuning is momenteel nog steeds beschikbaar in de open bare preview-versie en werkt mogelijk niet voor andere toepassingen. Sommige klanten hebben het gebruik van WebSocket-protocol met andere toepassingen gemende. Als u dergelijke scenario's test, zullen we uw resultaten graag horen. Stuur ons uw feedback op aadapfeedback@microsoft.com .

Functies (gebeurtenis logboeken, Power shell en Extern bureaublad-services) in Windows-beheer centrum (WAC) of Extern bureaublad web client (HTML5) werken niet via Azure AD-toepassingsproxy.

## <a name="link-translation"></a>Conversie van koppelingen

### <a name="does-using-link-translation-affect-performance"></a>Is het gebruik van de koppelings conversie van invloed op de prestaties?

Ja. De koppelings omzetting is van invloed op de prestaties. De Application proxy-service scant de toepassing op vastgelegde koppelingen en vervangt deze door hun respectieve, gepubliceerde externe Url's voordat ze aan de gebruiker worden gepresenteerd. 

Voor de beste prestaties raden we u aan om identieke interne en externe Url's te gebruiken door [aangepaste domeinen](./application-proxy-configure-custom-domain.md)te configureren. Als het gebruik van aangepaste domeinen niet mogelijk is, kunt u de prestaties van de koppelings omzetting verbeteren met behulp van de module mijn apps beveiligde aanmelding of micro soft Edge-browser op mobiele apparaten. Zie [hardcoded koppelingen omleiden voor apps die zijn gepubliceerd met Azure AD-toepassingsproxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Jokertekens

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Hoe kan ik joker tekens gebruiken om twee toepassingen te publiceren met dezelfde aangepaste domein naam, maar met verschillende protocollen, één voor HTTP en één voor HTTPS?

Dit scenario wordt niet rechtstreeks ondersteund. De opties voor dit scenario zijn:

1. Publiceer zowel de HTTP-en HTTPS-Url's als afzonderlijke toepassingen met een Joker teken, maar geef elk een ander aangepast domein. Deze configuratie werkt, omdat deze verschillende externe URL'S hebben.

2. Publiceer de HTTPS-URL via een Joker toepassing. Publiceer de HTTP-toepassingen afzonderlijk met de volgende Power shell-cmdlets voor toepassings proxy:
   - [Toepassings proxy toepassings beheer](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [Application proxy-connector beheer](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
