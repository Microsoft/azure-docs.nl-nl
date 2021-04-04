---
title: De SendGrid-e-mail service (PHP) gebruiken | Microsoft Docs
description: Meer informatie over het verzenden van e-mail met de SendGrid-e-mail service op Azure. Code voorbeelden geschreven in PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023785"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>De SendGrid-e-mail service van PHP gebruiken

In deze hand leiding wordt gedemonstreerd hoe u algemene programmeer taken uitvoert met de SendGrid-e-mail service op Azure. De voor beelden zijn geschreven in PHP.
De besproken scenario's zijn onder andere het **samen stellen van e-mail**, het **verzenden van e-mail** en het **toevoegen van bijlagen**. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over het SendGrid en verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid-e-mail service?
SendGrid is een [cloud-gebaseerde e-mail service] die betrouw bare [transactionele e-mail levering], schaal baarheid en real-time analyse biedt, samen met flexibele api's die eenvoudig aangepaste integratie maken. Veelvoorkomende scenario's voor SendGrid-gebruik zijn:

* Automatisch ontvangst bevestigingen verzenden naar klanten
* Distributie lijsten beheren voor het maandelijks verzenden van klanten per maand e-flyers en speciale aanbiedingen
* Realtime metrische gegevens verzamelen voor zaken als geblokkeerde e-mail en reactie tijd van klant
* Rapporten genereren om trends te identificeren
* Vragen van klanten door sturen
* E-mail meldingen van uw toepassing

Zie [https://sendgrid.com][https://sendgrid.com] voor meer informatie.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>SendGrid gebruiken in uw PHP-toepassing

Voor het gebruik van SendGrid in een Azure PHP-toepassing is geen speciale configuratie of code ring vereist. Omdat SendGrid een service is, kan deze op exact dezelfde manier worden geopend vanuit een Cloud toepassing als vanuit een on-premises toepassing.

## <a name="how-to-send-an-email"></a>Procedure: een E-mail verzenden

U kunt e-mail verzenden via SMTP of met de Web-API van SendGrid.

### <a name="smtp-api"></a>SMTP-API

Als u e-mail berichten wilt verzenden met de SendGrid SMTP API, gebruikt u *Swift Mailer*, een op onderdelen gebaseerde bibliotheek voor het verzenden van e-mail berichten van PHP-toepassingen. U kunt de [Swift Mailer-bibliotheek](https://swiftmailer.symfony.com/) v 5.3.0 downloaden (gebruik [Composer] om de SWIFT-mailer te installeren). Het verzenden van een e-mail met de bibliotheek omvat het maken van instanties van de `Swift\_SmtpTransport` `Swift\_Mailer` klassen,,, en het `Swift\_Message` instellen van de juiste eigenschappen, en het aanroepen van de- `Swift\_Mailer::send` methode.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Web-API
Gebruik de [krul functie][curl function] van PHP om e-mail te verzenden met behulp van de SendGrid-Web-API.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

De Web-API van SendGrid is vergelijkbaar met een REST API, hoewel het niet echt een resterende API is, omdat in de meeste gesp rekken zowel GET als POST-woorden door elkaar kunnen worden gebruikt.

## <a name="how-to-add-an-attachment"></a>Procedure: een bijlage toevoegen

### <a name="smtp-api"></a>SMTP-API

Het verzenden van een bijlage met de SMTP-API omvat een extra regel code in het voorbeeld script voor het verzenden van een e-mail met een Swift-Mailer.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

De aanvullende regel code is als volgt:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Deze regel code roept de methode attach aan voor het `Swift\_Message` object en gebruikt statische methode `fromPath` voor de `Swift\_Attachment` klasse om een bestand op te halen en aan een bericht toe te voegen.

### <a name="web-api"></a>Web-API

Het verzenden van een bijlage met de Web-API lijkt veel op het verzenden van een e-mail met de Web-API. Houd er echter rekening mee dat in het volgende voor beeld de parameter matrix dit element moet bevatten:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Voorbeeld

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Procedure: filters gebruiken om voet teksten, tracering en analyses in te scha kelen

SendGrid biedt extra e-mail functionaliteit dankzij het gebruik van *filters*. Dit zijn instellingen die kunnen worden toegevoegd aan een e-mail bericht om specifieke functionaliteit in te scha kelen, zoals het inschakelen van klikken op bijhouden, Google Analytics, bijhouden van abonnementen, enzovoort.

Filters kunnen worden toegepast op een bericht met behulp van de eigenschap filters. Elk filter wordt opgegeven met een hash die filter-specifieke instellingen bevat. In het volgende voor beeld wordt het filter voet tekst ingeschakeld en wordt een tekst bericht opgegeven dat aan de onderkant van het e-mail bericht wordt toegevoegd. Voor dit voor beeld gebruiken we [sendgrid-PHP-bibliotheek].

[Componist] gebruiken voor het installeren van de bibliotheek:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Voorbeeld  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van de SendGrid-e-mail service hebt geleerd, volgt u deze koppelingen voor meer informatie.

* SendGrid-documentatie: <https://sendgrid.com/docs>
* SendGrid PHP-bibliotheek: <https://github.com/sendgrid/sendgrid-php>
* SendGrid speciale aanbieding voor Azure-klanten: <https://sendgrid.com/windowsazure.html>

Zie ook het [PHP-ontwikkelaars centrum](https://azure.microsoft.com/develop/php/)voor meer informatie.

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[e-mail service op basis van de Cloud]: https://sendgrid.com/email-solutions
[e-mail levering via een transactie]: https://sendgrid.com/transactional-email
[sendgrid-PHP-bibliotheek]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
