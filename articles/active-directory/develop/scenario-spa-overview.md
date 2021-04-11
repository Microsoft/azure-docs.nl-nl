---
title: Java script-scenario met één pagina-app
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een toepassing met één pagina (scenario-overzicht) met behulp van het micro soft Identity-platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 64dfd35d387e5907792440ec40522d976706db22
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966857"
---
# <a name="scenario-single-page-application"></a>Scenario: Toepassing met één pagina

Meer informatie over wat u nodig hebt om een toepassing met één pagina (SPA) te maken.

## <a name="getting-started"></a>Aan de slag

Als u dat nog niet hebt gedaan, maakt u uw eerste app door de Java script-SPA Snelstartgids te volt ooien:

[Snelstartgids: toepassing met één pagina](./quickstart-v2-javascript-auth-code.md)

## <a name="overview"></a>Overzicht

Veel moderne webtoepassingen zijn gebouwd als toepassingen met één pagina aan de client zijde. Ontwikkelaars schrijven deze met JavaScript of een SPA-framework, zoals Angular, Vue en React. Deze toepassingen worden uitgevoerd in een webbrowser en hebben andere verificatiekenmerken dan traditionele webtoepassingen aan de serverzijde.

Het micro soft Identity-platform biedt **twee** opties voor het inschakelen van toepassingen met één pagina voor het aanmelden van gebruikers en het ophalen van tokens voor toegang tot back-end-services of Web-api's:

- [OAuth 2.0-autorisatiecodestroom (met PKCE)](./v2-oauth2-auth-code-flow.md). De autorisatiecodestroom stelt de toepassing in staat een autorisatiecode uit te wisselen voor **id-tokens**, om de geverifieerde gebruiker te vertegenwoordigen, en de benodigde **toegangstokens** om beveiligde API’s aan te roepen. Daarnaast worden met deze stroom **vernieuwingstokens** geretourneerd, die namens gebruikers langetermijntoegang bieden tot resources, zonder dat interactie met deze gebruikers is vereist. Dit is de **aanbevolen** methode.

![Toepassingen met één pagina - verificatie](./media/scenarios/spa-app-auth.svg)

- [Impliciete OAuth 2.0-stroom](./v2-oauth2-implicit-grant-flow.md). De impliciete toekenningsstroom stelt de toepassing in staat om **id-tokens** en **toegangstokens** op te halen. In tegenstelling tot de autorisatiecodestroom retourneert de impliciete toekenningsstroom geen **vernieuwingstoken**.

![Toepassingen met één pagina - impliciet](./media/scenarios/spa-app.svg)

In deze verificatiestroom zijn geen toepassingsscenario's opgenomen waarin wordt gebruikgemaakt van platformoverschrijdende JavaScript-frameworks, zoals Electron en React-Native. Ze vereisen verdere mogelijkheden voor interactie met de systeem eigen platformen.

## <a name="specifics"></a>Opsporingsgegevens

U hebt het volgende nodig om dit scenario voor uw toepassing in te scha kelen:

* Registratie van toepassingen met Azure Active Directory (Azure AD). De registratie stappen verschillen tussen de impliciete overdrachts stroom en de autorisatie code stroom.
* Toepassings configuratie met de eigenschappen van de geregistreerde toepassing, zoals de toepassings-ID.
* Micro soft-verificatie bibliotheek voor Java script (MSAL.js) gebruiken om de verificatie stroom uit te voeren en tokens te verkrijgen.

## <a name="recommended-reading"></a>Aanbevolen Lees bewerkingen

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [app-registratie](scenario-spa-app-registration.md).
