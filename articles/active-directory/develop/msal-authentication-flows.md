---
title: MSAL-verificatie stromen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verificatie stromen en subsidies die worden gebruikt door de micro soft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 78932e5852453fe996e26a278f8a1859a8ecf546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755019"
---
# <a name="authentication-flows"></a>Verificatie stromen

De micro soft Authentication Library (MSAL) ondersteunt verschillende verificatie stromen voor gebruik in verschillende toepassings scenario's.

| Stroom | Beschrijving | Gebruikt in |
|--|--|--|
| [Autorisatiecode](#authorization-code) | Wordt gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Hiermee kunt u aanmelding en API-toegang toevoegen aan uw mobiele en desktop-apps. | [Bureau blad-apps](scenario-desktop-overview.md), [mobiele apps](scenario-mobile-overview.md), [Web-apps](scenario-web-app-call-api-overview.md) |
| [Client referenties](#client-credentials) | Hiermee krijgt u toegang tot webhoste bronnen met behulp van de identiteit van een toepassing. Wordt meestal gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker. | [Daemon-apps](scenario-daemon-overview.md) |
| [Apparaatcode](#device-code) | Hiermee kunnen gebruikers zich aanmelden op apparaten met invoer beperkingen, zoals een Smart TV, IoT-apparaat of printer. | [Desktop/mobiele apps](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Impliciete toekenning](#implicit-grant) | Hiermee kan de app tokens ophalen zonder dat er een back-end-server referentie-uitwisseling wordt uitgevoerd. Hiermee kunt u de app in staat stelt om zich aan te melden bij de gebruiker, de sessie te onderhouden en tokens te verkrijgen voor andere web-Api's, allemaal in de Java script-code van de client | [Toepassingen met één pagina (SPA)](scenario-spa-overview.md) |
| [Namens-van](#on-behalf-of) | Een toepassing roept een service of Web-API aan, die op zijn beurt een andere service of Web-API moet aanroepen. Het is verstandig om de gedelegeerde gebruikers identiteit en de machtigingen via de aanvraag keten door te geven. | [Web-API's](scenario-web-api-call-api-overview.md) |
| [Gebruikers naam/wacht woord](#usernamepassword) | Hiermee kan een toepassing zich aanmelden bij de gebruiker door rechtstreeks hun wacht woord af te handelen. Deze stroom wordt niet aanbevolen. | [Desktop/mobiele apps](scenario-desktop-acquire-token.md#username-and-password) |
| [Geïntegreerde Windows-verificatie](#integrated-windows-authentication) | Hiermee kunnen toepassingen die lid zijn van een domein of Azure Active Directory (Azure AD), een token op de achtergrond verkrijgen (zonder interactie van de gebruikers interface van de gebruiker). | [Desktop/mobiele apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitstraalt

Afhankelijk van hoe uw client toepassing is gebouwd, kunnen ze een of meer van de verificatie stromen gebruiken die worden ondersteund door het micro soft Identity-platform. Deze stromen kunnen verschillende typen tokens en autorisatie codes produceren, en vereisen verschillende tokens om ze te laten werken.

| Stroom                                                                               | Nodig            | id_token | toegangs token | token vernieuwen | autorisatie code |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Autorisatie code stroom](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Client referenties](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (alleen app) |               |                    |
| [Stroom voor apparaatcode](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Impliciete stroom](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Namens-stroom](v2-oauth2-on-behalf-of-flow.md)                                | toegangs token        | x        | x            | x             |                    |
| [Gebruikers naam/wacht woord](v2-oauth-ropc.md) (ROPC)                                       | gebruikers naam & wacht woord | x        | x            | x             |                    |
| [Hybride OIDC-stroom](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Aflossingen van token vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | token vernieuwen       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Interactieve en niet-interactieve verificatie

Verschillende van deze stromen bieden ondersteuning voor zowel interactieve als niet-interactieve token overname.

  - **Interactief** betekent dat de gebruiker wordt gevraagd om in te voeren. Bijvoorbeeld: de gebruiker wordt gevraagd om zich aan te melden, multi-factor Authentication (MFA) uit te voeren of extra toestemming te verlenen voor resources.
  - **Niet-interactieve** of *stille* verificatie pogingen om een token op een manier te verkrijgen waarop de aanmeldings server de gebruiker *niet kan* vragen om aanvullende informatie.

Uw MSAL-toepassing moet eerst proberen om een token op de *achtergrond* te verkrijgen en alleen interactief als de niet-interactieve methode mislukt. Zie voor meer informatie over dit patroon [tokens verkrijgen en cache gebruiken met behulp van de micro soft Authentication Library (MSAL)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Autorisatiecode

De [OAuth 2 autorisatie code Grant](v2-oauth2-auth-code-flow.md) kan worden gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-api's. Zo kunt u aanmelden en API-toegang toevoegen aan uw mobiele en desktop-apps.

Wanneer gebruikers zich aanmelden bij webtoepassingen (websites), ontvangt de webtoepassing een autorisatie code. De autorisatie code wordt ingewisseld om een token te verkrijgen voor het aanroepen van web-Api's.

![Diagram van autorisatie code stroom](media/msal-authentication-flows/authorization-code.png)

In het voor gaande diagram wordt de toepassing:

1. Vraagt een autorisatie code op die wordt ingewisseld voor een toegangs token.
2. Maakt gebruik van het toegangs token om een web-API aan te roepen.

### <a name="considerations"></a>Overwegingen

- U kunt de autorisatie code slechts eenmaal gebruiken voor het inwisselen van een token. Probeer niet meerdere keren een token te verkrijgen met dezelfde autorisatie code omdat het expliciet is verboden door de specificatie van het Protocol standaard. Als u de code verschillende keren verwisselt, hetzij opzettelijk, hetzij omdat u niet weet dat een framework dit ook voor u doet, krijgt u de volgende fout:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Clientreferenties

Met de [OAuth 2 client-gegevens stroom](v2-oauth2-client-creds-grant-flow.md) kunt u toegang krijgen tot webbronnen met behulp van de identiteit van een toepassing. Dit type toekenning wordt meestal gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker. Deze typen toepassingen worden vaak daemons of serviceaccounts genoemd.

De overdrachts stroom van client referenties staat een webservice (een vertrouwelijke client) toe om eigen referenties te gebruiken, in plaats van een gebruiker te imiteren, om te verifiëren bij het aanroepen van een andere webservice. In dit scenario is de client doorgaans een middelste laag, een daemon-service of een website. Voor een hoger garantie niveau kan het micro soft Identity-platform ook een certificaat (in plaats van een gedeeld geheim) als referentie gebruiken.

> [!NOTE]
> De vertrouwelijke client stroom is niet beschikbaar op mobiele platforms zoals UWP, Xamarin. iOS en Xamarin. Android, omdat deze alleen open bare client toepassingen ondersteunen. Voor open bare client toepassingen weet u niet hoe u de identiteit van de toepassing kunt bewijzen aan de ID-provider. Een beveiligde verbinding kan worden bereikt voor web-app of Web-API-back-ends door een certificaat te implementeren.

### <a name="application-secrets"></a>Toepassings geheimen

![Diagram van vertrouwelijke client met wacht woord](media/msal-authentication-flows/confidential-client-password.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token met behulp van toepassings geheim of wachtwoord referenties.
2. Gebruikt het token om aanvragen van de resource te maken.

### <a name="certificates"></a>Certificaten

![Diagram van vertrouwelijke client met certificaat](media/msal-authentication-flows/confidential-client-certificate.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token met behulp van certificaat referenties.
2. Gebruikt het token om aanvragen van de resource te maken.

Deze client referenties moeten:

- Geregistreerd bij Azure AD.
- Wordt door gegeven tijdens het samen stellen van het object voor de vertrouwelijke client toepassing in uw code.

## <a name="device-code"></a>Apparaatcode

Met de [code ring van het OAuth 2-apparaat](v2-oauth2-device-code.md) kunnen gebruikers zich aanmelden op apparaten met invoer beperkingen zoals Smart Tv's, IOT-apparaten en printers. Interactieve verificatie met Azure AD vereist een webbrowser. Als het apparaat of besturings systeem geen webbrowser heeft, kan de gebruiker met de code stroom van het apparaat een ander apparaat gebruiken, zoals een computer of mobiele telefoon om zich interactief aan te melden.

Door de apparaatcode stroom te gebruiken, haalt de toepassing tokens op via een proces met twee stappen dat is ontworpen voor deze apparaten en besturings systemen. Voor beelden van dergelijke toepassingen zijn de Program ma's die worden uitgevoerd op IoT-apparaten en de opdracht regel interface (CLI).

![Diagram van de code stroom van het apparaat](media/msal-authentication-flows/device-code.png)

In het bovenstaande diagram:

1. Wanneer gebruikers verificatie is vereist, biedt de app een code en wordt de gebruiker gevraagd een ander apparaat te gebruiken, zoals een smartphone met Internet verbinding om een URL te bezoeken (bijvoorbeeld `https://microsoft.com/devicelogin` ). De gebruiker wordt vervolgens gevraagd de code in te voeren en door te gaan met een normale verificatie-ervaring, inclusief toestemming vragen en [multi-factor Authentication](../authentication/concept-mfa-howitworks.md), indien nodig.
1. Wanneer de verificatie is geslaagd, ontvangt de opdracht regel-app de vereiste tokens via een back-upkanaal en worden ze gebruikt om de Web-API te laten aanroepen.

### <a name="constraints"></a>Beperkingen

- De code stroom van het apparaat is alleen beschikbaar in open bare client toepassingen.
- De door gegeven instantie bij het maken van de open bare client toepassing moet een van de volgende zijn:
  - Tenanted, in de vorm `https://login.microsoftonline.com/{tenant}/,` waar `{tenant}` is de GUID voor de Tenant-id of een domein naam die aan de Tenant is gekoppeld.
  - Voor werk-en school accounts in het formulier `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Impliciete toekenning

Met de impliciete toewijzings stroom [OAuth 2](v2-oauth2-implicit-grant-flow.md) kan de app tokens ophalen van het micro soft-identiteits platform zonder dat hiervoor een back-end-server referentie-uitwisseling wordt uitgevoerd. Met deze stroom kan de app zich aanmelden bij de gebruiker, een sessie onderhouden en tokens ophalen voor andere web-Api's, allemaal in de Java script-code van de client.

![Diagram van impliciete toekennings stroom](media/msal-authentication-flows/implicit-grant.svg)

Veel moderne webtoepassingen zijn gebouwd als client-side, single page-Applications (SPA) die zijn geschreven in Java script of een beveiligd-wachtwoord verificatie-Framework zoals hoek, Vue.js en React.js. Deze toepassingen worden uitgevoerd in een webbrowser en hebben verschillende verificatie kenmerken dan traditionele webtoepassingen aan de server zijde. Met het micro soft Identity-platform kunnen toepassingen met één pagina worden aangemeld en tokens worden verkregen voor toegang tot back-end-services of Web-Api's met behulp van de impliciete toekennings stroom. Met de impliciete stroom kan de toepassing ID-tokens ophalen om de geverifieerde gebruiker te vertegenwoordigen, en kunnen ook toegangs tokens worden verkregen die nodig zijn om beveiligde Api's aan te roepen.

Deze verificatie stroom bevat geen toepassings scenario's die gebruikmaken van platformoverschrijdende java script frameworks zoals elektro-en React-Native, omdat ze verdere mogelijkheden nodig hebben voor interactie met de systeem eigen platformen.

Tokens die via de modus impliciete stroom zijn uitgegeven, hebben een **beperkte lengte** , omdat ze worden geretourneerd naar de browser met behulp van een URL (waarbij `response_mode` `query` of `fragment` ). Sommige browsers beperken de lengte van de URL in de browser balk en mislukken wanneer deze te lang is. Deze impliciete stroom tokens bevatten daarom geen `groups` of `wids` claims.

## <a name="on-behalf-of"></a>Namens-van

De time-outstroom [OAuth 2-verificatie](v2-oauth2-on-behalf-of-flow.md) wordt gebruikt wanneer een toepassing een service of Web-API aanroept die op zijn beurt een andere service of Web-API moet aanroepen. Het is verstandig om de gedelegeerde gebruikers identiteit en de machtigingen via de aanvraag keten door te geven. Voor de middelste service om geverifieerde aanvragen voor de downstream-service te maken, moet een toegangs token worden beveiligd vanuit het micro soft Identity *-platform* namens de gebruiker.

![Diagram van namens-stroom](media/msal-authentication-flows/on-behalf-of.png)

In het bovenstaande diagram:

1. De toepassing verkrijgt een toegangs token voor de Web-API.
2. Een client (Web-, Desktop-, mobiele of toepassing met één pagina) roept een beveiligde web-API aan en voegt het toegangs token toe als Bearer-token in de verificatie header van de HTTP-aanvraag. De Web-API verifieert de gebruiker.
3. Wanneer de-client de Web-API aanroept, vraagt de Web-API een andere token namens de gebruiker aan.
4. De beveiligde web-API gebruikt dit token voor het aanroepen van een downstream Web-API namens de gebruiker. De Web-API kan ook later tokens aanvragen voor andere downstream-Api's (maar nog steeds namens dezelfde gebruiker).

## <a name="usernamepassword"></a>Gebruikersnaam en wachtwoord

Met de machtiging wacht woord voor [OAuth 2 resource owner](v2-oauth-ropc.md) (ROPC) kan een toepassing zich aanmelden bij de gebruiker door rechtstreeks hun wacht woord af te handelen. In uw bureaublad toepassing kunt u de stroom gebruikers naam/wacht woord gebruiken om een token op de achtergrond te verkrijgen. Er is geen gebruikers interface vereist bij het gebruik van de toepassing.

![Diagram van de gebruikers naam/wacht woord stroom](media/msal-authentication-flows/username-password.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token door de gebruikers naam en het wacht woord naar de ID-provider te verzenden.
2. Hiermee wordt een web-API aangeroepen met behulp van het token.

> [!WARNING]
> Deze stroom wordt niet aanbevolen. Er is een hoge mate van vertrouwen en referentie blootstelling vereist. U moet deze stroom *alleen* gebruiken wanneer er geen beveiligde stromen kunnen worden gebruikt. Zie [Wat is de oplossing voor het groeiende probleem van wacht woorden?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)voor meer informatie.

De voorkeurs stroom voor het op de achtergrond verkrijgen van een token op computers die lid zijn van een Windows-domein is [geïntegreerde Windows-verificatie](#integrated-windows-authentication). In andere gevallen gebruikt u de [code stroom](#device-code)van het apparaat.

Hoewel de gebruikers naam en het wacht woord stroom kunnen zijn in sommige scenario's zoals DevOps, moet u deze voor komen als u gebruikers naam/wacht woord wilt gebruiken in interactieve scenario's waarin u uw eigen gebruikers interface opgeeft.

Met gebruikers naam/wacht woord:

- Gebruikers die multi-factor Authentication moeten uitvoeren, kunnen zich niet aanmelden omdat er geen interactie is.
- Gebruikers kunnen eenmalige aanmelding niet uitvoeren.

### <a name="constraints"></a>Beperkingen

Naast de [beperkingen voor geïntegreerde Windows-verificatie](#integrated-windows-authentication)gelden de volgende beperkingen ook:

- De gebruikers naam/wacht woord stroom is niet compatibel met voorwaardelijke toegang en multi-factor Authentication. Als uw app wordt uitgevoerd in een Azure AD-Tenant waarvoor de Tenant beheerder multi-factor Authentication vereist, kunt u deze stroom niet gebruiken. Veel organisaties doen dat.
- ROPC werkt alleen voor werk-en school accounts. U kunt ROPC voor micro soft-accounts (MSA) niet gebruiken.
- De stroom is beschikbaar op .NET Desktop en .NET core, maar niet op Universeel Windows-platform.
- In Azure AD B2C werkt de ROPC-stroom alleen voor lokale accounts. Zie [using ROPC with Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)voor meer informatie over ROPC in MSAL.NET en Azure AD B2C.

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

MSAL biedt ondersteuning voor geïntegreerde Windows-verificatie (IWA) voor desktop-en Mobile-toepassingen die worden uitgevoerd op een Windows-computer die lid is van een domein of die is toegevoegd aan Azure AD. Met behulp van IWA kunnen deze toepassingen een token op de achtergrond verkrijgen zonder gebruikers interface-interactie door de gebruiker te vereisen.

![Diagram van geïntegreerde Windows-verificatie](media/msal-authentication-flows/integrated-windows-authentication.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token met behulp van geïntegreerde Windows-verificatie.
2. Gebruikt het token om aanvragen van de resource te maken.

### <a name="constraints"></a>Beperkingen

Geïntegreerde Windows-verificatie (IWA) ondersteunt *alleen* federatieve gebruikers: gebruikers die zijn gemaakt in Active Directory en worden ondersteund door Azure AD. Gebruikers die rechtstreeks in azure AD zijn gemaakt zonder Active Directory-back-ups (beheerde gebruikers), kunnen deze verificatie stroom niet gebruiken. Deze beperking heeft geen invloed op de [gebruikers naam en het wacht woord](#usernamepassword).

IWA is voor .NET Framework-, .NET core-en Universeel Windows-platform-toepassingen.

IWA niet overs Laan multi-factor Authentication. Als multi-factor Authentication is geconfigureerd, kan IWA mislukken als een multi-factor Authentication-Challenge vereist is. Multi-factor Authentication vereist gebruikers interactie.

U kunt niet bepalen wanneer de identiteits provider twee ledige verificatie aanvragen moet uitvoeren. De Tenant beheerder doet dat. Normaal gesp roken is twee ledige verificatie vereist wanneer u zich aanmeldt vanuit een ander land of een andere regio, wanneer u niet verbonden bent via VPN met een bedrijfs netwerk, en soms zelfs wanneer u verbinding hebt via VPN. Azure AD maakt gebruik van AI om voortdurend te leren wanneer twee ledige verificatie is vereist. Als IWA mislukt, moet u terugvallen op een [interactieve prompt](#interactive-and-non-interactive-authentication)van de gebruiker.

De door gegeven instantie bij het maken van de open bare client toepassing moet een van de volgende zijn:

- Tenanted, in de vorm `https://login.microsoftonline.com/{tenant}/,` waar `{tenant}` is de GUID voor de Tenant-id of een domein naam die aan de Tenant is gekoppeld.
- Voor elke werk-en school account ( `https://login.microsoftonline.com/organizations/` ). Micro soft Personal accounts (MSA) worden niet ondersteund. u kunt geen `/common` `/consumers` tenants gebruiken.

Omdat IWA een stille stroom is, moet een van de volgende voor waarden worden voldaan:

- De gebruiker van uw toepassing moet eerder hebben ingestemd om de toepassing te gebruiken.
- De Tenant beheerder moet eerder toestemming hebben gegeven voor alle gebruikers in de Tenant om de toepassing te gebruiken.

Dit betekent dat een van de volgende voor waarden waar is:

- Als ontwikkelaar hebt u de mogelijkheid **verleend** in de Azure portal voor uzelf te selecteren.
- Een Tenant beheerder heeft de **toestemming beheerder verlenen/intrekken voor {Tenant domein}** geselecteerd op het tabblad **API-machtigingen** van de app-registratie in de Azure Portal (Zie [machtigingen toevoegen voor toegang tot uw web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)).
- U hebt de mogelijkheid gegeven om gebruikers toestemming te geven voor de toepassing; Zie [toestemming van individuele gebruiker aanvragen](v2-permissions-and-consent.md#requesting-individual-user-consent).
- U hebt de mogelijkheid van de Tenant beheerder om toestemming te geven voor de toepassing. Zie [toestemming](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)van de beheerder.

De IWA-stroom is ingeschakeld voor .NET Desktop-, .NET core-en Windows Universal platform-apps.

Zie [v 2.0-machtigingen en-toestemming](v2-permissions-and-consent.md)voor meer informatie over toestemming.

## <a name="next-steps"></a>Volgende stappen

Nu u de verificatie stromen hebt gecontroleerd die worden ondersteund door de micro soft Authentication Library (MSAL), kunt u meer informatie vinden over het verkrijgen en opslaan van de tokens die in deze stromen worden gebruikt:

[Tokens verkrijgen en in de cache opslaan met behulp van de micro soft Authentication Library (MSAL)](msal-acquire-cache-tokens.md)
