---
title: 'Zelfstudie: Language Understanding Bot C# v4'
description: Bouw met behulp van C# een chatbot met ingebouwd taalbegrip (LUIS). De bot is gebouwd met Bot Framework versie 4 en de Azure-web-app-botservice.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4114dd712028895da03835a2730ebd7a6ab971a4
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436875"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Zelfstudie: een web-app-bot met Language Understanding in C# gebruiken

Bouw met behulp van C# een chatbot met ingebouwd taalbegrip (LUIS). De bot is gebouwd met de Azure-resource [Web-app-bot](/azure/bot-service/) en [Bot Framework versie](https://github.com/Microsoft/botbuilder-dotnet) 4.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een web-app-bot maken. Bij dit proces wordt een nieuwe LUIS-app gemaakt.
> * Het project downloaden dat is gemaakt door de webbotservice
> * De bot en emulator lokaal op uw computer starten
> * De uitingsresultaten in de bot bekijken

## <a name="prerequisites"></a>Vereisten

* [Bot Framework-emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-web-app-bot-resource"></a>Een web-app-botresource maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Nieuwe resource maken**.

1. Zoek via het zoekvak naar **Web-app-bot** en selecteer deze optie. Selecteer **Maken**.

1. In **Bot Service** geeft u de vereiste informatie op:

    |Instelling|Doel|Aanbevolen instelling|
    |--|--|--|
    |Botnaam|Resourcenaam|`luis-csharp-bot-` + `<your-name>`, bijvoorbeeld, `luis-csharp-bot-johnsmith`|
    |Abonnement|Het abonnement waarvoor de bot moet worden gemaakt.|Uw primaire abonnement.
    |Resourcegroep|Logische groep van Azure-resources|Maak een nieuwe groep voor het opslaan van alle resources die worden gebruikt met deze bot. Geef de groep de naam `luis-csharp-bot-resource-group`.|
    |Locatie|Azure-regio: deze hoeft niet dezelfde te zijn als de LUIS-regio voor maken en publiceren.|`westus`|
    |Prijscategorie|Wordt gebruikt voor serviceaanvraaglimieten en facturatie.|`F0` is de gratis laag.
    |Naam van app|Deze naam wordt gebruikt als subdomein wanneer uw bot wordt geïmplementeerd in de cloud (bijvoorbeeld humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, bijvoorbeeld, `luis-csharp-bot-johnsmith`|
    |Botsjabloon|Instellingen voor het botframework - zie de volgende tabel|
    |Locatie van LUIS-app|Moet hetzelfde zijn als de regio waarin de LUIS-resource zich bevindt|`westus`|
    |App Service-plan/-locatie|Wijzig de gegeven standaardwaarde niet.|
    |Application Insights|Wijzig de gegeven standaardwaarde niet.|
    |Microsoft-app-id en wachtwoord|Wijzig de gegeven standaardwaarde niet.|

1. Selecteer het volgende in de **botsjabloon** en kies de knop **Selecteren** onder deze instellingen:

    |Instelling|Doel|Selectie|
    |--|--|--|
    |SDK-taal|Computertaal van de bot|**C#**|
    |Bot|Type bot|**Basisbot**|

1. Selecteer **Maken**. Hiermee maakt u de botservice en implementeert u deze in Azure. Bij dit proces wordt een LUIS-app gemaakt met de naam `luis-csharp-bot-XXXX`. Deze naam is gebaseerd op de app-naam /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Een web-app-bot maken](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Wacht tot de botservice is gemaakt voordat u doorgaat.

1. Selecteer `Go to resource` in de melding om naar de pagina van uw web-app-bot te gaan.

## <a name="the-bot-has-a-language-understanding-model"></a>De bot heeft een Language Understanding-model

Bij het maken van de botservice wordt ook een nieuwe LUIS-app met intenties en voorbeelduitingen gemaakt. Met de bot kunnen de volgende intenties worden toegewezen aan de nieuwe LUIS-app:

|LUIS-intenties van de basisbot|voorbeelduiting|
|--|--|
|Vlucht boeken|`Travel to Paris`|
|Annuleren|`bye`|
|GetWeather|`what's the weather like?`|
|Geen|Alles buiten het domein van de app.|

## <a name="test-the-bot-in-web-chat"></a>De bot testen in een webgesprek

1. Selecteer in de Azure Portal voor de nieuwe bot **Testen in webgesprek**.
1. Voer in het tekstvak **Typ uw bericht** de tekst `Book a flight from Seattle to Berlin tomorrow` in. De bot vraagt u te bevestigen dat u een vlucht wilt boeken.

    ![Schermopname van de Azure-portal, voer de tekst 'hello' in.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    U kunt de testfunctionaliteit gebruiken om uw bot snel te testen. Download de botcode en gebruik Visual Studio Code voor een volledige test, inclusief foutopsporing.

## <a name="download-the-web-app-bot-source-code"></a>De broncode van de web-app-bot downloaden

Als u de code van de web-app-bot verder wilt ontwikkelen, downloadt u de code en gebruikt u deze op uw lokale computer.

1. In de Azure-portal selecteert u **Bouwen** in het gedeelte **Botbeheer**.

1. Selecteer **Broncode bot downloaden**.

    [![Download de broncode van de web-app-bot voor de basisbot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Wanneer in het pop-updialoogvenster de vraag **App-instellingen opnemen in het gedownloade ZIP-bestand?** wordt weergegeven, selecteert u **Ja**.

1. Als de broncode is ingepakt, ziet u een bericht met een link voor het downloaden van de code. Selecteer de link.

1. Sla het zip-bestand op uw lokale computer op en pak de bestanden uit. Open het project in Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Code voor het verzenden van een uiting naar LUIS en het ontvangen van een antwoord controleren

1. Als u de gebruikersuiting wilt verzenden naar het LUIS-voorspellingseindpunt, opent u het bestand **FlightBookingRecognizer.js**. Hier wordt de uiting die de gebruiker heeft ingevoerd in de bot naar LUIS verzonden. Het antwoord van LUIS wordt geretourneerd op basis van de methode **RecognizeAsync**.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;

    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;

            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);

                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }

            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;

            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);

            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ```

1. In **Dialogs -> MainDialog.cs** wordt de uiting vastgelegd en naar executeLuisQuery verzonden in de methode actStep.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;

    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class MainDialog : ComponentDialog
        {
            private readonly FlightBookingRecognizer _luisRecognizer;
            protected readonly ILogger Logger;

            // Dependency injection uses this constructor to instantiate MainDialog
            public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                : base(nameof(MainDialog))
            {
                _luisRecognizer = luisRecognizer;
                Logger = logger;

                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(bookingDialog);
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    IntroStepAsync,
                    ActStepAsync,
                    FinalStepAsync,
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> IntroStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("NOTE: LUIS is not configured. To enable all capabilities, add 'LuisAppId', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.", inputHint: InputHints.IgnoringInput), cancellationToken);

                    return await stepContext.NextAsync(null, cancellationToken);
                }

                // Use the text provided in FinalStepAsync or the default if it is the first time.
                var messageText = stepContext.Options?.ToString() ?? "What can I help you with today?\nSay something like \"Book a flight from Paris to Berlin on March 22, 2020\"";
                var promptMessage = MessageFactory.Text(messageText, messageText, InputHints.ExpectingInput);
                return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = promptMessage }, cancellationToken);
            }

            private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
                }

                // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
                var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
                switch (luisResult.TopIntent().intent)
                {
                    case FlightBooking.Intent.BookFlight:
                        await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                        // Initialize BookingDetails with any entities we may have found in the response.
                        var bookingDetails = new BookingDetails()
                        {
                            // Get destination and origin from the composite entities arrays.
                            Destination = luisResult.ToEntities.Airport,
                            Origin = luisResult.FromEntities.Airport,
                            TravelDate = luisResult.TravelDate,
                        };

                        // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                    case FlightBooking.Intent.GetWeather:
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        var getWeatherMessageText = "TODO: get weather flow here";
                        var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                        break;

                    default:
                        // Catch all for unhandled intents
                        var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                        var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                        break;
                }

                return await stepContext.NextAsync(null, cancellationToken);
            }

            // Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
            // In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
            // will be empty if those entity values can't be mapped to a canonical item in the Airport.
            private static async Task ShowWarningForUnsupportedCities(ITurnContext context, FlightBooking luisResult, CancellationToken cancellationToken)
            {
                var unsupportedCities = new List<string>();

                var fromEntities = luisResult.FromEntities;
                if (!string.IsNullOrEmpty(fromEntities.From) && string.IsNullOrEmpty(fromEntities.Airport))
                {
                    unsupportedCities.Add(fromEntities.From);
                }

                var toEntities = luisResult.ToEntities;
                if (!string.IsNullOrEmpty(toEntities.To) && string.IsNullOrEmpty(toEntities.Airport))
                {
                    unsupportedCities.Add(toEntities.To);
                }

                if (unsupportedCities.Any())
                {
                    var messageText = $"Sorry but the following airports are not supported: {string.Join(',', unsupportedCities)}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await context.SendActivityAsync(message, cancellationToken);
                }
            }

            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                // If the child dialog ("BookingDialog") was cancelled, the user failed to confirm or if the intent wasn't BookFlight
                // the Result here will be null.
                if (stepContext.Result is BookingDetails result)
                {
                    // Now we have all the booking details call the booking service.

                    // If the call to the booking service was successful tell the user.

                    var timeProperty = new TimexProperty(result.TravelDate);
                    var travelDateMsg = timeProperty.ToNaturalLanguage(DateTime.Now);
                    var messageText = $"I have you booked to {result.Destination} from {result.Origin} on {travelDateMsg}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(message, cancellationToken);
                }

                // Restart the main dialog with a different message the second time around
                var promptMessage = "What else can I do for you?";
                return await stepContext.ReplaceDialogAsync(InitialDialogId, promptMessage, cancellationToken);
            }
        }
    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>De bot starten in Visual Studio

Start de bot in Visual Studio 2019. Er wordt een browservenster geopend met de website van de web-app-bot op `http://localhost:3978/`. Er wordt een startpagina met informatie over uw bot weergegeven.

![Er wordt een startpagina met informatie over uw bot weergegeven.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>De Bot Framework-emulator gebruiken om de bot te testen

1. Start de Bot Framework-emulator en selecteer **Bot openen**.
1. Voer in het pop-updialoogvenster **Een bot openen** uw bot-URL in, zoals `http://localhost:3978/api/messages`. De route `/api/messages` is het webadres voor de bot.
1. Voer de **Microsoft-app-id** en het **Microsoft-app-wachtwoord** in. U vindt deze in het bestand **appsettings.json** in de hoofdmap van de botcode die u hebt gedownload. Selecteer vervolgens **Verbinding maken**.

1. Voer in de Bot Framework-emulator `Book a flight from Seattle to Berlin tomorrow` in en ontvang hetzelfde antwoord voor de basisbot dat u hebt ontvangen bij **Testen in webgesprek** in een eerdere sectie.

    [![Schermopname toont de Bot Framework Emulator met een basic-bot-antwoord.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecteer **Ja**. De bot reageert met een overzicht van de bijbehorende acties.
1. Selecteer in het logboek van de Bot Framework-emulator de regel die `<- trace LuisV3 Trace` bevat. Hiermee wordt het JSON-antwoord van LUIS voor de intentie en entiteiten van de uiting weergegeven.

    [![Schermopname toont een basic-bot-antwoord met de LuisV3-tracering geselecteerd en het JSON-antwoord gemarkeerd.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk meer [voorbeelden](https://github.com/microsoft/botframework-solutions) met gespreksbots.

> [!div class="nextstepaction"]
> [Een Language Understanding-app bouwen met een aangepast onderwerpdomein](./tutorial-intents-only.md)