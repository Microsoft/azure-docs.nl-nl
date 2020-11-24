---
title: Twilio gebruiken voor spraak en SMS (Ruby) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Code voorbeelden geschreven in Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 49203195bf7746d0bff1b9543d1641f69ab23359
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542674"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Twilio gebruiken voor spraak-en SMS-mogelijkheden in Ruby
In deze hand leiding wordt gedemonstreerd hoe u veelvoorkomende programmeer taken uitvoert met de Twilio API-service in Azure. De besproken scenario's zijn onder andere het maken van een telefoon oproep en het verzenden van een SMS-bericht (Short Message Service). Zie de sectie [volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en SMS in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is een Telephony Web-Service-API waarmee u uw bestaande webtalen en-vaardig heden kunt gebruiken om spraak-en SMS-toepassingen te bouwen. Twilio is een service van derden (geen Azure-functie en geen micro soft-product).

Met **Twilio Voice** kunnen uw toepassingen telefoon gesprekken voeren en ontvangen. Met **TWILIO SMS** kunnen uw toepassingen SMS-berichten maken en ontvangen. Met **Twilio-client** kunnen uw toepassingen spraak communicatie mogelijk maken via bestaande Internet verbindingen, waaronder mobiele verbindingen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prijzen voor Twilio en speciale aanbiedingen
Informatie over prijzen voor Twilio is beschikbaar op [Twilio-prijzen][twilio_pricing]. Azure-klanten ontvangen een [speciale aanbieding][special_offer]: een gratis tegoed van 1000 teksten of 1000 binnenkomende minuten. Als u zich voor deze aanbieding wilt aanmelden of meer informatie wilt krijgen, gaat u naar [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio-API is een resterende API die spraak-en SMS-functionaliteit biedt voor toepassingen. Client bibliotheken zijn beschikbaar in meerdere talen. Zie [TWILIO API libraries][twilio_libraries]voor een lijst.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een reeks XML-instructies waarmee u Twilio kunt informeren over het verwerken van een aanroep of SMS.

Als voor beeld wordt met de volgende TwiML de tekst **Hallo wereld** naar spraak geconverteerd.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Alle TwiML-documenten hebben `<Response>` als hoofd element. Daar kunt u Twilio-werk woorden gebruiken om het gedrag van uw toepassing te definiëren.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML-werk woorden
Twilio-werk woorden zijn XML-tags waarmee wordt aangegeven Twilio wat er moet **gebeuren**. De term **&lt; zeg &gt;** geeft bijvoorbeeld Twilio aan audibly een bericht te verzenden bij een aanroep. 

Hier volgt een lijst met Twilio-werk woorden.

* **&lt; Dial &gt;**: verbindt de beller met een andere telefoon.
* **&lt; Verzamelen &gt;**: Hiermee worden numerieke cijfers op het telefoon blok verzameld.
* **&lt; Ophangen &gt;**: beëindigt een aanroep.
* **&lt; Afspelen &gt;**: Hiermee wordt een audio bestand afgespeeld.
* **&lt; Pause &gt;**: wacht een opgegeven aantal seconden op de achtergrond.
* **&lt; Record &gt;**: registreert de stem van de beller en retourneert een URL van een bestand dat de opname bevat.
* **&lt; Omleiden &gt;**: overdracht van de besturing van een aanroep of SMS naar de TWIML op een andere URL.
* **&lt; Afwijzen &gt;**: Hiermee wordt een inkomende oproep naar uw Twilio-nummer afgewezen zonder dat u wordt gefactureerd
* **&lt; Zeg &gt;**: Hiermee wordt tekst geconverteerd naar spraak die tijdens een aanroep wordt gemaakt.
* **&lt; SMS &gt;**: er wordt een SMS-bericht verzonden.

Zie [TwiML][twiml]voor meer informatie over Twilio-werk woorden, hun kenmerken en TwiML. Zie [TWILIO API][twilio_api](Engelstalig) voor meer informatie over de TWILIO-API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Wanneer u klaar bent om een Twilio-account op te halen, meldt u zich aan bij [try Twilio][try_twilio]. U kunt beginnen met een gratis account en later uw account bijwerken.

Wanneer u zich aanmeldt voor een Twilio-account, krijgt u een gratis telefoon nummer voor uw toepassing. U ontvangt ook een account-SID en een verificatie token. Beide zijn nodig voor het maken van Twilio-API-aanroepen. Zorg ervoor dat uw verificatie token is beveiligd om onbevoegde toegang tot uw account te voor komen. Uw account-SID en verificatie token zijn te zien op de [pagina Twilio-account][twilio_account]in de velden met de naam **account sid** en **verificatie token**.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Telefoon nummers controleren
Naast het aantal dat u krijgt door Twilio, kunt u ook de nummers controleren die u beheert (dat wil zeggen uw mobiele telefoon of telefoon nummer thuis) voor gebruik in uw toepassingen. 

Zie [nummers beheren][verify_phone]voor meer informatie over het controleren van een telefoon nummer.

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Een ruby-toepassing maken
Een ruby-toepassing die gebruikmaakt van de Twilio-service en wordt uitgevoerd in azure, is geen andere ruby-toepassing die gebruikmaakt van de Twilio-service. Hoewel Twilio-Services actief zijn en op verschillende manieren kunnen worden aangeroepen vanuit Ruby, wordt in dit artikel de nadruk gelegd op het gebruik van Twilio-Services met de [Twilio helper-bibliotheek voor ruby][twilio_ruby].

Stel eerst [een nieuwe Azure Linux-VM][azure_vm_setup] in die als host fungeert voor uw nieuwe ruby-webtoepassing. U kunt de stappen voor het maken van een Rails-app negeren, maar u hoeft alleen maar de virtuele machine in te stellen. Zorg ervoor dat u een eind punt maakt met een externe poort van 80 en een interne poort van 5000.

In de onderstaande voor beelden wordt [Sinatra][sinatra], een zeer eenvoudig webframework voor Ruby, gebruikt. Maar u kunt de Twilio helper-bibliotheek voor ruby gebruiken met elk ander webframework, met inbegrip van Ruby on Rails.

SSH in uw nieuwe virtuele machine en maak een map voor uw nieuwe app. Maak in de map een bestand met de naam Gemfile en kopieer de volgende code naar deze locatie:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

Op de opdracht regel uitvoeren `bundle install` . Hiermee worden de bovenstaande afhankelijkheden geïnstalleerd. Maak vervolgens een bestand met de naam `web.rb` . Dit is de plaats waar de code voor de web-app woont. Plak er de volgende code in:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

Op dit moment moet u de opdracht uitvoeren `ruby web.rb -p 5000` . Hiermee wordt een kleine webserver op poort 5000 gedraaid. U moet in uw browser naar deze app kunnen bladeren door de URL te bezoeken die u hebt ingesteld voor uw virtuele Azure-machine. Zodra u uw web-app in de browser kunt bereiken, bent u klaar om te beginnen met het bouwen van een Twilio-app.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Uw toepassing configureren voor het gebruik van Twilio
U kunt uw web-app configureren voor het gebruik van de Twilio-bibliotheek door de `Gemfile` volgende regel toe te voegen:

```bash
gem 'twilio-ruby'
```

Voer op de opdracht regel uit `bundle install` . Nu geopend `web.rb` en met deze regel aan de bovenkant:

```ruby
require 'twilio-ruby'
```

U bent nu klaar om de Twilio helper-bibliotheek voor ruby in uw web-app te gebruiken.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedure: een uitgaande oproep doen
Hieronder ziet u hoe u een uitgaande oproep kunt doen. De belangrijkste concepten zijn het gebruik van de Twilio helper-bibliotheek voor ruby om REST API aanroepen en rendering van TwiML te maken. Vervang uw waarden door **van** en **naar** telefoon nummers en zorg ervoor dat u het telefoon nummer **van** uw Twilio-account controleert voordat u de code uitvoert.

Voeg deze functie toe aan `web.md` :

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

Als u opent `http://yourdomain.cloudapp.net/make_call` in een browser, wordt de aanroep naar de Twilio-API geactiveerd om de telefoon oproep te voeren. De eerste twee para meters in `client.account.calls.create` zijn tamelijk zelf uitleg: het nummer van de aanroep `from` en het nummer van de aanroep `to` . 

De derde para meter ( `url` ) is de URL die Twilio vraagt om instructies te krijgen over wat u moet doen zodra de verbinding is verbonden. In dit geval stellen we een URL in ( `http://yourdomain.cloudapp.net` ) die een eenvoudig TwiML-document retourneert en de `<Say>` term gebruikt om tekst naar spraak te sturen en ' Hello-aap ' te zeggen aan de persoon die de oproep ontvangt.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Procedure: een SMS-bericht ontvangen
In het vorige voor beeld is een **uitgaande** telefoon oproep gestart. Laten we nu het telefoon nummer gebruiken dat Twilio tijdens het aanmelden heeft gekregen om een **binnenkomend** SMS-bericht te verwerken.

Meld u eerst aan bij uw [Twilio-dash board][twilio_account]. Klik op ' cijfers ' in de bovenste navigatie en klik vervolgens op het Twilio nummer dat u hebt ontvangen. U ziet twee Url's die u kunt configureren. Een spraak aanvraag-URL en een SMS-aanvraag-URL. Dit zijn de Url's die Twilio aanroept wanneer een telefoon oproep wordt gedaan of een SMS wordt verzonden naar uw nummer. De Url's worden ook wel ' webhooks ' genoemd.

We willen inkomende SMS-berichten verwerken, dus laten we de URL bijwerken naar `http://yourdomain.cloudapp.net/sms_url` . Klik op wijzigingen opslaan onder aan de pagina. Nu gaat u terug `web.rb` naar het programma om de toepassing te laten werken:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

Nadat u de wijziging hebt aangebracht, moet u de web-app opnieuw starten. Haal nu uw telefoon op en stuur een SMS-bericht naar uw Twilio-nummer. U wordt gevraagd om een SMS-bericht te ontvangen met de tekst ' Hey, Bedankt voor de ping! Twilio en Azure Rock!.

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Procedure: extra Twilio Services gebruiken
Naast de voor beelden die hier worden weer gegeven, biedt Twilio-Api's die u kunt gebruiken om gebruik te maken van extra Twilio-functionaliteit van uw Azure-toepassing. Zie de [TWILIO API-documentatie][twilio_api_documentation]voor meer informatie.

### <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu u de basis principes van de Twilio-service hebt geleerd, volgt u deze koppelingen voor meer informatie:

* [Twilio-beveiligings richtlijnen][twilio_security_guidelines]
* [Twilio HowTos en voorbeeld code][twilio_howtos]
* [Zelf studies voor Twilio Quick Start][twilio_quickstarts] 
* [Twilio op GitHub][twilio_on_github]
* [Neem contact op met ondersteuning voor Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: /previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app