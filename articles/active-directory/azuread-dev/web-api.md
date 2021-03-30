---
title: Web-API-apps in Azure Active Directory
description: Hierin wordt beschreven welke Web API-toepassingen en de basis beginselen van de protocol stroom, registratie en het verval van tokens voor dit type app.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154420"
---
# <a name="web-api"></a>Web-API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web-API-apps zijn webtoepassingen die resources moeten ophalen van een web-API. In dit scenario zijn er twee identiteits typen die de webtoepassing kan gebruiken om de Web-API te verifiëren en aan te roepen:

- **Toepassings-id** : in dit scenario worden OAuth 2,0-client referenties gebruikt voor verificatie als de toepassing en toegang tot de Web-API. Wanneer u een toepassings-id gebruikt, kan de Web-API alleen detecteren dat de webtoepassing deze aanroept, omdat de Web-API geen informatie over de gebruiker ontvangt. Als de toepassing informatie ontvangt over de gebruiker, wordt deze via het toepassings protocol verzonden en is deze niet ondertekend door Azure AD. De Web-API vertrouwt de verificatie van de gebruiker door de webtoepassing. Daarom wordt dit patroon een vertrouwd subsysteem genoemd.
- **Gedelegeerde gebruikers-id** : dit scenario kan op twee manieren worden uitgevoerd. OpenID Connect Connect en OAuth 2,0-autorisatie code verlenen met een vertrouwelijke client. De webtoepassing verkrijgt een toegangs token voor de gebruiker, die bewijst dat de Web-API die de gebruiker met succes is geverifieerd voor de webtoepassing en dat de webtoepassing een gedelegeerde gebruikers-id kan verkrijgen om de Web-API aan te roepen. Dit toegangs token wordt in de aanvraag verzonden naar de Web-API, waarmee de gebruiker wordt geautoriseerd en de gewenste resource wordt geretourneerd.

De identiteits typen toepassings identiteit en gedelegeerde gebruiker worden in de onderstaande stroom besproken. Het belangrijkste verschil is dat de gedelegeerde gebruikers-id eerst een autorisatie code moet verkrijgen voordat de gebruiker zich kan aanmelden en toegang kan krijgen tot de Web-API.

## <a name="diagram"></a>Diagram

