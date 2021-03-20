---
title: Meldingen verzenden naar specifieke Android-toepassingen met Azure Notification Hubs
description: Leer hoe u pushmeldingen kunt verzenden naar specifieke Android-gebruikers met behulp van Azure Notification Hubs.
documentationcenter: android
services: notification-hubs
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
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c137cbf0c2290bdbe228f7902e616e7f79cb459e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016978"
---
# <a name="tutorial-send-push-notification-to-specific-android-users-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Zelfstudie: Pushmelding verzenden naar specifieke Android-gebruikers met Azure Notification Hubs en Google Cloud Messaging (afgeschaft)

> [!WARNING]
> Sinds 10 april 2018 heeft Google Google Cloud Messaging (GCM) afgeschaft. De GCM-server en de client-API's zijn afgeschaft en worden verwijderd op 29 mei 2019. Raadpleeg voor meer informatie de [Veelgestelde vragen over GCM en FCM](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

In deze zelfstudie wordt uitgelegd hoe u met Azure Notification Hubs pushmeldingen kunt verzenden naar een specifieke app-gebruiker op een specifiek apparaat. Er wordt een WebAPI-back-end van ASP.NET gebruikt om clients te verifiëren en meldingen te genereren, zoals u kunt lezen in het artikel [Registering from your App Backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) (Registreren vanuit uw app-back-end). Deze zelfstudie bouwt voort op de meldingshub die u hebt gemaakt in de zelfstudie [Pushmeldingen verzenden naar Android-apparaten met behulp van Azure Notification Hubs en Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Het Web API-project voor de back-end maken waarmee gebruikers worden geverifieerd.  
> * De Android-toepassing bijwerken.
> * De app testen

## <a name="prerequisites"></a>Vereisten

U moet de [Zelfstudie: Pushmeldingen verzenden naar Android-apparaten met behulp van Azure Notification Hubs en Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md) voltooien voordat u aan deze zelfstudie begint.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Het Android-project maken

De volgende stap bestaat uit het bijwerken van de Android-toepassing die u hebt gemaakt in de zelfstudie [Pushmeldingen verzenden naar Android-apparaten met behulp van Azure Notification Hubs en Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md).

1. Open het bestand `res/layout/activity_main.xml` en vervang de volgende inhoudsdefinities:

    Hiermee worden nieuwe EditText-besturingselementen toegevoegd voor aanmelding als een gebruiker. Er wordt ook een veld toegevoegd voor een gebruikersnaam-tag die deel gaat uitmaken van meldingen die u verzendt:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="GCM on"
        android:textOff="GCM off"
        android:id="@+id/toggleButtonGCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
    />  
    </RelativeLayout>
    ```
2. Open het bestand `res/values/strings.xml` en vervang de definitie `send_button` door de volgende regels die de tekenreeks voor de `send_button` aanpassen. Voeg vervolgens tekenreeksen toe voor de andere besturingselementen:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    De grafische indeling van `main_activity.xml` ziet er nu uit als in de volgende afbeelding:

    ![Schermopname van een app met vakken voor gebruikersnaam, wachtwoord, ontvanger en bericht, en met knoppen voor aanmelding en voor verzending van meldingen.][A1]
3. Maak in hetzelfde pakket als de klasse `MainActivity` een nieuwe klasse met de naam `RegisterClient`. Gebruik de onderstaande code voor het nieuwe klassebestand.

    ```java
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;

    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;

    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;

        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }

        public String getAuthorizationHeader() {
            return authorizationHeader;
        }

        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }

        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "gcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));

            int statusCode = upsertRegistration(registrationId, deviceInfo);

            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }

        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }

        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);

            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);

            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

            return registrationId;
        }
    }
    ```

    Dit onderdeel implementeert de REST-aanroepen die nodig zijn om contact op te nemen met de app-back-end, om registratie voor pushmeldingen mogelijk te maken. Daarnaast wordt de *registrationIds* die is gemaakt door de meldingshub, lokaal opgeslagen. Zie [Registering from your App Backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) (Registreren vanuit uw app-back-end) voor meer informatie. De klasse maakt gebruik van een verificatietoken dat wordt opgeslagen in de lokale opslag wanneer u op de knop **Sign in** klikt.
4. Verwijder in uw klasse het private-veld voor `NotificationHub` of maak er een commentaar van. Voeg vervolgens een veld toe voor de klasse `RegisterClient` en een tekenreeks voor het eindpunt van uw ASP.NET-back-end. Zorg ervoor dat u `<Enter Your Backend Endpoint>` vervangt door het eindpunt van de back-end dat u eerder hebt vastgesteld. Bijvoorbeeld `http://mybackend.azurewebsites.net`.

    ```java
    //private NotificationHub hub;
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

5. Ga in de klasse `MainActivity` naar de methode `onCreate` en verwijder de initialisatie van het veld `hub` en de aanroep van de methode `registerWithNotificationHubs` of maak er een commentaar van. Voeg vervolgens code toe voor het initialiseren van een exemplaar van de klasse `RegisterClient`. De methode moet de volgende regels bevatten:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);

        //hub = new NotificationHub(HubName, HubListenConnectionString, this);
        //registerWithNotificationHubs();

        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

        setContentView(R.layout.activity_main);
    }
    ```
