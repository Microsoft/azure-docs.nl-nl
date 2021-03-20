---
title: Gelokaliseerde push meldingen verzenden naar iOS met Azure Notification Hubs | Microsoft Docs
description: Meer informatie over het gebruik van gelokaliseerde push meldingen naar iOS-apparaten met behulp van Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a78d3a76e2b13a120e9e744e181c95bfcb330f27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313915"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Zelf studie: gelokaliseerde push meldingen verzenden naar iOS met Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

In deze zelf studie leert u hoe u de [sjabloon](notification-hubs-templates-cross-platform-push-messages.md) functie van Azure notification hubs gebruikt voor het uitzenden van verbroken Nieuws meldingen die zijn gelokaliseerd per taal en apparaat. In deze zelf studie begint u met de iOS-app die u hebt gemaakt in [gebruik notification hubs om het laatste nieuws te verzenden]. Wanneer u klaar bent, kunt u zich registreren voor de categorieën waarin u bent geïnteresseerd, een taal opgeven waarin de meldingen moeten worden ontvangen en alleen push meldingen ontvangen voor de geselecteerde categorieën in die taal.

Er zijn twee onderdelen voor dit scenario:

* met de iOS-app kunnen client apparaten een taal opgeven en zich abonneren op verschillende belang rijke Nieuws Categorieën;
* De back-end verzendt de meldingen met behulp van het **Label** en de **sjabloon** functies van Azure notification hubs.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * De gebruikers interface van de app bijwerken
> * De iOS-app bouwen
> * Gelokaliseerde sjabloon meldingen verzenden vanuit een .NET-console-app
> * Gelokaliseerde sjabloon meldingen verzenden van het apparaat

## <a name="overview"></a>Overzicht

In [Notification hubs gebruiken om een nieuw nieuws te verzenden], hebt u een app gemaakt die gebruikmaakt van **Tags** om u te abonneren op meldingen voor verschillende nieuws categorieën. Veel apps zijn echter gericht op meerdere markten en moeten worden gelokaliseerd. Dit betekent dat de inhoud van de meldingen zelf moet worden gelokaliseerd en geleverd aan de juiste set apparaten. In deze zelf studie wordt uitgelegd hoe u de **sjabloon** functie van Notification hubs kunt gebruiken om eenvoudig gelokaliseerde vernieuwings meldingen te leveren.

> [!NOTE]
> Een manier om gelokaliseerde meldingen te verzenden is door meerdere versies van elke tag te maken. Om bijvoorbeeld Engels, Frans en Mandarijn te ondersteunen, hebt u drie verschillende tags nodig voor wereld nieuws: "world_en", "world_fr" en "world_ch". Vervolgens moet u een gelokaliseerde versie van het World News naar elk van deze Tags verzenden. In dit onderwerp gebruikt u sjablonen om de verspreiding van tags en de vereiste voor het verzenden van meerdere berichten te voor komen.

Sjablonen zijn een manier om op te geven hoe een specifiek apparaat een melding moet ontvangen. De sjabloon bepaalt de exacte indeling van de payload door te verwijzen naar eigenschappen die deel uitmaken van het bericht dat is verzonden door uw back-endapp. In uw geval verzendt u een neutraal-bericht dat alle ondersteunde talen bevat:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Vervolgens zorgt u ervoor dat apparaten worden geregistreerd met een sjabloon die verwijst naar de juiste eigenschap. Een iOS-app die wil registreren voor Franse nieuws registers, met de volgende syntaxis:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Zie het artikel over [sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie over sjablonen.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Push meldingen naar specifieke IOS-apparaten](notification-hubs-ios-xplat-segmented-apns-push-notification.md) zelf studie en laat de code beschikbaar, omdat deze zelf studie rechtstreeks op die code bouwt.
* Visual Studio 2019 is optioneel.

## <a name="update-the-app-user-interface"></a>De gebruikers interface van de app bijwerken

In deze sectie wijzigt u de laatste nieuws-app die u in het onderwerp hebt gemaakt, [met behulp van Notification hubs om een nieuw nieuws] te verzenden om vertaalde nieuws te verzenden met behulp van sjablonen.

Voeg in uw `MainStoryboard_iPhone.storyboard` een gesegmenteerd besturings element toe met de drie talen: Engels, Frans en Mandarijn.

![Het Story Board voor iOS-gebruikers interface maken][13]

Zorg ervoor dat u een IBOutlet toevoegt in uw view controller. h zoals wordt weer gegeven in de volgende afbeelding:

![Maak mogelijkheden voor de switches][14]

## <a name="build-the-ios-app"></a>De iOS-app bouwen

1. Voeg in uw `Notification.h` de- `retrieveLocale` methode toe en wijzig de methoden Store en Subscriber, zoals wordt weer gegeven in de volgende code:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Wijzig in uw `Notification.m` de- `storeCategoriesAndSubscribe` methode door de para meter toe te voegen `locale` en op te slaan in de standaard waarden van de gebruiker:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Wijzig vervolgens de methode *Subscriber* om de land instelling op te nemen:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    U gebruikt de `registerTemplateWithDeviceToken` -methode in plaats van `registerNativeWithDeviceToken` . Wanneer u zich registreert voor een sjabloon, moet u de JSON-sjabloon opgeven en ook een naam voor de sjabloon (omdat de app mogelijk andere sjablonen wil registreren). Zorg ervoor dat u uw categorieën als Tags registreert, zodat u zeker weet dat u de meldingen voor die nieuws wilt ontvangen.

    Voeg een methode toe om de land instelling op te halen uit de standaard instellingen van de gebruiker:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nu u de klasse hebt gewijzigd `Notifications` , moet u ervoor zorgen dat de `ViewController` nieuwe wordt gebruikt `UISegmentControl` . Voeg de volgende regel toe aan de `viewDidLoad` methode om ervoor te zorgen dat de land instellingen die momenteel zijn geselecteerd, worden weer gegeven:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Wijzig vervolgens in uw `subscribe` methode de aanroep naar de `storeCategoriesAndSubscribe` volgende code:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Ten slotte moet u de- `didRegisterForRemoteNotificationsWithDeviceToken` methode bijwerken in uw AppDelegate. m, zodat u uw registratie correct kunt vernieuwen wanneer uw app wordt gestart. Wijzig de aanroep van de `subscribe` meldings methode met de volgende code:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>Beschrijving Gelokaliseerde sjabloon meldingen verzenden vanuit een .NET-console-app

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>Beschrijving Gelokaliseerde sjabloon meldingen verzenden van het apparaat

Als u geen toegang hebt tot Visual Studio of als u alleen de gelokaliseerde sjabloon meldingen rechtstreeks vanuit de app op het apparaat wilt verzenden. U kunt de gelokaliseerde sjabloon parameters toevoegen aan de `SendNotificationRESTAPI` methode die u in de vorige zelf studie hebt gedefinieerd.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u gelokaliseerde meldingen naar iOS-apparaten verzonden. Ga verder met de volgende zelf studie voor meer informatie over het pushen van meldingen naar specifieke gebruikers van iOS-apps:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Notification Hubs gebruiken om belangrijk nieuws te verzenden]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/reference/dn223264(v=azure.100)