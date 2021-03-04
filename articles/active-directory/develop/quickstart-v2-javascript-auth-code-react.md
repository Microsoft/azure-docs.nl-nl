---
title: 'Quick Start: gebruikers aanmelden in Java script reageren op apps met één pagina (SPA) met verificatie code en aanroepen Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe een Java script reageert op een single-page-toepassing (SPA) gebruikers van persoonlijke accounts, werk accounts en school accounts kan aanmelden met behulp van de autorisatie code stroom en oproep Microsoft Graph.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 3df3d4a3e87f67678833f097a1e2aa3633a5991e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096425"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>Snelstartgids: Meld u aan en ontvang een toegangs token in een beveiligd-wachtwoord verificatie met behulp van de autorisatie code stroom

In deze Snelstartgids downloadt en voert u een code voorbeeld uit die laat zien hoe een Java script reageert op een toepassing met één pagina (SPA) kan worden aangemeld bij gebruikers en kan worden aangeroepen Microsoft Graph met de autorisatie code stroom. Het codevoorbeeld laat zien hoe u een toegangstoken kunt krijgen om de Microsoft Graph API of willekeurige web-API aan te roepen. 

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

Deze Snelstartgids maakt gebruik van MSAL reageren met de autorisatie code stroom. Voor een vergelijk bare Snelstartgids die gebruikmaakt van MSAL.js met de impliciete stroom, raadpleegt u [Quick Start: gebruikers aanmelden in Java script-apps met één pagina](./quickstart-v2-javascript.md).

Deze functie [!INCLUDE [active-directory-develop-preview](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Gratis een Azure-abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>De quickstart-toepassing registreren en downloaden
> Gebruik een van de volgende opties om uw quickstart-app te starten.
>
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1 (Express): registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart-ervaring <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure-portal - App-registraties</a>.
> 1. Voer een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**.
> 1. Ga naar het quickstart-deelvenster en volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2 (handmatig): registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** voor later gebruik.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer **Een platform toevoegen** onder **Platformconfiguraties**. Selecteer **Toepassing met één pagina** in het deelvenster dat wordt geopend.
> 1. Stel de waarde voor de **omleidings-uri's** in op `http://localhost:3000/` . Dit is de standaard poort NodeJS luistert op uw lokale machine. Nadat de gebruiker is geverifieerd, wordt de verificatie reactie naar deze URI geretourneerd. 
> 1. Selecteer **configureren** om de wijzigingen toe te passen.
> 1. Onder **platform configuraties** wordt een **toepassing met één pagina** uitgevouwen.
> 1. Controleer of onder **verleende typen** ![ die ](media/quickstart-v2-javascript/green-check.png) de omleidings-URI al hebben geconfigureerd, in aanmerking komt voor de autorisatie code stroom met PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Voor het code voorbeeld in deze Snelstartgids werkt u een **omleidings-URI** van toe `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

> [!div renderon="docs"]
> Als u het project wilt uitvoeren met een webserver met behulp van Node.js, [downloadt u de kernprojectbestanden](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Het project uitvoeren met een webserver met behulp van Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Stap 3: Configureer de JavaScript-app
>
> Open in de map *src* het *authConfig.js* -bestand en werk de `clientID` `authority` waarden, en `redirectUri` bij in het- `msalConfig` object.
>
> ```javascript
> /**
> * Configuration object to be passed to MSAL instance on creation. 
> * For a full list of MSAL.js configuration parameters, visit:
> * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md 
> */
> export const msalConfig = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>        redirectUri: "Enter_the_Redirect_Uri_Here"
>    },
>    cache: {
>        cacheLocation: "sessionStorage", // This configures where your cache will be stored
>        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    },
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

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app.

> [!div renderon="docs"]
>
> Schuif omlaag in hetzelfde bestand en werk de `graphMeEndpoint` . 
> - Vervang de teken reeks `Enter_the_Graph_Endpoint_Herev1.0/me` door `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` is het eindpunt waarop API-aanroepen worden geplaatst. Voer voor de primaire (globale) Microsoft Graph API-service `https://graph.microsoft.com/` in (neem de afsluitende slash op). Raadpleeg de [documentatie](/graph/deployments) voor meer informatie.
>
>
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>    export const graphConfig = {
>        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me"
>    };
> ```
>
>
#### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

Het project uitvoeren met een webserver met behulp van Node.js:

1. Voer de volgende opdracht uit vanuit de projectmap om de server te starten:
    ```console
    npm install
    npm start
    ```
1. Blader naar `http://localhost:3000/`.

1. Selecteer **Aanmelden** om het aanmeldingsproces te starten en roep vervolgens de Microsoft Graph API aan.

    De eerste keer dat u zich aanmeldt, wordt u gevraagd om toestemming te geven zodat de toepassing uw profiel kan openen en u kan aanmelden. Nadat u zich hebt aangemeld, klikt u op de **aanvraag profiel gegevens** om uw profiel informatie op de pagina weer te geven.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Diagram van de verificatiecodestroom voor een toepassing met één pagina.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

De MSAL.js-bibliotheek meldt zich aan bij gebruikers en vraagt de tokens aan die worden gebruikt voor toegang tot een API die wordt beveiligd door het micro soft Identity-platform.

Als Node.js is geïnstalleerd, kunt u de nieuwste versie downloaden met behulp van Node.js Package Manager (npm):

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende zelf studie voor een gedetailleerde stapsgewijze hand leiding voor het bouwen van de toepassing voor de verificatie code stroom met behulp van vanille java script:

> [!div class="nextstepaction"]
> [Zelfstudie om u aan te melden en MS Graph aan te roepen](./tutorial-v2-javascript-auth-code.md)