---
title: bestand opnemen
description: bestand opnemen
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176272"
---
### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

Maak een nieuw JavaScript-bestand met de naam `listener.js`.

### <a name="add-the-relay-npm-package"></a>Het pakket Relay NPM toevoegen

Voer `npm install hyco-https` uit vanaf een Node-opdrachtprompt in de projectmap.

### <a name="write-some-code-to-handle-requests"></a>Code schrijven om aanvragen af te handelen

1. Voeg de volgende constante toe aan het begin van het bestand `listener.js`.

    ```js
    const https = require('hyco-https');
    ```
2. Voeg de volgende constanten toe aan het bestand `listener.js` voor de gegevens van de hybride verbinding. Vervang de tijdelijke aanduidingen tussen punthaken door de waarden die u hebt verkregen bij het maken van de hybride verbinding.

   1. `const ns`: de Relay-naamruimte. Zorg ervoor dat u de volledig gekwalificeerde naamruimte gebruikt, bijvoorbeeld `{namespace}.servicebus.windows.net`.
   2. `const path`: de naam van de hybride verbinding.
   3. `const keyrule`: de naam van de SAS-sleutel.
   4. `const key`: de waarde van de SAS-sleutel.

3. Voeg de volgende code toe aan het bestand `listener.js`. :

    U ziet dat de code niet veel verschilt van andere eenvoudige HTTP-servervoorbeelden die u kunt vinden in zelfstudies voor beginners over Node.js, met uitzondering van het gebruik van `createRelayedServer` in plaats van de typische functie `createServer`.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    Zo zou het bestand listener.js er moeten uitzien:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

