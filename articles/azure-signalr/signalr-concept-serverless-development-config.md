---
title: Azure Functions app-Azure-Signaalr ontwikkelen & configureren
description: Meer informatie over het ontwikkelen en configureren van serverloze realtime-toepassingen met behulp van Azure Functions en de Azure signalerings service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858702"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service

Azure Functions toepassingen kunnen gebruikmaken van de [service bindingen van Azure signalering](../azure-functions/functions-bindings-signalr-service.md) om realtime-functies toe te voegen. Client toepassingen gebruiken client-Sdk's die beschikbaar zijn in verschillende talen om verbinding te maken met de Azure signalerings service en real-time berichten te ontvangen.

In dit artikel worden de concepten beschreven voor het ontwikkelen en configureren van een Azure function-app die is geïntegreerd met de signalerings service.

## <a name="signalr-service-configuration"></a>Signaal service configuratie

De Azure signalerings service kan in verschillende modi worden geconfigureerd. Bij gebruik in combi natie met Azure Functions moet de service worden geconfigureerd in de *serverloze* modus.

Ga in het Azure Portal naar de pagina *instellingen* van de bron van de signaal service. Stel de *Service modus* in op *serverloos*.

![SignalR Service-modus](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-ontwikkeling

Een serverloze toepassing in realtime die is gebouwd met Azure Functions en Azure SignalR Service heeft over het algemeen twee Azure Functions nodig:

* Een functie 'negotiate' die de client aanroept voor een geldig SignalR Service-toegangstoken en service-eindpunt-URL
* Een of meer functies die berichten van de seingevings service afhandelen en berichten verzenden of groepslid maatschap beheren

### <a name="negotiate-function"></a>functie onderhandelen

Een client toepassing vereist een geldig toegangs token om verbinding te maken met de Azure signalerings service. Een toegangs token kan anoniem zijn of worden geverifieerd op basis van een gebruikers-ID. Voor serverloze seingevings signalen is een HTTP-eind punt met de naam ' onderhandelen ' vereist voor het verkrijgen van een token en andere verbindings informatie, zoals de URL van het seingevings service-eind punt.

Gebruik een door HTTP geactiveerde Azure-functie en de *SignalRConnectionInfo* -invoer binding om het object verbindings gegevens te genereren. De functie moet een HTTP-route hebben die eindigt op `/negotiate` .

Met [model op basis van klasse](#class-based-model) in C# hebt u geen *SignalRConnectionInfo* -invoer binding nodig en kunt u veel eenvoudiger aangepaste claims toevoegen. Bekijk de [onderhandelings ervaring in model op basis van klassen](#negotiate-experience-in-class-based-model)

Zie voor meer informatie over het maken van de onderhandelings functie de referentie voor [ *SignalRConnectionInfo* -invoer binding](../azure-functions/functions-bindings-signalr-service-input.md).

Zie [app service-verificatie gebruiken](#using-app-service-authentication)voor meer informatie over het maken van een geverifieerd token.

### <a name="handle-messages-sent-from-signalr-service"></a>Berichten verwerken die zijn verzonden vanuit de seingevings service

Gebruik de binding van de *signaal trigger* om berichten te verwerken die zijn verzonden vanuit de signalerings service. U kunt een melding ontvangen wanneer clients berichten verzenden of clients die verbinding maken met of losgekoppeld zijn.

Zie voor meer informatie de referentie voor het activeren van de [ *signaal/trigger* binding](../azure-functions/functions-bindings-signalr-service-trigger.md).

U moet ook uw functie-eind punt configureren als upstream, zodat de service de functie wordt geactiveerd wanneer er een bericht van de client is. Raadpleeg dit [document](concept-upstream.md)voor meer informatie over het configureren van een upstream.

### <a name="sending-messages-and-managing-group-membership"></a>Berichten verzenden en groepslid maatschap beheren

De *signaal* uitvoer binding gebruiken om berichten te verzenden naar clients die zijn verbonden met de Azure signalerings service. U kunt berichten uitzenden naar alle clients of u kunt ze verzenden naar een subset van clients die zijn geverifieerd met een specifieke gebruikers-ID of die is toegevoegd aan een specifieke groep.

Gebruikers kunnen worden toegevoegd aan een of meer groepen. U kunt ook de uitvoer binding van de *signaal sterkte* gebruiken om gebruikers toe te voegen aan of te verwijderen uit groepen.

Zie voor meer informatie de referentie voor de [ *signaal* uitvoer binding](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Seingevings hubs

Signa lering heeft een concept van ' hubs '. Elke client verbinding en elk bericht dat wordt verzonden vanuit Azure Functions, zijn gericht op een specifieke hub. U kunt hubs gebruiken als een manier om uw verbindingen en berichten te scheiden in logische naam ruimten.

## <a name="class-based-model"></a>Model op basis van klasse

Het model op basis van klassen is specifiek voor C#. Met model op basis van klasse kan een consistente programmeer ervaring aan de server zijde worden gesignaleerd. Het bevat de volgende functies.

* Minder configuratie werk: de klassenaam wordt gebruikt als `HubName` , de naam van de methode wordt gebruikt als `Event` en de `Category` wordt automatisch bepaald volgens de naam van de methode.
* Automatische parameter binding: noch het `ParameterNames` kenmerk `[SignalRParameter]` is niet nodig. Para meters zijn automatisch gebonden aan argumenten van de Azure function-methode in de juiste volg orde.
* Handige uitvoer en onderhandelings ervaring.

De volgende codes illustreren deze functies:

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

Alle functies die gebruikmaken van op klassen gebaseerd model moeten de methode van klasse zijn die overneemt van **ServerlessHub**. De naam van de klasse `SignalRTestHub` in het voor beeld is de naam van de hub.

### <a name="define-hub-method"></a>Hub-methode definiëren

Alle hub-methoden **moeten** een argument van worden voorzien van het `InvocationContext` `[SignalRTrigger]` kenmerk, en constructor zonder para meters gebruiken. Vervolgens wordt de **methode naam** beschouwd als parameter **gebeurtenis**.

Standaard, `category=messages` behalve de naam van de methode is een van de volgende namen:

* **OnConnected**: behandeld als `category=connections, event=connected`
* **OnDisconnected**: behandeld als `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>Verbindings ervaring met para meters

In model op basis van klasse `[SignalRParameter]` is niet nodig omdat alle argumenten standaard zijn gemarkeerd, `[SignalRParameter]` behalve als dit een van de volgende situaties is:

* Het argument wordt gedecoreerd door een bindings kenmerk.
* Het type van het argument is `ILogger` of `CancellationToken`
* Het argument wordt gedecoreerd op kenmerk `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>Onderhandelen over ervaring in model op basis van klasse

In plaats van het gebruik van een signaal invoer binding `[SignalR]` , kan onderhandelingen in model op basis van klassen flexibeler zijn. Basis klasse `ServerlessHub` heeft een methode

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

Deze functies worden door de gebruiker aangepast `userId` of `claims` tijdens de uitvoering van de functie.

## <a name="use-signalrfilterattribute"></a>`SignalRFilterAttribute` gebruiken

Gebruiker kan de abstracte klasse overnemen en implementeren `SignalRFilterAttribute` . Als uitzonde ringen worden gegenereerd in `FilterAsync` , `403 Forbidden` worden teruggestuurd naar clients.

In het volgende voor beeld ziet u hoe u een klant filter implementeert dat alleen de `admin` aanroepen kan uitvoeren `broadcast` .

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

Gebruik het kenmerk om de functie te autoriseren.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>Client ontwikkeling

Seingevings client toepassingen kunnen gebruikmaken van de seingevings client-SDK in een van de verschillende talen om eenvoudig verbinding te maken met en berichten te ontvangen van de Azure signalerings service.

### <a name="configuring-a-client-connection"></a>Een client verbinding configureren

Een client kan alleen verbinding maken met de signalerings service als een geslaagde verbindings onderhandeling bestaat uit de volgende stappen:

1. Een aanvraag indienen bij het *onderhandelings* -http-eind punt dat hierboven wordt beschreven om geldige verbindings gegevens op te halen
1. Verbinding maken met de signaal service via de URL van het service-eind punt en het toegangs token dat is verkregen van het *Negotiate* -eind punt

De client-Sdk's van de seingevings bevatten al de logica die nodig is om de onderhandelings-Handshake uit te voeren. Geef de URL van het onderhandelings eindpunt, min het `negotiate` segment, door aan de SDK `HubConnectionBuilder` . Hier volgt een voor beeld in Java script:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per Conventie voegt de SDK automatisch `/negotiate` toe aan de URL en gebruikt deze om te beginnen met de onderhandeling.

> [!NOTE]
> Als u de Java script-type script-SDK in een browser gebruikt, moet u de [functie Cross-Origin Resource Sharing (CORS) inschakelen](#enabling-cors) op uw functie-app.

Raadpleeg de documentatie voor uw taal voor meer informatie over het gebruik van de seingevings client-SDK:

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Berichten verzenden van een client naar de service

Als u een [upstream](concept-upstream.md) hebt geconfigureerd voor uw signalerings bron, kunt u berichten van de client naar uw Azure functions verzenden met behulp van een seingevings client. Hier volgt een voor beeld in Java script:

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Functions configuratie

Azure function-apps die zijn geïntegreerd met de Azure signalerings service kunnen worden geïmplementeerd zoals een typische Azure function-app, met technieken zoals continue [implementatie](../azure-functions/functions-continuous-deployment.md), [zip-implementatie](../azure-functions/deployment-zip-push.md)en [uitvoeren vanuit pakket](../azure-functions/run-functions-from-deployment-package.md).

Er zijn echter enkele speciale overwegingen voor apps die gebruikmaken van de signalerings service bindingen. Als de client wordt uitgevoerd in een browser, moet CORS zijn ingeschakeld. En als de app verificatie vereist, kunt u het Negotiate-eind punt integreren met App Service-verificatie.

### <a name="enabling-cors"></a>CORS inschakelen

De Java script-type script-client maakt HTTP-aanvragen aan de onderhandelings functie om de verbindings onderhandelingen te initiëren. Wanneer de client toepassing wordt gehost op een ander domein dan de Azure function-app, moet CORS (cross-Origin Resource Sharing) zijn ingeschakeld op de functie-app of worden de aanvragen geblokkeerd door de browser.

#### <a name="localhost"></a>Lokalehost

Wanneer u de functie-app op uw lokale computer uitvoert, kunt u een `Host` sectie toevoegen aan *local.settings.jsin* om CORS in te scha kelen. Voeg in de `Host` sectie twee eigenschappen toe:

* `CORS` -Voer de basis-URL in die de oorsprong is van de client toepassing
* `CORSCredentials` -Stel deze instelling in op `true` ' withCredentials-aanvragen toestaan

Voorbeeld:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>CORS voor Cloud-Azure Functions

Als u CORS wilt inschakelen voor een Azure function-app, gaat u naar het configuratie scherm CORS op het tabblad *platform functies* van de functie-app in de Azure Portal.

> [!NOTE]
> CORS-configuratie is nog niet beschikbaar in Azure Functions Linux-verbruiks abonnement. Gebruik [Azure API Management](#cloud---azure-api-management) om CORS in te scha kelen.

CORS met Access-Control-Allow-referenties moet zijn ingeschakeld voor de seingevings client om de functie Negotiate aan te roepen. Schakel het selectie vakje in om dit in te scha kelen.

Voeg in de sectie *toegestane oorsprong* een vermelding toe met de basis-URL van de oorsprong van uw webtoepassing.

![CORS configureren](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud-Azure-API Management

Azure API Management biedt een API-gateway die mogelijkheden toevoegt aan bestaande back-end-services. U kunt deze gebruiken om CORS toe te voegen aan uw functie-app. Het biedt een verbruiks laag met betalen per actie en een maandelijkse gratis toekenning.

Raadpleeg de API Management-documentatie voor informatie over het [importeren van een Azure-functie-app](../api-management/import-function-app-as-api.md). Nadat het is geïmporteerd, kunt u een inkomend beleid toevoegen om CORS in te scha kelen met de ondersteuning voor Access-Control-Allow-credentials.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Configureer uw seingevings clients om de API Management URL te gebruiken.

### <a name="using-app-service-authentication"></a>App Service-verificatie gebruiken

Azure Functions heeft ingebouwde verificatie, die populaire providers ondersteunt, zoals Facebook, Twitter, micro soft-account, Google en Azure Active Directory. Deze functie kan worden geïntegreerd met de *SignalRConnectionInfo* -binding voor het maken van verbindingen met de Azure signalerings service die zijn geverifieerd voor een gebruikers-id. Uw toepassing kan berichten verzenden met behulp van de *signaal* uitvoer binding die is gericht op die gebruikers-id.

Open het venster *verificatie/autorisatie-* instellingen in het Azure Portal op het tabblad *platform functies* van uw functie-app. Volg de documentatie voor [app service verificatie](../app-service/overview-authentication-authorization.md) voor het configureren van verificatie met behulp van een id-provider van uw keuze.

Na de configuratie worden geverifieerde HTTP-aanvragen inclusief `x-ms-client-principal-name` en `x-ms-client-principal-id` kopteksten die respectievelijk de gebruikers naam en gebruikers-id van de geverifieerde identiteit bevatten.

U kunt deze headers gebruiken in uw *SignalRConnectionInfo* bindings configuratie om geverifieerde verbindingen te maken. Hier volgt een voor beeld van een C#-onderhandelings functie die gebruikmaakt van de- `x-ms-client-principal-id` header.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

U kunt vervolgens berichten naar die gebruiker verzenden door de `UserId` eigenschap van een signalerings bericht in te stellen.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Zie voor informatie over andere talen de [Azure signalerings service bindingen](../azure-functions/functions-bindings-signalr-service.md) voor Azure functions referentie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u serverloze signalerings service toepassingen kunt ontwikkelen en configureren met behulp van Azure Functions. Probeer zelf een toepassing te maken met behulp van een van de Quick starts of zelf studies op de [overzichts pagina van de signalerings service](index.yml).
