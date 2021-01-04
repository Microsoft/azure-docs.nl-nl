---
title: 'Zelfstudie: Een Javascript-app met één pagina maken die gebruikmaakt van het Microsoft-identiteitsplatform voor verificatie | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie bouwt u een JavaScript-app met één pagina (SPA) die gebruikmaakt van het Microsoft-identiteitsplatform voor het aanmelden van gebruikers. U krijgt een toegangstoken waarmee u de Microsoft Graph API namens hen kunt aanroepen.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 4eb3c2905f3c1ccfa63da1bb4a8c81decdbc2f2b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507724"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Zelfstudie: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een JavaScript-toepassing met één pagina (SPA)

In deze zelfstudie bouwt u een JavaScript-toepassing met één pagina (SPA) die zich aanmeldt bij gebruikers en die Microsoft Graph aanroepen met impliciete stroom. De SPA die u bouwt, maakt gebruik van de Microsoft Authentication Library (MSAL) voor JavaScript v1.0.

In deze zelfstudie:

> [!div class="checklist"]
> * Een JavaScript-project maken met `npm`
> * De app registreren in Azure Portal
> * Code toevoegen voor de ondersteuning van het aan- en afmelden van gebruikers
> * Code toevoegen om de Microsoft Graph API aan te roepen
> * De app testen

>[!TIP]
> In deze zelfstudie wordt gebruikgemaakt van MSAL.js v1.x. Dit is beperkt tot het gebruik van de impliciete toekenningsstroom voor toepassingen met één pagina. Het is raadzaam in plaats daarvan voor alle nieuwe toepassingen [MSAL.js 2.x en de autorisatiecodestroom met PKCE- en CORS-ondersteuning](tutorial-v2-javascript-auth-code.md) te gebruiken.

## <a name="prerequisites"></a>Vereisten

