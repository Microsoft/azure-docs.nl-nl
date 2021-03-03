---
title: Aan de slag met Notification Hubs die gebruikmaken van Baidu | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar Android-apparaten die gebruikmaken van Baidu.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 06/19/2019
ms.custom: devx-track-java, devx-track-csharp
ms.openlocfilehash: 098fb0ed967dcacac24ce3abfd4843f9fe14ff49
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727172"
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Aan de slag met Azure Notification Hubs die gebruikmaken van Baidu

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Baidu Cloud Push is een Chinese cloudservice waarmee u pushmeldingen naar mobiele apparaten kunt verzenden.

Aangezien Google Play en FCM (Firebase Cloud Messaging) niet beschikbaar zijn in China, moet u andere appstores en pushservices gebruiken. Baidu is er een van en deze wordt momenteel gebruikt door de Notification Hub.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Android SDK (ervan uitgaande dat u Android Studio gebruikt) die u kunt downloaden van de [Android-site](https://go.microsoft.com/fwlink/?LinkId=389797)
* [Android SDK Baidu Push]

> [!NOTE]
> U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F) voor meer informatie.

Ga als volgt te werk om aan de slag te gaan:

1. Maak een Baidu-account.
2. Maak een Baidu Cloud Push project en noteer de API-sleutel en de geheime sleutel.

## <a name="configure-a-new-notification-hub"></a>Een nieuwe Notification Hub configureren

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Selecteer **Notification Services** in de Notification Hub en selecteer vervolgens **Baidu (Android China)**.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs - Baidu](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

&emsp;&emsp;7. Schuif omlaag naar het gedeelte met meldingsinstellingen voor Baidu. Voer de API-sleutel en geheime sleutel die u hebt gekregen via de Baidu-console, in het Baidu Cloud Push-project in. Klik vervolgens op Save.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs - Baidu-geheimen](./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png)

De Notification Hub is nu geconfigureerd om met Baidu te kunnen werken. U hebt ook de **verbindingsreeksen** die u nodig hebt om uw app te registreren om pushmeldingen te verzenden en te ontvangen.

Noteer de `DefaultListenSharedAccessSignature` en `DefaultFullSharedAccessSignature` in het venster met verbindingsgegevens.

## <a name="connect-your-app-to-the-notification-hub"></a>Uw app verbinden met de Notification Hub

1. Maak een nieuw Android-project in Android Studio (File > New > New Project).

    ![Azure Notification Hubs - Baidu: nieuw project](./media/notification-hubs-baidu-get-started/AndroidNewProject.png)

2. Voer een toepassingsnaam in en zorg ervoor dat de minimaal vereiste SDK-versie is ingesteld op API 16: Android 4.1. **Zorg er ook voor dat de pakketnaam (应用包名) hetzelfde is als in de Baidu-cloudpush-portal**

    ![Azure Notification Hubs-Baidu min SDK1 ](./media/notification-hubs-baidu-get-started/AndroidMinSDK.png) ![ Azure notification hubs-BAIDU min SDK2](./media/notification-hubs-baidu-get-started/AndroidMinSDK2.png)

3. Klik op Next en doorloop de wizard totdat het venster Create Activity (Activiteit maken) wordt weergegeven. Zorg ervoor dat Empty Activity (Lege activiteit) is geselecteerd en selecteer daarna Finish (Voltooien) om een nieuwe Android-toepassing te maken.

    ![Azure Notification Hubs - Baidu: activiteit toevoegen](./media/notification-hubs-baidu-get-started/AndroidAddActivity.png)

4. Zorg ervoor dat het doel van de projectbuild juist is ingesteld.

