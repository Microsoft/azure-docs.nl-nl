---
title: Opties voor eenmalige aanmelding in Azure AD
description: Meer informatie over de beschikbare opties voor eenmalige aanmelding (SSO) in Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: 2bf84a22a384e6079c2d85c833b34ba37eecaa46
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252352"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Opties voor eenmalige aanmelding in Azure AD

Eenmalige aanmelding biedt veel voordelen ten opzichte van traditionele aanmeldingsmethoden.

- **Bij eenmalige aanmelding** melden gebruikers zich eenmaal aan met één account om toegang te krijgen tot apparaten die lid zijn van een domein, bedrijfsresources, SaaS-toepassingen (Software as a Service) en web-toepassingen. Nadat de gebruiker zich heeft aangemeld, kan deze toepassingen starten vanuit de Office 365-portal of MyApps. Beheerders kunnen het beheer van gebruikersaccounts centraal regelen en automatisch gebruikerstoegang tot toepassingen toevoegen of verwijderen op basis van groepslidmaatschap.

- **Zonder eenmalige aanmelding** moeten gebruikers toepassingsspecifieke wachtwoorden onthouden en zich bij elke toepassing aanmelden. IT-medewerkers moeten gebruikersaccounts maken en bijwerken voor elke toepassing, zoals Microsoft 365, Box en Salesforce. Gebruikers moeten hun wachtwoorden onthouden en zijn meer tijd kwijt omdat ze zich bij elke toepassing moeten aanmelden.

Zie [Wat is eenmalige aanmelding?](what-is-single-sign-on.md) voor meer informatie over eenmalige aanmelding.

## <a name="choosing-a-single-sign-on-method"></a>Een methode voor eenmalige aanmelding kiezen

Er zijn verschillende manieren om een toepassing te configureren voor eenmalige aanmelding. Het kiezen van een methode voor eenmalige aanmelding is afhankelijk van de wijze waarop de toepassing is geconfigureerd voor verificatie.

- Cloud-toepassingen kunnen gebruikmaken van de volgende methoden voor eenmalige aanmelding: OpenID Connect, OAuth, SAML, op een wachtwoord gebaseerd, gekoppeld of uitgeschakeld. 
- On-premises toepassingen kunnen gebruikmaken van de volgende methoden voor eenmalige aanmelding: op een wachtwoord gebaseerd, geïntegreerde Windows-verificatie, op headers gebaseerd, gekoppeld of uitgeschakeld. De on-premises opties kunnen worden toegepast wanneer toepassingen zijn geconfigureerd voor Application Proxy.

U kunt aan de hand van dit stroomdiagram bepalen welke methode voor eenmalige aanmelding het geschiktst is voor uw situatie.

