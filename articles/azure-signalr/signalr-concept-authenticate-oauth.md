---
title: Hand leiding voor het verifiëren van Azure signalerings service-clients
description: Meer informatie over hoe u uw eigen verificatie implementeert en integreert met de Azure signalerings service door het E2E-voor beeld te volgen.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cd5b709843ba2d98170aaa58eb5c3cc1ec8883af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734678"
---
# <a name="azure-signalr-service-authentication"></a>verificatie van Azure SignalR Service

Deze zelfstudie is gebaseerd op de toepassing voor een chatruimte die wordt beschreven in de snelstart. Als u [Een chatruimte maken met de SignalR-service](signalr-quickstart-dotnet-core.md) nog niet voltooid, moet u dat eerst doen.

In deze zelfstudie leert u hoe u uw eigen verificatie implementeert en deze vervolgens integreert met de Microsoft Azure SignalR-service.

De verificatie die in eerste instantie wordt gebruikt in de toepassing voor een chatruimte in de snelstart is te eenvoudig voor praktijkscenario's. In de toepassing kan elke client claimen wie ze zijn en de server accepteert dit vervolgens. Deze aanpak is niet erg handig in praktische toepassingen, waar een malafide gebruiker een andere gebruiker kan imiteren om toegang te krijgen tot gevoelige gegevens.

