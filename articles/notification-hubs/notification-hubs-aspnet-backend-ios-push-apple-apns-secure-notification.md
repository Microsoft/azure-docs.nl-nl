---
title: Azure Notification Hubs Secure push voor iOS
description: Meer informatie over het verzenden van beveiligde push meldingen naar een iOS-app vanuit Azure. Code voorbeelden geschreven in doel-C en C#.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090343"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs Secure push

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Overzicht

Met ondersteuning voor push meldingen in Microsoft Azure kunt u toegang krijgen tot een gebruiks vriendelijke, geschaalde push-infra structuur met meerdere platforms, waardoor de implementatie van push meldingen voor zowel consumenten als bedrijfs toepassingen voor mobiele platforms aanzienlijk wordt vereenvoudigd.

Vanwege regelgeving of beveiligings beperkingen kan het gebeuren dat een toepassing iets wil toevoegen in de melding die niet kan worden verzonden via de standaard infra structuur voor push meldingen. In deze zelf studie wordt beschreven hoe u dezelfde ervaring kunt krijgen door gevoelige informatie te verzenden via een beveiligde, geverifieerde verbinding tussen het client apparaat en de back-end van de app.

Op hoog niveau is de stroom als volgt:

1. De back-end van de app:
   * Slaat een beveiligde Payload op in de back-enddatabase.
   * Hiermee wordt de ID van deze melding naar het apparaat verzonden (er worden geen beveiligde gegevens verzonden).
2. De app op het apparaat wanneer de melding wordt ontvangen:
   * Het apparaat neemt contact op met de back-end die de beveiligde Payload aanvraagt.
   * De app kan de payload weer geven als een melding op het apparaat.

Het is belang rijk te weten dat in de voor gaande stroom (en in deze zelf studie) wordt aangenomen dat het apparaat een verificatie token opslaat in lokale opslag nadat de gebruiker zich heeft aangemeld. Dit garandeert een naadloze ervaring, omdat het apparaat de beveiligde payload van de melding kan ophalen met dit token. Als uw toepassing geen verificatie tokens opslaat op het apparaat of als deze tokens verlopen zijn, moet de app bij het ontvangen van de melding een algemene melding weer geven waarin de gebruiker wordt gevraagd de app te starten. De app verifieert vervolgens de gebruiker en toont de meldings lading.

In deze veilige push zelf studie wordt uitgelegd hoe u een push melding veilig verzendt. De zelf studie bouwt voort op de zelf studie [gebruikers melden](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , daarom moet u eerst de stappen in deze zelf studie volt ooien.

> [!NOTE]
> In deze zelf studie wordt ervan uitgegaan dat u uw notification hub hebt gemaakt en geconfigureerd, zoals wordt beschreven in [Push meldingen verzenden naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Het iOS-project wijzigen

Nu u de back-end van uw app hebt gewijzigd om alleen de id van een melding te verzenden, moet u uw iOS *-* app wijzigen zodat deze melding wordt afgehandeld en de back-end terugbellen om het beveiligde bericht weer te geven.

Om dit doel te verkrijgen, moeten we de logica schrijven om de beveiligde inhoud op te halen uit de back-end van de app.

1. `AppDelegate.m`Zorg ervoor dat de app registreert voor meldingen op de achtergrond zodat deze de meldings-id verwerkt die van de back-end wordt verzonden. Voeg de `UIRemoteNotificationTypeNewsstandContentAvailability` optie toe aan didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. In de `AppDelegate.m` sectie een implementatie toevoegen bovenaan met de volgende verklaring:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Voeg vervolgens in de sectie implementatie de volgende code toe en vervang de tijdelijke aanduiding `{back-end endpoint}` door het eind punt voor uw back-end die u eerder hebt verkregen:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Met deze methode wordt de back-end van uw app aangeroepen om de meldings inhoud op te halen met de referenties die zijn opgeslagen in de gedeelde voor keuren.

4. De inkomende melding wordt nu verwerkt en de bovenstaande methode wordt gebruikt om de inhoud op te halen die moet worden weer gegeven. Zorg er eerst voor dat uw iOS-app op de achtergrond wordt uitgevoerd wanneer er een push melding wordt ontvangen. In **Xcode** selecteert u uw app-project in het linkerdeel venster en klikt u vervolgens op het hoofd doel van de app in het gedeelte **doelen** van het middelste deel venster.
5. Klik vervolgens op het tabblad **mogelijkheden** boven aan het middelste deel venster en schakel het selectie vakje **externe meldingen** in.

    ![Scherm opname van XCode, waarbij het app-project is geselecteerd en het tabblad mogelijkheden geopend. De externe meldingen selectie vakje is ingeschakeld.][IOS1]

6. In `AppDelegate.m` Voeg de volgende methode toe voor het verwerken van push meldingen:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Houd er rekening mee dat het de voor keur heeft om de gevallen van ontbrekende verificatie-header-eigenschap of weigering door de back-end af te handelen. De specifieke afhandeling van deze cases is vooral afhankelijk van uw doel gebruikers ervaring. Een optie is om een melding weer te geven met een algemene prompt voor de gebruiker om de daad werkelijke melding op te halen.

## <a name="run-the-application"></a>De toepassing uitvoeren

Ga als volgt te werk om de toepassing uit te voeren:

1. In XCode voert u de app uit op een fysiek iOS-apparaat (push meldingen werken niet in de simulator).
2. Voer een gebruikers naam en wacht woord in de gebruikers interface van de iOS-app in. Dit kan een wille keurige teken reeks zijn, maar ze moeten dezelfde waarde hebben.
3. Klik in de gebruikers interface van de iOS-app op **Aanmelden**. Klik vervolgens op **Push verzenden**. U ziet dat de beveiligde melding wordt weer gegeven in uw meldingen centrum.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
