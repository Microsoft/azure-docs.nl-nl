---
title: 'Zelf studie: stemmen uw bot in te scha kelen met behulp van Speech SDK-Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie maakt u een echo bot met behulp van micro soft bot Framework, implementeert u het naar Azure en registreert u het met het bot Framework direct line speech Channel. Vervolgens configureert u een voor beeld-client-app voor Windows waarmee u naar uw bot kunt praten en kunt horen dat deze wordt weer gegeven.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: d91bfee228b946ff564f6d080976f9ce5c12caa4
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426260"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Zelf studie: spraak-uw bot inschakelen met de Speech SDK

U kunt de speech-service gebruiken om een chat-bot in te spreken.

In deze zelf studie maakt u een bot die naar wens wordt herhaald.
U maakt uw bot met behulp van micro soft bot Framework, implementeert deze in Azure en registreert het met het bot Framework direct line speech Channel.
Vervolgens configureert u een voor beeld-client-app voor Windows waarmee u naar uw bot kunt praten en kunt horen dat deze wordt weer gegeven.

Deze zelf studie is bedoeld voor ontwikkel aars die nieuw zijn in azure, bot Framework bots, direct line speech of de Speech SDK, en snel een werkend systeem met beperkte code moeten bouwen. Er is geen ervaring of kennis van deze services nodig.

De chat-bot die u in deze zelf studie maakt, volgt de volgende stappen:

1. De voor beeld-client toepassing is geconfigureerd om verbinding te maken met het directe lijn spraak kanaal en de bot.
1. Wanneer de gebruiker op een knop drukt, Voice audio streams van de microfoon. (Of audio wordt voortdurend vastgelegd wanneer een aangepast tref woord wordt gebruikt.)
1. Als er een aangepast tref woord wordt gebruikt, vindt de detectie van het sleutel woord op het lokale apparaat plaats, beperking audio streaming naar de Cloud.
1. De voor beeld-client toepassing maakt gebruik van Speech SDK om verbinding te maken met direct line speech Channel en streaming-audio.
1. U kunt desgewenst een hogere nauw keurigheid van het tref woord controleren op de service.
1. De audio wordt door gegeven aan de spraakherkennings service en naar tekst getranscribeerd.
1. De herkende tekst wordt door gegeven aan de echo-bot als een bot-Framework activiteit.
1. De antwoord tekst wordt omgezet in audio door de TTS-Service (tekst naar spraak) en gestreamd naar de client toepassing voor afspelen.

<!-- svg src in User Story 1754106 -->
![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "De stroom van het spraak kanaal")

> [!NOTE]
> Voor de stappen in deze zelf studie is geen betaalde service vereist. Als nieuwe Azure-gebruiker kunt u tegoed gebruiken van uw gratis Azure-proef abonnement en de gratis laag van de speech-service om deze zelf studie te volt ooien.

Deze zelfstudie bevat de volgende onderwerpen:
> [!div class="checklist"]
> * Nieuwe Azure-resources maken
> * Het voor beeld van de echo-bot bouwen, testen en implementeren op een Azure App Service
> * Uw bot registreren met een direct-lijn spraak kanaal
> * De Windows Voice Assistant-client bouwen en uitvoeren om te communiceren met uw ECHO bot
> * Aangepaste trefwoord activering toevoegen
> * Meer informatie over het wijzigen van de taal van de herkende en gesp roken spraak

## <a name="prerequisites"></a>Vereisten

Dit is wat u moet doen om deze zelf studie te volt ooien:

