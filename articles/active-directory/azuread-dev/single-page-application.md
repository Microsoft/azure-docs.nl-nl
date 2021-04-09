---
title: Toepassingen met één pagina in Azure Active Directory
description: Beschrijft welke toepassingen met één pagina (SPAs) en de basis principes van de protocol stroom, registratie en Token verloop tijd voor dit type app.
services: active-directory
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
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154743"
---
# <a name="single-page-applications"></a>Toepassingen met één pagina

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Toepassingen met één pagina (SPAs) zijn doorgaans gestructureerd als een Java script-presentatielaag (front-end) die wordt uitgevoerd in de browser, en een web-API-back-end die wordt uitgevoerd op een server en die de bedrijfs logica van de toepassing implementeert. Zie [inzicht krijgen in de OAuth2 impliciete toekennings stroom in azure Active Directory](v1-oauth2-implicit-grant-flow.md)voor meer informatie over de impliciete autorisatie toekenning en om te bepalen of deze geschikt is voor uw toepassings scenario.

Als de gebruiker zich aanmeldt in dit scenario, gebruikt de front-end Java script [Active Directory Authentication Library voor Java script (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) en de impliciete autorisatie toekenning om een id-token (id_token) te verkrijgen van Azure AD. Het token wordt in de cache opgeslagen en de client koppelt dit aan de aanvraag als het Bearer-token bij het aanroepen van de back-end van de Web-API, die wordt beveiligd met behulp van de OWIN-middleware.

## <a name="diagram"></a>Diagram

![Toepassings diagram met één pagina](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Protocol stroom

1. De gebruiker navigeert naar de webtoepassing.
1. De toepassing retourneert de Java script-front-end (presentatielaag) naar de browser.
1. De gebruiker initieert zich aan te melden, bijvoorbeeld door te klikken op een aanmeldings koppeling. De browser verzendt een GET naar het Azure AD-autorisatie-eind punt om een ID-token aan te vragen. Deze aanvraag bevat de toepassings-ID en antwoord-URL in de query parameters.
1. Azure AD valideert de antwoord-URL op basis van de geregistreerde antwoord-URL die in de Azure Portal is geconfigureerd.
1. De gebruiker meldt zich aan op de aanmeldings pagina.
1. Als de verificatie is geslaagd, maakt Azure AD een ID-token en retourneert deze als een URL-fragment (#) naar de antwoord-URL van de toepassing. Voor een productie toepassing moet deze antwoord-URL HTTPS zijn. Het geretourneerde token bevat claims over de gebruiker en Azure AD die vereist zijn voor de toepassing om het token te valideren.
1. Met de Java script-client code die in de browser wordt uitgevoerd, wordt het token geëxtraheerd van het antwoord dat moet worden gebruikt voor het beveiligen van aanroepen naar de Web-API-back-end van de toepassing.
1. De browser roept de Web-API van de toepassing aan met het ID-token in de autorisatie-header. Met de Azure AD-verificatie service wordt een ID-token uitgegeven dat kan worden gebruikt als Bearer-token als de bron hetzelfde is als de client-ID (in dit geval is dit True als de Web-API de eigen back-end van de app is).

## <a name="code-samples"></a>Codevoorbeelden

Zie de [code voorbeelden voor toepassings scenario's met één pagina](sample-v1-code.md#single-page-applications). Controleer regel matig of er nieuwe voor beelden worden toegevoegd.

## <a name="app-registration"></a>App-registratie

* Eén Tenant: als u een toepassing bouwt voor uw organisatie, moet deze worden geregistreerd in de adres lijst van uw bedrijf met behulp van de Azure Portal.
* Multi tenant: als u een toepassing bouwt die kan worden gebruikt door gebruikers buiten uw organisatie, moet deze zijn geregistreerd in de adres lijst van uw bedrijf, maar moet ook worden geregistreerd in de directory van elke organisatie waarin de toepassing wordt gebruikt. Om ervoor te zorgen dat uw toepassing beschikbaar is in hun Directory, kunt u een aanmeldings proces voor uw klanten toevoegen waarmee ze toestemming kunnen geven voor uw toepassing. Wanneer de gebruiker zich aanmeldt voor uw toepassing, wordt er een dialoog venster weer gegeven met de machtigingen die de toepassing nodig heeft en vervolgens de optie om toestemming te geven. Afhankelijk van de vereiste machtigingen kan een beheerder in de andere organisatie verplicht zijn om toestemming te geven. Wanneer de gebruiker of beheerder de toestemming heeft gegeven, wordt de toepassing geregistreerd in hun Directory.

Nadat de toepassing is geregistreerd, moet deze worden geconfigureerd voor het gebruik van OAuth 2,0 impliciet Grant-protocol. Dit protocol is standaard uitgeschakeld voor toepassingen. Als u het impliciete granting-Protocol van OAuth2 voor uw toepassing wilt inschakelen, bewerkt u het toepassings manifest van het Azure Portal en stelt u de waarde ' oauth2AllowImplicitFlow ' in op ' True '. Zie het [toepassings manifest](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie.

## <a name="token-expiration"></a>Token verloop tijd

Met ADAL.js kunt u het volgende doen:

* Een verlopen token vernieuwen
* Een toegangs token aanvragen om een web-API-resource aan te roepen

Na een geslaagde verificatie schrijft Azure AD een cookie in de browser van de gebruiker om een sessie tot stand te brengen. Houd er rekening mee dat de sessie bestaat tussen de gebruiker en Azure AD (niet tussen de gebruiker en de webtoepassing). Wanneer een token verloopt, ADAL.js deze sessie gebruiken om een andere token op de achtergrond te verkrijgen. ADAL.js gebruikt een verborgen iFrame om de aanvraag te verzenden en te ontvangen via het OAuth-granting-protocol. ADAL.js kan hetzelfde mechanisme ook gebruiken om toegangs tokens op de achtergrond te verkrijgen voor andere web API-resources. de toepassing wordt aangeroepen zolang deze resources ondersteuning bieden voor het delen van meerdere oorsprong (CORS), worden geregistreerd in de map van de gebruiker en de vereiste toestemming is gegeven door de gebruiker tijdens het aanmelden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [toepassings typen en scenario's](app-types.md)
* Meer informatie over de [basis beginselen](v1-authentication-scenarios.md) van Azure AD-verificatie
