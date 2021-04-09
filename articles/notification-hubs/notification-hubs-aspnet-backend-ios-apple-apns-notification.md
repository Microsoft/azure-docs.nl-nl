---
title: Push meldingen verzenden naar specifieke gebruikers met behulp van Azure Notification Hubs | Microsoft Docs
description: Meer informatie over het verzenden van push meldingen naar specifieke iOS-gebruikers met behulp van Azure Notification Hubs.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016936"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Zelf studie: Push meldingen verzenden naar specifieke gebruikers met behulp van Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

In deze zelfstudie wordt uitgelegd hoe u met Azure Notification Hubs pushmeldingen kunt verzenden naar een specifieke app-gebruiker op een specifiek apparaat. Een ASP.NET WebAPI-back-end wordt gebruikt voor het verifiëren van clients en het genereren van meldingen, zoals wordt weer gegeven in het onderwerp informatie over het [registreren van de back-end van uw app](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Het WebAPI-project maken
> * Clients verifiëren bij de WebAPI-back-end
> * Registreren voor meldingen met behulp van de WebAPI-back-end
> * Meldingen verzenden vanuit de WebAPI-back-end
> * De nieuwe WebAPI-back-end publiceren
> * Uw iOS-app wijzigen
> * De toepassing testen

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u uw notification hub hebt gemaakt en geconfigureerd, zoals wordt beschreven in [Push meldingen verzenden naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md). Deze zelf studie is ook de vereiste voor de zelf studie voor [beveiligd pushen (Ios)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Als u Mobile Apps wilt gebruiken als uw back-end-service, raadpleegt u de [Mobile apps aan de slag met push](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Uw iOS-app wijzigen

1. Open de app voor het weer geven van één pagina die u hebt gemaakt in de zelf studie [Push meldingen verzenden naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md) .

   > [!NOTE]
   > In deze sectie wordt ervan uitgegaan dat uw project is geconfigureerd met een lege organisatie naam. Als dat niet het geval is, laten voorafgaan door u de naam van uw organisatie op alle klassen namen.

2. Voeg in het `Main.storyboard` bestand de onderdelen toe die in de scherm opname van de object bibliotheek worden weer gegeven.

    ![Story Board bewerken in de opbouw functie voor Xcode-interfaces][1]

   * **Gebruikers naam**: een UITextField met plaatsaanduidingstekst, *Voer gebruikers naam* in, direct onder het label resultaten verzenden en beperkt tot de linker-en rechter marge en onder het label resultaten van verzenden.
   * **Wacht woord**: een UITextField met een tijdelijke tekst, voert u het *wacht woord* in direct onder het tekst veld van de gebruikers naam en beperkt tot de linker-en rechter marge en onder het tekst veld van de gebruikers naam. Controleer de optie voor **beveiligde tekst invoer** in kenmerk controle onder *retour sleutel*.
   * **Aanmelden**: een UIButton label direct onder het tekst veld voor het wacht woord en schakel de optie **ingeschakeld** in de kenmerkencontrole onder *besturings element-inhoud* uit.
   * **Wns**: label en schakel over om het verzenden van de meldings service voor meldingen in te scha kelen als deze is ingesteld op de hub. Raadpleeg de zelf studie aan de slag met [Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
   * **GCM**: label en schakel over om het verzenden van de melding naar Google Cloud Messaging in te scha kelen als deze is ingesteld op de hub. Zie de zelf studie aan de slag met [Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS**: label en schakel over om het verzenden van de melding naar de Apple platform Notification Service mogelijk te maken.
   * **Gebruikers naam van geadresseerde: een** UITextField met plaatsaanduidingstekst, label van de *gebruikers naam* van de geadresseerde, direct onder het label GCM en beperkt tot de linker-en rechter marge en onder het GCM-label.

     Sommige onderdelen zijn toegevoegd aan de zelf studie [Push meldingen verzenden naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md) .

3.  Sleep de elementen in de weer gave naar `ViewController.h` en voeg deze nieuwe mogelijkheden toe met CTRL:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. `ViewController.h`Voeg in het volgende toe `#define` na uw import-instructies. Vervang de `<Your backend endpoint>` tijdelijke aanduiding door de doel-URL die u hebt gebruikt voor het implementeren van de back-end van uw app in de vorige sectie. Bijvoorbeeld `http://your_backend.azurewebsites.net`:

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. Maak in uw project een nieuwe cacao Touch-klasse `RegisterClient` met de naam naar de interface met de back-end van de ASP.net die u hebt gemaakt. De klasse van overnemen van maken `NSObject` . Voeg vervolgens de volgende code toe in de `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. Werk in de `RegisterClient.m` sectie het `@interface` volgende bij:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Vervang de `@implementation` sectie in RegisterClient. m door de volgende code:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Met deze code wordt de logica geïmplementeerd die wordt beschreven in het artikel richt lijnen voor het [registreren van de back-end van uw app](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) met NSURLSESSION om rest-aanroepen naar de back-end van uw app uit te voeren en NSUserDefaults de registratie die door de notification hub wordt geretourneerd, lokaal op

    Voor deze klasse moet de eigenschap `authorizationHeader` worden ingesteld om correct te kunnen werken. Deze eigenschap wordt `ViewController` na de aanmelding ingesteld door de klasse.

8. `ViewController.h`Voeg in een `#import` instructie toe voor `RegisterClient.h` . Voeg vervolgens een declaratie voor het token apparaat en de verwijzing naar een `RegisterClient` exemplaar in de `@interface` sectie toe:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. In view controller. m voegt u een declaratie van een persoonlijke methode toe aan de `@interface` sectie:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Het volgende code fragment is geen beveiligd verificatie schema. u moet de implementatie van de    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` met uw specifieke verificatie mechanisme vervangen door het genereren van een verificatie token dat wordt gebruikt door de client klasse registreren, bijvoorbeeld OAuth, Active Directory.

10. Voeg vervolgens in de `@implementation` sectie van `ViewController.m` de volgende code toe, waarmee de implementatie wordt toegevoegd voor het instellen van het apparaat-token en de verificatie-header.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    U ziet hoe het instellen van het apparaat-token de knop **Aanmelden** mogelijk maakt. Als onderdeel van de aanmeldings actie registreert de controller de weer gave voor push meldingen met de back-end van de app. U wilt niet dat de **aanmeldings** actie toegankelijk is totdat het token van het apparaat correct is ingesteld. U kunt de aanmelding ontkoppelen van de push registratie, zolang het voormalige gebeurt vóór de laatste.

11. Gebruik in view controller. m de volgende fragmenten om de actie methode voor de **aanmeldings** knop en een methode voor het verzenden van het meldings bericht te implementeren met behulp van de ASP.net-back-end.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Werk de actie voor de knop **melding verzenden** bij om de ASP.net-back-end te gebruiken en te verzenden naar een PNS die is ingeschakeld door een switch.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. Voeg in de `ViewDidLoad` functie het volgende toe om het exemplaar te instantiëren `RegisterClient` en de gemachtigde in te stellen voor uw tekst velden.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Verwijder nu `AppDelegate.m` alle inhoud van de-methode `application:didRegisterForPushNotificationWithDeviceToken:` en vervang deze door de volgende (om ervoor te zorgen dat de weergave controller het meest recente apparaatbeleid bevat dat is opgehaald uit de APNs):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Ten slotte moet `AppDelegate.m` u de volgende methode hebben:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>De toepassing testen

1. In XCode voert u de app uit op een fysiek iOS-apparaat (push meldingen werken niet in de simulator).
2. Voer in de gebruikers interface van de iOS-app dezelfde waarde in voor de gebruikers naam en het wacht woord. Klik vervolgens op **Aanmelden**.

    ![iOS-test toepassing][2]

3. Er wordt een pop-upvenster weer gegeven waarin u wordt geïnformeerd over het slagen van de registratie. Klik op **OK**.

    ![iOS-test melding weer gegeven][3]

4. Voer in het tekst veld **ontvanger gebruikers* naam de code in die wordt gebruikt voor de registratie van een ander apparaat.
5. Voer een meldings bericht in en klik op **melding verzenden**. Alleen apparaten met een registratie met het label gebruikers naam ontvanger ontvangen het meldings bericht. Deze wordt alleen naar deze gebruikers verzonden.

    ![melding over gelabelde iOS-test][4]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u pushmeldingen kunt verzenden naar specifieke gebruikers door een tag te koppelen aan hun registraties. Als u wilt weten hoe u locatiegebaseerde pushmeldingen kunt verzenden, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Locatiegebaseerde pushmeldingen verzenden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
