---
title: Meldingen pushen naar specifieke Android-apparaten met Azure Notification Hubs en Google Cloud Messaging | Microsoft Docs
description: Leer hoe u pushmeldingen kunt verzenden naar specifieke Android-apparaten met behulp van Azure Notification Hubs en Google Cloud Messaging.
services: notification-hubs
documentationcenter: android
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
ms.openlocfilehash: c0c0018ac3007f77da820b9b0cecbb69c68bef31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92308298"
---
# <a name="tutorial-send-push-notifications-to-specific-android-devices-using-google-cloud-messaging-deprecated"></a>Zelfstudie: Pushmeldingen verzenden naar specifieke Android-apparaten met Google Cloud Messaging (afgeschaft)

> [!WARNING]
> Sinds 10 april 2018 heeft Google Google Cloud Messaging (GCM) afgeschaft. De GCM-server en de client-API's zijn afgeschaft en worden verwijderd op 29 mei 2019. Raadpleeg voor meer informatie de [Veelgestelde vragen over GCM en FCM](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs meldingen voor belangrijk nieuws verzendt naar een Android-app. Als u klaar bent, kunt u zich registreren voor nieuwscategorieën waarin u geïnteresseerd bent en alleen voor die categorieën pushmeldingen ontvangen als er belangrijk nieuws is. Dit scenario is een algemeen patroon voor veel apps die meldingen moeten verzenden naar groepen gebruikers die eerder hebben aangegeven in bepaalde onderwerpen geïnteresseerd te zijn, zoals een RSS-lezer, apps voor muziekfans, enzovoort.

Broadcast-scenario's zijn mogelijk door een of meer *tags* (of labels) toe te voegen wanneer u een registratie maakt in Notifications Hub. Wanneer meldingen worden verzonden naar een tag, ontvangen alle apparaten die zich hebben geregistreerd voor de tag de melding. Omdat tags niet meer dan tekenreeksen zijn, hoeven ze niet vooraf te worden opgesteld. Zie [Notification Hubs-routering en tagexpressies](notification-hubs-tags-segment-push-message.md) voor meer informatie over tags.

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Categorieselectie toevoegen aan de mobiele app
> * Geregistreerd voor meldingen met tags.
> * Getagde meldingen verzenden
> * De app testen

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie bouwt voort op de app die u hebt gemaakt in de zelfstudie [Pushmeldingen verzenden naar Android-apparaten met behulp van Azure Notification Hubs en Google Cloud Messaging][get-started]. Voordat u aan deze zelfstudie begint, volgt u de [Zelfstudie: Pushmeldingen naar Android-apparaten met Azure Notification Hubs en Google Cloud Messaging][get-started].

## <a name="add-category-selection-to-the-app"></a>Categorieselectie toevoegen aan de app

De eerste stap is het toevoegen van UI-elementen aan de bestaande hoofdactiviteit, zodat gebruikers categorieën kunnen selecteren waarvoor ze zich willen registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, wordt er een apparaatregistratie gemaakt in uw meldingshub, met de geselecteerde categorieën als tags.

1. Open het `res/layout/activity_main.xml file` en vervang de inhoud door het volgende:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Open het bestand `res/values/strings.xml` en voeg de volgende regels toe:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    De grafische indeling van `main_activity.xml` ziet er uit als in de volgende afbeelding:

    ![Schermafbeelding van een ontwikkelomgeving, waarbij een app-scherm wordt weergegeven. In de app worden de nieuwscategorieën weergegeven die zijn toegevoegd aan de code.][A1]
3. Maak een `Notifications`-klasse in hetzelfde pakket als uw `MainActivity`-klasse.

    ```java
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName,
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM,
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```

    Deze klasse gebruik de lokale opslag voor het opslaan van de nieuwscategorieën die dit apparaat moet ontvangen. De klasse bevat ook methoden om te registreren voor deze categorieën.
4. Verwijder in de `MainActivity`-klasse uw persoonlijke velden voor `NotificationHub` en `GoogleCloudMessaging` en voeg een veld toe voor `Notifications`:

    ```java
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Vervolgens verwijdert u in de `onCreate`-methode de initialisatie van het veld `hub` en de `registerWithNotificationHubs`-methode. Voeg daarna de volgende regels toe om een exemplaar van de `Notifications`-klasse te initialiseren.

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Controleer of de naam van de hub en de verbindingsreeks goed zijn ingesteld in de klasse NotificationSettings.

    > [!NOTE]
    > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor listen-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

6. Voeg nu deze imports toe:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
7. Voeg de volgende methode `subscribe` toe voor het afhandelen van het klikken op de knop Subscribe:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Met deze methode maakt u een lijst met categorieën en gebruikt u de klasse `Notifications` om de lijst op te slaan in de lokale opslag en de bijbehorende tags te registreren bij uw Notification Hub. Wanneer categorieën worden gewijzigd, wordt de registratie opnieuw gemaakt met de nieuwe categorieën.

Uw app kan nu een set categorieën opslaan in de lokale opslag op het apparaat en deze registreren bij de meldingshub wanneer de gebruiker de selectie van categorieën wijzigt.

## <a name="register-for-notifications"></a>Registreren voor meldingen

Met deze stappen registreert u zich tijdens het opstarten bij de meldingshub met behulp van de categorieën die zijn opgeslagen in de lokale opslag.

> [!NOTE]
> Omdat de registrationId die wordt toegewezen door GCM (Google Cloud Messaging) op elk moment kan veranderen, moet u zich regelmatig registreren voor meldingen om fouten te voorkomen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u regelmatig uitvoert (meer dan één keer per dag), kunt u de registratie waarschijnlijk overslaan om bandbreedte te besparen als er minder dan een dag is verstreken sinds de vorige registratie.

1. Voeg de volgende code toe aan het einde van de methode `onCreate` in de klasse `MainActivity`:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Deze code zorgt ervoor dat wanneer de app wordt gestart, de categorieën worden opgehaald uit de lokale opslag en registratie voor deze categorieën wordt aangevraagd.
2. Werk vervolgens de methode `onStart()` van de klasse `MainActivity` als volgt bij:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Met deze code wordt de hoofdactiviteit bijgewerkt op basis van de status van eerder opgeslagen categorieën.

De app is nu klaar en kan een set categorieën opslaan in de lokale opslag op het apparaat en deze registreren bij de meldingshub wanneer de gebruiker de selectie van categorieën wijzigt. U gaat nu een back-end definiëren die categoriemeldingen naar deze app kan verzenden.

## <a name="send-tagged-notifications"></a>Getagde meldingen verzenden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>De app testen

1. Voer in Android Studio de app uit op uw Android-apparaat of in een emulator. De UI van de app biedt een reeks schakelopties waarmee u de categorieën kunt kiezen waarop u zich wilt abonneren.
2. Zet een of meer categorieën op On en klik vervolgens op **Subscribe**. De app zet de geselecteerde categorieën om in tags en vraagt bij Notification Hubs een nieuwe apparaatregistratie aan voor de geselecteerde tags. De geregistreerde categorieën worden geretourneerd en weergegeven in een pop-upmelding.

    ![Abonneren op categorieën](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Voer de .NET-console-app uit, om meldingen voor elke categorie te verzenden. Meldingen voor de geselecteerde categorieën worden weergegeven als pop-upmeldingen.

    ![Meldingen over technologienieuws](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u broadcastmeldingen verzonden naar specifieke Android-apparaten die zijn geregistreerd voor de categorieën. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke gebruikers:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)