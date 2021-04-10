---
title: Mobiele apps registreren die web-Api's aanroepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept (registratie van de app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6385f03556d155941139b77333d6f4a25081fe67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103155"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Mobiele apps registreren die web-Api's aanroepen

Dit artikel bevat instructies om u te helpen bij het registreren van een mobiele toepassing die u maakt.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

De account typen die door uw mobiele toepassingen worden ondersteund, zijn afhankelijk van de ervaring die u wilt inschakelen en de stromen die u wilt gebruiken.

### <a name="audience-for-interactive-token-acquisition"></a>Doel groep voor het verkrijgen van interactief tokens

De meeste mobiele toepassingen gebruiken interactieve verificatie. Als uw app gebruikmaakt van deze vorm van verificatie, kunt u zich aanmelden bij gebruikers vanuit elk [account type](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Doel groep voor geïntegreerde Windows-verificatie, gebruikers naam-wacht woord en B2C

Als u een Universeel Windows-platform-app (UWP) hebt, kunt u gebruikmaken van geïntegreerde Windows-verificatie om gebruikers aan te melden. Als u geïntegreerde Windows-verificatie of gebruikers naam-wachtwoord verificatie wilt gebruiken, moet uw toepassing zich aanmelden bij uw eigen line-of-Business (LOB)-ontwikkelaars Tenant. In een scenario met een onafhankelijke software leverancier (ISV) kan uw toepassing gebruikers in Azure Active Directory organisaties aanmelden. Deze verificatie stromen worden niet ondersteund voor persoonlijke micro soft-accounts.

U kunt gebruikers ook aanmelden met sociale identiteiten die een B2C-instantie en-beleid door geven. U kunt deze methode gebruiken door alleen interactieve verificatie en gebruikers naam-wachtwoord verificatie te gebruiken. Gebruikers naam-wachtwoord verificatie wordt momenteel alleen ondersteund op Xamarin. iOS, Xamarin. Android en UWP.

Zie [scenario's en ondersteunde verificatie stromen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) en- [scenario's en ondersteunde platforms en talen](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)voor meer informatie.

## <a name="platform-configuration-and-redirect-uris"></a>Platform configuratie en omleidings-Uri's

### <a name="interactive-authentication"></a>Interactieve verificatie

Wanneer u een mobiele app bouwt die gebruikmaakt van interactieve verificatie, is de meest kritieke registratie stap de omleidings-URI. U kunt interactieve verificatie instellen met behulp [van de platform configuratie op de Blade **authenticatie**](https://aka.ms/MobileAppReg).

Met deze ervaring wordt uw app in staat stellen om eenmalige aanmelding (SSO) te ontvangen via Microsoft Authenticator (en Intune-bedrijfsportal op Android). Het biedt ook ondersteuning voor het beheer beleid voor apparaten.

De portal voor app-registratie biedt een preview-ervaring waarmee u de brokered antwoord-URI voor iOS-en Android-toepassingen kunt berekenen:

1. Selecteer in de app-registratie Portal de optie **verificatie**  >  **proberen de nieuwe ervaring**.

   ![De Blade verificatie, waar u een nieuwe ervaring kiest](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecteer **een platform toevoegen**.

   ![Een platform toevoegen](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Wanneer de lijst met platformen wordt ondersteund, selecteert u **IOS**.

   ![Een mobiele toepassing kiezen](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Voer uw bundel-ID in en selecteer vervolgens **registreren**.

   ![Voer uw bundel-ID in](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Wanneer u de stappen hebt voltooid, wordt de omleidings-URI voor u berekend, zoals in de volgende afbeelding.

![De resulterende omleidings-URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Als u de omleidings-URI liever hand matig wilt configureren, kunt u dit doen via het manifest van de toepassing. Dit is de aanbevolen indeling voor het manifest:

- **IOS**: `msauth.<BUNDLE_ID>://auth`
  - Voer bijvoorbeeld in: `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - U kunt de Android-handtekening-hash genereren met behulp van de release sleutel of de debug-toets via de opdracht van het hulp programma.

### <a name="username-password-authentication"></a>Gebruikers naam-wachtwoord verificatie

Als uw app alleen een gebruikers naam-wachtwoord verificatie gebruikt, hoeft u geen omleidings-URI voor uw toepassing te registreren. Deze stroom voert een retour ronding uit naar het micro soft Identity-platform. Uw toepassing wordt niet terugaangeroepen op een specifieke URI.

Identificeer uw toepassing echter als een open bare client toepassing. Hiervoor doet u het volgende:

1. Selecteer in de <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>nog steeds uw app in **app-registraties** en selecteer vervolgens **verificatie**.
1. In **Geavanceerde instellingen**  >  **kunnen open bare client stromen**  >  **de volgende mobiele en desktop stromen inschakelen:**, selecteer **Ja**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Instelling van open bare client inschakelen in het deel venster verificatie in Azure Portal":::

## <a name="api-permissions"></a>API-machtigingen

Mobiele toepassingen bellen Api's namens de aangemelde gebruiker. Uw app moet gedelegeerde machtigingen aanvragen. Deze machtigingen worden ook bereiken genoemd. Afhankelijk van de gewenste ervaring kunt u gedelegeerde machtigingen statisch aanvragen via de Azure Portal. Of u kunt ze dynamisch aanvragen tijdens runtime.

Door machtigingen statisch te registreren, kunnen beheerders uw app eenvoudig goed keuren. Statische registratie wordt aanbevolen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, configuratie van de [app-code](scenario-mobile-app-configuration.md).
