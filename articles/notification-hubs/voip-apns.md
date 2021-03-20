---
title: APNS VOIP-meldingen verzenden met Azure Notification Hubs
description: Meer informatie over het verzenden van APNS VOIP-meldingen via Azure Notification Hubs (niet officieel ondersteund).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80146886"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>APNS VOIP gebruiken via Notification Hubs (niet officieel ondersteund)

Het is mogelijk om APNS VOIP-meldingen te gebruiken via Azure Notification Hubs; Er is echter geen officiële ondersteuning voor dit scenario.

## <a name="considerations"></a>Overwegingen

Als u nog steeds APNS VOIP-meldingen via Notification Hubs wilt verzenden, moet u rekening houden met de volgende beperkingen:

- Voor het verzenden van een VOIP-melding moet de `apns-topic` header worden ingesteld op de Application bundel-id + het `.voip` achtervoegsel. Bijvoorbeeld: voor een voor beeld-app met de bundel-ID `com.microsoft.nhubsample` `apns-topic` moet de header worden ingesteld op `com.microsoft.nhubsample.voip.`

   Deze methode werkt niet goed met Azure Notification Hubs, omdat de bundel-ID van de app moet worden geconfigureerd als onderdeel van de APNS-referenties van de hub en de waarde kan niet worden gewijzigd. Daarnaast staat Notification Hubs niet toe dat de waarde van de `apns-topic` header tijdens runtime wordt overschreven.

   Als u VOIP-meldingen wilt verzenden, moet u een afzonderlijke notification hub configureren met de `.voip` app-bundel-id.

- Voor het verzenden van een VOIP-melding moet de `apns-push-type` header worden ingesteld op de waarde `voip` .

   Om klanten te helpen met de overgang naar iOS 13, probeert Notification Hubs de juiste waarde voor de header af te leiden `apns-push-type` . De interferentie logica is opzettelijk eenvoudig, in het belang om te voor komen dat standaard meldingen worden verbroken. Helaas veroorzaakt deze methode problemen met VOIP-meldingen, omdat Apple VOIP-meldingen behandelt als een speciaal geval dat niet dezelfde regels heeft als standaard meldingen.

   Als u VOIP-meldingen wilt verzenden, moet u een expliciete waarde voor de `apns-push-type` header opgeven.

- Met Notification Hubs worden APNS-nettoladingen beperkt tot 4 KB, zoals beschreven door Apple. Voor VOIP-meldingen biedt Apple payloads tot wel 5 KB. Notification Hubs maakt geen onderscheid tussen de standaard-en VOIP-meldingen. Daarom zijn alle meldingen beperkt tot 4 KB.

   Als u VOIP-meldingen wilt verzenden, moet u de maximale grootte van 4 KB voor de nettolading niet overschrijden.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende koppelingen voor meer informatie:

- [Documentatie voor `apns-topic` en `apns-push-type` kopteksten en waarden, met inbegrip van de speciale gevallen voor VoIP-meldingen](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentatie voor maximale grootte van nettolading](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Notification hubs updates voor IOS 13](push-notification-updates-ios-13.md#apns-push-type).
