---
title: Azure Notification Hubs Rich push
description: Meer informatie over het verzenden van uitgebreide push meldingen naar een iOS-app vanuit Azure. Code voorbeelden geschreven in doel-C en C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090360"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs Rich push

## <a name="overview"></a>Overzicht

Als u gebruikers wilt voorzien van een directe inhoud, kan een toepassing niet alleen in tekst zonder opmaak worden gepusht. Deze meldingen verhogen gebruikers interacties en presen teren inhoud zoals Url's, geluiden, afbeeldingen/Coupons en meer. Deze zelf studie bouwt voort op de zelf studie [gebruikers melden](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) en laat zien hoe u push meldingen verzendt die payloads bevatten (bijvoorbeeld afbeeldingen).

Deze zelf studie is compatibel met iOS 7 en 8.

  ![Drie scherm afbeeldingen: een app-scherm met de knop push verzenden, een start scherm op een apparaat en een Windows-logo met een knop terug.][IOS1]

Op hoog niveau:

1. De app-back-end:
   * Hiermee wordt de uitgebreide nettolading (in dit geval de afbeelding) opgeslagen in de back-end-data base/lokale opslag.
   * Hiermee wordt de ID van deze uitgebreide melding naar het apparaat verzonden.
2. App op het apparaat:
   * Neem contact op met de back-end die de uitgebreide Payload aanvraagt met de ID die wordt ontvangen.
   * Stuurt gebruikers meldingen op het apparaat wanneer het ophalen van gegevens is voltooid en toont de payload onmiddellijk wanneer gebruikers tikken op meer informatie.

## <a name="webapi-project"></a>WebAPI-project

