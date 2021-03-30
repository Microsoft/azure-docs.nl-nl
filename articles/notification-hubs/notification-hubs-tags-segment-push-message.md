---
title: Routerings-en label expressies in azure Notification Hubs
description: Meer informatie over het omleiden en labelen van expressies voor Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ff8225522e79e2be40682fb5e4823777dde2aa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998299"
---
# <a name="routing-and-tag-expressions"></a>Routerings-en label expressies

## <a name="overview"></a>Overzicht

Met label expressies kunt u specifieke sets van apparaten of meer specifieke registraties richten bij het verzenden van een push melding via Notification Hubs.

## <a name="targeting-specific-registrations"></a>Specifieke registraties richten

De enige manier om specifieke meldings registraties te richten, is door labels te koppelen en vervolgens de labels te richten. Zoals beschreven in [registratie beheer](notification-hubs-push-notification-registration-management.md), voor het ontvangen van push meldingen, moet een app een apparaat-handle registreren op een notification hub. Zodra de app een registratie op een notification hub heeft gemaakt, kan de back-end van de toepassing push meldingen verzenden. De back-end van de toepassing kan op de volgende manieren de registraties voor doel met een specifieke melding kiezen:

1. **Broadcast**: alle registraties in de notification hub ontvangen de melding.
2. **Tag**: alle registraties die het opgegeven label bevatten ontvangen de melding.
3. **Tag-expressie**: alle registraties waarvan de set labels overeenkomen met de opgegeven expressie ontvangen de melding.

## <a name="tags"></a>Tags

Een tag kan een wille keurige teken reeks zijn, Maxi maal 120 tekens bevatten en de volgende niet-alfanumerieke tekens bevatten: ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ' `_` `@` `#` `.` `:` `-` . In het volgende voor beeld ziet u een toepassing waarvan u pop-upmeldingen over specifieke muziek groepen kunt ontvangen. In dit scenario is een eenvoudige manier om meldingen te routeren met label registraties met labels die de verschillende banden vertegenwoordigen, zoals in de volgende afbeelding:

![Overzicht van Tags](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

In de afbeelding bereikt het bericht dat met **Beatles** is gelabeld alleen de Tablet die is geregistreerd bij de tag **Beatles**.

Zie [registratie beheer](notification-hubs-push-notification-registration-management.md)voor meer informatie over het maken van registraties voor Tags.

U kunt meldingen naar Tags verzenden met behulp van de methoden voor het verzenden van meldingen van de `Microsoft.Azure.NotificationHubs.NotificationHubClient` klasse in de [Microsoft Azure notification hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. U kunt ook Node.js gebruiken, of de REST-Api's voor push meldingen.  Hier volgt een voor beeld van het gebruik van de SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Tags mogen niet vooraf worden ingericht en kunnen verwijzen naar meerdere app-specifieke concepten. Gebruikers van deze voorbeeld toepassing kunnen bijvoorbeeld opmerkingen maken op banden en pop-upmeldingen ontvangen, niet alleen voor de opmerkingen op hun favoriete banden, maar ook voor alle opmerkingen van hun vrienden, ongeacht de band waarop ze opmerkingen maken. In de volgende afbeelding ziet u een voor beeld van dit scenario:

![Tags voor vrienden](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

In dit voor beeld is Lisa geïnteresseerd in updates voor de Beatles en is Bob geïnteresseerd in updates voor de Wailers. Bob is ook geïnteresseerd in de opmerkingen van Charlie en Charlie is geïnteresseerd in de Wailers. Wanneer er een melding wordt verzonden voor de opmerking van Charlie op de Beatles, wordt deze door Notification Hubs naar zowel Alice als Robert verzonden.

Hoewel u meerdere problemen in tags kunt coderen (bijvoorbeeld `band_Beatles` of `follows_Charlie` ), zijn Tags eenvoudige teken reeksen en geen eigenschappen met waarden. Een registratie komt alleen overeen met de aanwezigheid of het ontbreken van een specifieke tag.

Zie voor een volledige stapsgewijze zelf studie over het gebruik van tags voor het verzenden van belangen groepen, het [afbreken van nieuws](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs ondersteunt Maxi maal 60 Tags per registratie.

## <a name="using-tags-to-target-users"></a>Labels gebruiken voor doel gebruikers

Een andere manier om tags te gebruiken is het identificeren van alle apparaten die aan een bepaalde gebruiker zijn gekoppeld. U kunt een registratie labelen met een tag die de gebruikers-ID bevat, zoals in de volgende afbeelding:

![Gebruikers labelen](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

In de afbeelding bereikt het gelabelde bericht `user_Alice` alle apparaten die zijn gelabeld met `user_Alice` .

## <a name="tag-expressions"></a>Label expressies

Er zijn gevallen waarin meldingen moeten worden gericht op een set registraties die niet zijn geïdentificeerd door één tag, maar door een Boole-expressie die gebruikmaakt van tags.

Overweeg een sport toepassing die een herinnering stuurt naar iedereen in Boston over een spel tussen de Rode Sox en de Cardinals. Als de client-app Tags registreert over interesse in teams en locatie, moet de melding worden gericht op iedereen in Boston die geïnteresseerd is in de Rode Sox of de Cardinals. Deze voor waarde kan worden weer gegeven met de volgende booleaanse expressie:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Label expressies](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Label expressies bieden ondersteuning voor veelvoorkomende Booleaanse Opera tors zoals `AND` ( `&&` ), `OR` ( `||` ) en `NOT` ( `!` ); ze kunnen ook haakjes bevatten. Label expressies met alleen `OR` Opera tors kunnen verwijzen naar 20 Tags; expressie met `AND` Opera Tors, maar geen `OR` Opera tors kunnen verwijzen naar tien Tags; anders zijn label expressies beperkt tot 6 labels.

Hier volgt een voor beeld van het verzenden van meldingen met label expressies met behulp van de SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
