---
title: Overzicht van de Api's voor het Azure Relay-knoop punt | Microsoft Docs
description: Dit artikel bevat een overzicht van de Node.js-API voor de Azure Relay-service. Ook wordt uitgelegd hoe u het hyco-WS-knooppunt pakket gebruikt.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 558f49c09203192ff4cbb1af392eaeef8d705c94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263484"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Overzicht van de API voor relay Hybride verbindingen-knoop punt

## <a name="overview"></a>Overzicht

Het [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) knooppunt pakket voor Azure Relay hybride verbindingen is gebaseerd op en uitgebreid het [WS](https://www.npmjs.com/package/ws) -NPM-pakket. Met dit pakket worden alle exports van het basis pakket opnieuw geëxporteerd en worden nieuwe exports toegevoegd waarmee integratie met de Azure Relay service Hybride verbindingen functie wordt ingeschakeld. 

Bestaande toepassingen die `require('ws')` Dit pakket kunnen gebruiken in `require('hyco-ws')` plaats daarvan, waarbij ook hybride scenario's kunnen worden ingeschakeld waarin een toepassing lokaal vanaf de firewall kan Luis teren naar WebSocket-verbindingen en via hybride verbindingen tegelijk.
  
## <a name="documentation"></a>Documentatie

De Api's worden [beschreven in het hoofd pakket ' WS '](https://github.com/websockets/ws/blob/master/doc/ws.md). In dit artikel wordt beschreven hoe dit pakket afwijkt van die basis lijn. 

De belangrijkste verschillen tussen het basis pakket en het ' hyco-WS ' zijn dat er een nieuwe server klasse wordt toegevoegd, geëxporteerd via `require('hyco-ws').RelayedServer` en een paar helper-methoden.

### <a name="package-helper-methods"></a>Pakket helper-methoden

Er zijn verschillende hulpprogramma methoden beschikbaar voor het exporteren van het pakket. u kunt als volgt een verwijzing maken:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

De helper-methoden zijn bestemd voor gebruik met dit pakket, maar kunnen ook worden gebruikt door een knooppunt server om web-of apparaatclients in te scha kelen om listeners of afzenders te maken. De server maakt gebruik van deze methoden door Uri's toe te voegen die de korte-levens tokens insluiten. Deze Uri's kunnen ook worden gebruikt met algemene WebSocket-stacks die geen ondersteuning bieden voor het instellen van HTTP-headers voor de WebSocket-handshake. Het insluiten van autorisatie tokens in de URI wordt voornamelijk ondersteund voor die bibliotheek-externe gebruiks scenario's. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Hiermee maakt u een geldige Azure Relay URI voor de Hybrid Connection-listener voor de opgegeven naam ruimte en het pad. Deze URI kan vervolgens worden gebruikt met de relay-versie van de WebSocketServer-klasse.

- `namespaceName` (vereist): de domein gekwalificeerde naam van de Azure Relay naam ruimte die moet worden gebruikt.
- `path` (vereist): de naam van een bestaande Azure Relay hybride verbinding in die naam ruimte.
- `token` (optioneel): een eerder uitgegeven relay-toegangs token dat is inge sloten in de listener URI (Zie het volgende voor beeld).
- `id` (optioneel): een tracerings-ID die end-to-end diagnostische gegevens tracering van aanvragen mogelijk maakt.

De `token` waarde is optioneel en moet alleen worden gebruikt wanneer het niet mogelijk is om HTTP-headers samen met de WebSocket-Handshake te verzenden, net als bij de W3C-WebSocket-stack.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Hiermee maakt u een geldige Azure Relay hybride verbinding Send URI voor de opgegeven naam ruimte en het pad. Deze URI kan worden gebruikt met een WebSocket-client.

- `namespaceName` (vereist): de domein gekwalificeerde naam van de Azure Relay naam ruimte die moet worden gebruikt.
- `path` (vereist): de naam van een bestaande Azure Relay hybride verbinding in die naam ruimte.
- `token` (optioneel): een eerder uitgegeven relay-toegangs token dat is inge sloten in de verzend-URI (Zie het volgende voor beeld).
- `id` (optioneel): een tracerings-ID die end-to-end diagnostische gegevens tracering van aanvragen mogelijk maakt.

De `token` waarde is optioneel en moet alleen worden gebruikt wanneer het niet mogelijk is om HTTP-headers samen met de WebSocket-Handshake te verzenden, net als bij de W3C-WebSocket-stack.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Hiermee maakt u een Azure Relay-Shared Access Signature (SAS)-token voor de opgegeven doel-URI, SAS-regel en SAS-regel sleutel die geldig zijn voor het opgegeven aantal seconden of voor een uur vanaf het huidige moment dat het argument Expires wordt wegge laten.

- `uri` (vereist): de URI waarvoor het token moet worden uitgegeven. De URI wordt genormaliseerd voor het gebruik van het HTTP-schema en de query teken reeks gegevens worden verwijderd.
- `ruleName` (vereist)-SAS-regel naam voor de entiteit die wordt vertegenwoordigd door de opgegeven URI of voor de naam ruimte die wordt vertegenwoordigd door het URI-host gedeelte.
- `key` (vereist)-geldige sleutel voor de SAS-regel. 
- `expirationSeconds` (optioneel): het aantal seconden tot het gegenereerde token moet verlopen. Als niet wordt opgegeven, is de standaard waarde 1 uur (3600).

Het uitgegeven token verleent de rechten die zijn gekoppeld aan de opgegeven SAS-regel voor de opgegeven duur.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Deze methode is functioneel equivalent aan de `createRelayToken` eerder beschreven methode, maar retourneert het token correct toe aan de invoer-URI.

### <a name="class-wsrelayedserver"></a>Klasse WS. RelayedServer

De `hycows.RelayedServer` klasse is een alternatief voor de `ws.Server` klasse die niet luistert op het lokale netwerk, maar die luistert naar de Azure Relay-service.

De twee klassen zijn voornamelijk contract compatibel, wat inhoudt dat een bestaande toepassing die de klasse gebruikt, `ws.Server` gemakkelijk kan worden gewijzigd om de relay-versie te gebruiken. De belangrijkste verschillen bevinden zich in de constructor en in de beschik bare opties.

#### <a name="constructor"></a>Constructor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

De `RelayedServer` constructor ondersteunt een andere set argumenten dan de `Server` , omdat deze geen zelfstandige listener is of kan worden inge sloten in een bestaand HTTP-listener-Framework. Er zijn ook minder opties beschikbaar, omdat het beheer van websockets grotendeels wordt overgedragen aan de Relay-service.

Constructor-argumenten:

- `server` (vereist): de volledig gekwalificeerde URI voor de naam van een hybride verbinding waarop moet worden geluisterd, meestal gemaakt met de helper-methode WebSocket. createRelayListenUri ().
- `token` (vereist): dit argument bevat een eerder uitgegeven token teken reeks of een call back-functie die kan worden aangeroepen om een dergelijke token teken reeks te verkrijgen. De terugbel optie verdient de voor keur bij het vernieuwen van tokens.

#### <a name="events"></a>Gebeurtenissen

`RelayedServer` instanties verzenden drie gebeurtenissen waarmee u binnenkomende aanvragen kunt afhandelen, verbindingen tot stand kunt brengen en fout voorwaarden kunt detecteren. U moet zich abonneren op de `connect` gebeurtenis voor het afhandelen van berichten. 

##### <a name="headers"></a>koppen

```JavaScript 
function(headers)
```

De `headers` gebeurtenis treedt op net voordat een binnenkomende verbinding wordt geaccepteerd, waardoor het wijzigen van de headers kan worden verzonden naar de client. 

##### <a name="connection"></a>verbinding

```JavaScript
function(socket)
```

Verzonden wanneer een nieuwe WebSocket-verbinding wordt geaccepteerd. Het object is van het type `ws.WebSocket` , hetzelfde als bij het basis pakket.


##### <a name="error"></a>fout

```JavaScript
function(error)
```

Als de onderliggende server een fout verzendt, wordt deze hier doorgestuurd.  

#### <a name="helpers"></a>Helpers

Om het starten van een omleidings server te vereenvoudigen en zich onmiddellijk te abonneren op binnenkomende verbindingen, wordt in het pakket een eenvoudige Help-functie weer gegeven, die ook in de voor beelden wordt gebruikt, als volgt:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Met deze methode wordt de constructor aangeroepen om een nieuw exemplaar van de RelayedServer te maken en wordt de geleverde call back vervolgens geabonneerd op de gebeurtenis Connection.
 
##### <a name="relayedconnect"></a>relayedConnect

Het spie gelen `createRelayedServer` van de helper in functie, `relayedConnect` maakt een client verbinding en abonneert zich op de gebeurtenis openen op de resulterende socket.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over Azure Relay naar deze koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Beschik bare relay-Api's](relay-api-overview.md)