1. Open in Visual Studio het **project appbackend** -project dat u hebt gemaakt in de zelf studie [gebruikers melden](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Verkrijg een installatie kopie waarmee u gebruikers op de hoogte wilt stellen en plaats deze in een **IMG** -map in de projectmap.
3. Klik op **alle bestanden weer geven** in de Solution Explorer en klik met de rechter muisknop op de map die u **in project wilt invoegen**.
4. Selecteer de installatie kopie en wijzig de **actie samen stellen** in het venster **Eigenschappen** in **Inge sloten resource**.

    ![Scherm opname van Solution Explorer. Het afbeeldings bestand is geselecteerd en in het deel venster met eigenschappen is de Inge sloten resource vermeld als de actie maken.][IOS2]
5. `Notifications.cs`Voeg in de volgende `using` instructie toe:

    ```csharp
    using System.Reflection;
    ```

6. Vervang de `Notifications` klasse door de volgende code. Vervang de tijdelijke aanduidingen door uw aanmeldings gegevens voor de notification hub en de naam van het afbeeldings bestand:

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. In `NotificationsController.cs` moet u opnieuw definiëren `NotificationsController` met de volgende code. Hiermee wordt een eerste meldings-ID op de achtergrond verzonden naar het apparaat en kan de installatie kopie aan de client zijde worden opgehaald:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. Implementeer deze app nu opnieuw naar een Azure-website om deze toegankelijk te maken vanaf alle apparaten. Klik met de rechtermuisknop op het project **AppBackend** en selecteer **Publiceren**.
9. Selecteer de **Azure-website** als uw publicatie doel. Meld u aan met uw Azure-account en selecteer een bestaande of nieuwe website en noteer de eigenschap **doel-URL** op het tabblad **verbinding** . Verderop in deze zelf studie verwijzen we naar deze URL als *back-end-eind punt* . Selecteer **Publiceren**.

## <a name="modify-the-ios-project"></a>Het iOS-project wijzigen

Nu u de back-end van uw app hebt gewijzigd om alleen de *id* van een melding te verzenden, wijzigt u de IOS-app voor het verwerken van die id en haalt u het uitgebreide bericht op van uw back-end:

1. Open uw iOS-project en Schakel externe meldingen in door naar uw hoofd doel van de app in de sectie **doelen** te gaan.
2. Selecteer **mogelijkheden**, Schakel **achtergrond modi** in en schakel het selectie vakje **externe meldingen** in.

    ![Scherm afbeelding van het iOS-project waarin het scherm mogelijkheden wordt weer gegeven. De achtergrond modi zijn ingeschakeld en het selectie vakje externe meldingen is ingeschakeld.][IOS3]
3. Open `Main.storyboard` en zorg ervoor dat u over een weergave controller (Home view-controller in deze zelf studie) beschikt vanuit de zelf studie voor de [gebruiker melden](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
4. Voeg een **Navigatie controller** toe aan het Story Board en houd Control ingedrukt en sleep de controller van de start weergave om de **hoofd weergave** van de navigatie te maken. Zorg ervoor dat de controller van de **eerste weer gave** in de kenmerken controle alleen is geselecteerd voor de navigatie controller.
5. Een **weergave controller** toevoegen aan het Story Board en een **afbeeldings weergave** toevoegen. Dit is de pagina die gebruikers te zien krijgen wanneer ze kiezen om meer te weten te komen door te klikken op de melding. Het Story Board moet er als volgt uitzien:

    ![Scherm afbeelding van een Story Board. Er zijn drie app-schermen zichtbaar: een navigatie weergave, een start weergave en een afbeeldings weergave.][IOS4]
6. Klik op de **Start weergave controller** in het Story Board en zorg ervoor dat deze **HomeViewController** als de **aangepaste klasse** en het bijbehorende **Story Board-id** onder de identiteits controle.
7. Doe hetzelfde voor de afbeeldings weergave controller als **imageViewController**.
8. Maak vervolgens een nieuwe weergave controller klasse met de naam **imageViewController** om de gebruikers interface die u zojuist hebt gemaakt, te verwerken.
9. Voeg in **imageViewController. h** de volgende code toe aan de interface declaraties van de controller. Zorg ervoor dat u Control ingedrukt houden en slepen van de Story Board-afbeeldings weergave naar deze eigenschappen om de twee te koppelen:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. `imageViewController.m`Voeg in het volgende toe aan het einde van `viewDidload` :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. `AppDelegate.m`Importeer in de installatie kopie-controller die u hebt gemaakt:

    ```objc
    #import "imageViewController.h"
    ```

12. Voeg een interface sectie toe met de volgende verklaring:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. `AppDelegate`Zorg ervoor dat uw app wordt geregistreerd voor meldingen in `application: didFinishLaunchingWithOptions` :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Vervang de volgende implementatie door `application:didRegisterForRemoteNotificationsWithDeviceToken` om de gebruikers interface van het Story Board in te voeren in het account:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. Voeg vervolgens de volgende methoden toe om `AppDelegate.m` de installatie kopie van uw eind punt op te halen en een lokale melding te verzenden wanneer het ophalen is voltooid. Zorg ervoor dat u de tijdelijke aanduiding vervangt `{backend endpoint}` door het back-end-eind punt:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. Verwerk de vorige lokale melding door de afbeeldings weergave controller te openen in `AppDelegate.m` met de volgende methoden:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

1. In XCode voert u de app uit op een fysiek iOS-apparaat (push meldingen werken niet in de simulator).
2. Voer in de gebruikers interface van de iOS-app een gebruikers naam en wacht woord in met dezelfde waarde voor verificatie en klik op **Aanmelden**.
3. Klik op **Push verzenden** om een waarschuwing in de app weer te geven. Als u op **meer** klikt, wordt u naar de installatie kopie geleid die u in de back-end van uw app hebt gekozen.
4. U kunt ook op **Push verzenden** klikken en direct op de knop Start op het apparaat drukken. Over enkele ogen blikken ontvangt u een push melding. Als u erop tikt of op meer klikt, wordt u naar uw app en de inhoud van de uitgebreide afbeelding geleid.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