5. Voeg daarna Azure Notification Hubs-bibliotheken toe. Voeg in het bestand `Build.Gradle` voor de app de volgende regels toe in het gedeelte met afhankelijkheden.

    ```javascript
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

    Voeg de volgende opslagplaats toe na het gedeelte met afhankelijkheden.

    ```javascript
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

    Als u het lijstconflict wilt voorkomen, kunt u de volgende code toevoegen in het bestand `Manifest.xml` van het project:

    ```xml
    <manifest package="YOUR.PACKAGE.NAME"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android">
    ```

    en in de tag `<application/>`:

    ```xml
    <application
        tools:replace="android:allowBackup,icon,theme,label">
    ```

6. Download [Baidu Push Android SDK](https://push.baidu.com/doc/android/api) en pak deze uit. Kopieer het bestand `pushservice-x.y.z jar` in de map libs. Kopieer de `.so`-bestanden in de mappen in `src/main/jniLibs` (maak een nieuwe map) van uw Android-toepassing.

    ![Azure Notification Hubs - Baidu SDK Libs](./media/notification-hubs-baidu-get-started/BaiduSDKLib.png)

7. Klik in de map `libs` van het project met de rechtermuisknop op het bestand `pushervice-x.y.z.jar`. Selecteer **Toevoegen als bibliotheek** om deze bibliotheek in het project op te nemen.

    ![Azure Notification Hubs - Baidu: toevoegen als bibliotheek](./media/notification-hubs-baidu-get-started/BaiduAddAsALib.jpg)

8. Open het bestand `AndroidManifest.xml` van het Android-project en voeg de machtigingen toe die vereist zijn door de Baidu SDK. **Vervang `YOURPACKAGENAME` door de pakketnaam**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR" />
    !! <uses-permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" />
    !!<permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" android:protectionLevel="normal" />

    ```

9. Voeg de volgende configuratie toe aan het toepassingselement na het activiteitelement `.MainActivity`, waarbij u *yourprojectname* vervangt (bijvoorbeeld `com.example.BaiduTest`):

    ```xml
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaViewActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaViewActivity" />
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaListActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaListActivity"
        android:launchMode="singleTask" />

    <!-- Push application definition message -->
    <receiver android:name=".MyPushMessageReceiver">
        <intent-filter>

            <!-- receive push message-->
            <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
            <!-- receive bind,unbind,fetch,delete.. message-->
            <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
            <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.PushServiceReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            <action android:name="android.intent.action.MEDIA_MOUNTED" />
            <action android:name="android.intent.action.USER_PRESENT" />
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.RegistrationReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.METHOD" />
            <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REMOVED" />

            <data android:scheme="package" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.baidu.android.pushservice.PushService"
        android:exported="true"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
        </intent-filter>
    </service>

    <service
        android:name="com.baidu.android.pushservice.CommandService"
        android:exported="true" />

    <!-- Adapt the ContentProvider declaration required for the Android N system, and the write permissions include the application package name-->
    <provider
        android:name="com.baidu.android.pushservice.PushInfoProvider"
        android:authorities="com.baidu.push.example.bdpush"
        android:exported="true"
        android:protectionLevel="signature"
        android:writePermission="baidu.push.permission.WRITE_PUSHINFOPROVIDER. yourprojectname  " />

    <!-- API Key of the Baidu application -->
    <meta-data
        android:name="api_key"
        !!   android:value="api_key" />
    </application>
    ```

10. Voeg een nieuwe klasse genaamd `ConfigurationSettings.java` toe aan het project.

    ```java
    public class ConfigurationSettings {
        public static String API_KEY = "...";
        public static String NotificationHubName = "...";
        public static String NotificationHubConnectionString = "...";
    }
    ```

    Stel de waarde van de tekenreeks `API_KEY` in op de API_KEY uit het Baidu-cloudproject.

    Stel de waarde van de tekenreeks `NotificationHubName` in op de naam van uw Notification Hub uit [Azure Portal] en stel daarna `NotificationHubConnectionString` in op `DefaultListenSharedAccessSignature` uit [Azure Portal].

11. Open MainActivity.java en voeg het volgende toe aan de methode onCreate:

    ```java
    PushManager.startWork(this, PushConstants.LOGIN_TYPE_API_KEY,  API_KEY );
    ```

12. Voeg een nieuwe klasse genaamd `MyPushMessageReceiver.java` toe en voeg de volgende code eraan toe. Dit is de klasse die verantwoordelijk is voor de pushmeldingen die worden ontvangen van de Baidu-pushserver.

    ```java
    package your.package.name;

    import android.content.Context;
    import android.content.Intent;
    import android.os.AsyncTask;
    import android.text.TextUtils;
    import android.util.Log;

    import com.baidu.android.pushservice.PushMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import org.json.JSONException;
    import org.json.JSONObject;

    import java.util.List;

    public class MyPushMessageReceiver extends PushMessageReceiver {

        public static final String TAG = MyPushMessageReceiver.class
                .getSimpleName();
        public static NotificationHub hub = null;
        public static String mChannelId, mUserId;

        @Override
        public void onBind(Context context, int errorCode, String appid,
                        String userId, String channelId, String requestId) {
            String responseString = "onBind errorCode=" + errorCode + " appid="
                    + appid + " userId=" + userId + " channelId=" + channelId
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Binding successful
                Log.d(TAG, " Binding successful");
            }
            try {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName,
                            ConfigurationSettings.NotificationHubConnectionString,
                            context);
                    Log.i(TAG, "Notification hub initialized");
                }
            } catch (Exception e) {
                Log.e(TAG, e.getMessage());
            }
            mChannelId = channelId;
            mUserId = userId;

            registerWithNotificationHubs();
        }
        private void registerWithNotificationHubs() {

            new AsyncTask<Void, Void, Void>() {
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        hub.registerBaidu(mUserId, mChannelId);
                        Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        @Override
        public void onMessage(Context context, String message,
                            String customContentString) {
            String messageString = " onMessage=\"" + message
                    + "\" customContentString=" + customContentString;
            Log.d(TAG, messageString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

        }

        @Override
        public void onNotificationArrived(Context context, String title, String description, String customContentString) {
            String notifyString = " Notice Arrives onNotificationArrived  title=\"" + title
                    + "\" description=\"" + description + "\" customContent="
                    + customContentString;
            Log.d(TAG, notifyString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onNotificationClicked(Context context, String title, String description, String customContentString) {
            String notifyString = " onNotificationClicked title=\"" + title + "\" description=\""
                    + description + "\" customContent=" + customContentString;
            Log.d(TAG, notifyString);
            Intent intent = new Intent(context.getApplicationContext(),MainActivity.class);
            intent.putExtra("title",title);
            intent.putExtra("description",description);
            intent.putExtra("isFromNotify",true);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.getApplicationContext().startActivity(intent);

        }

        @Override
        public void onSetTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onSetTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onDelTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onDelTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onListTags(Context context, int errorCode, List<String> tags,
                            String requestId) {
            String responseString = "onListTags errorCode=" + errorCode + " tags="
                    + tags;
            Log.d(TAG, responseString);

        }

        @Override
        public void onUnbind(Context context, int errorCode, String requestId) {
            String responseString = "onUnbind errorCode=" + errorCode
                    + " requestId = " + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Unbinding is successful
                Log.d(TAG, " Unbinding is successful ");
            }
        }
    }
    ```

## <a name="send-notifications-to-your-app"></a>Pushmeldingen naar uw app verzenden

U kunt de ontvangst van meldingen snel testen via [Azure Portal]: gebruik de knop **Verzenden** in het configuratiescherm van de Notification Hub, zoals in de volgende schermen wordt weergegeven:

![Scherm opname van de Azure-Portal met de optie voor het verzenden van testen, die wordt beschreven in een rood en een rode pijl die ernaar wijst. ](./media/notification-hubs-baidu-get-started/BaiduTestSendButton.png)
 ![ Scherm opname van de pagina voor het verzenden van de Baidu-test in de Azure Portal.](./media/notification-hubs-baidu-get-started/BaiduTestSend.png)

Pushmeldingen worden gewoonlijk in een back-endservice zoals Mobile Services of ASP.NET verzonden met een compatibele bibliotheek. U kunt de REST API ook rechtstreeks gebruiken om meldingen te verzenden als er geen bibliotheek beschikbaar is voor uw back-end.

In deze zelfstudie wordt voor het gemak een console-app gebruikt om te laten zien hoe u een melding verzendt met de .NET-SDK. U kunt echter het beste de zelfstudie [Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) doornemen voor informatie over het verzenden van meldingen vanuit een ASP.NET-back-end. 

Hier zijn verschillende methoden om meldingen te verzenden:

* **REST-interface**: u kunt meldingen op elk back-endplatform ondersteunen met de [REST-interface](/previous-versions/azure/reference/dn223264(v=azure.100)).
* **Microsoft Azure Notification Hubs .NET SDK**: in NuGet Package Manager voor Visual Studio voert u [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) uit.
* **Node.js**: [Notification hubs van Node.jsgebruiken](notification-hubs-nodejs-push-notification-tutorial.md).
* **Mobile Apps**: zie [Pushmeldingen toevoegen voor mobiele apps](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) voor een voorbeeld van hoe u meldingen verzendt vanuit een Azure App Service Mobile Apps-backend die is geïntegreerd met Notification Hubs.
* **Java/PHP**: zie 'Notification Hubs gebruiken vanuit Java/PHP' voor een voorbeeld van hoe u meldingen verzendt met de REST API's ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Optioneel) Meldingen verzenden vanuit een .NET-console-app

In dit gedeelte behandelen we hoe u een melding vanuit een .NET-console-app kunt verzenden.

1. Maak een nieuwe Visual C#-consoletoepassing:

    ![Scherm afbeelding van het dialoog venster Nieuw project met de optie console toepassing Visual C# gemarkeerd.](./media/notification-hubs-baidu-get-started/ConsoleProject.png)

2. Stel in het venster Package Manager-console het **standaardproject** in op uw nieuwe consoletoepassingsproject en voer vervolgens in het consolevenster de volgende opdracht uit:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

    Hiermee wordt een verwijzing toegevoegd aan de Azure Notification Hubs-SDK met het [Microsoft.Azure.Notification Hubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

    ![Scherm afbeelding van het dialoog venster Package Manager-console met de optie verzenden naar notification hub, omcirkeld in het rood.](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Open het bestand `Program.cs` en voeg de volgende instructie toe:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

4. Voeg in de klasse `Program` de volgende methode toe en vervang `DefaultFullSharedAccessSignatureSASConnectionString` en `NotificationHubName` door de waarden die u hebt.

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
        string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
        var result = await hub.SendBaiduNativeNotificationAsync(message);
    }
    ```

5. Voeg de volgende regels in de `Main`-methode toe:

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

## <a name="test-your-app"></a>Uw app testen

Als u uw app met een telefoon wilt testen, sluit u de telefoon met een USB-kabel op uw computer aan. Met deze actie wordt uw app naar de gekoppelde telefoon geladen.

Als u deze app wilt testen met de emulator, klikt u op de bovenste taakbalk in Android Studio op **Run** (Uitvoeren) en selecteert u uw app. De emulator wordt gestart en daarna wordt de app geladen en uitgevoerd.

De app haalt de `userId` en `channelId` op uit de Baidu-pushmeldingenservice en registreert zich bij de Notification Hub.

U kunt een testmelding verzenden vanaf het foutopsporingstabblad van [Azure Portal]. Als u de .Net-console-toepassing hebt gebouwd voor Visual Studio, drukt u in Visual Studio op F5 om de toepassing te starten. De toepassing verstuurt een melding. Deze verschijnt in het bovenste gedeelte voor meldingen op uw apparaat of in de emulator.

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK Baidu Push]: https://push.baidu.com/sdk/push_client_sdk_for_android
[Azure-portal]: https://portal.azure.com/
[Baidu portal]: https://www.baidu.com/
