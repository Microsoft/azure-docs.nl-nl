---
title: Azure Queue Storage gebruiken vanuit Node.js-Azure Storage
description: Meer informatie over het gebruik van Azure Queue Storage voor het maken en verwijderen van wacht rijen. Meer informatie over het invoegen, ophalen en verwijderen van berichten met behulp van Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803677"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Azure Queue Storage gebruiken vanuit Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Overzicht

Deze hand leiding laat zien hoe u veelvoorkomende scenario's kunt uitvoeren met behulp van Azure Queue Storage. De voor beelden zijn geschreven met behulp van de Node.js-API. De gedekte scenario's zijn het invoegen, inspecteren, ophalen en verwijderen van wachtrij berichten. Meer informatie over het maken en verwijderen van wacht rijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

Als u een lege Node.js-toepassing wilt maken, raadpleegt u [een Node.js web-app maken in azure app service](../../app-service/quickstart-nodejs.md), [een Node.js-toepassing bouwen en implementeren in azure Cloud Services](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) met Power shell of [Visual Studio code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Uw toepassing configureren voor toegang tot opslag

De [Azure Storage-client bibliotheek voor Java script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) bevat een reeks gebruiks vriendelijke bibliotheken die communiceren met de opslag rest-Services.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Gebruik node Package Manager (NPM) om het pakket te verkrijgen

1. Gebruik een opdracht regel interface zoals Power shell (Windows), Terminal (Mac) of bash (UNIX), navigeer naar de map waarin u de voorbeeld toepassing hebt gemaakt.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Typ `npm install @azure/storage-queue` in het opdracht venster.

1. Controleer of er een `node_modules` map is gemaakt. In deze map vindt u het `@azure/storage-queue` pakket, dat de client bibliotheek bevat die u nodig hebt om toegang te krijgen tot de opslag.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

1. Typ `npm install azure-storage` in het opdracht venster.

1. Controleer of er een `node_modules` map is gemaakt. In die map vindt u het `azure-storage` pakket, dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot opslag.

---

### <a name="import-the-package"></a>Het pakket importeren

Gebruik uw code-editor om het volgende toe te voegen aan de bovenkant van het Java script-bestand waar u wacht rijen wilt gebruiken.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Een wachtrij maken

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Met de volgende code wordt de waarde van een omgevings variabele met de naam opgehaald `AZURE_STORAGE_CONNECTION_STRING` en gebruikt om een object te maken [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) . Dit object wordt vervolgens gebruikt voor het maken [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) van een object waarmee u met een specifieke wachtrij kunt werken.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Als de wachtrij al bestaat, wordt er een uitzonde ring gegenereerd.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

In de Azure-module worden de omgevings variabelen `AZURE_STORAGE_ACCOUNT` en `AZURE_STORAGE_ACCESS_KEY` , of `AZURE_STORAGE_CONNECTION_STRING` voor de gegevens die nodig zijn om verbinding te maken met uw Azure Storage-account, gelezen. Als deze omgevings variabelen niet zijn ingesteld, moet u de account gegevens opgeven wanneer u aanroept `createQueueService` .

Met de volgende code wordt een `QueueService` -object gemaakt, waarmee u met wacht rijen kunt werken.

```javascript
var queueSvc = azure.createQueueService();
```

Roep de `createQueueIfNotExists` methode aan om een nieuwe wachtrij met de opgegeven naam te maken of de wachtrij te retour neren als deze al bestaat.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Als de wachtrij is gemaakt, `result.created` is waar. Als de wachtrij bestaat, `result.created` is onwaar.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een bericht wilt toevoegen aan een wachtrij, roept u de- [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) methode aan.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Als u een bericht in een wachtrij wilt invoegen, roept `createMessage` u de methode voor het maken van een nieuw bericht op en voegt u het toe aan de wachtrij.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Het volgende bericht bekijken

U kunt de berichten in de wachtrij bekijken zonder ze uit de wachtrij te verwijderen door de methode aan te roepen `peekMessages` .

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

In wordt standaard weer [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) gegeven als één bericht. In het volgende voor beeld worden de eerste vijf berichten in de wachtrij weer geven. Als er minder dan vijf berichten zichtbaar zijn, worden alleen de zicht bare berichten weer gegeven.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

In wordt standaard weer `peekMessages` gegeven als één bericht.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Het `result` bevat het bericht.

---

`peekMessages`Als er geen berichten in de wachtrij worden aangeroepen, wordt er geen fout geretourneerd. Er worden echter geen berichten geretourneerd.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

In het volgende voor beeld wordt de tekst van een bericht bijgewerkt.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Wijzig de inhoud van een bericht dat in de wachtrij wordt geplaatst door aan te roepen [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Wijzig de inhoud van een bericht dat in de wachtrij wordt geplaatst door aan te roepen `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Een bericht uit de wachtrij verwijderen

Een bericht uit de wachtrij verwijderen is een proces in twee fasen:

1. Het bericht ophalen.

1. Verwijder het bericht.

In het volgende voor beeld wordt een bericht opgehaald en vervolgens verwijderd.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een bericht wilt ontvangen, roept u de- [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) methode aan. Met deze aanroep worden de berichten onzichtbaar gemaakt in de wachtrij, zodat andere clients deze niet kunnen verwerken. Als uw toepassing een bericht heeft verwerkt, [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) kunt u de aanroep verwijderen uit de wachtrij.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Standaard wordt een bericht 30 seconden verborgen. Na 30 seconden is de weer gave van andere clients zichtbaar. U kunt een andere waarde opgeven door in te stellen [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) Wanneer u aanroept `receiveMessages` .

`receiveMessages`Als er geen berichten in de wachtrij worden aangeroepen, wordt er geen fout geretourneerd. Er worden echter geen berichten geretourneerd.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Als u een bericht wilt ontvangen, roept u de- `getMessages` methode aan. Met deze aanroep worden de berichten onzichtbaar gemaakt in de wachtrij, zodat andere clients deze niet kunnen verwerken. Als uw toepassing een bericht heeft verwerkt, `deleteMessage` kunt u de aanroep verwijderen uit de wachtrij.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Standaard wordt een bericht 30 seconden verborgen. Na 30 seconden is de weer gave van andere clients zichtbaar. U kunt een andere waarde opgeven met `options.visibilityTimeout` with `getMessages` .

`getMessages`Als er geen berichten in de wachtrij staan, wordt er geen fout geretourneerd. Er worden echter geen berichten geretourneerd.

---

## <a name="additional-options-for-dequeuing-messages"></a>Aanvullende opties voor het dequeuing van berichten

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Haal een batch berichten op (Maxi maal 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Stel een langere of korte time-out in voor de zicht baarheid.

In het volgende voor beeld wordt de `receiveMessages` methode gebruikt om vijf berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `for` lus verwerkt. Ook wordt de time-out voor ininzicht ingesteld op vijf minuten voor alle berichten die door deze methode worden geretourneerd.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen:

- `options.numOfMessages`: Haal een batch berichten op (Maxi maal 32).
- `options.visibilityTimeout`: Stel een langere of korte time-out in voor de zicht baarheid.

In het volgende voor beeld wordt de `getMessages` methode gebruikt om 15 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `for` lus verwerkt. Ook wordt de time-out voor ininzicht ingesteld op vijf minuten voor alle berichten die door deze methode worden geretourneerd.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>De lengte van de wachtrij ophalen

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

De [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) -methode retourneert meta gegevens over de wachtrij, met inbegrip van het geschatte aantal berichten in de wachtrij.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

De `getQueueMetadata` -methode retourneert meta gegevens over de wachtrij, met inbegrip van het geschatte aantal berichten in de wachtrij.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Wacht rijen weer geven

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Om een lijst met wacht rijen op te halen, roept u aan [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Als u een lijst wilt ophalen die is gefilterd op een specifiek voor voegsel, stelt u [Opties. voor voegsel](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) in de aanroep van in `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Gebruik om een lijst met wacht rijen op te halen `listQueuesSegmented` . Als u een lijst wilt ophalen die is gefilterd op een specifiek voor voegsel, gebruikt u `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Als niet alle wacht rijen kunnen worden geretourneerd, geeft `result.continuationToken` u door als de eerste para meter van `listQueuesSegmented` of de tweede para meter van `listQueuesSegmentedWithPrefix` voor het ophalen van meer resultaten.

---

## <a name="how-to-delete-a-queue"></a>Een wachtrij verwijderen

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de- [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) methode aan voor het `QueueClient` object.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Als u alle berichten uit een wachtrij wilt wissen zonder deze te verwijderen, roept u aan [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de `deleteQueue` methode aan in het wachtrij object.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Als u alle berichten uit een wachtrij wilt wissen zonder deze te verwijderen, roept u aan `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

- Ga naar het [Azure Storage-Team blog](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) voor meer informatie over wat er nieuw is
- Ga naar de [Azure Storage-client bibliotheek voor de Java script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) -opslag plaats op github
