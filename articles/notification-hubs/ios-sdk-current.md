---
title: Pushmeldingen verzenden naar iOS met Azure Notification Hubs en versie 3.0.0, preview 1, van de iOS-SDK
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en de Apple-service voor pushmeldingen verzendt naar iOS-apparaten (versie 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100627352"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Zelf studie: Push meldingen verzenden naar iOS-apps met behulp van Azure Notification Hubs SDK voor Apple

Deze zelf studie laat zien hoe u met Azure Notification Hubs push meldingen naar een iOS-toepassing kunt verzenden met behulp van de Azure Notification Hubs SDK voor Apple.

Deze zelfstudie bestaat uit de volgende stappen:

- Maak een voorbeeld iOS-app.
- Verbind de iOS-app met Azure Notification Hubs.
- Verzend testpushmeldingen.
- Controleer of uw app meldingen ontvangt.

U kunt de volledige code voor deze zelfstudie downloaden van [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

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

   - Integratie door de binaire bestanden naar uw project te kopiëren:

      U kunt de integratie uitvoeren door de binaire bestanden in uw project te kopiëren. Dit doet u als volgt:

        - Download het [Azure Notification Hubs SDK-framework](https://github.com/Azure/azure-notificationhubs-iOS/releases/) als een zip-bestand wordt aangeboden en pak het bestand uit.

        - Klik met de rechtermuisknop op uw project in Xcode en klik op de optie **Add Files to** (Bestanden toevoegen aan) om de map **WindowsAzureMessaging.framework** aan uw Xcode-project toe te voegen. Selecteer **Options** (Opties), zorg ervoor dat **Copy items if needed** (Copy items if needed) is geselecteerd en klik op **Add** (Toevoegen).

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Framework toevoegen":::

6. Voeg een bestand toe met de naam **DevSettings. plist** dat twee eigenschappen bevat, `CONNECTION_STRING` voor de Connection String aan de Azure notification hub en `HUB_NAME` voor de Azure notification hub.

7. Voeg de gegevens voor het maken van verbinding met Azure Notification Hubs toe in de desbetreffende `<string></string>` sectie.  Vervang de tijdelijke aanduidingen voor letterlijke tekenreeks `--HUB-NAME--` en `--CONNECTION-STRING--` en vervang respectievelijk de naam van de hub en de **DefaultListenSharedAccessSignature**, die u eerder hebt verkregen via de portal:

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

8. Vervang in hetzelfde **AppDelegate.m**-bestand de code na `didFinishLaunchingWithOptions` door de volgende code:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Deze code maakt verbinding met de notification hub met behulp van de verbindings gegevens die u hebt opgegeven in **DevSettings. plist**. Er wordt vervolgens een apparaattoken aan de Notification Hub toegekend, zodat de hub meldingen kan verzenden.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController-headerbestand maken

1. Net als bij de voor gaande instructies, voegt u nog een veldnamen bestand toe met de naam **SetupViewController. h**. Vervang de inhoud van het nieuwe headerbestand door de volgende code:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Voeg het implementatie bestand **SetupViewController. m** toe. Vervang de inhoud van het bestand door de volgende code, waarmee de UIViewController-methoden worden geïmplementeerd:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Ontwikkel en voer de app op uw apparaat uit om te controleren of er geen fouten zijn.

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