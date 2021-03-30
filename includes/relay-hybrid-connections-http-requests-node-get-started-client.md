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
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176273"
---
### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

Als u de optie 'Vereist clientautorisatie' hebt uitgeschakeld tijdens het maken van de relay, kunt u met elke browser aanvragen verzenden naar de URL van de hybride verbindingen. Voor toegang tot beveiligde eindpunten moet u een token maken en doorgeven in de header `ServiceBusAuthorization`, die hier wordt weergegeven.

U begint door een nieuw JavaScript-bestand met de naam `sender.js` te maken.

### <a name="add-the-relay-npm-package"></a>Het pakket Relay NPM toevoegen

Voer `npm install hyco-https` uit vanaf een Node-opdrachtprompt in de projectmap. Dit pakket importeert ook het gewone `https`-pakket. Aan de clientzijde is het belangrijkste verschil dat het pakket functies biedt voor het samenstellen van URI's en tokens voor de relay.

### <a name="write-some-code-to-send-messages"></a>Code schrijven om berichten te verzenden

1. Plaats de volgende `constants` boven aan het bestand `sender.js`.
   
    ```js
    const https = require('hyco-https');
    ```

2. Voeg de volgende constanten toe aan het bestand `sender.js` voor de gegevens van de hybride verbinding. Vervang de tijdelijke aanduidingen tussen punthaken door de waarden die u hebt verkregen bij het maken van de hybride verbinding.
   
   1. `const ns`: de Relay-naamruimte. Zorg ervoor dat u de volledig gekwalificeerde naamruimte gebruikt, bijvoorbeeld `{namespace}.servicebus.windows.net`.
   2. `const path`: de naam van de hybride verbinding.
   3. `const keyrule`: de naam van de SAS-sleutel.
   4. `const key`: de waarde van de SAS-sleutel.

3. Voeg de volgende code toe aan het bestand `sender.js`. U ziet dat de code niet veel verschilt van het normale gebruik van de Node.js-HTTPS-client; er wordt alleen een autorisatie-header toegevoegd.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Het bestand sender.js moet er als volgt uitzien:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

