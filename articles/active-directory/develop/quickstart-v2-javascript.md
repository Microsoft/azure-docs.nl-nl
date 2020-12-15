---
title: 'Snelstart: Gebruikers aanmelden in JavaScript-apps met één pagina | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart vindt u meer informatie over hoe u met een JavaScript-app een API kunt aanroepen waarvoor toegangstokens zijn vereist die zijn verstrekt door het Microsoft-identiteitsplatform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 1d6f04b0d6dec06954c594434ac7a9db62f0c89e
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762785"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Quickstart: Gebruikers aanmelden en een toegangstoken verkrijgen in een JavaScript SPA

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld en de Microsoft Graph API kan worden aangeroepen met een SPA-toepassing (JavaScript-toepassing met één pagina). Het codevoorbeeld laat ook zien hoe u een toegangstoken kunt krijgen om de Microsoft Graph API of willekeurige web-API aan te roepen.

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (om projectbestanden te bewerken)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>De quickstart-toepassing registreren en downloaden
> Gebruik een van de volgende opties om uw quickstart-app te starten.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1 (Express): registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang hebt tot meerdere tenants, selecteert u het account in de rechterbovenhoek en stelt u de portalsessie in op de Azure Active Directory-tenant die u wilt gebruiken.
> 1. Ga naar de nieuwe [Azure-portal - App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2 (handmatig): registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** voor later gebruik.
> 1. Voor deze quickstart moet de [Impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld. Selecteer in het linkerdeelvenster van de geregistreerde toepassing de optie **Verificatie**.
> 1. Selecteer **Een platform toevoegen** onder **Platformconfiguraties**. Er opent een venster aan de linkerkant. Selecteer daar de regio **Webtoepassingen**.
> 1. Stel links de waarde van **Omleidings-URI** in op `http://localhost:3000/`. Selecteer vervolgens **Toegangstoken** en **Id-token**.
> 1. Selecteer **Configureren**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> U moet een `redirectUri` als `http://localhost:3000/` toevoegen en **Impliciete toekenning** inschakelen voor een juiste werking van het codevoorbeeld uit deze quickstart.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

> [!div renderon="docs"]
> Als u het project wilt uitvoeren met een webserver met behulp van Node.js, [downloadt u de kernprojectbestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Het project uitvoeren met een webserver met behulp van Node.js

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Stap 3: Configureer de JavaScript-app
>
> Bewerk *authConfig.js* en stel de waarden `clientID`, `authority` en `redirectUri` in onder `msalConfig` in de map *JavaScriptSPA*.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Waar:
> - *\<Enter_the_Application_Id_Here>* is de **Toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
> - *\<Enter_the_Cloud_Instance_Id_Here>* is de instantie van de Azure-cloud. Voer *https://login.microsoftonline.com* in voor de hoofd- (of globale) Azure-cloud. Zie [Nationale clouds](./authentication-national-cloud.md) voor **nationale** clouds (bijvoorbeeld China).
> - *\<Enter_the_Tenant_info_here>* is ingesteld op een van de volgende opties:
>    - Als uw toepassing *accounts in deze organisatiemap* ondersteunt, vervangt u deze waarde door de **tenant-id** of **tenantnaam** (bijvoorbeeld *contoso.microsoft.com*).
>    - Als uw toepassing *accounts in elke organisatiemap* ondersteunt, vervang deze waarde dan door **organisaties**.
>    - Als uw toepassing *accounts in elke organisatiemap en persoonlijke Microsoft-accounts* ondersteunt, vervang deze waarde dan door **algemeen**. Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervang deze waarde dan door **consumenten**.
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** , **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app.

> [!div renderon="docs"]
>
> Bewerk vervolgens in dezelfde map het bestand *graphConfig.js* en om de waarden `graphMeEndpoint` en `graphMeEndpoint` in te stellen voor het object `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
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
> Waar:
> - *\<Enter_the_Graph_Endpoint_Here>* is het eindpunt waarop API-aanroepen worden geplaatst. Voer `https://graph.microsoft.com` in voor de voornaamste of globale Microsoft Graph API-service. Zie [Implementatie van nationale clouds](/graph/deployments) voor meer informatie.
>
> #### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

Het project uitvoeren met een webserver met behulp van [Node.js](https://nodejs.org/en/download/):

1. Voer de volgende opdracht uit vanuit de projectmap om de server te starten:
    ```cmd
    npm install
    npm start
    ```
1. Open een webbrowser en ga naar `http://localhost:3000/`.

1. Selecteer **Aanmelden** om de aanmelding te starten en roep vervolgens Microsoft Graph API aan.

Nadat de toepassing door de browser is geladen, selecteert u **Aanmelden**. De eerste keer dat u zich aanmeldt, wordt u gevraagd om toestemming te geven zodat de toepassing uw profiel kan openen en u kan aanmelden. Nadat u bent aangemeld, worden de gegevens van uw gebruikersprofiel weergegeven op de pagina.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Hoe het JavaScript SPA-voorbeeld werkt: 1. De SPA start de aanmelding. 2. De SPA verkrijgt een id-token van het Microsoft-identiteitsplatform. 3. De SPA-aanroepen verkrijgen een token. 4. Het Microsoft-identiteitsplatform retourneert een toegangstoken aan de SPA. 5. De SPA maakt met de toegangstoken een HTTP GET-aanvraag aan de Microsoft Graph API. 6. De Graph API retourneert een HTTP-antwoord naar de SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

De MSAL-bibliotheek meldt gebruikers aan en verzoekt dat tokens worden gebruikt voor toegang tot een API die wordt beveiligd door het Microsoft Identity-platform. Het bestand *index.html* van de quickstart bevat een referentie naar de bibliotheek:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> U de voorgaande versie vervangen door de meest recente versie onder [MSAL.js releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Als Node.js is geïnstalleerd, kunt u de nieuwste versie downloaden via Node.js Package Manager (npm):

```cmd
npm install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren

De quickstart-code laat ook zien hoe u de MSAL-bibliotheek kunt initialiseren:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Waar  | Beschrijving |
> |---------|---------|
> |`clientId`     | De toepassings-id van de toepassing die is geregistreerd in het Azure-portal.|
> |`authority`    | (Optioneel) De URL van de instantie die accounttypen ondersteunt zoals eerder beschreven in het configuratiegedeelte. De standaardinstantie is `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | De geconfigureerde reply/redirectUri van de toepassingsregistratie. In dit geval, `http://localhost:3000/`. |
> |`cacheLocation`  | (Optioneel) Stelt de browseropslag in voor de verificatiestatus. De standaardwaarde is sessionStorage.   |
> |`storeAuthStateInCookie`  | (Optioneel) De bibliotheek die de status van de verificatie-aanvraag opslaat die nodig is voor de validatie van de verificatiestromen in de browsercookies. Deze cookie is ingesteld voor IE- en Edge-browsers om bepaalde [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) te verhelpen. |

Raadpleeg [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md) voor meer informatie over beschikbare opties die u kunt configureren.

### <a name="sign-in-users"></a>Gebruikers aanmelden

In het volgende codefragment wordt weergegeven hoe u gebruikers kunt aanmelden:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Waar  | Beschrijving |
> |---------|---------|
> | `scopes`   | (Optioneel) Bevat bereiken die worden aangevraagd voor gebruikerstoestemming tijdens het aanmelden. Bijvoorbeeld, `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API’s (`api://<Application ID>/access_as_user`). |

> [!TIP]
> U kunt er ook voor kiezen om de methode `loginRedirect` te gebruiken om de huidige pagina om te leiden naar de aanmeldingspagina, in plaats van een pop-upvenster.

### <a name="request-tokens"></a>Tokens aanvragen

MSAL gebruikt drie methoden om tokens op te halen: `acquireTokenRedirect`, `acquireTokenPopup` en `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `acquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat de methode `loginRedirect` of `loginPopup` de eerste keer is uitgevoerd, wordt voor volgende aanroepen meestal de methode `acquireTokenSilent` gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources. Aanroepen voor het aanvragen of vernieuwen van tokens worden op de achtergrond uitgevoerd.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Waar  | Beschrijving |
> |---------|---------|
> | `scopes`   | Bevat bereiken die worden aangevraagd om te worden geretourneerd in het toegangstoken voor de API. Bijvoorbeeld, `[ "mail.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API’s (`api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Er zijn situaties waarin u wilt afdwingen dat gebruikers met het Microsoft-identity platform-eindpunt communiceren. Bijvoorbeeld:
* Gebruikers moeten mogelijk hun referenties opnieuw opgeven omdat het wachtwoord is verlopen.
* De toepassing vraagt toegang tot aanvullende resourcebereiken waarvoor de gebruiker toestemming moet geven.
* Verificatie in twee stappen is vereist.

Het patroon dat gewoonlijk wordt aanbevolen voor de meeste toepassingen: eerst `acquireTokenSilent` aanroepen, vervolgens de uitzondering detecteren, en daarna `acquireTokenPopup` (of `acquireTokenRedirect`) aanroepen om een interactieve aanvraag te starten.

De `acquireTokenPopup` aanroepen opent een pop-upvenster voor aanmelding. (Of `acquireTokenRedirect` leidt gebruikers om naar het eindpunt van het Microsoft-identiteitsplatform.) In dat venster moeten gebruikers hun referenties bevestigen, toestemming geven voor de vereiste resource of verificatie in wee stappen uitvoeren.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> In deze quickstart wordt gebruikgemaakt van de methoden `loginRedirect` en `acquireTokenRedirect` met Internet Explorer, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) dat te maken heeft met het verwerken van pop-upvensters in Internet Explorer.

## <a name="next-steps"></a>Volgende stappen

Voor een gedetailleerde stapsgewijze handleiding voor het bouwen van de toepassing die in deze quickstart, zie:

> [!div class="nextstepaction"]
> [Zelfstudie: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een JavaScript-toepassing met één pagina (SPA)](tutorial-v2-javascript-spa.md)
