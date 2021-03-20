---
title: Pushmeldingen naar Android sturen met Azure Notification Hubs en Firebase SDK versie 0.6 | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en Google Firebase Cloud Messaging pushmeldingen verzendt naar Android-apparaten (versie 0.6).
services: notification-hubs
documentationcenter: android
keywords: pushmeldingen,pushmelding,android-pushmelding, firebase cloud messaging
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c5485dacc4d9e3210ad69819caf4e36f96c626da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92428376"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-06"></a>Zelfstudie: Pushmeldingen verzenden naar Android-apparaten met Firebase SDK versie 0.6

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs en de Firebase Cloud Messaging (FCM) SDK versie 0.6 en pushmeldingen verzendt naar een Android-toepassing. In deze zelfstudie gaat u een lege Android-app maken die pushmeldingen ontvangt via Firebase Cloud Messaging (FCM).

U kunt de voltooide code voor deze zelfstudie downloaden [op GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een Android Studio-project maken.
> * Een Firebase-project maken dat Firebase Cloud Messaging ondersteunt.
> * Maak een hub.
> * Verbind uw app met de hub.
> * De app testen.

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/free/) voor meer informatie. 

U hebt ook de volgende items nodig: 

* De nieuwste versie van [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 of hoger voor Firebase Cloud Messaging
* Google Repository revisie 27 of hoger voor Firebase Cloud Messaging
* Google Play-Services 9.0.2 of hoger voor Firebase Cloud Messaging

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor Android-apps.

## <a name="create-an-android-studio-project"></a>Een Android Studio-project maken

1. Start Android Studio.
2. Selecteer **File**, wijs naar **New** en selecteer **New Project**. 
2. Selecteer **Empty Activity** op de pagina **Choose your project** en selecteer **Next**. 
3. Voer de volgende stappen uit op de pagina **Configure your project**: 
    1. Voer een naam in voor de toepassing.
    2. Geef een locatie op voor het opslaan van de projectbestanden. 
    3. Selecteer **Finish**. 

        ![Het project configureren](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Een Firebase-project maken dat FCM ondersteunt

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Een hub configureren

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Firebase Cloud Messaging-instellingen voor de hub configureren

1. Selecteer in het linkerdeelvenster onder **Instellingen** de optie **Google (GCM/FCM)** . 
2. Voer de **serversleutel** in voor het FCM-project dat u eerder hebt opgeslagen. 
3. Selecteer **Opslaan** op de werkbalk. 

    ![Azure Notification Hub - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. De Azure-portal geeft een bericht weer in de waarschuwingen dat de hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen. 

Uw hub is nu geconfigureerd om te werken met Firebase Cloud Messaging. U hebt ook de verbindingsreeksen die nodig zijn om meldingen naar een apparaat te verzenden en een app te registreren voor het ontvangen van meldingen.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Uw app verbinden met de Notification Hub

### <a name="add-google-play-services-to-the-project"></a>Google Play-services aan het project toevoegen

1. Selecteer in Android Studio in het menu de optie **Hulpprogramma’s**, en selecteer vervolgens **SDK Manager**. 
2. Selecteer de doelversie van de Android SDK die wordt gebruikt in het project. Selecteer vervolgens **Pakketdetails weergeven**. 

    ![Android SDK Manager - doelversie selecteren](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecteer **Google API’s** als dit nog niet is geïnstalleerd.

    ![Android SDK Manager - Google API’s geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Schakel over naar het tabblad **SDK-hulpprogramma's** . Als u Google Play Service nog niet hebt geïnstalleerd, selecteert u **Google Play Services** zoals wordt weergegeven in de onderstaande afbeelding. Selecteer vervolgens **Toepassen** om de installatie te starten. Noteer het SDK-pad om het in een later stadium te kunnen gebruiken.

    ![Android SDK Manager - Google Play Services geselecteerd](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Klik op **OK** zodra het dialoogvenster **Wijziging bevestigen** wordt weergegeven. De vereiste onderdelen worden geïnstalleerd met behulp van het installatieprogramma voor onderdelen. Selecteer **Voltooien** zodra de onderdelen zijn geïnstalleerd.
4. Selecteer **OK** om het dialoogvenster **Instellingen voor nieuwe projecten** te sluiten.  
1. Open het bestand AndroidManifest.xml en voeg de volgende code toe aan de tag *toepassing*.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs-bibliotheken toevoegen

1. Voeg in het bestand Build.Gradle voor de app de volgende regels toe in het gedeelte met afhankelijkheden.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Voeg de volgende opslagplaats toe na het gedeelte met afhankelijkheden.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Ondersteuning voor Google Firebase toevoegen

1. Voeg in het bestand Build.Gradle voor de app de volgende regels toe aan het gedeelte **afhankelijkheden** als die er nog niet staan. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Voeg de volgende invoegtoepassing toe aan het einde van het bestand als deze er nog niet staat. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Selecteer **Nu synchroniseren** op de werkbalk.

### <a name="update-the-androidmanifestxml-file"></a>Het bestand AndroidManifest.xml bijwerken

1. Nadat u uw FCM-registratietoken hebt ontvangen, gebruikt u dit om [te registreren bij Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). U ondersteunt deze registratie op de achtergrond met een `IntentService` met de naam `RegistrationIntentService`. Met deze service wordt ook uw FCM-registratietoken vernieuwd. U maakt ook een klasse met de naam `FirebaseService` als subklasse van `FirebaseMessagingService` en overschrijft de `onMessageReceived`-methode om meldingen te ontvangen en af te handelen. 

    Voeg de volgende servicedefinitie toe aan het bestand AndroidManifest.xml in de `<application>`-tag.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Voeg de volgende vereiste FCM-gerelateerde machtigingen toe onder de tag `</application>`.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Code toevoegen

1. Vouw in de Project-weergave **app** > **src** > **main** > **java** uit. Klik met de rechtermuisknop op de pakketmap onder **java**, selecteer **Nieuw** en selecteer vervolgens **Java-klasse**. Voer **NotificationSettings** in voor de naam en selecteer vervolgens **OK**.

    Zorg ervoor dat u de volgende drie tijdelijke aanduidingen in de volgende code bijwerkt voor de klasse `NotificationSettings`:

   * **HubListenConnectionString**: De verbindingsreeks **DefaultListenAccessSignature** voor de hub. Kopieer deze verbindingsreeks door te klikken op **Toegangsbeleid** in uw hub in de [Azure-portal].
   * **HubName**: Gebruik de naam van uw hub die wordt weergegeven op de hubpagina in de [Azure-portal].

     `NotificationSettings`-code:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Voer de **naam** en de **DefaultListenSharedAccessSignature** van uw hub voordat u verdergaat. 

2. Voeg een andere nieuwe klasse toe aan uw project met de naam `RegistrationIntentService`. Met deze klasse wordt de `IntentService`-interface geïmplementeerd. Deze zorgt ook voor [het vernieuwen van het GCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en [de registratie bij de Notification Hub](notification-hubs-push-notification-registration-management.md).

    Gebruik de volgende code voor deze klasse.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
               else if (!(storedToken = sharedPreferences.getString("FCMtoken", "")).equals(FCM_token)) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. Voeg in de klasse `MainActivity` de volgende instructies voor `import` toe boven de klassendeclaratie.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Voeg de volgende leden toe bovenaan de klasse. U gebruikt deze velden [om de beschikbaarheid van Google Play Services te controleren, zoals aanbevolen door Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Voeg in de klasse `MainActivity` de volgende methode toe om de beschikbaarheid van Google Play Services te controleren.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

6. Voeg in de klasse `MainActivity` de volgende code toe waarmee Google Play Services wordt gecontroleerd voordat u de `IntentService` aanroept om uw FCM-registratietoken op te halen en te registreren met uw hub:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. In de methode `OnCreate` van de klasse `MainActivity` voegt u de volgende code toe om het registratieproces te starten wanneer de activiteit wordt gemaakt:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Voeg deze extra methoden toe aan de `MainActivity` om de status van de app te controleren en een rapport van de status in uw app op te nemen:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

9. Voor de methode `ToastNotify` wordt het besturingselement *Hallo wereld* `TextView` gebruikt om de status en kennisgevingen permanent in de app te melden. Voeg in de indeling **res** > **layout** > **activity_main.xml** de volgende id toe voor het besturingselement.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Schermopname met android:id="@+id/text_hello" id toegepast op het TextView-besturingselement.](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Vervolgens gaat u een subklasse toevoegen voor de ontvanger die u hebt gedefinieerd in AndroidManifest.xml. Voeg een andere nieuwe klasse toe aan uw project met de naam `FirebaseService`.

11. Voeg boven in `FirebaseService.java` de volgende importinstructie toe:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Voeg de volgende code toe voor de klasse `FirebaseService`, zodat dit een subklasse van `FirebaseMessagingService` wordt.

    Deze code overschrijft de methode `onMessageReceived`, en rapporteert de ontvangen meldingen. Deze verzendt ook de pushmelding naar Android Notification Manager met de methode `sendNotification()`. De methode `sendNotification()` moet worden aangeroepen wanneer de app niet actief is en een melding is ontvangen.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. Klik in Android Studio in de menubalk op **Bouwen** > **Project opnieuw opbouwen** om ervoor te zorgen dat uw code geen fouten bevat. Als u een foutbericht ontvangt over het pictogram `ic_launcher`, verwijdert u de volgende instructie uit het bestand AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Zorg ervoor dat u een virtueel apparaat hebt voor het uitvoeren van de app. Als u er geen hebt, voegt u er als volgt een toe:
    1. ![Apparaatbeheer openen](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Virtueel apparaat maken](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Voer de app uit op uw geselecteerde apparaat en controleer of dat deze correct is geregistreerd in de hub.

    > [!NOTE]
    > De registratie kan mislukken bij de eerste keer starten totdat de methode `onTokenRefresh()` van de exemplaar-id-service wordt aangeroepen. De vernieuwing moet een succesvolle registratie met de Notification Hub tot stand brengen.

    ![De registratie van het apparaat is voltooid](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testen van melding verzenden vanuit de Notification Hub

U kunt pushmeldingen verzenden vanuit de [Azure-portal] door de volgende stappen uit te voeren:

1. Ga in de Azure-portal naar de pagina Notification Hub voor uw hub en selecteer **Verzenden testen** in het gedeelte **Probleemoplossing**.
3. Selecteer voor **Platforms** de optie **Android**.
4. Selecteer **Verzenden**.  U ziet nog geen melding op het Android-apparaat omdat u daarop de mobiele app niet hebt uitgevoerd. Nadat u de mobiele app hebt uitgevoerd, selecteert u opnieuw **Verzenden** om de melding weer te geven.
5. Bekijk het resultaat van de bewerking in de lijst onderaan.

    ![Azure Notification Hubs - Verzenden testen](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. U ziet de melding op uw apparaat. 

    ![Melding op apparaat](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>De mobiele app uitvoeren in een emulator

Voordat pushmeldingen binnen een emulator test, moet u ervoor zorgen dat de installatiekopie van de emulator het Google API-niveau ondersteunt dat u voor uw app hebt gekozen. Als uw installatiekopie geen ondersteuning biedt voor native Google-API’s, kan de uitzondering **SERVICE\_NIET\_BESCHIKBAAR** worden weergegeven.

Bovendien moet uw Google-account zijn toegevoegd aan de actieve emulator onder **Instellingen** > **Accounts**. Anders kunnen pogingen om opnieuw te registreren bij FCM leiden tot de uitzondering **VERIFICATIE\_MISLUKT**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Firebase Cloud Messaging gebruikt om meldingen te verzenden naar alle Android-apparaten die zijn geregistreerd bij de service. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke apparaten:

> [!div class="nextstepaction"]
>[Zelfstudie: Pushmeldingen verzenden naar specifieke Android-apparaten](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure-portal]: https://portal.azure.com
