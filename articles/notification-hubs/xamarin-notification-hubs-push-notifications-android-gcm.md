---
title: Pushmeldingen verzenden naar Xamarin.Android-apps met Azure Notification Hubs | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Xamarin.Android-toepassing.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221031"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar Xamarin.Android-apps met behulp van Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Xamarin.Android-toepassing. U maakt een lege Xamarin.Android-app die pushmeldingen ontvangt via Firebase Cloud Messaging (FCM). U gebruikt u de Notification Hub om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd. De voltooide code is beschikbaar in het voorbeeld [NotificationHubs-app](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een Firebase-project maken en Firebase Cloud Messaging inschakelen
> * Een Notification Hub maken
> * Een Xamarin.Android-app maken en deze verbinden met de Notification Hub
> * Testmeldingen verzenden vanuit Azure Portal

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* [Visual Studio met Xamarin] onder Windows of [Visual Studio voor Mac] onder OS X.
* Actief Google-account

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Een Firebase-project maken en Firebase Cloud Messaging inschakelen

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>GCM/FCM-instellingen configureren voor de Notification Hub

1. Selecteer **Google (GCM/FCM)** in het gedeelte **Instellingen** in het menu links.
2. Voer de **serversleutel** in die u eerder hebt genoteerd in de Google Firebase Console.
3. Selecteer **Opslaan** op de werkbalk.

    ![Schermopname van de meldingshub in Azure Portal met de optie Google G C M F C M gemarkeerd en een contour in rood.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

De Notification Hub is geconfigureerd om te werken met FCM en u hebt de verbindingsreeksen om uw app te registreren voor het ontvangen van meldingen en voor het verzenden van pushmeldingen.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Een Xamarin.Android-app maken en deze verbinden met de Notification Hub

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio-project maken en NuGet-pakketten toevoegen

> [!NOTE]
> De stappen die in deze zelfstudie worden beschreven, zijn voor Visual Studio 2017. 

1. Open in Visual Studio het menu **File** en selecteer **New** en vervolgens **Project**. Voer in het venster **Nieuw project** de volgende stappen uit:
    1. Vouw **Geïnstalleerd** uit, vervolgens **Visual C#** en klik dan op **Android**.
    2. Selecteer **Android-app (Xamarin)** uit de lijst.
    3. Voer een **naam** in voor het project.
    4. Selecteer een **locatie** voor het project.
    5. Selecteer **OK**

        ![Het dialoogvenster Nieuw project](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Selecteer in het dialoogvenster **Nieuwe Android-app** de optie **Lege app** en dan **OK**.

    ![Schermopname waarin de sjabloon Lege app is gemarkeerd.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Vouw in het venster **Solution Explorer** het item **Properties** uit en klik op **AndroidManifest.xml**. Wijzig de naam van het pakket zodat deze overeenkomt met de pakketnaam die u hebt ingevoerd tijdens het toevoegen van Firebase Cloud Messaging aan uw project in Google Firebase Console.

    ![Pakketnaam in GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Stel de Android-doel versie voor het project in op **android 10,0** door de volgende stappen uit te voeren: 
    1. Klik met de rechtermuisknop op het project en selecteer **Eigenschappen**. 
    1. Selecteer **android 10,0** voor het veld **compileren met behulp van Android-versie: (Target Framework)** . 
    1. Selecteer **Ja** in het berichtvenster om door te gaan met het wijzigen van het doelframework.
1. Voeg de vereiste NuGet-pakketten toe aan het project door de volgende stappen uit te voeren:
    1. Klik met de rechtermuisknop op het project en selecteer **Manage NuGet Packages...** .
    1. Ga naar het tabblad **Geïnstalleerd**, selecteer **Xamarin.Android.Support.Design** en selecteer **Update** in het rechterdeelvenster om het pakket bij te werken naar de meest recente versie.
    1. Ga naar het tabblad **Bladeren**. Zoek naar **Xamarin.GooglePlayServices.Base**. Selecteer **Xamarin.GooglePlayServices.Base** in de lijst met resultaten. Selecteer vervolgens **Install**.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Zoek in het venster **NuGet Package Manager** naar **Xamarin.Firebase.Messaging**. Selecteer **Xamarin.Firebase.Messaging** in de lijst met resultaten. Selecteer vervolgens **Install**.
    7. Zoek nu naar **Xamarin.Azure.NotificationHubs.Android**. Selecteer **Xamarin.Azure.NotificationHubs.Android** in de lijst met resultaten. Selecteer vervolgens **Install**.

### <a name="add-the-google-services-json-file"></a>Voeg het JSON-bestand van Google Services toe

1. Kopieer het bestand `google-services.json` dat u uit de Google Firebase Console hebt gedownload naar de projectmap.
2. Voeg `google-services.json` toe aan het project.
3. Selecteer `google-services.json` in het venster **Solution Explorer**.
4. Stel in het deelvenster **Properties** de optie Build Action in op **GoogleServicesJson**. Als u **GoogleServicesJson** niet ziet, sluit u Visual Studio, opent u het programma opnieuw, opent u het project opnieuw en probeert u het nog een keer.

    ![Build Action van GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Notification Hubs in uw project instellen

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging registreren

1. Als u migreert van Google Cloud Messaging naar Firebase, kan het bestand van het project `AndroidManifest.xml` een verouderde GCM-configuratie bevatten. Dit kan leiden tot het dupliceren van meldingen. Bewerk het bestand en verwijder de volgende regels in de `<application>` sectie, indien aanwezig:

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Voeg de volgende instructies toe **vóór het element van de toepassing**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Verzamel de volgende informatie voor uw Android-app en Notification Hub:

   * **Verbindingsreeks voor luisteren**: Kies op het dashboard in de [Azure-portal] de optie **Verbindingsreeksen weergeven**. Kopieer de verbindingsreeks `DefaultListenSharedAccessSignature` voor deze waarde.
   * **Hubnaam**: Naam van uw hub in de [Azure-portal]. Bijvoorbeeld *mynotificationhub2*.
4. Klik in het venster **Solution Explorer** met de rechtermuisknop op uw **project**, selecteer **Add** en vervolgens **Class**.
5. Maak een klasse `Constants.cs` voor uw Xamarin-project en definieer de volgende constantewaarden in de klasse. Vervang de tijdelijke aanduidingen door de waarden.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Voeg de volgende using-instructies toe aan `MainActivity.cs`:

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. Voeg de volgende eigenschappen toe aan de klasse MainActivity:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Voeg een klasse met de naam `AzureListener` toe aan uw project.
10. Voeg de volgende using-instructies toe aan `AzureListener.cs`.

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Voeg het volgende toe boven de klassen declaratie en laat uw klasse overnemen van `Java.Lang.Object` en implementeer de `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Voeg de volgende code toe in de klasse `MyFirebaseMessagingService` om de ontvangen berichten te verwerken.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Compileer** het nieuwe project.
1. **Voer uw app uit** op uw apparaat of in een geladen emulator.

## <a name="send-test-notification-from-the-azure-portal"></a>Testmeldingen verzenden vanuit Azure Portal

U kunt ontvangst van meldingen in uw app testen met de optie **Test verzenden** in [Azure-portal]. Er wordt dan een pushmelding als test naar uw apparaat verzonden.

![Azure Portal - Test verzenden](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Pushmeldingen worden gewoonlijk in een back-endservice zoals Mobile Services of ASP.NET verzonden via een compatibele bibliotheek. U kunt de REST API ook rechtstreeks gebruiken om meldingsberichten te verzenden als er geen bibliotheek beschikbaar is voor uw back-end.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meldingen uitgezonden naar al uw Android-apparaten die zijn geregistreerd bij de back-end. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke Android-apparaten, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke apparaten](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio met Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio voor Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure-portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android