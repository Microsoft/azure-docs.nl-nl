---
title: Activiteit aangepaste opdrachten verzenden naar client toepassing
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u een activiteit kunt verzenden vanuit een aangepaste opdrachten toepassing naar een client toepassing die de Speech SDK uitvoert.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566092"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Activiteit aangepaste opdrachten verzenden naar client toepassing

In dit artikel leert u hoe u een activiteit kunt verzenden vanuit een aangepaste opdrachten toepassing naar een client toepassing die de Speech SDK uitvoert.

U voert de volgende taken uit:

- Een aangepaste JSON-nettolading definiëren en verzenden vanuit uw toepassing voor aangepaste opdrachten
- De aangepaste inhoud van de JSON-nettolading ontvangen en visualiseren vanuit een C# UWP Speech SDK-client toepassing

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) of hoger. In deze hand leiding wordt gebruikgemaakt van Visual Studio 2019
> * Een Azure-abonnementssleutel voor de Spraak-service: [haal gratis een sleutel op](overview.md#try-the-speech-service-for-free) of maak er een met de [Azure-portal](https://portal.azure.com)
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)
> * Een Speech SDK met ingeschakelde client [-app: procedures: integreren met een client toepassing met behulp van Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Activiteit verzenden naar client installeren 
1. Open de toepassing voor aangepaste opdrachten die u eerder hebt gemaakt
1. Selecteer **TurnOnOff** opdracht, selecteer **ConfirmationResponse** onder voltooiings regel en selecteer vervolgens **een actie toevoegen** .
1. Onder **nieuw actie-type**, selecteer **activiteit verzenden naar client**
1. Kopieer de JSON hieronder naar **activiteit inhoud**
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
1. Klik op **Opslaan** om een nieuwe regel met de actie activiteit verzenden te maken, de wijziging te **trainen** en te **publiceren**

   > [!div class="mx-imgBorder"]
   > ![Voltooiings regel voor activiteit verzenden](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integreren met clienttoepassing

In [procedures: client toepassing met Speech SDK (preview)](./how-to-custom-commands-setup-speech-sdk.md)hebt u een UWP-client toepassing gemaakt met een Speech SDK die opdrachten zoals `turn on the tv` , heeft verwerkt `turn off the fan` . Als er visuele elementen zijn toegevoegd, kunt u het resultaat van deze opdrachten bekijken.

Voeg het volgende XML-blok van StackPanel toe aan om vakken met een label toe te voegen aan een tekst die **is aan-of** **uitgeschakeld** `MainPage.xaml` .

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

### <a name="add-reference-libraries"></a>Referentie bibliotheken toevoegen

Omdat u een JSON-nettolading hebt gemaakt, moet u een verwijzing naar de [JSON.net](https://www.newtonsoft.com/json) -bibliotheek toevoegen om de deserialisatie te verwerken.

1. Rechts-client uw oplossing.
1. Kies **NuGet-pakketten beheren voor oplossing**, selecteer **Bladeren** 
1. Als u **Newtonsoft.js** al hebt geïnstalleerd, zorg er dan voor dat de versie ten minste 12.0.3 is. Als dat niet het geval is, gaat u naar **NuGet-pakketten beheren voor oplossing-updates**, zoekt u naar **Newtonsoft.jsop** om het bij te werken. In deze hand leiding wordt gebruikgemaakt van versie 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Nettolading activiteit verzenden](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Zorg er ook voor dat NuGet-pakket **micro soft. NetCore. UniversalWindowsPlatform** ten minste 6.2.10 is. In deze hand leiding wordt gebruikgemaakt van versie 6.2.10.

Voeg in ' MainPage. xaml. cs '

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>De ontvangen nettolading verwerken

`InitializeDialogServiceConnector`Vervang in de gebeurtenis-handler door de `ActivityReceived` volgende code. De `ActivityReceived` gebeurtenis-handler die is gewijzigd, haalt de payload op uit de activiteit en wijzigt de visuele status van de TV of ventilator respectievelijk.

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
1. De visuele status van de TV moet worden gewijzigd in aan
   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding die laat zien dat de visuele status van de T V nu is ingeschakeld.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: Web-eind punten instellen (preview-versie)](./how-to-custom-commands-setup-web-endpoints.md)
