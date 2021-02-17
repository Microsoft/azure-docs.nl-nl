---
title: Notification Hubs beveiligings model
description: Meer informatie over het beveiligings model voor Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548247"
---
# <a name="notification-hubs-security"></a>Notification Hubs beveiliging

## <a name="overview"></a>Overzicht

In dit onderwerp wordt het beveiligings model van Azure Notification Hubs beschreven.

## <a name="shared-access-signature-security"></a>Shared Access Signature beveiliging

Notification Hubs implementeert een beveiligings schema op entiteits niveau met de naam een *Shared Access Signature* (SAS). Elke regel bevat een naam, een sleutel waarde (gedeeld geheim) en een set rechten, zoals verderop in [beveiligings claims](#security-claims)wordt uitgelegd. 

Wanneer u een hub maakt, worden er automatisch twee regels gemaakt: één met **Luister** rechten (die de client-app gebruikt) en één met **alle** rechten (die de app-back-end gebruikt):

- **DefaultListenSharedAccessSignature**: verleent alleen **listen** -machtiging.
- **DefaultFullSharedAccessSignature**: verleent machtigingen voor **Luis teren**, **beheren** en **verzenden** . Dit beleid wordt alleen gebruikt in de back-end van uw app. Gebruik het niet in client toepassingen. Gebruik een beleid met alleen toegang voor **Luis teren** . Zie [SAS-tokens voor toegangs beleid](#sas-tokens-for-access-policies) verderop in dit artikel voor meer informatie over het maken van een nieuw aangepast toegangs beleid met een nieuwe SAS-token.

Bij het uitvoeren van registratie beheer vanuit client-apps, als de gegevens die via meldingen worden verzonden, niet gevoelig zijn (bijvoorbeeld weer updates), een veelgebruikte manier om toegang te krijgen tot een notification hub is de sleutel waarde van de regel alleen-lezen toegang tot de client-app te geven en de sleutel waarde van de regel volledige toegang te geven tot de back-end van de app.

Apps moeten de sleutel waarde niet insluiten in Windows Store-client-apps. Laat de client-app in plaats daarvan het ophalen van de back-end van de app bij het opstarten.

De sleutel met **listen** -toegang kan een client-app voor elk label registreren. Als uw app de registraties beperkt tot specifieke tags voor specifieke clients (bijvoorbeeld wanneer labels gebruikers-Id's vertegenwoordigen), moet uw app-back-end de registraties uitvoeren. Zie [registratie beheer](notification-hubs-push-notification-registration-management.md)voor meer informatie. Op deze manier is de client-app niet rechtstreeks toegang tot Notification Hubs.

## <a name="security-claims"></a>Beveiligings claims

Net als andere entiteiten zijn notification hub-bewerkingen toegestaan voor drie beveiligings claims: **Luis teren**, **verzenden** en **beheren**.

| Claim   | Description                                          | Toegestane bewerkingen |
| ------- | ---------------------------------------------------- | ------------------ |
| Luisteren  | Enkelvoudige registraties maken/bijwerken, lezen en verwijderen | Registratie maken/bijwerken<br><br>Registratie lezen<br><br>Alle registraties voor een ingang lezen<br><br>Registratie verwijderen |
| Verzenden    | Berichten verzenden naar de notification hub                | Bericht verzenden |
| Beheren  | RUWE gegevens op Notification Hubs (inclusief het bijwerken van PNS-referenties en beveiligings sleutels) en het lezen van registraties op basis van Tags |Hubs maken/bijwerken/lezen/verwijderen<br><br>Registraties per tag lezen |

Notification Hubs accepteert SAS-tokens die zijn gegenereerd met gedeelde sleutels die rechtstreeks op de hub zijn geconfigureerd.

Het is niet mogelijk om een melding naar meer dan één naam ruimte te verzenden. Naam ruimten zijn logische containers voor Notification Hubs en zijn niet betrokken bij het verzenden van meldingen.

Gebruik het toegangs beleid op naam ruimte niveau (referenties) voor bewerkingen op naam ruimte niveau. bijvoorbeeld: het vermelden van hubs, het maken of verwijderen van hubs, enzovoort. Alleen met het toegangs beleid op hubniveau kunt u meldingen verzenden.

### <a name="sas-tokens-for-access-policies"></a>SAS-tokens voor toegangs beleid

Ga als volgt te werk om een nieuwe beveiligings claim te maken of om bestaande SAS-sleutels weer te geven:

1. Meld u aan bij de Azure-portal.
2. Selecteer **Alle resources**.
3. Selecteer de naam van de notification hub waarvoor u de claim wilt maken of Bekijk de SAS-sleutel.
4. Selecteer in het linkermenu **toegangs beleid**.
5. Selecteer **Nieuw beleid** om een nieuwe beveiligings claim te maken. Geef het beleid een naam en selecteer de machtigingen die u wilt verlenen. Selecteer vervolgens **OK**.
6. De volledige connection string (inclusief de nieuwe SAS-sleutel) wordt weer gegeven in het venster toegangs beleid. U kunt deze teken reeks naar het klem bord kopiëren voor later gebruik.

Als u de SAS-sleutel uit een specifiek beleid wilt extra heren, selecteert u de knop **kopiëren** naast het beleid met de gewenste SAS-sleutel. Plak deze waarde op een tijdelijke locatie en kopieer vervolgens het SAS-sleutel gedeelte van de connection string. In dit voor beeld wordt gebruikgemaakt van een Notification Hubs naam ruimte met de naam **mytestnamespace1** en een beleid met de naam **policy2**. De SAS-sleutel is de waarde aan het einde van de teken reeks, opgegeven door **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS-sleutels ophalen](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Notification Hubs](notification-hubs-push-notification-overview.md)
