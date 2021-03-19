---
title: Pushmeldingen verzenden naar Windows Phone-apps met Azure Notification Hubs | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Windows Phone 8 of Windows Phone 8.1 Silverlight-toepassing.
services: notification-hubs
documentationcenter: windows
keywords: pushmelding,pushmelding,windows phone-push
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
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
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998336"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar Windows Phone-apps met Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In deze zelfstudie ziet u hoe u met Azure Notification Hubs pushmeldingen verzendt naar Silverlight-toepassingen van Windows Phone 8 of Windows Phone 8.1. Als u ontwikkelt voor Windows Phone 8.1 (zonder Silverlight), raadpleegt u de [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)-versie van deze zelfstudie.

In deze zelfstudie maakt u een lege Windows Phone 8-app die pushmeldingen ontvangt via Microsoft Push Notification Service (MPNS). Als u de app hebt gemaakt, kunt u de Notification Hub gebruiken om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd.

> [!NOTE]
> De Notification Hubs Windows Phone SDK biedt geen ondersteuning voor het gebruik van Windows Push Notification Service (WNS) met Windows Phone 8.1 Silverlight-apps. Als u WNS (in plaats van MPNS) met Windows Phone 8.1 Silverlight-apps wilt gebruiken, raadpleegt u de [Notification Hubs - Windows Phone Silverlight-zelfstudie] waarin REST API's worden gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Notification Hub maken
> * Een Windows Phone-toepassing maken
> * Een testmelding verzenden

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 Express met onderdelen voor de ontwikkeling van mobiele apps](https://www.visualstudio.com/vs/older-downloads/)

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor Windows Phone 8-apps.

## <a name="create-your-notification-hub"></a>Een Notification Hub maken

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Windows Phone-instellingen (MPNS) configureren

1. Selecteer **Windows Phone (MPNS)** onder **MELDINGSINSTELLINGEN**.
2. Selecteer **Verificatiepush inschakelen**.
3. Selecteer **Opslaan** op de werkbalk.

    ![Azure Portal - Niet-geverifieerde pushmeldingen inschakelen](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    De hub is nu gemaakt en geconfigureerd om niet-geverifieerde meldingen voor Windows Phone te verzenden.

    > [!NOTE]
    > In deze zelfstudie wordt MPNS in niet-geverifieerde modus gebruikt. Bij de niet-geverifieerde MPNS-modus gelden beperkingen voor meldingen die u naar elk kanaal kunt verzenden. Notification Hubs ondersteunt de [geverifieerde MPNS-modus](/previous-versions/windows/apps/ff941099(v=vs.105)) doordat u uw certificaat kunt uploaden.

## <a name="create-a-windows-phone-application"></a>Een Windows Phone-toepassing maken

In deze sectie maakt u een Windows Phone-toepassing die zichzelf bij uw Notification Hub registreert.

1. Maak een nieuwe Windows Phone 8-toepassing in Visual Studio.

    ![Visual Studio - Nieuw project - Windows Phone-app][13]

    In Visual Studio 2013 Update 2 of hoger maakt u daarentegen een Windows Phone Silverlight-toepassing.

    ![Visual Studio - Nieuw project - Lege app - Windows Phone Silverlight][11]
2. Klik met de rechtermuisknop op de oplossing in Visual Studio en klik vervolgens op **NuGet-pakketten beheren**.
3. Zoek `WindowsAzure.Messaging.Managed` en klik op **Installeren**. Accepteer vervolgens de gebruiksvoorwaarden.

    ![Visual Studio - NuGet Package Manager][20]
4. Open het bestand App.xaml.cs en voeg de volgende `using`-instructies toe:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. Voeg de volgende code toe bovenaan methode `Application_Launching` in `App.xaml.cs`:

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > De waarde `MyPushChannel` is een index die wordt gebruikt om een bestaand kanaal te zoeken in de collectie [HttpNotificationChannel](/previous-versions/ff402781(v=vs.110)). Als het kanaal niet wordt gevonden, maakt u een nieuwe vermelding met die naam.

    Voeg de naam van uw hub in en de verbindingsreeks met de naam `DefaultListenSharedAccessSignature` die u in de voorgaande sectie hebt genoteerd.
    Met deze code wordt de kanaal-URI voor de app opgehaald uit MPNS en wordt vervolgens die kanaal-URI voor uw Notification Hub geregistreerd. Ook wordt hiermee gegarandeerd dat de kanaal-URI in uw Notification Hub wordt geregistreerd telkens wanneer de toepassing wordt gestart.

   > [!NOTE]
   > In deze zelfstudie wordt een pop-upmelding naar het apparaat verzonden. Wanneer u een tegelmelding verzendt, moet u in plaats daarvan de methode `BindToShellTile` voor het kanaal aanroepen. Voor ondersteuning van zowel pop-up- als tegelmeldingen roept u zowel `BindToShellTile` als `BindToShellToast` aan.

6. Vouw in Solution Explorer **Eigenschappen** uit, open het bestand `WMAppManifest.xml`, klik op het tabblad **Mogelijkheden** en zorg ervoor dat de mogelijkheid **ID_CAP_PUSH_NOTIFICATION** is ingeschakeld. Uw app kan nu pushmeldingen ontvangen.

    ![Visual Studio - Windows Phone-app-mogelijkheden][14]
7. Druk op de toets `F5` om de app uit te voeren. Er wordt een registratiebericht in de app weergegeven.
8. Sluit de app of ga naar de startpagina.

   > [!NOTE]
   > Als u een pop-upmelding wilt ontvangen, moet de toepassing niet worden uitgevoerd op de voorgrond.

## <a name="test-send-a-notification"></a>Een testmelding verzenden

1. Ga in Azure Portal naar het tabblad Overzicht.
2. Selecteer **Verzenden testen**.

    ![Knop Verzenden testen](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. Voer in het venster **Verzenden testen** de volgende stappen uit:

    1. Selecteer **Windows Phone** bij **Platforms**.
    2. Selecteer **Pop-up** bij **Meldingstype**.
    3. Selecteer **Verzenden**
    4. Bekijk het **resultaat** in de lijst onderaan het venster.

        ![Het venster Verzenden testen](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. Controleer in de Windows Phone-emulator of op de Windows-telefoon of u de melding ziet.

    ![Melding op Windows-telefoon](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Volgende stappen

In dit eenvoudige voorbeeld hebt u pushmeldingen uitgezonden naar al uw Windows Phone 8-apparaten. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke apparaten:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[Notification Hubs - Windows Phone Silverlight-zelfstudie]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
