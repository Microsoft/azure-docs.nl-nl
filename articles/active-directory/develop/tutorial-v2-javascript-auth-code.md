---
title: 'Zelfstudie: Een JavaScript-app met één pagina maken die gebruikmaakt van de verificatiecodestroom | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie maakt u een JavaScript-app met één pagina waarmee gebruikers kunnen worden aangemeld en de verificatiecodestroom kan worden gebruikt om een toegangstoken van het Microsoft-identiteitsplatform te verkrijgen en de Microsoft Graph-API aan te roepen.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 79fe821e2da494ab8c9e4cb407e2c2b025f75568
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169100"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Zelfstudie: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een JavaScript-app met één pagina met behulp van autorisatiecodestroom

In deze zelfstudie bouwt u een JavaScript-toepassing met één pagina (SPA) die zich aanmeldt bij gebruikers en die Microsoft Graph aanroepen met behulp van de autorisatiecodestroom met PKCE. De SPA die u bouwt, maakt gebruik van de Microsoft Authentication Library (MSAL) voor JavaScript v2.0.

In deze zelfstudie hebt u het volgende gedaan:
> [!div class="checklist"]
> * De OAuth 2.0-autorisatiecodestroom uitvoeren met PKCE
> * Zowel werk- en schoolaccounts als persoonlijke Microsoft-accounts aanmelden
> * Een toegangstoken verkrijgen
> * Roep Microsoft Graph aan of uw eigen API die toegangstokens vereist die zijn verkregen van het Microsoft identiteitsplatform-eindpunt

MSAL.js 2.0 verbetert MSAL.js 1.0 door de autorisatiecodestroom in de browser te ondersteunen in plaats van de impliciete toekenningsstroom. MSAL.js 2.0 biedt **GEEN** ondersteuning voor de impliciete stroom.

## <a name="prerequisites"></a>Vereisten

