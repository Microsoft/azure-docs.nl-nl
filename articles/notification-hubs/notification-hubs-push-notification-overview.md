---
title: Wat is Azure Notification Hubs?
description: Leer hoe u mogelijkheden voor pushmeldingen toevoegt met Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: ba5a329d12735fbddc86ff2e3725a1e7de6d9d89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546462"
---
# <a name="what-is-azure-notification-hubs"></a>Wat is Azure Notification Hubs?

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows enzovoort). Notification Hubs is zeer geschikt voor zowel bedrijfs- als consumentenscenario's. Enkele voorbeelden:

- Meldingen van belangrijk nieuws met een lage latentie verzenden naar miljoenen gebruikers.
- Coupons op basis van locatie verzenden naar geïnteresseerde gebruikerssegmenten.
- Meldingen voor evenementen verzenden naar gebruikers of groepen voor bijvoorbeeld media-, sport- en financiële toepassingen.
- Aanbiedingen pushen naar toepassingen om klanten te benaderen en werven.
- Gebruikers van bedrijfsgebeurtenissen, zoals nieuwe berichten en werkitems, op de hoogte stellen.
- Codes verzenden voor meervoudige verificatie.

## <a name="what-are-push-notifications"></a>Wat zijn pushmeldingen?

Pushmeldingen zijn een vorm van app-naar-gebruikercommunicatie waarbij gebruikers van mobiele apps op de hoogte worden gesteld van bepaalde informatie, meestal in een pop-upvenster of dialoogvenster, op een mobiel apparaat. Gebruikers kiezen er doorgaans voor om het bericht te bekijken of te sluiten; in het eerste geval wordt de mobiele toepassing geopend die de melding heeft gecommuniceerd. Sommige meldingen zijn stil; ze worden op de achtergrond verstuurd zodat de app ze kan verwerken om te bepalen wat er moet gebeuren.

Pushmeldingen zijn essentieel voor consumenten-apps om de betrokkenheid en het gebruik te vergroten. Voor zakelijke apps zijn ze belangrijk om actuele bedrijfsgegevens te communiceren. Het is de beste app-naar-gebruikercommunicatie omdat het energie-efficiënt is voor mobiele apparaten, flexibel voor de afzenders en beschikbaar wanneer de bijbehorende toepassingen niet actief zijn.

> [!NOTE]
> Azure Notification Hubs biedt geen officiële ondersteuning voor VOIP-pushmeldingen (Voice Over Internet Protocol); in [dit artikel wordt echter beschreven hoe u APNS VOIP-meldingen](voip-apns.md) via Azure Notification Hubs kunt gebruiken.

Zie de volgende onderwerpen voor meer informatie over pushmeldingen voor enkele populaire platforms:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>Hoe werken pushmeldingen?

Pushmeldingen worden bezorgd via platformspecifieke infrastructuren, *PNS (Platform Notification System)* genoemd. Ze bieden essentiële pushfunctionaliteit voor het bezorgen van een bericht op een apparaat met een opgegeven ingang, en hebben geen algemene interface. Om een melding te verzenden naar alle klanten via de Android-, iOS- en Windows-versies van een app, moet de ontwikkelaar afzonderlijk gebruikmaken van APNS (Apple Push Notification Service), FCM (Firebase Cloud Messaging) en WNS (Windows Notification Service).

Dit zijn de algemene stappen voor het verzenden van pushberichten:

1. Een app wil een melding ontvangen en maakt daarvoor verbinding met het PNS voor het doelplatform waarop de app wordt uitgevoerd en vraagt een unieke en tijdelijke push-ingang aan. Het type ingang is afhankelijk van het systeem (zo maakt WNS gebruik van URI's en APNS van tokens).
2. De client-app slaat deze ingang op in de back-end of provider van de app.
3. Voor het verzenden van een pushmelding neemt de back-end van de app via de ingang contact op met het PNS om een bepaalde client-app te bereiken.
4. De PNS stuurt de melding door naar het apparaat dat is opgegeven door de ingang.

![Werkstroom voor pushmeldingen](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Aandachtspunten voor pushmeldingen

Platform Notification System zijn krachtig. App-ontwikkelaars moeten echter zelfs voor meer algemene scenario’s voor pushmeldingen nog heel wat werk verrichten om deze te implementeren, bijvoorbeeld voor het broadcasten van pushmeldingen naar gesegmenteerde gebruikers.

Pushmeldingen vereisen een complexe infrastructuur die geen verband houdt met de voornaamste bedrijfslogica van de toepassing. Enkele aandachtspunten voor de infrastructuur:

- **Platformafhankelijkheid**
  - Aangezien de Platform Notification Systems niet uniform zijn, vereist de back-end complexe en moeilijk te onderhouden platformafhankelijke logica om meldingen te kunnen verzenden naar apparaten op verschillende platformen.
- **Schalen**
  - Volgens de PNS-richtlijnen moeten apparaattokens elke keer dat de app wordt gestart worden vernieuwd. Dit betekent dat de back-end een grote hoeveelheid verkeer en databasetoegang moet verwerken, alleen al om de tokens bij te werken. Wanneer het aantal apparaten groeit tot misschien wel miljoenen, lopen de kosten voor het maken en bijwerken van deze infrastructuur enorm op.
  - De meeste Platform Notification Systems bieden geen ondersteuning voor broadcasten naar meerdere apparaten. Om één bericht te broadcasten naar een miljoen apparaten, zijn een miljoen aanroepen naar de Platform Notification Systems nodig. Het is zeer lastig om een dergelijke hoeveelheid verkeer met een minimale latentie te schalen.
- **Routering**
  - Hoewel Platform Notification Systems een manier bieden om berichten naar apparaten te verzenden, worden de meeste app-meldingen gericht op bepaalde gebruikers of doelgroepen. In de back-end moet een register worden bijgehouden om apparaten te koppelen aan belangengroepen, gebruikers, eigenschappen, enzovoort. Deze overhead verlengt de implementatietijd en verhoogt de onderhoudskosten van een app.

## <a name="why-use-azure-notification-hubs"></a>Waarom kiezen voor Azure Notification Hubs?

Met Notification Hubs wordt het vele malen eenvoudiger om zelf pushmeldingen te versturen vanuit uw eigen app-back-end. De uitgeschaalde infrastructuur ondersteunt meerdere platforms, waardoor er minder push-gerelateerde code nodig is en de back-end kan worden vereenvoudigd. Met Notification Hubs hoeven apparaten alleen maar hun PNS-ingangen te registreren bij een hub, terwijl de back-end berichten verstuurt naar gebruikers of belangengroepen, zoals wordt weergegeven in de volgende afbeelding:

![Schema van Notification Hubs](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs is een gebruiksklare push-engine met de volgende voordelen:

- **Platformoverschrijdend**
  - Ondersteuning voor alle veelgebruikte push-platformen.
  - Een algemene interface om te pushen naar alle platforms in platformspecifieke of platformonafhankelijk indelingen zonder platformspecifieke taken.
  - Beheer van apparaatingang op één plaats.
- **Ondersteuning voor verschillende back-ends**
  - Cloud of on-premises.
  - .NET, Node.js, Java, Python enzovoort.
- **Veel verschillende bezorgingsmethoden**
  - Uitzenden naar een of meer platformen: U kunt met één API-aanroep direct uitzenden naar miljoenen apparaten op verschillende platformen.
  - Pushen naar apparaat: U kunt meldingen op afzonderlijke apparaten richten.
  - Pushen naar gebruiker: Met behulp van tags en sjablonen kunt u alle apparaten van een gebruiker op meerdere platformen bereiken.
  - Pushen naar segment met dynamische tags: Met behulp van tags kunt u apparaten onderverdelen in segmenten en vervolgens alleen naar een bepaald segment pushen of naar meerdere segmenten door een expressie te gebruiken (bijvoorbeeld actief AND woont in Apeldoorn NOT nieuwe gebruiker). U bent niet beperkt tot publiceren en abonneren; u kunt tags van apparaten altijd en overal bijwerken.
  - Gelokaliseerd pushen: Met behulp van sjablonen kunt u lokalisatie toepassen zonder dat de code voor de back-end hoeft te worden gewijzigd.
  - Pushen op de achtergrond: U kunt het push-naar-pull-patroon inschakelen door op de achtergrond meldingen te verzenden naar apparaten en deze zo opdracht te geven om bepaalde pulls of acties te voltooien.
  - Gepland pushen: U kunt meldingen inplannen om op elk gewenst moment te worden verzonden.
  - Direct pushen: U kunt het registreren van apparaten bij de Notification Hubs-service overslaan en rechtstreeks batchgewijs pushen naar een lijst met apparaatingangen.
  - Gepersonaliseerd pushen: Push-variabelen voor apparaten maken het mogelijk om apparaatspecifieke, gepersonaliseerde pushmeldingen te verzenden met aangepaste sleutel-waardeparen.
- **Uitgebreide telemetrie**
  - Er zijn via de Azure-portal en programmacode telemetriegegevens beschikbaar voor pushen, apparaten, fouten en bewerkingen.
  - Via de functie voor het bijhouden van telemetrie per bericht kunt u elk pushbericht volgen vanaf de eerste aanvraag tot het moment dat de Notification Hubs-service de pushberichten verstuurt.
  - Met de functie Feedback van Platform Notification System wordt alle feedback van Platform Notification Systems gecommuniceerd om te helpen bij foutopsporing.
- **Schaalbaarheid**
  - U kunt snel berichten versturen naar miljoenen apparaten zonder dat u de architectuur hoeft aan te passen of sharding hoeft toe te passen.
- **Beveiliging**
  - Shared Access Secret (SAS) of federatieve verificatie.

## <a name="next-steps"></a>Volgende stappen

U kunt zelf een meldingshub maken en gebruiken met deze [zelfstudie: Push notifications to mobile applications](notification-hubs-android-push-notification-google-fcm-get-started.md) (Pushmeldingen naar mobiele toepassingen sturen).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
