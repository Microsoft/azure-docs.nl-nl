---
title: Een telefoon gesprek doen vanuit Twilio (PHP) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Voor beelden zijn voor PHP-toepassing.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: ef0642b26898249d78f5103e8a8f80198887bed9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95542521"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Een telefoon gesprek doen met behulp van Twilio in een PHP-toepassing in azure
In het volgende voor beeld ziet u hoe u Twilio kunt gebruiken om een oproep te doen van een PHP-webpagina die wordt gehost in Azure. De resulterende toepassing vraagt de gebruiker om telefoon gesprek waarden, zoals wordt weer gegeven in de volgende scherm afbeelding.

![Azure-oproep formulier met Twilio en PHP][twilio_php]

U moet de volgende stappen uitvoeren om de code in dit onderwerp te gebruiken:

1. Haal een Twilio-account en verificatie token op uit uw [Twilio-console][twilio_console]. Als u aan de slag wilt gaan met Twilio, evalueert u de prijzen op [https://www.twilio.com/pricing][twilio_pricing] . U kunt zich aanmelden voor een proef account op [https://www.twilio.com/try-twilio][try_twilio] .
2. Verkrijg de [Twilio-bibliotheek voor php](https://github.com/twilio/twilio-php) of installeer deze als een peer pakket. Raadpleeg het [Leesmij-bestand](https://github.com/twilio/twilio-php/blob/master/README.md)voor meer informatie.
3. Installeer de Azure SDK voor PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](./app-service/quickstart-php.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Een webformulier maken voor het aanroepen van een gesprek
De volgende HTML-code laat zien hoe u een webpagina (**callform.html**) bouwt waarmee gebruikers gegevens worden opgehaald voor het maken van een gesprek:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>De code maken om de aanroep uit te voeren
De volgende code laat zien hoe u **makecall. php** bouwt, die wordt aangeroepen wanneer de gebruiker het formulier dat wordt weer gegeven door **callform.html** verzendt. Met de onderstaande code wordt het aanroep bericht gemaakt en wordt de aanroep gegenereerd. Zorg er ook voor dat u uw Twilio-account en verificatie token gebruikt in de [Twilio-console][twilio_console] in plaats van de waarden voor tijdelijke aanduidingen die zijn toegewezen aan **$sid** en **$token** in de onderstaande code.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Naast het aanroepen van de aanroep, wordt in **makecall. php** enkele meta gegevens van het gesprek weer gegeven, zoals wordt weer gegeven in de onderstaande afbeelding. Zie voor meer informatie over het aanroepen van meta gegevens [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] .

![Azure-aanroep antwoord met Twilio en PHP][twilio_php_response]

## <a name="run-the-application"></a>De toepassing uitvoeren
De volgende stap is het [implementeren van uw toepassing naar Azure web apps met git](app-service/quickstart-php.md) (maar niet alle informatie die er relevant is). 

## <a name="next-steps"></a>Volgende stappen
Deze code werd verschaft om u de basis functionaliteit te laten zien met behulp van Twilio in PHP op Azure. Voordat u naar Azure implementeert in productie, wilt u mogelijk meer fout afhandeling of andere functies toevoegen. Bijvoorbeeld:

* In plaats van een webformulier te gebruiken, kunt u Azure Storage-blobs of SQL Database gebruiken om telefoon nummers op te slaan en tekst aan te roepen. Zie [Azure Storage gebruiken met PHP-toepassingen][howto_blob_storage_php]voor meer informatie over het gebruik van Azure Storage-blobs in PHP. Zie [using SQL database with PHP Applications][howto_sql_azure_php](Engelstalig) voor meer informatie over het gebruik van SQL database in PHP.
* De **makecall. php** -code maakt gebruik van de door de Twilio geleverde URL ( [https://twimlets.com/message][twimlet_message_url] ) om een TwiML-antwoord (Twilio Markup Language) op te geven waarmee Twilio wordt geïnformeerd over hoe u kunt door gaan met de aanroep. De TwiML die wordt geretourneerd, kan bijvoorbeeld een werk `<Say>` woord bevatten dat resulteert in de tekst die wordt gesp roken naar de ontvanger van het gesprek. In plaats van de Twilio-URL te gebruiken, kunt u uw eigen service bouwen om te reageren op de aanvraag van Twilio. Zie [Twilio gebruiken voor spraak-en SMS-mogelijkheden in PHP][howto_twilio_voice_sms_php]voor meer informatie. Meer informatie over TwiML vindt u op [https://www.twilio.com/docs/api/twiml][twiml] , en meer informatie over `<Say>` en andere Twilio-werk woorden vindt u op [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Lees de Twilio Security-richt lijnen op [https://www.twilio.com/docs/security][twilio_docs_security] .

Zie voor meer informatie over Twilio [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Zie ook
* [Twilio gebruiken voor spraak-en SMS-mogelijkheden in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: ./storage/blobs/storage-quickstart-blobs-php.md
[howto_sql_azure_php]: ./azure-sql/database/connect-query-content-reference-guide.md
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php