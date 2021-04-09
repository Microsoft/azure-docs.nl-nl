---
title: Toepassings typen voor het micro soft-identiteits platform | Azure
description: De typen apps en scenario's die worden ondersteund door het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: 7ec309f016e73642262399bd75e7b5146bc5e497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752786"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Toepassings typen voor het micro soft Identity-platform

Het micro soft Identity-platform ondersteunt verificatie voor een verscheidenheid aan moderne app-architecturen, die allemaal zijn gebaseerd op gestandaardiseerde protocollen [OAuth 2,0 of OpenID Connect Connect](active-directory-v2-protocols.md). In dit artikel worden de typen apps beschreven die u kunt bouwen met behulp van micro soft Identity platform, ongeacht uw voorkeurs taal of-platform. De informatie is ontworpen om u te helpen bij het begrijpen van scenario's met een hoog niveau voordat u aan de slag gaat met de code in de [toepassings scenario's](authentication-flows-app-scenarios.md#application-scenarios).

## <a name="the-basics"></a>De basisbeginselen

U moet elke app registreren die gebruikmaakt van het micro soft-identiteits platform in de Azure Portal [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908). Het registratie proces van de app verzamelt en wijst deze waarden voor uw app toe:

* Een **toepassings-id (client)** waarmee uw app op unieke wijze wordt geïdentificeerd
* Een **omleidings-URI** die u kunt gebruiken om antwoorden terug te sturen naar uw app
* Een paar andere scenario-specifieke waarden, zoals ondersteunde account typen

Meer informatie over hoe u [een app kunt registreren](quickstart-register-app.md).

Nadat de app is geregistreerd, communiceert de app met het micro soft-identiteits platform door aanvragen naar het eind punt te verzenden. We bieden open-source frameworks en bibliotheken die de details van deze aanvragen verwerken. U hebt ook de optie om de verificatie logica zelf te implementeren door aanvragen voor deze eind punten te maken:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Apps met één pagina (Java script)

Veel moderne apps hebben een front-end van de app met één pagina, voornamelijk geschreven in Java script, vaak met een kader als een hoek, reageren of Vue. Het micro soft Identity-platform ondersteunt deze apps door gebruik te maken van het [OpenID Connect Connect](v2-protocols-oidc.md) -protocol voor verificatie en ofwel [OAuth 2,0 impliciet Grant flow](v2-oauth2-implicit-grant-flow.md) of de recentere [OAuth 2,0-autorisatie code + PKCE-stroom](v2-oauth2-auth-code-flow.md) voor autorisatie (zie hieronder).

In het onderstaande diagram ziet u de OAuth 2,0-autorisatie code toekenning (met details over PKCE wegge laten), waarbij de app een code van het micro soft Identity platform- `authorize` eind punt ontvangt en deze opnieuw gebruikt voor tokens en tokens vernieuwt met behulp van cross-site webaanvragen. Het vernieuwings token verloopt elke 24 uur en de app moet een andere code aanvragen. Naast het toegangs token wordt een van `id_token` de aangemelde gebruiker voor de client toepassing doorgaans ook aangevraagd via dezelfde stroom en/of een afzonderlijke OpenID Connect-verbindings aanvraag (hier niet weer gegeven).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram van de OAuth 2-autorisatie code stroom tussen een app met één pagina en het eind punt van het beveiligings token service." border="false":::

Als u dit scenario in actie wilt zien, raadpleegt u de [zelf studie: Meld u aan bij gebruikers en roep de Microsoft Graph-API aan vanuit een Java script Spa met behulp van auth code flow](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Autorisatie code stroom versus impliciete stroom

Voor de meeste van de geschiedenis van OAuth 2,0 was de [impliciete stroom](v2-oauth2-implicit-grant-flow.md) de aanbevolen manier om apps met één pagina te bouwen. Als u [cookies](reference-third-party-cookies-spas.md) van derden verwijdert en [meer aandacht](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) besteed aan beveiligings problemen rondom de impliciete stroom, zijn we verplaatst naar de autorisatie code stroom voor apps met één pagina.

Om ervoor te zorgen dat uw app in Safari en andere privacy bewuste browsers wordt ondersteund, wordt het gebruik van de impliciete stroom niet langer aangeraden en wordt de autorisatie code stroom echter wel aanbevolen.

## <a name="web-apps"></a>Web-apps

Voor web-apps (.NET, PHP, Java, Ruby, Python, node) die de gebruiker via een browser opent, kunt u [OpenID Connect Connect](active-directory-v2-protocols.md) gebruiken voor aanmelding door gebruikers. In OpenID Connect Connect wordt de web-app een ID-token ontvangen. Een ID-token is een beveiligings token waarmee de identiteit van de gebruiker wordt geverifieerd en informatie wordt verstrekt over de gebruiker in de vorm van claims:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Meer informatie over de verschillende typen tokens die worden gebruikt in het micro soft Identity platform zijn beschikbaar in de naslag informatie voor het [toegangs token](access-tokens.md) en het [id_token](id-tokens.md)

In web server-apps neemt de aanmeldings verificatie stroom de volgende stappen op hoog niveau:

![Toont de verificatie stroom van de web-app](./media/v2-app-types/convergence-scenarios-webapp.svg)

U kunt de identiteit van de gebruiker controleren door de ID-token te valideren met een open bare handtekening sleutel die wordt ontvangen van het micro soft Identity-platform. Er wordt een sessie cookie ingesteld die kan worden gebruikt om de gebruiker te identificeren op volgende pagina-aanvragen.

Als u dit scenario in actie wilt zien, probeert u de code voorbeelden in de web-app uit te proberen in het [scenario voor gebruikers](scenario-web-app-sign-user-overview.md).

Naast het gebruik van een eenvoudige aanmelding moet een webserver-app mogelijk toegang hebben tot een andere webservice, zoals een REST API. In dit geval maakt de webserver-app deel uit van een gecombineerde OpenID connect-verbinding en OAuth 2,0-stroom met behulp van de [OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md). Meer informatie over dit scenario vindt u [in aan de slag met web apps en Web-api's](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).

## <a name="web-apis"></a>Web-API's

U kunt het micro soft-identiteits platform gebruiken voor het beveiligen van webservices, zoals de REST Web API van uw app. Web-Api's kunnen worden geïmplementeerd in talloze platforms en talen. Ze kunnen ook worden geïmplementeerd met behulp van HTTP-triggers in Azure Functions. In plaats van ID-tokens en sessie cookies gebruikt een web-API een OAuth 2,0-toegangs token om de gegevens te beveiligen en inkomende aanvragen te verifiëren. De aanroeper van een web-API voegt een toegangs token toe in de autorisatie-header van een HTTP-aanvraag, zoals:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

De Web-API gebruikt het toegangs token om de identiteit van de API-aanroeper te controleren en om informatie over de aanroeper op te halen uit claims die zijn gecodeerd in het toegangs token. Meer informatie over de verschillende typen tokens die worden gebruikt in het micro soft Identity platform zijn beschikbaar in de naslag informatie voor het [toegangs token](access-tokens.md) en het [id_token](id-tokens.md) .

Een web-API kan gebruikers de bevoegdheid geven om zich aan te melden of om specifieke functionaliteit of gegevens uit te scha kelen door machtigingen (ook wel [scopes](v2-permissions-and-consent.md)genoemd) te bieden. Voor een aanroep-app om machtigingen voor een bereik te verkrijgen, moet de gebruiker toestemming geven voor het bereik tijdens een stroom. Het micro soft Identity-platform vraagt de gebruiker om toestemming en registreert vervolgens machtigingen in alle toegangs tokens die de Web-API ontvangt. De Web-API valideert de toegangs tokens die op elke aanroep worden ontvangen en voert autorisatie controles uit.

Een web-API kan toegangs tokens ontvangen van alle typen apps, waaronder web server apps, desktop-en Mobile apps, apps met één pagina, daemons aan server zijde en zelfs andere web-Api's. De stroom op hoog niveau voor een web-API ziet er als volgt uit:

![Hiermee wordt de Web-API-verificatie stroom weer gegeven](./media/v2-app-types/convergence-scenarios-webapi.svg)

Als u wilt weten hoe u een web-API kunt beveiligen met behulp van OAuth2-toegangs tokens, raadpleegt u de voor beelden van de Web-API in het scenario voor de [beveiligde web-API](scenario-protected-web-api-overview.md).

In veel gevallen moeten Web-Api's ook uitgaande aanvragen indienen bij andere stroomafwaartse Web-Api's die worden beveiligd door het micro soft Identity-platform. Om dit te doen, kunnen Web-Api's gebruikmaken van de **namen van** de stroom, waarmee de Web-API een binnenkomend toegangs token kan uitwisselen voor een ander toegangs token dat in uitgaande aanvragen moet worden gebruikt. Zie voor meer informatie het [micro soft Identity-platform en de OAuth 2,0-stroom](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobiele en systeemeigen apps

Apps die zijn geïnstalleerd op apparaten, zoals mobiele en desktop-apps, hebben vaak toegang tot back-end-services of Web-Api's waarmee gegevens worden opgeslagen en functies kunnen worden uitgevoerd namens een gebruiker. Deze apps kunnen aanmelding en autorisatie toevoegen aan back-end-services met behulp van de [OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md).

In deze stroom ontvangt de app een autorisatie code van het micro soft Identity-platform wanneer de gebruiker zich aanmeldt. De autorisatie code vertegenwoordigt de machtiging van de app voor het aanroepen van back-end-services namens de gebruiker die zich heeft aangemeld. De app kan de autorisatie code op de achtergrond voor een OAuth 2,0-toegangs token en een vernieuwings token uitwisselen. De app kan het toegangs token gebruiken om te verifiëren bij Web-Api's in HTTP-aanvragen en het vernieuwings token gebruiken om nieuwe toegangs tokens op te halen wanneer oudere toegangs tokens verlopen.

![Toont de systeem eigen app-verificatie stroom](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Als de toepassing de standaard systeem-Webweergave gebruikt, raadpleegt u de informatie over "bevestig mijn aanmelding" en de fout code AADSTS50199 in [Azure AD-verificatie en autorisatie fout codes](reference-aadsts-error-codes.md).

## <a name="daemons-and-server-side-apps"></a>Daemons en apps aan de server zijde

Apps die langlopende processen hebben of die werken zonder interactie met een gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Deze apps kunnen tokens verifiëren en ophalen met behulp van de identiteit van de app, in plaats van de gedelegeerde identiteit van een gebruiker, met de OAuth 2,0-client referenties stroom. U kunt de identiteit van de app bewijzen met behulp van een client geheim of certificaat. Zie de [.net core daemon-console toepassing met micro soft Identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)voor meer informatie.

In deze stroom communiceert de app rechtstreeks met het `/token` eind punt om toegang te krijgen:

![Toont de verificatie stroom van de daemon-app](./media/v2-app-types/convergence-scenarios-daemon.svg)

Als u een daemon-app wilt maken, raadpleegt u de [documentatie van client referenties](v2-oauth2-client-creds-grant-flow.md)of probeert u een [.net-voorbeeld toepassing](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de typen toepassingen die worden ondersteund door het micro soft Identity platform, leest u meer over [OAuth 2,0 en OpenID Connect Connect](active-directory-v2-protocols.md) om inzicht te krijgen in de protocol onderdelen die worden gebruikt door de verschillende scenario's.
