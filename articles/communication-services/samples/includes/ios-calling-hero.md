---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: f1d49aa0583f34327afff229b46d7a366202aded
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105152730"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

De Azure Communication Services-groep die het **voor beeld van een held aanroept voor IOS** laat zien hoe de communicatie services die IOS SDK aanroept, kunnen worden gebruikt voor het bouwen van een groep waarmee u de ervaring met spraak en video kunt opdoen. In dit voor beeld Quick Start leert u hoe u het voor beeld kunt instellen en uitvoeren. Er wordt een overzicht van het voor beeld gegeven voor context.

## <a name="download-code"></a>Code downloaden

Zoek de voltooide code voor deze Quick Start op [github](https://github.com/Azure-Samples/communication-services-ios-calling-hero).

## <a name="overview"></a>Overzicht

Het voor beeld is een systeem eigen iOS-toepassing die gebruikmaakt van de iOS-Sdk's van Azure Communication Services voor het bouwen van een gespreks ervaring die is voorzien van spraak-en video gesprekken. De toepassing maakt gebruik van een component aan server zijde om toegangs tokens in te richten die vervolgens worden gebruikt om de Azure Communication Services SDK te initialiseren. Als u dit onderdeel aan de server zijde wilt configureren, kunt u de [vertrouwde service met Azure functions](../../tutorials/trusted-service-tutorial.md) zelf studie volgen.

Het voorbeeld ziet er als volgt uit:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Schermopname van de landingspagina van de voorbeeldtoepassing.":::

Wanneer u op de knop nieuwe oproep starten klikt, maakt de iOS-toepassing een nieuwe aanroep en wordt deze toegevoegd. Met de toepassing kunt u lid worden van een bestaande oproep van Azure Communication Services door de ID van de bestaande oproep op te geven. U kunt ook deel nemen aan een team vergadering door de koppeling voor koppelen in de uitnodiging van de vergadering op te geven.  (De koppeling koppelen heeft de volgende indeling: `https://teams.microsoft.com/l/meetup-join/` ). Ga voor meer informatie over teams Interop naar de [conceptuele documentatie van teams Interop](../../concepts/teams-interop.md).

Nadat u een gesprek hebt toegevoegd, wordt u gevraagd om de toepassing toegang te geven tot uw camera en microfoon. U wordt ook gevraagd om een weergave naam op te geven.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Schermopname van het scherm voorafgaand aan het gesprek van de voorbeeldtoepassing.":::

Zodra u uw weergave naam en apparaten hebt geconfigureerd, kunt u aan de oproep worden toegevoegd. U ziet het belangrijkste aanroepende canvas waar de kern ervaring van het gesprek woont.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Schermopname van het hoofdscherm van de voorbeeldtoepassing.":::

Onderdelen van het hoofdgespreksscherm:

- **Mediagalerie**: Het hoofdgebied waarin de deelnemers worden weergegeven. Als deelnemers hun camera hebben ingeschakeld, wordt hier hun videofeed weergegeven. Elke deel nemer heeft een afzonderlijke tegel waarin de weergave naam en de video stroom (als er een wordt vermeld) worden weer gegeven. De galerie ondersteunt meerdere deel nemers en wordt bijgewerkt wanneer deel nemers worden toegevoegd aan of verwijderd uit de aanroep.
- **Actie balk**: dit is de plaats waar de primaire oproep besturings elementen zich bevinden. Met deze besturings elementen kunt u uw video en microfoon in-of uitschakelen, uw scherm delen en de aanroep verlaten.

Hieronder vindt u meer informatie over de vereisten en stappen voor het instellen van het voorbeeld.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd, evenals een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.
- Een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../quickstarts/create-communication-resource.md) voor meer informatie.
- Een Azure-functie waarmee het [verificatie-eind punt](../../tutorials/trusted-service-tutorial.md) wordt uitgevoerd om toegangs tokens op te halen.

## <a name="running-sample-locally"></a>Voor beeld lokaal uitvoeren

De groep die voor beeld aanroept, kan lokaal worden uitgevoerd met behulp van XCode. Ontwikkel aars kunnen een fysiek apparaat of een emulator gebruiken om de toepassing te testen.

### <a name="before-running-the-sample-for-the-first-time"></a>Voordat u het voorbeeld voor de eerste keer uitvoert

1. Installeer afhankelijkheden door uit te voeren `pod install` .
2. Open `AzureCalling.xcworkspace` in Xcode.
3. Werk `AppSettings.plist` bij. Stel de waarde voor de `communicationTokenFetchUrl` sleutel in op de URL voor het verificatie-eind punt.

### <a name="run-sample"></a>Voor beeld uitvoeren

Bouw en voer het voor beeld uit in XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>Beschrijving Een verificatie-eind punt beveiligen

Voor demonstratie doeleinden gebruikt dit voor beeld een openbaar toegankelijk eind punt dat standaard een Azure Communication Services-toegangs token ophaalt. Voor productie scenario's kunt u het beste uw eigen beveiligde eind punt gebruiken om uw eigen tokens in te richten.

Met aanvullende configuratie ondersteunt dit voor beeld verbinding met een met **Azure Active Directory** (Azure AD) beveiligd eind punt, zodat de gebruiker zich kan aanmelden om een Azure Communication Services-toegangs token op te halen. Zie de onderstaande stappen:

1. Schakel Azure Active Directory verificatie in uw app in.  
   - [Uw app registreren onder Azure Active Directory (met iOS/macOS-platform instellingen)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Uw App Service of Azure Functions app configureren voor het gebruik van Azure AD-aanmelding](../../../app-service/configure-authentication-provider-aad.md)
2. Ga naar de pagina overzicht van geregistreerde apps onder Azure Active Directory app-registraties. Noteer de `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Active Directory configuratie op Azure Portal.":::

3. Open `AppSettings.plist` Xcode en voeg de volgende sleutel waarden toe:
   - `communicationTokenFetchUrl`: De URL voor het aanvragen van een Azure Communication Services-token 
   - `isAADAuthEnabled`: Een Booleaanse waarde die aangeeft of de Azure Communication Services-token verificatie vereist is
   - `aadClientId`: Uw toepassings-ID (client)
   - `aadTenantId`: Uw directory (Tenant)-ID
   - `aadRedirectURI`: De omleidings-URI moet de volgende indeling hebben: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Een matrix met machtigings bereiken die worden aangevraagd door gebruikers voor autorisatie. Toevoegen aan `<Application ID URI>/user_impersonation` de matrix om toegang te verlenen tot het verificatie-eind punt

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
>[Het voorbeeld downloaden uit GitHub](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

Raadpleeg voor meer informatie de volgende artikelen:

- Vertrouwd raken met [het gebruik van de aanroepende SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Meer informatie over [de werking van aanroepen](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Meer artikelen

- [Azure Communication GitHub](https://github.com/Azure/communication): u vindt meer voorbeelden en informatie op de officiële GitHub-pagina
- Voor [beelden](./../overview.md) : u vindt meer voor beelden op onze overzichts pagina voor beelden.
- [Functies voor het aanroepen van Azure-communicatie](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) : voor meer informatie over de aanroepende IOS SDK-[Azure Communication IOS Calling SDK](https://github.com/Azure/Communication/releases/)
