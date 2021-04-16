---
title: Het gebruik van Notification Hubs met Python
description: Meer informatie over het gebruik van Azure Notification Hubs vanuit een Python-toepassing.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: f964957b916c6841da097f93173b0306bb65c8a4
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576020"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Een Notification Hubs python gebruiken

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

U hebt toegang tot alle Notification Hubs-functies vanuit een Java-/PHP-/Python-/Ruby-back-end met behulp van de Notification Hub REST-interface, zoals beschreven in het MSDN-artikel [Notification Hubs REST API's.](/previous-versions/azure/reference/dn223264(v=azure.100))

> [!NOTE]
> Dit is een voorbeeld van een referentie-implementatie voor het implementeren van de melding die in Python wordt verzendt en is niet de officieel ondersteunde Notification Hub Python SDK. Het voorbeeld is gemaakt met python 3.4.

In dit artikel leest u informatie over:

- Bouw een REST-client voor Notification Hubs-functies in Python.
- Meldingen verzenden met behulp van de Python-interface naar de Notification Hub REST API's.
- Haal een dump op van de HTTP REST-aanvraag/-respons voor het gebruik van debugging/het onderwijs.

U kunt de [zelfstudie Aan de slag volgen](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) voor uw mobiele platform naar keuze en het back-endgedeelte implementeren in Python.

> [!NOTE]
> Het bereik van het voorbeeld is alleen beperkt tot het verzenden van meldingen en er wordt geen registratiebeheer voor gebruikt.

## <a name="client-interface"></a>Clientinterface

De belangrijkste clientinterface kan dezelfde methoden bieden die beschikbaar zijn in [de .NET Notification Hubs SDK.](https://msdn.microsoft.com/library/jj933431.aspx) Met deze interface kunt u alle zelfstudies en voorbeelden die momenteel beschikbaar zijn op deze site, rechtstreeks vertalen en bijdragen leveren door de community op internet.

U vindt alle code die beschikbaar is in het [Python REST wrapper-voorbeeld.]

Als u bijvoorbeeld een client wilt maken:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Een Pop-upmelding voor Windows verzenden:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementatie

Als u dat nog [] niet hebt gedaan, volgt u de zelfstudie Aan de slag tot aan de laatste sectie waar u de back-end moet implementeren.

Alle details voor het implementeren van een volledige REST-wrapper vindt u op [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). In deze sectie wordt de Python-implementatie beschreven van de belangrijkste stappen die nodig zijn voor toegang Notification Hubs REST-eindpunten en het verzenden van meldingen

1. De verbindingsreeks parseren
2. Het autorisatie-token genereren
3. Een melding verzenden met http-REST API

### <a name="parse-the-connection-string"></a>De verbindingsreeks parseren

Hier is de hoofdklasse die de client implementeert, waarvan de constructor de connection string:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Beveiliging token maken

De details van het maken van het beveiliging token zijn hier [beschikbaar.](/previous-versions/azure/reference/dn495627(v=azure.100))
Voeg de volgende methoden toe aan de klasse om het token te maken op basis van de URI van de huidige aanvraag en de referenties die zijn geëxtraheerd `NotificationHub` uit de connection string.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Een melding verzenden met http-REST API

Laten we eerst een klasse definiëren gebruiken die een melding vertegenwoordigt.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'gcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Deze klasse is een container voor een systeemeigen meldingstekst of een set eigenschappen van een sjabloonmelding, een set headers die indeling (systeemeigen platform of sjabloon) en platformspecifieke eigenschappen (zoals de eigenschap Verloop van Apple en WNS-headers) bevat.

Raadpleeg de documentatie [Notification Hubs REST API's](/previous-versions/azure/reference/dn495827(v=azure.100)) en de indelingen van de specifieke meldingsplatforms voor alle beschikbare opties.

Schrijf nu met deze klasse de methoden voor het verzenden van meldingen in de `NotificationHub` klasse .

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_google_notification(self, payload, tags=""):
    nh = Notification("gcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Met deze methoden wordt een HTTP POST-aanvraag verzonden naar het eindpunt /messages van uw Notification Hub, met de juiste hoofdtekst en headers om de melding te verzenden.

### <a name="using-debug-property-to-enable-detailed-logging"></a>De foutopsporings eigenschap gebruiken om gedetailleerde logboekregistratie in teschakelen

Als u de eigenschap foutopsporing inschakelen tijdens het initialiseren van de Notification Hub, worden gedetailleerde logboekgegevens over de HTTP-aanvraag en de reactiedump wegschreven, evenals gedetailleerde resultaten van het verzenden van meldingen.
De [eigenschap Notification Hubs TestSend](/previous-versions/azure/reference/dn495827(v=azure.100)) retourneert gedetailleerde informatie over het resultaat van het verzenden van meldingen.
Als u deze wilt gebruiken, initialiseert u met behulp van de volgende code:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Als gevolg hiervan wordt de HTTP-URL van de Notification Hub-aanvraag verzenden toegevoegd met een 'test'-queryreeks.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Voltooi de zelfstudie

U kunt nu de zelfstudie Aan de slag voltooien door de melding te verzenden vanuit een Python-back-end.

Initialiseer uw Notification Hubs client (vervang de connection string en hubnaam zoals in de zelfstudie [Aan de]slag ):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Voeg vervolgens de verzendcode toe, afhankelijk van uw mobiele doelplatform. In dit voorbeeld worden ook methoden op een hoger niveau toegevoegd om het verzenden van meldingen mogelijk te maken op basis van het platform, send_windows_notification voor Windows; send_apple_notification (voor apple) enzovoort.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store en Windows Phone 8.1 (niet-Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 en 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
gcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_google_notification(gcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Als u de Python-code wilt uitvoeren, wordt er een melding weergegeven op het doelapparaat.

## <a name="examples"></a>Voorbeelden

### <a name="enabling-the-debug-property"></a>De eigenschap `debug` inschakelen

Wanneer u de foutopsporingsvlag inschakelen tijdens het initialiseren van de NotificationHub, ziet u gedetailleerde HTTP-aanvraag en antwoorddump, evenals NotificationOutcome, zoals hieronder, waar u kunt begrijpen welke HTTP-headers worden doorgegeven in de aanvraag en welk HTTP-antwoord is ontvangen van de Notification Hub:

![Schermopname van een console met details van de H T T P-aanvraag- en antwoorddump- en meldingsresultaten in rood omschreven.][1]

U ziet bijvoorbeeld een gedetailleerd Resultaat van Notification Hub.

- wanneer het bericht is verzonden naar de Push Notification Service.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Als er geen doelen zijn gevonden voor een pushmelding, ziet u waarschijnlijk de volgende uitvoer als antwoord (wat aangeeft dat er geen registraties zijn gevonden om de melding te leveren, waarschijnlijk omdat de registraties niet-overeenkomende tags hadden)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Pop-upmelding uitzenden naar Windows

Let op de headers die worden verzonden wanneer u een pop-upmelding verstuurt naar de Windows-client.

```python
hub.send_windows_notification(wns_payload)
```

![Schermopname van een console met details van de H T T P-aanvraag en de waarden Service Bus Notification Format en X W N S Type rood omschreven.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Melding verzenden waarin een tag (of tagexpressie) wordt opgegeven

Let op de TAGS HTTP-header, die wordt toegevoegd aan de HTTP-aanvraag (in het onderstaande voorbeeld wordt de melding alleen verzonden naar registraties met 'sport'-nettolading)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Schermopname van een console met details van de H T T P-aanvraag en de Service Bus Notification Format, de waarden Service Bus Notification Tag en X W N S Type rood.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Melding verzenden waarin meerdere tags worden opgegeven

U ziet hoe de TAGS HTTP-header verandert wanneer er meerdere tags worden verzonden.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Schermopname van een console met details van de H T T P-aanvraag en de Service Bus Notification Format, meerdere Service Bus Notification Tags en X W N S Type in rood omschreven waarden.][4]

### <a name="templated-notification"></a>Sjabloonmelding

U ziet dat de INDELING van de HTTP-header wordt gewijzigd en dat de hoofdtekst van de nettolading wordt verzonden als onderdeel van de hoofdtekst van de HTTP-aanvraag:

**Clientzijde - geregistreerde sjabloon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Serverzijde: de nettolading verzenden:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Schermopname van een console met details van de H T T P-aanvraag en de waarden inhoudstype en Service Bus notification format rood omschreven.][5]

## <a name="next-steps"></a>Volgende stappen

In dit artikel is beschreven hoe u een Python REST-client voor Notification Hubs. Hier kunt u het volgende doen:

- Download het volledige [Python REST wrapper-voorbeeld,]dat alle code in dit artikel bevat.
- Meer informatie over de functie Notification Hubs taggen in de [zelfstudie Voor belangrijk nieuws]
- Meer informatie over de functie Notification Hubs sjablonen in de [zelfstudie Nieuws lokaliseren]

<!-- URLs -->
[Voorbeeld van Python REST-wrapper]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Zelfstudie Aan de slag]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Zelfstudie voor het laatste nieuws]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Zelfstudie Nieuws lokaliseren]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
