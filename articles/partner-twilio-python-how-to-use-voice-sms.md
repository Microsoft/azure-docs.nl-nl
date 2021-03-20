---
title: Twilio gebruiken voor spraak en SMS (python) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Code voorbeelden geschreven in python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: b4b9cd0db2a3a99aca80f42b6d69485a542bbadb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580945"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Twilio gebruiken voor spraak-en SMS-mogelijkheden in python
In deze hand leiding wordt gedemonstreerd hoe u veelvoorkomende programmeer taken uitvoert met de Twilio API-service in Azure. De besproken scenario's zijn onder andere het maken van een telefoon oproep en het verzenden van een SMS-bericht (Short Message Service). Zie de sectie [volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en SMS in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is de toekomst van bedrijfs communicatie ingeschakeld, waardoor ontwikkel aars spraak, VoIP en berichten in toepassingen kunnen insluiten. Ze virtualiseren alle infra structuur die nodig is in een in de cloud gebaseerde wereld wijde omgeving, waardoor deze toegankelijk is via het Twilio Communications API-platform. Toepassingen zijn eenvoudig te bouwen en te schaalbaar. Profiteer van flexibiliteit met prijzen voor betalen per gebruik en profiteer van de betrouw baarheid van de Cloud.

Met **Twilio Voice** kunnen uw toepassingen telefoon gesprekken voeren en ontvangen.
Met **TWILIO SMS** kan uw toepassing tekst berichten verzenden en ontvangen.
Met **Twilio-client** kunt u VoIP-aanroepen van elke telefoon, tablet of browser maken en WebRTC ondersteunen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prijzen voor Twilio en speciale aanbiedingen
Azure-klanten ontvangen een [speciale aanbieding][special_offer] van $10 Twilio-tegoed wanneer u uw Twilio-account bijwerkt. Dit Twilio-tegoed kan worden toegepast op elk Twilio-gebruik ($10-credit equivalent om zoveel 1.000 SMS-berichten te verzenden of Maxi maal 1000 binnenkomende spraak minuten te ontvangen), afhankelijk van de locatie van uw telefoon nummer en bericht-of oproep doel. Inwisselen van dit [Twilio tegoed][special_offer] en aan de slag.