![Webtoepassing voor web-API-diagram](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Protocol stroom

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Toepassings-id met OAuth 2,0-client referenties toewijzen

1. Een gebruiker is aangemeld bij Azure AD in de webtoepassing (Zie de sectie **Web apps** voor meer informatie).
1. De webtoepassing moet een toegangs token verkrijgen zodat het kan worden geverifieerd bij de Web-API en de gewenste resource kan ophalen. Er wordt een aanvraag gemaakt voor het token eindpunt van Azure AD, met de referentie, toepassings-ID en de URI van de toepassings-ID van de Web-API.
1. Azure AD verifieert de toepassing en retourneert een JWT-toegangs token dat wordt gebruikt om de Web-API aan te roepen.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangs token om de JWT-teken reeks toe te voegen met de aanduiding ' Bearer ' in de autorisatie-header van de aanvraag naar de Web-API. De Web-API valideert vervolgens de JWT-token en als de validatie is geslaagd, wordt de gewenste resource geretourneerd.

### <a name="delegated-user-identity-with-openid-connect"></a>Gedelegeerde gebruikers-id met OpenID Connect Connect

1. Een gebruiker is aangemeld bij een webtoepassing met behulp van Azure AD (Zie de sectie webbrowser to Web Application hierboven). Als de gebruiker van de webtoepassing nog niet heeft ingestemd, zodat de webtoepassing de Web-API namens u kan aanroepen, moet de gebruiker toestemming geven. De toepassing geeft de benodigde machtigingen weer en als een van deze machtigingen op beheerders niveau is, kan een normale gebruiker in de Directory geen toestemming geven. Dit toestemming proces geldt alleen voor toepassingen met meerdere tenants, niet voor afzonderlijke Tenant toepassingen, omdat de toepassing al over de benodigde machtigingen beschikt. Wanneer de gebruiker zich heeft aangemeld, heeft de webtoepassing een ID-token ontvangen met informatie over de gebruiker, evenals een autorisatie code.
1. Met de autorisatie code die is uitgegeven door Azure AD, verzendt de webtoepassing een aanvraag naar het token-eind punt van Azure AD met daarin de autorisatie code, Details over de client toepassing (toepassings-ID en omleidings-URI) en de gewenste resource (ID van de toepassings-URI voor de Web-API).
1. De autorisatie code en informatie over de webtoepassing en Web-API worden gevalideerd door Azure AD. Bij een geslaagde validatie retourneert Azure AD twee tokens: een JWT-toegangs token en een JWT-vernieuwings token.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangs token om de JWT-teken reeks toe te voegen met de aanduiding ' Bearer ' in de autorisatie-header van de aanvraag naar de Web-API. De Web-API valideert vervolgens de JWT-token en als de validatie is geslaagd, wordt de gewenste resource geretourneerd.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Gedelegeerde gebruikers-id met OAuth 2,0-autorisatie code verlenen

1. Een gebruiker is al aangemeld bij een webtoepassing, waarvan het verificatie mechanisme onafhankelijk is van Azure AD.
1. De webtoepassing vereist een autorisatie code voor het verkrijgen van een toegangs token, waardoor een aanvraag via de browser wordt uitgegeven in het autorisatie-eind punt van Azure AD, waarbij de toepassings-ID en omleidings-URI voor de webtoepassing worden opgegeven nadat de verificatie is geslaagd. De gebruiker meldt zich aan bij Azure AD.
1. Als de gebruiker van de webtoepassing nog niet heeft ingestemd, zodat de webtoepassing de Web-API namens u kan aanroepen, moet de gebruiker toestemming geven. De toepassing geeft de benodigde machtigingen weer en als een van deze machtigingen op beheerders niveau is, kan een normale gebruiker in de Directory geen toestemming geven. Deze toestemming geldt voor zowel één als multi tenant-toepassing. In het geval van één Tenant kan een beheerder toestemming geven om namens hun gebruikers toestemming te geven voor de beheerder. U kunt dit doen met behulp `Grant Permissions` van de knop in de [Azure Portal](https://portal.azure.com). 
1. Nadat de gebruiker heeft ingestemd, ontvangt de webtoepassing de autorisatie code die nodig is om een toegangs token te verkrijgen.
1. Met de autorisatie code die is uitgegeven door Azure AD, verzendt de webtoepassing een aanvraag naar het token-eind punt van Azure AD met daarin de autorisatie code, Details over de client toepassing (toepassings-ID en omleidings-URI) en de gewenste resource (ID van de toepassings-URI voor de Web-API).
1. De autorisatie code en informatie over de webtoepassing en Web-API worden gevalideerd door Azure AD. Bij een geslaagde validatie retourneert Azure AD twee tokens: een JWT-toegangs token en een JWT-vernieuwings token.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangs token om de JWT-teken reeks toe te voegen met de aanduiding ' Bearer ' in de autorisatie-header van de aanvraag naar de Web-API. De Web-API valideert vervolgens de JWT-token en als de validatie is geslaagd, wordt de gewenste resource geretourneerd.

## <a name="code-samples"></a>Codevoorbeelden

Zie de code voorbeelden voor Web Application to Web API-scenario's. En kom regel matig terug: nieuwe voor beelden worden regel matig toegevoegd. Web [Application to Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>App-registratie

Zie [een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor het registreren van een toepassing met het Azure AD v 1.0-eind punt.

* Eén Tenant: voor zowel de toepassings identiteit als overgedragen gebruikers-id's moet de webtoepassing en de Web-API zijn geregistreerd in dezelfde map in azure AD. De Web-API kan worden geconfigureerd om een set machtigingen beschikbaar te stellen, die wordt gebruikt om de toegang van de webtoepassing tot de resources te beperken. Als een overgedragen gebruikers identiteits type wordt gebruikt, moet de webtoepassing de gewenste machtigingen selecteren in de vervolg keuzelijst **machtigingen voor andere toepassingen** in de Azure Portal. Deze stap is niet vereist als het toepassings identiteits type wordt gebruikt.
* Multi tenant: eerst wordt de webtoepassing geconfigureerd om de machtigingen aan te geven die nodig zijn om functioneel te zijn. Deze lijst met vereiste machtigingen wordt weer gegeven in een dialoog venster wanneer een gebruiker of beheerder in de doelmap toestemming geeft voor de toepassing, zodat deze beschikbaar is voor hun organisatie. Voor sommige toepassingen zijn alleen machtigingen op gebruikers niveau vereist, waarbij elke gebruiker in de organisatie toestemming kan geven. Andere toepassingen vereisen machtigingen op beheerders niveau, die een gebruiker in de organisatie niet kan instemmen. Alleen een adreslijst beheerder kan toestemming geven voor toepassingen waarvoor dit machtigings niveau is vereist. Wanneer de gebruiker of beheerder de toestemming heeft gegeven, worden de webtoepassing en de Web-API geregistreerd in hun Directory.

## <a name="token-expiration"></a>Token verloop tijd

Wanneer de webtoepassing de autorisatie code gebruikt om een JWT-toegangs token op te halen, ontvangt deze ook een JWT-vernieuwings token. Wanneer het toegangs token is verlopen, kan het vernieuwings token worden gebruikt om de gebruiker opnieuw te verifiëren zonder dat ze zich opnieuw moeten aanmelden. Deze vernieuwings token wordt vervolgens gebruikt om de gebruiker te verifiëren, wat resulteert in een nieuw toegangs token en vernieuwings token.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [toepassings typen en scenario's](app-types.md)
- Meer informatie over de [basis beginselen](v1-authentication-scenarios.md) van Azure AD-verificatie