* [Node.js-](https://nodejs.org/en/download/) voor het uitvoeren van een lokale webserver.
* [Visual Studio Code](https://code.visualstudio.com/download) of een andere editor voor het wijzigen van projectbestanden.
* Een moderne webbrowser. **Internet Explorer** wordt **niet ondersteund** door de app die u in deze zelfstudie hebt gemaakt. Dit komt door het gebruik van [ES6](http://www.ecma-international.org/ecma-262/6.0/)-conventies van de app.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voorbeeld-app werkt die wordt gegenereerd in deze gids

![Er wordt getoond hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

Met de voorbeeldtoepassing die in deze gids wordt gemaakt, kan een JavaScript-toepassing met één pagina een query uitvoeren bij de Microsoft Graph API of een web-API die tokens accepteert van het Microsoft-identity platform-eindpunt. Wanneer in dit scenario een gebruiker zich aanmeldt, wordt er een toegangstoken gevraagd en toegevoegd aan HTTP-aanvragen via de autorisatie-header. Dit token wordt gebruikt om het profiel en de e-mailberichten van de gebruiker op te halen via **MS Graph API**.

Het ophalen en vernieuwen van tokens worden verwerkt door de [Microsoft Authentication Library voor JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) .

## <a name="set-up-your-web-server-or-project"></a>Uw webserver of project instellen

> Wilt u in plaats daarvan het project van dit voorbeeld downloaden? [Download de projectbestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Ga verder met de [configuratiestap](#register-your-application) om het codevoorbeeld te configureren voordat u het uitvoert.

## <a name="create-your-project"></a>Uw project maken

Zorg ervoor dat u [Node.js](https://nodejs.org/en/download/) hebt geïnstalleerd en maak een map om uw toepassing te hosten. Daar implementeren we een eenvoudige [Express](https://expressjs.com/)-webserver die fungeert als host voor uw `index.html`-bestand.

1. Zoek de projectmap via een terminal (bijvoorbeeld de geïntegreerde terminal van Visual Studio Code) en typ vervolgens:

   ```console
   npm init
   ```

2. Installeer vervolgens de vereiste afhankelijkheden:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Maak nu een JS-bestand met de naam `server.js` en voeg de volgende code toe:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

U hebt nu een eenvoudige webserver voor uw toepassing met één pagina. Aan het einde van deze zelfstudie moet de mapstructuur er als volgt uitzien:

![een tekstuele afbeelding van de beoogde mapstructuur van de toepassing met één pagina](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Een SPA-gebruikersinterface maken

1. Maak een `index.html`-bestand voor uw JavaScript-toepassing met één pagina. Dit bestand implementeert een gebruikersinterface die is gebouwd met **Bootstrap 4 Framework** en importeert scriptbestanden voor configuratie, verificatie en API-aanroepen.

   Voeg in het bestand `index.html` de volgende code toe:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > U kunt de versie van MSAL.js in het voorgaande script vervangen door de meest recente versie onder [MSAL.js releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Maak nu een JS-bestand met de naam `ui.js`, waarmee DOM-elementen worden geopend en bijgewerkt, en voeg de volgende code toe:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

Voordat u verdergaat met de verificatie, registreert u uw toepassing op **Azure Active Directory**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Selecteer in de sectie **Omleidings-URI** het platform **Web** in de vervolgkeuzelijst en stel de waarde vervolgens in op de URL van de toepassing die is gebaseerd op uw webserver.
1. Selecteer **Registreren**.
1. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** voor later gebruik.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer in de sectie **Impliciete toekenning** de opties **Id-tokens** en **Toegangstokens**. Id-tokens en toegangstokens zijn vereist, omdat via deze app gebruikers moeten worden aangemeld en een API moet worden aangeroepen.
1. Selecteer **Opslaan**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Een omleidings-URL instellen voor Node.js
>
> Voor Node.js kunt u de webserverpoort instellen in het bestand *server.js*. In deze zelfstudie wordt poort 3000 gebruikt, maar u kunt elke andere beschikbare poort gebruiken.
>
> Als u een omleidings-URL wilt instellen in de registratiegegevens van de toepassing, gaat u terug naar het deelvenster **Toepassingsregistratie** en voert u een van de volgende handelingen uit:
>
> - Stel *`http://localhost:3000/`* in als de **omleidings-URL**.
> - Als u een aangepaste TCP-poort gebruikt, gebruikt u *`http://localhost:<port>/`* (hierbij is *\<port>* het nummer van de aangepaste TCP-poort).
>   1. Kopieer de waarde van de **URL**.
>   1. Ga terug naar het deelvenster **Toepassingsregistratie** en plak de gekopieerde waarde in **Omleidings-URL**.
>

### <a name="configure-your-javascript-spa"></a>Uw JavaScript-SPA configureren

Maak een nieuw bestand met de naam `authConfig.js` om uw configuratieparameters voor verificatie op te nemen en voeg de volgende code toe:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Waar:
 - *\<Enter_the_Application_Id_Here>* is de **Toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
 - *\<Enter_the_Cloud_Instance_Id_Here>* is de instantie van de Azure-cloud. Voer *https://login.microsoftonline.com* in voor de hoofd- (of globale) Azure-cloud. Zie [Nationale clouds](./authentication-national-cloud.md) voor **nationale** clouds (bijvoorbeeld China).
 - *\<Enter_the_Tenant_info_here>* is ingesteld op een van de volgende opties:
   - Als uw toepassing *accounts in deze organisatiemap* ondersteunt, vervangt u deze waarde door de **tenant-id** of **tenantnaam** (bijvoorbeeld *contoso.microsoft.com*).
   - Als uw toepassing *accounts in elke organisatiemap* ondersteunt, vervang deze waarde dan door **organisaties**.
   - Als uw toepassing *accounts in elke organisatiemap en persoonlijke Microsoft-accounts* ondersteunt, vervang deze waarde dan door **algemeen**. Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervang deze waarde dan door **consumenten**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Gebruik Microsoft Authentication Library (MSAL) om de gebruiker aan te melden

Maak een nieuw JS-bestand met de naam `authPopup.js` om uw logica voor verificatie en het verkrijgen van een token op te nemen, en voeg de volgende code toe:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Meer informatie

Als een gebruiker de knop **Aanmelden** voor de eerste keer selecteert, roept de methode `signIn` `loginPopup` aan om zich aan te melden bij de gebruiker. Met deze methode wordt een pop-upvenster geopend met het *Microsoft identity platform-eindpunt* om de referenties van de gebruiker te vragen en te valideren. Nadat het aanmelden is geslaagd, wordt de gebruiker omgeleid naar de oorspronkelijke *index.html*-pagina. Er wordt een token ontvangen, verwerkt door `msal.js` en de informatie in het token wordt opgeslagen in de cache. Dit token wordt ook wel het *id-token* genoemd en bevat basisinformatie over de gebruiker, zoals de weergavenaam van de gebruiker. Als u van plan bent om gegevens te gebruiken die door dit token worden verstrekt, moet u ervoor zorgen dat dit token wordt gevalideerd door de back-endserver om te garanderen dat het token is uitgegeven aan een geldige gebruiker voor uw toepassing.

De toepassing met één pagina die u met behulp van deze gids hebt gegenereerd, roept `acquireTokenSilent` en/of `acquireTokenPopup` aan om een *toegangstoken* te verkrijgen dat wordt gebruikt om een query uit te voeren op de Microsoft Graph API voor profielgegevens van gebruikers. Als u een voorbeeld van het valideren van het id-token wilt zien, bekijkt u [deze](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Voorbeeld GitHub Active-Directory-java script-singlepageapp-DotNet-webapi-v2") voorbeeldtoepassing in GitHub. In het voorbeeld wordt een ASP.NET-web-API gebruikt voor het valideren van tokens.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Na de eerste aanmelding wilt u gebruikers niet meer vragen om zich telkens opnieuw te verifiëren wanneer ze een token moeten aanvragen voor toegang tot een resource. Daarom moet de meeste tijd *acquireTokenSilent* worden gebruikt voor het verkrijgen van tokens. Maar er zijn situaties waarin u wilt afdwingen dat gebruikers met het Microsoft-identity platform-eindpunt communiceren. Voorbeelden zijn:

- Gebruikers moeten hun referenties opnieuw opgeven omdat het wachtwoord is verlopen.
- Uw toepassing vraagt toegang tot een resource waarvoor de gebruiker toestemming moet geven.
- Verificatie in twee stappen is vereist.

Als u *acquireTokenPopup* aanroept, wordt een pop-upvenster geopend (of, als u *acquireTokenRedirect* aanroept, worden gebruikers omgeleid naar het Microsoft-identity platform-eindpunt). In dat venster moeten gebruikers hun referenties bevestigen, toestemming geven voor de vereiste resource of verificatie in wee stappen uitvoeren.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `acquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat de methode `loginPopup` (of `loginRedirect`) de eerste keer is uitgevoerd, wordt voor volgende aanroepen meestal de methode `acquireTokenSilent` gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources. (Aanroepen naar aanvraag- of vernieuwingstokens worden op de achtergrond uitgevoerd.) `acquireTokenSilent` kan in sommige gevallen mislukken. Het is bijvoorbeeld mogelijk dat het wachtwoord van de gebruiker is verlopen. Uw toepassing kan deze uitzondering op twee manieren afhandelen:

1. Een aanroep naar `acquireTokenPopup` onmiddellijk uitvoeren zodat een aanmeldingsprompt van een gebruiker wordt geactiveerd. Dit patroon wordt vaak gebruikt in online toepassingen waarbij er geen niet-geverifieerde inhoud in de toepassing beschikbaar is voor de gebruiker. Het voorbeeld dat door deze begeleide installatie wordt gegenereerd, maakt gebruik van dit patroon.

1. In toepassingen kan ook visueel aan de gebruiker worden aangegeven dat een interactieve aanmelding is vereist, zodat de gebruiker de juiste tijd kan selecteren om zich aan te melden, of de toepassing kan `acquireTokenSilent` op een later tijdstip opnieuw proberen. Deze methode wordt vaak gebruikt wanneer de gebruiker andere functionaliteit van de toepassing kan gebruiken zonder te worden onderbroken. Het is bijvoorbeeld mogelijk dat er niet-geverifieerde inhoud beschikbaar is in de toepassing. In dit geval kan de gebruiker beslissen wanneer hij of zij zich wil aanmelden om toegang te krijgen tot de beveiligde resource, of om de verouderde informatie te vernieuwen.

> [!NOTE]
> In deze quickstart worden standaard de methoden `loginPopup` en `acquireTokenPopup` gebruikt. Als u de browser Internet Explorer gebruikt, is het raadzaam om de methoden `loginRedirect` en `acquireTokenRedirect` te gebruiken vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met de manier waarop Internet Explorer pop-upvensters verwerkt. Zie [deze quickstart](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) als u wilt weten hoe u hetzelfde resultaat kunt krijgen met behulp van `Redirect methods`.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>De Microsoft Graph API aanroepen met behulp van het token dat u zojuist hebt verkregen

1. Maak eerst een JS-bestand met de naam `graphConfig.js` om uw REST-eindpunten in op te slaan. Voeg de volgende code toe:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Waar:
   - *\<Enter_the_Graph_Endpoint_Here>* is het exemplaar van Microsoft Graph API. Voor het globale Microsoft Graph API-eindpunt vervangt u deze tekenreeks door `https://graph.microsoft.com`. Raadpleeg de [documentatie voor Graph API](/graph/deployments) voor implementaties in nationale clouds.

1. Maak vervolgens een JS-bestand met de naam `graph.js`, waarmee een REST-aanroep naar Graph API van Microsoft wordt gemaakt, en voeg de volgende code toe:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In de voorbeeldtoepassing die aan de hand van deze gids wordt gemaakt, wordt de methode `callMSGraph()` gebruikt om een HTTP `GET`-aanvraag uit te voeren voor een beveiligde bron waarvoor een token is vereist. De aanvraag retourneert vervolgens de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd in de *HTTP-autorisatie-header*. In de voorbeeldtoepassing die met behulp van deze gids is gemaakt, is de resource het Microsoft Graph API-eindpunt *me*, waarmee de profielgegevens van de gebruiker worden weergegeven.

## <a name="test-your-code"></a>Uw code testen

1. Configureer de server om te luisteren naar een TCP-poort dat is gebaseerd op de locatie van uw *index.html*-bestand. Voor Node.js start u de webserver om te luisteren naar de poort door de volgende opdrachten in een opdrachtregelprompt in de toepassingsmap uit te voeren:

   ```bash
   npm install
   npm start
   ```
1. Voer in uw browser **http://localhost:3000** of **http://localhost:{port}** in, waarbij *poort* de poort is waarnaar uw webserver luistert. U moet de inhoud van uw bestand *index.html* en de knop **Aanmelden** zien.

Nadat de browser het bestand *index.html* heeft geladen, selecteert u **Aanmelden**. U wordt gevraagd om u aan te melden met het Microsoft-identiteitsplatform-eindpunt:

![Het aanmeldvenster voor het account voor de JavaScript-toepassing met één pagina](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Toestemming voor toegang tot de toepassing geven

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt u gevraagd om deze toegang te geven tot uw profiel en om u aan te melden:

![Het venster 'Machtiging gegeven'](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Resultaten van de toepassing weergeven

Nadat u zich hebt aangemeld, worden uw gebruikersprofielgegevens geretourneerd in de Microsoft Graph API-reactie die wordt weergegeven:

![Resultaten van de aanroep van Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

De Microsoft Graph API vereist het bereik *user.read* om het profiel van een gebruiker te lezen. Dit bereik wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in de registratieportal. Overige API's voor Microsoft Graph, evenals aangepaste API's voor uw back-endserver, vereisen mogelijk aanvullende bereiken. De Microsoft Graph API vereist bijvoorbeeld het bereik *Mail.Read* om de e-mailberichten van de gebruiker op te sommen.

> [!NOTE]
> De gebruiker wordt mogelijk gevraagd om aanvullende machtigingen te geven naarmate u het aantal bereiken verhoogt.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Lees meer over het ontwikkelen van toepassingen met enkele pagina (SPA) op het Microsoft-identiteitsplatform in onze reeks met meerdere scenario's.

> [!div class="nextstepaction"]
> [Scenario: Toepassing met één pagina](scenario-spa-overview.md)
