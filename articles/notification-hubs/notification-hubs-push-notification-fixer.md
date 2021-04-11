---
title: Problemen met verwijderde meldingen in azure Notification Hubs vaststellen
description: Meer informatie over het oplossen van veelvoorkomende problemen met verwijderde meldingen in azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 787cf922fcee0ee613fc0874a490830da9adf38a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455022"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Problemen met verwijderde meldingen in azure Notification Hubs vaststellen

Een veelgestelde vraag over Azure Notification Hubs is het oplossen van problemen met meldingen van een toepassing die niet op client apparaten worden weer gegeven. Klanten willen weten waar en waarom meldingen zijn verwijderd en hoe ze het probleem kunnen oplossen. In dit artikel wordt aangegeven waarom meldingen verloren kunnen gaan of niet worden ontvangen door apparaten. Ook wordt uitgelegd hoe u de hoofd oorzaak kunt bepalen.

Het is essentieel om eerst te begrijpen hoe Notification Hubs meldingen naar een apparaat pusht.

![Notification Hubs architectuur][0]

In een typische meldings stroom verzenden wordt het bericht verzonden vanuit de *back-end* van de toepassing naar notification hubs. Notification Hubs alle registraties verwerkt. Het houdt rekening met de geconfigureerde Tags en label expressies om doelen te bepalen. Doelen zijn de registraties die de push melding moeten ontvangen. Deze registraties kunnen een van de volgende ondersteunde platforms omvatten: Android, Baidu (Android-apparaten in China), Fire OS (Amazon) iOS, Windows en Windows Phone.

Met de ingestelde doelen Notification Hubs push meldingen naar de *service voor push meldingen* voor het platform van het apparaat. Voor beelden zijn de Apple Push Notification Service (APNs) voor iOS en macOS, en Firebase Cloud Messa ging (FCM) voor Android-apparaten. Notification Hubs pusht meldingen over meerdere batches van registraties. De verificatie wordt uitgevoerd met de respectieve Push Notification Service, op basis van de referenties die u hebt ingesteld in de Azure Portal, onder **Notification hub configureren**. De Push Notification service stuurt de meldingen vervolgens door naar de respectievelijke *client apparaten*.

Het laatste gedeelte van de meldings levering ligt tussen de push meldingen service van het platform en het apparaat. De bezorging van een melding kan mislukken in een van de vier fasen van het push meldings proces (client, back-end van toepassing, Notification Hubs en de Push Notification Service van het platform). Zie [Notification hubs Overview]voor meer informatie over de notification hubs architectuur.

Er kan een fout optreden bij het afleveren van meldingen tijdens de eerste fase van de test/fase ring. Verwijderde meldingen in deze fase kunnen duiden op een configuratie probleem. Als er een fout optreedt bij het afleveren van meldingen, worden sommige of alle meldingen mogelijk verwijderd. In dit geval wordt een dieper probleem met toepassings-of bericht patronen aangegeven.

In de volgende sectie ziet u scenario's waarin meldingen kunnen worden verwijderd, variërend van gebruikelijk tot zeldzaam.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs onjuiste configuratie

Als u meldingen wilt verzenden naar de respectieve Push Notification Service, moet Notification Hubs zichzelf verifiëren in de context van uw toepassing. U moet een ontwikkelaars account maken met de meldings service van het doel platform (micro soft, Apple, Google, etc.). Vervolgens moet u uw toepassing registreren bij het besturings systeem waar u een token of sleutel krijgt die u gebruikt om te werken met de doel-PNS.

U moet de referenties van het platform toevoegen aan de Azure Portal. Als er geen meldingen worden bereikt op het apparaat, is de eerste stap om te controleren of de juiste referenties zijn geconfigureerd in Notification Hubs. De referenties moeten overeenkomen met de toepassing die is gemaakt onder een platformspecifieke ontwikkelaars account.

Zie [aan de slag met Azure notification hubs]voor stapsgewijze instructies om dit proces te volt ooien.

Hier volgen enkele veelvoorkomende onjuiste configuraties om te controleren:

### <a name="notification-hub-name-location"></a>Locatie van de naam van de notification hub

