---
title: 'Snelstart: Gebruikers aanmelden in Angular-apps met één pagina - Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart vindt u meer informatie over hoe u met een Angular-app een API kunt aanroepen waarvoor toegangstokens zijn vereist die zijn verstrekt door het Microsoft-identiteitsplatform.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814024"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Quickstart: Gebruikers aanmelden en een toegangstoken verkrijgen in een Angular-toepassing met één pagina

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld met een SPA-toepassing (hoektoepassing met één pagina), en een toegangstoken kunnen verkrijgen om de Microsoft Graph API aan te roepen. Het codevoorbeeld laat zien hoe u een toegangstoken kunt krijgen om de Microsoft Graph API of willekeurige web-API aan te roepen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) voor het bewerken van projectbestanden of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) om het project uit te voeren.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>De quickstart-app registreren en downloaden
>
> Gebruik een van de volgende opties om de quickstart-app te starten.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Optie 1 (express): Registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart-ervaring <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure-portal - App-registraties</a>.
> 1. Voer een naam in voor de toepassing en selecteer dan **Registreren**.
> 1. Zoek in het snelstartvenster de snelstart voor Angular. Volg de instructies om de nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Optie 2 (handmatig): Registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-the-application"></a>Stap 1: Registreer de toepassing
>
> 1. Volg de [instructies om een toepassing met één pagina te registreren](./scenario-spa-app-registration.md) in de Azure-portal.
> 1. Voeg een nieuw platform toe aan het deelvenster **Verificatie** van de registratie van uw app en registreer de omleidings-URI: `http://localhost:4200/`.
> 1. In deze quickstart wordt gebruikgemaakt van de [impliciete toewijzingsstroom](v2-oauth2-implicit-grant-flow.md). Selecteer in **de sectie Impliciete toekenning en** hybride stromen **id-tokens en** **Toegangstokens.** Id-tokens en toegangstokens zijn vereist omdat deze app gebruikers meldt en een API aanroept.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Stap 1: De toepassing configureren in de Azure-portal
>
> Om ervoor te zorgen dat het codevoorbeeld in deze quickstart werkt, moet u een omleidings-URI toevoegen aan `http://localhost:4200/` en **Impliciete toekenning inschakelen.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: Het codevoorbeeld downloaden
>[!div renderon="docs"]
>Als u het project wilt uitvoeren met een webserver met behulp van Node.js, kloont u de [voorbeeldopslagplaats](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) of downloadt u [de kernprojectbestanden](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Open de bestanden in een editor zoals Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Stap 3: De JavaScript-app configureren
>
> Bewerk in de map *src/app* *app.module.ts* en stel de waarden `clientId` en `authority` in onder `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Vervang deze waarden:
>
>|Waardenaam|Beschrijving|
>|---------|---------|
>|Voer_hier_de_toepassings-id_in|Op de pagina **Overzicht** van de registratie van uw toepassing, is dit uw waarde voor **Toepassings-id (client)** . |
>|Voer_hier_het_cloud-exemplaar_in|Dit is het Azure-cloud-exemplaar. Voer `https://login.microsoftonline.com` in voor de primaire of algemene Azure-cloud. Raadpleeg [Nationale clouds](./authentication-national-cloud.md) voor nationale clouds (bijvoorbeeld China).|
>|Voer_hier_tenantgegevens_in| Ingesteld op een van de volgende opties: Als uw toepassing *accounts in* deze organisatiemap ondersteunt, vervangt u deze waarde door de map-id (tenant)-id of tenantnaam (bijvoorbeeld `contoso.microsoft.com` ). Als uw toepassing ondersteuning biedt voor *accounts in elke organisatiemap*, vervangt u waarde door `organizations`. Als uw toepassing *accounts in elke organisatiemap en persoonlijke Microsoft-accounts* ondersteunt, vervang deze waarde dan door `common`. Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervang deze waarde dan door `consumers`. |
>|Voer_hier_de_omleidings-URI_in|Vervang door `http://localhost:4200` .|
>|cacheLocation  | (Optioneel) Stel de browseropslag in voor de verificatiestatus. De standaardwaarde is `sessionStorage`.   |
>|storeAuthStateInCookie  | (Optioneel) De bibliotheek identificeren waarin de status van de verificatie-aanvraag wordt opgeslagen. Deze status is vereist voor het valideren van de verificatiestromen in de browsercookies. Deze cookie is ingesteld voor Internet Explorer en Microsoft Edge voor deze twee browsers. Zie de pagina met [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Om de waarden van **Toepassings-id (client-id)** , **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

> Raadpleeg [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md) voor meer informatie over beschikbare opties die u kunt configureren.

> U vindt de broncode voor de MSAL.js-bibliotheek in de opslagplaats [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) op GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app.

> [!div renderon="docs"]
>
> Schuif omlaag in hetzelfde bestand en werk de `graphMeEndpoint` bij. 
> - Vervang de tekenreeks `Enter_the_Graph_Endpoint_Herev1.0/me` door `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` is het eindpunt waarop API-aanroepen worden geplaatst. Voer voor de primaire (globale) Microsoft Graph API-service `https://graph.microsoft.com/` in (neem de afsluitende slash op). Raadpleeg de [documentatie](https://docs.microsoft.com/graph/deployments) voor meer informatie.
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

Als u Node.js gebruikt:

1. Start de server door de volgende opdrachten uit te voeren vanaf de projectmap:

   ```console
   npm install
   npm start
   ```

1. Ga naar `http://localhost:4200/`.
1. Selecteer **Aanmelden**. De eerste keer dat u zich aanmelden, wordt u gevraagd de toepassing toegang te geven tot uw profiel en u automatisch aan te melden.
1. Selecteer **Profiel** om Microsoft Graph aan te roepen. Uw gebruikersprofielgegevens worden weergegeven op de pagina.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Diagram waarin wordt weergegeven hoe de voorbeeld-app in deze quickstart werkt.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanmelden van een gebruiker en het verkrijgen van tokens in de Angular-zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudies voor Angular](./tutorial-v2-angular.md)
