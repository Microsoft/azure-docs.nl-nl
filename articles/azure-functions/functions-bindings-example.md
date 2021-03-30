---
title: Voorbeeld van trigger en binding met Azure Functions
description: Meer informatie over het configureren van Azure function-bindingen
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205972"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Voorbeeld van trigger en binding met Azure Functions

In dit artikel wordt beschreven hoe u een [trigger en bindingen](./functions-triggers-bindings.md) configureert in een Azure-functie.

Stel dat u een nieuwe rij wilt schrijven naar Azure-tabel opslag wanneer er een nieuw bericht wordt weer gegeven in de Azure-wachtrij opslag. Dit scenario kan worden geïmplementeerd met een Azure Queue Storage-trigger en een Azure Table Storage-uitvoerbinding. 

Hier volgt een *function.jsin* het bestand voor dit scenario. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Het eerste element in de `bindings` matrix is de opslag trigger voor de wachtrij. De `type` `direction` Eigenschappen en identificeren de trigger. De `name` eigenschap identificeert de functie parameter die de inhoud van het wachtrij bericht ontvangt. De naam van de wachtrij die moet worden bewaakt `queueName` , en de Connection String bevindt zich in de app-instelling geïdentificeerd door `connection` .

Het tweede element in de `bindings` matrix is de Azure Table Storage-uitvoer binding. De `type` `direction` Eigenschappen en identificeren de binding. De `name` eigenschap geeft aan hoe de functie de nieuwe tabelrij levert, in dit geval met behulp van de functie retour waarde. De naam van de tabel is in `tableName` en de Connection String bevindt zich in de app-instelling geïdentificeerd door `connection` .

Als u de inhoud van *function.js* wilt weer geven en bewerken in de Azure Portal, klikt u op de optie **Geavanceerde editor** op het tabblad **integreren** van uw functie.

> [!NOTE]
> De waarde van `connection` is de naam van een app-instelling die de Connection String bevat, niet de connection string zelf. Bindingen gebruiken verbindings reeksen die zijn opgeslagen in de app-instellingen om de best practice te afdwingen waarvan *function.json* geen service geheimen bevat.

## <a name="c-script-example"></a>Voor beeld van C#-script

Hier volgt een C#-script code die werkt met deze trigger en binding. U ziet dat de naam van de para meter die de inhoud van het wachtrij bericht levert `order` , is. deze naam is vereist omdat de waarde van de `name` eigenschap in *function.jsop* is `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Java script-voor beeld

Dezelfde *function.jsvoor* het bestand kan worden gebruikt met een Java script-functie:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Class Library-voor beeld

In een klassen bibliotheek worden dezelfde trigger-en binding informatie &mdash; wachtrij en tabel namen, opslag accounts, functie parameters voor invoer en uitvoer &mdash; verstrekt door kenmerken in plaats van een function.jsin het bestand. Hier volgt een voorbeeld:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

U hebt nu een werk functie die wordt geactiveerd door een Azure-wachtrij en waarmee gegevens worden uitgevoerd naar Azure Table Storage.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Expressie patronen voor de binding Azure Functions](./functions-bindings-expressions-patterns.md)
