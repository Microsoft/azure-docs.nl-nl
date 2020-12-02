---
title: 'Zelfstudie: Een Blazor Server-chat-app bouwen - Azure SignalR'
description: In deze zelf studieleert u hoe u een Blazor Server-app bouwt en wijzigt met de Azure SignalR-service
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: ea55762aa40360d8eea94223a030f08aad504206
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485389"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Zelfstudie: Een Blazor Server-chat-app bouwen

In deze zelfstudie wordt uitgelegd hoe u een Blazor Server-app bouwt en wijzigt. U leert het volgende:

> [!div class="checklist"]
> * Een eenvoudige chatruimte bouwen met Blazor Server-app.
> * Razor-onderdelen wijzigen.
> * Gebeurtenisverwerking en gegevensbinding gebruiken in onderdelen.
> * Azure App Service snel implementeren in Visual Studio.
> * Lokale SignalR migreren naar Azure SignalR Service.

## <a name="prerequisites"></a>Vereisten
* Installeer [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (Versie >= 3.0.100)
* Installeer [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (Versie >= 16.3)
> De preview-versie van Visual Studio 2019 is ook geschikt voor de meest recente versie van de sjabloon voor de Blazor Server-app die is gericht op de nieuwere versie van .Net Core.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Een lokale chatroom bouwen in Blazor Server-app

Vanaf versie 16.2.0 van Visual Studio 2019 is Azure SignalR Service ingebouwd om het publiceren van web-apps en het beheren van afhankelijkheden tussen web-apps en SignalR te verbeteren. U kunt op hetzelfde moment in de lokale SignalR-ontwikkelingsomgeving en in Azure SignalR Service voor Azure App Service werken zonder dat er codewijzigingen plaatsvinden.

1. Een Blazor-chatapp maken
   
   Kies in Visual Studio Nieuw project maken -> Blazor-app-> (geef de app een naam en kies een map)-> Blazor Server-app. Zorg ervoor dat u .NET Core SDK 3.0+ al hebt geïnstalleerd, zodat Visual Studio in het doelframework goed kan herkennen.

   [ ![In Een nieuw project maken zijn de sjablonen voor de Blazor-app geselecteerd.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Of voer cmd uit
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Voeg een `BlazorChatSampleHub.cs`-bestand toe om `Hub` te implementeren voor chat.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Voeg een eindpunt toe voor de hub in `Startup.Configure()`.
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Installeer het `Microsoft.AspNetCore.SignalR.Client`-pakket om de SignalR-client te gebruiken.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Maak `ChatRoom.razor` onder de map `Pages` om de SignalR-client te implementeren. Voer de onderstaande stappen uit of kopieer gewoon [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Voeg een paginakoppeling en verwijzing toe.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Voeg code toe aan de nieuwe SignalR-client om berichten te verzenden en te ontvangen.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Voeg een renderingsonderdeel toe vóór `@code` zodat de gebruikersinterface kan communiceren met de SignalR-client.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Werk `NavMenu.razor` bij om een invoermenu voor de chatruimte onder `NavMenuCssClass` in te voegen, zoals rest.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Werk `site.css` bij om de praatwolkweergaven in het chatgebied te optimaliseren. Voeg de volgende code toe aan het einde.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Druk op <kbd>F5</kbd> om de app uit te voeren. U kunt nu chatten, zoals hieronder.

   [ ![Er wordt een geanimeerd chatgesprek tussen Bob en Alice weergegeven. Alice zegt hallo, Bob zegt hoi.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publiceren naar Azure

   Tot nu toe werkt de Blazor-app op de lokale SignalR-service. Wanneer de app wordt geïmplementeerd op Azure App Service, wordt aangeraden om [Azure SignalR Service](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) te gebruiken. Daarmee kan een Blazor Server-app worden geschaald naar een groot aantal gelijktijdige SignalR-verbindingen. Daarnaast helpen het wereldwijde bereik en de hoogwaardige datacentra voor SignalR aanzienlijk bij het verminderen van de latentie die ontstaat door geografische spreiding.

> [!IMPORTANT]
> De statussen van de gebruikersinterface in de Blazor Server-app worden op de service opgeslagen, waardoor in dit geval Server Sticky is vereist. Als er één app-server is, wordt server sticky standaard gegarandeerd. Als er echter meerdere app-servers zijn, is er een kans dat de onderhandeling van de client en de verbinding naar verschillende servers worden geleid en dat er fouten in de gebruikersinterface optreden in de Blazor-app. Daarom moet u server stick inschakelen zoals hieronder in `appsettings.json`:
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Klik met de rechtermuisknop op het project en ga naar `Publish`.

   * Doel: Azure
   * Specifiek doel: Alle soorten **Azure App Service** worden ondersteund.
   * App Service: maak een nieuw account of selecteer een bestaande app-service.

   [ ![De animatie toont de selectie van Azure als doel en vervolgens Azure App Service als specifiek doel.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure SignalR Service-afhankelijkheid toevoegen

   Nadat het publicatieprofiel is gemaakt, kunt u een aanbevolen bericht zien onder **Serviceafhankelijkheden**. Klik op **Configureren** om nieuwe Azure SignalR Service te maken of een bestaand exemplaar te selecteren in het deelvenster.

   [ ![Bij Publiceren is de te configureren koppeling gemarkeerd.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   De afhankelijkheid van de service zorgt ervoor dat uw app automatisch overschakelt naar de Azure SignalR Service wanneer u Azure gebruikt.

   * Werk [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) bij om de Azure SignalR Service te gebruiken.
   * Voeg NuGet-pakketverwijzingen toe aan de Azure SignalR Service.
   * Werk de profieleigenschappen bij om de afhankelijkheidsinstellingen op te slaan.
   * Configureer het geheimenarchief naar keuze.
   * Voeg de `appsettings`-configuratie toe om ervoor te zorgen dat uw app-doel de Azure SignalR Service selecteert.

   [ ![Bij Samenvatting van wijzigingen worden de selectievakjes gebruikt om alle afhankelijkheden te selecteren.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. De app publiceren

   De app is nu klaar om te worden gepubliceerd. De pagina wordt automatisch bezocht nadat de publicatie is voltooid. 
   > [!NOTE]
   > Het werkt mogelijk niet direct de eerste keer dat de pagina wordt bezocht vanwege latentie in het opstarten van de Azure App Service-implementatie. Het vernieuwen van de pagina kan enige vertraging opleveren.
   > U kunt ook de foutopsporingsmodus van de browser gebruiken (<kbd>F12</kbd>) om te controleren of het verkeer al wordt omgeleid naar de Azure SignalR Service.

   [ ![Blazor SignalR-chatvoorbeeld heeft een tekstvak voor uw naam en een Chat!-knop om een chatgesprek te beginnen.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Verder onderwerp: Azure SignalR Service inschakelen in lokale ontwikkeling

1. Verwijzing naar de Azure SignalR-SDK toevoegen

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Een aanroep toevoegen aan Azure SignalR Service in `Startup.ConfigureServices()`.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Configureer Azure SignalR Service `ConnectionString` in `appsetting.json` of met het hulpprogramma [Secret Manager](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager)

> [!NOTE]
> Stap 2 kan worden vervangen door [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) te gebruiken voor de SignalR-SDK.
> 
> 1. Voeg een configuratie toe om Azure SignalR Service in te schakelen in `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Wijs de opstart-assembly voor hosting toe om de Azure SignalR-SDK te gebruiken. Bewerk `launchSettings.json` en voeg een configuratie toe, zoals hieronder in `environmentVariables`.
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, verwijdert u de resourcegroep met Azure Portal.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een eenvoudige chatruimte bouwen met Blazor Server-app.
> * Razor-onderdelen wijzigen.
> * Gebeurtenisverwerking en gegevensbinding gebruiken in onderdelen.
> * Azure App Service snel implementeren in Visual Studio.
> * Lokale SignalR migreren naar Azure SignalR Service.

Meer informatie over hoge beschikbaarheid.
> [!div class="nextstepaction"]
> [Tolerantie en herstel na noodgevallen](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Extra resources

* [ASP.NET Core Blazor](/aspnet/core/blazor)
