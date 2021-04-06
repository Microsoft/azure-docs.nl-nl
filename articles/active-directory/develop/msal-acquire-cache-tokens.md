---
title: '&-cache-tokens ophalen met micro soft Authentication Library (MSAL) | Azure'
titleSuffix: Microsoft identity platform
description: Meer informatie over het verkrijgen en caching van tokens met behulp van MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755067"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Tokens verkrijgen en in de cache opslaan met behulp van de micro soft Authentication Library (MSAL)

Met [toegangs tokens](access-tokens.md) kunnen clients veilig Web-api's aanroepen die worden beveiligd door Azure. Er zijn verschillende manieren om een token op te halen met behulp van de micro soft Authentication Library (MSAL). Voor sommige gebruikers interactie is een webbrowser vereist, terwijl anderen geen gebruikers interactie nodig hebben. Over het algemeen is de methode die wordt gebruikt voor het verkrijgen van een token, afhankelijk van of de toepassing een open bare client toepassing is, zoals een bureau blad of een mobiele app, of een vertrouwelijke client toepassing, zoals web-app, Web-API of daemon-toepassing.

MSAL slaat een token op in de cache nadat het is verkregen. De code van uw toepassing moet eerst proberen een token op de achtergrond op te halen uit de cache voordat u een token op een andere manier probeert op te halen.

U kunt ook de token cache wissen. dit wordt bereikt door de accounts uit de cache te verwijderen. Hiermee wordt de sessie cookie in de browser echter niet verwijderd.

## <a name="scopes-when-acquiring-tokens"></a>Bereiken bij het ophalen van tokens

[Scopes](v2-permissions-and-consent.md) zijn de machtigingen die een web-API toont dat client toepassingen toegang kunnen vragen tot. Client toepassingen vragen de toestemming van de gebruiker voor deze bereiken bij het maken van verificatie aanvragen voor het verkrijgen van tokens voor toegang tot de Web-Api's. Met MSAL kunt u tokens ophalen om toegang te krijgen tot Azure AD voor ontwikkel aars (v 1.0) en de micro soft Identity platform-Api's. v 2.0-protocol gebruikt scopes in plaats van resource in de aanvragen. Lees de [vergelijking van v 1.0 en v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md)voor meer informatie. Op basis van de configuratie van de token die door de Web-API wordt geaccepteerd, retourneert het v 2.0-eind punt het toegangs token voor MSAL.

Diverse methoden voor het verkrijgen van tokens voor MSAL vereisen een `scopes` para meter. De `scopes` para meter is een lijst met teken reeksen die de gewenste machtigingen en de aangevraagde bronnen declareren. Bekende bereiken zijn de [Microsoft Graph machtigingen](/graph/permissions-reference).

Het is ook mogelijk in MSAL om toegang te krijgen tot v 1.0-resources. Zie [bereiken voor een v 1.0-toepassing](msal-v1-app-scopes.md)voor meer informatie.

### <a name="request-scopes-for-a-web-api"></a>Aanvraag bereiken voor een web-API

Wanneer uw toepassing een toegangs token moet aanvragen met specifieke machtigingen voor een resource-API, geeft u de scopes met de App-ID-URI van de API op in de indeling `<app ID URI>/<scope>` .

Enkele voor beelden van bereik waarden voor verschillende resources:

- Microsoft Graph-API: `https://graph.microsoft.com/User.Read`
- Aangepaste web-API: `api://11111111-1111-1111-1111-111111111111/api.read`

De indeling van de bereik waarde varieert, afhankelijk van de bron (de API) die het toegangs token ontvangt en de `aud` claim waarden die worden geaccepteerd.

Alleen voor Microsoft Graph kan de `user.read` scope worden toegewezen aan `https://graph.microsoft.com/User.Read` en beide bereik indelingen kunnen door elkaar worden gebruikt.

Bepaalde web-Api's, zoals de Azure Resource Manager-API, https://management.core.windows.net/) verwachten een afsluitende slash ('/') in de claim `aud` van de doel groep () van het toegangs token. In dit geval geeft u de scope op als `https://management.core.windows.net//user_impersonation` , met inbegrip van de dubbele slash (//).

