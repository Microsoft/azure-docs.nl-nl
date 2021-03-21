---
title: Azure Notification Hubs Secure push voor Windows
description: Meer informatie over het verzenden van beveiligde push meldingen in Azure. Codevoorbeelden geschreven in C# met .NET API.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019414"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Beveiligde push meldingen verzenden vanuit Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Overzicht

Met ondersteuning voor push meldingen in Microsoft Azure kunt u toegang krijgen tot een gebruiks vriendelijke, geschaalde push-infra structuur met meerdere platforms, waardoor de implementatie van push meldingen voor zowel consumenten als bedrijfs toepassingen voor mobiele platforms aanzienlijk wordt vereenvoudigd.

Vanwege regelgeving of beveiligings beperkingen kan het gebeuren dat een toepassing iets wil toevoegen in de melding die niet kan worden verzonden via de standaard infra structuur voor push meldingen. In deze zelf studie wordt beschreven hoe u dezelfde ervaring kunt krijgen door gevoelige informatie te verzenden via een beveiligde, geverifieerde verbinding tussen het client apparaat en de back-end van de app.

Op hoog niveau is de stroom als volgt:

1. De back-end van de app:
   * Slaat een beveiligde Payload op in de back-enddatabase.
   * Hiermee wordt de ID van deze melding naar het apparaat verzonden (er worden geen beveiligde gegevens verzonden).
2. De app op het apparaat wanneer de melding wordt ontvangen:
   * Het apparaat neemt contact op met de back-end die de beveiligde Payload aanvraagt.
   * De app kan de payload weer geven als een melding op het apparaat.

Het is belang rijk te weten dat in de voor gaande stroom (en in deze zelf studie) wordt aangenomen dat het apparaat een verificatie token opslaat in lokale opslag nadat de gebruiker zich heeft aangemeld. Dit garandeert een volledig naadloze ervaring, omdat het apparaat de beveiligde payload van de melding kan ophalen met dit token. Als uw toepassing geen verificatie tokens opslaat op het apparaat of als deze tokens verlopen zijn, moet de app bij het ontvangen van de melding een algemene melding weer geven waarin de gebruiker wordt gevraagd de app te starten. De app verifieert vervolgens de gebruiker en toont de meldings lading.

