---
title: Azure Notification Hubs gebruiken met PHP
description: Meer informatie over het gebruik van Azure Notification Hubs van een PHP-back-end.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: daebf7c6a5fc9056e16b77a40ee9f90db598749f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87076628"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Notification Hubs van PHP gebruiken

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

U hebt toegang tot alle Notification Hubs functies van een Java/PHP/Ruby-back-end met behulp van de REST-interface van Notification hub, zoals beschreven in het MSDN-onderwerp [Notification hubs rest-api's](/previous-versions/azure/reference/dn223264(v=azure.100)).

In dit onderwerp laten we zien hoe u:

* Bouw een REST-client voor Notification Hubs functies in PHP;
* Volg de stappen voor het [verzenden van push meldingen naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md) voor uw mobiele platform naar keuze, waarbij u het back-end-gedeelte implementeert in PHP.

## <a name="client-interface"></a>Client interface

De hoofd client interface kan dezelfde methoden bieden die beschikbaar zijn in de [.net notification hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx), waarmee u alle zelf studies en voor beelden die momenteel op deze site beschikbaar zijn, rechtstreeks kunt vertalen en die door de community op internet zijn bijgedragen.

U kunt alle beschik bare code vinden in het voor [beeld van PHP rest-wrapper].

Als u bijvoorbeeld een client wilt maken:

```php
$hub = new NotificationHub("connection string", "hubname");
```

Een systeem eigen iOS-melding verzenden:

```php
$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
$hub->sendNotification($notification, null);
```

## <a name="implementation"></a>Implementatie

Als u dit nog niet hebt gedaan, volgt u de [zelf studie aan de slag] tot de laatste sectie waarin u de back-end moet implementeren.
Als u wilt, kunt u ook de code van het voor [beeld van PHP rest-wrapper] gebruiken en rechtstreeks naar de sectie [zelf studie](#complete-tutorial) gaan.

Alle Details voor het implementeren van een volledige REST wrapper vindt u op [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). In deze sectie beschrijven we de PHP-implementatie van de belangrijkste stappen die nodig zijn voor toegang tot Notification Hubs REST-eind punten:

1. De verbindingsreeks parseren
2. Het autorisatie token genereren
3. De HTTP-aanroep uitvoeren

### <a name="parse-the-connection-string"></a>De verbindingsreeks parseren

Hier is de hoofd klasse die de-client implementeert, waarvan de constructor de connection string parseert:

```php
class NotificationHub {
    const API_VERSION = "?api-version=2013-10";

    private $endpoint;
    private $hubPath;
    private $sasKeyName;
    private $sasKeyValue;

    function __construct($connectionString, $hubPath) {
        $this->hubPath = $hubPath;

        $this->parseConnectionString($connectionString);
    }

    private function parseConnectionString($connectionString) {
        $parts = explode(";", $connectionString);
        if (sizeof($parts) != 3) {
            throw new Exception("Error parsing connection string: " . $connectionString);
        }

        foreach ($parts as $part) {
            if (strpos($part, "Endpoint") === 0) {
                $this->endpoint = "https" . substr($part, 11);
            } else if (strpos($part, "SharedAccessKeyName") === 0) {
                $this->sasKeyName = substr($part, 20);
            } else if (strpos($part, "SharedAccessKey") === 0) {
                $this->sasKeyValue = substr($part, 16);
            }
        }
    }
}
```

### <a name="create-a-security-token"></a>Een beveiligings token maken

Raadpleeg de Azure-documentatie voor informatie over het [maken van een SAS-beveiligings token](/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Voeg de `generateSasToken` methode toe aan de `NotificationHub` klasse om het token te maken op basis van de URI van de huidige aanvraag en de referenties die zijn geëxtraheerd uit de Connection String.

```php
private function generateSasToken($uri) {
    $targetUri = strtolower(rawurlencode(strtolower($uri)));

    $expires = time();
    $expiresInMins = 60;
    $expires = $expires + $expiresInMins * 60;
    $toSign = $targetUri . "\n" . $expires;

    $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

    $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

    return $token;
}
```

### <a name="send-a-notification"></a>Een melding verzenden

Laten we eerst een klasse definiëren die een melding weergeeft.

```php
class Notification {
    public $format;
    public $payload;

    # array with keynames for headers
    # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
    # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
    public $headers;

    function __construct($format, $payload) {
        if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
            throw new Exception('Invalid format: ' . $format);
        }

        $this->format = $format;
        $this->payload = $payload;
    }
}
```

Deze klasse is een container voor een systeem eigen meldings hoofdtekst of een set eigenschappen voor het geval van een sjabloon melding en een set kopteksten, die een indeling (systeem eigen platform of sjabloon) en platformspecifieke eigenschappen (zoals Apple Expires-eigenschappen en WNS-headers) bevat.

Raadpleeg de [documentatie van Notification hubs rest-api's](/previous-versions/azure/reference/dn495827(v=azure.100)) en de specifieke indelingen voor het meldings platform voor alle beschik bare opties.

In strijd met deze klasse kunnen we nu de methoden voor het verzenden van meldingen binnen de `NotificationHub` klasse schrijven:

```php
public function sendNotification($notification, $tagsOrTagExpression="") {
    if (is_array($tagsOrTagExpression)) {
        $tagExpression = implode(" || ", $tagsOrTagExpression);
    } else {
        $tagExpression = $tagsOrTagExpression;
    }

    # build uri
    $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
    $ch = curl_init($uri);

    if (in_array($notification->format, ["template", "apple", "fcm"])) {
        $contentType = "application/json";
    } else {
        $contentType = "application/xml";
    }

    $token = $this->generateSasToken($uri);

    $headers = [
        'Authorization: '.$token,
        'Content-Type: '.$contentType,
        'ServiceBusNotification-Format: '.$notification->format
    ];

    if ("" !== $tagExpression) {
        $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
    }

    # add headers for other platforms
    if (is_array($notification->headers)) {
        $headers = array_merge($headers, $notification->headers);
    }

    curl_setopt_array($ch, array(
        CURLOPT_POST => TRUE,
        CURLOPT_RETURNTRANSFER => TRUE,
        CURLOPT_SSL_VERIFYPEER => FALSE,
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_POSTFIELDS => $notification->payload
    ));

    // Send the request
    $response = curl_exec($ch);

    // Check for errors
    if($response === FALSE){
        throw new Exception(curl_error($ch));
    }

    $info = curl_getinfo($ch);

    if ($info['http_code'] <> 201) {
        throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
    }
} 
```

Met de bovenstaande methoden wordt een HTTP POST-aanvraag verzonden naar het `/messages` eind punt van uw notification hub, met de juiste hoofd tekst en kopteksten voor het verzenden van de melding.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>De zelf studie volt ooien

U kunt nu de zelf studie aan de slag volt ooien door de melding van een PHP-back-end te verzenden.

Initialiseer uw Notification Hubs-client (Vervang de connection string en de naam van de hub volgens de instructies in de [zelf studie aan de slag]):

```php
$hub = new NotificationHub("connection string", "hubname");
```

Voeg vervolgens de verzend code toe, afhankelijk van uw mobiele doel platform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store en Windows Phone 8,1 (niet-Silverlight)

```php
$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
$notification = new Notification("windows", $toast);
$notification->headers[] = 'X-WNS-Type: wns/toast';
$hub->sendNotification($notification, null);
```

### <a name="ios"></a>iOS

```php
$alert = '{"aps":{"alert":"Hello from PHP!"}}';
$notification = new Notification("apple", $alert);
$hub->sendNotification($notification, null);
```

### <a name="android"></a>Android

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("fcm", $message);
$hub->sendNotification($notification, null);
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 en 8,1 Silverlight

```php
$toast = '<?xml version="1.0" encoding="utf-8"?>' .
            '<wp:Notification xmlns:wp="WPNotification">' .
               '<wp:Toast>' .
                    '<wp:Text1>Hello from PHP!</wp:Text1>' .
               '</wp:Toast> ' .
            '</wp:Notification>';
$notification = new Notification("windowsphone", $toast);
$notification->headers[] = 'X-WindowsPhone-Target : toast';
$notification->headers[] = 'X-NotificationClass : 2';
$hub->sendNotification($notification, null);
```

### <a name="kindle-fire"></a>Kindle Fire

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("adm", $message);
$hub->sendNotification($notification, null);
```

Als u uw PHP-code uitvoert, moet er nu een melding op uw doel apparaat worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit onderwerp laten we zien hoe u een eenvoudige Java-REST-client maakt voor Notification Hubs. Hier kunt u het volgende doen:

* Down load het volledige [php rest-wrapper]-voor beeld, dat alle bovenstaande code bevat.
* Meer informatie over de functie voor het markeren van Notification Hubs in de [laatste nieuws zelf studie]
* Meer informatie over het pushen van meldingen naar afzonderlijke gebruikers in [zelf studie voor gebruikers melden]

Zie ook het [PHP-ontwikkelaars centrum](https://azure.microsoft.com/develop/php/)voor meer informatie.

[Voor beeld van PHP REST-wrapper]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Pushmeldingen verzenden naar iOS-apps met Azure Notification Hubs](ios-sdk-get-started.md))
