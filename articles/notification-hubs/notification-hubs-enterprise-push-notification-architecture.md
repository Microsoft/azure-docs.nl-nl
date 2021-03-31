---
title: Notification Hubs Enter prise-push architectuur
description: Meer informatie over het gebruik van Azure Notification Hubs in een bedrijfs omgeving
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 018315b7ed468e24fb922337848d14703ffdcd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89003623"
---
# <a name="enterprise-push-architectural-guidance"></a>Hulp voor architectuur via pushmeldingen van het bedrijf

De huidige ondernemingen worden geleidelijk verplaatst om mobiele toepassingen te maken voor hun eind gebruikers (extern) of voor de werk nemers (intern). Ze hebben bestaande back-end-systemen die zijn ingesteld als mainframes of sommige LoB-toepassingen, die moeten worden geïntegreerd in de architectuur van de mobiele toepassing. In deze hand leiding vindt u informatie over de beste oplossing voor het uitvoeren van deze integratie om mogelijke oplossingen te adviseren voor veelvoorkomende scenario's.

Een regel matige vereiste is voor het verzenden van een push melding naar de gebruikers via hun mobiele toepassing wanneer er sprake is van een belang rijke gebeurtenis op de back-end-systemen. Bijvoorbeeld: een Bank klant die de Bank-app van een iPhone heeft, wil een melding ontvangen wanneer een debetbedrag boven een bepaald bedrag van het account of een intranet scenario wordt gemaakt, waarbij een werk nemer van de afdeling Financiën die een app voor het goed keuren van een budget heeft op een Windows Phone wil worden gewaarschuwd wanneer de goedkeurings aanvraag wordt ontvangen.

De Bank rekening of goedkeurings verwerking wordt waarschijnlijk uitgevoerd in een back-end-systeem, dat een push naar de gebruiker moet initiëren. Er kunnen meerdere dergelijke back-end-systemen zijn, die allemaal dezelfde soort logica moeten bouwen om te pushen wanneer een gebeurtenis een melding activeert. De complexiteit is het integreren van verschillende back-end-systemen in combi natie met één push systeem waarbij de eind gebruikers mogelijk zijn geabonneerd op verschillende meldingen en er zelfs meerdere mobiele toepassingen kunnen zijn. Bijvoorbeeld mobiele apps in het intranet waarbij één mobiele toepassing meldingen van meerdere dergelijke back-upsystemen wil ontvangen. De back-end-systemen weten of hoeven geen push semantiek/technologie te hebben, dus een veelvoorkomende oplossing is een onderdeel te introduceren, waarmee de back-end-systemen worden gecontroleerd op belang rijke gebeurtenissen en die verantwoordelijk is voor het verzenden van push berichten naar de client.

Een betere oplossing is het gebruik van Azure Service Bus-onderwerp/-abonnements model, waardoor de complexiteit wordt gereduceerd en de oplossing schaalbaar wordt.

Hier volgt de algemene architectuur van de oplossing (gegeneraliseerd met meerdere mobiele apps, maar ook van toepassing als er slechts één mobiele app is)

## <a name="architecture"></a>Architectuur

![Diagram van de bedrijfs architectuur met de stroom gebeurtenissen, abonnementen en push berichten.][1]

Het sleutel gedeelte in dit architectuur diagram is Azure Service Bus, dat een programmeer model voor onderwerpen/abonnementen bevat (meer op het [Service Bus pub/sub-programmering]). De ontvanger, in dit geval de mobiele back-end (meestal [Azure Mobile Service], die een push naar de mobiele apps initieert) ontvangt geen berichten rechtstreeks van de back-end-systemen, maar een tussenliggende abstractie laag die wordt geleverd door [Azure service bus], waarmee mobiele back-end berichten van een of meer back-upsystemen kan ontvangen. Er moet een Service Bus onderwerp worden gemaakt voor elk van de back-end-systemen, bijvoorbeeld account, HR, Finance, wat in principe ' Topics ' is, en die berichten initieert om te worden verzonden als push melding. De back-end-systemen verzenden berichten naar deze onderwerpen. Een mobiele back-end kan zich abonneren op een of meer van deze onderwerpen door een Service Bus-abonnement te maken. Hiermee wordt de mobiele back-end gemachtigd om een melding van het bijbehorende back-end-systeem te ontvangen. Mobiele back-end wordt geluisterd naar berichten op hun abonnementen en zodra een bericht binnenkomt, wordt dit teruggezet en wordt het als een melding verzonden naar de notification hub. Notification hubs leveren uiteindelijk het bericht af voor de mobiele app. Hier volgt een lijst met belang rijke onderdelen:

1. Back-end systemen (LoB/verouderde systemen)
   * Maakt Service Bus onderwerp
   * Bericht verzenden
1. Mobiele back-end
   * Hiermee maakt u een service abonnement
   * Ontvangt bericht (van back-end-systeem)
   * Hiermee wordt een melding verzonden naar clients (via Azure notification hub)
1. Mobiele toepassing
   * Melding ontvangen en weer geven

### <a name="benefits"></a>Voordelen

1. De ontkoppeling tussen de ontvanger (Mobile App/service via notification hub) en de afzender (back-end-systemen) maakt het mogelijk dat aanvullende back-end-systemen worden geïntegreerd met een minimale wijziging.
1. Het maakt ook het scenario van meerdere mobiele apps in staat om gebeurtenissen van een of meer back-upsystemen te ontvangen.  

## <a name="sample"></a>Voorbeeld

### <a name="prerequisites"></a>Vereisten

Voltooi de volgende zelf studies om vertrouwd te raken met de concepten en de algemene procedures voor het maken van & configuratie:

1. [Service Bus pub/sub-programmering] : in deze zelf studie worden de details van het werken met Service Bus onderwerpen/abonnementen beschreven, hoe u een naam ruimte maakt om onderwerpen/abonnementen te bevatten, & berichten van deze bestanden kunnen verzenden.
2. [Notification hubs-Windows Universal-zelf] studie: in deze zelf studie wordt uitgelegd hoe u een Windows Store-app instelt en hoe u notification hubs kunt registreren en vervolgens meldingen kunt ontvangen.

### <a name="sample-code"></a>Voorbeeldcode

De volledige voorbeeld code is beschikbaar op [Notification hub]-voor beelden. Het is onderverdeeld in drie onderdelen:

1. **EnterprisePushBackendSystem**

    a. Dit project maakt gebruik van het NuGet-pakket **WindowsAzure. ServiceBus** en is gebaseerd op [Service Bus pub/sub-programmering].

    b. Deze toepassing is een eenvoudige C#-console-app voor het simuleren van een LoB-systeem, waarmee het bericht wordt gestart dat aan de mobiele app moet worden geleverd.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic` wordt gebruikt om het Service Bus onderwerp te maken.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage` wordt gebruikt om de berichten naar dit Service Bus onderwerp te verzenden. Met deze code wordt regel matig een reeks wille keurige berichten naar het onderwerp verzonden voor het doel van het voor beeld. Normaal gesp roken is er een back-end-systeem dat berichten verzendt wanneer er zich een gebeurtenis voordoet.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. In dit project worden de taken *WindowsAzure. ServiceBus* en **micro soft. Web. webjobs gebruikt. Publiceer** NuGet-pakketten en is gebaseerd op [Service Bus pub/sub-programmering].

    b. De volgende console-app wordt uitgevoerd als een [Azure-Webtaak] , omdat deze continu moet worden uitgevoerd om te Luis teren naar berichten van de LOB/back-end-systemen. Deze toepassing maakt deel uit van uw mobiele back-end.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription` wordt gebruikt om een Service Bus-abonnement te maken voor het onderwerp waar het back-upsysteem berichten verzendt. Afhankelijk van het bedrijfs scenario maakt dit onderdeel een of meer abonnementen op de bijbehorende onderwerpen (er kunnen bijvoorbeeld enkele berichten worden ontvangen van het HR-systeem, sommige van het financiële systeem, enzovoort)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification` wordt gebruikt om het bericht in het onderwerp te lezen met behulp van het-abonnement. als de Lees bewerking is geslaagd, voert u een melding in (in het voorbeeld scenario is een melding van een Windows-systeem eigen pop-up) om te worden verzonden naar de mobiele toepassing met behulp van Azure Notification Hubs.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Als u deze app als **Webtaak** wilt publiceren, klikt u met de rechter muisknop op de oplossing in Visual Studio en selecteert u **publiceren als Webtaak**

    ![Scherm opname van de opties die met de rechter muisknop worden weer gegeven met publiceren als Azure-Webtaak die in het rood wordt beschreven.][2]

    f. Selecteer uw publicatie profiel en maak een nieuwe Azure-WebSite als deze nog niet bestaat, die als host fungeert voor deze Webtaak en wanneer u de WebSite vervolgens **publiceert**.

    :::image type="complex" source="./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png" alt-text="Scherm opname van de werk stroom voor het maken van een site in Azure.":::
    Scherm afbeelding van het dialoog venster Publish Web met de optie Microsoft Azure Websites geselecteerd, een groene pijl die verwijst naar het dialoog venster bestaande website selecteren met de nieuwe optie die wordt beschreven in rood en een groene pijl die verwijst naar het dialoog venster site maken op Microsoft Azure met de site naam en de opties voor het maken van een rood kader.
    :::image-end:::

    g. Configureer de taak zo dat deze continu wordt uitgevoerd. Als u zich aanmeldt bij de [Azure Portal] , moet u er ongeveer als volgt uitzien:

    ![Scherm opname van de Azure-Portal met de Enter prise push back-end-webtaken die worden weer gegeven en de naam, planning en logboeken waarden die in het rood worden beschreven.][4]

3. **EnterprisePushMobileApp**

    a. Deze toepassing is een Windows Store-toepassing, die pop-upmeldingen ontvangt van de Webtaak die wordt uitgevoerd als onderdeel van uw mobiele back-end en die wordt weer gegeven. Deze code is gebaseerd op de [universele zelf studie over notification hubs-Windows].  

    b. Zorg ervoor dat uw toepassing is ingeschakeld voor het ontvangen van pop-upmeldingen.

    c. Zorg ervoor dat de volgende Notification Hubs registratie code wordt aangeroepen bij het opstarten van de app (na het vervangen van de `HubName` waarden en) `DefaultListenSharedAccessSignature` :

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Het voorbeeld uitvoeren

1. Zorg ervoor dat de Webtaak wordt uitgevoerd en is gepland om continu te worden uitgevoerd.
2. Voer de **EnterprisePushMobileApp** uit, waarmee de Windows Store-app wordt gestart.
3. Voer de **EnterprisePushBackendSystem** -console toepassing uit die de LOB-back-end simuleert en begint met het verzenden van berichten. de pop-upmeldingen worden weer gegeven zoals in de volgende afbeelding:

    ![Scherm opname van een console waarop de Enter prise push back-end-systeem-app wordt uitgevoerd en het bericht dat wordt verzonden door de app.][5]

4. De berichten zijn oorspronkelijk verzonden naar Service Bus-onderwerpen, die door Service Bus abonnementen in uw Webtaak worden bewaakt. Zodra een bericht is ontvangen, is er een melding gemaakt en verzonden naar de mobiele app. U kunt de logboeken van de Webtaak bekijken om de verwerking te bevestigen wanneer u naar de koppeling Logboeken in [Azure Portal] voor uw webtaken gaat:

    ![Scherm afbeelding van het dialoog venster Details van doorlopende Webtaak, met het bericht dat in een rood kader wordt verzonden.][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Voor beelden van Notification hub]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: ../service-bus-messaging/service-bus-messaging-overview.md
[Service Bus pub/sub-programmering]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[Azure-Webtaak]: ../app-service/webjobs-create.md
[Notification Hubs-Windows Universal-zelf studie]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Azure-portal]: https://portal.azure.com/
