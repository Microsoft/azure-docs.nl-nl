---
title: Updates voor Azure Notification Hubs iOS 13 | Microsoft Docs
description: Meer informatie over wijzigingen in de Azure-Notification Hubs voor iOS 13
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998047"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Updates voor Azure Notification Hubs voor iOS 13

Er zijn onlangs enkele wijzigingen aangebracht in de open bare push service van Apple. de wijzigingen zijn vooral afgestemd op de releases van iOS 13 en Xcode. In dit artikel wordt de impact van deze wijzigingen op Azure Notification Hubs beschreven.

## <a name="apns-push-payload-changes"></a>Wijzigingen in APNS-push nettoladingen

### <a name="apns-push-type"></a>Type APNS-push

Apple vereist nu dat ontwikkel aars meldingen identificeren als waarschuwings-of achtergrond meldingen via de nieuwe `apns-push-type` header in de APNS API. Volgens de [documentatie van Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): ' de waarde van deze header moet nauw keurig overeenkomen met de inhoud van de nettolading van uw melding. Als er sprake is van een niet-overeenkomend of als de header ontbreekt op de vereiste systemen, retourneert APNs mogelijk een fout, wordt de bezorging van de melding vertraagd of wordt deze helemaal verwijderd. "

Ontwikkel aars moeten deze header nu instellen in toepassingen die meldingen verzenden via Azure Notification Hubs. Vanwege een technische beperking moeten klanten verificatie op basis van tokens gebruiken voor APNS-referenties met aanvragen die dit kenmerk bevatten. Als u op certificaat gebaseerde verificatie gebruikt voor uw APNS-referenties, moet u overschakelen naar op tokens gebaseerde verificatie.

De volgende voorbeeld code laat zien hoe u dit header-kenmerk instelt in meldings aanvragen die via Azure Notification Hubs worden verzonden.

#### <a name="template-notifications---net-sdk"></a>Sjabloon meldingen-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Systeem eigen meldingen-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Directe REST-aanroepen

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Wanneer Azure Notification Hubs een melding detecteert die niet is `apns-push-type` ingesteld, wordt door de service het push-type van de meldings aanvraag afgezet en wordt de waarde automatisch ingesteld, om u tijdens deze overgang te helpen. Houd er rekening mee dat u Azure Notification Hubs moet configureren voor het gebruik van verificatie op basis van tokens om de vereiste header in te stellen. Zie [op tokens gebaseerde verificatie (http/2) voor APNS](./notification-hubs-push-notification-http2-token-authentication.md)voor meer informatie.

## <a name="apns-priority"></a>Prioriteit van APNS

Een andere kleine wijziging, maar een wijziging in de back-end-toepassing die meldingen verzendt, is de vereiste dat voor achtergrond meldingen de `apns-priority` header nu moet worden ingesteld op 5. Veel toepassingen stellen de `apns-priority` header in op 10 (hetgeen aangeeft directe levering), of deze niet instellen en de standaard waarde ontvangen (ook 10).

Het instellen van deze waarde op 10 is niet meer toegestaan voor achtergrond meldingen en u moet de waarde voor elke aanvraag instellen. Apple levert geen achtergrond meldingen als deze waarde ontbreekt. Bijvoorbeeld:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-wijzigingen

Voor jaren gebruiken iOS-ontwikkel aars het `description` kenmerk van de `deviceToken` gegevens die worden verzonden naar de gemachtigde van het push-token om het push token op te halen dat een back-end-toepassing gebruikt om meldingen naar het apparaat te verzenden. Met Xcode 11 is dat `description` kenmerk gewijzigd in een andere indeling. Bestaande code die ontwikkel aars voor dit kenmerk gebruiken, is nu verbroken. We hebben de Azure Notification Hubs SDK bijgewerkt zodat deze wijziging kan worden doorgevoerd. werk dus de SDK bij die door uw toepassingen wordt gebruikt voor versie 2.0.4 of hoger van de [Azure notification hubs IOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