Zorg ervoor dat de naam van de notification hub (zonder type fouten) hetzelfde is op elk van deze locaties:

* De locatie van de registratie bij de client
* Waar u meldingen van de back-end verzendt
* Waar u de referenties voor de Push Notification Service hebt geconfigureerd

Zorg ervoor dat u de juiste configuratie teken reeksen voor gedeelde toegangs handtekening gebruikt op de client en de back-end van de toepassing. Over het algemeen moet u **DefaultListenSharedAccessSignature** gebruiken op de client en **DefaultFullSharedAccessSignature** op de back-end van de toepassing. Hiermee worden machtigingen verleend voor het verzenden van meldingen naar Notification Hubs.

### <a name="apn-configuration"></a>APN-configuratie

U moet twee verschillende hubs onderhouden: één voor productie en een andere voor het testen. U moet het certificaat dat u in een sandbox-omgeving gebruikt uploaden naar een afzonderlijke hub dan het certificaat/de hub dat u in productie gaat gebruiken. Probeer niet verschillende typen certificaten te uploaden naar dezelfde hub. Er worden meldings fouten veroorzaakt.

Als u per ongeluk verschillende typen certificaten naar dezelfde hub uploadt, moet u de hub verwijderen en met een nieuwe hub beginnen met vernieuwen. Als u om een of andere reden de hub niet kunt verwijderen, moet u alle bestaande registraties van de hub ten minste verwijderen.

### <a name="fcm-configuration"></a>FCM-configuratie

1. Zorg ervoor dat de *Server sleutel* die u hebt verkregen van Firebase overeenkomt met de server sleutel die u hebt geregistreerd in de Azure Portal.

   ![Firebase-server sleutel][3]

2. Zorg ervoor dat u de **project-id** op de client hebt geconfigureerd. U kunt de waarde voor **project-id** verkrijgen in het Firebase-dash board.

   ![Firebase project-ID][1]

## <a name="application-issues"></a>Toepassings problemen

### <a name="tags-and-tag-expressions"></a>Tags en label expressies

Als u labels of label expressies gebruikt om uw doel groep te segmenteren, is het mogelijk dat er geen doel wordt gevonden wanneer u de melding verzendt. Deze fout is gebaseerd op de opgegeven Tags of label expressies in uw gesprek verzenden.

Controleer uw registraties om te controleren of de labels overeenkomen wanneer u een melding verzendt. Controleer vervolgens de ontvangst bevestiging van alleen de clients die deze registraties hebben.

Stel bijvoorbeeld dat alle registraties met Notification Hubs de tag ' politiek ' gebruiken. Als u vervolgens een melding met het label sporten verzendt, wordt de melding niet naar een apparaat verzonden. Een complexe case kan bestaan uit label expressies waarbij u zich hebt geregistreerd met behulp van ' tag A ' *of* ' tag B ', maar waarvoor u een label a && tag b hebt gericht. Verderop in dit artikel leest u hoe u uw registraties en de bijbehorende labels kunt controleren.

### <a name="template-issues"></a>Sjabloon problemen

Als u sjablonen gebruikt, moet u ervoor zorgen dat u de richt lijnen volgt die worden beschreven in [sjablonen].

### <a name="invalid-registrations"></a>Ongeldige registraties

Als de notification hub correct is geconfigureerd en tags of label expressies correct zijn gebruikt, zijn er geldige doelen gevonden. Meldingen moeten naar deze doelen worden verzonden. Notification Hubs vervolgens meerdere verwerkings batches parallel afgesloten. Elke batch verzendt berichten naar een set registraties.

> [!NOTE]
> Omdat Notification Hubs batches parallel verwerkt, wordt de volg orde waarin de meldingen worden bezorgd niet gegarandeerd.

Notification Hubs is geoptimaliseerd voor het bezorgings model ' in de meeste keren '. We proberen ontdubbeling uit te voeren, zodat er geen meldingen meer dan één keer naar een apparaat worden geleverd. Registraties worden gecontroleerd om ervoor te zorgen dat er slechts één bericht per apparaat-id wordt verzonden voordat het naar de Push Notification Service wordt verzonden.

