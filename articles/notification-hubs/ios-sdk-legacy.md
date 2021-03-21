---
title: Pushmeldingen verzenden naar iOS met Azure Notification Hubs en versie 2.0.4 van de iOS-SDK
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en de Apple-service voor pushmeldingen verzendt naar iOS-apparaten (versie 2.0.4).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: a1017f7c7aa0ede9e3c91acd3dba510618e15fb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100627935"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-legacy-api"></a>Zelf studie: Push meldingen verzenden naar iOS-apps met behulp van Azure Notification Hubs (verouderde API)

Deze zelf studie laat zien hoe u met Azure Notification Hubs push meldingen naar een iOS-toepassing kunt verzenden met behulp van de Azure Notification Hubs SDK voor Apple legacy Api's.

Deze zelfstudie bestaat uit de volgende stappen:

- Maak een voorbeeld iOS-app.
- Verbind de iOS-app met Azure Notification Hubs.
- Verzend testpushmeldingen.
- Controleer of uw app meldingen ontvangt.

U kunt de volledige code voor deze zelfstudie downloaden van [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppLegacyObjC).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd en een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.
- Een iPhone of iPad waarop iOS versie 10 of hoger wordt uitgevoerd.
- Uw fysieke apparaat dat is geregistreerd in de [Apple Portal](https://developer.apple.com/) en is gekoppeld aan uw certificaat.

Zorg ervoor dat u voordat u verder gaat, de vorige zelfstudie over hoe u aan de slag gaat met [Azure Notification Hubs voor iOS-apps](ios-sdk-get-started.md) hebt doorlopen, zodat u weet hoe u pushreferenties instelt en configureert in uw meldingenhub. Zelfs als u geen ervaring hebt met iOS-ontwikkeling, zou u deze stappen moeten kunnen volgen.

> [!NOTE]
> Vanwege configuratievereisten voor pushmeldingen moet u pushmeldingen op een fysiek iOS-apparaat (iPhone of iPad) implementeren en testen in plaats van in de iOS-emulator.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Uw iOS-app verbinden met Notification Hubs

1. Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View Application** (Toepassing met één weergave).

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Sjabloon selecteren":::

2. Bij het instellen van de opties voor het nieuwe project moet u dezelfde **productnaam** en **organisatie-id** gebruiken als bij het instellen van de bundel-id in de Apple Developer-portal.

3. Selecteer onder Projectnavigator de naam van uw project onder **Doelen** en ga vervolgens naar het tabblad **Ondertekening en mogelijkheden**. Zorg ervoor dat u het juiste **team** voor uw Apple Developer-account selecteert. XCode moet automatisch het profiel voor inrichting openen dat u eerder op basis van uw bundel-id hebt gemaakt.

   Als u het nieuwe profiel voor inrichting dat u hebt gemaakt in Xcode niet ziet, vernieuwt u de profielen voor uw identiteit voor ondertekening. Klik op **Xcode** in de menubalk, klik op **Preferences** (Voorkeuren), klik op het tabblad **Account** en klik op de knop **View Details** (Details weergeven), klik op uw identiteit voor ondertekening en klik vervolgens op de knop voor vernieuwen in de rechterbenedenhoek.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Details weergeven":::

4. Selecteer op het tabblad **Ondertekenen en mogelijkheden** de optie **+ Mogelijkheid**. Dubbelklik op **Pushmeldingen** om deze optie in te schakelen.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Mogelijkheid":::

5. Voeg de Azure Notification Hubs SDK-modules toe.

   U kunt de Azure Notification Hubs-SDK integreren in uw app met behulp van [Cocoapods](https://cocoapods.org/) of door de binaire bestanden handmatig toe te voegen aan uw project.

   - Integratie via Cocoapods: Voeg de volgende afhankelijkheden toe aan uw podfile om de Azure Notification Hubs-SDK op te nemen in uw app:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Voer pod install uit om uw zojuist gedefinieerde pod te installeren en uw .xcworkspace te openen.

         Als er tijdens het uitvoeren van de pod-installatie een fout optreedt, zoals **Kan geen specificatie vinden voor AzureNotificationHubs-iOS-** , moet u `pod repo update` uitvoeren om de nieuwste pods uit de Cocoapods-opslagplaats op te halen. Voer daarna de pod-installatie uit.

   - Integratie via Carthage: Voeg de volgende afhankelijkheden toe aan uw Cartfile om de Azure Notification Hubs-SDK in uw app op te nemen:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Werk vervolgens de build-afhankelijkheden bij:

      ```shell
      $ carthage update
      ```

      Raadpleeg de [Carthage-opslagplaats in GitHub](https://github.com/Carthage/Carthage) voor meer informatie over het gebruik van Carthage.

   - Integratie door de binaire bestanden naar uw project te kopiëren: U kunt de integratie uitvoeren door de binaire bestanden in uw project te kopiëren. Dit doet u als volgt:

        - Download het [Azure Notification Hubs SDK-framework](https://github.com/Azure/azure-notificationhubs-android/releases) als een zip-bestand wordt aangeboden en pak het bestand uit.

        - Klik met de rechtermuisknop op uw project in Xcode en klik op de optie **Add Files to** (Bestanden toevoegen aan) om de map **WindowsAzureMessaging.framework** aan uw Xcode-project toe te voegen. Selecteer **Options** (Opties), zorg ervoor dat **Copy items if needed** (Copy items if needed) is geselecteerd en klik op **Add** (Toevoegen).

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Framework toevoegen":::

6. Voeg de gegevens voor het maken van verbinding met Azure Notification Hubs toe in de desbetreffende `<string></string>` sectie.  Vervang de tijdelijke aanduidingen voor letterlijke tekenreeks `--HUB-NAME--` en `--CONNECTION-STRING--` en vervang respectievelijk de naam van de hub en de **DefaultListenSharedAccessSignature**, die u eerder hebt verkregen via de portal:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

7. Open uw projectbestand **AppDelegate.h** en vervang de inhoud door de volgende code:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,   UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end
    ```

8. Open het projectbestand **AppDelegate.m** en voeg de volgende `import` instructies toe:

    ```objc
    #import "NotificationDetailViewController.h"
    ```

12. Voeg daarnaast in uw **AppDelegate.m**-bestand de volgende regel code toe aan de methode `didFinishLaunchingWithOptions` op basis van uw versie van iOS. Deze code registreert uw apparaatingang met APNs:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Vervang in hetzelfde **AppDelegate.m**-bestand de code na `didFinishLaunchingWithOptions` door de volgende code:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];
        
        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];
        
        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }
        
        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");
        
        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).
        
        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];
        
        // Use 'options' to specify which default behaviors to enable.
        // - UNNotificationPresentationOptionsBadge: Apply the notification's badge value to the app’s icon.
        // - UNNotificationPresentationOptionList: Show in the Notification Center
        // - UNNotificationPresentationOptionsSound: Play the sound associated with the notification.
        //
        completionHandler(UNNotificationPresentationOptionsBadge | UNNotificationPresentationOptionsSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");
        
        // The system calls this delegate method when the user taps or responds to the system notification.
        
        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];
        
        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];
        
        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        
        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }

            if (granted) {
                NSLog(@"Authorization granted");
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }

        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];
        
        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Deze code maakt verbinding met de Notification Hub via de verbindingsgegevens die u hebt opgegeven in **Constants.h**. Er wordt vervolgens een apparaattoken aan de Notification Hub toegekend, zodat de hub meldingen kan verzenden.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController-headerbestand maken

1. Voeg, net als in de vorige instructies, een ander header-bestand toe met de naam **NamedNotificationDetailViewController.h**. Vervang de inhoud van het nieuwe headerbestand door de volgende code:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Voeg het implementatiebestand **NotificationDetailViewController.m** toe. Vervang de inhoud van het bestand door de volgende code, waarmee de UIViewController-methoden worden geïmplementeerd:

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
   }

   - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
        [super viewDidLoad];
        
        NSString *title = nil;
        NSString *body = nil;
        
        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }
        
        if (title == nil) {
            title = @"<unset>";
        }
        
        if (body == nil) {
            body = @"<unset>";
        }
        
        self.titleLabel.text = title;
        self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. Open het projectbestand **ViewController.h** en voeg de volgende `import` instructies toe:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. Voeg daarnaast in **ViewController.h** de volgende eigenschapsdeclaraties toe na de declaratie `@interface`:

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   @property (strong, nonatomic) IBOutlet UIButton *registerButton;
   @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
   ```

3. Vervang de inhoud van het implementatiebestand **ViewController.m** van het project door de volgende code:

   ```objc
   #import "ViewController.h"
   #import "AppDelegate.h"

    static NSString *const kNHUserDefaultTags = @"notification_tags";

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:kNHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:kNHUserDefaultTags];
        
        // Delegate processing the register action to the app delegate.
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. Ontwikkel en voer de app op uw apparaat uit om te controleren of er geen fouten zijn.

## <a name="send-test-push-notifications"></a>Testpushmeldingen verzenden

U kunt ontvangst van meldingen in uw app testen met de optie **Test verzenden** in [Azure-portal](https://portal.azure.com/). Er wordt dan een pushmelding als test naar uw apparaat verzonden.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Verzenden testen":::

Pushmeldingen worden gewoonlijk in een back-endservice zoals Mobile Apps of ASP.NET verzonden met een compatibele bibliotheek. U kunt de REST API ook rechtstreeks gebruiken om meldingsberichten te verzenden als er geen bibliotheek beschikbaar is voor uw back-end.

Hier volgt een lijst met andere zelfstudies die u mogelijk kunt bekijken voor het verzenden van meldingen:

- Azure Mobile Apps: Zie [Pushmeldingen toevoegen aan uw iOS-app](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push) voor een voorbeeld van hoe u meldingen verzendt vanuit een back-end van Mobile Apps die is geïntegreerd met Notification Hubs.
- ASP.NET: [Gebruik Notification Hubs om pushmeldingen naar gebruikers te verzenden](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK: zie [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (Notification Hubs gebruiken vanuit Java) voor het verzenden van meldingen vanuit Java. Dit is getest in Eclipse voor Android-ontwikkeling.
- PHP: zie [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (Notification Hubs gebruiken vanuit PHP).

## <a name="verify-that-your-app-receives-push-notifications"></a>Controleren of uw app pushmeldingen ontvangt

Als u pushmeldingen op iOS wilt testen, moet u de app implementeren op een fysiek iOS-apparaat. U kunt geen Apple pushmeldingen verzenden via de iOS-simulator.

1. Voer de app uit en controleer of de registratie is gelukt en druk vervolgens op **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Registreren":::

2. Verzend vervolgens als test een pushmelding vanuit [Azure Portal](https://portal.azure.com/), zoals in de vorige sectie wordt beschreven.

3. De pushmelding wordt verzonden naar alle apparaten die zijn geregistreerd voor het ontvangen van meldingen van de specifieke meldingenhub.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Test verzenden":::

## <a name="next-steps"></a>Volgende stappen

In dit eenvoudige voorbeeld zendt u pushmeldingen uit naar al uw geregistreerde iOS-apparaten. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke iOS-apparaten, gaat u verder met de volgende zelfstudie:

[Zelfstudie: Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [REST API's voor Notification Hubs](/rest/api/notificationhubs/)
- [Notification Hubs SDK voor back-endbewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK op GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registreren bij back-end van toepassing](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
- [Werken met tags](notification-hubs-tags-segment-push-message.md)
- [Werken met aangepaste sjablonen](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus-toegangsbeheer met handtekeningen voor gedeelde toegang](../service-bus-messaging/service-bus-sas.md)
- [Programmatisch SAS-tokens genereren](/rest/api/eventhub/generate-sas-token)
- [Apple-beveiliging: algemene cryptografie](https://developer.apple.com/security/)
- [UNIX Epoche-tijd](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)