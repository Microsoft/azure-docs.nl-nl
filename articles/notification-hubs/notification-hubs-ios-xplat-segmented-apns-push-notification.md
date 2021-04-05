---
title: Push meldingen verzenden naar specifieke iOS-apparaten met behulp van Azure Notification Hubs | Microsoft Docs
description: In deze zelf studie leert u hoe u Azure Notification Hubs kunt gebruiken om Push meldingen te verzenden naar specifieke iOS-apparaten.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 2cb979491e247a4d44b9ae9ae27c433fb3f436d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579224"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Zelf studie: Push meldingen verzenden naar specifieke iOS-apparaten met behulp van Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overzicht

In deze zelf studie leert u hoe u Azure Notification Hubs kunt gebruiken om de Nieuws meldingen te verzenden naar een iOS-app. Wanneer u klaar bent, kunt u zich registreren voor de nieuws categorieën waarin u geïnteresseerd bent en alleen push meldingen ontvangen voor die categorieën. Dit scenario is een algemeen patroon voor veel apps die meldingen moeten verzenden naar groepen gebruikers die eerder hebben aangegeven in bepaalde onderwerpen geïnteresseerd te zijn, zoals een RSS-lezer, apps voor muziekfans, enzovoort.

Broadcast-scenario's zijn mogelijk door een of meer *tags* (of labels) toe te voegen wanneer u een registratie maakt in Notifications Hub. Wanneer meldingen naar een tag worden verzonden, ontvangen apparaten die zijn geregistreerd voor de tag, de melding. Omdat tags niet meer dan tekenreeksen zijn, hoeven ze niet vooraf te worden opgesteld. Zie [Notification Hubs-routering en tagexpressies](notification-hubs-tags-segment-push-message.md) voor meer informatie over tags.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een categorie selectie toevoegen aan de app
> * Getagde meldingen verzenden
> * Meldingen verzenden vanaf het apparaat
> * De app uitvoeren en meldingen genereren

## <a name="prerequisites"></a>Vereisten

