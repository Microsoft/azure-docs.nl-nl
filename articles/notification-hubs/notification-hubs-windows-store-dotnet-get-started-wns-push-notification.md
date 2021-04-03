---
title: Meldingen verzenden naar Universal Windows Platform-apps met behulp van Azure Notification Hubs | Microsoft Docs
description: Leer hoe u Azure Notification Hubs gebruikt om meldingen naar een UWP-toepassing (universeel Windows-platform) te pushen.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 4f55b6eafe230f722979d535111ce45aa35981f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93125034"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Zelfstudie: Meldingen verzenden naar UWP-apps met behulp van Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In deze zelfstudie maakt u een Notification Hub om pushmeldingen te verzenden naar een UWP-app (Universal Windows Platform). U maakt een lege Windows Store-app die pushmeldingen ontvangt via Windows Push Notification Service (WNS). Vervolgens gebruikt u uw Notification Hub om pushmeldingen te verzenden naar alle apparaten waarop uw app wordt uitgevoerd.

> [!NOTE]
> De volledige code voor deze zelfstudie vindt u op [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

U voert de volgende stappen uit:

> [!div class="checklist"]
> * Een app maken in Windows Store
> * Een Notification Hub maken
> * Een voorbeeld-app voor Windows maken
> * Testmeldingen verzenden

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Microsoft Visual Studio 2017 of hoger. In het voorbeelden in deze zelfstudie wordt [Visual Studio 2019](https://www.visualstudio.com/products) gebruikt.
- [Ontwikkelhulpprogramma's voor UWP-apps geïnstalleerd](/windows/uwp/get-started/get-set-up)
- Een actief Windows Store-account
- Controleer of **Meldingen van apps en andere afzenders ontvangen** is ingeschakeld. 
    - Open het venster **Instellingen** op de computer.
    - Selecteer de tegel **Systeem**.
    - Selecteer **Meldingen en acties** in het menu aan de linkerkant. 
    - Controleer of de instelling **Meldingen van apps en andere afzenders ontvangen** is ingeschakeld. Als dat niet het geval is, schakelt u deze in.

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor UWP-apps.

## <a name="create-an-app-in-windows-store"></a>Een app maken in Windows Store

Als u pushmeldingen naar UWP-apps wilt verzenden, koppelt u uw app aan de Windows Store. Vervolgens configureert u de Notification Hub voor integratie met WNS.

1. Navigeer naar het [Windows-ontwikkelaarscentrum](https://partner.microsoft.com/dashboard/windows/first-run-experience), meld u aan met uw Microsoft-account en selecteer vervolgens **Een nieuwe app maken**.

    ![Knop Nieuwe app](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Typ een naam voor uw app en selecteer **Productnaam reserveren**. Hiermee maakt u een nieuwe Windows Store-registratie voor uw app.

    ![Naam van de Store-app](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Vouw **Productbeheer** uit, selecteer **WNS/MPNS** en vervolgens **Live Services-site**. Meld u aan bij uw Microsoft-account. De toepassingsregistratiepagina wordt in een nieuw tabblad geopend. U kunt ook rechtstreeks naar de pagina [Mijn toepassingen](https://apps.dev.microsoft.com) gaan en daar de naam van uw toepassing selecteren om deze pagina te openen.

    ![WNS MPNS-pagina](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Noteer het wachtwoord voor **Toepassingsgeheimen** en zowel de **Beveiligings-id (SID) van het pakket** als de **Toepassings-id** onder de sectie Windows Store.

    >[!WARNING]
    >Het toepassingsgeheim en de pakket-SID zijn belangrijke beveiligingsreferenties. Deel deze waarden met niemand en distribueer ze niet met uw app.

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>WNS-instellingen voor de hub configureren

1. Selecteer in de categorie **MELDINGSINSTELLINGEN** de optie **Windows (WNS)** .
2. Voer waarden in voor **pakket-SID** en **Beveiligingssleutel** die u in de vorige sectie hebt genoteerd.
3. Klik op **Opslaan** op de werkbalk.

    ![De vakken Pakket-SID en Beveiligingssleutel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

De Notification Hub is nu geconfigureerd om met WNS te kunnen werken. U hebt de verbindingsreeksen om uw app te registreren en meldingen te verzenden.

## <a name="create-a-sample-windows-app"></a>Een voorbeeld-app voor Windows maken

1. Open in Visual Studio het menu **File** en selecteer **New** en vervolgens **Project**.
2. Voer in het dialoogvenster **Een nieuw project maken** de volgende stappen uit:

    1. Typ **Windows Universal** in het zoekvak bovenaan.
    2. Selecteer in de zoekresultaten **Lege app (Universeel Windows)** en selecteer vervolgens **Volgende**.

       ![Het dialoogvenster Nieuw project](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. Voer in het dialoogvenster **Uw nieuwe project configureren** een **projectnaam in** en een **locatie** voor de projectbestanden in.
    4. Selecteer **Maken**.

3. Accepteer de standaardwaarden voor het **doel** en de **minimale** platformversies en selecteer **OK**.
4. Klik in Solution Explorer met de rechtermuisknop op het Windows Store-app-project, selecteer **Publiceren** en klik vervolgens op **App aan de Store koppelen**. Hierop wordt de wizard **Uw app koppelen aan Windows Store** weergegeven.
5. Meld u in de wizard aan met uw Microsoft-account.
6. Selecteer de app die u in stap 2 hebt geregistreerd, selecteer **Volgende** en selecteer vervolgens **koppelen**. Hierdoor worden de vereiste registratiegegevens voor Windows Store toegevoegd aan het toepassingsmanifest.
7. Klik met de rechtermuisknop op de oplossing in Visual Studio en selecteer **Manage NuGet Packages**. Het venster **Manage NuGet Packages** wordt geopend.
8. Voer in het zoekvak **WindowsAzure.Messaging.Managed** in, selecteer **Install** en accepteer de gebruiksvoorwaarden.

    ![Het venster NuGet-pakketten beheren][20]

    Met deze actie wordt een verwijzing gedownload, geïnstalleerd en toegevoegd. Deze verwijst naar de Azure Notification Hubs-bibliotheek voor Windows door gebruik te maken van het [Microsoft.Azure.NotificationHubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Open het projectbestand `App.xaml.cs` en voeg de volgende instructies toe:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. Zoek in het bestand `App.xaml.cs` van het project de klasse `App` en voeg de volgende `InitNotificationsAsync`-methodedefinitie toe. Vervang `<your hub name>` door de naam van de Notification Hub die u hebt gemaakt in de Azure-portal, en vervang `<Your DefaultListenSharedAccessSignature connection string>` door de verbindingsreeks `DefaultListenSharedAccessSignature` van de pagina **Toegangsbeleid** van uw Notification Hub:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
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

    Met deze code wordt de kanaal-URI voor de app opgehaald uit WNS en wordt vervolgens die kanaal-URI voor uw Notification Hub geregistreerd.

    >[!NOTE]
    > Vervang de tijdelijke aanduiding `hub name` door de naam van de Notification Hub die in de Azure-portal wordt weergegeven. Vervang ook de tijdelijke plaatsaanduiding van de verbindingstekenreeks door de verbindingsreeks `DefaultListenSharedAccessSignature` die u in een eerdere sectie hebt verkregen op de pagina **Toegangsbeleid** van uw Notification Hub.

11. Voeg bovenaan de gebeurtenis-handler `OnLaunched` in `App.xaml.cs` de volgende oproep naar de nieuwe methode `InitNotificationsAsync` toe:

    ```csharp
    InitNotificationsAsync();
    ```

    Met deze actie wordt gegarandeerd dat de kanaal-URI in uw Notification Hub wordt geregistreerd telkens wanneer de toepassing wordt gestart.

12. Klik met de rechtermuisknop op `Package.appxmanifest` en selecteer Code weergeven (**F7**). Zoek `<Identity .../>` en vervang de waarde door de **Toepassings-id** van de WNS die u [eerder](#create-an-app-in-windows-store) hebt aangemaakt.

13. Als u de app wilt uitvoeren, drukt u de toets **F5** op het toetsenbord. Er wordt een dialoogvenster met de registratiesleutel weergegeven. Klik op **OK** om het dialoogvenster te sluiten.

    ![Registratie voltooid](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Uw app is nu gereed om pop-upmeldingen te ontvangen.

## <a name="send-test-notifications"></a>Testmeldingen verzenden

U kunt het ontvangen van meldingen in de app snel testen door meldingen te verzenden in [Azure Portal](https://portal.azure.com/).

1. Ga in de Azure Portal naar het tabblad Overzicht en selecteer **Test verzenden** op de werkbalk.

    ![Knop Verzenden testen](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Voer in het venster **Test verzenden** de volgende acties uit:
    1. Selecteer **Windows** bij **Platforms**.
    2. Selecteer **Pop-up** bij **Meldingstype**.
    3. Selecteer **Verzenden**.

        ![Het venster Verzenden testen](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Bekijk het resultaat van de verzendbewerking in de lijst **Resultaat** onder in het venster. U ziet ook een waarschuwingsbericht.

    ![Resultaat van de verzendbewerking](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. U ziet de melding: **Testbericht** op het bureaublad.

    ![Melding](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Volgende stappen
U hebt meldingen verzonden naar al uw Windows-apparaten via de portal of een console-app. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke apparaten:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke apparaten](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
