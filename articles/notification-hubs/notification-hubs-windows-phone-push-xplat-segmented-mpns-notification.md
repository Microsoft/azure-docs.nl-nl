---
title: Pushmeldingen verzenden naar specifieke Windows-telefoons met Azure Notification Hubs | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar specifieke (niet alle) apparaten met Windows Phone 8 of Windows Phone 8.1 die zijn geregistreerd bij de app-back-end.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998234"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar specifieke Windows-telefoons met Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

In deze zelfstudie ziet u hoe u met Azure Notification Hubs pushmeldingen kunt verzenden naar specifieke apparaten met Windows Phone 8 of Windows Phone 8.1. Als u ontwikkelt voor Windows Phone 8.1 (zonder Silverlight), raadpleegt u de [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)-versie van deze zelfstudie.

U maakt dit scenario mogelijk door een of meer *tags* (of labels) toe te voegen wanneer u een registratie maakt in Notifications Hub. Wanneer meldingen worden verzonden naar een tag, ontvangen alle apparaten die zich hebben geregistreerd voor de tag de melding. Zie [Tags in registraties](notification-hubs-tags-segment-push-message.md) voor meer informatie over tags.

> [!NOTE]
> De Notification Hubs Windows Phone SDK biedt geen ondersteuning voor het gebruik van Windows Push Notification Service (WNS) met Windows Phone 8.1 Silverlight-apps. Als u WNS (in plaats van MPNS) met Windows Phone 8.1 Silverlight-apps wilt gebruiken, raadpleegt u de Notification Hubs - Windows Phone Silverlight tutorial (Notification Hubs: zelfstudie voor Windows Phone Silverlight) waarin REST API's worden gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Categorieselectie toevoegen aan de mobiele app
> * Registreren voor meldingen met tags
> * Getagde meldingen verzenden
> * De app testen

## <a name="prerequisites"></a>Vereisten

U moet de [Zelfstudie: Pushmeldingen verzenden naar Windows Phone-apps met Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md). In deze zelfstudie werkt u de mobiele toepassing zij, zodat u zich kunt registreren voor nieuwscategorieën waarin u geïnteresseerd bent en u alleen voor die categorieën pushmeldingen ontvangt als er belangrijk nieuws is.

## <a name="add-category-selection-to-the-mobile-app"></a>Categorieselectie toevoegen aan de mobiele app

De eerste stap is het toevoegen van UI-elementen aan de bestaande hoofdpagina, zodat gebruikers categorieën kunnen selecteren waarvoor ze zich willen registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, wordt er een apparaatregistratie gemaakt in uw meldingshub, met de geselecteerde categorieën als tags.

1. Open het bestand `MainPage.xaml` en vervang de `Grid`-elementen `TitlePanel` en `ContentPanel` door de volgende code:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Voeg een klasse met de naam `Notifications` toe aan het project. Voeg de modifier `public` toe aan de klassedefinitie. Voeg vervolgens de volgende `using`-instructies toe aan het nieuwe codebestand:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Kopieer de volgende code naar de nieuwe klasse `Notifications`:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Deze klasse gebruikt de geïsoleerde opslag voor het opslaan van de nieuwscategorieën die dit apparaat moet ontvangen. De klasse bevat ook methoden om te registreren voor deze categorieën met behulp van een [sjabloon](notification-hubs-templates-cross-platform-push-messages.md).
4. Voeg in het projectbestand `App.xaml.cs` de volgende eigenschap toe aan de klasse `App`. Vervang de tijdelijke aanduidingen `<hub name>` en `<connection string with listen access>` door de naam van de meldingshub en de verbindingsreeks voor *DefaultFullSharedAccessSignature* die u eerder hebt opgevraagd.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor listen-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

5. Voeg in `MainPage.xaml.cs` de volgende regel toe:

    ```csharp
    using Windows.UI.Popups;
    ```
6. Voeg in het projectbestand MainPage.xaml.cs de volgende methode toe:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Met deze methode maakt u een lijst met categorieën en gebruikt u de klasse `Notifications` om de lijst op te slaan in de lokale opslag en de bijbehorende tags te registreren bij uw Notification Hub. Wanneer categorieën worden gewijzigd, wordt de registratie opnieuw gemaakt met de nieuwe categorieën.

Uw app kan nu een set categorieën opslaan in de lokale opslag op het apparaat en deze registreren bij de meldingshub wanneer de gebruiker de selectie van categorieën wijzigt.

## <a name="register-for-notifications"></a>Registreren voor meldingen

Met deze stappen registreert u zich tijdens het opstarten bij de meldingshub met behulp van de categorieën die zijn opgeslagen in de lokale opslag.

> [!NOTE]
> Omdat de kanaal-URI die wordt toegewezen door MPNS (Microsoft Push Notification Service) op elk moment kan veranderen, moet u zich regelmatig registreren voor meldingen om fouten te voorkomen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u regelmatig uitvoert (meer dan één keer per dag), kunt u de registratie waarschijnlijk overslaan om bandbreedte te besparen als er minder dan een dag is verstreken sinds de vorige registratie.

1. Open het bestand App.xaml.cs en voeg de modifier `async` toe aan de methode `Application_Launching` en vervang de registratiecode van Notification Hubs die u hebt toegevoegd in [Aan de slag met Notification Hubs] door de volgende code:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Deze code zorgt ervoor dat wanneer de app wordt gestart, de categorieën worden opgehaald uit de lokale opslag en registratie voor deze categorieën wordt aangevraagd.
2. Voeg in het projectbestand MainPage.xaml.cs de volgende code toe om de methode `OnNavigatedTo` te implementeren:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Met deze code wordt de hoofdpagina bijgewerkt op basis van de status van eerder opgeslagen categorieën.

De app is nu klaar en kan een set categorieën opslaan in de lokale opslag op het apparaat en deze registreren bij de meldingshub wanneer de gebruiker de selectie van categorieën wijzigt. U gaat nu een back-end definiëren die categoriemeldingen naar deze app kan verzenden.

## <a name="send-tagged-notifications"></a>Getagde meldingen verzenden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>De app testen

1. Druk in Visual Studio op F5 om de app te compileren en te starten.

    ![Mobiele app met categorieën][1]

    De UI van de app biedt een reeks schakelopties waarmee u de categorieën kunt kiezen waarop u zich wilt abonneren.
2. Zet een of meer categorieën op On en klik vervolgens op **Subscribe**.

    De app zet de geselecteerde categorieën om in tags en vraagt bij Notification Hubs een nieuwe apparaatregistratie aan voor de geselecteerde tags. De geregistreerde categorieën worden geretourneerd en weergegeven in een dialoogvenster.

    ![Bericht dat abonneren is gelukt][2]
3. Na ontvangst van een bevestiging dat het abonnement voor uw categorieën is voltooid, voert u de console-app uit om meldingen voor elke categorie te verzenden. Controleer of u alleen een melding ontvangt voor de categorieën waarop u zich hebt geabonneerd.

    ![Melding][3]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u pushmeldingen kunt verzenden naar specifieke apparaten door een tag te koppelen aan hun registraties. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke gebruikers die mogelijk meerdere apparaten gebruiken, gaat u verder met de volgende zelfstudie: 

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke gebruikers](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Aan de slag met Notification Hubs]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