Twilio is een service voor betalen naar gebruik. Er zijn geen instel kosten en u kunt uw account op elk gewenst moment sluiten. U kunt meer informatie vinden op [Twilio prijzen][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio-API is een resterende API die spraak-en SMS-functionaliteit biedt voor toepassingen. Client bibliotheken zijn beschikbaar in meerdere talen. Zie [TWILIO API libraries][twilio_libraries]voor een lijst.

De belangrijkste aspecten van de Twilio-API zijn Twilio-werk woorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-werk woorden
De API maakt gebruik van Twilio-werk woorden. de term **&lt; zeg &gt;** geeft bijvoorbeeld Twilio aan audibly een bericht te verzenden bij een aanroep.

Hier volgt een lijst met Twilio-werk woorden. Meer informatie over de andere bewerkingen en mogelijkheden via [documentatie voor Twilio Markup Language][twiml].

* **&lt; Dial &gt;**: verbindt de beller met een andere telefoon.
* **&lt; Verzamelen &gt;**: Hiermee worden numerieke cijfers op het telefoon blok verzameld.
* **&lt; Ophangen &gt;**: beëindigt een aanroep.
* **&lt; Pause &gt;**: wacht een opgegeven aantal seconden op de achtergrond.
* **&lt; Afspelen &gt;**: Hiermee wordt een audio bestand afgespeeld.
* **&lt; Wachtrij &gt;**: Voeg het toe aan een wachtrij met bellers.
* **&lt; Record &gt;**: registreert de stem van de aanroeper en retourneert een URL van een bestand dat de opname bevat.
* **&lt; Omleiden &gt;**: overdracht van de besturing van een aanroep of SMS naar de TWIML op een andere URL.
* **&lt; Afwijzen &gt;**: Hiermee wordt een inkomende oproep naar uw Twilio-nummer afgewezen zonder dat u wordt gefactureerd.
* **&lt; Zeg &gt;**: Hiermee wordt tekst geconverteerd naar spraak die tijdens een aanroep wordt gemaakt.
* **&lt; SMS &gt;**: er wordt een SMS-bericht verzonden.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een reeks op XML gebaseerde instructies op basis van de Twilio-werk woorden die Twilio over het verwerken van een aanroep of SMS.

Als voor beeld wordt met de volgende TwiML de tekst **Hallo wereld** naar spraak geconverteerd.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

Wanneer uw toepassing de Twilio API aanroept, is een van de API-para meters de URL die het TwiML-antwoord retourneert. Voor ontwikkelings doeleinden kunt u Twilio-Url's gebruiken om de TwiML-reacties te geven die worden gebruikt door uw toepassingen. U kunt ook uw eigen Url's hosten om de TwiML-reacties te maken en een andere optie is het object te gebruiken `TwiMLResponse` .

Zie [TwiML][twiml]voor meer informatie over Twilio-werk woorden, hun kenmerken en TwiML. Zie [TWILIO API][twilio_api](Engelstalig) voor meer informatie over de TWILIO-API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Wanneer u klaar bent om een Twilio-account op te halen, meldt u zich aan bij [try Twilio][try_twilio]. U kunt beginnen met een gratis account en later uw account bijwerken.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-SID en een verificatie token. Beide zijn nodig voor het maken van Twilio-API-aanroepen. Zorg ervoor dat uw verificatie token is beveiligd om onbevoegde toegang tot uw account te voor komen. Uw account-SID en verificatie token zijn zichtbaar in de [Twilio-console][twilio_console], in de velden met de naam account- **sid** en **verificatie token**.

## <a name="create-a-python-application"></a><a id="create_app"></a>Een python-toepassing maken
Een python-toepassing die gebruikmaakt van de Twilio-service en wordt uitgevoerd in azure, is niet anders dan andere python-toepassingen die gebruikmaken van de Twilio-service. Hoewel Twilio Services op verschillende manieren kunnen worden aangeroepen vanuit Python, wordt in dit artikel de nadruk gelegd op het gebruik van Twilio-Services met [Twilio-bibliotheek voor python van github][twilio_python]. Zie voor meer informatie over het gebruik van de Twilio-bibliotheek voor python [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] .

Eerst [Stel een nieuwe Azure Linux-VM] [azure_vm_setup] in als host voor uw nieuwe python-webtoepassing. Wanneer de virtuele machine wordt uitgevoerd, moet u uw toepassing zichtbaar maken op een open bare poort, zoals hieronder wordt beschreven.

### <a name="add-an-incoming-rule"></a>Een binnenkomende regel toevoegen
  1. Ga naar de pagina [netwerk beveiligings groep] [azure_nsg].
  2. Selecteer de netwerk beveiligings groep die overeenkomt met uw virtuele machine.
  3. Voeg een **regel voor uitgaande** verbindingen toe voor **poort 80**. Zorg ervoor dat u inkomende berichten vanaf elk adres toelaat.

### <a name="set-the-dns-name-label"></a>Het DNS-naam label instellen
  1. Ga naar de pagina [Openbare IP-adressen] [azure_ips].
  2. Selecteer het open bare IP-adres dat overeenkomt met uw virtuele machine.
  3. Stel het **DNS-naam label** in het gedeelte **configuratie** in. In het geval van dit voor beeld ziet het er ongeveer als volgt uit: *uw domein label*. centralus.cloudapp.Azure.com

Zodra u via SSH verbinding kunt maken met de virtuele machine, kunt u het webframework van uw keuze installeren (de twee meest bekende in python die de [kolf](http://flask.pocoo.org/) en [Django](https://www.djangoproject.com)zijn). U kunt een van beide installeren door de opdracht uit te voeren `pip install` .

Houd er wel voor dat we de virtuele machine zo hebben geconfigureerd dat alleen verkeer wordt toegestaan op poort 80. Zorg er dus voor dat u de toepassing configureert voor het gebruik van deze poort.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren voor het gebruik van Twilio-bibliotheken
U kunt uw toepassing configureren voor het gebruik van de Twilio-bibliotheek voor python op twee manieren:

* Installeer de Twilio-bibliotheek voor python als PIP-pakket. Deze kan worden geïnstalleerd met de volgende opdrachten:
   
  `$ pip install twilio`

    OF

* Down load de Twilio-bibliotheek voor python van GitHub ( [https://github.com/twilio/twilio-python][twilio_python] ) en installeer deze als volgt:

  `$ python setup.py install`

Nadat u de Twilio-bibliotheek voor python hebt geïnstalleerd, kunt u `import` deze in uw python-bestanden weer:

  `import twilio`

Zie [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md)voor meer informatie.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedure: een uitgaande oproep doen
Hieronder ziet u hoe u een uitgaande oproep kunt doen. Deze code maakt ook gebruik van een Twilio-site om het TwiML-antwoord (Twilio Markup Language) te retour neren. Vervang uw waarden door de **from_number** en **to_number** telefoon nummers en zorg ervoor dat u het **From_number** telefoon nummer voor uw Twilio-account hebt gecontroleerd voordat u de code uitvoert.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> Telefoon nummers moeten worden opgemaakt met een ' + ' en een land nummer. Bijvoorbeeld: + 16175551212 (E. 164-indeling). Twilio accepteert ook niet-opgemaakte Amerikaanse getallen. Bijvoorbeeld (415) 555-1212 of 415-555-1212.

Zoals vermeld, gebruikt deze code een Twilio-site voor het retour neren van het TwiML-antwoord. U kunt in plaats daarvan uw eigen site gebruiken om het TwiML-antwoord op te geven. Zie [TwiML-reacties op uw eigen website bieden](#howto_provide_twiml_responses)voor meer informatie.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Procedure: een SMS-bericht verzenden
Hieronder ziet u hoe u een SMS-bericht verzendt met behulp van de- `TwilioRestClient` klasse. Het **from_number** nummer wordt verzorgd door Twilio voor proef accounts om SMS-berichten te verzenden. Het **to_number** nummer moet worden geverifieerd voor uw Twilio-account voordat u de code uitvoert.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Procedure: TwiML-reacties van uw eigen website opgeven
Wanneer uw toepassing een aanroep naar de Twilio-API initieert, stuurt Twilio uw aanvraag naar een URL die naar verwachting een TwiML-antwoord retourneert. In het bovenstaande voor beeld wordt gebruikgemaakt van de Twilio-URL [https://twimlets.com/message][twimlet_message_url] . (Hoewel TwiML is ontworpen voor gebruik door Twilio, kunt u het weer geven in uw browser. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een leeg element weer te geven. `<Response>` als een ander voor beeld klikt [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] u op om een `<Response>` element met een-element weer te geven `<Say>` .)

In plaats van te vertrouwen op de Twilio-URL, kunt u uw eigen site maken die HTTP-antwoorden retourneert. U kunt de site maken in elke taal die XML-antwoorden retourneert; in dit onderwerp wordt ervan uitgegaan dat u python gebruikt om de TwiML te maken.

In de volgende voor beelden wordt een TwiML-antwoord uitgevoerd met de tekst **Hallo wereld** bij de aanroep.

Met kolf:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Met Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Zoals u in het bovenstaande voor beeld kunt zien, is het TwiML-antwoord slechts een XML-document. De Twilio-bibliotheek voor python bevat klassen waarmee TwiML voor u wordt gegenereerd. In het volgende voor beeld wordt het equivalente antwoord gegenereerd, zoals hierboven wordt weer gegeven, maar wordt de `twiml` module gebruikt in de Twilio-bibliotheek voor python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Zie voor meer informatie over TwiML [https://www.twilio.com/docs/api/twiml][twiml_reference] .

Zodra u uw python-toepassing hebt ingesteld om TwiML-antwoorden te geven, gebruikt u de URL van de toepassing als de URL die wordt door gegeven aan de- `client.calls.create`  methode. Als u bijvoorbeeld een webtoepassing met de naam **MyTwiML** hebt geïmplementeerd in een door Azure gehoste service, kunt u de URL als webhook gebruiken, zoals wordt weer gegeven in het volgende voor beeld:

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Procedure: extra Twilio Services gebruiken
Naast de voor beelden die hier worden weer gegeven, biedt Twilio-Api's die u kunt gebruiken om gebruik te maken van extra Twilio-functionaliteit van uw Azure-toepassing. Zie de [TWILIO API-documentatie][twilio_api]voor meer informatie.

## <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu u de basis principes van de Twilio-service hebt geleerd, volgt u deze koppelingen voor meer informatie:

* [Twilio-beveiligings richtlijnen][twilio_security_guidelines]
* [Twilio-hand leidingen en voorbeeld code][twilio_howtos]
* [Zelf studies voor Twilio Quick Start][twilio_quickstarts]
* [Twilio op GitHub][twilio_on_github]
* [Neem contact op met ondersteuning voor Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
