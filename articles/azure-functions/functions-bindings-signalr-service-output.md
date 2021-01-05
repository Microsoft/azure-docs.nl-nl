---
title: Uitvoer binding van Azure Functions signalerings service
description: Meer informatie over de uitvoer binding van de signaal/service voor Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 159df4c228f74bb605c745e899d99bfff2aa511b
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763282"
---
# <a name="signalr-service-output-binding-for-azure-functions"></a>Uitvoer binding van de signaal/service voor Azure Functions

De *signaal* uitvoer binding gebruiken om een of meer berichten te verzenden met behulp van de Azure signalerings service. U kunt een bericht verzenden naar:

- Alle verbonden clients
- Verbonden clients die zijn geverifieerd voor een specifieke gebruiker

Met de uitvoer binding kunt u ook groepen beheren.

Zie het [overzicht](functions-bindings-signalr-service.md)voor meer informatie over de installatie-en configuratie details.

## <a name="broadcast-to-all-clients"></a>Broadcasten naar alle clients

In het volgende voor beeld ziet u een functie die een bericht verzendt met behulp van de uitvoer binding naar alle verbonden clients. Het *doel* is de naam van de methode die op elke client moet worden aangeroepen. De eigenschap *Arguments* is een matrix met nul of meer objecten die moeten worden door gegeven aan de client methode.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de C#-script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de Python-code:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="send-to-a-user"></a>Naar een gebruiker verzenden

U kunt alleen een bericht verzenden naar verbindingen die zijn geverifieerd voor een gebruiker door de *gebruikers-id* in te stellen in het signaal bericht.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de C#-script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de Python-code:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="send-to-a-group"></a>Naar een groep verzenden

U kunt alleen een bericht verzenden naar verbindingen die zijn toegevoegd aan een groep door de *groeps naam* in het signaal bericht in te stellen.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de C#-script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de Python-code:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="group-management"></a>Groepsbeheer

Met de seingevings service kunnen gebruikers worden toegevoegd aan groepen. Berichten kunnen vervolgens naar een groep worden verzonden. U kunt de `SignalR` uitvoer binding gebruiken om het groepslid maatschap van een gebruiker te beheren.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Als `ClaimsPrincipal` u de juiste binding wilt krijgen, moet u de verificatie-instellingen hebben geconfigureerd in azure functions.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

Voor beeld *function.jsop*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

Voor beeld *function.jsop*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Als `ClaimsPrincipal` u de juiste binding wilt krijgen, moet u de verificatie-instellingen hebben geconfigureerd in azure functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

Voor beeld *function.jsop*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

Voor beeld *function.jsop*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="python"></a>[Python](#tab/python)

### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

Voor beeld *function.jsop*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

Voor beeld *function.jsop*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="java"></a>[Java](#tab/java)

### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Configuratie

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `SignalRConnectionInfo`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**| N.v.t. | Moet worden ingesteld op `signalRConnectionInfo`.|
|**direction**| N.v.t. | Moet worden ingesteld op `in`.|
|**name**| N.v.t. | De naam van de variabele die wordt gebruikt in de functie code voor het verbindings info-object. |
|**hubName**|**HubName**| Deze waarde moet worden ingesteld op de naam van de seingevings hub waarvoor de verbindings gegevens worden gegenereerd.|
|**userId**|**Naam**| Optioneel: de waarde van de claim voor de gebruikers-id die moet worden ingesteld in het toegangs sleutel token. |
|**connectionStringSetting**|**ConnectionStringSetting**| De naam van de app-instelling die de seingevings service connection string bevat (standaard ingesteld op ' AzureSignalRConnectionString ') |

### <a name="signalr"></a>SignalR

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `SignalR`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**| N.v.t. | Moet worden ingesteld op `signalR`.|
|**direction**| N.v.t. | Moet worden ingesteld op `out`.|
|**name**| N.v.t. | De naam van de variabele die wordt gebruikt in de functie code voor het verbindings info-object. |
|**hubName**|**HubName**| Deze waarde moet worden ingesteld op de naam van de seingevings hub waarvoor de verbindings gegevens worden gegenereerd.|
|**connectionStringSetting**|**ConnectionStringSetting**| De naam van de app-instelling die de seingevings service connection string bevat (standaard ingesteld op ' AzureSignalRConnectionString ') |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Volgende stappen

- [Berichten van de seingevings service (binding activeren) afhandelen](./functions-bindings-signalr-service-trigger.md)
- [De URL en het toegangs token van het service-eind punt retour neren (invoer binding)](./functions-bindings-signalr-service-input.md)
