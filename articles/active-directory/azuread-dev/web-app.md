---
title: Web-apps in Azure Active Directory
description: Beschrijft wat web-apps zijn en de basis principes van protocol stroom, registratie en verloop tijd van tokens voor dit type app.
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
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154403"
---
# <a name="web-apps"></a>Web-apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web-apps zijn toepassingen die een gebruiker in een webbrowser aan een webtoepassing verifiëren. In dit scenario leidt de webtoepassing de browser van de gebruiker om deze te registreren bij Azure AD. Azure AD retourneert een aanmeldings reactie via de browser van de gebruiker, die claims bevat over de gebruiker in een beveiligings token. Dit scenario biedt ondersteuning voor aanmelding met de protocollen OpenID Connect Connect, SAML 2,0 en WS-Federation.

## <a name="diagram"></a>Diagram

![Verificatie stroom voor browser voor webtoepassing](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protocol stroom

1. Wanneer een gebruiker de toepassing bezoekt en zich moet aanmelden, worden ze omgeleid via een aanmeldings aanvraag naar het eind punt van de verificatie in azure AD.
1. De gebruiker meldt zich aan op de aanmeldings pagina.
1. Als de verificatie is gelukt, maakt Azure AD een verificatie token en retourneert het een aanmeldings antwoord naar de antwoord-URL van de toepassing die is geconfigureerd in de Azure Portal. Voor een productie toepassing moet deze antwoord-URL HTTPS zijn. Het geretourneerde token bevat claims over de gebruiker en Azure AD die vereist zijn voor de toepassing om het token te valideren.
1. De toepassing valideert het token met behulp van een open bare handtekening sleutel en informatie over de uitgever die beschikbaar is op het federatieve meta gegevens document voor Azure AD. Nadat de toepassing het token heeft gevalideerd, wordt een nieuwe sessie met de gebruiker gestart. Met deze sessie krijgt de gebruiker toegang tot de toepassing tot deze verloopt.

## <a name="code-samples"></a>Codevoorbeelden

Zie de code voorbeelden voor webtoepassingen van de webbrowser. En controleer regel matig of er nieuwe voor beelden worden toegevoegd.

## <a name="app-registration"></a>App-registratie

Zie [een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)om een web-app te registreren.

* Eén Tenant: als u een toepassing bouwt voor uw organisatie, moet deze worden geregistreerd in de adres lijst van uw bedrijf met behulp van de Azure Portal.
* Multi tenant: als u een toepassing bouwt die kan worden gebruikt door gebruikers buiten uw organisatie, moet deze zijn geregistreerd in de adres lijst van uw bedrijf, maar moet ook worden geregistreerd in de directory van elke organisatie waarin de toepassing wordt gebruikt. Om ervoor te zorgen dat uw toepassing beschikbaar is in hun Directory, kunt u een aanmeldings proces voor uw klanten toevoegen waarmee ze toestemming kunnen geven voor uw toepassing. Wanneer de gebruiker zich aanmeldt voor uw toepassing, wordt er een dialoog venster weer gegeven met de machtigingen die de toepassing nodig heeft en vervolgens de optie om toestemming te geven. Afhankelijk van de vereiste machtigingen kan een beheerder in de andere organisatie verplicht zijn om toestemming te geven. Wanneer de gebruiker of beheerder de toestemming heeft gegeven, wordt de toepassing geregistreerd in hun Directory.

## <a name="token-expiration"></a>Token verloop tijd

De sessie van de gebruiker verloopt wanneer de levens duur van het token dat is uitgegeven door Azure AD verloopt. Uw toepassing kan deze tijds periode zo nodig verkorten, zoals het afmelden van gebruikers op basis van een periode van inactiviteit. Wanneer de sessie is verlopen, wordt de gebruiker opnieuw gevraagd zich aan te melden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [toepassings typen en scenario's](app-types.md)
* Meer informatie over de [basis beginselen](v1-authentication-scenarios.md) van Azure AD-verificatie
