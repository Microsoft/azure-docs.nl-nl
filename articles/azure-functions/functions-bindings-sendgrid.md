---
title: Azure Functions SendGrid-bindingen
description: Azure Functions SendGrid-bindings verwijzing.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: b3d09ec4c4ab578a87f0d983c0f243bee2a84597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94991227"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid-bindingen

In dit artikel wordt uitgelegd hoe u e-mail verzendt met behulp van [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) -bindingen in azure functions. Azure Functions ondersteunt een uitvoer binding voor SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De SendGrid-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-pakket, versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De SendGrid-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die gebruikmaakt van een service bus wachtrij trigger en een SendGrid-uitvoer binding.

### <a name="synchronous"></a>Synchroon

```cs
using SendGrid.Helpers.Mail;
using System.Text.Json;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonSerializer.Deserialize<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Asynchroon

```cs
using SendGrid.Helpers.Mail;
using System.Text.Json;

...

[FunctionName("SendEmail")]
public static async Task Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonSerializer.Deserialize<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

U kunt de instelling van de eigenschap van het kenmerk weglaten `ApiKey` Als u uw API-sleutel hebt in een app-instelling met de naam ' AzureWebJobsSendGridApiKey '.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een SendGrid-uitvoer binding in eenfunction.jsin een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt *van* de binding.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een SendGrid-uitvoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een door HTTP geactiveerde functie die een e-mail verzendt met behulp van de SendGrid-binding. U kunt standaard waarden opgeven in de binding configuratie. Zo is het e-mail adres *van* e-mail geconfigureerd in *function.jsop*. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

De volgende functie laat zien hoe u aangepaste waarden voor optionele eigenschappen kunt opgeven.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld wordt de `@SendGridOutput` aantekening uit de [runtime-bibliotheek van Java functions](/java/api/overview/azure/functions/runtime) gebruikt om een e-mail te verzenden met de SendGrid-uitvoer binding.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) .

Zie [configuratie](#configuration)voor informatie over kenmerk eigenschappen die u kunt configureren. Hier volgt een `SendGrid` voor beeld van een kenmerk in een methode handtekening:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Zie C#-voor [beeld](#example)voor een volledig voor beeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Met de aantekening [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) kunt u de SendGrid-binding declaratief configureren door configuratie waarden op te geven. Zie het voor [beeld](#example) en de [configuratie](#configuration) secties voor meer informatie.

---

## <a name="configuration"></a>Configuratie

De volgende tabel geeft een lijst van de bindings configuratie-eigenschappen die beschikbaar zijn in de  *function.jsin* het bestand en het `SendGrid` kenmerk/aantekening.

| *function.jsbij* eigenschap | Kenmerk/annotatie-eigenschap | Description | Optioneel |
|--------------------------|-------------------------------|-------------|----------|
| type |N.v.t.| Moet worden ingesteld op `sendGrid`.| No |
| richting |N.v.t.| Moet worden ingesteld op `out`.| No |
| naam |n.v.t.| De naam van de variabele die wordt gebruikt in de functie code voor de aanvraag of aanvraag tekst. Deze waarde is `$return` wanneer er slechts één retour waarde is. | No |
| apiKey | ApiKey | De naam van een app-instelling die uw API-sleutel bevat. Als deze niet is ingesteld, is de standaard naam voor de app-instelling *AzureWebJobsSendGridApiKey*.| No |
| tot| Tot | Het e-mail adres van de ontvanger. | Yes |
| from| Van | Het e-mail adres van de afzender. |  Yes |
| Onderwerp| Onderwerp | Het onderwerp van het e-mail bericht. | Ja |
| tekst| Tekst | De inhoud van het e-mail bericht. | Yes |

Voor optionele eigenschappen zijn mogelijk standaard waarden in de binding gedefinieerd en worden ze programmatisch toegevoegd of genegeerd.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsop instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het volgende voor beeld host.jsin het onderstaande bestand bevat alleen de instellingen van versie 2. x + voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x en verder [host.jsop referentie voor Azure functions](functions-host-json.md).

> [!NOTE]
> Zie [host.jsbij verwijzing voor Azure functions 1. x](functions-host-json-v1.md)voor een referentie van host.jsin in functions 1. x.

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|from|n.v.t.|Het e-mail adres van de afzender over alle functies.| 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
