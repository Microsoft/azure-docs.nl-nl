---
title: Verificatie op basis van tokens (HTTP/2) voor APNS in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het gebruik van de nieuwe token verificatie voor APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460460"
---
# <a name="token-based-http2-authentication-for-apns"></a>Verificatie op basis van tokens (HTTP/2) voor APNS

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u het nieuwe APNS HTTP/2-protocol gebruikt met verificatie op basis van tokens.

De belangrijkste voor delen van het gebruik van het nieuwe protocol zijn:

* Het genereren van tokens is relatief eenvoudig (vergeleken met certificaten)
* Geen verval datums meer: u hebt de controle over uw verificatie tokens en de intrekking ervan
* Nettoladingen kunnen nu Maxi maal 4 KB groot zijn
* Synchrone feedback
* U bevindt zich in het meest recente Protocol: certificaten gebruiken nog steeds het binaire protocol, dat is gemarkeerd voor afschaffing

Het gebruik van dit nieuwe mechanisme kan in twee stappen worden uitgevoerd:

* Haal de benodigde informatie op uit de Apple Developer-account Portal.
* Configureer uw notification hub met de nieuwe informatie.

Notification Hubs is nu ingesteld voor het gebruik van het nieuwe verificatie systeem met APNS.

Houd er rekening mee dat als u de certificaat referenties voor APNS hebt gemigreerd, de token eigenschappen uw certificaat overschrijft in het systeem, maar uw toepassing blijft meldingen naadloos ontvangen.

## <a name="obtaining-authentication-information-from-apple"></a>Verificatie gegevens verkrijgen van Apple

Als u verificatie op basis van tokens wilt inschakelen, hebt u de volgende eigenschappen van uw Apple Developer-account nodig:

### <a name="key-identifier"></a>Sleutel-id

U kunt de sleutel-ID verkrijgen op de pagina **sleutels** onder **certificaten, id's & profielen** in uw Apple Developer-account:

![Certificaten](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![Id's](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Toepassings-id en toepassings naam

De toepassings naam en-id zijn ook beschikbaar op de pagina **certificaten, id's & profielen** in het ontwikkelaars account:

![Certificaten en Id's](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configureren via de .NET SDK of de Azure Portal

U kunt uw hub configureren voor het gebruik van verificatie op basis van tokens met behulp van onze [nieuwste client-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs), of in de Azure Portal. Als u verificatie op basis van tokens in de portal wilt inschakelen, meldt u zich aan bij de Azure Portal en gaat u naar de instellingen van uw notification hub **> het Apple (APNS)** . Selecteer **token** van de eigenschap **authenticatie modus** om uw hub bij te werken met alle relevante token eigenschappen.

![Token configureren](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Voer de eigenschappen in die u van uw Apple Developer-account hebt opgehaald.
* Kies de toepassings modus (**productie** of **sandbox**).
* Klik op de knop **Opslaan** om uw APNS-referenties bij te werken.

Op tokens gebaseerde referenties bestaan uit de volgende velden:

* **Sleutel-id**: de id van de persoonlijke sleutel die is gegenereerd in de Apple Developer-portal. bijvoorbeeld `2USFGKSKLT` .
* **Team-ID**: ook wel het voor voegsel of het voor voegsel van de app genoemd. Dit is de id voor de organisatie in de Apple Developer-portal. bijvoorbeeld `S4V3D7CHJR` .
* **Bundel-id**: ook wel de app-id genoemd. Dit is de bundel-id voor de toepassing. bijvoorbeeld `com.example.myapp` . Houd er rekening mee dat u slechts één sleutel voor één app kunt gebruiken. Deze waarde wordt toegewezen aan de `apns-topic` http-header bij het verzenden van een melding en wordt gebruikt om de specifieke toepassing te richten. U kunt de waarde van expliciet niet instellen `apns-topic` .
* **Token**: ook wel de ' sleutel ' of ' persoonlijke sleutel ' genoemd. Dit wordt opgehaald uit het. P8-bestand dat is gegenereerd op de Apple Developer-portal. Voor de sleutel moet APNS zijn ingeschakeld (deze is geselecteerd in de Apple-ontwikkelaars portal bij het genereren van de sleutel). Voor de waarde moet de PEM-kop/-voet tekst worden verwijderd wanneer u deze aan de NH-Portal/API levert.
* **Eind punt**: dit is een wissel knop in de blade notification hubs Portal en een teken reeks veld in de API. Geldige waarden zijn `https://api.development.push.apple.com:443/3/device` of `https://api.sandbox.push.apple.com:443/3/device` . Notification Hubs gebruikt deze waarde voor de productie-of sandbox-omgeving voor het verzenden van meldingen. Dit moet overeenkomen met het `aps-environment` recht in de app, anders worden de tokens van de APNS-apparaten gegenereerd die niet overeenkomen met de omgeving en kunnen de meldingen niet worden verzonden.

Hier volgt een code voorbeeld dat het juiste gebruik illustreert:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Volgende stappen

* [Een Azure notification hub maken met behulp van de Azure-portal](create-notification-hub-portal.md)
* [Een notification hub configureren in de Azure Portal](create-notification-hub-portal.md)