6. Ga naar de klasse `MainActivity` en verwijder de hele methode `registerWithNotificationHubs` of maak er een commentaar van. Deze methode wordt niet gebruikt in deze zelfstudie.
7. Voeg de volgende instructies voor `import` toe aan uw `MainActivity.java`-bestand.

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;

    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;

    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;

    import android.app.AlertDialog;
    import android.content.DialogInterface;
    ```
8. Vervang de code van de onStart-methode door de volgende code:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Voeg vervolgens de volgende methoden toe voor het afhandelen van het klikken op de knop **Sign in** en het verzenden van pushmeldingen.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    String regid = gcm.register(NotificationSettings.SenderId);
                    registerClient.register(regid, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Logged in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "gcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                            response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    De `login`-handler voor de knop **Sign in** genereert een eenvoudig verificatietoken op basis van de gebruikersnaam en het wachtwoord die zijn ingevoerd (of ieder ander token dat door uw verificatieschema wordt gebruikt) en gebruikt vervolgens `RegisterClient` om de back-end aan te roepen voor registratie.

    De methode `sendPush` roept de back-end aan om een beveiligde melding te activeren naar de gebruiker, op basis van de gebruikerstag. De Platform Notification Service die door `sendPush` wordt benaderd, is afhankelijk van de `pns`-tekenreeks die wordt doorgegeven.

10. Voeg de volgende methode `DialogNotify` toe aan de klasse `MainActivity`.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. Werk in uw klasse `MainActivity` de methode `sendNotificationButtonOnClick` als volgt bij voor het aanroepen van de methode `sendPush` met de door de gebruiker geselecteerd Platform Notification Services.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
        {
            sendPush("gcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. Voeg in het bestand `build.gradle` de volgende regel toe aan de sectie `android` na de sectie `buildTypes`.

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. Maak het project.

## <a name="test-the-app"></a>De app testen

1. Voer de toepassing uit op een apparaat of in een emulator met Android Studio.
2. Voer in de Android-app een gebruikersnaam en wachtwoord in. Deze moeten beide dezelfde tekenreekswaarde zijn en ze mogen geen spaties of speciale tekens bevatten.
3. Klik in de Android-app op **Sign in**. Wacht tot u het pop-upbericht **Logged in and registered** ziet. De knop **Send Notification** kan nu worden gekozen.

    ![Schermopname van een app. Een pop-upbericht waarin wordt bevestigd dat de gebruiker is aangemeld, ‘geregistreerd’ zichtbaar is en de knop Melding verzenden is ingeschakeld.][A2]
4. Klik op de wisselknoppen om alle platforms in te schakelen waarop u de app hebt uitgevoerd en een gebruiker hebt geregistreerd.
5. Voer de naam van de gebruiker in die de melding moet ontvangen. Die gebruiker moet zijn geregistreerd voor meldingen op de doelapparaten.
6. Voer een bericht in dat de gebruiker als een pushmelding moet ontvangen.
7. Klik op **Send Notification**.  Elk apparaat dat een registratie heeft met de overeenkomende gebruikersnaam-tag ontvangt de pushmelding.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u pushmeldingen kunt verzenden naar specifieke gebruikers door een tag te koppelen aan hun registraties. Als u wilt weten hoe u locatiegebaseerde pushmeldingen kunt verzenden, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Locatiegebaseerde pushmeldingen verzenden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