Deze zelf studie laat zien hoe u een push melding veilig verzendt. De zelf studie bouwt voort op de zelf studie [gebruikers melden](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , daarom moet u eerst de stappen in deze zelf studie volt ooien.

> [!NOTE]
> In deze zelf studie wordt ervan uitgegaan dat u uw notification hub hebt gemaakt en geconfigureerd zoals wordt beschreven in [meldingen verzenden naar universeel Windows-platform-apps](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Houd er ook rekening mee dat Windows Phone 8,1 Windows-referenties (niet Windows Phone) vereist en dat de achtergrond taken niet werken op Windows Phone 8,0 of Silverlight 8,1. Voor Windows Store-toepassingen kunt u alleen meldingen ontvangen via een achtergrond taak als het vergrendelings scherm van de app is ingeschakeld (Klik op het selectie vakje in de Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Het Windows Phone project wijzigen

1. Voeg in het project **NotifyUserWindowsPhone** de volgende code toe aan app. xaml. CS om de taak push achtergrond te registreren. Voeg de volgende regel code toe aan het einde van de `OnLaunched()`-methode:

    ```csharp
    RegisterBackgroundTask();
    ```

2. Voeg nog steeds in app. xaml. cs de volgende code toe direct na de- `OnLaunched()` methode:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```

3. Voeg de volgende `using` instructies toe boven aan het bestand app. xaml. CS:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. Klik in het menu **Bestand** in Visual Studio op **Alles opslaan**.

## <a name="create-the-push-background-component"></a>Het push-achtergrond onderdeel maken

De volgende stap is het maken van het push-achtergrond onderdeel.

1. Klik in Solution Explorer met de rechter muisknop op het knoop punt op het hoogste niveau van de oplossing (**oplossing SecurePush** in dit geval), klik op **toevoegen** en klik vervolgens op **Nieuw project**.
2. Vouw **Store-apps** uit, klik op **Windows Phone apps** en klik vervolgens op **Windows runtime onderdeel (Windows Phone)**. Noem het project **PushBackgroundComponent** en klik vervolgens op **OK** om het project te maken.

    ![Scherm afbeelding van het dialoog venster Nieuw project toevoegen met de Visual C#-optie Windows Runtime onderdeel (Windows Phone) gemarkeerd.][12]
3. Klik in Solution Explorer met de rechter muisknop op het project **PushBackgroundComponent (Windows Phone 8,1)** , klik op **toevoegen** en klik vervolgens op **klasse**. Geef de nieuwe klasse een naam `PushBackgroundTask.cs` . Klik op **toevoegen** om de klasse te genereren.
4. Vervang de volledige inhoud van de `PushBackgroundComponent` naam ruimte definitie door de volgende code, waarbij u de tijdelijke aanduiding vervangt `{back-end endpoint}` door het back-end-eind punt dat is verkregen tijdens het implementeren van uw back-end:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```

5. Klik in Solution Explorer met de rechter muisknop op het PushBackgroundComponent-project **(Windows Phone 8,1)** en klik vervolgens op **NuGet-pakketten beheren**.
6. Klik aan de linkerkant op **Online**.
7. Typ **Http Client** in **het zoekvak**.
8. Klik in de lijst met resultaten op **HTTP-client bibliotheken van micro soft** en klik vervolgens op **installeren**. Voltooi de installatie.
9. Typ nu **Json.net** in **het zoekvak** van NuGet. Installeer het **JSON.net** -pakket en sluit vervolgens het venster NuGet Package Manager.
10. Voeg `using` boven aan het bestand de volgende-instructies toe `PushBackgroundTask.cs` :

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```

11. Klik in Solution Explorer, in het **NotifyUserWindowsPhone (Windows Phone 8,1)** -project, met de rechter muisknop op **verwijzingen** en klik vervolgens op **verwijzing toevoegen...**. Schakel in het dialoog venster referentie beheer het selectie vakje naast **PushBackgroundComponent** in en klik vervolgens op **OK**.
12. In Solution Explorer dubbelklikt u op **package. appxmanifest** in het **NotifyUserWindowsPhone-project (Windows Phone 8,1)** . Stel onder **meldingen** de **pop-up kan** in op **Ja**.

    ![Scherm afbeelding van het Solution Explorer venster waarin wordt gefocust op package. appxmanifest met de optie voor het inschakelen van de pop-up ingesteld op Ja, in rood beschreven.][3]
13. Klik nog steeds in **package. appxmanifest** op het vervolg keuzelijst met **declaraties** bovenaan. Klik in de vervolg keuzelijst **beschik bare aangiften** op **achtergrond taken** en klik vervolgens op **toevoegen**.
14. In **package. appxmanifest**, onder **Eigenschappen**, **push melding** controleren.
15. In **package. appxmanifest**, onder **app-instellingen**, typt u **PushBackgroundComponent. PushBackgroundTask** in het veld **ingangs punt** .

    ![Scherm afbeelding van het Solution Explorer venster waarin wordt gefocust op package. appxmanifest met de beschik bare aangiften, ondersteunde aangiften, Push meldingen en toegangs punt opties die in het rood worden weer gegeven.][13]
16. Klik in het menu **Bestand** op **Alles opslaan**.

## <a name="run-the-application"></a>De toepassing uitvoeren

Ga als volgt te werk om de toepassing uit te voeren:

1. Voer in Visual Studio de **project appbackend** Web API-toepassing uit. Er wordt een ASP.NET-webpagina weer gegeven.
2. Voer in Visual Studio de Windows Phone-app **NotifyUserWindowsPhone (Windows Phone 8,1)** uit. De Windows Phone-Emulator wordt uitgevoerd en de app wordt automatisch geladen.
3. Voer in de gebruikers interface van de **NotifyUserWindowsPhone** -app een gebruikers naam en wacht woord in. Dit kan een wille keurige teken reeks zijn, maar ze moeten dezelfde waarde hebben.
4. Klik in de gebruikers interface van de **NotifyUserWindowsPhone** -app op **Aanmelden en registreren**. Klik vervolgens op **Push verzenden**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
