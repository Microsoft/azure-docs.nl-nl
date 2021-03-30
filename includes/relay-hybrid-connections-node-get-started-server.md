---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176625"
---
### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

Maak een nieuw JavaScript-bestand met de naam `listener.js`.

### <a name="add-the-relay-npm-package"></a>Het pakket Relay NPM toevoegen

Voer `npm install hyco-ws` uit vanaf een Node-opdrachtprompt in de projectmap.

### <a name="write-some-code-to-receive-messages"></a>Code schrijven om berichten te ontvangen

1. Voeg de volgende constante toe aan het begin van het bestand `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Voeg de volgende constanten toe aan het bestand `listener.js` voor de gegevens van de hybride verbinding. Vervang de tijdelijke aanduidingen tussen punthaken door de waarden die u hebt verkregen bij het maken van de hybride verbinding.
   
   1. `const ns`: de Relay-naamruimte. Zorg ervoor dat u de volledig gekwalificeerde naamruimte gebruikt, bijvoorbeeld `{namespace}.servicebus.windows.net`.
   2. `const path`: de naam van de hybride verbinding.
   3. `const keyrule`: de naam van de SAS-sleutel.
   4. `const key`: de waarde van de SAS-sleutel.

3. Voeg de volgende code toe aan het bestand `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Zo zou het bestand listener.js er moeten uitzien:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

