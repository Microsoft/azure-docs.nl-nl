---
title: Twilio gebruiken voor spraak-, VoIP-en SMS-berichten in azure
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Code voorbeelden geschreven in Node.js.
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: c4b1c506ff0e78e5e304ceb1682c73f9cd784c95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88080538"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Twilio gebruiken voor spraak-, VoIP-en SMS-berichten in azure
In deze hand leiding wordt gedemonstreerd hoe u apps bouwt die communiceren met Twilio en node.js op Azure.

<a name="whatis"></a>

## <a name="what-is-twilio"></a>Wat is Twilio?
Twilio is een API-platform waarmee ontwikkel aars eenvoudig telefoon gesprekken kunnen voeren en ontvangen, SMS-berichten kunnen verzenden en ontvangen, en VoIP kunt insluiten in browser-en systeem eigen mobiele toepassingen. Laten we eens kijken hoe dit werkt voordat u aan de slag gaat.

### <a name="receiving-calls-and-text-messages"></a>Oproepen en SMS-berichten ontvangen
Met Twilio kunnen ontwikkel aars [Programmeer bare telefoon nummers kopen][purchase_phone] die kunnen worden gebruikt voor het verzenden en ontvangen van gesp rekken en SMS-berichten. Wanneer een Twilio-nummer een inkomende oproep of tekst ontvangt, stuurt Twilio uw webtoepassing een HTTP POST-of GET-aanvraag, waarin u wordt gevraagd om instructies te geven over het afhandelen van de aanroep of tekst. Uw server reageert op de HTTP-aanvraag van Twilio met [TwiML][twiml], een eenvoudige set XML-tags die instructies bevatten over het afhandelen van een aanroep of tekst. Voor beelden van TwiML worden in slechts een moment weer gegeven.

### <a name="making-calls-and-sending-text-messages"></a>Aanroepen en verzenden van tekst berichten
Met behulp van HTTP-aanvragen voor de Twilio Web Service-API kunnen ontwikkel aars tekst berichten verzenden of uitgaande telefoon gesprekken initiëren. Voor uitgaande oproepen moet de ontwikkelaar ook een URL opgeven die TwiML instructies retourneert voor het afhandelen van de uitgaande oproep zodra deze is verbonden.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>VoIP-mogelijkheden insluiten in gebruikers interface code (Java script, iOS of Android)
Twilio biedt een SDK aan de client zijde die een bureaublad webbrowser, iOS-app of Android-app kan omzetten in een VoIP-telefoon. In dit artikel wordt aandacht besteed aan het gebruik van VoIP-aanroepen in de browser. Naast de *Twilio java script SDK* die in de browser wordt uitgevoerd, moet een toepassing aan de server zijde (onze node.js-toepassing) worden gebruikt voor het uitgeven van een ' bekwaamheids token ' aan de Java script-client. Meer informatie over het gebruik van VoIP met node.js vindt u [in de Twilio dev-blog][voipnode].

<a name="signup"></a>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Meld u aan voor Twilio (korting van micro soft)
Voordat u Twilio Services kunt gebruiken, moet u [zich eerst aanmelden voor een account][signup]. Microsoft Azure klanten een speciale korting ontvangen, moet [u zich hier aanmelden][signup].

<a name="azuresite"></a>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Een node.js Azure-website maken en implementeren
Vervolgens moet u een node.js website maken die op Azure wordt uitgevoerd. [De officiële documentatie][azure_new_site]hiervoor vindt u hier. Op hoog niveau gaat u als volgt te werk:

* Aanmelden voor een Azure-account, als u er nog geen hebt
* De Azure-beheer console gebruiken om een nieuwe website te maken
* Ondersteuning voor broncode beheer toevoegen (er wordt ervan uitgegaan dat u Git hebt gebruikt)
* Een bestand `server.js` met een eenvoudige node.js-webtoepassing maken
* Deze eenvoudige toepassing implementeren in azure

<a name="twiliomodule"></a>

