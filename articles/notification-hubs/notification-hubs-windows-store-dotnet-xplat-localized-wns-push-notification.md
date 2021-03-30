---
title: Gelokaliseerde meldingen verzenden naar Windows-apps met Azure Notification Hubs | Microsoft Docs
description: Leer hoe u met behulp van Azure Notification Hubs gelokaliseerde meldingen voor belangrijk nieuws kunt verzenden.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: d91320d8f78942ca916084cbf29a88818d95f531
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92314625"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>Zelfstudie: Gelokaliseerde pushmeldingen verzenden naar Windows-apps met Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Overzicht

Deze zelfstudie laat zien hoe u gelokaliseerde pushmeldingen naar mobiele apparaten kunt versturen die zijn geregistreerd bij de Notification Hubs-service. U kunt in de zelfstudie toepassingen bijwerken die zijn gemaakt de [Zelfstudie: Meldingen verzenden naar specifieke apparaten (Universeel Windows-platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) ter ondersteuning van de volgende scenario's:

- In de Windows Store-app kunnen clientapparaten een taal opgeven en zich abonneren op verschillende nieuwscategorieën.
- De back-endapp verstuurt meldingen met behulp van de **tag**- en **sjabloon**-functies van Azure Notification Hubs.

Wanneer u de zelfstudie hebt voltooid, kunt u in de mobiele toepassing categorieën kiezen waarvoor u zich wilt registreren, evenals de taal waarin u meldingen voor die nieuwscategorieën wilt ontvangen. De back-endtoepassing verstuurt meldingen die per taal en apparaat zijn gelokaliseerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Windows-app bijwerken om landinstellingen te ondersteunen
> * Back-endapp bijwerken om gelokaliseerde meldingen te verzenden
> * De app testen

## <a name="prerequisites"></a>Vereisten

U moet de [Zelfstudie: Meldingen verzenden naar specifieke apparaten (Universeel Windows-platform).](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

In de zelfstudie [Meldingen verzenden naar UWP-apps (Universal Windows Platform) met behulp van Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) bouwt u een app die gebruikmaakt van **tags** om zich te abonneren op meldingen voor verschillende nieuws **categorieën** . In deze zelfstudie gebruikt u de **sjabloon**-functie van Notification Hubs om eenvoudig **gelokaliseerde** meldingen van belangrijk nieuws te bezorgen.

In deze context zijn sjablonen een manier om de indeling op te geven waarin een specifiek apparaat een melding moet ontvangen. De sjabloon bepaalt de exacte indeling van de payload door te verwijzen naar eigenschappen die deel uitmaken van het bericht dat is verzonden door uw back-endapp. In deze zelfstudie verzendt de back-endtoepassing een bericht met alle ondersteunde talen:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

De apparaten registreren zich bij een sjabloon die naar de juiste eigenschap verwijst. Een Windows Store-app die een pop-upbericht in het Engels wil ontvangen, registreert zich bijvoorbeeld voor de volgende sjabloon met alle bijbehorende tags:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Zie [Push Templates](notification-hubs-templates-cross-platform-push-messages.md) (Pushsjablonen) voor meer informatie over sjablonen.

## <a name="update-windows-app-to-support-locale-information"></a>Windows-app bijwerken om landinstellingen te ondersteunen

1. Open in Visual Studio de oplossing die u hebt gemaakt voor de [Zelfstudie: Meldingen verzenden naar specifieke apparaten (Universeel Windows-platform).](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)
2. Werk het bestand `MainPage.xaml` van de oplossing bij met een keuzelijst met invoervak voor landinstellingen:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. Voeg in de klasse `Notifications` een landinstellingenparameter toe aan de methoden `StoreCategoriesAndSubscribe` en `SubscribeToCategories`.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Roep in plaats van methode `RegisterNativeAsync` methode `RegisterTemplateAsync` aan. U registreert een specifieke indeling voor de melding waarin de sjabloon afhankelijk is van de landinstelling. U geeft ook een naam op voor de sjabloon ('localizedWNSTemplateExample'), aangezien u misschien meer dan één sjabloon wilt registreren (bijvoorbeeld één voor pop-upmeldingen en één voor tegels). U moet ook een naam opgeven om sjablonen te kunnen bijwerken of verwijderen.

    Als op een apparaat meerdere sjablonen met dezelfde tag zijn geregistreerd, worden er meerdere meldingen verstuurd naar het apparaat (één voor elke sjabloon) als er een bericht binnenkomt dat gericht is op deze tag. Dit gedrag is handig wanneer hetzelfde logische bericht moet resulteren in meerdere visual meldingen (bijvoorbeeld een badge en een pop-up weergeven in een Windows Store-toepassing).
4. Voeg de volgende methode toe om de opgeslagen landinstelling op te halen:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. Werk in het bestand `MainPage.xaml.cs` de knop-klikhandler bij voor het ophalen van de huidige waarde van de keuzelijst met invoervak voor landinstellingen en geef deze waarde door aan de aanroep van de klasse `Notifications`:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Ten slotte werkt u in het bestand `App.xaml.cs` de methode `InitNotificationsAsync` bij om de landinstelling op te halen en gebruikt u deze bij het abonneren:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>De UWP-toepassing uitvoeren

1. Voer de Universal Windows Platform-toepassing uit. Wacht tot u het bericht ziet dat de **registratie is gelukt**.

    ![Registratie van mobiele toepassing](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Selecteer de **categorieën** en **locale** (landinstelling), en klik op **Subscribe**. De app zet de geselecteerde categorieën om in tags en vraagt bij Notification Hubs een nieuwe apparaatregistratie aan voor de geselecteerde tags.

    ![Mobiele toepassing](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. U ziet een **bevestigingsbericht** over de **abonnementen**.

    ![Abonnementsbericht](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Console-app bijwerken om gelokaliseerde meldingen te verzenden

Wanneer u sjabloonmeldingen verstuurt, hoeft u alleen een set eigenschappen op te geven. In deze zelfstudie verstuurt de back-endtoepassing de set eigenschappen met de gelokaliseerde versie van het actuele nieuws, bijvoorbeeld:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

In deze sectie gaat u het consoletoepassingsproject in de oplossing bijwerken. Wijzig de methode `SendTemplateNotificationAsync` in de console-app die u eerder hebt gemaakt met de volgende code:

> [!IMPORTANT]
> Geef in de code de naam en verbindingsreeks met volledige toegang op voor uw meldingshub.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Met deze eenvoudige aanroep wordt het gelokaliseerde nieuwsbericht bezorgd op **alle** apparaten, ongeacht het platform. Dit kan doordat uw Notification Hub de juiste payload voor het platform samenstelt en verstuurt naar alle apparaten die zijn geabonneerd op een bepaalde tag.

## <a name="run-console-app-to-send-localized-notification"></a>Console-app uitvoeren om gelokaliseerde meldingen te verzenden
Voer de **console-app** uit om meldingen te verzenden voor elke categorie en in elke ondersteunde taal. Controleer of u alleen een melding krijgt voor de categorieën waarop u zich hebt geabonneerd en of het bericht overeenkomt met de landinstelling die u hebt geselecteerd.

![Meldingen](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u gelokaliseerde pushmeldingen kunt verzenden naar specifieke apparaten door een tag te koppelen aan hun registraties. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke gebruikers die mogelijk meer dan één apparaat gebruiken, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)