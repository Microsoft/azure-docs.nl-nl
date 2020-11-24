---
title: Twilio gebruiken voor spraak en SMS (PHP) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Code voorbeelden geschreven in PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521016"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Twilio gebruiken voor spraak-en SMS-mogelijkheden in PHP
In deze hand leiding wordt gedemonstreerd hoe u veelvoorkomende programmeer taken uitvoert met de Twilio API-service in Azure. De besproken scenario's zijn onder andere het maken van een telefoon oproep en het verzenden van een SMS-bericht (Short Message Service). Zie de sectie [volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en SMS in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is de toekomst van bedrijfs communicatie ingeschakeld, waardoor ontwikkel aars spraak, VoIP en berichten in toepassingen kunnen insluiten. Ze virtualiseren alle infra structuur die nodig is in een in de cloud gebaseerde wereld wijde omgeving, waardoor deze toegankelijk is via het Twilio Communications API-platform. Toepassingen zijn eenvoudig te bouwen en te schaalbaar. Profiteer van flexibiliteit met prijzen voor betalen per gebruik en profiteer van de betrouw baarheid van de Cloud.

Met **Twilio Voice** kunnen uw toepassingen telefoon gesprekken voeren en ontvangen. Met **TWILIO SMS** kan uw toepassing tekst berichten verzenden en ontvangen. Met **Twilio-client** kunt u VoIP-aanroepen van elke telefoon, tablet of browser maken en WebRTC ondersteunen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prijzen voor Twilio en speciale aanbiedingen
Azure-klanten ontvangen een [speciale aanbieding](https://www.twilio.com/azure): gratis $10 aan Twilio-tegoed bij het upgraden van uw Twilio-account. Dit Twilio-tegoed kan worden toegepast op elk Twilio-gebruik ($10-credit equivalent om zoveel 1.000 SMS-berichten te verzenden of Maxi maal 1000 binnenkomende spraak minuten te ontvangen), afhankelijk van de locatie van uw telefoon nummer en bericht-of oproep doel. Laat dit Twilio tegoed inwisselen en ga aan de slag op: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) .

Twilio is een service voor betalen naar gebruik. Er zijn geen instel kosten en u kunt uw account op elk gewenst moment sluiten. U kunt meer informatie vinden op [Twilio prijzen][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio-API is een resterende API die spraak-en SMS-functionaliteit biedt voor toepassingen. Client bibliotheken zijn beschikbaar in meerdere talen. Zie [TWILIO API libraries][twilio_libraries]voor een lijst.

De belangrijkste aspecten van de Twilio-API zijn Twilio-werk woorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-werk woorden
De API maakt gebruik van Twilio-werk woorden. de term **&lt; zeg &gt;** geeft bijvoorbeeld Twilio aan audibly een bericht te verzenden bij een aanroep.

Hier volgt een lijst met Twilio-werk woorden. Meer informatie over de andere bewerkingen en mogelijkheden via [documentatie voor Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

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

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een reeks op XML gebaseerde instructies op basis van de Twilio-werk woorden die Twilio over het verwerken van een aanroep of SMS.

Als voor beeld wordt met de volgende TwiML de tekst **Hallo wereld** naar spraak geconverteerd.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Wanneer uw toepassing de Twilio API aanroept, is een van de API-para meters de URL die het TwiML-antwoord retourneert. Voor ontwikkelings doeleinden kunt u Twilio-Url's gebruiken om de TwiML-reacties te geven die worden gebruikt door uw toepassingen. U kunt ook uw eigen Url's hosten om de TwiML-reacties te maken en een andere optie is het object **TwiMLResponse** te gebruiken.

Zie [TwiML][twiml]voor meer informatie over Twilio-werk woorden, hun kenmerken en TwiML. Zie [TWILIO API][twilio_api](Engelstalig) voor meer informatie over de TWILIO-API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Wanneer u klaar bent om een Twilio-account op te halen, meldt u zich aan bij [try Twilio][try_twilio]. U kunt beginnen met een gratis account en later uw account bijwerken.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-ID en een verificatie token. Beide zijn nodig voor het maken van Twilio-API-aanroepen. Zorg ervoor dat uw verificatie token is beveiligd om onbevoegde toegang tot uw account te voor komen. Uw account-ID en verificatie token zijn te zien op de [pagina Twilio-account][twilio_account]in de velden met de naam **account sid** en **verificatie token**.

## <a name="create-a-php-application"></a><a id="create_app"></a>Een PHP-toepassing maken
Een PHP-toepassing die gebruikmaakt van de Twilio-service en wordt uitgevoerd in azure, is niet anders dan andere PHP-toepassingen die gebruikmaken van de Twilio-service. Hoewel Twilio Services op verschillende manieren kunnen worden aangeroepen vanuit PHP, wordt in dit artikel aandacht besteed aan het gebruik van Twilio-Services met Twilio- [bibliotheek voor php van github][twilio_php]. Zie voor meer informatie over het gebruik van de Twilio-bibliotheek voor PHP [https://www.twilio.com/docs/libraries/php][twilio_lib_docs] .

Gedetailleerde instructies voor het bouwen en implementeren van een Twilio/PHP-toepassing in azure zijn beschikbaar op [het maken van een telefoon oproep met behulp van Twilio in een PHP-toepassing in azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren voor het gebruik van Twilio-bibliotheken
U kunt uw toepassing configureren voor het gebruik van de Twilio-bibliotheek voor PHP op twee manieren:

1. Down load de Twilio-bibliotheek voor PHP van GitHub ( [https://github.com/twilio/twilio-php][twilio_php] ) en voeg de Directory **Services** toe aan uw toepassing.
   
    OF
2. Installeer de Twilio-bibliotheek voor PHP als een peer-pakket. Deze kan worden geïnstalleerd met de volgende opdrachten:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

Zodra u de Twilio-bibliotheek voor PHP hebt geïnstalleerd, kunt u boven aan uw PHP-bestanden een **require_once** -instructie toevoegen om te verwijzen naar de bibliotheek:

```php
require_once 'Services/Twilio.php';
```

Zie [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme] voor meer informatie.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedure: een uitgaande oproep doen
Hieronder ziet u hoe u een uitgaande oproep maakt met behulp van de **Services_Twilio** -klasse. Deze code maakt ook gebruik van een Twilio-site om het TwiML-antwoord (Twilio Markup Language) te retour neren. Vervang uw waarden door **van** en **naar** telefoon nummers en zorg ervoor dat u het telefoon nummer **van** uw Twilio-account controleert voordat u de code uitvoert.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Zoals vermeld, gebruikt deze code een Twilio-site voor het retour neren van het TwiML-antwoord. U kunt in plaats daarvan uw eigen site gebruiken om het TwiML-antwoord op te geven. Zie [TwiML-reacties op uw eigen website bieden](#howto_provide_twiml_responses)voor meer informatie.

* **Opmerking**: Zie voor het oplossen van problemen met TLS/SSL-certificaat validatie [https://www.twilio.com/docs/api/errors][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Procedure: een SMS-bericht verzenden
Hieronder ziet u hoe u een SMS-bericht verzendt met behulp van de **Services_Twilio** -klasse. Het **van** -nummer wordt verzorgd door Twilio voor proef accounts om SMS-berichten te verzenden. Het **to** -nummer moet worden geverifieerd voor uw Twilio-account voordat u de code uitvoert.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Procedure: TwiML-reacties van uw eigen website opgeven
Wanneer uw toepassing een aanroep naar de Twilio-API initieert, stuurt Twilio uw aanvraag naar een URL die naar verwachting een TwiML-antwoord retourneert. In het bovenstaande voor beeld wordt gebruikgemaakt van de Twilio-URL [https://twimlets.com/message][twimlet_message_url] . (Hoewel TwiML is ontworpen voor gebruik door Twilio, kunt u het weer geven in uw browser. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een leeg element weer te geven. `<Response>` als een ander voor beeld klikt [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] u op om een `<Response>` element met een-element weer te geven `<Say>` .)

In plaats van te vertrouwen op de Twilio-URL, kunt u uw eigen site maken die HTTP-antwoorden retourneert. U kunt de site maken in elke taal die XML-antwoorden retourneert; in dit onderwerp wordt ervan uitgegaan dat u PHP gebruikt om de TwiML te maken.

De volgende PHP-pagina resulteert in een TwiML-antwoord met de tekst **Hallo wereld** bij de aanroep.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Zoals u in het bovenstaande voor beeld kunt zien, is het TwiML-antwoord slechts een XML-document. De Twilio-bibliotheek voor PHP bevat klassen waarmee TwiML voor u wordt gegenereerd. In het volgende voor beeld wordt het equivalente antwoord gegenereerd, zoals hierboven wordt weer gegeven, maar wordt de Twilio-klasse **Services \_ \_ Twiml** in de TWILIO-bibliotheek voor php gebruikt:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

Zie voor meer informatie over TwiML [https://www.twilio.com/docs/api/twiml][twiml_reference] . 

Zodra u uw PHP-pagina hebt ingesteld om TwiML-reacties te geven, gebruikt u de URL van de PHP-pagina als de URL die wordt door gegeven aan de-  `Services_Twilio->account->calls->create`  methode. Als u bijvoorbeeld een webtoepassing met de naam **MyTwiML** hebt geïmplementeerd in een door Azure gehoste service en de naam van de PHP-pagina **MyTwiML. php** is, kan de URL worden door gegeven aan  **Services_Twilio->account->-aanroepen->maken**  , zoals in het volgende voor beeld wordt weer gegeven:

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Voor meer informatie over het gebruik van Twilio in azure met PHP raadpleegt u [hoe u een telefoon oproep maakt met behulp van Twilio in een PHP-toepassing in azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Procedure: extra Twilio Services gebruiken
Naast de voor beelden die hier worden weer gegeven, biedt Twilio-Api's die u kunt gebruiken om gebruik te maken van extra Twilio-functionaliteit van uw Azure-toepassing. Zie de [TWILIO API-documentatie][twilio_api_documentation]voor meer informatie.

## <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu u de basis principes van de Twilio-service hebt geleerd, volgt u deze koppelingen voor meer informatie:

* [Twilio-beveiligings richtlijnen][twilio_security_guidelines]
* [Twilio procedure-en voorbeeld code][twilio_howtos]
* [Zelf studies voor Twilio Quick Start][twilio_quickstarts] 
* [Twilio op GitHub][twilio_on_github]
* [Neem contact op met ondersteuning voor Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
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