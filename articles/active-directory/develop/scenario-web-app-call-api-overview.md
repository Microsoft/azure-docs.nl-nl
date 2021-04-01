---
title: Een web-app bouwen die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (overzicht)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1fdbdada54320ef28f6a4b04a7f415c835acc9dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756285"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: een web-app die web-Api's aanroept

Informatie over het bouwen van een web-app die gebruikers ondertekent bij het micro soft-identiteits platform en vervolgens Web-Api's aanroept namens de aangemelde gebruiker.

## <a name="prerequisites"></a>Vereisten

In dit scenario wordt ervan uitgegaan dat u het scenario al hebt voltooid [: Web-app die gebruikers aantekent](scenario-web-app-sign-user-overview.md).

## <a name="overview"></a>Overzicht

U voegt verificatie toe aan uw web-app, zodat deze gebruikers kan aanmelden en een web-API kan aanroepen namens de aangemelde gebruiker.

![Web-app die web-API's aanroept](./media/scenario-webapp/web-app.svg)

Web-apps die web-Api's aanroepen, zijn vertrouwelijke client toepassingen.
Daarom registreren ze een geheim (een toepassings wachtwoord of-certificaat) met Azure Active Directory (Azure AD). Dit geheim wordt door gegeven tijdens het aanroepen van Azure AD om een token op te halen.

## <a name="specifics"></a>Opsporingsgegevens

> [!NOTE]
> Het toevoegen van een aanmelding aan een web-app is het beveiligen van de web-app zelf. Deze beveiliging wordt bereikt met behulp van *middleware* -Bibliotheken, niet van de micro soft Authentication Library (MSAL). Het voor gaande scenario, de [Web-app die zich aanmeldt bij gebruikers](scenario-web-app-sign-user-overview.md), valt onder dat onderwerp.
>
> In dit scenario wordt beschreven hoe u Web-Api's aanroept vanuit een web-app. U moet toegangs tokens voor deze web-Api's ontvangen. U kunt MSAL-bibliotheken gebruiken om deze tokens te verkrijgen.

Voor de ontwikkeling van dit scenario zijn de volgende specifieke taken vereist:

- Tijdens de registratie van de [toepassing](scenario-web-app-call-api-app-registration.md)moet u een antwoord-URI, geheim of certificaat opgeven dat wordt gedeeld met Azure AD. Als u uw app op verschillende locaties implementeert, geeft u voor elke locatie een antwoord-URI op.
- De [configuratie](scenario-web-app-call-api-app-configuration.md) van de toepassing moet de client referenties opgeven die met Azure AD zijn gedeeld tijdens de registratie van de toepassing.

## <a name="recommended-reading"></a>Aanbevolen Lees bewerkingen

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [app-registratie](scenario-web-app-call-api-app-registration.md).