Elke batch wordt verzonden naar de Push Notification Service, die op zijn beurt de registraties accepteert en valideert. Tijdens dit proces is het mogelijk dat de Push Notification Service een fout detecteert met een of meer registraties in een batch. De Push Notification service retourneert vervolgens een fout naar Notification Hubs en het proces wordt gestopt. De Push Notification Service daalt die batch volledig. Dit geldt met name voor APNs, dat gebruikmaakt van een TCP-stream protocol.

In dit geval wordt de fout registratie verwijderd uit de data base. Vervolgens wordt de meldings levering voor de rest van de apparaten in die batch opnieuw geprobeerd.

Als u meer informatie wilt over de mislukte bezorgings poging voor een registratie, kunt u de Notification Hubs REST-Api's [per bericht-telemetrie gebruiken: Ontvang een telemetrie-meldings bericht](/rest/api/notificationhubs/get-notification-message-telemetry) en [PNS feedback](/previous-versions/azure/reference/mt705560(v=azure.100)). Zie de [rest-voor beeld verzenden](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)voor een voorbeeld code.

## <a name="push-notification-service-issues"></a>Problemen met Push Notification Service

Nadat de Push Notification Service de melding heeft ontvangen, wordt de melding aan het apparaat geleverd. Op dit moment heeft Notification Hubs geen controle over de levering van de melding aan het apparaat.

Omdat platform Notification Services robuust zijn, hebben meldingen doorgaans binnen een paar seconden toegang tot de apparaten. Als de service voor push meldingen wordt beperkt, Notification Hubs een exponentiële back-upstrategie Toep assen. Als de Push Notification Service gedurende 30 minuten niet bereikbaar is, is er een beleid om te verlopen en de berichten permanent neer te zetten.

Als een Push Notification Service een melding probeert af te leveren maar het apparaat offline is, wordt de melding opgeslagen door de service voor push meldingen. Het wordt slechts gedurende een beperkte periode bewaard. De melding wordt op het apparaat bezorgd wanneer het apparaat beschikbaar wordt.

In elke app wordt slechts één recente melding opgeslagen. Als er meerdere meldingen worden verzonden terwijl een apparaat offline is, zorgt elke nieuwe melding ervoor dat de laatste wordt verwijderd. Het *bewaren van de* nieuwste melding heet samen voegen in APNs en *samengevouwen* in FCM. (FCM gebruikt een samenvouw sleutel.) Wanneer het apparaat lange tijd offline blijft, worden meldingen die voor het apparaat zijn opgeslagen, verwijderd. Zie [overzicht van APNs] en [informatie over FCM-berichten]voor meer informatie.

Met Notification Hubs kunt u een samenvoegings sleutel door geven via een HTTP-header met behulp van de algemene SendNotification-API. U kunt bijvoorbeeld voor de .NET SDK gebruiken `SendNotificationAsync` . De SendNotification-API accepteert ook HTTP-headers die worden door gegeven aan de respectieve Push Notification Service.

## <a name="self-diagnosis-tips"></a>Tips voor zelf diagnose

Hier vindt u de paden voor het vaststellen van de hoofd oorzaak van verwijderde meldingen in Notification Hubs.

### <a name="verify-credentials"></a>Referenties verifiëren

#### <a name="push-notification-service-developer-portal"></a>Service ontwikkelaars portal voor push meldingen

