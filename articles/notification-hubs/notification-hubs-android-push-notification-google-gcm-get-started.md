---
title: Pushmeldingen verzenden naar Android met Azure Notification Hubs en Google Cloud Messaging | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en Google Firebase Cloud Messaging pushmeldingen verzendt naar Android-apparaten.
services: notification-hubs
documentationcenter: android
keywords: pushmeldingen,pushmelding,android-pushmelding
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c97710202a448c613ab685932cb335bbaed4953
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87832651"
---
# <a name="tutorial-send-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Zelfstudie: Pushmeldingen naar Android-apparaten verzenden met Azure Notification Hubs en Google Cloud Messaging (afgeschaft)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> Sinds 10 april 2018 heeft Google Google Cloud Messaging (GCM) afgeschaft. De GCM-server en de client-API's zijn afgeschaft en worden verwijderd op 29 mei 2019. Raadpleeg voor meer informatie de [Veelgestelde vragen over GCM en FCM](https://developers.google.com/cloud-messaging/faq).

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Android-toepassing.
U maakt een lege Android-app die pushmeldingen ontvangt via Google Cloud Messaging (GCM).

> [!IMPORTANT]
> De Google Cloud Messaging (GCM) is afgeschaft en zal [binnenkort](https://developers.google.com/cloud-messaging/faq) worden verwijderd.

> [!IMPORTANT]
> In dit onderwerp worden pushmeldingen met Google Cloud Messaging (GCM) getoond. Als u gebruikmaakt van Google Firebase Cloud Messaging (FCM), verwijzen wij u naar [Sending push notifications to Android with Azure Notification Hubs and FCM](notification-hubs-android-push-notification-google-fcm-get-started.md) (Pushmeldingen verzenden naar Android met Azure Notification Hubs en FCM).

U kunt de voltooide code voor deze zelfstudie [hier](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp) downloaden op GitHub.

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Maak een project dat Google Cloud Messaging ondersteunt.
> * Een Notification Hub maken
> * Uw app verbinden met de Notification Hub
> * De app testen

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Een project maken dat Google Cloud Messaging ondersteunt

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>GCM-instelling voor de Notification Hub configureren

1. Selecteer **Google (GCM)** in **INSTELLINGEN VOOR MELDINGEN**.
2. Voer de **API-sleutel** in die u van de Google Cloud Console hebt gekregen.
3. Selecteer **Opslaan** op de werkbalk.

    ![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

De Notification Hub is nu geconfigureerd voor GCM en u hebt de verbindingsreeksen om uw app te registreren voor het ontvangen en verzenden van pushmeldingen.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Uw app verbinden met de Notification Hub

### <a name="create-a-new-android-project"></a>Een nieuw Android-project maken

1. Start een nieuw Android Studio-project in Android Studio.

   ![Android Studio - nieuw project][13]
2. Kies de vormfactor voor **Telefoon en tablet** en de **minimale SDK** die u wilt ondersteunen. Klik op **Volgende**.

   ![Android Studio - werkstroom voor het maken van een project][14]
3. Kies **Lege activiteit** als belangrijkste activiteit, klik op **Volgende** en klik vervolgens op **Voltooien**.

### <a name="add-google-play-services-to-the-project"></a>Google Play-services aan het project toevoegen

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs-bibliotheken toevoegen

1. Voeg in het bestand `Build.Gradle` voor de **app** de volgende regels toe in het gedeelte **afhankelijkheden**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Voeg de volgende opslagplaats toe na het gedeelte **afhankelijkheden**.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>De AndroidManifest.xml van het project bijwerken

1. Als u GCM wilt ondersteunen, implementeert u een exemplaar-id listenerservice in de code die wordt gebruikt voor het [verkrijgen van registratietokens](https://developers.google.com/cloud-messaging/) met de [API-exemplaar-id van Google](https://developers.google.com/instance-id/). In deze zelfstudie is de naam van de klasse `MyInstanceIDService`.

    Voeg de volgende servicedefinitie toe aan het bestand AndroidManifest.xml in de `<application>`-tag. Vervang de tijdelijke aanduiding `<your package>` door de werkelijke pakketnaam die bovenaan het bestand `AndroidManifest.xml` wordt weergegeven.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```

2. Nadat de toepassing het GCM-registratietoken van de API voor exemplaar-id’s heeft ontvangen, gebruikt het token voor [registratie bij de Azure Notification Hub](notification-hubs-push-notification-registration-management.md). De registratie op de achtergrond wordt uitgevoerd met een `IntentService` met de naam `RegistrationIntentService`. Deze service is ook verantwoordelijk voor [het vernieuwen van het GCM-registratietoken](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    Voeg de volgende servicedefinitie toe aan het bestand AndroidManifest.xml in de `<application>`-tag. Vervang de tijdelijke aanduiding `<your package>` door de werkelijke pakketnaam die bovenaan het bestand `AndroidManifest.xml` wordt weergegeven.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Definieer een ontvanger voor het ontvangen van meldingen. Voeg de volgende ontvangerdefinitie toe aan het bestand AndroidManifest.xml in de `<application>`-tag. Vervang de tijdelijke aanduiding `<your package>` door de werkelijke pakketnaam die bovenaan het bestand `AndroidManifest.xml` wordt weergegeven.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

4. Voeg de volgende vereiste GCM-machtigingen toe onder de `<application>`-tag. Vervang `<your package>` door de pakketnaam die boven in het bestand `AndroidManifest.xml` wordt weergegeven.

    Zie [Een GCM Client-app instellen voor Android](https://developers.google.com/cloud-messaging/) voor meer informatie over deze machtigingen.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>Code toevoegen

1. Vouw in de Project-weergave **app** > **src** > **main** > **java** uit. Klik met de rechtermuisknop op de pakketmap onder **java**, klik op **Nieuw** en klik vervolgens op **Java-klasse**. Voeg een nieuwe klasse met de naam `NotificationSettings` toe.

    ![Android Studio - nieuwe Java-klasse][6]

    Werk de drie tijdelijke aanduidingen in de volgende code bij voor de klasse `NotificationSettings`:

   * `SenderId`: Het projectnummer dat u eerder in de [Google Cloud Console](https://cloud.google.com/console) hebt verkregen.
   * `HubListenConnectionString`: De verbindingsreeks **DefaultListenAccessSignature** voor de hub. Kopieer deze verbindingsreeks door te klikken op **Toegangsbeleid** op de hubpagina **Instellingen** in [Azure-portal].
   * `HubName`: Gebruik de naam van uw Notification Hub die wordt weergegeven op de hubpagina in de [Azure-portal].

     `NotificationSettings`-code:

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```

2. Voeg een andere nieuwe klasse toe met de naam `MyInstanceIDService`. Deze klasse is de implementatie van uw exemplaar-id listenerservice.

    Met de code voor deze klasse roept u `IntentService` aan voor het op de achtergrond [vernieuwen van het GCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Voeg een andere nieuwe klasse aan uw project toe met de naam `RegistrationIntentService`. Met deze klasse implementeert u `IntentService` die zorgt voor [het vernieuwen van het GCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en [de registratie bij de Notification Hub](notification-hubs-push-notification-registration-management.md).

    Gebruik de volgende code voor deze klasse.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Voeg in uw klasse `MainActivity` de volgende instructies voor `import` toe aan het begin van de klasse.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```

5. Voeg de volgende privé-leden toe aan de bovenkant van de klasse. Deze code [controleert de beschikbaarheid van Google Play Services, zoals aanbevolen door Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```

6. Voeg in uw klasse `MainActivity` de volgende methode toe om de beschikbaarheid van Google Play Services te controleren.

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
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

7. Voeg in uw klasse `MainActivity` de volgende code toe waarmee Google Play Services wordt gecontroleerd voordat u uw `IntentService` aanroept om uw GCM-registratietoken op te halen en te registreren met de Notification Hub.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. In de methode `OnCreate` van de klasse `MainActivity` voegt u de volgende code toe om het registratieproces te starten wanneer de activiteit wordt gemaakt.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```

9. Voeg deze extra methoden toe aan de `MainActivity` om de status van de app te controleren en een rapport van de status in uw app op te nemen.

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

10. Voor de methode `ToastNotify` wordt het besturingselement *Hallo wereld* `TextView` gebruikt om de status en kennisgevingen permanent in de app te melden. In de indeling activity_main.xml voegt u de volgende id toe voor het besturingselement.

    ```xml
    android:id="@+id/text_hello"
    ```

11. Voeg een subklasse toe voor de ontvanger die is gedefinieerd in AndroidManifest.xml. Voeg een andere nieuwe klasse toe aan uw project met de naam `MyHandler`.

12. Voeg boven in `MyHandler.java` de volgende importinstructie toe:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```

13. Voeg de volgende code toe voor de klasse `MyHandler`, zodat dit een subklasse van `com.microsoft.windowsazure.notifications.NotificationsHandler` wordt.

    Deze code overschrijft de `OnReceive`-methode, zodat de handler de ontvangen meldingen rapporteert. De handler verzendt ook de pushmelding naar Android Notification Manager met de methode `sendNotification()`. De methode `sendNotification()` moet worden uitgevoerd wanneer de app niet actief is en een melding is ontvangen.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```

14. Klik in Android Studio in de menubalk op **Bouwen** > **Project opnieuw opbouwen** om ervoor te zorgen dat uw code geen fouten bevat.

## <a name="testing-your-app"></a>Uw app testen

### <a name="run-the-mobile-application"></a>De mobiele toepassing uitvoeren

1. Start de app. U zult zien dat de registratie-id meldt dat de registratie is gelukt.

      ![Testen op Android - Kanaalregistratie][18]
2. Voer een melding in die moet worden verzonden naar alle Android-apparaten die zijn geregistreerd bij de hub.

      ![Testen op Android - een bericht verzenden][19]

3. Druk op **Melding verzenden**. Alle apparaten waarop de app actief is, ontvangen een `AlertDialog`-exemplaar bij de pushmelding. Apparaten waarop de app niet actief is, maar die eerder zijn geregistreerd voor pushmeldingen, ontvangen een melding in Android Notification Manager. De meldingen kunnen worden bekeken door omlaag te vegen vanuit de linkerbovenhoek.

      ![Testen op Android - meldingen][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Het verzenden van pushmeldingen vanuit Azure Portal testen

U kunt het ontvangen van meldingen in de app testen door deze te verzenden via [Azure-portal].

1. In de sectie **Probleemoplossing** selecteert u **Test verzenden**.
2. Selecteer voor **Platforms** de optie **Android**.
3. Selecteer **Verzenden** om de testmelding te verzenden.
4. Controleer of de melding op het Android-apparaat wordt weergegeven.

    ![Azure Notification Hubs - Verzenden testen](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Pushmeldingen in de emulator

Als u pushmeldingen binnen een emulator wilt testen, moet u ervoor zorgen dat de installatiekopie van de emulator het Google API-niveau ondersteunt dat u voor uw app hebt gekozen. Als uw installatiekopie geen ondersteuning biedt voor native Google API’s, verschijnt de uitzondering **SERVICE\_NIET\_BESCHIKBAAR**.

Bovendien moet uw Google-account zijn toegevoegd aan de actieve emulator onder **Instellingen** > **Accounts**. Anders kunnen pogingen om opnieuw te registreren bij GCM leiden tot de uitzondering **VERIFICATIE\_MISLUKT**.

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Optioneel) Pushmeldingen rechtstreeks vanuit de app verzenden

Normaal gesproken verzendt u meldingen via een back-endserver. Mogelijk wilt u pushmeldingen direct vanuit de clienttoepassing kunnen verzenden. In dit gedeelte wordt uitgelegd hoe u meldingen vanuit de client verzendt met de [Azure Notification Hub REST API](/previous-versions/azure/reference/dn223264(v=azure.100)).

1. Vouw in de Project-weergave van Android Studio-Project **App** > **src** > **main** > **res** > **layout** uit. Open het indelingsbestand `activity_main.xml` en klik op het tabblad **Tekst** om de tekst van het bestand bij te werken. Werk de tekst bij met de onderstaande code, waarmee u nieuwe besturingselementen `Button` en `EditText` voor het verzenden van pushmeldingen toevoegt aan de Notification Hub. Voeg deze code onderin toe, net voor `</RelativeLayout>`.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```

2. Vouw in de Project-weergave van Android Studio-Project **App** > **src** > **main** > **res** > **values** uit. Open het bestand `strings.xml` en voeg de tekenreekswaarden toe waarnaar wordt verwezen door de nieuwe besturingselementen `Button` en `EditText`. Voeg de volgende regels onder in het bestand toe, net voor `</resources>`.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```

3. Voeg in het bestand `NotificationSetting.java` de volgende instelling toe voor de klasse `NotificationSettings`.

    Werk `HubFullAccess` bij met de verbindingsreeks **DefaultFullSharedAccessSignature** voor uw hub. U kunt deze verbindingsreeks kopiëren vanuit [Azure-portal] door te klikken op **Toegangsbeleid** op de pagina **Instellingen** voor uw Notification Hub.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```

4. Voeg in uw bestand `MainActivity.java` de volgende instructies voor `import` toe aan het begin van het bestand.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```

5. Voeg in uw bestand `MainActivity.java` de volgende leden toe boven de klasse `MainActivity`.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```

6. Maak een SAS-token (Shared Access Signature) om een POST-aanvraag te verifiëren om berichten naar uw Notification Hub te kunnen verzenden. Parseer de belangrijkste gegevens uit de verbindingsreeks en maak vervolgens het SaS-token, zoals vermeld in [Algemene concepten](/previous-versions/azure/reference/dn495627(v=azure.100)) REST API-verwijzing. De volgende code is een voorbeeldimplementatie.

    Voeg in `MainActivity.java` de volgende methode toe aan de klasse `MainActivity` om uw verbindingsreeks te parseren.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```

7. Voeg in `MainActivity.java` de volgende methode toe aan de klasse `MainActivity` om een SAS-verificatietoken te maken.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```

8. Voeg in `MainActivity.java` de volgende methode toe aan de klasse `MainActivity` om het klikken op de knop **Melding verzenden** te activeren en de pushmelding naar de hub te verzenden met de ingebouwde REST-API.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meldingen uitgezonden naar al uw Android-apparaten die zijn geregistreerd bij de back-end. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke Android-apparaten, gaat u verder met de volgende zelfstudie:  

 > [!div class="nextstepaction"]
 > [Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure-portal]: https://portal.azure.com
