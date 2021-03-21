---
title: 'Zelfstudie: EenNode.js-web-API beveiligen met Azure AD B2C en toegang verlenen tot een toepassing met één pagina (SPA)'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Active Directory B2C gebruikt om een Node.js-web-API te beveiligen en aan te roepen vanuit een toepassing met één pagina.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421087"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Zelfstudie: Een Node.js-web-API beveiligen en toegang verlenen vanuit een toepassing met één pagina met Azure AD B2C

Deze zelfstudie laat zien hoe u een met Azure Active Directory B2C (Azure AD B2C) beveiligde Node.js-web-API vanuit een toepassing met één pagina kunt aanroepen.

Deze zelfstudie, de tweede in een tweedelige serie, bevat:

> [!div class="checklist"]
> * Een web-API-toepassingsregistratie maken in uw Azure AD B2C-tenant
> * Bereiken voor de web-API configureren
> * Machtigingen verlenen aan de web-API
> * Een web-API-codevoorbeeld wijzigen voor samenwerking met uw tenant

In de [eerste zelfstudie](tutorial-single-page-app.md) in deze reeks hebt u het codevoorbeeld gedownload en gewijzigd om gebruikers aan te melden met een gebruikersstroom in uw Azure AD B2C-tenant.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Voltooi de stappen en de vereisten in [Zelfstudie: Verificatie inschakelen in een toepassing met één pagina met Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) of een andere code-editor
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie gaat u lees- en schrijfmachtigingen definiëren voor de web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder **Bereiken** voor het bereik `demo.read` voor gebruik in een latere stap wanneer u de toepassing met één pagina configureert. De volledige bereikwaarde is vergelijkbaar met `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een andere toepassing, moet u uw toepassing machtigingen geven voor de web-API.

In de vereiste zelfstudie heft u een toepassing met één pagina met de naam *spaapp1* gemaakt. In deze zelfstudie configureert u die toepassing om de web-API aan te roepen die u in een vorige sectie hebt gemaakt, *spaapp1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Aan uw webtoepassing met één pagina zijn nu machtigingen voor de beveiligde web-API verleend voor de opgegeven bereiken. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing met één pagina te kunnen gebruiken. De app met één pagina verkrijgt een toegangstoken van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Voorbeeld configureren

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om deze met uw Azure AD B2C-tenant te kunnen laten werken. In deze zelfstudie gaat u een Node.js web-API configureren die u kunt downloaden op GitHub.

[Download een \*.zip-archief](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon het web-API-voorbeeldproject op GitHub. U kunt ook rechtstreeks naar het project [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) op GitHub bladeren.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open het bestand *config. json* in de code-editor.
1. Wijzig de variabele waarden zodat deze overeenkomen met die van de registratie van de toepassing die u eerder hebt gemaakt. Werk ook `policyName` bij met de gebruikersstroom die u hebt gemaakt als onderdeel van de vereisten. Bijvoorbeeld *B2C_1_signupsignin1*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>CORS inschakelen

Schakel [CORS](https://expressjs.com/en/resources/middleware/cors.html) in de web-API in zodat de toepassing met één pagina de Node.js-web-API kan aanroepen. In een productietoepassing moet u opletten welk domein de aanvraag doet, maar in deze zelfstudie worden aanvragen van elk domein toegestaan.

Gebruik de volgende middleware om CORS in te schakelen. In de voorbeeldcode van de Node.js-web-API in deze zelfstudie is deze al toegevoegd aan het bestand *index.js*.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>De toepassing met één pagina configureren

De toepassing met één pagina (SPA) uit de [vorige zelfstudie](tutorial-single-page-app.md) in de reeks maakt gebruik van Azure AD B2C voor het registreren en aanmelden van gebruikers, en roept standaard de Node.js-web-API aan die door de demotenant *fabrikamb2c* wordt beveiligd.

In deze sectie werkt u de webtoepassing met één pagina bij om de Node.js-web-API aan te roepen die wordt beveiligd door *uw* Azure AD B2C-tenant (en die u op uw lokale computer uitvoert).

Instellingen in de SPA wijzigen:

1. Open in het project [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa], dat u in de vorige zelfstudie hebt gedownload of gekloond, het bestand *apiConfig.js* in de map *JavaScriptSPA*.
1. Configureer het voorbeeld met de URI voor het bereik *demo.read* dat u eerder hebt gemaakt en de URL van de web-API.
    1. Vervang in de `apiConfig`-definitie de `b2cScopes`-waarde door de volledige URI voor het bereik *demo.read* (de waarde **Bereik** die u eerder hebt vastgelegd).
    1. Wijzig het domein in de `webApi`-waarde in de omleidings-URI die u hebt toegevoegd toen u de web-API-toepassing in een eerdere stap hebt geregistreerd.

    De API is toegankelijk is op het `/hello`-eindpunt, dus laat */hello* in de URI staan.

    De `apiConfig`-definitie moet er ongeveer uitzien als in het volgende codeblok, maar met de naam van uw B2C-tenant op de plaats van `<your-tenant-name>`:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>De SPA en web-API uitvoeren

U bent nu klaar om de toegang gerelateerd aan een bereik van de toepassing met één pagina tot de API testen. Voer de Node.js-web-API en het JavaScript-toepassingsvoorbeeld met één pagina op de lokale computer uit. Meld u vervolgens aan bij de toepassing met één pagina en selecteer de knop **Aanroep-API** om een aanvraag naar de beveiligde API te initiëren.

Hoewel beide toepassingen lokaal worden uitgevoerd wanneer u deze zelfstudie volgt, hebt u deze geconfigureerd voor het gebruik met Azure AD B2C voor veilig registreren /aanmelden en het verlenen van toegang tot de beveiligde web-API.

### <a name="run-the-nodejs-web-api"></a>Voer de web-API van Node.js uit

1. Open een consolevenster en ga naar de map met het Node.js-web-API-voorbeeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    node index.js
    ```

    Het consolevenster geeft het poortnummer waar de app wordt gehost.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>De app met één pagina uitvoeren

1. Open een ander consolevenster en ga naar de map met het JavaScript SPA-voorbeeld. Bijvoorbeeld:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    npm start
    ```

    Het consolevenster geeft het poortnummer van waar de app wordt gehost.

    ```console
    Listening on port 6420...
    ```

1. Ga in de browser naar `http://localhost:6420` om de toepassing te bekijken.

    ![Voorbeeld-app met één pagina weergegeven in de browser](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Meld u aan met het e-mailadres en wachtwoord dat u in de [vorige zelfstudie](tutorial-single-page-app.md) hebt gebruikt. Na aanmelden wordt het bericht `User 'Your Username' logged-in` weergegeven.
1. Klik op de knop **API aanroepen**. De SPA verkrijgt een autorisatietoekenning van Azure AD B2C en opent vervolgens de beveiligde web-API om de naam van de aangemelde gebruiker weer te geven:

    ![Toepassing met één pagina in de browser met het JSON-resultaat met de gebruikersnaam die door de API is geretourneerd](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een web-API-toepassingsregistratie in uw Azure AD B2C-tenant gemaakt
> * Bereiken voor de web-API geconfigureerd
> * Machtigingen aan de web-API verleend
> * Een web-API-codevoorbeeld gewijzigd voor samenwerking met uw tenant

U hebt gezien hoe een SPA een resource heeft aangevraagd vanuit een beveiligde web-API. U kunt nu nog meer inzicht krijgen in hoe deze toepassingstypen met elkaar en met Azure AD B2C werken.

> [!div class="nextstepaction"]
> [Toepassingstypen die kunnen worden gebruikt in Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
