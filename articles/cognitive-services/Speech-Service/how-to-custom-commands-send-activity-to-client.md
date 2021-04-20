---
title: Een aangepaste opdrachten verzenden naar de clienttoepassing
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u activiteit vanuit een aangepaste opdrachten verzendt naar een clienttoepassing met de Speech SDK.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725094"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Een aangepaste opdrachten verzenden naar de clienttoepassing

In dit artikel leert u hoe u activiteit vanuit een aangepaste opdrachten verzendt naar een clienttoepassing met de Speech SDK.

U voltooit de volgende taken:

- Een aangepaste JSON-nettolading definiëren en verzenden vanuit uw aangepaste opdrachten toepassing
- De aangepaste inhoud van de JSON-nettolading ontvangen en visualiseren vanuit een C# UWP Speech SDK-clienttoepassing

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) of hoger. In deze handleiding wordt Visual Studio 2019 gebruikt
> * Een Azure-abonnementssleutel voor de Spraak-service: [haal gratis een sleutel op](overview.md#try-the-speech-service-for-free) of maak er een met de [Azure-portal](https://portal.azure.com)
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)
> * Een client-app met speech-SDK: hoe: integreren met een clienttoepassing met [behulp van speech-SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Activiteit verzenden naar client instellen 
1. Open de aangepaste opdrachten die u eerder hebt gemaakt
1. Selecteer **de opdracht TurnOnOff,** selecteer **ConfirmationResponse** onder de voltooiingsregel en selecteer **vervolgens Een actie toevoegen**
1. Selecteer **onder Nieuw actietype** de optie **Activiteit verzenden naar client**
1. Kopieer de onderstaande JSON naar **Activiteitsinhoud**
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. Klik **op Opslaan** om een nieuwe regel te maken met de actie Activiteit verzenden, de wijziging **trainen** **en** publiceren

   > [!div class="mx-imgBorder"]
   > ![Regel voor voltooiing van activiteit verzenden](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integreren met clienttoepassing

In [How-to: Setup client application with Speech SDK (Preview)](./how-to-custom-commands-setup-speech-sdk.md),you created a UWP client application with Speech SDK that handled commands as `turn on the tv` , `turn off the fan` . Nu er enkele visuals zijn toegevoegd, ziet u het resultaat van deze opdrachten.

Voeg het volgende XML-blok  van StackPanel toe aan om gelabelde vakken toe te voegen met tekst die aangeeft of `MainPage.xaml` niet.

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Referentiebibliotheken toevoegen

Omdat u een JSON-nettolading hebt gemaakt, moet u een verwijzing naar de JSON.NET [bibliotheek](https://www.newtonsoft.com/json) toevoegen om deserialisatie af te handelen.

1. De juiste client voor uw oplossing.
1. Kies **NuGet-pakketten beheren voor oplossing,** selecteer **Bladeren** 
1. Als u al een **Newtonsoft.jsgeïnstalleerd op**, moet u ervoor zorgen dat de versie ten minste 12.0.3 is. Als dat niet het probleem is, gaat u naar **NuGet-pakketten voor** oplossing beheren - Updates, zoekt **u naarNewtonsoft.jsu dit** wilt bijwerken. In deze handleiding wordt versie 12.0.3 gebruikt.

    > [!div class="mx-imgBorder"]
    > ![Nettolading van activiteit verzenden](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Zorg er ook voor dat NuGet-pakket **Microsoft.NETCore.UniversalWindowsPlatform** ten minste 6.2.10 is. In deze handleiding wordt versie 6.2.10 gebruikt.

Voeg in MainPage.xaml.cs

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>De ontvangen nettolading verwerken

Vervang `InitializeDialogServiceConnector` in de `ActivityReceived` gebeurtenis-handler door de volgende code. De gewijzigde `ActivityReceived` gebeurtenis-handler extraht de nettolading uit de activiteit en wijzigt respectievelijk de visuele status van de tv of ventilator.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Beleid uitproberen

1. De toepassing starten
1. Selecteer Microfoon inschakelen
1. Selecteer de knop Spreken
1. Zeg `turn on the tv`
1. De visuele status van de tv moet worden gewijzigd in 'aan'
   > [!div class="mx-imgBorder"]
   > ![Schermopname die laat zien dat de visuele status van de T-V nu is aan.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [How to: set up web endpoints (Web-eindpunten instellen)](./how-to-custom-commands-setup-web-endpoints.md)