Andere Api's kunnen vereisen dat *er geen schema of host* is opgenomen in de bereik waarde, en verwacht alleen de app-id (een GUID) en de naam van het bereik, bijvoorbeeld:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Als de downstream-resource zich niet onder uw besturings element bevindt, moet u mogelijk verschillende notaties voor bereik waarden proberen (bijvoorbeeld met/zonder schema en host) als u `401` of er andere fouten optreden bij het door geven van het toegangs token aan de resource.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dynamische bereiken aanvragen voor incrementele toestemming

Als de functies van uw toepassing of de vereisten veranderen, kunt u indien nodig aanvullende machtigingen aanvragen met behulp van de bereik parameter. Met dergelijke *dynamische bereiken* kunnen uw gebruikers incrementele toestemming geven aan scopes.

U kunt zich bijvoorbeeld aanmelden bij de gebruiker, maar de toegang tot resources wordt in eerste instantie geweigerd. Later kunt u hen de mogelijkheid geven om de agenda te bekijken door het kalender bereik aan te vragen bij de methode voor het verkrijgen van een token en de toestemming van de gebruiker te verkrijgen. Bijvoorbeeld door de-en-Scope aan te vragen `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` .

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tokens op de achtergrond verkrijgen (vanuit de cache)

MSAL onderhoudt een token cache (of twee caches voor vertrouwelijke client toepassingen) en slaat een token op in de cache nadat het is verkregen. In veel gevallen probeert het op de achtergrond ophalen van een token een andere token te verkrijgen met meer scopes op basis van een token in de cache. Het is ook mogelijk om een token te vernieuwen wanneer het bijna is verlopen (omdat de token cache ook een vernieuwings token bevat).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Aanbevolen aanroep patroon voor open bare client toepassingen

De toepassings code moet eerst proberen om een token op de achtergrond te verkrijgen vanuit de cache. Als de aanroep van de methode de fout of uitzonde ring ' gebruikers interface vereist ' retourneert, kunt u een token op een andere manier ophalen.

Er zijn echter twee stromen waarin u **niet moet** proberen een token op de achtergrond op te halen:

