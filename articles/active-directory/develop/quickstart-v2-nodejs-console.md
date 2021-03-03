---
title: 'Snelstartgids: Microsoft Graph aanroepen vanuit een Node.js-console-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze Snelstartgids downloadt en voert u een code voorbeeld uit die laat zien hoe een Node.js-console toepassing een toegangs token kan ophalen en een API aanroept die wordt beveiligd door een micro soft Identity platform-eind punt, met behulp van de eigen identiteit van de app
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653742"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Node.js console-app met behulp van de identiteit van de app

In deze Snelstartgids downloadt en voert u een code voorbeeld uit die laat zien hoe een Node.js-console toepassing een toegangs token kan krijgen met behulp van de identiteit van de app om de Microsoft Graph-API aan te roepen en een [lijst met gebruikers](/graph/api/user-list) in de Directory weer te geven. Het codevoorbeeld laat zien hoe een taak of Windows-service zonder toezicht kan worden uitgevoerd met een toepassings-id, in plaats van een gebruikers-id.

Deze Snelstartgids maakt gebruik van de [micro soft-verificatie bibliotheek voor Node.js (MSAL-knoop punt)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) met de [client referenties verlenen](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>De voorbeeld toepassing registreren en downloaden
>
> Volg de onderstaande stappen om aan de slag te gaan.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Stap 1: Registreer de toepassing
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
> 1. Selecteer onder **Clientgeheimen** de optie **Nieuw clientgeheim**. Voer een naam in en selecteer vervolgens **Toevoegen**. Noteer de waarde voor het geheim op een veilige locatie, voor gebruik in een latere stap.
> 1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**. Selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder het knooppunt **Gebruiker** de optie **User.Read.All**. Selecteer vervolgens **Machtigingen toevoegen**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>De voor beeld-app downloaden en configureren
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Stap 1: de toepassing configureren in Azure Portal
> Om ervoor te zorgen dat het codevoorbeeld voor deze quickstart werkt, moet u een clientgeheim maken en de toepassingstoestemming **User.Read.All** van Graph API toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Stap 2: het voorbeeld project van Node.js downloaden

> [!div renderon="docs"]
> [Het codevoorbeeld downloaden](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Stap 3: het Node.js-voorbeeld project configureren
>
> 1. Pak het zip-bestand uit naar een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld *C:/Azure-samples*.
> 1. Bewerk *. env* en vervang de waarden van de velden `TENANT_ID` , `CLIENT_ID` en `CLIENT_SECRET` met het volgende code fragment:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Waar:
>    - `Enter_the_Application_Id_Here` -is de **toepassings-id (client)** van de toepassing die u eerder hebt geregistreerd. Deze ID vindt u in het deel venster **overzicht** van de app-registratie in de Azure Portal.
>    - `Enter_the_Tenant_Id_Here` -Vervang deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld contoso.Microsoft.com).  Deze waarden vindt u in het deel venster **overzicht** van de app-registratie in de Azure Portal.
>    - `Enter_the_Client_Secret_Here` -Vervang deze waarde door het client geheim dat u eerder hebt gemaakt. Als u een nieuwe sleutel wilt genereren, gebruikt u **certificaten & geheimen** in de instellingen voor app-registratie in de Azure Portal.
>
> > [!WARNING]
> > Een geheim met lees bare tekst in de bron code vormt een groter beveiligings risico. In dit artikel wordt een client geheim met lees bare tekst gebruikt voor alleen eenvoud. Gebruik [certificaat referenties](active-directory-certificate-credentials.md) in plaats van client geheimen in uw vertrouwelijke client toepassingen, met name de apps die u wilt implementeren voor productie.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Stap 3: toestemming van de beheerder

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Stap 4: toestemming van de beheerder

Als u op dit moment probeert de toepassing uit te voeren, krijgt u de foutmelding *HTTP 403: verboden*: `Insufficient privileges to complete the operation`. Deze fout treedt op omdat de *machtiging alleen* door de **beheerder** is vereist: een globale beheerder van uw directory moet toestemming geven aan uw toepassing. Selecteer een van de opties hieronder, afhankelijk van uw rol:

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale Tenant beheerder bent, gaat u naar de pagina **API-machtigingen** in de registratie van de Azure portal van de toepassing en selecteert u **toestemming van beheerder geven voor {Tenant naam}** (waarbij {Tenant naam} staat voor de naam van uw map).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **Beheerder toestemming verlenen voor Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaard gebruiker bent van uw Tenant, moet u een globale beheerder vragen om toestemming van de **beheerder** voor uw toepassing te verlenen. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Waar:
>> * `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **Tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

Zoek de hoofdmap van het voor beeld (waar `package.json` bevindt zich) in een opdracht prompt of-console. U moet de afhankelijkheden van dit voor beeld eenmaal installeren:

```console
npm install
```

Voer dan de toepassing uit via de opdrachtprompt of console:

```console
node . --op getUsers
```

U ziet in de console-uitvoer een bepaald JSON-fragment dat een lijst met gebruikers in uw Azure AD-Directory weergeeft.

## <a name="about-the-code"></a>Over de code

Hieronder worden enkele belang rijke aspecten van de voorbeeld toepassing besproken.

### <a name="msal-node"></a>MSAL Node

[MSAL knoop punt](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt om toegang te krijgen tot een API die wordt beveiligd door micro soft Identity platform. Zoals beschreven, verzoekt deze Quick Start tokens op basis van toepassings machtigingen (met behulp van de eigen identiteit van de toepassing) in plaats van gedelegeerde machtigingen. De verificatie stroom die in dit geval wordt gebruikt, wordt ook wel [OAuth 2,0-client referenties stroom](v2-oauth2-client-creds-grant-flow.md)genoemd. Zie [scenario: daemon-toepassing](scenario-daemon-overview.md)voor meer informatie over het gebruik van het MSAL-knoop punt met daemon-apps.

 U kunt MSAL-knoop punt installeren door de volgende NPM-opdracht uit te voeren.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```javascript
const msal = require('@azure/msal-node');
```

Vervolgens initialiseert u MSAL met de volgende code:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Waar: |Beschrijving |
> |---------|---------|
> | `clientId` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |
> | `authority`    | Het STS-eindpunt voor de gebruiker voor verificatie. Dat is meestal `https://login.microsoftonline.com/{tenant}` voor de openbare cloud, waarbij {tenant} de naam van uw tenant of uw tenant-id is.|
> | `clientSecret` | Het clientgeheim is dat voor de toepassing in Azure-portal wordt gemaakt. |

Zie de [naslagdocumentatie voor `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) voor meer informatie

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `acquireTokenByClientCredential`-methode:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Waar:| Beschrijving |
> |---------|---------|
> | `tokenRequest` | De aangevraagde bereiken bevat. Voor vertrouwelijke clients moet hiervoor de indeling worden gebruikt die vergelijkbaar is met `{Application ID URI}/.default` om aan te geven dat de aangevraagde bereiken dezelfde zijn die statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure-portal (voor Microsoft Graph verwijst `{Application ID URI}` naar `https://graph.microsoft.com`). Voor aangepaste web-Api's `{Application ID URI}` wordt gedefinieerd onder **een API** weer geven in de toepassings registratie van Azure Portal. |
> | `tokenResponse` | Het antwoord bevat een toegangs token voor de aangevraagde bereiken. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de zelf studie voor meer informatie over het ontwikkelen van daemon/console-apps met MSAL-knoop punt:

> [!div class="nextstepaction"]
> [Een daemon-app die web-API's aanroept](tutorial-v2-nodejs-console.md)