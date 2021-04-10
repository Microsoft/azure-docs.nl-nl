---
title: Pushmeldingen naar Android sturen met Azure Notification Hubs en Firebase SDK versie 1.0.0-preview1
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en Google Firebase Cloud Messaging pushmeldingen verzendt naar Android-apparaten (versie 1.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 31a411cbcecab8192643f86b6b54d09ac03e7f45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581715"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Zelfstudie: Pushmeldingen verzenden naar Android-apparaten met Firebase SDK versie 1.0.0-preview1

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs en de bijgewerkte versie van de Firebase Cloud Messaging (FCM) SDK (versie 1.0.0-preview1) pushmeldingen verzendt naar een Android-toepassing. In deze zelfstudie gaat u een lege Android-app maken die pushmeldingen ontvangt via Firebase Cloud Messaging (FCM).

U kunt de voltooide code voor deze zelfstudie downloaden op [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Deze zelfstudie bestaat uit de volgende stappen:

- Een Android Studio-project maken.
- Een Firebase-project maken dat Firebase Cloud Messaging ondersteunt.
- Een Notification Hub maken.
- Verbind uw app met de hub.
- De app testen.

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/free/) voor meer informatie.

U hebt ook het volgende nodig:

- We bevelen hiervoor de nieuwste versie van [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) aan.
- De minimale ondersteuning is API Level 19.

## <a name="create-an-android-studio-project"></a>Een Android Studio-project maken

De eerste stap is een project in Android Studio te maken:

1. Start Android Studio.

2. Selecteer **Bestand**, selecteert vervolgens **Nieuw** en vervolgens **Nieuw project**.

3. Selecteer **Empty Activity** op de pagina **Choose your project** en selecteer **Next**.

4. Voer het volgende uit op de pagina **Uw project configureren**:
   1. Voer een naam in voor de toepassing.
   2. Geef een locatie op voor het opslaan van de projectbestanden.
   3. Selecteer **Finish**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Project configureren":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Een Firebase-project maken dat FCM ondersteunt

1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project als u er nog geen hebt.

2. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Firebase toevoegen":::

3. Voer het volgende uit op de pagina **Firebase toevoegen aan uw Android-app**:

   1. Voor **Android-pakketnaam** kopieert u de waarde van de **applicationId** in het bestand **build.gradle** van uw toepassing. In dit voorbeeld is het `com.fabrikam.fcmtutorial1app`.

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Pakketnaam opgeven":::

   2. Selecteer **App registreren**.

4. Selecteer **google-services.json downloaden**, sla het bestand op in de map **app** van uw project, en selecteer **Volgende**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Google-service downloaden":::

5. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Projectinstellingen":::

6. Als u het bestand **google-services.json** niet hebt gedownload in de map **app** van uw Android Studio-project, kunt u dat op deze pagina doen.

7. Ga naar het tabblad **Cloud Messaging**.

8. Kopieer de **Serversleutel** en sla deze op voor later gebruik. U gebruikt deze waarde om uw hub te configureren.

## <a name="configure-a-notification-hub"></a>Een Notification Hub configureren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Alle services** in het menu aan de linkerkant en selecteer **Notification Hubs** in de sectie **Mobiel**. Selecteer het sterpictogram naast de servicenaam om de service toe te voegen aan de sectie **FAVORIETEN** in het menu aan de linkerkant. Nadat u **Notification Hubs** hebt toegevoegd aan **FAVORIETEN**, selecteert u dit in het linkermenu.

3. Op de **Notification Hubs**-pagina selecteert u **Toevoegen** op de werkbalk.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Hub toevoegen":::

4. Voer op de pagina **Notification Hubs** het volgende uit:

   1. Voer een naam in **Notification Hub** in.

   2. Voer een naam in **Een nieuwe naamruimte maken** in. Een naamruimte bevat een of meer hubs.

   3. Selecteer een waarde uit de vervolgkeuzelijst **Locatie**. Deze waarde specificeert de locatie waar u de hub wilt maken.

   4. Selecteer een bestaande resourcegroep bij **Resourcegroep** of maak een nieuwe.

   5. Selecteer **Maken**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Hub maken":::

5. Selecteer **Meldingen** (het belpictogram) en selecteer vervolgens **Ga naar resource**. U kunt ook de lijst op de pagina **Notification Hubs** vernieuwen en uw hub selecteren.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Hub selecteren":::

6. Selecteer **Toegangsbeleid** in de lijst. Merk op dat er twee verbindingsreeksen beschikbaar zijn. Later moet u er pushmeldingen mee afhandelen.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Toegangsbeleid":::

   > [!IMPORTANT]
   > Gebruik niet het beleid **DefaultFullSharedAccessSignature** in uw toepassing. Dit beleid dient alleen in de back-end van de app te worden gebruikt.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Firebase Cloud Messaging-instellingen voor de hub configureren

1. Selecteer in het linkerdeelvenster onder **Instellingen** de optie **Google (GCM/FCM)** .

2. Voer de **serversleutel** in voor het FCM-project dat u eerder hebt opgeslagen.

3. Selecteer **Opslaan** op de werkbalk.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Serversleutel":::

4. De Azure-portal geeft een bericht weer dat de hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

Uw Notification Hub is nu geconfigureerd om te werken met Firebase Cloud Messaging. U hebt ook de verbindingsreeksen die nodig zijn om meldingen naar een apparaat te verzenden en een app te registreren voor het ontvangen van meldingen.

## <a name="connect-your-app-to-the-notification-hub"></a>Uw app verbinden met de Notification Hub

### <a name="add-google-play-services-to-the-project"></a>Google Play-services aan het project toevoegen

1. Selecteer in Android Studio in het menu de optie **Hulpprogramma’s**, en selecteer vervolgens **SDK Manager**.

2. Selecteer de doelversie van de Android SDK die wordt gebruikt in het project. Selecteer vervolgens **Pakketdetails weergeven**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK Manager":::

3. Selecteer **Google API’s** als dit nog niet is geïnstalleerd.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API's":::

4. Schakel over naar het tabblad **SDK-hulpprogramma's** . Als u Google Play Service nog niet hebt geïnstalleerd, selecteert u **Google Play Services** zoals wordt weergegeven in de onderstaande afbeelding. Selecteer vervolgens **Toepassen** om de installatie te starten. Noteer het SDK-pad om het in een later stadium te kunnen gebruiken.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play Services":::

5. Klik op **OK** zodra het dialoogvenster **Wijziging bevestigen** wordt weergegeven. De vereiste onderdelen worden geïnstalleerd met behulp van het installatieprogramma voor onderdelen. Selecteer **Voltooien** zodra de onderdelen zijn geïnstalleerd.

6. Selecteer **OK** om het dialoogvenster **Instellingen voor nieuwe projecten** te sluiten.

### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs-bibliotheken toevoegen

1. Voeg in het bestand **build.gradle** voor de app de volgende regels toe in de sectie met afhankelijkheden:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Voeg de volgende opslagplaats toe na de sectie met afhankelijkheden:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Ondersteuning voor Google Firebase toevoegen

1. Voeg de volgende invoegtoepassing toe aan het einde van het bestand als deze er nog niet staat.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Selecteer **Nu synchroniseren** op de werkbalk.

### <a name="add-code"></a>Code toevoegen

1. Maak een **NotificationHubListener**-object, dat het onderscheppen van de berichten uit Azure Notification Hubs verwerkt.

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. In de methode `OnCreate` van de klasse `MainActivity` voegt u de volgende code toe om het Notification Hubs-initialisatieproces te starten wanneer de activiteit wordt gemaakt:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. Klik in Android Studio in de menubalk op **Bouwen** en selecteer vervolgens **Project opnieuw opbouwen** om ervoor te zorgen dat uw code geen fouten bevat. Als u een foutbericht ontvangt over het pictogram **ic_launcher**, verwijdert u de volgende instructie uit het bestand AndroidManifest.xml:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Zorg ervoor dat u een virtueel apparaat hebt voor het uitvoeren van de app. Als u er geen hebt, voegt u er als volgt een toe:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Apparaatbeheer":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Virtuele apparaten":::
   3. Voer de app uit op uw geselecteerde apparaat en verifieer dat deze correct is geregistreerd in de hub.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Apparaatregistratie":::

      > [!NOTE]
      > De registratie kan mislukken bij de eerste keer starten totdat de methode `onTokenRefresh()` van de exemplaar-id-service wordt aangeroepen. Een vernieuwing moet een succesvolle registratie met de Notification Hub tot stand brengen.

## <a name="send-a-test-notification"></a>Een testmelding verzenden

U kunt als volgt pushmeldingen naar uw Notification Hub verzenden vanuit de [Azure Portal](https://portal.azure.com/):

1. Ga in de Azure Portal naar de pagina ‘Notification Hub’ voor uw hub en selecteer **Verzenden testen** in het gedeelte **Probleemoplossing**.

2. Selecteer voor **Platforms** **Android**.

3. Selecteer **Verzenden**. U ziet nog geen melding op het Android-apparaat omdat u daarop de mobiele app niet hebt uitgevoerd. Nadat u de mobiele app hebt uitgevoerd, selecteert u opnieuw **Verzenden** om de melding weer te geven.

4. Bekijk het resultaat van de bewerking in de lijst onderaan de portalpagina.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Testmelding verzenden":::

5. U ziet de melding op uw apparaat.

Pushmeldingen worden gewoonlijk in een back-endservice zoals Mobile Apps of ASP.NET verzonden met een compatibele bibliotheek. U kunt de REST API ook rechtstreeks gebruiken om meldingsberichten te verzenden als er geen bibliotheek beschikbaar is voor uw back-end.

## <a name="run-the-mobile-app-on-emulator"></a>De mobiele app uitvoeren in een emulator

Voordat pushmeldingen binnen een emulator test, moet u ervoor zorgen dat de installatiekopie van de emulator het Google API-niveau ondersteunt dat u voor uw app hebt gekozen. Als uw installatiekopie geen ondersteuning biedt voor native Google-API’s, kan de uitzondering **SERVICE_NIET_BESCHIKBAAR** worden weergegeven.

Bovendien moet uw Google-account zijn toegevoegd aan de actieve emulator onder **Instellingen** > **Accounts**. Anders kunnen pogingen om opnieuw te registreren bij FCM leiden tot de uitzondering **VERIFICATIE_MISLUKT**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Firebase Cloud Messaging gebruikt om meldingen te verzenden naar alle Android-apparaten die zijn geregistreerd bij de service. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke apparaten:

> [!div class="nextstepaction"]
>[Zelfstudie: Meldingen verzenden naar specifieke gebruikers](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Het volgende is een lijst met andere zelfstudies voor het verzenden van meldingen:

- Azure Mobile Apps: Zie [Pushmeldingen toevoegen aan uw iOS-app](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push) voor een voorbeeld van hoe u meldingen verzendt vanuit een back-end van Mobile Apps die is geïntegreerd met Notification Hubs.

- ASP.NET: [Gebruik Notification Hubs om pushmeldingen naar gebruikers te verzenden](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Azure Notification Hubs Java SDK: zie [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (Notification Hubs gebruiken vanuit Java) voor het verzenden van meldingen vanuit Java. Dit is getest in Eclipse voor Android-ontwikkeling.

- PHP: zie [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (Notification Hubs gebruiken vanuit PHP).