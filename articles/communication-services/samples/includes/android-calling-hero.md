---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: e8ef354480c69fa9b0b5407c88209b368485127d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729793"
---
De Azure Communication Services- **groep voor het aanroepen van een voor beeld van een feed voor Android** laat zien hoe de communicatie services die Android SDK aanroept, kunnen worden gebruikt voor het bouwen van een groep waarmee u de ervaring met spraak en video kunt opbouwen. In dit voor beeld Quick Start leert u hoe u het voor beeld kunt instellen en uitvoeren. Er wordt een overzicht van het voor beeld gegeven voor context.

## <a name="download-code"></a>Code downloaden

Zoek de voltooide code voor deze Quick Start op [github](https://github.com/Azure-Samples/communication-services-android-calling-hero).

## <a name="overview"></a>Overzicht

Het voor beeld is een systeem eigen Android-toepassing die gebruikmaakt van de Android-Sdk's van Azure Communication Services voor het bouwen van een gespreks ervaring die is voorzien van spraak-en video gesprekken. De toepassing maakt gebruik van een component aan server zijde om toegangs tokens in te richten die vervolgens worden gebruikt om de Azure Communication Services SDK te initialiseren. Als u dit onderdeel aan de server zijde wilt configureren, kunt u de [vertrouwde service met Azure functions](../../tutorials/trusted-service-tutorial.md) zelf studie volgen.

Het voorbeeld ziet er als volgt uit:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Schermopname van de landingspagina van de voorbeeldtoepassing.":::

Wanneer u op de knop nieuwe oproep starten klikt, maakt de Android-toepassing een nieuwe oproep en wordt deze toegevoegd. Met de toepassing kunt u ook een bestaande Azure Communication Services-oproep toevoegen door de bestaande oproep-ID op te geven.

Nadat u een gesprek hebt toegevoegd, wordt u gevraagd om de toepassing toegang te geven tot uw camera en microfoon. U wordt ook gevraagd om een weergave naam op te geven.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Schermopname van het scherm voorafgaand aan het gesprek van de voorbeeldtoepassing.":::

Zodra u uw weergave naam en apparaten hebt geconfigureerd, kunt u aan de oproep worden toegevoegd. U ziet het belangrijkste aanroepende canvas waar de kern ervaring van het gesprek woont.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Schermopname van het hoofdscherm van de voorbeeldtoepassing.":::

Onderdelen van het hoofdgespreksscherm:

- **Mediagalerie**: Het hoofdgebied waarin de deelnemers worden weergegeven. Als deelnemers hun camera hebben ingeschakeld, wordt hier hun videofeed weergegeven. Elke deel nemer heeft een afzonderlijke tegel waarin de weergave naam en de video stroom (als er een wordt vermeld) worden weer gegeven. De galerie ondersteunt meerdere deel nemers en wordt bijgewerkt wanneer deel nemers worden toegevoegd aan of verwijderd uit de aanroep.
- **Actie balk**: dit is de plaats waar de primaire oproep besturings elementen zich bevinden. Met deze besturings elementen kunt u uw video en microfoon in-of uitschakelen, uw scherm delen en de aanroep verlaten.

Hieronder vindt u meer informatie over de vereisten en stappen voor het instellen van het voorbeeld.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- [Android Studio](https://developer.android.com/studio) op uw computer wordt uitgevoerd
- Een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../quickstarts/create-communication-resource.md) voor meer informatie.
- Een Azure-functie waarmee het [verificatie-eind punt](../../tutorials/trusted-service-tutorial.md) wordt uitgevoerd om toegangs tokens op te halen.

## <a name="running-sample-locally"></a>Voor beeld lokaal uitvoeren

De groep waarmee een voor beeld wordt aangeroepen, kan lokaal worden uitgevoerd met Android Studio. Ontwikkel aars kunnen een fysiek apparaat of een emulator gebruiken om de toepassing te testen.

### <a name="before-running-the-sample-for-the-first-time"></a>Voordat u het voorbeeld voor de eerste keer uitvoert

1. Open Android Studio en selecteer `Open an Existing Project`
2. Open de `AzureCalling` map in de gedownloade release voor het voor beeld.
3. Vouw app/assets uit om bij te werken `appSettings.properties` . Stel de waarde voor de sleutel `communicationTokenFetchUrl` in op de URL voor het verificatie-eind punt dat is ingesteld als een vereiste.

### <a name="run-sample"></a>Voor beeld uitvoeren

Bouw en voer het voor beeld uit in Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>Beschrijving Een verificatie-eind punt beveiligen

Voor demonstratie doeleinden gebruikt dit voor beeld een openbaar toegankelijk eind punt dat standaard een Azure Communication Services-token ophaalt. Voor productie scenario's kunt u het beste uw eigen beveiligde eind punt gebruiken om uw eigen tokens in te richten.

Met aanvullende configuratie biedt dit voor beeld ondersteuning voor het verbinden met een met **Azure Active Directory** (Azure AD) beveiligd eind punt, zodat gebruikers zich kunnen aanmelden om een Azure Communication Services-token op te halen. Zie de onderstaande stappen:

1. Schakel Azure Active Directory verificatie in uw app in.  
   - [Uw app registreren onder Azure Active Directory (Android-platform instellingen gebruiken)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Uw App Service of Azure Functions app configureren voor het gebruik van Azure AD-aanmelding](../../../app-service/configure-authentication-provider-aad.md)

2. Ga naar de pagina overzicht van geregistreerde apps onder Azure Active Directory app-registraties. Noteer de `Package name` , `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Active Directory configuratie op Azure Portal.":::

3. Bewerken `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` en instellen `isAADAuthEnabled` om Azure Active Directory in te scha kelen
4. Bewerken `AndroidManifest.xml` en instellen `android:path` op hand tekening-hash voor de code van de opslag. Beschrijving. De huidige waarde gebruikt hash van gebundelde debug. Store. Als er een andere versie van de opslag wordt gebruikt, moet dit worden bijgewerkt.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Kopieer de MSAL Android-configuratie van Azure Portal en plak deze in `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . ' Account_mode ' toevoegen: ' SINGLE '
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Bewerk `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` en stel de waarde voor de sleutel `communicationTokenFetchUrl` in op de URL voor uw veilige verificatie-eind punt.
7. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`De waarde voor de sleutel `aadScopes` van `Azure Active Directory` `Expose an API` bereiken bewerken en instellen

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
>[Het voorbeeld downloaden uit GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Raadpleeg voor meer informatie de volgende artikelen:

- Vertrouwd raken met [het gebruik van de aanroepende SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Meer informatie over [de werking van aanroepen](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Meer artikelen

- [Azure Communication GitHub](https://github.com/Azure/communication): u vindt meer voorbeelden en informatie op de officiële GitHub-pagina
- Voor [beelden](./../overview.md) : u vindt meer voor beelden op onze overzichts pagina voor beelden.
- [Functies voor het aanroepen van Azure-communicatie](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) : voor meer informatie over de aanroepende Android SDK-[Azure Communication Android-AANroepende SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