* [Node.js-](https://nodejs.org/en/download/) voor het uitvoeren van een lokale webserver
* [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

## <a name="how-the-tutorial-app-works"></a>Hoe de zelfstudie-app werkt

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagram van de autorisatiecodestroom voor een toepassing met één pagina":::

Met de toepassing die u in deze zelfstudie maakt, kunt u een JavaScript-SPA uitvoeren op de Microsoft Graph API door beveiligingstokens van het Microsoft identiteitsplatform-eindpunt te verkrijgen. Wanneer een gebruiker zich aanmeldt, wordt in dit scenario een toegangstoken aangevraagd en toegevoegd aan HTTP-aanvragen in de autorisatie-header. Het ophalen en vernieuwen van tokens worden verwerkt door de Microsoft Authentication Library voor JavaScript (MSAL.js).

Deze zelfstudie maakt gebruik van de volgende bibliotheek:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) Microsoft Authentication Library voor JavaScript v 2.0-browserpakket

## <a name="get-the-completed-code-sample"></a>Het voltooide codevoorbeeld ophalen

Wilt u in plaats daarvan het voltooide voorbeeldproject van deze zelfstudie downloaden? Als u het project wilt uitvoeren met behulp van een lokale webserver, zoals node.js, kloont u de [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)-opslagplaats:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Ga verder met de [configuratiestap](#register-your-application) om het codevoorbeeld te configureren vóórdat u het uitvoert.

Als u wilt doorgaan met de zelfstudie en de toepassing zelf wilt maken, gaat u naar de volgende sectie, [Vereisten](#prerequisites).

## <a name="create-your-project"></a>Uw project maken

Nadat u [Node.js](https://nodejs.org/en/download/) hebt geïnstalleerd, maakt u een map om uw toepassing te hosten, bijvoorbeeld *msal-spa-tutorial*.

Implementeer vervolgens een kleine [Express](https://expressjs.com/)-webserver om uw *index.html*-bestand te leveren.

1. Ga eerst naar de projectmap in uw terminal en voer de volgende `npm`-opdrachten uit:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Maak vervolgens het bestand met de naam *server.js* en voeg de volgende code toe:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

U hebt nu een kleine webserver voor uw SPA. Nadat u de rest van de zelfstudie hebt voltooid, moeten de bestands- en mapstructuur van het project er ongeveer als volgt uitzien:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Een SPA-gebruikersinterface maken

1. Maak een *app*-map in de projectmap en maak een *index.html*-bestand voor uw JavaScript-SPA. Dit bestand implementeert een UI die is gebouwd met het **Bootstrap 4 Framework** en importeert scriptbestanden voor configuratie, verificatie en API-aanroepen.

    Voeg in het bestand *index. html* de volgende code toe:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
          </div>
        </div>
        </div>
        <br>
        <br>
          <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
          </div>
          <div class="col-md-5">
            <div class="tab-content" id="nav-tabContent">
            </div>
          </div>
        </div>
        <br>
        <br>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Maak vervolgens in de *app*-map een bestand met de naam *ui.js* en voeg de volgende code toe. Met dit bestand worden DOM-elementen geopend en bijgewerkt.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Uw toepassing registreren

Volg de stappen in [Toepassing met één pagina: App-registratie](scenario-spa-app-registration.md) voor het maken van een app-registratie voor uw SPA.

In de [omleidings-URI: MSAL.js 2.0 met verificatiecodestroom](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)-stap, voer `http://localhost:3000` in, de standaardlocatie waar de toepassing van deze zelfstudie wordt uitgevoerd.

Als u een andere poort wilt gebruiken, voert u `http://localhost:<port>` in, waarbij `<port>` het voorkeurs-TCP-poortnummer is. Als u een ander poortnummer dan `3000`opgeeft, moet u ook *server.js* met uw voorkeurspoortnummer bijwerken.

### <a name="configure-your-javascript-spa"></a>Uw JavaScript-SPA configureren

Maak een bestand met de naam *authConfig.js* in de map *app* om uw configuratieparameters voor verificatie op te nemen en voeg vervolgens de volgende code toe:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Wijzig de waarden in de sectie `msalConfig`, zoals hier wordt beschreven:

- `Enter_the_Application_Id_Here`: De **Id van de toepassing (client)** van de toepassing die u hebt geregistreerd.
- `Enter_the_Cloud_Instance_Id_Here`: Het Azure-cloudexemplaar waarin uw toepassing is geregistreerd.
  - Voer `https://login.microsoftonline.com` in voor de hoofd- (of *globale*) Azure-cloud.
  - Voor **nationale** clouds (bijvoorbeeld China) kunt u de juiste waarden vinden in [Nationale clouds](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` een van de volgende waarden zijn:
  - Als uw toepassing ondersteuning biedt voor *accounts in deze organisatiemap*, vervang deze waarde dan door de **Tenant-id** of **Tenantnaam**. Bijvoorbeeld `contoso.microsoft.com`.
  - Als uw toepassing ondersteuning biedt voor *accounts in elke organisatiemap*, vervangt u waarde door `organizations`.
  - Als uw toepassing *accounts in elke organisatiemap en persoonlijke Microsoft-accounts* ondersteunt, vervang deze waarde dan door `common`.
  - Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervang deze waarde dan door `consumers`.
- `Enter_the_Redirect_Uri_Here` is `http://localhost:3000`.

De waarde `authority` in uw *authConfig.js* moet er als volgt uitzien als u de wereldwijde Azure-cloud gebruikt:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Maak nog in de map *app* een bestand met de naam *graphConfig.js*. Voeg de volgende code toe om uw toepassing de configuratieparameters te geven voor het aanroepen van de Microsoft Graph API:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Wijzig de waarden in de sectie `graphConfig`, zoals hier wordt beschreven:

- `Enter_the_Graph_Endpoint_Here` is het exemplaar van de Microsoft Graph API waarmee de toepassing moet communiceren.
  - Vervang beide exemplaren van deze teken reeks door `https://graph.microsoft.com` voor het **wereldwijde** Microsoft Graph API-eindpunt.
  - Zie [Nationale cloudimplementaties](/graph/deployments) in de Microsoft Graph-documentatie voor eindpunten in **nationale** cloudimplementaties.

De waarden `graphMeEndpoint` en `graphMailEndpoint` in uw *authConfig.js* moeten er als volgt uitzien als u het wereldwijde eindpunt gebruikt:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>Microsoft Authentication Library (MSAL) gebruiken om de gebruiker aan te melden

### <a name="pop-up"></a>Pop-up

Maak in de map van de *app* een bestand met de naam *authPopup.js* en voeg de volgende code toe voor het verkrijgen van een verificatie en token voor de pop-up van de aanmelding:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Omleiden

Maak een bestand met de naam *authRedirect.js* in de map van de *app* en voeg de volgende code toe voor het verkrijgen van een verificatie en token toe voor het omleiden van de aanmelding:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Hoe de code werkt

Wanneer een gebruiker de knop **Aanmelden** voor de eerste keer selecteert, roept de methode `signIn` `loginPopup` aan om zich aan te melden bij de gebruiker. Met de methode `loginPopup` wordt een pop-upvenster geopend met het *Microsoft-identiteitsplatform-eindpunt* om de referenties van de gebruiker te vragen en te valideren. Na een geslaagde aanmelding, initieert *msal.js* de [autorisatiecodestroom](v2-oauth2-auth-code-flow.md).

Op dit moment wordt een met PKCE beveiligde autorisatiecode verzonden naar het eindpunt van het met CORS beveiligde token en wordt deze uitgewisseld voor tokens. Er worden een ID-token, toegangstoken en vernieuwingstoken ontvangen door uw toepassing en verwerkt door *msal.js*, en de gegevens in de tokens worden in de cache opgeslagen.

Het ID-token bevat basisinformatie over de gebruiker, zoals de weergavenaam. Als u van plan bent om gegevens te gebruiken die worden verstrekt door het ID-token, *moet* uw back-endserver dit valideren om te garanderen dat het token is uitgegeven aan een geldige gebruiker voor uw toepassing.

Het toegangstoken heeft een beperkte levensduur en verloopt na 24 uur. Het vernieuwingstoken kan worden gebruikt om nieuwe toegangstokens op de achtergrond te verkrijgen.

De SPA die u in deze zelfstudie hebt gemaakt, roept `acquireTokenSilent` en/of `acquireTokenPopup` aan om een *toegangstoken te verkrijgen* dat wordt gebruikt om een query uit te voeren op de Microsoft Graph API voor profielgegevens van gebruikers. Als u een voorbeeld nodig hebt dat het ID-token valideert, raadpleegt u de voorbeeldtoepassing [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) op GitHub. In het voorbeeld wordt een ASP.NET-web-API gebruikt voor het valideren van tokens.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Na de eerste aanmelding moet uw app gebruikers niet meer vragen om opnieuw te verifiëren voor elke keer dat ze toegang moeten krijgen tot een beveiligde bron (dat wil zeggen, om een token aan te vragen). Roep `acquireTokenSilent` aan om dergelijke nieuwe verificatieverzoeken te voorkomen. Er zijn echter een aantal situaties waarin u mogelijk wilt afdwingen dat gebruikers met het Microsoft-identiteitsplatform-eindpunt kunnen communiceren. Bijvoorbeeld:

- Gebruikers moeten mogelijk hun referenties opnieuw opgeven omdat het wachtwoord is verlopen.
- Uw toepassing vraagt toegang tot een resource waarvoor de gebruiker toestemming moet geven.
- Verificatie in twee stappen is vereist.

Als u `acquireTokenPopup` aanroept, wordt een pop-upvenster geopend (of `acquireTokenRedirect` gebruikers omgeleid naar het Microsoft-identiteitsplatform-eindpunt). In dat venster moeten gebruikers hun referenties bevestigen, toestemming geven voor de vereiste resource of verificatie in wee stappen uitvoeren.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `acquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat de methode `loginPopup` (of `loginRedirect`) de eerste keer is uitgevoerd, wordt voor volgende aanroepen meestal de methode `acquireTokenSilent` gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources. (Aanroepen naar aanvraag- of vernieuwingstokens worden op de achtergrond uitgevoerd.) `acquireTokenSilent` kan in sommige gevallen mislukken. Het is bijvoorbeeld mogelijk dat het wachtwoord van de gebruiker is verlopen. Uw toepassing kan deze uitzondering op twee manieren afhandelen:

1. Een aanroep naar `acquireTokenPopup` onmiddellijk uitvoeren om een aanmeldingsprompt van een gebruiker te activeren. Dit patroon wordt vaak gebruikt in online toepassingen waarbij er geen niet-geverifieerde inhoud in de toepassing beschikbaar is voor de gebruiker. Het voorbeeld dat door deze begeleide installatie wordt gegenereerd, maakt gebruik van dit patroon.
1. Visueel aan de gebruiker aangeven door dat een interactieve aanmelding is vereist, zodat de gebruiker de juiste tijd kan selecteren om zich aan te melden, of de toepassing kan `acquireTokenSilent` op een later tijdstip opnieuw proberen. Deze techniek wordt vaak gebruikt wanneer de gebruiker andere functionaliteit van de toepassing kan gebruiken zonder te worden onderbroken. Het is bijvoorbeeld mogelijk dat er niet-geverifieerde inhoud beschikbaar is in de toepassing. In dit geval kan de gebruiker beslissen wanneer hij of zij zich wil aanmelden om toegang te krijgen tot de beveiligde resource, of om de verouderde informatie te vernieuwen.

> [!NOTE]
> In deze zelfstudie wordt standaard gebruikgemaakt van de methoden `loginPopup` en `acquireTokenPopup`. Als u Internet Explorer gebruikt, raden we u aan de methoden `loginRedirect` en `acquireTokenRedirect` te gebruiken vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met Internet Explorer en pop-upvensters. Zie [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) op GitHub voor een voorbeeld van het bereiken van hetzelfde resultaat met behulp van omleidingsmethoden.

## <a name="call-the-microsoft-graph-api"></a>De Microsoft Graph API aanroepen

Maak een bestand met de naam *Graph.js* in de map van de *app* en voeg de volgende code toe om REST-aanroepen naar de Microsoft Graph API te brengen:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

In de voorbeeldtoepassing die in deze zelfstudie wordt gemaakt, wordt de methode `callMSGraph()` gebruikt om een HTTP `GET`-aanvraag uit te voeren voor een beveiligde bron waarvoor een token is vereist. De aanvraag retourneert vervolgens de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd in de *HTTP-autorisatie-header*. In de voorbeeldtoepassing die in deze zelfstudie is gemaakt, is de beveiligde resource het Microsoft Graph API *me*-eindpunt waarin de profielgegevens van de aangemelde gebruiker worden weergegeven.

## <a name="test-your-application"></a>Uw toepassing testen

Het maken van de toepassing is voltooid en u bent nu klaar om de Node.js-webserver te starten en de functionaliteit van de app te testen.

1. Start de Node.js-webserver door de volgende opdracht uit te voeren in de hoofdmap van de projectmap:

   ```console
   npm start
   ```
1. Ga in uw browser naar `http://localhost:3000` of `http://localhost:<port>`, waarbij `<port>` de poort is, waarop uw webserver luistert. U moet de inhoud van uw bestand *index.html* en de knop **Aanmelden** zien.

### <a name="sign-in-to-the-application"></a>Aanmelden bij Azure Portal

Nadat de browser het bestand *index.html* heeft geladen, selecteert u **Aanmelden**. U wordt gevraagd om u aan te melden met het Microsoft-identiteitsplatform-eindpunt:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Webbrowser waarin het dialoogvenster voor de aanmelding wordt weergegeven":::

### <a name="provide-consent-for-application-access"></a>Toestemming voor toegang tot de toepassing geven

De eerste keer dat u zich aanmeldt bij uw toepassing, wordt u gevraagd om deze toegang te geven tot uw profiel en om u aan te melden:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Dialoogvenster inhoud weergegeven in webbrowser":::

Als u akkoord gaat met de aangevraagde machtigingen, wordt in de webtoepassingen uw gebruikersnaam weer gegeven, met een geslaagde aanmelding:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Resultaten van een geslaagde aanmelding in de webbrowser":::

### <a name="call-the-graph-api"></a>De Graph API aanroepen

Nadat u zich hebt aangemeld, selecteert u **Profiel bekijken** om de profielgegevens van de gebruiker weer te geven die zijn geretourneerd in het antwoord van de aanroep van de Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Profielgegevens van Microsoft Graph weergegeven in de browser":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

De Microsoft Graph API vereist het bereik *user.read* om het profiel van een gebruiker te lezen. Dit bereik wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in de Azure-portal. Overige API's voor Microsoft Graph, evenals aangepaste API's voor uw back-endserver, vereisen mogelijk aanvullende bereiken. De Microsoft Graph API vereist bijvoorbeeld het bereik *Mail.Read* om de e-mail van de gebruiker op te sommen.

Wanneer u scopes toevoegt, kunnen uw gebruikers worden gevraagd om extra toestemming te geven voor de toegevoegde scopes.

Als voor een back-end-API geen bereik is vereist, wat niet aanbevolen wordt, kunt u `clientId` gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het ontwikkelen van JavaScript-toepassingen met één pagina op het Microsoft-identiteitsplatform onze reeks scenario's:

> [!div class="nextstepaction"]
> [Scenario: Toepassing met één pagina](scenario-spa-overview.md)