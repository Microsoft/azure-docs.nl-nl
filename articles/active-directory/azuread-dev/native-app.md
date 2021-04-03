---
title: Systeem eigen apps in Azure Active Directory
description: Beschrijft wat systeem eigen apps zijn en de basis principes van protocol stroom, registratie en Token verloop tijd voor dit type app.
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154794"
---
# <a name="native-apps"></a>Systeemeigen apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Systeem eigen apps zijn toepassingen die een web-API namens een gebruiker aanroepen. Dit scenario is gebaseerd op het subsidie type OAuth 2,0-autorisatie code met een open bare client, zoals beschreven in sectie 4,1 van de [oauth 2,0-specificatie](https://tools.ietf.org/html/rfc6749). De systeem eigen toepassing haalt een toegangs token voor de gebruiker op met behulp van het OAuth 2,0-protocol. Dit toegangs token wordt vervolgens verzonden naar de Web-API, waarmee de gebruiker wordt geautoriseerd en de gewenste resource wordt geretourneerd.

## <a name="diagram"></a>Diagram

![Webapi-diagram van systeem eigen toepassing](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Protocol stroom

Als u de Active Directory-verificatie bibliotheken gebruikt, worden de meeste protocol gegevens die hieronder worden beschreven, voor u afgehandeld, zoals het pop-upvenster browser, het in de cache opslaan van tokens en het afhandelen van vernieuwings tokens.

1. Met behulp van een browser pop-up maakt de systeem eigen toepassing een aanvraag voor het autorisatie-eind punt in azure AD. Deze aanvraag bevat de toepassings-ID en de omleidings-URI van de systeem eigen toepassing, zoals wordt weer gegeven in de Azure Portal, en de URI van de toepassings-ID voor de Web-API. Als de gebruiker zich nog niet heeft aangemeld, wordt u gevraagd om u opnieuw aan te melden
1. Azure AD verifieert de gebruiker. Als het een toepassing voor meerdere tenants is en toestemming vereist is om de toepassing te gebruiken, moet de gebruiker toestemming geven als deze nog niet is gedaan. Na het verlenen van toestemming en bij geslaagde verificatie, stuurt Azure AD een reactie van een autorisatie code terug naar de omleidings-URI van de client toepassing.
1. Wanneer in azure AD een reactie van een autorisatie code wordt geretourneerd naar de omleidings-URI, wordt de browser interactie door de client toepassing gestopt en wordt de autorisatie code opgehaald uit het antwoord. Met deze autorisatie code verzendt de client toepassing een aanvraag naar het token eindpunt van Azure AD met daarin de autorisatie code, Details over de client toepassing (toepassings-ID en omleidings-URI) en de gewenste bron (ID van de toepassings-URI voor de Web-API).
1. De autorisatie code en informatie over de client toepassing en Web-API worden gevalideerd door Azure AD. Bij een geslaagde validatie retourneert Azure AD twee tokens: een JWT-toegangs token en een JWT-vernieuwings token. Daarnaast retourneert Azure AD basis informatie over de gebruiker, zoals de weergave naam en Tenant-ID.
1. Via HTTPS gebruikt de client toepassing het geretourneerde JWT-toegangs token om de JWT-teken reeks toe te voegen met de aanduiding ' Bearer ' in de autorisatie-header van de aanvraag naar de Web-API. De Web-API valideert vervolgens de JWT-token en als de validatie is geslaagd, wordt de gewenste resource geretourneerd.
1. Wanneer het toegangs token is verlopen, ontvangt de client toepassing een fout melding die aangeeft dat de gebruiker opnieuw moet worden geverifieerd. Als de toepassing een geldig vernieuwings token heeft, kan dit worden gebruikt om een nieuw toegangs token te verkrijgen zonder dat de gebruiker wordt gevraagd om zich opnieuw aan te melden. Als het vernieuwings token verloopt, moet de toepassing de gebruiker vervolgens interactief verifiëren.

> [!NOTE]
> Het vernieuwings token dat is uitgegeven door Azure AD kan worden gebruikt om toegang te krijgen tot meerdere resources. Als u bijvoorbeeld een client toepassing hebt die gemachtigd is om twee Web-Api's aan te roepen, kan het vernieuwings token ook worden gebruikt om een toegangs token te verkrijgen voor de andere web-API.

## <a name="code-samples"></a>Codevoorbeelden

Zie de code voorbeelden voor systeem eigen toepassingen voor web API-scenario's. En controleer het regel matig. nieuwe voor beelden worden regel matig toegevoegd. [Systeem eigen toepassing naar Web-API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>App-registratie

Zie [een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor het registreren van een toepassing met het Azure AD v 1.0-eind punt.

* Eén Tenant: zowel de systeem eigen toepassing als de Web-API moeten zijn geregistreerd in dezelfde directory in azure AD. De Web-API kan worden geconfigureerd om een set machtigingen beschikbaar te stellen, die wordt gebruikt om de toegang van de systeem eigen toepassing tot de bijbehorende resources te beperken. De client toepassing selecteert vervolgens de gewenste machtigingen in het vervolg keuzemenu ' machtigingen voor andere toepassingen ' in de Azure Portal.
* Multi tenant: eerst de systeem eigen toepassing die ooit is geregistreerd in de map van de ontwikkelaar of de uitgever. Ten tweede is de systeem eigen toepassing geconfigureerd om de machtigingen aan te geven die nodig zijn om functioneel te zijn. Deze lijst met vereiste machtigingen wordt weer gegeven in een dialoog venster wanneer een gebruiker of beheerder in de doelmap toestemming geeft voor de toepassing, zodat deze beschikbaar is voor hun organisatie. Voor sommige toepassingen zijn alleen machtigingen op gebruikers niveau vereist, waarbij elke gebruiker in de organisatie toestemming kan geven. Andere toepassingen vereisen machtigingen op beheerders niveau, die een gebruiker in de organisatie niet kan instemmen. Alleen een adreslijst beheerder kan toestemming geven voor toepassingen waarvoor dit machtigings niveau is vereist. Wanneer de gebruiker of beheerder de toestemming heeft gegeven, wordt alleen de Web-API in de Directory geregistreerd. 

## <a name="token-expiration"></a>Token verloop tijd

Wanneer de systeem eigen toepassing de autorisatie code gebruikt om een JWT-toegangs token op te halen, ontvangt deze ook een JWT-vernieuwings token. Wanneer het toegangs token is verlopen, kan het vernieuwings token worden gebruikt om de gebruiker opnieuw te verifiëren zonder dat ze zich opnieuw moeten aanmelden. Deze vernieuwings token wordt vervolgens gebruikt om de gebruiker te verifiëren, wat resulteert in een nieuw toegangs token en vernieuwings token.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [toepassings typen en scenario's](app-types.md)
- Meer informatie over de [basis beginselen](v1-authentication-scenarios.md) van Azure AD-verificatie