![Beslissingsstroomdiagram voor de methode voor eenmalige aanmelding](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

In de volgende tabel vindt u een overzicht van de methoden voor eenmalige aanmelding en koppelingen voor meer informatie.

| Methode voor eenmalige aanmelding | Toepassingstypen | Wanneer gebruikt u dit? |
| :------ | :------- | :----- |
| [OpenID Connect en OAuth](#openid-connect-and-oauth) | cloud en on-premises | Gebruik OpenID Connect en OAuth bij het ontwikkelen van een nieuwe toepassing. Dit protocol vereenvoudigt de configuratie van toepassingen, biedt gebruiksvriendelijke SDK's en stelt uw toepassing in staat om MS Graph te gebruiken.
| [SAML](#saml-sso) | cloud en on-premises | Kies indien mogelijk SAML voor bestaande toepassingen die niet gebruikmaken van OpenID Connect of OAuth. SAML kan worden gebruikt voor toepassingen waarbij de verificatie wordt uitgevoerd aan de hand van een van de SAML-protocollen.|
| [Op basis van een wachtwoord](#password-based-sso) | cloud en on-premises | Kies voor eenmalige aanmelding op basis van een wachtwoord wanneer de verificatie voor de toepassing wordt uitgevoerd met een gebruikersnaam en wachtwoord. Bij eenmalige aanmelding op basis van een wachtwoord is beveiligde toepassingswachtwoordopslag en -herhaling mogelijk door middel van een webbrowserextensie of mobiele app. Deze methode maakt gebruik van de bestaande aanmeldingsprocedure van de toepassing, maar stelt een beheerder in staat om de wachtwoorden te beheren. |
| [Gekoppeld](#linked-sign-on) | cloud en on-premises | Kies voor een gekoppelde aanmelding wanneer de toepassing is geconfigureerd voor eenmalige aanmelding in een andere id-providerservice. Bij deze optie wordt geen eenmalige aanmelding toegevoegd aan de toepassing. Voor de toepassing is echter mogelijk al een eenmalige aanmelding geïmplementeerd met behulp van een andere service, zoals Active Directory Federation Services.|
| [Uitgeschakeld](#disabled-sso) | cloud en on-premises | Kies voor een uitgeschakelde eenmalige aanmelding wanneer de app niet gereed is om te worden geconfigureerd voor eenmalige aanmelding. Deze modus is de standaardinstelling bij het maken van de app.|
| [Geïntegreerde Windows-verificatie](#integrated-windows-authentication-iwa-sso) | alleen on-premises | Kies eenmalige aanmelding met geïntegreerde Windows-verificatie voor toepassingen die gebruikmaken van [geïntegreerde Windows-verificatie](/aspnet/web-api/overview/security/integrated-windows-authentication) of claimbewuste toepassingen. Voor geïntegreerde Windows-verificatie maken de Application Proxy-connectors gebruik van beperkte Kerberos-delegering om gebruikers te verifiëren voor de toepassing. |
| [Op basis van headers](#header-based-sso) | alleen on-premises | Gebruik eenmalige aanmelding op basis van headers wanneer de toepassing headers gebruikt voor verificatie. Application Proxy gebruikt Azure AD om de gebruiker te verifiëren en geeft vervolgens het verkeer door via de connectorservice.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect en OAuth

Gebruik bij het ontwikkelen van nieuwe toepassingen moderne protocollen, zoals OpenID Connect en OAuth, om de beste eenmalige aanmelding voor uw app op meerdere platformen te realiseren. Met OAuth kunnen gebruikers of beheerders [toestemming geven](configure-user-consent.md) voor beveiligde resources, zoals [Microsoft Graph](/graph/overview). We bieden [SDK's](../develop/reference-v2-libraries.md) voor uw app die eenvoudig kunnen worden aangepast en daarnaast kan [Microsoft Graph](/graph/overview) worden gebruikt door uw app.

Zie voor meer informatie:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Handleiding voor ontwikkelaars van Microsoft Identity Platform](../develop/index.yml).

## <a name="saml-sso"></a>Eenmalige aanmelding op basis van SAML

Met **eenmalige aanmelding op basis van SAML** voert Azure AD een verificatie voor de toepassing uit met behulp van het Azure AD-account van de gebruiker. Azure AD geeft de aanmeldingsgegevens door aan de toepassing via een verbindingsprotocol. Met eenmalige aanmelding op basis van SAML kunt u gebruikers toewijzen aan specifieke toepassingsrollen op basis van de regels die u in uw SAML-claims definieert.

Kies voor eenmalige aanmelding op basis van SAML wanneer de toepassing dit ondersteunt.

Eenmalige aanmelding op basis van SAML wordt ondersteund voor toepassingen die gebruikmaken van de volgende protocollen:

- SAML 2.0
- Webservices-federatie

Zie [Eenmalige aanmelding op basis van SAML configureren](configure-saml-single-sign-on.md) als u een SaaS-toepassing wilt configureren voor eenmalige aanmelding op basis van SAML. Daarnaast bestaat er voor veel SaaS-toepassingen (Software as a Service) een [toepassingsspecifieke zelfstudie](../saas-apps/tutorial-list.md) waarin de configuratie voor eenmalige aanmelding op basis van SAML stapsgewijs wordt beschreven.

Als u een toepassing voor WS-Federation wilt configureren, volgt u dezelfde richtlijnen om de toepassing te configureren voor eenmalige aanmelding op basis van SAML. In de stap waarin de toepassing wordt geconfigureerd voor gebruik van Azure AD, moet u de aanmeldings-URL van Azure AD voor het eindpunt van WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed` vervangen.

Raadpleeg [Eenmalige aanmelding op basis van SAML voor on-premises toepassingen met Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md) als u een on-premises toepassing wilt configureren voor eenmalige aanmelding op basis van SAML.

Zie [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md) voor meer informatie over het SAML-protocol.

## <a name="password-based-sso"></a>Eenmalige aanmelding op basis van een wachtwoord

Bij aanmelding op basis van een wachtwoord melden gebruikers zich aan bij de toepassing met een gebruikersnaam en wachtwoord wanneer ze deze voor de eerste keer gaan gebruiken. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord voor de toepassing.

Eenmalige aanmelding op basis van een wachtwoord maakt gebruik van de bestaande verificatieprocedure die door de toepassing wordt geboden. Wanneer u eenmalige aanmelding op basis van een wachtwoord inschakelt voor een toepassing, worden gebruikersnamen en wachtwoorden voor de toepassing door Azure AD verzameld en beveiligd. Gebruikersreferenties worden versleuteld opgeslagen in de map.

Kies voor eenmalige aanmelding op basis van wachtwoord wanneer:

- Een toepassing geen ondersteuning biedt voor het protocol van eenmalige aanmelding op basis van SAML.
- Verificatie voor een toepassing wordt uitgevoerd met een gebruikersnaam en wachtwoord in plaats van toegangstokens en headers.

>[!NOTE]
>U geen beleid voor voorwaardelijke toegang of meervoudige verificatie kunt toepassen voor eenmalige aanmelding op basis van een wachtwoord.

Eenmalige aanmelding op basis van een wachtwoord wordt ondersteund voor alle cloud-toepassingen die een HTML-aanmeldingspagina hebben. De gebruiker kan een van de volgende browsers gebruiken:

- Internet Explorer 11 in Windows 7 of hoger
   > [!NOTE]
   > Internet Explorer heeft beperkte ondersteuning en ontvangt geen nieuwe software-updates meer. Microsoft Edge is de aanbevolen browser.

- Microsoft Edge in Windows 10 Anniversary Edition of hoger
- Microsoft Edge voor iOS en Android
- Intune Managed Browser
- Chrome in Windows 7 of hoger en in macOS X of hoger
- Firefox 26.0 of hoger in Windows XP SP2 of hoger en in macOS X 10.6 of hoger

Zie [Eenmalige aanmelding op basis van een wachtwoord configureren](configure-password-single-sign-on-non-gallery-applications.md) als u een cloudtoepassing wilt configureren voor eenmalige aanmelding op basis van een wachtwoord.

Raadpleeg [Wachtwoordkluis voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-password-vaulting.md) als u een on-premises toepassing wilt configureren voor eenmalige aanmelding via Application Proxy

### <a name="how-authentication-works-for-password-based-sso"></a>Hoe verificatie wordt toegepast voor eenmalige aanmelding op basis van een wachtwoord

Als u een gebruiker wilt verifiëren voor een toepassing, haalt Azure AD de referenties van de gebruiker op uit de directory en voert deze in op de aanmeldingspagina van de toepassing.  Azure AD geeft de gebruikersreferenties veilig door via een webbrowserextensie of een mobiele app. Op deze manier kan een beheerder gebruikersreferenties beheren en hoeven gebruikers hun wachtwoord niet te onthouden.

> [!IMPORTANT]
> De referenties worden niet weergegeven voor de gebruiker tijdens de geautomatiseerde aanmeldingsprocedure. De referenties kunnen echter wel worden weergegeven met behulp van webfoutopsporingsprogramma's. Gebruikers en beheerders moeten hetzelfde beveiligingsbeleid volgen alsof referenties rechtstreeks door de gebruiker zijn ingevoerd.

### <a name="managing-credentials-for-password-based-sso"></a>Referenties voor eenmalige aanmelding op basis van een wachtwoord beheren

Wachtwoorden voor elke toepassing kunnen worden beheerd door de Azure AD-beheerder of door de gebruikers.

Wanneer de Azure AD-beheerder de referenties beheert, is het volgende van toepassing:  

- De gebruiker hoeft de gebruikersnaam en het wachtwoord niet opnieuw in te stellen of te onthouden. De gebruiker kan toegang krijgen tot de toepassing door erop te klikken in MyApps of via een geleverde koppeling.
- De beheerder kan beheertaken uitvoeren voor de referenties. De beheerder kan bijvoorbeeld de toegang tot de toepassing bijwerken op basis van lidmaatschappen van gebruikersgroepen en de status van de werknemers.
- De beheerder kan beheerdersreferenties gebruiken om toegang te bieden tot toepassingen die worden gedeeld door veel gebruikers. De beheerder kan bijvoorbeeld toestaan dat iedereen die toegang heeft tot een toepassing toegang heeft tot een socialemediatoepassing of een toepassing voor het delen van documenten.

Wanneer de eindgebruiker de referenties beheert, is het volgende van toepassing:

- Gebruikers kunnen hun wachtwoorden beheren door deze naar behoefte bij te werken of te verwijderen.
- Beheerders kunnen nog steeds nieuwe referenties voor de toepassing instellen.

## <a name="linked-sign-on"></a>Gekoppelde aanmelding
Met gekoppelde aanmelding kan Azure AD eenmalige aanmelding bieden voor een toepassing die al is geconfigureerd voor eenmalige aanmelding in een andere service. De gekoppelde toepassing kan worden weergegeven voor eindgebruikers in de Office 365-portal of MyApps-portal van Azure AD. Een gebruiker kan bijvoorbeeld een toepassing starten die is geconfigureerd voor eenmalige aanmelding in Active Directory Federation Services 2.0 (AD FS) van de Office 365-portal. Aanvullende rapportage is ook beschikbaar voor gekoppelde toepassingen die worden gestart vanuit de Office 365-portal of de MyApps-portal van Azure AD. Zie [Gekoppelde aanmelding configureren](configure-linked-sign-on.md) als u voor een toepassing gekoppelde aanmelding wilt configureren.

### <a name="linked-sign-on-for-application-migration"></a>Gekoppelde aanmelding voor de migratie van toepassingen

Een gekoppelde aanmelding kan een consistente gebruikerstoepassing bieden tijdens het migreren van toepassingen gedurende een bepaalde periode. Als u toepassingen migreert naar Azure Active Directory, kunt u een gekoppelde aanmelding gebruiken om snel koppelingen te publiceren naar alle toepassingen die u wilt migreren.  Gebruikers kunnen alle koppelingen vinden in de [MyApps-portal](../user-help/my-apps-portal-end-user-access.md) of het [programma voor het starten van Microsoft 365-toepassingen](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Gebruikers weten niet dat ze toegang hebben tot een gekoppelde toepassing of een gemigreerde toepassing.  

Wanneer een gebruiker eenmaal is geverifieerd met een gekoppelde toepassing, moet er een accountrecord worden gemaakt voordat de eindgebruiker toegang via eenmalige aanmelding wordt geboden. Het inrichten van deze accountrecord kan automatisch plaatsvinden of kan handmatig door een beheerder worden uitgevoerd.

>[!NOTE]
>U kunt geen beleid voor voorwaardelijke toegang of meervoudige verificatie toepassen op een gekoppelde toepassing. Dit komt doordat een gekoppelde toepassing geen functionaliteit voor eenmalige aanmelding biedt via Azure AD. Wanneer u een gekoppelde toepassing configureert, voegt u simpelweg een koppeling toe die wordt weergegeven in het programma voor het starten van toepassingen of in de MyApps-portal. 

## <a name="disabled-sso"></a>Uitgeschakelde eenmalige aanmelding

In de modus Uitgeschakeld wordt eenmalige aanmelding niet gebruikt voor de app. Wanneer eenmalige aanmelding is uitgeschakeld, moeten gebruikers mogelijk twee keer worden geverifieerd. Eerst moeten gebruikers zich verifiëren bij Azure AD en vervolgens melden ze zich aan bij de toepassing.

Pas de modus voor uitgeschakelde eenmalige aanmelding toe in de volgende situaties:

- Als u deze toepassing nog niet kunt integreren met de eenmalige aanmelding van Azure AD, of
- Als u andere aspecten van de toepassing test, of
- Als een beveiligingslaag voor een on-premises toepassing waarvoor gebruikers zich niet hoeven te verifiëren. In de modus Uitgeschakeld moet de gebruiker zich verifiëren.

Houd er rekening mee dat als u de toepassing hebt geconfigureerd voor eenmalige aanmelding op basis van SAML die door de serviceprovider wordt gestart en u de modus voor eenmalige aanmelding wijzigt in Uitgeschakeld, gebruikers zich nog steeds buiten de MyApps-portal kunnen aanmelden bij de toepassing. Als u dit wilt bereiken, moet u [de mogelijkheid uitschakelen voor gebruikers om zich aan te melden](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Eenmalige aanmelding met geïntegreerde Windows-verificatie

[Application Proxy](application-proxy.md) biedt eenmalige aanmelding voor toepassingen die gebruikmaken van [geïntegreerde Windows-verificatie](/aspnet/web-api/overview/security/integrated-windows-authentication) of claimbewuste toepassingen. Als uw toepassing gebruikmaakt van geïntegreerde Windows-verificatie, voert Application Proxy een verificatie uit voor de toepassing met behulp van beperkte Kerberos-delegatie. Voor een claimbewuste toepassing die Azure Active Directory vertrouwt, kan eenmalige aanmelding worden gebruikt omdat de gebruiker al is geverifieerd door Azure AD.

Kies voor de modus voor eenmalige aanmelding met geïntegreerde Windows-verificatie als u eenmalige aanmelding wilt gebruiken voor een on-premises toepassing die wordt geverifieerd met geïntegreerde Windows-verificatie.

Raadpleeg [Beperkte Kerberos-delegering voor eenmalige aanmelding bij uw toepassingen met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) als u een on-premises toepassing wilt configureren voor geïntegreerde Windows-verificatie.

### <a name="how-single-sign-on-with-kcd-works"></a>Hoe eenmalige aanmelding met beperkte Kerberos-delegering werkt
In dit diagram wordt de stroom weergegeven voor een gebruiker die toegang wil krijgen tot een on-premises toepassing die gebruikmaakt van geïntegreerde Windows-verificatie.

![Stroomdiagram voor Microsoft Azure AD-verificatie](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. De gebruiker voert de URL in om toegang te krijgen tot de on-premises toepassing via Application Proxy.
1. Application Proxy leidt de aanvraag om naar Azure AD-verificatieservices om vooraf een verificatie uit te voeren. Op dit punt past Azure AD de betreffende verificatie en het relevante verificatiebeleid, zoals meervoudige verificatie, toe. Als de gebruiker is gevalideerd, wordt door Azure AD een token gemaakt en verzonden naar de gebruiker.
1. De gebruiker geeft het token door aan Application Proxy.
1. Application Proxy valideert het token en haalt de user principal name (UPN) op uit het token. Vervolgens worden de aanvraag, de UPN en de SPN-naam (Service Principal Name) verzonden naar de connector via een dubbel geverifieerd beveiligd kanaal.
1. De connector maakt gebruik van onderhandeling voor beperkte Kerberos-delegering met de on-premises AD en imiteert de gebruiker om een Kerberos-token voor de toepassing te krijgen.
1. Active Directory verzendt het Kerberos-token voor de toepassing naar de connector.
1. De connector verzendt de oorspronkelijke aanvraag naar de toepassingsserver met behulp van het Kerberos-token dat is ontvangen van AD.
1. De toepassing stuurt het antwoord naar de connector en dit wordt vervolgens teruggestuurd naar de Application Proxy-service en uiteindelijk naar de gebruiker.

## <a name="header-based-sso"></a>Eenmalige aanmelding op basis van headers

Eenmalige aanmelding op basis van headers kan worden gebruikt voor toepassingen die HTTP-headers gebruiken voor de verificatie.

Kies voor een eenmalige aanmelding op basis van headers wanneer Application Proxy zijn geconfigureerd voor de on-premises toepassing.

Raadpleeg [Op header gebaseerde SSO](application-proxy-configure-single-sign-on-with-headers.md) voor meer informatie over op header gebaseerde verificatie.


## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Een implementatie van eenmalige aanmelding plannen](plan-sso-deployment.md)
* [Eenmalige aanmelding bij on-premises apps](application-proxy-config-sso-how-to.md)