- [Client referenties flow](msal-authentication-flows.md#client-credentials), die geen gebruik maakt van de token cache van de gebruiker, maar een toepassings token cache. Deze methode zorgt ervoor dat de token cache van de toepassing wordt gecontroleerd voordat een aanvraag naar de Security Token Service (STS) wordt verzonden.
- [Autorisatie code stroom](msal-authentication-flows.md#authorization-code) in web-apps, omdat er een code wordt ingewisseld die de toepassing heeft verkregen door zich aan te melden bij de gebruiker en hen toestemming te geven voor meer scopes. Omdat een code en geen account als een para meter worden door gegeven, kan de methode niet in de cache zoeken voordat de code wordt ingewisseld, waardoor een aanroep naar de service wordt aangeroepen.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Aanbevolen aanroep patroon in web-apps met behulp van de autorisatie code stroom

Voor webtoepassingen die gebruikmaken van de [OpenID Connect Connect-autorisatie code stroom](v2-protocols-oidc.md), is het aanbevolen patroon in de controllers:

- Een vertrouwelijke client toepassing instantiëren met een token cache met aangepaste serialisatie.
- Het token verkrijgen met de autorisatie code stroom

## <a name="acquiring-tokens"></a>Tokens ophalen

Over het algemeen is de methode voor het verkrijgen van een token afhankelijk van of het een open bare client of een vertrouwelijke client toepassing is.

### <a name="public-client-applications"></a>Open bare client toepassingen

Voor open bare client toepassingen (desktop of mobiele app) gaat u als volgt te werk:

- Vaak tokens interactief verkrijgen, waarbij de gebruiker zich aanmeldt via een gebruikers interface of pop-upvenster.
- Kan [een token op de achtergrond ontvangen voor de aangemelde gebruiker](msal-authentication-flows.md#integrated-windows-authentication) die gebruikmaakt van geïntegreerde Windows-authenticatie (IWA/Kerberos) als de bureaublad toepassing wordt uitgevoerd op een Windows-computer die is toegevoegd aan een domein of aan Azure.
- Kan [een token verkrijgen met een gebruikers naam en wacht woord](msal-authentication-flows.md#usernamepassword) in .NET Framework desktop client-toepassingen (niet aanbevolen). Gebruik geen gebruikers naam/wacht woord in vertrouwelijke client toepassingen.
- Kan een token verkrijgen via de [code stroom](msal-authentication-flows.md#device-code) van het apparaat in toepassingen die worden uitgevoerd op apparaten die geen webbrowser hebben. De gebruiker wordt voorzien van een URL en een code, die vervolgens naar een webbrowser op een ander apparaat gaat en de code invoert en zich aanmeldt. Azure AD stuurt vervolgens een token terug naar het browser-less-apparaat.

### <a name="confidential-client-applications"></a>Vertrouwelijke client toepassingen

Voor vertrouwelijke client toepassingen (Web-app, Web-API of een daemon-toepassing, zoals een Windows-service), doet u het volgende:

- Tokens ophalen **voor de toepassing zelf** en niet voor een gebruiker, met behulp van de [client referenties stroom](msal-authentication-flows.md#client-credentials). Deze techniek kan worden gebruikt voor het synchroniseren van hulpprogram ma's of hulpprogram ma's die gebruikers in het algemeen en niet een specifieke gebruiker verwerken.
- Gebruik de [namens-stroom](msal-authentication-flows.md#on-behalf-of) voor een web-API om een API namens de gebruiker aan te roepen. De toepassing wordt aangeduid met client referenties om een token te verkrijgen op basis van een gebruikers bevestiging (SAML, bijvoorbeeld of een JWT-token). Deze stroom wordt gebruikt door toepassingen die toegang moeten hebben tot bronnen van een bepaalde gebruiker in service-to-service-aanroepen.
- Tokens verkrijgen met de [autorisatie code stroom](msal-authentication-flows.md#authorization-code) in web apps nadat de gebruiker zich heeft aangemeld via de URL van de verificatie aanvraag. OpenID Connect Connect Application gebruikt dit mechanisme doorgaans, waarmee de gebruiker zich kan aanmelden met open ID Connect en vervolgens toegang tot Web-Api's namens de gebruiker kan krijgen.

## <a name="authentication-results"></a>Verificatie resultaten

Als uw client een toegangs token aanvraagt, retourneert Azure AD ook een verificatie resultaat dat meta gegevens over het toegangs token bevat. Deze informatie omvat de verloop tijd van het toegangs token en de scopes waarvoor deze geldig is. Met deze gegevens kan uw app intelligente caching van toegangs tokens uitvoeren zonder dat het toegangs token zelf moet worden geparseerd. Het verificatie resultaat is beschikbaar:

- Het [toegangs token](access-tokens.md) voor de Web-API voor toegang tot bronnen. Deze teken reeks is doorgaans een met base64 gecodeerde JWT, maar de client mag nooit in het toegangs token kijken. De indeling is niet gegarandeerd stabiel en kan worden versleuteld voor de resource. Personen die code schrijven, afhankelijk van de inhoud van het toegangs token op de client, is een van de meest voorkomende oorzaken van fouten en storingen in client logica.
- Het [id-token](id-tokens.md) voor de gebruiker (een JWT).
- Het verval van het token, waarmee de datum/tijd wordt aangegeven waarop het token verloopt.
- De Tenant-ID bevat de Tenant waarin de gebruiker is gevonden. Voor gast gebruikers (Azure AD B2B-scenario's) is de Tenant-ID de gast-Tenant, niet de unieke Tenant. Wanneer het token wordt geleverd met de naam van een gebruiker, bevat het verificatie resultaat ook informatie over deze gebruiker. Voor vertrouwelijke client stromen waarbij tokens worden aangevraagd zonder gebruiker (voor de toepassing), is deze gebruikers informatie null.
- De bereiken waarvoor het token is uitgegeven.
- De unieke ID voor de gebruiker.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Gevanceerde Toegang krijgen tot de tokens in de cache van de gebruiker in de achtergrond-apps en-services

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Volgende stappen

Diverse platformen die door MSAL worden ondersteund, hebben extra informatie over token cache in de documentatie voor de bibliotheek van dat platform. Bijvoorbeeld:
- [Een Token ophalen uit de token cache met behulp van MSAL.NET](msal-net-acquire-token-silently.md)
- [Eenmalige aanmelding met MSAL.js](msal-js-sso.md)
- [Serialisatie van aangepaste token cache in MSAL voor python](msal-python-token-cache-serialization.md)
- [Aangepaste serialisatie van token cache in MSAL voor Java](msal-java-token-cache-serialization.md)