Controleer de referenties in de respectieve Push Notification Service Developer-Portal (APNs, FCM, Windows Notification Service, enzovoort). Zie [zelf studie: meldingen verzenden naar universeel Windows-platform-apps met behulp van Azure notification hubs](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor meer informatie.

#### <a name="azure-portal"></a>Azure Portal

Ga naar het tabblad **toegangs beleid** in het Azure Portal om de referenties te controleren en te vergelijken met de gegevens die u hebt verkregen via de Push Notification Service-ontwikkelaars Portal.

![Toegangs beleid Azure Portal][4]

### <a name="verify-registrations"></a>Registraties verifiëren

#### <a name="visual-studio"></a>Visual Studio

In Visual Studio kunt u verbinding maken met Azure via Server Explorer om meerdere Azure-Services weer te geven en te beheren, met inbegrip van Notification Hubs. Deze snelkoppeling is vooral nuttig voor uw ontwikkel-en test omgeving.

![Server Explorer van Visual Studio][9]

![Server Explorer](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

U kunt alle registraties in uw hub weer geven en beheren. De registraties kunnen worden gecategoriseerd op basis van platform, systeem eigen of sjabloon registratie, tag, Push Notification Service-id, registratie-ID en verval datum. U kunt ook een registratie op deze pagina bewerken. Het is vooral handig voor het bewerken van tags.

Klik met de rechter muisknop op de notification hub in **Server Explorer** en selecteer **diagnose**. 

![Visual Studio Server Explorer: diagnose menu](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

U ziet de volgende pagina:

![Visual Studio: diagnose van de pagina](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Schakel over naar **de pagina apparaatregistratie:**

![Visual Studio: apparaatregistratie](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

U kunt de pagina **verzenden testen** gebruiken om een bericht voor een test melding te verzenden:

![Visual Studio: verzenden testen](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Gebruik Visual Studio om alleen registraties te bewerken tijdens ontwikkeling/testen en met een beperkt aantal registraties. Als u uw registraties in bulk wilt bewerken, kunt u overwegen om de registratie functionaliteit voor exporteren en importeren te gebruiken die wordt beschreven in [How to: registraties bulksgewijs exporteren en wijzigen](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Veel klanten gebruiken [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) om hun notification hubs weer te geven en te beheren. Service Bus Explorer is een open source-project. 

### <a name="verify-message-notifications"></a>Bericht meldingen controleren

#### <a name="azure-portal"></a>Azure Portal

Als u een test melding naar uw clients wilt verzenden zonder dat er een back-up van een service wordt uitgevoerd, selecteert u onder **ondersteuning en probleem oplossing** de optie **Test verzenden**.

![De functionaliteit voor verzenden testen in azure][7]

#### <a name="visual-studio"></a>Visual Studio

U kunt ook test meldingen verzenden vanuit Visual Studio.

![De functionaliteit voor verzenden testen in Visual Studio][10]

Zie de volgende artikelen voor meer informatie over het gebruik van Notification Hubs met Visual Studio Server Explorer:

* [Apparaatregistratie voor notification hubs weer geven](/previous-versions/windows/apps/dn792122(v=win.10))
* [Grondige kennis: Visual Studio 2013 update 2 RC en Azure SDK 2,3]
* [Aankondiging van de release van Visual Studio 2013 update 3 en Azure SDK 2,4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Fouten opsporen in mislukte meldingen en resultaten van beoordelings meldingen

#### <a name="enabletestsend-property"></a>Eigenschap EnableTestSend

Wanneer u een melding verzendt via Notification Hubs, wordt de melding in eerste instantie in de wachtrij geplaatst. Notification Hubs bepaalt de juiste doelen en verzendt de melding vervolgens naar de Push Notification Service. Als u de REST API of een van de client-Sdk's gebruikt, betekent het retour neren van uw verzend oproep alleen dat het bericht in de wachtrij is geplaatst met Notification Hubs. Het biedt geen inzicht in wat er is gebeurd wanneer Notification Hubs uiteindelijk de melding naar de Push Notification Service hebt verzonden.

Als uw melding niet op het client apparaat arriveert, is er mogelijk een fout opgetreden toen Notification Hubs deze probeerde te leveren aan de Push Notification Service. Zo kan de nettolading groter zijn dan het maximum dat is toegestaan door de service voor push meldingen of de referenties die in Notification Hubs zijn geconfigureerd, ongeldig zijn.

U kunt de eigenschap [EnableTestSend] gebruiken om inzicht te krijgen in service fouten van push meldingen. Deze eigenschap wordt automatisch ingeschakeld wanneer u test berichten verzendt vanuit de portal of de Visual Studio-client. U kunt deze eigenschap gebruiken om gedetailleerde informatie over fout opsporing en ook via Api's te bekijken. Op dit moment kunt u het gebruiken in de .NET SDK. Deze wordt uiteindelijk toegevoegd aan alle client-Sdk's.

Als u de `EnableTestSend` eigenschap met de rest-aanroep wilt gebruiken, voegt u een query reeks parameter met de naam *test* toe aan het einde van uw verzend oproep. Bijvoorbeeld:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Voor beeld van .NET SDK

Hier volgt een voor beeld van het gebruik van de .NET-SDK voor het verzenden van een pop-upmelding in de native modus:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Aan het einde van de uitvoering, `result.State` staat er gewoon `Enqueued` . De resultaten bieden geen inzicht in wat er is gebeurd met uw push melding.

Daarna kunt u de `EnableTestSend` Booleaanse eigenschap gebruiken. Gebruik de `EnableTestSend` eigenschap terwijl u initialiseert `NotificationHubClient` om een gedetailleerde status te krijgen over Push Notification Service-fouten die optreden wanneer de melding wordt verzonden. De verzend oproep heeft extra tijd nodig om te retour neren, omdat het eerst moet Notification Hubs om de melding aan de Push Notification Service te kunnen leveren.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Voorbeelduitvoer

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Dit bericht geeft aan dat de referenties die zijn geconfigureerd in Notification Hubs ongeldig zijn of dat er een probleem is met de registraties in de hub. Verwijder deze registratie en laat de client de registratie opnieuw maken voordat het bericht wordt verzonden.

> [!NOTE]
> Het gebruik van de `EnableTestSend` eigenschap is zwaar beperkt. Gebruik deze optie alleen in een ontwikkel-en test omgeving en met een beperkt aantal registraties. Debug-meldingen worden naar slechts 10 apparaten verzonden. Er is ook een limiet voor het verwerken van debug-verzen dingen, 10 per minuut.

### <a name="review-telemetry"></a>Telemetrie controleren

#### <a name="azure-portal"></a>Azure Portal

In de portal kunt u een snel overzicht krijgen van alle activiteiten in uw notification hub.

1. Op het tabblad **overzicht** ziet u een geaggregeerde weer gave van registraties, meldingen en fouten per platform.

   ![Notification Hubs-overzichts dashboard][5]

2. Op het tabblad **monitor** kunt u veel andere platformspecifieke metrische gegevens voor een diep gaande blik toevoegen. U kunt specifiek kijken naar fouten die worden geretourneerd wanneer Notification Hubs probeert de melding te verzenden naar de Push Notification Service.

   ![Azure Portal activiteiten logboek][6]

3. Begin met het controleren van **inkomende berichten**, **registratie bewerkingen** en **geslaagde meldingen**. Ga vervolgens naar het tabblad per platform om fouten te bekijken die specifiek zijn voor de Push Notification Service.

4. Als de verificatie-instellingen voor uw notification hub onjuist zijn, wordt de bericht **PNS-verificatie fout** weer gegeven. Het is een goede indicatie om de referenties van de Push Notification Service te controleren.

#### <a name="programmatic-access"></a>Toegang op programmeerniveau

Zie [programmatische toegang](/previous-versions/azure/azure-services/dn458823(v=azure.100))voor meer informatie over toegang via een programma.

> [!NOTE]
> Verschillende telemetrie-gerelateerde functies, zoals het exporteren en importeren van registraties en telemetrie-toegang via Api's, zijn alleen beschikbaar voor de Standard-servicelaag. Als u deze functies probeert te gebruiken vanuit de laag gratis of Basic, ontvangt u een uitzonderings bericht als u de SDK gebruikt. U ontvangt een HTTP-fout 403 (verboden) als u de functies rechtstreeks vanuit de REST-Api's gebruikt.
>
> Als u functies met betrekking tot telemetrie wilt gebruiken, zorg er dan voor dat u eerst de Azure Portal hebt die u gebruikt voor de servicelaag Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Overzicht van Notification Hubs]: notification-hubs-push-notification-overview.md
[Aan de slag met Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Sjablonen]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[Overzicht van APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Over FCM-berichten]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Grondige kennis: Visual Studio 2013 update 2 RC en Azure SDK 2,3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Aankondiging van de release van Visual Studio 2013 update 3 en Azure SDK 2,4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
