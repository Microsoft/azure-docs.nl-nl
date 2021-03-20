---
title: De huidige gebruiker registreren voor push meldingen met behulp van Web-API | Microsoft Docs
description: Meer informatie over het aanvragen van een push melding in een iOS-app met Azure Notification Hubs wanneer de registratie wordt uitgevoerd door de ASP.NET-Web-API.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0819f5196fffca25a840dc16d1df04cdd0a55029
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86223309"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>De huidige gebruiker registreren voor push meldingen met behulp van ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Overzicht

In dit onderwerp wordt beschreven hoe u registratie van push meldingen met Azure Notification Hubs aanvraagt wanneer de registratie wordt uitgevoerd door de ASP.NET-Web-API. In dit onderwerp wordt de zelf studie uitgebreid [met Notification hubs]. U moet de vereiste stappen in deze zelf studie al hebben uitgevoerd om de geverifieerde mobiele service te maken. Zie gebruikers op de [hoogte stellen van Notification hubs]voor meer informatie over het scenario gebruikers waarschuwen.

## <a name="update-your-app"></a>Uw app bijwerken

1. Voeg in uw MainStoryboard_iPhone. Story Board de volgende onderdelen uit de object bibliotheek toe:

   * **Label**: ' pushen naar gebruiker met Notification hubs '
   * **Label**: "InstallationId"
   * **Label**: ' gebruiker '
   * **Tekst veld**: gebruiker
   * **Label**: "wacht woord"
   * **Tekst veld**: "wacht woord"
   * **Knop**: aanmelden

     Op dit moment ziet uw Story Board er als volgt uit:

     ![Scherm afbeelding van de app MainStoryboard_iPhone. Story Board met de onderdelen die zijn toegevoegd.][0]

2. Maak in de assistent-editor uitvoer mogelijkheden voor alle switching-besturings elementen en aanroepen, Verbind de tekst velden met de weer gave-controller (gemachtigde) en maak een **actie** voor de knop **Aanmelden** .

    ![Scherm afbeelding van de assistent-editor in de app MainStoryboard_iPhone. Story Board][1]

    Uw BreakingNewsViewController. h-bestand moet nu de volgende code bevatten:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Maak een klasse `DeviceInfo` met de naam en kopieer de volgende code in het gedeelte interface van het bestand DeviceInfo. h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Kopieer de volgende code in de sectie implementatie van het bestand DeviceInfo. m:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. Voeg in PushToUserAppDelegate. h de volgende eigenschap Singleton toe:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. `didFinishLaunchingWithOptions`Voeg de volgende code toe aan de methode in PushToUserAppDelegate. m:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    De eerste regel initialiseert de `DeviceInfo` Singleton. De tweede regel start de registratie voor push meldingen, die al aanwezig is als u de zelf studie [aan de slag met Notification hubs] al hebt voltooid.
7. In PushToUserAppDelegate. m implementeert u de methode `didRegisterForRemoteNotificationsWithDeviceToken` in uw AppDelegate en voegt u de volgende code toe:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Hiermee stelt u het apparaat-token in voor de aanvraag.

   > [!NOTE]
   > Op dit moment mogen er in deze methode geen andere code worden gebruikt. Als u al een aanroep hebt van de `registerNativeWithDeviceToken` methode die is toegevoegd toen u de zelf studie [Push meldingen verzenden naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md) , moet u een opmerking of de aanroep verwijderen.

8. Voeg in het `PushToUserAppDelegate.m` bestand de volgende handler-methode toe:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Met deze methode wordt een waarschuwing weer gegeven in de gebruikers interface wanneer uw app meldingen ontvangt terwijl deze wordt uitgevoerd.

9. Open het `PushToUserViewController.m` bestand en retour neer het toetsen bord in de volgende implementatie:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. In de `viewDidLoad` methode in het `PushToUserViewController.m` bestand initialiseert u het `installationId` label als volgt:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Voeg de volgende eigenschappen toe aan de interface in `PushToUserViewController.m` :

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Voeg vervolgens de volgende implementatie toe:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Kopieer de volgende code naar de `login` handler-methode die is gemaakt door Xcode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Met deze methode wordt zowel een installatie-ID als een kanaal voor push meldingen opgehaald en verzonden, samen met het apparaattype, naar de geverifieerde Web API-methode waarmee een registratie in Notification Hubs wordt gemaakt. Deze web-API is gedefinieerd in [gebruikers met Notification hubs op de hoogte stellen].

Nu de client-app is bijgewerkt, keert u terug naar de gebruikers op de [hoogte van Notification hubs] en werkt u de mobiele service bij om meldingen te verzenden met behulp van Notification hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Gebruikers op de hoogte stellen met Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Aan de slag met Notification Hubs]: ios-sdk-get-started.md