Dit onderwerp is gebaseerd op de app die u in de [zelf studie hebt gemaakt: Push meldingen naar IOS-apps met behulp van Azure notification hubs][get-started]. Voordat u met deze zelf studie begint, moet u al [zelf zelf studie hebben voltooid: Push meldingen naar IOS-apps met behulp van Azure notification hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Categorieselectie toevoegen aan de app

De eerste stap is het toevoegen van de UI-elementen aan uw bestaande Story Board waarmee de gebruiker categorieën kan selecteren die u wilt registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, wordt er een apparaatregistratie gemaakt in uw meldingshub, met de geselecteerde categorieën als tags.

1. Voeg in uw **MainStoryboard_iPhone. Story Board** de volgende onderdelen uit de object bibliotheek toe:

   * Een label met de tekst ' laatste nieuws ',
   * Labels met categorie teksten "wereld", "politiek", "zakelijk", "technologie", "Science", "Sporten",
   * Zes switches, één per categorie, stellen elke switch **status** standaard **uit** .
   * Een knop met het label abonneren

     Het Story Board moet er als volgt uitzien:

     ![Opbouw functie voor Xcode-interface][3]

2. Maak in de assistent-editor uitschakelingen voor alle switches en noem ze "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Een actie maken voor de knop met de naam `subscribe` ; de `ViewController.h` volgende code moet worden opgenomen:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Maak een nieuwe **cacao aanraak klasse** met de naam `Notifications` . Kopieer de volgende code in de sectie interface van de bestands meldingen. h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Voeg de volgende import instructie toe aan meldingen. m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Kopieer de volgende code in de sectie implementatie van de bestands meldingen. m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Deze klasse gebruikt lokale opslag om de Nieuws Categorieën op te slaan en op te halen die dit apparaat ontvangt. Daarnaast bevat het een methode om te registreren voor deze categorieën met behulp van een [sjabloon](notification-hubs-templates-cross-platform-push-messages.md) registratie.

7. Voeg in het `AppDelegate.h` bestand een instructie import toe voor `Notifications.h` en voeg een eigenschap toe voor een exemplaar van de `Notifications` klasse:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. Voeg in de `didFinishLaunchingWithOptions` methode in de `AppDelegate.m` code toe om het meldings exemplaar aan het begin van de methode te initialiseren.  
    `HUBNAME` en `HUBLISTENACCESS` (gedefinieerd in `hubinfo.h` ) moeten al de `<hub name>` en `<connection string with listen access>` tijdelijke aanduidingen vervangen door de naam van de notification hub en de Connection String voor *DefaultListenSharedAccessSignature* die u eerder hebt verkregen

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor listen-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

9. Vervang in de `didRegisterForRemoteNotificationsWithDeviceToken` methode in de `AppDelegate.m` code in de-methode door de volgende code om het apparaat-token door te geven aan de `notifications` klasse. De- `notifications` klasse voert de registratie uit voor meldingen met de categorieën. Als de gebruiker categorie selecties wijzigt, roept u de- `subscribeWithCategories` methode aan als reactie op de knop **Abonneren** om ze bij te werken.

    > [!NOTE]
    > Omdat het apparaat-token dat door de Apple Push Notification Service (APNS) is toegewezen, op elk gewenst moment kan worden gewijzigd, moet u zich regel matig registreren voor meldingen om meldings fouten te voor komen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u regelmatig uitvoert (meer dan één keer per dag), kunt u de registratie waarschijnlijk overslaan om bandbreedte te besparen als er minder dan een dag is verstreken sinds de vorige registratie.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Op dit moment mag er geen andere code in de- `didRegisterForRemoteNotificationsWithDeviceToken` methode staan.

10. De volgende methoden moeten al aanwezig zijn in `AppDelegate.m` de zelf studie [aan de slag met Notification hubs][get-started] . Als dat niet het geval is, voegt u deze toe.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Deze methode verwerkt meldingen die worden ontvangen wanneer de app wordt uitgevoerd door een eenvoudige **UIAlert** weer te geven.

11. `ViewController.m`Voeg in een `import` instructie toe voor `AppDelegate.h` en kopieer de volgende code in de door Xcode gegenereerde `subscribe` methode. Met deze code wordt de meldings registratie bijgewerkt voor het gebruik van de nieuwe categorie tags die de gebruiker heeft gekozen in de gebruikers interface.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Met deze methode maakt `NSMutableArray` u een van de categorieën en wordt de- `Notifications` klasse gebruikt voor het opslaan van de lijst in de lokale opslag en worden de bijbehorende tags geregistreerd bij de notification hub. Wanneer categorieën worden gewijzigd, wordt de registratie opnieuw gemaakt met de nieuwe categorieën.

12. Voeg in de `ViewController.m` volgende code toe aan de `viewDidLoad` methode om de gebruikers interface in te stellen op basis van de eerder opgeslagen categorieën.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Met de app kan nu een set categorieën worden opgeslagen in de lokale opslag ruimte van het apparaat die wordt gebruikt voor registratie bij de notification hub wanneer de app wordt gestart. De gebruiker kan de selectie van categorieën tijdens runtime wijzigen en op de `subscribe` methode klikken om de registratie voor het apparaat bij te werken. Vervolgens werkt u de app bij om de meldingen over de laatste keer rechtstreeks in de app te verzenden.

## <a name="optional-send-tagged-notifications"></a>Beschrijving Gelabelde meldingen verzenden

Als u geen toegang hebt tot Visual Studio, kunt u door gaan naar de volgende sectie en meldingen verzenden vanuit de app zelf. U kunt ook de juiste sjabloon melding van de [Azure Portal] verzenden met behulp van het tabblad fout opsporing voor uw notification hub.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>Beschrijving Meldingen verzenden vanaf het apparaat

Normaal gesp roken worden meldingen verzonden door een back-end-service, maar u kunt ook rechtstreeks vanuit de app belang rijke Nieuws meldingen verzenden. U kunt dit doen door de `SendNotificationRESTAPI` methode die u hebt gedefinieerd in de zelf studie [aan de slag met Notification hubs][get-started] bij te werken.

1. `ViewController.m`Werk de methode in bij, `SendNotificationRESTAPI` zodat deze een para meter voor de categorie label accepteert en de juiste [sjabloon](notification-hubs-templates-cross-platform-push-messages.md) melding verzendt.

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
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

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

2. Werk in de `ViewController.m` actie in, `Send Notification` zoals wordt weer gegeven in de volgende code. Zodat de meldingen via elke code afzonderlijk worden verzonden en naar meerdere platforms worden verzonden.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Bouw het project opnieuw op en zorg ervoor dat er geen compilatie fouten zijn.

## <a name="run-the-app-and-generate-notifications"></a>De app uitvoeren en meldingen genereren

1. Druk op de knop Uitvoeren om het project te bouwen en de app te starten. Selecteer enkele belang rijke nieuws opties om u te abonneren op en druk op de knop **Abonneren** . Er wordt een dialoog venster weer gegeven met de melding dat u bent geabonneerd op de meldingen.

    ![Voorbeeld melding op iOS][1]

    Wanneer u **op abonneren** klikt, worden de geselecteerde categorieën door de app omgezet in tags en wordt er een nieuwe apparaatregistratie voor de geselecteerde Tags in de notification hub.

2. Voer een bericht in dat moet worden verzonden als laatste nieuws en druk op de knop **melding verzenden** . U kunt ook de .NET-console-app uitvoeren om meldingen te genereren.

    ![Meldings voorkeuren in iOS wijzigen][2]

3. Elk apparaat dat is geabonneerd op het breken van nieuws, ontvangt de laatste nieuws meldingen die u zojuist hebt verzonden.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uitgezonden meldingen verzonden naar specifieke iOS-apparaten die zijn geregistreerd voor de categorieën. Ga verder met de volgende zelf studie voor meer informatie over het pushen van gelokaliseerde meldingen:

> [!div class="nextstepaction"]
>[Gelokaliseerde pushmeldingen verzenden](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/dn530749(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[get-started]: ios-sdk-get-started.md
[Azure-portal]: https://portal.azure.com
