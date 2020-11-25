---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027902"
---
In deze sectie verzendt u het laatste nieuws als gelabelde sjabloonmeldingen vanuit een .NET-console-app.

1. Maak in Visual Studio een nieuwe Visual C#-consoletoepassing:
    1. Selecteer in het menu **Bestand** > **Nieuw** > **Project**.
    1. Selecteer bij **Een nieuw project maken** de **Consoletoepassing (.NET Framework)** voor C# in de lijst met sjablonen en selecteer **Volgende**.
    1. voer een naam voor de app in.
    1. Bij **Oplossing** kiest u **Aan oplossing toevoegen** en selecteert u **Maken** om het project te maken.

1. Selecteer **Tools** > **NuGet Package Manager** > **Package Manager Console** en voer vervolgens in het consolevenster de volgende opdracht uit:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Met deze actie wordt een verwijzing toegevoegd aan de Azure Notification Hubs SDK met het [Microsoft.Azure.NotificationHubs]-pakket.

1. Open het bestand *Program.cs* en voeg de volgende `using`-instructie toe:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Voeg in de klasse `Program` de volgende methode toe of vervang de methode als deze al bestaat:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Met deze code wordt een sjabloonmelding verzonden voor elk van de zes labels in de tekenreeksmatrix. Het gebruik van labels zorgt ervoor dat apparaten meldingen ontvangen voor de geregistreerde categorieën.

1. Vervang in de voorgaande code de tijdelijke aanduidingen `<hub name>` en `<connection string with full access>` door de naam van de meldingenhub en de verbindingsreeks voor *DefaultFullSharedAccessSignature* uit het dashboard van de meldingenhub.

1. Voeg de volgende regels toe in de methode `Main()`:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Bouw de console-app.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/