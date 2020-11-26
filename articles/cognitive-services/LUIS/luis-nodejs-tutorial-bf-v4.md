---
title: 'Zelfstudie: Language Understanding Bot Node.js v4'
description: Bouw met behulp van Node.js een chatbot met ingebouwd taalbegrip (LUIS) in deze zelfstudie. Deze chatbot maakt gebruik van de app Human Resources om snel een botoplossing te implementeren. De bot is gebouwd met Bot Framework versie 4 en de Azure-web-app-bot.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: d070a03a81f70cdbf2c721cc67a3bc40c5d731fc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018732"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Zelfstudie: Een web-app-bot met Language Understanding in Node.js gebruiken

Bouw met behulp van Node.js een chatbot met ingebouwd taalbegrip (LUIS). De bot is gebouwd met de Azure-resource [Web-app-bot](/azure/bot-service/) en [Bot Framework versie](https://github.com/Microsoft/botbuilder-dotnet) 4.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een web-app-bot maken. Bij dit proces wordt een nieuwe LUIS-app gemaakt.
> * Het project downloaden dat is gemaakt door de webbotservice
> * De bot en emulator lokaal op uw computer starten
> * De uitingsresultaten in de bot bekijken

## <a name="prerequisites"></a>Vereisten

* [Bot Framework Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Een web-app-botresource maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Nieuwe resource maken**.

1. Zoek via het zoekvak naar **Web-app-bot** en selecteer deze optie. Selecteer **Maken**.

1. In **Bot Service** geeft u de vereiste informatie op:

    |Instelling|Doel|Aanbevolen instelling|
    |--|--|--|
    |Botnaam|Resourcenaam|`luis-nodejs-bot-` + `<your-name>`, bijvoorbeeld, `luis-nodejs-bot-johnsmith`|
    |Abonnement|Het abonnement waarvoor de bot moet worden gemaakt.|Uw primaire abonnement.
    |Resourcegroep|Logische groep van Azure-resources|Maak een nieuwe groep voor het opslaan van alle resources die worden gebruikt met deze bot. Geef de groep de naam `luis-nodejs-bot-resource-group`.|
    |Locatie|Azure-regio: deze hoeft niet dezelfde te zijn als de LUIS-regio voor maken en publiceren.|`westus`|
    |Prijscategorie|Wordt gebruikt voor serviceaanvraaglimieten en facturatie.|`F0` is de gratis laag.
    |Naam van app|Deze naam wordt gebruikt als subdomein wanneer uw bot wordt geïmplementeerd in de cloud (bijvoorbeeld humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, bijvoorbeeld, `luis-nodejs-bot-johnsmith`|
    |Botsjabloon|Instellingen voor het botframework - zie de volgende tabel|
    |Locatie van LUIS-app|Moet hetzelfde zijn als de regio waarin de LUIS-resource zich bevindt|`westus`|
    |App Service-plan/-locatie|Wijzig de gegeven standaardwaarde niet.|
    |Application Insights|Wijzig de gegeven standaardwaarde niet.|
    |Microsoft-app-id en wachtwoord|Wijzig de gegeven standaardwaarde niet.|

1. Selecteer het volgende in de **botsjabloon** en kies de knop **Selecteren** onder deze instellingen:

    |Instelling|Doel|Selectie|
    |--|--|--|
    |SDK-taal|Computertaal van de bot|**Node.js**|
    |Bot|Type bot|**Basisbot**|

1. Selecteer **Maken**. Hiermee maakt u de botservice en implementeert u deze in Azure. Bij dit proces wordt een LUIS-app gemaakt met de naam `luis-nodejs-bot-XXXX`. Deze naam is gebaseerd op de app-naam /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Een web-app-bot maken](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

    U kunt de testfunctionaliteit gebruiken om uw bot snel te testen. Download de bot-code en gebruik Visual Studio Code voor een volledige test, inclusief foutopsporing.

## <a name="download-the-web-app-bot-source-code"></a>De broncode van de web-app-bot downloaden
Als u de code van de web-app-bot verder wilt ontwikkelen, downloadt u de code en gebruikt u deze op uw lokale computer.

1. In de Azure-portal selecteert u **Bouwen** in het gedeelte **Botbeheer**.

1. Selecteer **Broncode bot downloaden**.

    [![Download de broncode van de web-app-bot voor de basisbot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Wanneer in het pop-updialoogvenster de vraag **App-instellingen opnemen in het gedownloade ZIP-bestand?** wordt weergegeven, selecteert u **Ja**. Hiermee worden de LUIS-instellingen geconfigureerd.

1. Als de broncode is ingepakt, ziet u een bericht met een link voor het downloaden van de code. Selecteer de link.

1. Sla het zip-bestand op uw lokale computer op en pak de bestanden uit. Open de projectmap met Visual Studio Code.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Code voor het verzenden van een uiting naar LUIS en het ontvangen van een antwoord controleren

1. Als u de gebruikersuiting verzenden naar het LUIS-voorspellingseindpunt, opent u het bestand **dialogs -> flightBookingRecognizer.js**. Hier wordt de uiting die de gebruiker heeft ingevoerd in de bot naar LUIS verzonden. Het antwoord van LUIS wordt geretourneerd op basis van de methode **executeLuisQuery**.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. In **dialogs -> mainDialog** wordt de uiting vastgelegd en naar executeLuisQuery verzonden in de methode actStep.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>De botcode starten

1. Open een Windows-console of een Linux- of macOS-terminal.

1. Ga naar de map met de botcode en voer de volgende opdracht in om de dotenv-afhankelijkheid te installeren:

    ```console
    npm install dotenv --save
    ```

1. Voer de volgende opdracht uit om de bot te starten:

    ```console
    node index.js
    ```

Er wordt een browservenster geopend met de website van de web-app-bot op `http://localhost:3978/`. Er wordt een startpagina met informatie over uw bot weergegeven.

![Er wordt een startpagina met informatie over uw bot weergegeven.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>De bot Framework emulator gebruiken om de bot te testen

Stel de bot een vraag voor de intentie Vlucht boeken.

1. Open de Bot Framework-emulator en selecteer **Bot openen**.
1. Voer in het pop-updialoogvenster **Een bot openen** uw bot-URL in, zoals `http://localhost:3978/api/messages`. De route `/api/messages` is het webadres voor de bot.
1. Voer de **Microsoft-app-id** en het **Microsoft-app-wachtwoord** in. U vindt deze in het bestand met de extensie **.env** in de hoofdmap van de botcode die u hebt gedownload.

1. Voer in de Bot Framework-emulator `Book a flight from Seattle to Berlin tomorrow` in en ontvang hetzelfde antwoord voor de basisbot wat u hebt ontvangen bij **Testen in webgesprek**.

    [![Schermopname van de Bot Framework Emulator.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecteer **Ja**. De bot reageert met een overzicht van de bijbehorende acties.
1. Selecteer in het logboek van de Bot Framework-emulator de regel die `<- trace LuisV3 Trace` bevat. Hiermee wordt het JSON-antwoord van LUIS voor de intentie en entiteiten van de uiting weergegeven.

    [![Schermopname van de Bot Framework Emulator, met in het rechterpaneel de JSON.code.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk meer [voorbeelden](https://github.com/microsoft/botframework-solutions) met gespreksbots.

> [!div class="nextstepaction"]
> [Een Language Understanding-app bouwen met een aangepast onderwerpdomein](./tutorial-intents-only.md)