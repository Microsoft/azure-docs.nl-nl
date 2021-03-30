---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176622"
---
### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

Maak een nieuw JavaScript-bestand met de naam `sender.js`.

### <a name="add-the-relay-npm-package"></a>Het pakket Relay NPM toevoegen

Voer `npm install hyco-ws` uit vanaf een Node-opdrachtprompt in de projectmap.

### <a name="write-some-code-to-send-messages"></a>Code schrijven om berichten te verzenden

1. Plaats de volgende `constants` boven aan het bestand `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Voeg de volgende constanten toe aan het bestand `sender.js` voor de gegevens van de hybride verbinding. Vervang de tijdelijke aanduidingen tussen punthaken door de waarden die u hebt verkregen bij het maken van de hybride verbinding.
   
   1. `const ns`: de Relay-naamruimte. Zorg ervoor dat u de volledig gekwalificeerde naamruimte gebruikt, bijvoorbeeld `{namespace}.servicebus.windows.net`.
   2. `const path`: de naam van de hybride verbinding.
   3. `const keyrule`: de naam van de SAS-sleutel.
   4. `const key`: de waarde van de SAS-sleutel.

3. Voeg de volgende code toe aan het bestand `sender.js`:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Het bestand sender.js moet er als volgt uitzien:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

