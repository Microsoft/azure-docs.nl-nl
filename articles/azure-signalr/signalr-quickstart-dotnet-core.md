---
title: 'Snelstart: Leren werken met Azure SignalR Service'
description: Een snelstart waarin u leert hoe u met de Azure SignalR-service een chatruimte maakt met ASP.NET Core MVC-apps.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 9099a8620f6e4d87ec38c10226d94b1b3cd3462f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865977"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Quickstart: Een chatruimte maken met behulp van SignalR Service

De Azure SignalR-service is een Azure-service waarmee ontwikkelaars eenvoudig webtoepassingen met realtime-functies kunnen bouwen. Deze service is oorspronkelijk gebaseerd op [SignalR voor ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), maar ondersteunt nu latere versies.

In dit artikel leest u hoe u aan de slag gaat met de Azure SignalR-service. In deze snelstart maakt u een chattoepassing met behulp van een web-app van ASP.NET Core MVC. Deze app maakt verbinding met de resource van de Azure SignalR-service om inhoud in realtime bij te werken. U gaat de webtoepassing lokaal hosten en verbinding maken met verschillende browserclients. Elke client kan bijgewerkte inhoud pushen naar alle andere clients. 

U kunt elke code-editor gebruiken om de stappen in deze snelstart uit te voeren. Eén optie is [Visual Studio Code](https://code.visualstudio.com/), dat beschikbaar is op de Windows-, macOS- en Linux-platforms.

De code voor deze zelfstudie is beschikbaar als download in de [GitHub-opslagplaats met AzureSignalR-voorbeelden](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). U kunt de Azure-resources die in deze quickstart worden gebruikt ook maken door [Een SignalR Service-script maken](scripts/signalr-cli-create-service.md) te volgen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Vereisten

* Installeer de [.NET Core SDK](https://dotnet.microsoft.com/download).
* Download of kloon het [Azure SignalR-voorbeeld](https://github.com/aspnet/AzureSignalR-samples) in de GitHub-opslagplaats. 

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Een Azure SignalR-resource maken

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

In dit gedeelte gebruikt u de [opdrachtregelinterface (CLI) van .NET Core](/dotnet/core/tools/) om een web-app-project van ASP.NET Core MVC te maken. Het voordeel van de CLI van .NET Core ten opzichte van Visual Studio is dat de interface beschikbaar is voor Windows, macOS en Linux. 

1. Maak een map voor uw project. In deze snelstart wordt *E:\Testing\chattest* gebruikt.

2. Voer in de nieuwe map de volgende opdracht uit om het project te maken:

    ```dotnetcli
    dotnet new mvc
    ```

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>Secret Manager toevoegen aan het project

In dit gedeelte voegt u het [hulpprogramma Secret Manager](/aspnet/core/security/app-secrets) toe aan uw project. Dit hulpprogramma slaat gevoelige gegevens voor ontwikkeltaken op buiten de projectstructuur. Deze aanpak voorkomt dat er per ongeluk appgeheimen worden gedeeld in de broncode.

1. Open het bestand *.csproj*. Voeg een element `DotNetCliToolReference` toe voor *Microsoft.Extensions.SecretManager.Tools*. Voeg ook een `UserSecretsId`-element toe zoals getoond in de volgende code voor *chattest.csproj* en sla het bestand op.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Azure SignalR toevoegen aan de web-app

1. Voeg een verwijzing toe naar het NuGet-pakket `Microsoft.Azure.SignalR` door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

3. Voeg een geheim met de naam *Azure:SignalR:ConnectionString* toe aan Secret Manager. 

    Dit geheim bevat straks de verbindingsreeks voor toegang tot de resource van de SignalR-service. *Azure:SignalR:ConnectionString* is de standaardconfiguratiesleutel waarnaar SignalR zoekt om een verbinding tot stand te brengen. Vervang de waarde in de volgende opdracht door de verbindingsreeks voor de resource van de SignalR-service.

    Deze opdracht moet worden uitgevoerd in dezelfde map als waar het *.csproj*-bestand zich bevindt.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Secret Manager wordt alleen gebruikt om de web-app te testen terwijl deze lokaal wordt gehost. In een latere zelfstudie gaat u de web-app voor de chatruimte implementeren in Azure. Nadat de web-app is geïmplementeerd, gebruikt u een toepassingsinstelling in plaats van de verbindingsreeks op te slaan met Secret Manager.

    Dit geheim is toegankelijk met de Configuratie-API. Een dubbele punt (:) werkt in de configuratienaam met de configuratie-API op alle ondersteunde platforms. Zie [Configuratie per omgeving](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Open *Startup.cs* en werk de methode `ConfigureServices` bij voor het gebruik van de Azure SignalR-service door de methoden `AddSignalR()` en `AddAzureSignalR()` aan te roepen:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Omdat er geen parameter wordt doorgegeven aan `AddAzureSignalR()`, wordt in deze code de standaardconfiguratiesleutel gebruikt voor de resourceverbindingsreeks van SignalR Service. De standaardconfiguratiesleutel is *Azure:SignalR:ConnectionString*.

5. In *Startup.cs* moet u de `Configure` methode bijwerken door hem te vervangen met de onderstaande code.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Een hub-klasse toevoegen

In SignalR is een hub een belangrijk onderdeel waarmee een set methoden beschikbaar wordt gesteld die kunnen worden aangeroepen vanuit de client. In dit gedeelte gaat u een hub-klasse met twee methoden definiëren:

* `Broadcast`: met deze methode verstuurt u een bericht naar alle clients.
* `Echo`: met deze methode stuurt u een bericht terug naar de aanroepende functie.

Beide methoden maken gebruik van de `Clients`-interface die wordt geboden door de SignalR-SDK van ASP.NET Core. Via deze interface hebt u toegang tot alle verbonden clients, zodat u inhoud naar uw clients kunt pushen.

1. Voeg in de projectmap een nieuwe map toe met de naam *Hub*. Voeg een nieuw bestand met hub-code met de naam *ChatHub.cs* toe aan de nieuwe map.

2. Voeg de volgende code toe aan *ChatHub.cs* om de hub-klasse te definiëren en sla het bestand op.

    Werk de naamruimte voor deze klasse bij als u een andere projectnaam dan *SignalR.Mvc* hebt gebruikt.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>De clientinterface voor de web-app toevoegen

De clientgebruikersinterface voor deze chatruimte-app bestaat uit HTML en JavaScript in een bestand met de naam *index.html* in de map *wwwroot*.

Kopieer het bestand *css/site.css* uit de map *wwwroot* van de [sample-opslagplaats](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Vervang de *css/site.css* van het project door de versie die u hebt gekopieerd.

Dit is de hoofdcode van *index.html*:

Maak een nieuw bestand in de *wwwroot*-map met de naam *index.html-* , kopieer en plak de volgende HTML-code in het zojuist gemaakte bestand:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

Met de code in *index.html* wordt `HubConnectionBuilder.build()` aangeroepen om een HTTP-verbinding tot stand te brengen met de Azure SignalR-resource.

Als de verbinding tot stand is gebracht, wordt de verbinding doorgegeven aan `bindConnectionMessage`. Deze functie voegt gebeurtenis-handlers toe om binnenkomende inhoud naar de client te pushen. 

`HubConnection.start()` start de communicatie met de hub. Vervolgens worden met `onConnected()` de knopgebeurtenis-handlers toegevoegd. Deze handlers gebruiken de verbinding om deze client in staat te stellen updates van inhoud naar alle verbonden clients te pushen.

## <a name="add-a-development-runtime-profile"></a>Een profiel voor een runtime-ontwikkelomgeving toevoegen

In dit gedeelte voegt u een runtime-ontwikkelomgeving voor ASP.NET Core toe. Zie [Use multiple environments in ASP.NET Core](/aspnet/core/fundamentals/environments) (Meerdere omgevingen gebruiken in ASP.NET Core) voor meer informatie.

1. Maak een nieuwe map in uw project met de naam *Properties*.

2. Voeg een nieuw bestand met de naam *launchSettings.json* toe aan de map, met de onderstaande inhoud, en sla het bestand op.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

    De app wordt lokaal gehost op poort 5000, zoals geconfigureerd in ons profiel voor de runtime-ontwikkelomgeving:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Open twee browservensters. Ga in beide browsers naar `http://localhost:5000`. U wordt gevraagd uw naam in te voeren. Voer een clientnaam in voor beide clients en test het pushen van berichtinhoud tussen beide clients met behulp van de knop **Send**.

    ![Voorbeeld van een Azure SignalR-groeps-chat](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en opnieuw gebruiken.

Als u klaar bent met de voorbeeldtoepassing uit de quickstart, kunt u de Azure-resources verwijderen die in deze quickstart zijn gemaakt, om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep is onomkeerbaar, en omvat alle resources in die groep. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via de blade waar deze zich bevindt in plaats van de resourcegroep te verwijderen.

Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

Typ de naam van de resourcegroep in het tekstvak **Filteren op naam**. In de instructies voor deze snelstart is een resourcegroep met de naam *SignalRTestResources* gebruikt. Selecteer het beletselteken ( **...** ) bij de resourcegroep in de lijst met resultaten en vervolgens **Resourcegroep verwijderen**.

![Selecties voor het verwijderen van een resourcegroep](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Voer de naam van de resourcegroep in ter bevestiging en selecteer **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle bijbehorende resources verwijderd.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe Azure SignalR Service-resource gemaakt. Vervolgens hebt u deze met een ASP.NET Core web-app gebruikt om inhoudsupdates in realtime naar meerdere verbonden clients te pushen. Als u meer wilt weten over het gebruik van de Azure SignalR-service, gaat u verder met de zelfstudie waarin verificatie wordt behandeld.

> [!div class="nextstepaction"]
> [Verificatie van Azure SignalR-service](./signalr-concept-authenticate-oauth.md)