## <a name="configure-the-twilio-module"></a>De Twilio-module configureren
We gaan nu een eenvoudige node.js toepassing schrijven die gebruikmaakt van de Twilio-API. Voordat we beginnen, moeten we de referenties voor het Twilio-account configureren.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Twilio-referenties configureren in systeem omgevingsvariabelen
Voor het maken van geverifieerde aanvragen voor de back-end van de Twilio hebben we het account-SID en verificatie token nodig, dat als de gebruikers naam en het wacht woord is ingesteld voor het Twilio-account. De veiligste manier om deze te configureren voor gebruik met de knooppunt module in Azure is via systeem omgevingsvariabelen, die u rechtstreeks kunt instellen in de Azure-beheer console.

Selecteer uw node.js website en klik op de koppeling configureren.  Als u een bit omlaag schuift, ziet u een gebied waar u de configuratie-eigenschappen voor uw toepassing kunt instellen.  Voer uw referenties voor het Twilio-account ([gevonden in uw Twilio-console][twilio_console]) in zoals weer gegeven: Zorg ervoor dat u deze naam `TWILIO_ACCOUNT_SID` en `TWILIO_AUTH_TOKEN` respectievelijk

![Azure-beheer console][azure-admin-console]

Nadat u deze variabelen hebt geconfigureerd, start u de toepassing opnieuw op in de Azure-console.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declareer de Twilio-module in package.jsop
We moeten nu een package.jsmaken voor het beheren van de afhankelijkheden van de node module via [NPM]. Op hetzelfde niveau als het `server.js` bestand dat u hebt gemaakt in de zelf studie over *Azure/node.js* maakt u een bestand met de naam `package.json` .  In dit bestand plaatst u het volgende:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Hiermee declareert u de twilio-module als afhankelijkheid, evenals het populaire [Express-webframework][express] en de EJS-sjabloon engine.  Nu gaan we de code schrijven...

<a name="makecall"></a>

## <a name="make-an-outbound-call"></a>Een uitgaande oproep doen
We gaan een eenvoudig formulier maken dat een aanroep naar een nummer dat wij kiezen. Open up `server.js` en voer de volgende code in. Houd er rekening mee dat de naam van de Azure-website in CHANGE_ME wordt vermeld:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Maak vervolgens een map `views` met de naam-in deze map en maak een bestand `index.ejs` met de naam met de volgende inhoud:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Implementeer uw website nu naar Azure en open uw start. U moet uw telefoon nummer in het tekst veld kunnen invoeren en een gesprek ontvangen van uw Twilio-nummer.

<a name="sendmessage"></a>

## <a name="send-an-sms-message"></a>Een SMS-bericht verzenden
Nu gaan we een gebruikers interface en formulier verwerkings logica instellen om een SMS-bericht te verzenden. Open up `server.js` en voeg de volgende code toe na de laatste aanroep naar `app.post` :

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

Voeg in een `views/index.ejs` ander formulier in de eerste vorm toe om een nummer en een tekst bericht in te dienen:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Implementeer uw toepassing opnieuw naar Azure en u kunt dit formulier nu verzenden en uzelf (of een van de dichtstbijzijnde vrienden) een SMS-bericht sturen.

<a name="nextsteps"></a>

## <a name="next-steps"></a>Volgende stappen
U hebt nu de basis beginselen geleerd van het gebruik van node.js en Twilio om apps te bouwen die met elkaar communiceren. Maar deze voor beelden krassen het Opper vlak van wat mogelijk is met Twilio en node.js. Raadpleeg de volgende bronnen voor meer informatie over het gebruik van Twilio met node.js:

* [Officiële module docs][docs]
* [Zelf studie over VoIP met node.js-toepassingen][voipnode]
* [Votr: een real-time SMS-stem toepassing met node.js en CouchDB (drie delen)][votr]
* [Gekoppelde Program ma's in de browser met node.js][pair]

We hopen dat u node.js en Twilio op Azure hebt gekraakt.

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/quickstart-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
