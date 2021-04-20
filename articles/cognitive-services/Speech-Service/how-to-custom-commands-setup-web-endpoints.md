---
title: Webeindpunten instellen
titleSuffix: Azure Cognitive Services
description: web-eindpunten instellen voor aangepaste opdrachten
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725904"
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
> * Een aangepaste opdrachten-app (zie [Een spraakassistent maken met aangepaste opdrachten](quickstart-custom-commands-application.md))
> * Een client-app met Speech SDK (zie Integreren met een [clienttoepassing met behulp van speech-SDK](how-to-custom-commands-setup-speech-sdk.md))

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Een extern web-eindpunt implementeren met behulp van de Azure Function-app

Voor deze zelfstudie hebt u een HTTP-eindpunt nodig dat de staten bijhoudt voor alle apparaten die u hebt ingesteld in de **TurnOnOff-opdracht** van uw aangepaste opdrachten toepassing.

Als u al een web-eindpunt hebt dat u wilt aanroepen, gaat u verder met [de volgende sectie](#setup-web-endpoints-in-custom-commands). De volgende sectie bevat ook details over een standaard gehost web-eindpunt dat u kunt gebruiken als u deze sectie wilt overslaan.

### <a name="input-format-of-azure-function"></a>Invoerindeling van Azure-functie

Vervolgens implementeert u een eindpunt met behulp [van Azure Functions](../../azure-functions/index.yml).
Hier volgt de indeling van een aangepaste opdrachten gebeurtenis die wordt doorgegeven aan uw Azure-functie. Gebruik deze informatie wanneer u uw Azure Function-app schrijft.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
In de volgende tabel worden de belangrijkste kenmerken van deze invoer beschreven:
        
| Kenmerk | Uitleg |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | De unieke id van het gesprek. Houd er rekening mee dat deze id kan worden gegenereerd door de client-app. |
| **currentCommand** | De opdracht die momenteel actief is in het gesprek. |
| **name** | De naam van de opdracht. Het `parameters` kenmerk is een kaart met de huidige waarden van de parameters. |
| **currentGlobalParameters** | Een kaart zoals `parameters` , maar gebruikt voor globale parameters. |


Voor de **Azure-functie DeviceState** ziet een aangepaste opdrachten er als volgt uit. Dit zal fungeren als invoer **voor** de functie-app.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Azure Function-uitvoer voor een aangepaste opdracht-app

Als de uitvoer van uw Azure-functie wordt gebruikt door een aangepaste opdrachten app, moet deze in de volgende indeling worden weergegeven. Zie [Een opdracht bijwerken vanaf een web-eindpunt](./how-to-custom-commands-update-command-from-web-endpoint.md) voor meer informatie.

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>Azure Function-uitvoer voor een clienttoepassing

Als de uitvoer van uw Azure-functie wordt gebruikt door een clienttoepassing, kan de uitvoer elke vorm hebben die de clienttoepassing vereist.

Voor ons **DeviceState-eindpunt** wordt de uitvoer van uw Azure-functie gebruikt door een clienttoepassing in plaats van de aangepaste opdrachten toepassing. Voorbeelduitvoer van de Azure-functie moet er als volgt uitzien:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Deze uitvoer moet naar een externe opslag worden geschreven, zodat u de status van apparaten kunt behouden. De status van de externe opslag wordt gebruikt in de [sectie Integreren met clienttoepassing](#integrate-with-client-application) hieronder.


### <a name="deploy-azure-function"></a>De Azure-functie implementeren

We bieden een voorbeeld dat u kunt configureren en implementeren als een Azure Functions app. Volg deze stappen om een opslagaccount voor ons voorbeeld te maken.
 
1. Tabelopslag maken om de apparaattoestand op te slaan. Maak in Azure Portal nieuwe resource van het type **Opslagaccount** op naam **devicestate**.
1. Kopieer de **waarde voor Verbindingsreeks** **van devicestate -> Access keys**. U moet dit tekenreeksgeheim toevoegen aan de gedownloade voorbeeldcode van de functie-app.
1. Download de [voorbeeldcode van de functie-app](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start).
1. Open de gedownloade oplossing in Visual Studio 2019. Vervang **Connections.jsin** STORAGE_ACCOUNT_SECRET_CONNECTION_STRING **door** het geheim uit stap 2.
1.  Download de **code DeviceStateAzureFunction.**

Volg deze stappen om de voorbeeld-app Azure Functions te implementeren.

1. [Implementeer](../../azure-functions/index.yml) de Azure Functions app.
1. Wacht tot de implementatie is geslaagd en ga naar de geïmplementeerde resource op Azure Portal. 
1. Selecteer **Functies** in het linkerdeelvenster en selecteer vervolgens **DeviceState.**
1.  Selecteer in het nieuwe venster **Code + Testen en** selecteer vervolgens **Functie-URL krijgen.**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Web-eindpunten instellen in aangepaste opdrachten

Laten we de Azure-functie aansluiten op de bestaande aangepaste opdrachten toepassing.
In deze sectie gebruikt u een bestaand standaard **DeviceState-eindpunt.** Als u uw eigen web-eindpunt hebt gemaakt met behulp van azure-functie of anderszins, gebruikt u dat in plaats van de standaard `https://webendpointexample.azurewebsites.net/api/DeviceState` .

1. Open de aangepaste opdrachten-toepassing die u eerder hebt gemaakt.
1. Ga naar **Web-eindpunten** en klik **op Nieuw web-eindpunt.**

   > [!div class="mx-imgBorder"]
   > ![Nieuw webeindpunt](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Naam | UpdateDeviceState | Naam voor het webeindpunt. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | De URL van het eindpunt waarmee u uw aangepaste opdrachten-app wilt laten communiceren. |
   | Methode | POST | De toegestane interacties (zoals GET, POST) met uw eindpunt.|
   | Kopteksten | Sleutel: app, waarde: gebruik de eerste acht cijfers van uw toepassings-id | De headerparameters die moeten worden meegenomen in de aanvraagheader.|

    > [!NOTE]
    > - Het voorbeeld van een web-eindpunt dat is gemaakt [met Azure Functions](../../azure-functions/index.yml), die wordt vastgemaakt met de database waarmee de apparaattoestand van de tv en ventilator wordt opgeslagen.
    > - De voorgestelde header is alleen nodig voor het voorbeeld-eindpunt.
    > - Neem de eerste 8 cijfers van uw applicationId om ervoor te zorgen dat de waarde van de header uniek is in ons **voorbeeld-eindpunt.**
    > - In de echte wereld kan het web-eindpunt het eindpunt zijn van de [IOT-hub](../../iot-hub/about-iot-hub.md) die uw apparaten beheert.

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
   > ![Schermopname van het scherm Bij geslaagd - Actie om uit te voeren.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

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
- Sla, train en test bij het antwoord geslaagd op.
   > [!div class="mx-imgBorder"]
   > ![Schermopname van het antwoord Bij geslaagd.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Verwijder bij Fail response een van de queryparameters, sla op, hertrain en test.
   > [!div class="mx-imgBorder"]
   > ![Actie voor het aanroepen van webeindpunten bij een geslaagde poging](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integreren met clienttoepassing

In [Activiteit aangepaste opdrachten verzenden naar clienttoepassing](./how-to-custom-commands-send-activity-to-client.md)hebt u de actie Activiteit verzenden naar **client** toegevoegd. De activiteit wordt verzonden naar de clienttoepassing, ongeacht of de actie **Webeindpunt aanroepen** is geslaagd of niet.
Normaal gesproken wilt u echter alleen activiteit verzenden naar de clienttoepassing wanneer de aanroep van het web-eindpunt is geslaagd. In dit voorbeeld is dat wanneer de status van het apparaat is bijgewerkt.

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
U verzendt nu alleen activiteit naar de client wanneer de aanvraag naar het web-eindpunt is geslaagd.

### <a name="create-visuals-for-syncing-device-state"></a>Visuals maken voor het synchroniseren van de apparaatstatus

Voeg de volgende XML toe `MainPage.xaml` aan boven het blok **EnableMicrophoneButton.**

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Apparaatstatus synchroniseren

Voeg aan `MainPage.xaml.cs` de referentie `using Windows.Web.Http;` toe. Voeg de volgende code toe aan de klasse `MainPage`. Met deze methode wordt een GET-aanvraag naar het voorbeeldeindpunt verzonden en wordt de huidige apparaatstatus opgehaald voor uw app. Zorg ervoor dat u `<your_app_name>` wijzigt in wat u hebt gebruikt in de **header** in het web-eindpunt aangepaste opdracht.

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

## <a name="try-it-out"></a>Probeer het eens

1. Start de toepassing.
1. Klik op Apparaatstatus synchroniseren.\
Als u de app hebt getest met in de vorige sectie, ziet u `turn on tv` dat de tv wordt uitgevoerd als **op**.
    > [!div class="mx-imgBorder"]
    > ![Apparaatstatus synchroniseren](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecteer **Microfoon inschakelen.**
1. Selecteer de **knop** Praten.
1. Bijvoorbeeld `turn on the fan` . De visuele status van de ventilator moet worden gewijzigd in **op .**
    > [!div class="mx-imgBorder"]
    > ![Ventilator inschakelen](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepaste opdrachten exporteren als een externe vaardigheid](./how-to-custom-commands-integrate-remote-skills.md)
