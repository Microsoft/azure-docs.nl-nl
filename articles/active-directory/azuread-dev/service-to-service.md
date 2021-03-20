---
title: Service-naar-service-apps in Azure Active Directory
description: Hierin worden de service-to-service-toepassingen en de basis beginselen van de protocol stroom, registratie en het verval van tokens voor dit app-type beschreven.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154760"
---
# <a name="service-to-service-apps"></a>Service-naar-service-apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Service-naar-service-toepassingen kunnen een daemon of server toepassing zijn die bronnen van een web-API moet ophalen. Er zijn twee subscenario's die van toepassing zijn op deze sectie:

- Een daemon die een web-API moet aanroepen, gebaseerd op OAuth 2,0-client referenties toekennings type

    In dit scenario is het belang rijk om enkele dingen te begrijpen. Ten eerste is gebruikers interactie niet mogelijk met een daemon-toepassing. hiervoor moet de toepassing een eigen identiteit hebben. Een voor beeld van een daemon-toepassing is een batch-taak of een besturingssysteem service die op de achtergrond wordt uitgevoerd. Met dit type toepassing wordt een toegangs token aangevraagd met behulp van de toepassings-ID en worden de toepassings-id's, referenties (wacht woord of certificaat) en de URI van de toepassings-ID naar Azure AD gepresenteerd. Na een geslaagde verificatie ontvangt de daemon een toegangs token van Azure AD, dat vervolgens wordt gebruikt om de Web-API aan te roepen.

- Een server toepassing (zoals een web-API) die een web-API moet aanroepen, gebaseerd op OAuth 2,0 namens-concept specificatie

    In dit scenario Stel dat een gebruiker heeft geverifieerd op een systeem eigen toepassing en dat deze systeem eigen toepassing moet aanroepen van een web-API. Azure AD geeft een JWT-toegangs token om de Web-API aan te roepen. Als de Web-API een andere stroomafwaartse Web-API moet aanroepen, kan deze de identiteit van de gebruiker delegeren en verifiëren voor de Web-API op de tweede laag.

## <a name="diagram"></a>Diagram

![Web API-diagram van daemon of server toepassing](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Protocol stroom

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Toepassings-id met OAuth 2,0-client referenties toewijzen

1. Eerst moet de server toepassing worden geverifieerd met Azure AD als zichzelf, zonder menselijke tussen komst, zoals een interactief aanmeld venster. Er wordt een aanvraag gemaakt voor het token eindpunt van Azure AD, met de referentie, toepassings-ID en URI voor de toepassings-ID.
1. Azure AD verifieert de toepassing en retourneert een JWT-toegangs token dat wordt gebruikt om de Web-API aan te roepen.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangs token om de JWT-teken reeks toe te voegen met de aanduiding ' Bearer ' in de autorisatie-header van de aanvraag naar de Web-API. De Web-API valideert vervolgens de JWT-token en als de validatie is geslaagd, wordt de gewenste resource geretourneerd.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Gedelegeerde gebruikers-id met OAuth 2,0 namens-concept specificatie

In de onderstaande stroom wordt ervan uitgegaan dat een gebruiker is geverifieerd op een andere toepassing (zoals een systeem eigen toepassing) en dat de gebruikers-id is gebruikt om een toegangs token aan de Web-API op de eerste laag te verkrijgen.

1. De systeem eigen toepassing stuurt het toegangs token naar de Web-API voor de eerste laag.
1. De Web-API voor de eerste laag verzendt een aanvraag naar het token eindpunt van Azure AD, met de toepassings-ID en referenties, evenals het toegangs token van de gebruiker. Daarnaast wordt de aanvraag verzonden met een on_behalf_of para meter die aangeeft dat de Web-API nieuwe tokens aanvraagt voor het aanroepen van een stroomafwaartse Web-API namens de oorspronkelijke gebruiker.
1. Azure AD controleert of de Web-API voor de eerste laag machtigingen heeft voor toegang tot de Web-API op de tweede laag en valideert de aanvraag, retourneert een JWT-toegangs token en een JWT-vernieuwings token naar de Web-API voor de eerste laag.
1. Via HTTPS roept de Web-API voor de eerste laag vervolgens de Web-API op de tweede laag aan door de token teken reeks toe te voegen aan de autorisatie-header in de aanvraag. Met de Web-API voor de eerste laag kunt u de Web-API op de tweede laag aanroepen, zolang het toegangs token en de vernieuwings tokens geldig zijn.

## <a name="code-samples"></a>Codevoorbeelden

Zie de code voorbeelden voor daemon of server toepassing Web API-scenario's: [Server-of daemon-toepassing naar Web-API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>App-registratie

* Eén Tenant: voor zowel de toepassings identiteit als de gedelegeerde gebruikers identiteiten moet de daemon of de server toepassing zijn geregistreerd in dezelfde map in azure AD. De Web-API kan worden geconfigureerd om een set machtigingen beschikbaar te stellen, die wordt gebruikt om de daemon of server toegang tot de resources te beperken. Als een overgedragen gebruikers identiteits type wordt gebruikt, moet de server toepassing de gewenste machtigingen selecteren. Klik op de pagina **API-machtiging** voor de registratie van de toepassing, nadat u **een machtiging toevoegen** hebt geselecteerd en de API-groep hebt gekozen, kies **gedelegeerde machtigingen** en selecteer vervolgens uw machtigingen. Deze stap is niet vereist als het toepassings identiteits type wordt gebruikt.
* Multi tenant: eerst is de daemon of server toepassing geconfigureerd om de machtigingen aan te geven die nodig zijn om functioneel te zijn. Deze lijst met vereiste machtigingen wordt weer gegeven in een dialoog venster wanneer een gebruiker of beheerder in de doelmap toestemming geeft voor de toepassing, zodat deze beschikbaar is voor hun organisatie. Voor sommige toepassingen zijn alleen machtigingen op gebruikers niveau vereist, waarbij elke gebruiker in de organisatie toestemming kan geven. Andere toepassingen vereisen machtigingen op beheerders niveau, die een gebruiker in de organisatie niet kan instemmen. Alleen een adreslijst beheerder kan toestemming geven voor toepassingen waarvoor dit machtigings niveau is vereist. Wanneer de gebruiker of beheerder de toestemming heeft gegeven, worden beide Web-Api's geregistreerd in hun Directory.

## <a name="token-expiration"></a>Token verloop tijd

Wanneer de eerste toepassing de autorisatie code gebruikt om een JWT-toegangs token op te halen, ontvangt deze ook een JWT-vernieuwings token. Wanneer het toegangs token is verlopen, kan het vernieuwings token worden gebruikt om de gebruiker opnieuw te verifiëren zonder dat om referenties wordt gevraagd. Deze vernieuwings token wordt vervolgens gebruikt om de gebruiker te verifiëren, wat resulteert in een nieuw toegangs token en vernieuwings token.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [toepassings typen en scenario's](app-types.md)
- Meer informatie over de [basis beginselen](v1-authentication-scenarios.md) van Azure AD-verificatie