- Een Windows 10-PC met een werkende microfoon en luid sprekers (of hoofd telefoon)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) of hoger, waarop de **ASP.net-en Web Development** -werk belasting is geïnstalleerd
- [.NET Framework runtime 4.6.1](https://dotnet.microsoft.com/download) of hoger
- Een Azure-account. [Gratis aanmelden](https://azure.microsoft.com/free/cognitive-services/)
- Een [GitHub](https://github.com/)-account
- [Git voor Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

De client-app die u in deze zelf studie maakt, maakt gebruik van een aantal Azure-Services. Als u de retour tijd voor reacties van uw bot wilt beperken, moet u ervoor zorgen dat deze services zich in dezelfde Azure-regio bevinden. In deze sectie maakt u een resource groep in de regio **VS-West** . Deze resource groep wordt gebruikt bij het maken van afzonderlijke resources voor het bot Framework, het directe lijn spraak kanaal en de spraak service.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Een resource groep maken </a>
1. U wordt gevraagd om de volgende informatie op te geven:
   * Stel **abonnement** in op **gratis proef versie** (u kunt ook een bestaand abonnement gebruiken).
   * Voer een naam in voor de **resource groep**. We raden **SpeechEchoBotTutorial-ResourceGroup** aan.
   * Selecteer in de vervolg keuzelijst **regio** **VS West**.
1. Klik op **Controleren en maken**. U ziet een banner waarvoor Lees **validatie is geslaagd**.
1. Klik op **Create**. Het kan enkele minuten duren om de resource groep te maken.
1. Net als bij de resources die u later in deze zelf studie maakt, is het een goed idee om deze resource groep vast te maken aan uw dash board voor eenvoudige toegang. Als u deze resource groep wilt vastmaken, klikt u op het speld pictogram rechts van de naam van de resource groep.

### <a name="choosing-an-azure-region"></a>Een Azure-regio kiezen

Als u voor deze zelf studie een andere regio wilt gebruiken, kunnen de volgende factoren uw keuzes beperken:

* Zorg ervoor dat u een [ondersteunde Azure-regio](regions.md#voice-assistants)gebruikt.
* Het directe lijn spraak kanaal maakt gebruik van de tekst-naar-spraak-service, die standaard en Neural stemmen heeft. Neural stemmen zijn [beperkt tot specifieke Azure-regio's](regions.md#standard-and-neural-voices).

Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie over regio's.

## <a name="create-resources"></a>Resources maken

Nu u een resource groep in een ondersteunde regio hebt, is de volgende stap het maken van afzonderlijke resources voor elke service die u in deze zelf studie gaat gebruiken.

### <a name="create-a-speech-service-resource"></a>Een Spraak-serviceresource maken

Volg deze instructies om een spraak bron te maken:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een speech service-resource maken </a>
4. U wordt gevraagd om de volgende informatie op te geven:
   * Geef uw resource een **naam**. U kunt **het beste SpeechEchoBotTutorial-speech**
   * Zorg ervoor dat een **gratis proef versie** is geselecteerd voor het **abonnement**.
   * Selecteer voor **locatie** de optie **VS-West**.
   * Selecteer voor **prijs categorie** de optie **F0**. Dit is de gratis laag.
   * Voor **resource groep** selecteert u **SpeechEchoBotTutorial-ResourceGroup**.
5. Nadat u alle vereiste gegevens hebt ingevoerd, klikt u op **maken**. Het kan een paar minuten duren voordat de resource is gemaakt.
6. Verderop in deze zelf studie hebt u abonnements sleutels voor deze service nodig. U kunt deze sleutels op elk gewenst moment openen vanuit het **overzicht** van uw resource (sleutels beheren) of **sleutels**.

Controleer op dit punt of de resource groep (**SpeechEchoBotTutorial-ResourceGroup**) een spraak bron heeft:

| Naam | Type  | Locatie |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | VS - west |

### <a name="create-an-azure-app-service-plan"></a>Een Azure App Service-plan maken

De volgende stap is het maken van een App Service-abonnement. Een App Service-plan definieert een set rekenresources waarmee een web-app kan worden uitgevoerd.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Een Azure App Service plan maken </a>
4. U wordt gevraagd om de volgende informatie op te geven:
   * Stel **abonnement** in op **gratis proef versie** (u kunt ook een bestaand abonnement gebruiken).
   * Voor **resource groep** selecteert u **SpeechEchoBotTutorial-ResourceGroup**.
   * Geef uw resource een **naam**. We raden **SpeechEchoBotTutorial-AppServicePlan** aan
   * Selecteer voor **besturings systeem** **Windows**.
   * Selecteer voor **regio** **VS-West**.
   * Zorg ervoor dat **standaard S1** is geselecteerd voor de **prijs categorie**. Dit moet de standaard waarde zijn. Als dat niet het geval is, moet u ervoor zorgen dat u het **besturings systeem** instelt op **Windows** zoals hierboven wordt beschreven.
5. Klik op **Controleren en maken**. U ziet een banner waarvoor Lees **validatie is geslaagd**.
6. Klik op **Create**. Het kan enkele minuten duren om de resource groep te maken.

Controleer op dit punt of de resource groep (**SpeechEchoBotTutorial-ResourceGroup**) twee resources heeft:

| Naam | Type  | Locatie |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service-plan | VS - west |
| SpeechEchoBotTutorial-Speech | Cognitive Services | VS - west |

## <a name="build-an-echo-bot"></a>Een echo bot bouwen

Nu u enkele resources hebt gemaakt, gaan we een bot bouwen. We beginnen met het voor beeld van de echo bot, zoals de naam al aangeeft, ECHO de tekst die u hebt ingevoerd als antwoord. U hoeft zich geen zorgen te maken dat de voorbeeld code klaar is om zonder wijzigingen te worden gebruikt. Het is geconfigureerd om te werken met het directe-lijn spraak kanaal dat we gaan verbinden nadat we de bot hebben geïmplementeerd in Azure.

> [!NOTE]
> De volgende instructies, evenals aanvullende informatie over de echo-bot, zijn beschikbaar in het Leesmij-bestand van het [voor beeld op github](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Het voor beeld van de bot op uw computer uitvoeren

1. De opslag plaats voor beelden klonen:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Start Visual Studio.
3. Selecteer in de werk balk **File**  >  **Open**  >  **Project/Solution** en open de echo bot project-oplossing:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Nadat het project is geladen, drukt u op <kbd>F5</kbd> om het project te bouwen en uit te voeren.
5. Er wordt een browser geopend en er wordt een scherm weer gegeven dat er ongeveer zo uitziet.
    > [!div class="mx-imgBorder"]
    > [![Scherm afbeelding toont de pagina EchoBot met het bericht uw bot is klaar.](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot die worden uitgevoerd op localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Het bot-voor beeld testen met de bot Framework-emulator

De [bot Framework-emulator](https://github.com/microsoft/botframework-emulator) is een bureau blad-app waarmee bot-ontwikkel aars hun bots op een lokale manier kunnen testen en debuggen (of extern via een tunnel). De emulator accepteert getypte tekst als de invoer (niet Voice). De bot reageert ook met tekst. Volg deze stappen om de bot Framework emulator te gebruiken om uw ECHO-bot die lokaal wordt uitgevoerd, te testen met tekst invoer en tekst uitvoer. Nadat we de bot hebben geïmplementeerd in azure, zullen we deze testen met spraak invoer en spraak uitvoer.

1. De [bot Framework-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versie 4.3.0 of hoger installeren
2. Start de bot Framework emulator en open uw bot:
   * **Bestand**  ->  **Open bot**.
3. Voer de URL in voor uw bot. Bijvoorbeeld:

   ```
   http://localhost:3978/api/messages
   ```
   en klik op verbinden.
4. De bot moet u begroeten met ' Hallo en Welkom! ' . Typ een tekst bericht en bevestig dat u een reactie van de bot ontvangt.
5. Zo kan een uitwisseling van communicatie met een echo bot eruit zien als: [ ![scherm afbeelding toont de bot Framework emulator.](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework Emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Uw bot implementeren op een Azure App Service

De volgende stap is het implementeren van de echo-bot in Azure. Er zijn enkele manieren om een bot te implementeren, maar in deze zelf studie wordt u geadviseerd om rechtstreeks vanuit Visual Studio te publiceren.

> [!NOTE]
> U kunt ook een bot implementeren met behulp van de [Azure cli](/azure/bot-service/bot-builder-deploy-az-cli) -en [implementatie sjablonen](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot).

> [!NOTE]
> Als **publiceren...** niet wordt weer gegeven tijdens het uitvoeren van de volgende stappen, gebruikt u Visual Studio Installer om de **ASP.net-en Web Development** -werk belasting toe te voegen.

1. Open in Visual Studio de echo-bot die is geconfigureerd voor gebruik met een direct-lijn spraak kanaal:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. Klik in de **Solution Explorer** met de rechter muisknop op het **EchoBot** -project en selecteer **publiceren...**
1. Er wordt een nieuw venster met de titel **Publish** geopend.
1. Selecteer **Azure**, klik op **volgende**, selecteer **Azure app service (Windows)**, klik op **volgende** en klik vervolgens op **een nieuw Azure app service maken...** door het groene plus teken.
1. Wanneer het venster **app service (Windows)** wordt weer gegeven:
   * Klik op **een account toevoegen** en meld u aan met de referenties van uw Azure-account. Als u al bent aangemeld, selecteert u het account dat u wilt in de vervolgkeuzelijst.
   * Voor de **naam** moet u een wereld wijd unieke naam invoeren voor uw bot. Deze naam wordt gebruikt om een unieke bot-URL te maken. Er wordt een standaard waarde ingevuld, inclusief de datum en tijd (bijvoorbeeld: "EchoBot20190805125647"). U kunt de standaard naam voor deze zelf studie gebruiken.
   * Voor het **abonnement** stelt u deze in op **gratis proef versie**
   * Voor **resource groep** selecteert u **SpeechEchoBotTutorial-ResourceGroup**
   * Voor **hosting plan** selecteert u **SpeechEchoBotTutorial-AppServicePlan**
1. Klik op **Create**. Klik in het laatste scherm van de wizard op **volt ooien**.
1. Klik aan de rechter kant van het venster publiceren op **publiceren** . Visual Studio implementeert de bot naar Azure.
1. Er wordt een bericht weer gegeven in het uitvoer venster van Visual Studio dat er als volgt uitziet:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. De standaard browser moet een pagina openen en weer geven met de volgende tekst: ' uw bot is klaar! '.
1. Controleer op dit punt de resource groep **SpeechEchoBotTutorial-ResourceGroup** in de Azure Portal en bevestig deze drie resources:

| Naam | Type  | Locatie |
|------|-------|----------|
| EchoBot20190805125647 | App Service | VS - west |
| SpeechEchoBotTutorial-AppServicePlan | App Service-plan | VS - west |
| SpeechEchoBotTutorial-Speech | Cognitive Services | VS - west |

## <a name="enable-web-sockets"></a>Websockets inschakelen

U moet een kleine configuratie wijziging aanbrengen zodat uw bot met behulp van websockets met het directe lijn spraak kanaal kan communiceren. Volg deze stappen om Web sockets in te scha kelen:

1. Ga naar het [Azure Portal](https://portal.azure.com)en klik op uw app service. De resource moet een naam hebben die vergelijkbaar is met **EchoBot20190805125647** (uw unieke app-naam).
2. Klik in het navigatie deel venster links onder **instellingen** op **configuratie**.
3. Selecteer het tabblad **algemene instellingen** .
4. Zoek de wissel knop voor **Web sockets** en stel deze **in op aan**.
5. Klik op **Opslaan**.

> [!TIP]
> U kunt de besturings elementen aan de bovenkant van de Azure App Service-pagina gebruiken om de service te stoppen of opnieuw te starten. Dit kan handig zijn bij het oplossen van problemen.

## <a name="create-a-channel-registration"></a>Een kanaal registratie maken

Nu u een Azure App Service hebt gemaakt om uw bot te hosten, is de volgende stap het maken van een **bot-kanaal registratie**. Het maken van een kanaal registratie is een vereiste voor het registreren van uw bot met bot Framework-kanalen, waaronder direct line speech Channel. Zie [een bot verbinden met kanalen](/azure/bot-service/bot-service-manage-channels)als u meer wilt weten over hoe bots kanalen gebruiken.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Een Azure bot-kanaal registratie maken </a>
2. U wordt gevraagd om de volgende informatie op te geven:
   * Voor **bot-ingang** voert u **SpeechEchoBotTutorial-BotRegistration-# # #** # in en vervangt u door een wille keurig **####** aantal van uw keuze. Houd er rekening mee dat de bot-ingang globaal uniek moet zijn. Als u een bot-ingang opgeeft, maar het fout bericht ophalen _de aangevraagde bot-id niet beschikbaar is_, kiest u een ander getal. In de onderstaande voor beelden hebben we 8726 gebruikt.
   * Selecteer voor **abonnement** een **gratis proef versie**.
   * Voor **resource groep** selecteert u **SpeechEchoBotTutorial-ResourceGroup**.
   * Selecteer voor **locatie** de optie **VS-West**.
     * Selecteer voor **prijs categorie** de optie **F0**.
     * Voor **berichten eind punt** voert u de URL voor uw web-app in waarbij het `/api/messages` pad aan het einde is toegevoegd. Als uw wereld wijde unieke app-naam bijvoorbeeld **EchoBot20190805125647** is, zou uw e-mail eindpunt: `https://EchoBot20190805125647.azurewebsites.net/api/messages/` .
     * Voor **Application Insights** kunt u dit instellen op **uit**. Zie [bot Analytics](/azure/bot-service/bot-service-manage-analytics)voor meer informatie.
     * **Automatisch maken van app-id en wacht woord** negeren.
5. Klik onder aan de Blade **registratie van bot-kanalen** op **maken**.

Controleer op dit punt de resource groep **SpeechEchoBotTutorial-ResourceGroup** in de Azure Portal. Er moeten nu ten minste vier resources worden weer gegeven:

| Naam | Type  | Locatie |
|------|-------|----------|
| EchoBot20190805125647 | App Service | VS - west |
| SpeechEchoBotTutorial-AppServicePlan | App Service-plan | VS - west |
| SpeechEchoBotTutorial-BotRegistration-8726 | Registratie van bot-kanalen | Globaal |
| SpeechEchoBotTutorial-Speech | Cognitive Services | VS - west |

> [!IMPORTANT]
> In de bron van de bot-kanalen wordt de wereld wijde regio weer gegeven, zelfs als u vs West hebt geselecteerd. Dit is normaal.

## <a name="optional-test-in-web-chat"></a>Optioneel: testen in Web Chat

De registratie pagina van Azure bot-kanalen bevat een **test in de optie Web Chat** onder **bot Management**. Het werkt niet standaard met uw bot, omdat Webchat moet verifiëren tegen uw bot. Als u uw geïmplementeerde bot wilt testen met tekst invoer, volgt u de onderstaande stappen. Houd er rekening mee dat deze stappen optioneel zijn en niet zijn vereist om door te gaan met de volgende stappen van de zelf studie. 

1. Zoek en open uw **EchoBotTutorial-BotRegistration-# #** # #-resource in de [Azure Portal](https://portal.azure.com)
1. Selecteer in de **bot Management** -navigatie de optie **instellingen**. Kopieer de waarde onder **micro soft app id**
1. Open de Visual Studio EchoBot-oplossing. Zoek in het Solution Explorer naar en dubbel klik op **appsettings.jsop**
1. Vervang de lege teken reeks naast **MicrosoftAppId** in het JSON-bestand met de GEKOPIEERDE id-waarde
1. Terug naar de Azure Portal, in **bot Management** -navigatie, selecteert **u instellingen** en klikt u op **(beheren)** naast **micro soft app-id**
1. Klik op **Nieuw client geheim**. Voeg een beschrijving toe (bijvoorbeeld Web Chat) en klik op **toevoegen**. Het nieuwe geheim kopiëren
1. Vervang de lege teken reeks naast **MicrosoftAppPassword** in het JSON-bestand door de gekopieerde geheime waarde
1. Sla het JSON-bestand op. Het ziet er ongeveer als volgt uit:
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. Publiceer de app opnieuw (Klik met de rechter muisknop op **EchoBot** -project in Visual Studio Solution Explorer, selecteer **publiceren...** en klik op de knop **publiceren** )
10. U bent nu klaar om de bot in web chat te testen.

## <a name="register-the-direct-line-speech-channel"></a>Het directe-lijn spraak kanaal registreren

Nu is het tijd om uw bot te registreren met het directe-lijn spraak kanaal. Met dit kanaal maakt u een verbinding tussen uw bot en een client-app die is gecompileerd met de spraak-SDK.

1. Zoek en open uw **SpeechEchoBotTutorial-BotRegistration-# #** # #-resource in de [Azure Portal](https://portal.azure.com).
1. Selecteer in de **bot Management** -navigatie de optie **kanalen**.
   * Klik onder **meer kanalen** op **direct line speech**.
   * Bekijk de tekst op de pagina met de titel **directe lijn spraak configureren** en vouw vervolgens de vervolg keuzelijst **cognitieve service account** uit.
   * Selecteer de spraak resource die u eerder hebt gemaakt (bijvoorbeeld **SpeechEchoBotTutorial-speech**) in het menu om uw bot te koppelen aan de sleutel van uw spraak abonnement.
   * Negeer de rest van de optionele velden.
   * Klik op **Opslaan**.

1. Klik in de navigatie van het **bot-beheer** op **instellingen**.
   * Schakel het selectie vakje **streaming-eind punt inschakelen** in het vak in. Dit is nodig om een communicatie protocol te maken dat is gebaseerd op Web-sockets tussen uw bot en het directe lijn spraak kanaal.
   * Klik op **Opslaan**.

> [!TIP]
> Als u meer informatie wilt, raadpleegt u [een bot aansluiten op direct line speech](/azure/bot-service/bot-service-channel-connect-directlinespeech). Deze pagina bevat aanvullende informatie en bekende problemen.

## <a name="run-the-windows-voice-assistant-client"></a>De Windows Voice Assistant-client uitvoeren

In deze stap gaat u de Windows Voice Assistant-client uitvoeren. De client is een Windows Presentation Foundation-app (WPF) in C# die de [Speech SDK](./speech-sdk.md) gebruikt om de communicatie met uw bot te beheren met behulp van het directe spraak kanaal. Gebruik het om te communiceren met en test uw bot voordat u een aangepaste client-app schrijft. Het is een open-source, dus u kunt het uitvoer bare bestand downloaden en uitvoeren, of het zelf bouwen.

De Windows Voice Assistant-client beschikt over een eenvoudige gebruikers interface waarmee u de verbinding met uw bot kunt configureren, het tekst gesprek kunt bekijken, de activiteiten van de bot-Framework in JSON-indeling kunt bekijken en adaptieve kaarten weer geven. Het biedt ook ondersteuning voor het gebruik van aangepaste tref woorden. U gebruikt deze client om met uw bot te praten en een antwoord op de telefoon te ontvangen.

> [!NOTE]
> Controleer op dit punt of uw microfoon en luid sprekers zijn ingeschakeld en werken.

1. Ga naar de GitHub-opslag plaats voor de [Windows Voice Assistant-client](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
1. Volg de instructies die beschikbaar zijn voor
   * down load een vooraf gemaakt uitvoerbaar bestand in een ZIP-pakket om uit te voeren, of
   * bouw het uitvoer bare bestand zelf door de opslag plaats te klonen en het project te bouwen.

1. Start de- `VoiceAssistantClient.exe` client toepassing en configureer deze om verbinding te maken met uw bot door de instructies in de GitHub-opslag plaats te volgen.
1. Klik op **opnieuw verbinding maken** en zorg ervoor dat het bericht **nieuwe conversatie gestart-type is of druk op de knop microfoon**.
1. Laten we dit testen. Klik op de knop microfoon en spreek enkele woorden in het Engels in. De herkende tekst wordt weer gegeven tijdens het spreken. Wanneer u klaar bent met spreken, reageert de bot op een eigen stem, met de tekst ' echo ' gevolgd door de herkende woorden.
1. U kunt ook tekst gebruiken om te communiceren met de bot. U hoeft alleen de tekst in de onderste balk te typen. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Problemen met de Windows Voice Assistant-client oplossen

Als er een fout bericht wordt weer gegeven in het hoofd venster van de app, gebruikt u deze tabel om de fout te identificeren en op te lossen:

| Fout | Wat moet u doen? |
|-------|----------------------|
|Fout (AuthenticationFailure): bijwerken van WebSocket is mislukt met een verificatie fout (401). Controleren op juiste abonnements sleutel (of autorisatie token) en regio naam| Controleer op de pagina instellingen van de app of u de sleutel voor spraak abonnementen en de regio juist hebt ingevoerd.<br>Zorg ervoor dat de spraak sleutel en de sleutel regio correct zijn ingevoerd. |
|Fout (ConnectionFailure): de verbinding is door de externe host gesloten. Fout code: 1011. Fout Details: er kan geen verbinding worden gemaakt met de bot voordat een bericht wordt verzonden | Zorg ervoor dat u het selectie vakje [streaming-eind punt inschakelen](#register-the-direct-line-speech-channel) en/of [ **websockets**](#enable-web-sockets) op op hebt ingeschakeld.<br>Zorg ervoor dat uw Azure App Service actief is. Als dat het geval is, start u de App Service opnieuw op.|
|Fout (ConnectionFailure): de verbinding is door de externe host gesloten. Fout code: 1002. Fout Details: de server heeft de status code ' 503 ' geretourneerd wanneer de status code ' 101 ' werd verwacht | Zorg ervoor dat u het selectie vakje [streaming-eind punt inschakelen](#register-the-direct-line-speech-channel) en/of [ **websockets**](#enable-web-sockets) op op hebt ingeschakeld.<br>Zorg ervoor dat uw Azure App Service actief is. Als dat het geval is, start u de App Service opnieuw op.|
|Fout (ConnectionFailure): de verbinding is door de externe host gesloten. Fout code: 1011. Fout Details: de antwoord status code geeft geen succes aan: 500 (InternalServerError)| Uw bot heeft een Neural-stem opgegeven in het veld [uitspreken](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) van uitvoer activiteit, maar de Azure-regio die aan uw spraak abonnements sleutel is gekoppeld, biedt geen ondersteuning voor Neural stemmen. Zie de [standaard-en Neural stemmen](./regions.md#standard-and-neural-voices).|

Als uw probleem niet wordt opgelost in de tabel, raadpleegt u [spraak assistenten: veelgestelde vragen](faq-voice-assistants.md). Als uw probleem nog steeds niet kan worden opgelost nadat u alle stappen in deze zelf studie hebt uitgevoerd, voert u een nieuw probleem in op de GitHub-pagina van de  [Voice Assistant](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues).

#### <a name="a-note-on-connection-time-out"></a>Een opmerking over de time-out van de verbinding

Als u verbonden bent met een bot en er in de afgelopen vijf minuten geen activiteit is opgetreden, sluit de service automatisch de WebSocket-verbinding met de client en met de bot. Dit is standaard. Er wordt een bericht weer gegeven in de onderste balk: *Er is een time-out opgetreden voor de actieve verbinding, maar u kunt opnieuw verbinding maken op aanvraag*. U hoeft niet op de knop opnieuw verbinding maken te klikken. u hoeft alleen maar op de knop microfoon te drukken en te praten, een SMS-bericht te typen of het tref woord te zeggen (als er een is ingeschakeld). De verbinding wordt automatisch hersteld.  
### <a name="view-bot-activities"></a>Bot-activiteiten weer geven

Elke bot verzendt en ontvangt **activiteiten** berichten. In het venster **activiteiten logboek** van de Windows Voice Assistant-client ziet u getimede logboeken met elke activiteit die de client heeft ontvangen van de bot. U kunt ook de activiteiten zien die de client naar de bot heeft verzonden met behulp van de- [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)  methode. Wanneer u een logboek item selecteert, worden de details van de gekoppelde activiteit als JSON weer gegeven.

Hier volgt een voor beeld van een JSON van een activiteit die de client heeft ontvangen:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Zie [velden in de activiteit](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)voor meer informatie over wat er wordt geretourneerd in de JSON-uitvoer. Voor het doel van deze zelf studie kunt u zich richten op de velden [tekst](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) en [spreken](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) .

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Client bron code weer geven voor aanroepen naar de Speech SDK

De Windows Voice Assistant-client maakt gebruik van het NuGet-pakket [micro soft. CognitiveServices. speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), dat de Speech SDK bevat. Een goede plaats om te beginnen met het controleren van de voorbeeld code is de methode InitSpeechConnector () in het bestand [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) , waarmee deze twee Speech SDK-objecten worden gemaakt:
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -Voor configuratie-instellingen (bijvoorbeeld sleutel voor spraak abonnementen, sleutel regio)
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) -De kanaal verbinding en gebeurtenissen van client abonnementen beheren voor het verwerken van herkende spraak-en bot-reacties.

## <a name="add-custom-keyword-activation"></a>Aangepaste trefwoord activering toevoegen

De Speech SDK ondersteunt aangepaste trefwoord activering. Net als bij "Hey Cortana" voor de assistent van micro soft kunt u een app schrijven die voortdurend naar een tref woord naar keuze wordt geluisterd. Houd er rekening mee dat een tref woord één woord of een woord groep met meerdere woorden kan zijn.

> [!NOTE]
> Het *tref woord* term wordt vaak door elkaar gebruikt met de term *Wake woord*, en u ziet dat beide worden gebruikt in micro soft-documentatie.

De detectie van het tref woord wordt uitgevoerd op de client-app. Als u een tref woord gebruikt, wordt audio alleen gestreamd naar het directe-lijn spraak kanaal als het sleutel woord wordt gedetecteerd. Het directe lijn spraak kanaal bevat een onderdeel met de naam verificatie van het *sleutel woord (KWV)*, dat complexere verwerking in de Cloud biedt om te controleren of het tref woord dat u hebt gekozen aan het begin van de audio stroom is. Als de verificatie van de sleutel woorden is geslaagd, communiceert het kanaal met de bot.

Volg deze stappen om een trefwoord model te maken, de Windows Voice Assistant-client te configureren voor het gebruik van dit model en tot slot het te testen met uw bot.

1. Volg deze instructies voor [het maken van een aangepast tref woord met behulp van de speech-service](./custom-keyword-basics.md).
2. Pak het model bestand uit dat u in de vorige stap hebt gedownload. Dit moet een naam voor het tref woord zijn. U zoekt naar een bestand met de naam `kws.table` .
3. Ga in de Windows Voice Assistant-client naar het menu **instellingen** (Zoek het tandwiel pictogram in de rechter bovenhoek). Zoek het pad naar het **model bestand** en voer de volledige padnaam op voor het `kws.table` bestand uit stap 2.
4. Zorg ervoor dat het selectie vakje is **ingeschakeld**. Dit bericht wordt weer gegeven naast het selectie vakje: ' Luister naar het tref woord bij de volgende verbinding '. Als u het verkeerde bestand of een ongeldig pad hebt, wordt een fout bericht weer gegeven.
5. Voer de sleutel van uw spraak **abonnement**, de regio van de **abonnements sleutel** en klik vervolgens op **OK** om het menu **instellingen** te sluiten.
6. Klik op **opnieuw verbinding maken**. Er wordt een bericht weer gegeven met de volgende tekst: ' nieuw gesprek gestart-type, druk op de microfoon knop of zeg het tref woord '. De app luistert nu voortdurend.
7. Spreek een wille keurige woord groep in die begint met uw tref woord. Bijvoorbeeld:**{uw tref woord}**, wat is het tijdstip? '. U hoeft niet te onderbreken na het uttering van het sleutel woord. Wanneer u klaar bent, gebeuren er twee dingen:
   * U ziet een transcriptie van uw Spaak
   * Kort daarna kunt u het antwoord van de bot horen
8. Ga door met het experimenteren met de drie invoer typen die uw bot ondersteunt:
   * Getypte tekst in de onderste balk
   * Het microfoon pictogram drukken en spreken
   * Een woord groep zeggen die begint met uw tref woord

### <a name="view-the-source-code-that-enables-keyword"></a>De bron code weer geven waarmee sleutel woord wordt ingeschakeld

In de Windows Voice Assistant-client bron code bekijkt u deze bestanden om de code te controleren die wordt gebruikt om de detectie van tref woorden in te scha kelen:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) bevat een aanroep van de methode Speech SDK [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) , die wordt gebruikt om het model te instantiëren vanuit een lokaal bestand op schijf.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) bevat een aanroep naar de Speech SDK [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) -methode, waarmee doorlopende trefwoord detectie wordt geactiveerd.

## <a name="optional-change-the-language-and-bot-voice"></a>Beschrijving De taal-en bot-stem wijzigen

De bot die u hebt gemaakt, luistert naar en reageert in het Engels, met een standaard Amerikaans Engels tekst-naar-spraak-stem. U bent echter niet beperkt tot het gebruik van Engels of een standaard stem. In deze sectie leert u hoe u de taal kunt wijzigen die door uw bot wordt geluisterd en hoe deze reageert. U leert ook hoe u een andere stem selecteert voor die taal.

### <a name="change-the-language"></a>De taal wijzigen

U kunt kiezen uit een van de talen die worden vermeld in de tabel met [spraak-naar-tekst](language-support.md#speech-to-text) . In het onderstaande voor beeld wijzigen we de taal in het Duits.

1. Open de Windows Voice Assistant-client-app, klik op de knop instellingen (bovenste tandwiel pictogram) en voer `de-de` in het veld taal in (dit is de waarde voor de land instelling die wordt vermeld in de tabel [met spraak-naar-tekst](language-support.md#speech-to-text) ). Hiermee wordt de gesp roken taal herkend en wordt de standaard waarde genegeerd `en-us` . Dit geeft ook een direct lijn spraak kanaal om een standaard Duitse stem te gebruiken voor het antwoord van de bot.
2. Sluit de pagina instellingen en klik op de knop opnieuw verbinding maken om een nieuwe verbinding met uw ECHO-bot tot stand te brengen.
3. Klik op de knop microfoon en zeg een zin in het Duits. U ziet de herkende tekst en de reactie van de echo met de standaard Duitse stem.

### <a name="change-the-default-bot-voice"></a>De standaard bot voice wijzigen

Het selecteren van de stem tekst-naar-spraak en het beheren van de uitspraak kan worden uitgevoerd als de bot het antwoord opgeeft in de vorm van een SSML ( [Speech synthese Markup Language](speech-synthesis-markup.md) ) in plaats van eenvoudige tekst. De echo bot maakt geen gebruik van SSML, maar we kunnen de code eenvoudig aanpassen. In het onderstaande voor beeld voegen we SSML toe aan het antwoord op de echo-bot, zodat de Duitse Voice Stefan Apollo (een mannelijk stem) wordt gebruikt in plaats van de standaard vrouwelijke stem. Bekijk de lijst met [standaard stemmen](language-support.md#standard-voices) en [Neural stemmen](language-support.md#neural-voices) die voor uw taal worden ondersteund.

1. Laten we beginnen met het openen van `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` .
2. Ga naar deze twee regels:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Vervang deze door:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Bouw uw oplossing in Visual Studio en los eventuele build-fouten op.

Met het tweede argument in de methode ' MessageFactory. Text ' wordt het veld voor de [activiteit speak](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) in het antwoord van de bot ingesteld. Met de bovenstaande wijziging is deze vervangen van eenvoudige tekst in SSML om een niet-standaard Duitse stem op te geven.

### <a name="redeploy-your-bot"></a>Uw bot opnieuw implementeren

Nu u de nood zakelijke wijziging hebt aangebracht in de bot, is de volgende stap het opnieuw publiceren naar uw Azure App Service en probeer het uit:

1. Klik in het venster Solution Explorer met de rechter muisknop op het project **EchoBot** en selecteer **publiceren**.
2. De vorige implementatie configuratie is al geladen als standaard. Klik op **publiceren** naast **EchoBot20190805125647-Web Deploy**.
3. Het bericht **publiceren is voltooid** wordt weer gegeven in het venster Visual Studio-uitvoer en er wordt een webpagina geopend met het bericht ' uw bot is klaar! '.
4. Open de Windows Voice Assistant-client-app, klik op de knop instellingen (bovenste tandwiel pictogram) en zorg ervoor dat u nog steeds `de-de` in het veld taal hebt.
5. Volg de instructies in [de Windows Voice Assistant-client uitvoeren](#run-the-windows-voice-assistant-client) om opnieuw verbinding te maken met de zojuist geïmplementeerde bot, praat in de nieuwe taal en luistert u in die taal met de nieuwe stem.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelf studie geïmplementeerde ECHO-bot niet wilt blijven gebruiken, kunt u deze verwijderen en alle bijbehorende Azure-resources door simpelweg de Azure-resource groep **SpeechEchoBotTutorial-ResourceGroup** te verwijderen.

1. Klik vanuit de [Azure Portal](https://portal.azure.com)op **resource groepen** in de navigatie van Azure- **Services** .
2. Zoek de resource groep met de naam: **SpeechEchoBotTutorial-ResourceGroup**. Klik op de drie puntjes (...).
3. Selecteer **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eigen client-app bouwen met de Speech SDK](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>Zie ook

* Implementatie in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/locations/) in de buurt om de reactie tijd van bot te bekijken
* Implementeren in een [Azure-regio die hoogwaardige NEURAL TTS-stemmen ondersteunt](./regions.md#standard-and-neural-voices)
* Prijzen gekoppeld aan direct-lijn spraak kanaal:
  * [Prijzen van bot-service](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Bouwen en implementeren van uw eigen spraak ingeschakelde bot:
  * Een [bot-Framework-bot](https://dev.botframework.com/)bouwen. Registreer deze met [direct line speech Channel](/azure/bot-service/bot-service-channel-connect-directlinespeech) en [Pas uw bot aan voor spraak](/azure/bot-service/directline-speech-bot)
  * Bestaande [bot-Framework oplossingen](https://microsoft.github.io/botframework-solutions/index)verkennen: een [virtuele assistent](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) bouwen en [uitbreiden naar direct line speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
