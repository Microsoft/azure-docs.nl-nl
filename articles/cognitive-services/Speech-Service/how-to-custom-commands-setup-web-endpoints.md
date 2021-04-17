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
ms.openlocfilehash: 95f27827950c5ed38caa1f83ede266afb57a1697
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515631"
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

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Een extern web-eindpunt implementeren met behulp van de Azure Function-app

* In deze zelfstudie hebt u een HTTP-eindpunt nodig dat de staten bijhoudt voor alle apparaten die u hebt ingesteld in de **TurnOnOff-opdracht** van uw aangepaste opdrachtentoepassing.

* Als u al een web-eindpunt hebt dat u wilt aanroepen, gaat u verder met [de volgende sectie](#setup-web-endpoints-in-custom-commands). In de volgende sectie hebben we u ook een standaard gehost web-eindpunt geboden dat u kunt gebruiken als u deze sectie wilt overslaan.

### <a name="input-format-of-azure-function"></a>Invoerindeling van Azure Function
* Vervolgens implementeert u een eindpunt met behulp [van Azure Functions](../../azure-functions/index.yml).
Hier volgt de algemene indeling van een aangepaste opdrachten gebeurtenis die wordt doorgegeven aan uw Azure-functie. Gebruik deze informatie wanneer u uw functie-app schrijft.

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

    
* Laten we eens kijken naar de belangrijkste kenmerken van deze invoer:
        
    | Kenmerk | Uitleg |
    | ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
    | **conversationId** | De unieke id van het gesprek. Houd er rekening mee dat deze id kan worden gegenereerd op basis van de client-app. |
    | **currentCommand** | De opdracht die momenteel actief is in het gesprek. |
    | **name** | De naam van de opdracht. Het `parameters` kenmerk is een kaart met de huidige waarden van de parameters. |
    | **currentGlobalParameters** | Een kaart zoals `parameters` , maar gebruikt voor globale parameters. |


* Voor de **Azure-functie DeviceState** ziet een aangepaste opdrachten er als volgt uit. Dit zal fungeren als invoer **voor** de functie-app.
    
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

### <a name="output-format-of-azure-function"></a>Uitvoerindeling van Azure Function

#### <a name="output-consumed-by-a-custom-commands--application"></a>Uitvoer die wordt gebruikt door een aangepaste opdrachten toepassing
In dit geval kunt u instellen dat de uitvoerindeling moet voldoen aan de volgende indeling. Volg [Een opdracht bijwerken vanaf een web-eindpunt](./how-to-custom-commands-update-command-from-web-endpoint.md) voor meer informatie.

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

#### <a name="output-consumed-by-a-client-application"></a>Uitvoer die wordt gebruikt door een clienttoepassing
In dit geval kunt u de uitvoerindeling instellen op de behoeften van uw client.
* Voor ons **DeviceState-eindpunt** wordt de uitvoer van de Azure-functie gebruikt door een clienttoepassing in plaats van de aangepaste opdrachten toepassing. **Voorbeelduitvoer** van de Azure-functie moet er als volgt uit zien:
    
    ```json
    {
      "TV": "on",
      "Fan": "off"
    }
    ``` 

*  Deze uitvoer moet ook naar een externe opslag worden geschreven, zodat u de status van apparaten dienovereenkomstig kunt onderhouden. De status van de externe opslag wordt gebruikt in de [sectie Integreren met clienttoepassing.](#integrate-with-client-application)


### <a name="host-azure-function"></a>Azure-functie hosten

1. Maak een Table Storage-account om de apparaattoestand op te slaan.
    1. Ga naar Azure Portal en maak een nieuwe resource van het type **Opslagaccount** op naam **devicestate**.
        1. Kopieer de **waarde verbindingsreeks** **van devicestate -> Access keys**.
        1. U moet deze tekenreeks toevoegen aan de gedownloade voorbeeldcode van de functie-app.
    1. Download de [voorbeeldcode van de functie-app](https://aka.ms/speech/cc-function-app-sample).
    1. Open de gedownloade oplossing in VS 2019. Vervang in **Connections.jsbestandsbestand** STORAGE_ACCOUNT_SECRET_CONNECTION_STRING **waarde** in het gekopieerde geheim uit stap *a*.
1.  Download de **code DeviceStateAzureFunction.**
1. [De](../../azure-functions/index.yml) Functions-app implementeren in Azure.
    
    1.  Wacht tot de implementatie is geslaagd en ga naar de geïmplementeerde resource op Azure Portal. 
    1. Selecteer **Functies** in het linkerdeelvenster en selecteer vervolgens **DeviceState.**
    1.  Selecteer in het nieuwe venster **Code + Test** en selecteer vervolgens **Functie-URL op halen.**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Web-eindpunten instellen in aangepaste opdrachten
Laten we de Azure-functie aansluiten op de bestaande aangepaste opdrachten toepassing.
In deze sectie gebruikt u een bestaand standaard **DeviceState-eindpunt.** Als u uw eigen web-eindpunt hebt gemaakt met azure-functie of anderszins, gebruikt u dat in plaats van de standaard https://webendpointexample.azurewebsites.net/api/DeviceState .

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
    > - Het voorbeeld van een web-eindpunt dat is gemaakt met behulp van [de Azure-functie](../../azure-functions/index.yml), die wordt vastgemaakt met de database waarmee de apparaattoestand van de tv en ventilator wordt opgeslagen
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
- Antwoord bij geslaagde poging\
Opslaan, trainen en testen
   > [!div class="mx-imgBorder"]
   > ![Schermopname met het antwoord Bij succes.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Antwoord bij mislukte poging\
Een van de queryparameters verwijderen, opslaan, opnieuw trainen en testen
   > [!div class="mx-imgBorder"]
   > ![Actie voor het aanroepen van webeindpunten bij een geslaagde poging](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integreren met clienttoepassing

In [How-to: Send activity to client application](./how-to-custom-commands-send-activity-to-client.md)(Activiteit verzenden naar clienttoepassing) hebt u de actie Activiteit verzenden naar **client** toegevoegd. De activiteit wordt verzonden naar de clienttoepassing, ongeacht of de actie **Webeindpunt aanroepen** is geslaagd of niet.
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
> [Een aangepaste opdrachten exporteren als een externe vaardigheid](./how-to-custom-commands-integrate-remote-skills.md)

