---
title: Webeindpunten instellen (preview)
titleSuffix: Azure Cognitive Services
description: webeindpunten instellen voor aangepaste opdrachten
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6f2dfdbb5833b34441b4abba7359ad70c4717d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602161"
---
# <a name="set-up-web-endpoints"></a>Webeindpunten instellen

In dit artikel leert u hoe u webeindpunten instelt in de toepassing aangepaste opdrachten waarmee u HTTP-aanvragen kunt maken vanuit een clienttoepassing. U gaat de volgende taken uitvoeren:

- Webeindpunten instellen in de toepassing aangepaste opdrachten
- Webeindpunten aanroepen in de toepassing aangepaste opdrachten
- Het antwoord van de webeindpunten ontvangen
- Het antwoord van de webeindpunten integreren in een aangepaste JSON-payload, verzenden, en visualiseren vanuit een UWP-clienttoepassing van de Spraak-SDK in C#

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Een Azure-abonnementssleutel voor de Spraak-service: [haal gratis een sleutel op](overview.md#try-the-speech-service-for-free) of maak er een met de [Azure-portal](https://portal.azure.com)
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)
> * Een client-app die de Spraak-SDK ondersteunt: [Instructies: activiteit naar clienttoepassing beëindigen](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Webeindpunten instellen

1. Open de aangepaste opdrachten-toepassing die u eerder hebt gemaakt.
1. Ga naar Webeindpunten en klik op Nieuw webeindpunt.

   > [!div class="mx-imgBorder"]
   > ![Nieuw webeindpunt](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Naam | UpdateDeviceState | Naam voor het webeindpunt. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | De URL van het eindpunt waarmee u uw aangepaste opdrachten-app wilt laten communiceren. |
   | Methode | POST | De toegestane interacties (zoals GET, POST) met uw eindpunt.|
   | Kopteksten | Sleutel: app, waarde: gebruik de eerste acht cijfers van uw toepassings-id | De headerparameters die moeten worden meegenomen in de aanvraagheader.|

    > [!NOTE]
    > - Het voorbeeld-webeindpunt dat is gemaakt met behulp van [Azure Functions](../../azure-functions/index.yml), dat wordt gekoppeld aan de database waarin de apparaatstatus van de tv en ventilator worden opgeslagen
    > - De voorgestelde header is alleen nodig voor het voorbeeldeindpunt
    > - Om ervoor te zorgen dat de waarde van de header uniek is in het voorbeeldeindpunt, gebruikt u de eerste acht cijfers van uw toepassings-id
    > - In het echt kan het webeindpunt het eindpunt zijn van de [IOT-hub](../../iot-hub/about-iot-hub.md) waarmee uw apparaten worden beheerd

1. Klik op **Opslaan**.

## <a name="call-web-endpoints"></a>Webeindpunten aanroepen

1. Ga naar de opdracht **TurnOnOff** (In/-uitschakelen), selecteer **ConfirmationResponse** onder de voltooiingsregel en selecteer vervolgens **Add an action** (Actie toevoegen).
1. Selecteer **Call web endpoint** (Webeindpunt aanroepen) onder **New Action-Type** (Nieuw actietype)
1. Selecteer in **Edit Action - Endpoints** (Actie bewerken - Eindpunten) het webeindpunt dat we hebben gemaakt. Dat is **UpdateDeviceState** (Apparaatstatus bijwerken).  
1. Geef in **Configuration** de volgende waarden op:
   > [!div class="mx-imgBorder"]
   > ![Actieparameters voor het aanroepen van webeindpunten](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Eindpunten | UpdateDeviceState | Het webeindpunt dat u wilt aanroepen in deze actie. |
   | Queryparameters | item={SubjectDevice}&&value={OnOff} | De queryparameters die moeten worden toegevoegd aan de URL van het webeindpunt.  |
   | Hoofdtekst | N.v.t. | De hoofdtekst van de aanvraag. |

    > [!NOTE]
    > - De voorgestelde queryparameters zijn alleen nodig voor het voorbeeldeindpunt

1. Selecteer **Gesproken antwoord verzenden** in **Bij voltooid - Actie die moet worden uitgevoerd**.

    Voer `{SubjectDevice} is {OnOff}` in **Simple editor** (Eenvoudige editor) in.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het scherm voor het uitvoeren van de actie bij geslaagd.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Actie die moet worden uitgevoerd | Gesproken antwoord verzenden | Actie die moet worden uitgevoerd als de aanvraag op het webeindpunt is geslaagd |

   > [!NOTE]
   > - U kunt de velden in het HTTP-antwoord ook rechtstreeks openen met behulp van `{YourWebEndpointName.FieldName}`. Bijvoorbeeld: `{UpdateDeviceState.TV}`

1. Selecteer **Gesproken antwoord verzenden** in **Bij fout - Actie die moet worden uitgevoerd**

    Voer `Sorry, {WebEndpointErrorMessage}` in **Simple editor** (Eenvoudige editor) in.

   > [!div class="mx-imgBorder"]
   > ![Actie voor het aanroepen van webeindpunten bij een fout](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Actie die moet worden uitgevoerd | Gesproken antwoord verzenden | Actie die moet worden uitgevoerd als de aanvraag op het webeindpunt is mislukt |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` is optioneel. U kunt dit verwijderen als u geen foutbericht wilt weergeven.
   > - In ons voorbeeldeindpunt retourneren we een HTTP-antwoord met gedetailleerde foutberichten voor veelvoorkomende fouten, zoals ontbrekende headerparameters.

### <a name="try-it-out-in-test-portal"></a>Probeer het uit in de testportal
- Antwoord bij geslaagde poging\
Opslaan, trainen en testen
   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het antwoord op geslaagde pogingen.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Antwoord bij mislukte poging\
Een van de queryparameters verwijderen, opslaan, opnieuw trainen en testen
   > [!div class="mx-imgBorder"]
   > ![Actie voor het aanroepen van webeindpunten bij een geslaagde poging](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integreren met clienttoepassing

In [Instructies: Activiteit verzenden naar clienttoepassing (preview)](./how-to-custom-commands-send-activity-to-client.md) hebt u de actie **Activiteit verzenden naar client** toegevoegd. De activiteit wordt verzonden naar de clienttoepassing, ongeacht of de actie **Webeindpunt aanroepen** is geslaagd of niet.
In de meeste gevallen wilt u echter alleen een activiteit naar de clienttoepassing verzenden wanneer de aanroep van het webeindpunt geslaagd is. In dit voorbeeld is dat wanneer de status van het apparaat is bijgewerkt.

1. Verwijder de actie **Activiteit verzenden naar client** die u eerder hebt toegevoegd.
1. Aanroepen van webeindpunt bewerken:
    1. Zorg ervoor dat in **Configuration** (Configuratie) **Query Parameters** (Queryparameters) is ingesteld op `item={SubjectDevice}&&value={OnOff}`
    1. Wijzig **Bij voltooid** **Actie die moet worden uitgevoerd** in **Activiteit verzenden naar client**
    1. Kopieer de JSON hieronder naar **Activity Content** (Inhoud van de activiteit)
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
Nu wordt er alleen activiteit naar de client verzonden wanneer de aanvraag op het eindpunt is geslaagd.

### <a name="create-visuals-for-syncing-device-state"></a>Visuals maken voor het synchroniseren van de apparaatstatus
Voeg de volgende XML toe aan `MainPage.xaml` boven het blok `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Apparaatstatus synchroniseren

Voeg aan `MainPage.xaml.cs` de referentie `using Windows.Web.Http;` toe. Voeg de volgende code toe aan de klasse `MainPage`. Met deze methode wordt een GET-aanvraag naar het voorbeeldeindpunt verzonden en wordt de huidige apparaatstatus opgehaald voor uw app. Zorg ervoor dat u `<your_app_name>` wijzigt in wat u hebt gebruikt in de **header** in het webeindpunt voor aangepaste opdrachten

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Beleid uitproberen

1. De toepassing starten
1. Klik op Apparaatstatus synchroniseren.\
Als u de app met `turn on tv` hebt getest in de vorige sectie, ziet u dat de tv wordt weergegeven als 'aan'.
    > [!div class="mx-imgBorder"]
    > ![Apparaatstatus synchroniseren](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecteer Microfoon inschakelen
1. Selecteer de knop Spreken
1. Zeg `turn on the fan`
1. De visuele status van de ventilator wordt gewijzigd in 'aan'
    > [!div class="mx-imgBorder"]
    > ![Ventilator inschakelen](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Toepassing voor het exporteren van aangepaste opdrachten als externe vaardigheid](./how-to-custom-commands-integrate-remote-skills.md)

