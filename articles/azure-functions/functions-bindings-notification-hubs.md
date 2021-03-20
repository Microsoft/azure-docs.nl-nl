---
title: Notification Hubs bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure notification hub-binding in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: c4198a1b73f76d61e39324befc85b55bd260e363
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212228"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs uitvoer binding voor Azure Functions

In dit artikel wordt uitgelegd hoe u push meldingen verzendt met behulp van [Azure notification hubs](../notification-hubs/notification-hubs-push-notification-overview.md) -bindingen in azure functions. Azure Functions ondersteunt uitvoer bindingen voor Notification Hubs.

Azure Notification Hubs moet worden geconfigureerd voor de PNS (platform Notification Service) die u wilt gebruiken. Zie [Aan de slag met Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) en selecteer uw doelclientplatform in de vervolgkeuzelijst aan de bovenkant van de pagina voor meer informatie over het ophalen van pushmeldingen in uw client-app van Notification Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google heeft [Google Cloud Messaging (GCM) afgeschaft voor Firebase Cloud Messa ging (FCM)](https://developers.google.com/cloud-messaging/faq). Deze uitvoer binding biedt geen ondersteuning voor FCM. Als u meldingen wilt verzenden met behulp van FCM, gebruikt u de [Firebase-API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) direct in uw functie of gebruikt u [sjabloon meldingen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De Notification Hubs-bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. notification hubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet, versie 1. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

Deze binding is niet beschikbaar in de functies 2. x en hoger.

## <a name="example---template"></a>Voor beeld-sjabloon

De meldingen die u verzendt, kunnen systeem eigen meldingen of [sjabloon meldingen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)zijn. Systeem eigen meldingen richten zich op een specifiek client platform zoals geconfigureerd in de `platform` eigenschap van de uitvoer binding. Een sjabloon melding kan worden gebruikt om te richten op meerdere platforms.   

Zie het taalspecifieke voor beeld:

* [C# script-out-para meter](#c-script-template-example---out-parameter)
* [C#-script-asynchroon](#c-script-template-example---asynchronous)
* [C#-script-JSON](#c-script-template-example---json)
* [C#-script-bibliotheek typen](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Voorbeeld parameter sjabloon C#-script

In dit voor beeld wordt een melding verzonden voor een [sjabloon registratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) die een `message` tijdelijke aanduiding in de sjabloon bevat.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C#-script sjabloon voor beeld-asynchroon

Als u asynchrone code gebruikt, zijn out-para meters niet toegestaan. In dit geval gebruikt `IAsyncCollector` u om uw sjabloon melding te retour neren. De volgende code is een asynchroon voor beeld van de bovenstaande code. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C#-script sjabloon voor beeld-JSON

In dit voor beeld wordt een melding verzonden voor een [sjabloon registratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) die een `message` tijdelijke aanduiding in de sjabloon bevat met een geldige JSON-teken reeks.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#-script sjabloon voor beeld: bibliotheek typen

In dit voor beeld ziet u hoe typen worden gebruikt die in de [Microsoft Azure notification hubs-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)zijn gedefinieerd. 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Voor beeld van F #-sjabloon

In dit voor beeld wordt een melding verzonden voor een [sjabloon registratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) met `location` en `message` .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Java script-sjabloon voorbeeld

In dit voor beeld wordt een melding verzonden voor een [sjabloon registratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) met `location` en `message` .

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Voor beeld-APNS-systeem eigen

In dit voor beeld van een C#-script ziet u hoe u een systeem eigen APNS-melding verzendt. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Voor beeld-WNS systeem eigen

In dit voor beeld van een C#-script ziet u hoe u in de [Microsoft Azure notification hubs-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) typen kunt gebruiken om een systeem eigen wns-pop-upmelding te verzenden. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) .

De para meters en eigenschappen van de constructor van het kenmerk worden beschreven in de sectie [configuratie](#configuration) .

## <a name="configuration"></a>Configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `NotificationHub` kenmerk:

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** |N.v.t.| Moet worden ingesteld op `notificationHub`. |
|**direction** |N.v.t.| Moet worden ingesteld op `out`. | 
|**name** |N.v.t.| De naam van de variabele die wordt gebruikt in de functie code voor het bericht van de notification hub. |
|**tagExpression** |**TagExpression** | Met label expressies kunt u opgeven dat meldingen worden bezorgd bij een set apparaten die zijn geregistreerd voor het ontvangen van meldingen die overeenkomen met de tag-expressie.  Zie [route ring en label expressies](../notification-hubs/notification-hubs-tags-segment-push-message.md)voor meer informatie. |
|**hubName** | **HubName** | De naam van de notification hub-resource in de Azure Portal. |
|**connection** | **ConnectionStringSetting** | De naam van een app-instelling die een Notification Hubs connection string bevat.  De connection string moet worden ingesteld op de waarde *DefaultFullSharedAccessSignature* voor uw notification hub. Zie de installatie van de [verbindings reeks](#connection-string-setup) later in dit artikel.|
|**onafhankelijk** | **Platform** | De platform eigenschap geeft aan op welk client platform uw meldings doelen zijn gericht. Als de platform eigenschap wordt wegge laten uit de uitvoer binding, kunnen standaard sjabloon meldingen worden gebruikt om te richten op elk platform dat is geconfigureerd op de Azure notification hub. Zie [sjablonen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)voor meer informatie over het gebruik van sjablonen in het algemeen voor het verzenden van meldingen tussen verschillende platforms met een Azure notification hub. Wanneer het is ingesteld, moet **platform** een van de volgende waarden zijn: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Zie [Push meldingen verzenden naar IOS met Azure notification hubs](../notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started.md)voor meer informatie over het configureren van de notification hub voor APNS en het ontvangen van de melding in een client-app.</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Zie aan de slag [met Notification hubs voor Kindle-apps](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)voor meer informatie over het configureren van de notification hub voor adm en het ontvangen van de melding in een Kindle-app.</li><li><code>wns</code>&mdash;[Windows-Push Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) gericht op Windows-platforms. Windows Phone 8,1 en hoger wordt ook ondersteund door WNS. Zie aan de slag [met Notification hubs voor Windows Universal platform-apps](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor meer informatie.</li><li><code>mpns</code>&mdash;[Micro soft Push Notification Service](/previous-versions/windows/apps/ff402558(v=vs.105)). Dit platform ondersteunt Windows Phone 8 en eerdere Windows Phone platforms. Zie [Push meldingen verzenden met Azure notification hubs op Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)voor meer informatie.</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Voor beeld van function.jsin bestand

Hier volgt een voor beeld van een Notification Hubs binding in een *function.jsin* het bestand.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Verbindingsteken reeks instellen

Als u een notification hub-uitvoer binding wilt gebruiken, moet u de connection string voor de hub configureren. U kunt een bestaande notification hub selecteren of een nieuw item maken op het tabblad *integreren* in de Azure Portal. U kunt de connection string ook hand matig configureren. 

De connection string configureren voor een bestaande notification hub:

1. Navigeer naar uw notification hub in de [Azure Portal](https://portal.azure.com), kies **toegangs beleid** en selecteer de knop kopiëren naast het **DefaultFullSharedAccessSignature** -beleid. Hiermee wordt de connection string voor het *DefaultFullSharedAccessSignature* -beleid naar uw notification hub gekopieerd. Met deze connection string kunt u met de functie meldings berichten verzenden naar de hub.
    ![De notification hub kopiëren connection string](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigeer naar uw functie-app in de Azure Portal, kies **Toepassings instellingen**, voeg een sleutel toe, zoals **MyHubConnectionString**, plak de gekopieerde *DefaultFullSharedAccessSignature*  voor uw notification hub als waarde en klik vervolgens op **Opslaan**.

De naam van deze toepassings instelling is wat er gebeurt in de instelling voor de verbindings binding van de uitvoer in *function.js* of het .net-kenmerk. Zie de [sectie configuratie](#configuration) eerder in dit artikel.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Notification Hub | [Operations Guide](/rest/api/notificationhubs/) (Bedieningshandleiding) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