[GitHub](https://github.com/) biedt verificatie-API's die zijn gebaseerd op een veelgebruikt standaardprotocol met de naam [OAuth](https://oauth.net/). Met deze API's kunnen toepassingen van derden GitHub accounts verifiëren. In deze zelfstudie gebruikt u deze API's voor het implementeren van verificatie via een GitHub-account voordat clients zich mogen aanmelden bij de toepassing voor een chatruimte. Na verificatie van een GitHub-account, worden de accountgegevens als een cookie toegevoegd om te worden gebruikt voor verificatie door de webclient.

Zie het Engelstalige document [Basics of Authentication](https://developer.github.com/v3/guides/basics-of-authentication/) voor meer informatie over de verificatie API's van OAuth die worden aangeboden via GitHub.

U kunt elke code-editor gebruiken om de stappen in deze snelstart uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

De code voor deze zelfstudie is beschikbaar als download in de [GitHub-opslagplaats met AzureSignalR-voorbeelden](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![Downloaden voltooid in Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe OAuth-app registreren met uw GitHub-account
> * Een verificatiecontroller toevoegen ter ondersteuning van GitHub verificatie
> * Uw web-app van ASP.NET Core implementeren in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

- Een account op [GitHub](https://github.com/)
- [Git](https://git-scm.com/)
- [.NET Core-SDK](https://www.microsoft.com/net/download/windows)
- [Azure Cloud shell](../cloud-shell/quickstart.md) geconfigureerd voor de bash omgeving.
- Download of kloon het [Azure SignalR-voorbeeld](https://github.com/aspnet/AzureSignalR-samples) in de GitHub-opslagplaats.

## <a name="create-an-oauth-app"></a>Een OAuth-app maken

1. Open een webbrowser, ga naar `https://github.com` en meld u aan bij uw account.

2. Voor uw account gaat u naar **instellingen**  >  **ontwikkelaars instellingen** en klikt u op **een nieuwe toepassing registreren** of **nieuwe OAuth-app** onder *OAuth-apps*.

3. Gebruik de volgende instellingen voor de nieuwe OAuth-app en klik vervolgens op **Register application**:

    | Instellingsnaam | Voorgestelde waarde | Beschrijving |
    | ------------ | --------------- | ----------- |
    | De naam van de toepassing | *Azure SignalR Chat* | De GitHub-gebruiker moet de app waarmee hij of zij verificatie uitvoert, kunnen herkennen en vertrouwen.   |
    | Homepage URL | `http://localhost:5000/home` | |
    | Application description | *Een voorbeeld van een chatruimte die Azure SignalR Service gebruikt voor GitHub-verificatie* | Een zinvolle beschrijving van de toepassing zodat de gebruikers van uw toepassing de context begrijpen waarin de verificatie wordt gebruikt. |
    | Authorization callback URL | `http://localhost:5000/signin-github` | Deze instelling is de belangrijkste instelling voor uw OAuth-toepassing. Dit is de callback-URL waarnaar de gebruiker door GitHub wordt omgeleid na een geslaagde authenticatie. In deze zelfstudie moet u de standaard-callback-URL voor het pakket *AspNet.Security.OAuth.GitHub* gebruiken, te weten */signin-github*.  |

4. Zodra de registratie van de nieuwe OAuth-app is voltooid, voegt u de *Client ID* en *Client Secret* toe aan Secret Manager. Gebruik hiervoor de volgende opdrachten. Vervang *Your_GitHub_Client_Id* en *Your_GitHub_Client_Secret* door de waarden voor uw OAuth-app.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>De OAuth-stroom implementeren

### <a name="update-the-startup-class-to-support-github-authentication"></a>De Startup-klasse bijwerken om ondersteuning voor GitHub-verificatie te bieden

1. Voeg een verwijzing toe naar de meest recente pakketten van *Microsoft.AspNetCore.Authentication.Cookies* en *AspNet.Security.OAuth.GitHub* en zet alle pakketten terug.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. Open *Startup.cs* en voeg `using`-instructies toe voor de volgende naamruimten:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. Voeg aan het begin van de `Startup`-klasse constanten toe voor de sleutels van Secret Manager die de geheimen voor de GitHub OAuth-app bevatten.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Voeg de volgende code toe aan de methode `ConfigureServices` om verificatie met de GitHub OAuth-app te ondersteunen:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Voeg de helper-methode `GetUserCompanyInfoAsync` toe aan de klasse `Startup`.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Werk de methode `Configure` van de Startup-klasse bij met de volgende regel code en sla het bestand op.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Een verificatiecontroller toevoegen

In dit gedeelte gaat u een `Login`-API implementeert die clients verifieert met behulp van de GitHub OAuth-app. Als de verificatie is voltooid, voegt de API een cookie toen aan de respons van de webclient voordat de client weer wordt teruggeleid naar de chat-app. Dat cookie wordt vervolgens gebruikt voor het identificeren van de client.

1. Voeg een nieuw bestand met controllercode toe aan de map *chattest\Controllers*. Noem het bestand *AuthController.cs*.

2. Voeg de volgende code toe voor de verificatiecontroller. Vergeet niet om de naamruimte bij te werken als de projectmap niet *chattest* was:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Sla uw wijzigingen op.

### <a name="update-the-hub-class"></a>De Hub-klasse bijwerken

Wanneer een webclient probeert verbinding te maken met de SignalR-service, is de standaardinstelling dat de verbinding wordt verleend op basis van een toegangstoken dat intern wordt aangeboden. Dit toegangstoken is niet gekoppeld aan een geverifieerde identiteit. Deze toegang is dus in feite anoniem.

In dit gedeelte gaat u echte verificatie inschakelen door het kenmerk `Authorize` toe te voegen aan de Hub-klasse en de hub-methoden bij te werken, zodat deze de gebruikersnaam lezen uit de claim van de geverifieerde gebruiker.

1. Open *Hub\Chat.cs* en voeg verwijzingen naar deze naamruimten toe:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Werk de hub-code bij zoals hieronder wordt weergegeven. Met deze code wordt het kenmerk `Authorize` toegevoegd aan de `Chat`-klasse, en wordt de geverifieerde identiteit van de gebruiker in de hub-methoden gebruikt. Daarnaast wordt de methode `OnConnectedAsync` toegevoegd, die ervoor zorgt dat er een systeembericht in de chatruimte wordt geregistreerd wanneer een nieuwe client verbinding maakt.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Sla uw wijzigingen op.

### <a name="update-the-web-client-code"></a>De code van de webclient bijwerken

1. Open *wwwroot\index.html* en vervang de code die om de gebruikersnaam vraagt door code waarmee het cookie wordt gebruikt dat wordt geretourneerd door de verificatiecontroller.

    Verwijder de volgende code uit *index.html*:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Voeg de volgende code toe om het cookie te gebruiken:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Ga naar de regel net onder de coderegel die u hebt toegevoegd om het cookie te gebruiken en voeg daar de volgende definitie voor de functie `appendMessage` toe:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Werk de functies `bindConnectionMessage` en `onConnected` bij met de volgende code zodat ze `appendMessage` gebruiken.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. Werk aan het einde van *index.html* de code voor foutafhandeling voor `connection.start()` bij zoals hieronder wordt weergegeven, zodat de gebruiker wordt gevraagd om zich aan te melden.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Sla uw wijzigingen op.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Sla de wijzigingen in alle bestanden op.

2. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

3. Als het compileren is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

    De app wordt standaard lokaal gehost op poort 5000:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. Open een browservenster en ga naar `http://localhost:5000`. Klik bovenaan op de link **here** om u aan te melden bij GitHub.

    ![Downloaden voltooid in Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    U wordt gevraagd om de chat-app toegang te geven tot uw GitHub-account. Klik op de knop **Authorize**.

    ![OAuth-app autoriseren](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    U wordt teruggeleid naar de chattoepassing en aangemeld met de naam van uw GitHub-account. De webtoepassing heeft uw accountnaam vastgesteld door u te verifiëren met behulp van de nieuwe verificatie die u hebt toegevoegd.

    ![Account geïdentificeerd](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    De chat-app gebruikt nu GitHub voor verificatie en slaat de verificatiegegevens op als cookies. U moet de app implementeren naar Azure om andere gebruikers in staat te stellen zich met hun account te verifiëren en te communiceren vanaf andere werkstations.

## <a name="deploy-the-app-to-azure"></a>De app implementeren in Azure

De omgeving voorbereiden op de Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

In deze sectie gebruikt u de Azure-opdracht regel interface (CLI) om een nieuwe web-app te maken in [Azure app service](../app-service/index.yml) om uw ASP.NET-toepassing in azure te hosten. De web-app wordt geconfigureerd voor het gebruik van een lokale Git-implementatie. De web-app wordt daarnaast geconfigureerd met de verbindingsreeks voor SignalR, geheimen van de GitHub OAuth-app en een implementatiegebruiker.

Bij het maken van de volgende resources moet u de resourcegroep gebruiken waarin zich ook de resource van de SignalR-service bevindt. Op deze manier is het achteraf veel eenvoudiger om alle resources te verwijderen. In deze voorbeelden wordt ervan uitgegaan dat u de groepsnaam hebt gebruikt die in eerdere zelfstudies wordt aanbevolen, te weten *SignalRTestResources*.

### <a name="create-the-web-app-and-plan"></a>De web-app en het plan maken

Kopieer de tekst voor de onderstaande opdrachten en werk de parameters bij. Plak het bijgewerkte script in Azure Cloud-Shell en druk op **Enter** om een nieuw App Service-plan en een nieuwe web-app te maken.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parameter | Beschrijving |
| -------------------- | --------------- |
| ResourceGroupName | De naam van deze resourcegroep is voorgesteld in eerdere zelfstudies. Het is een goed idee om alle resources van de zelfstudies bij elkaar te zetten. Gebruik de resourcegroep die u in de eerdere zelfstudies hebt gebruikt. |
| WebAppPlan | Voer een unieke naam in voor het nieuwe App Service-plan. |
| WebAppName | Dit is de naam voor de nieuwe web-app en maakt deel uit van de URL. Gebruik een unieke naam. zoals signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>App-instellingen toevoegen aan de web-app

In dit gedeelte voegt u app-instellingen toe voor de volgende onderdelen:

* Verbindingsreeks voor resource van SignalR-service
* Client-id van GitHub OAuth-app
* Clientgeheim van GitHub OAuth-app

Kopieer de tekst voor de onderstaande opdrachten en werk de parameters bij. Plak het bijgewerkte script in Azure Cloud-Shell en druk op **Enter** om de app-instellingen toe te voegen:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parameter | Beschrijving |
| -------------------- | --------------- |
| GitHubClientId | Geef hier de geheime client-id voor uw GitHub OAuth-app op. |
| GitHubClientSecret | Geef hier het geheime wachtwoord voor uw GitHub OAuth-app op. |
| ResourceGroupName | Werk deze variabele bij met de naam van de resourcegroep die u in het vorige gedeelte hebt gebruikt. |
| SignalRServiceResource | Werk deze variabele bij met de naam van de resource voor de SignalR-service die u in de snelstart hebt gemaakt, bijvoorbeeld signalrtestsvc48778624. |
| WebAppName | Werk deze variabele bij met de naam van de nieuwe web-app die u in het vorige gedeelte hebt gemaakt. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>De web-app configureren voor lokale Git-implementatie

Plak het volgende script in Azure Cloud Shell. Met dit script maakt u een nieuwe gebruikersnaam en een nieuw wachtwoord voor de implementatie die u gaat gebruiken wanneer u de code met Git implementeert naar de web-app. Het script configureert de web-app ook voor implementatie met een lokale Git-opslagplaats, en retourneert de URL van de Git-implementatie.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parameter | Beschrijving |
| -------------------- | --------------- |
| DeploymentUserName | Kies een nieuwe naam voor de implementatiegebruiker. |
| DeploymentUserPassword | Kies een wachtwoord voor de nieuwe implementatiegebruiker. |
| ResourceGroupName | Gebruik de resourcegroep die u in het vorige gedeelte hebt gebruikt. |
| WebAppName | Dit is de naam voor de nieuwe web-app die u eerder hebt gemaakt. |

Noteer de URL van de Git-implementatie die wordt geretourneerd door deze opdracht. U hebt deze URL later nodig.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Uw code implementeren in de Azure-web-app

Voer de volgende opdrachten uit in een Git-shell om uw code te implementeren.

1. Ga naar de hoofdmap van de projectmap. Als u het project nog niet hebt geïnitialiseerd met een Git-opslagplaats, voert u de volgende opdracht uit:

    ```bash
    git init
    ```

2. Voeg een remote toe voor de URL van de Git-implementatie die u eerder hebt genoteerd:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Zet alle bestanden klaar in de geïnitialiseerde opslagplaats en voeg een commit toe.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Implementeer uw code in de Azure-web-app.

    ```bash
    git push Azure main
    ```

    Hiervoor moet u zich eerst verifiëren. Voer de gebruikersnaam en het wachtwoord in van de implementatiegebruiker die u eerder hebt gemaakt.

### <a name="update-the-github-oauth-app"></a>De GitHub-OAuth-app bijwerken

Als laatste moet u de waarden voor **Homepage URL** en **Authorization callback URL** van de GitHub OAuth-app bijwerken, zodat deze verwijzen naar de nieuwe gehoste app.

1. Open [https://github.com](https://github.com) in een browser en navigeer naar de **instellingen** van uw account OAuth-apps voor  >  **ontwikkel aars**  >  .

2. Klik op de verificatie-app en werk de waarden voor **Homepage URL** en **Authorization callback URL** bij zoals hieronder wordt weergegeven:

    | Instelling | Voorbeeld |
    | ------- | ------- |
    | Homepage URL | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | Authorization callback URL | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Navigeer naar de URL van de web-app en test de toepassing.

    ![Downloaden voltooid in Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en gebruiken met de volgende zelfstudie.

Als u niet verder wilt met de voorbeeldtoepassing uit de snelstart, kunt u de Azure-resources verwijderen die in deze snelstart zijn gemaakt om kosten te voorkomen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.

Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.

Typ de naam van de resourcegroep in het tekstvak **Filteren op naam...** . In de instructies voor dit artikel is een resourcegroep met de naam *SignalRTestResources* gebruikt. Klik in de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

![Verwijderen](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ de naam van de resourcegroep om te bevestigen en klik op **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u verificatie met OAuth toegevoegd om een betere methode voor verificatie met de Azure SignalR-service mogelijk te maken. Voor meer informatie over het gebruik van Azure SignalR Server gaat u verder met de voorbeelden van Azure CLI voor de SignalR-service.

> [!div class="nextstepaction"]
> [Azure CLI-voorbeelden voor SignalR](./signalr-reference-cli.md)