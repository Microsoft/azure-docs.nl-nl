---
title: 'Snelstart: Gebruikers aanmelden in JavaScript-apps met één pagina (SPA) met verificatiecode | Azure'
titleSuffix: Microsoft identity platform
description: In deze snelstart leert u hoe u een Javascript-toepassing met één pagina (SPA) gebruikers van persoonlijke Microsoft-accounts, werkaccounts en schoolaccounts kunt aanmelden met behulp van een autorisatiecodestroom.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 40929a165e7a3afbbc92a472c352172723745040
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533188"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Quickstart: Gebruikers aanmelden en een toegangstoken verkrijgen in een JavaScript SPA met behulp van de verificatiecodestroom

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld met een SPA-toepassing (JavaScript-toepassing met één pagina), en een toegangstoken kunnen verkrijgen om de Microsoft Graph API aan te roepen. Het codevoorbeeld laat zien hoe u een toegangstoken kunt verkrijgen om de Microsoft Graph API of willekeurige web-API aan te roepen. 

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

Deze quickstart maakt gebruik van MSAL.js 2.0 met de verificatiecodestroom. Raadpleeg voor een vergelijkbare quickstart die gebruikmaakt van MSAL.js 1.0 met de impliciete stroom [Quickstart: gebruikers aanmelden in JavaScript-apps met één pagina](./quickstart-v2-javascript.md).

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Gratis een Azure-abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>De quickstart-toepassing registreren en downloaden
> Gebruik een van de volgende opties om uw quickstart-app te starten.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1 (Express): registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als u via uw account toegang hebt tot meerdere tenants, selecteert u het account in de rechterbovenhoek en stelt u de portalsessie in op de Azure Active Directory-tenant die u wilt gebruiken.
> 1. Selecteer [App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**.
> 1. Ga naar het quickstart-deelvenster en volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2 (handmatig): registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als u via uw account toegang hebt tot meerdere tenants, selecteert u uw account rechts bovenin en stelt u de portalsessie in op de Azure Active Directory-tenant die u wilt gebruiken.
> 1. Selecteer [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** voor later gebruik.
> 1. Selecteer in het linkerdeelvenster van de geregistreerde toepassing de optie **Verificatie**.
> 1. Selecteer **Een platform toevoegen** onder **Platformconfiguraties**. Selecteer **Toepassing met één pagina** in het deelvenster dat wordt geopend.
> 1. Stel de waarde van **Omleidings-URI** in op `http://localhost:3000/`.
> 1. Selecteer **Configureren**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> U moet een `redirectUri` als `http://localhost:3000/` toevoegen voor een juiste werking van het codevoorbeeld uit deze quickstart.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

> [!div renderon="docs"]
> Als u het project wilt uitvoeren met een webserver met behulp van Node.js, [downloadt u de kernprojectbestanden](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Het project uitvoeren met een webserver met behulp van Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Stap 3: Configureer de JavaScript-app
>
> Open in de map *App* het bestand *authConfig.js* en werk de waarden `clientID`, `authority` en `redirectUri` bij in het object `msalConfig`.
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Wijzig de waarden in de sectie `msalConfig`, zoals hier wordt beschreven:
>
> - `Enter_the_Application_Id_Here` is de **Toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Cloud_Instance_Id_Here` is de instantie van de Azure-cloud. Voer `https://login.microsoftonline.com/` in voor de primaire of algemene Azure-cloud. Zie [Nationale clouds](authentication-national-cloud.md) voor **nationale** clouds (bijvoorbeeld China).
> - `Enter_the_Tenant_info_here` wordt ingesteld op een van de volgende:
>   - Als uw toepassing ondersteuning biedt voor *accounts in deze organisatiemap*, vervangt u deze waarde door de **Tenant-id** of **Tenantnaam**. Bijvoorbeeld `contoso.microsoft.com`.
>   - Als uw toepassing ondersteuning biedt voor *accounts in elke organisatiemap*, vervangt u waarde door `organizations`.
>   - Als uw toepassing ondersteuning biedt voor *accounts in elke organisatiemap en persoonlijke Microsoft-accounts*, vervangt u deze waarde door `common`. Gebruik `common` **voor deze quickstart**.
>   - Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervangt u deze waarde door `consumers`.
> - `Enter_the_Redirect_Uri_Here` is `http://localhost:3000/`.
>
> De waarde `authority` in uw *authConfig.js* moet er als volgt uitzien als u de primaire Azure-cloud (globaal) gebruikt:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** , **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app-registratie in Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app.

> [!div renderon="docs"]
>
> Bewerk vervolgens in dezelfde map het bestand *graphConfig.js* en werk de waarden `graphMeEndpoint` en `graphMailEndpoint` bij in het object `apiConfig`.
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>
> [!div renderon="docs"]
>
> `Enter_the_Graph_Endpoint_Here` is het eindpunt waarop API-aanroepen worden geplaatst. Voer voor de primaire (globale) Microsoft Graph API-service `https://graph.microsoft.com/` in (neem de afsluitende slash op). Zie [National Cloud Deployment](/graph/deployments) voor meer informatie over Microsoft Graph in nationale clouds.
>
> De waarden `graphMeEndpoint` en `graphMailEndpoint` in het bestand *graphConfig.js* moeten er als volgt uitzien als u de primaire (globale) Microsoft Graph API-service gebruikt:
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

Het project uitvoeren met een webserver met behulp van Node.js:

1. Voer de volgende opdracht uit vanuit de projectmap om de server te starten:
    ```console
    npm install
    npm start
    ```
1. Blader naar `http://localhost:3000/`.

1. Selecteer **Aanmelden** om het aanmeldingsproces te starten en roep vervolgens de Microsoft Graph API aan.

    De eerste keer dat u zich aanmeldt, wordt u gevraagd om toestemming te geven zodat de toepassing uw profiel kan openen en u kan aanmelden. Nadat u bent aangemeld, worden de gegevens van uw gebruikersprofiel weergegeven op de pagina.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Diagram van de verificatiecodestroom voor een toepassing met één pagina.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

De MSAL.js-bibliotheek meldt gebruikers aan en verzoekt dat tokens worden gebruikt voor toegang tot een API die wordt beveiligd door het Microsoft Identity-platform. Het bestand *index.html* van het voorbeeld bevat een referentie naar de bibliotheek:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Als Node.js is geïnstalleerd, kunt u de nieuwste versie downloaden met behulp van Node.js Package Manager (npm):

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende zelfstudie voor een gedetailleerde stapsgewijze handleiding voor het bouwen van de toepassing die in deze quickstart wordt gebruikt:

> [!div class="nextstepaction"]
> [Zelfstudie om u aan te melden en MS Graph aan te roepen](./tutorial-v2-javascript-auth-code.md)
