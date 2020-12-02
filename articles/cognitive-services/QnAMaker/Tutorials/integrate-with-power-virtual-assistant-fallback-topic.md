---
title: 'Zelfstudie: Integreren met Power Virtual Agents - QnA Maker'
description: In deze zelfstudie gaat u de kwaliteit van uw Knowledge Base verbeteren met behulp van actief leren. Beoordeel, accepteer of weiger, of voeg toe zonder bestaande vragen te verwijderen of te wijzigen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 3801bb44fed6bf24788957c41de77c89b3025ae8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351109"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Zelfstudie: uw Knowledge Base toevoegen aan Power Virtual Agents
Maak een bot met [Power Virtual Agents](https://powervirtualagents.microsoft.com/) en breid deze uit om antwoorden uit uw Knowledge Base op te halen.

In deze zelfstudie leert u het volgende:

<!-- green checkmark -->
> [!div class="checklist"]
> * Een bot met Power Virtual Agents maken
> * Een onderwerp voor systeemterugval maken
> * QnA Maker als een actie toevoegen aan een onderwerp als een Power Automate-stroom
> * Een Power Automate-oplossing maken
> * Een Power Automate-stroom toevoegen aan uw oplossing
> * Power Virtual Agents publiceren
> * Power Virtual Agents testen en een antwoord ontvangen uit uw Knowledge Base van QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Een agent integreren met een Knowledge Base

Met [Power Virtual Agents](https://powervirtualagents.microsoft.com/) kunnen teams zonder code te schrijven krachtige bots maken met behulp van een begeleide grafische interface. U hebt dus geen gegevenswetenschappers of ontwikkelaars nodig.

In Power Virtual Agents maakt u een agent met een reeks onderwerpen (onderwerpgebieden) om vragen van gebruikers te beantwoorden door acties uit te voeren. Als er geen antwoord wordt gevonden, kan via een terugvalactie een standaardantwoord worden gegeven.

Configureer de agent om de vraag naar uw Knowledge Base te verzenden als onderdeel van de actie van een onderwerp, of als onderdeel van het onderwerppad *Systeemterugval*. Ze gebruiken beide een actie om verbinding te maken met uw Knowledge Base en een antwoord te retourneren.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate maakt verbinding met actie `GenerateAnswer`

Als u uw agent wilt verbinden met uw Knowledge Base, gebruikt u Power Automate om de actie te maken. Power Automate biedt een processtroom die verbinding maakt met de API `GenerateAnswer` van QnA Maker.

Nadat u de stroom hebt ontworpen en opgeslagen, is deze beschikbaar in een Power Automate-oplossing. Gebruik deze oplossing als een actie in uw agent.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Een agent verbinden met uw Knowledge Base

Hier volgt een overzicht van de stappen die nodig zijn om een agent in Power Virtual Agents te verbinden met een Knowledge Base in QnA Maker.

* In de portal van [QnA Maker](https://www.qnamaker.ai/):
    * Stel de Knowledge Base samen en publiceer deze.
    * Kopieer de gegevens van uw Knowledge Base, zoals de id, de runtime-eindpuntsleutel en de runtime-eindpunthost.
* In de portal van [Power Virtual Agents](https://powerva.microsoft.com/):
    * Bouw een onderwerp voor de agent.
    * Roep een actie aan (in Power Automate).
* In de portal van [Power Automate](https://us.flow.microsoft.com/):
    * Zoek de sjabloon _Generate answers using QnA Maker_.
    * Gebruik de sjabloon om de stroom te configureren voor het gebruik van [GenerateAnswer van QnA Maker](/connectors/cognitiveservicesqnamaker/).
        * In QnA Maker gepubliceerde gegevens van de Knowledge Base:
            * Id van de Knowledge Base
            * Eindpunthost voor QnA Maker-resource
            * Eindpuntsleutel voor QnA Maker-resource
        * Invoer - gebruikersquery
        * Uitvoer - antwoord uit de Knowledge Base
    * Maak een oplossing en voeg de stroom toe, of voeg de stroom toe aan een bestaande oplossing.
* Ga terug naar Power Virtual Agents:
    * Selecteer de uitvoer van de oplossing als een bericht voor een onderwerp.

## <a name="create-and-publish-a-knowledge-base"></a>Een knowledge base maken

1. Volg de [quickstart](../Quickstarts/create-publish-knowledge-base.md) om een Knowledge Base te maken. Het laatste gedeelte, over het maken van een bot, hoeft u niet te doen. Gebruik in plaats daarvan deze zelfstudie om een bot te maken met Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van gepubliceerde Knowledge Base-instellingen](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Neem de gepubliceerde Knowledge Base-instellingen (op de pagina **Settings**) over in de portal van [QnA Maker](https://www.qnamaker.ai/). U hebt deze gegevens nodig voor de [Power Automate-stap](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) om de verbinding met `GenerateAnswer` van QnA Maker te configureren.

1. Zoek in de QnA Maker-portal op de pagina **Settings** de eindpuntsleutel, de eindpunthost en de Knowledge Base-id.

## <a name="create-an-agent-in-power-virtual-agents"></a>Een agent maken in Power Virtual Agents

1. [Meld u aan bij Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Gebruik uw schoolaccount of e-mailaccount van het werk.

1. Als dit uw eerste bot is, komt u op de **startpagina** van de agent. Als dit niet uw eerste bot is, selecteert u de bot in de rechterbovenhoek van de pagina en selecteert u **+ Nieuwe bot**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van de startpagina van Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Onderwerpen in de bot

De agent gebruikt de verzameling onderwerpen voor het beantwoorden van vragen in uw onderwerpgebied. In deze zelfstudie bevat de agent al verschillende onderwerpen, onderverdeeld in gebruikersonderwerpen en systeemonderwerpen.

Selecteer **Onderwerpen** in het navigatiemenu aan de linkerkant om de onderwerpen te zien die standaard beschikbaar zijn.

> [!div class="mx-imgBorder"]
> ![Schermopname van onderwerpen die beschikbaar zijn in de agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Het onderwerp voor systeemterugval maken

Hoewel de agent vanuit elk onderwerp verbinding kan maken met uw Knowledge Base, gebruiken we in deze zelfstudie het onderwerp *systeemterugval*. Het terugvalonderwerp wordt gebruikt wanneer de agent geen antwoord kan vinden. De agent geeft de tekst van de gebruiker door aan de API `GenerateAnswer` van QnA Maker, ontvangt het antwoord uit de Knowledge Base en geeft dit als een bericht weer aan de gebruiker.

1. Ga naar de portal van **Power Virtual Agents** en selecteer rechtsboven [Instellingen](https://powerva.microsoft.com/#/) (pictogram van een tandwiel). Selecteer vervolgens **Systeemterugval**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het menu-item Systeemterugval in Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selecteer **+ Toevoegen** om een terugvalonderwerp voor het systeem toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het toevoegen van een terugvalonderwerp.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Nadat het onderwerp is toegevoegd, selecteert u **Naar terugvalonderwerp gaan** om het terugvalonderwerp te maken op het ontwerpcanvas.

    > [!TIP]
    > Als u terug wilt naar het terugvalonderwerp, kunt u het vinden in de sectie **Onderwerpen**, in het gedeelte **Systeemonderwerpen**.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Het ontwerpcanvas gebruiken om een actie toe te voegen

Gebruik het ontwerpcanvas van Power Virtual Agents om het terugvalonderwerp te koppelen aan uw Knowledge Base. Het onderwerp begint met de niet-herkende gebruikerstekst. Voeg een actie toe waarmee deze tekst wordt doorgegeven aan QnA Maker, waarna het antwoord als een bericht wordt weergegeven. De laatste stap is het weergeven van een antwoord. Dit wordt als een [afzonderlijke stap](#add-your-solutions-flow-to-power-virtual-agents) behandeld, verderop in deze zelfstudie.

In dit gedeelte maakt u de gespreksstroom voor het terugvalonderwerp.

1. De nieuwe terugvalactie bevat mogelijk al elementen in de gespreksstroom. Verwijder het item **Escaleren** door het menu **Opties** te selecteren.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="Gedeeltelijke schermopname van de gespreksstroom, met de optie Verwijderen gemarkeerd.":::

1. Selecteer de connector **+** naar het vak **Bericht** en selecteer vervolgens **Een actie aanroepen**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Gedeeltelijke schermopname van Een actie aanroepen.":::

1. Selecteer **Een stroom maken**. U gaat nu naar de portal van Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van Een stroom maken](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Power Automate wordt geopend met een nieuwe sjabloon. U gaat deze nieuwe sjabloon niet gebruiken.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Gedeeltelijke schermopname van Power Automate met sjabloon voor nieuwe stroom.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Een Power Automate-stroom maken om te verbinden met uw Knowledge Base

> [!NOTE]
> Op dit moment ondersteunt de Power Automate-sjabloon geen door QnA Maker beheerde (preview) eindpunten. Als u een door QnA Maker beheerde (preview) Knowledge Base aan Power Automate wilt toevoegen, slaat u deze stap over en voegt u de eindpunten er handmatig aan toe. 

Met de volgende procedure maakt u een Power Automate-stroom die het volgende doet:
* Ontvangt de inkomende tekst van de gebruiker en verstuurt deze naar QnA Maker.
* Retourneert het eerste antwoord naar uw agent.

1. Selecteer in **Power Automate** **Sjablonen** in het navigatiemenu aan de linkerkant. Als er wordt gevraagd of u de browserpagina wilt verlaten, bevestigt u dat.

1. Zoek op de pagina met sjablonen naar de sjabloon **Generate answers using QnA Maker** en selecteer vervolgens de sjabloon. Deze sjabloon bevat alle stappen voor het aanroepen van QnA Maker met de instellingen van uw Knowledge Base en het retourneren van het eerste antwoord.

1. Selecteer **Doorgaan** op het nieuwe scherm voor de QnA Maker-stroom.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="Gedeeltelijke schermopname van QnA Maker-sjabloonstroom met de knop Doorgaan gemarkeerd.":::

1. Selecteer het actievak **Antwoord genereren** en vul uw QnA Maker-instellingen in vanuit een vorige sectie met de titel [Een knowledge base maken](#create-and-publish-a-knowledge-base). **Servicehost** in de volgende afbeelding verwijst naar de host van uw Knowledge Base (**Host**) en heeft de indeling `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker`.


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="Gedeeltelijke schermafbeelding van QnA Maker-sjabloonstroom met de knop Antwoord genereren (preview-versie) gemarkeerd.":::

1. Selecteer **Opslaan** om de stroom op te slaan.

## <a name="create-a-solution-and-add-the-flow"></a>Een oplossing maken en de stroom toevoegen

De agent kan de stroom alleen vinden en er verbinding mee maken als de stroom is opgenomen in een Power Automate-oplossing.

1. Selecteer in de portal van Power Automate **Oplossingen** in het navigatiemenu aan de linkerkant.

1. Selecteer **+ Nieuwe oplossing**

1. Voer een weergavenaam in. De lijst met oplossingen bevat elke oplossing in uw organisatie of school. Kies een naamconventie waarmee u kunt filteren op alleen uw oplossingen. U kunt bijvoorbeeld uw e-mailadres als voorvoegsel toevoegen aan de naam van de oplossing: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Selecteer uw uitgever in de lijst met uitgevers.

1. Accepteer de standaardwaarden voor de naam en versie.

1. Selecteer **Maken** om het proces te voltooien.

## <a name="add-your-flow-to-the-solution"></a>Uw stroom toevoegen aan de oplossing

1. Selecteer de oplossing die u zojuist hebt gemaakt in de lijst met oplossingen. De oplossing staat als het goed is bovenaan de lijst. Als dat niet het geval is, kunt u zoeken op uw e-mailadres, dat immers deel uitmaakt van de naam van de oplossing.

1. Selecteer in de oplossing **+ Bestaan item toevoegen** en selecteer vervolgens **Stroom** in de lijst.

1. Zoek uw stroom in de lijst **Buiten oplossingen** en selecteer vervolgens **Toevoegen** om het proces te voltooien. Als er veel stromen zijn, kijkt u naar de kolom **Gewijzigd** om de meest recente stroom te vinden.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>De stroom van uw oplossing toevoegen aan Power Virtual Agents

1. Ga terug naar het browsertabblad met uw agent in Power Virtual Agents. Het ontwerpcanvas is als het goed is nog steeds geopend.

1. Als u een nieuwe stap in de stroom wilt invoegen, selecteert u boven het actievak **Bericht** de connector **+** . Selecteer vervolgens **Een actie aanroepen**.

1. Selecteer in het pop-upvenster **Stroom** de nieuwe stroom met de naam **Generate answers using QnA Maker...** . De nieuwe actie wordt weergegeven in de stroom.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Gedeeltelijke schermopname van het canvas in Power Virtual Agents met het onderwerp na het toevoegen van de QnA Maker-stroom.":::

1. Om de invoervariabele correct in te stellen op de QnA Maker-actie, selecteert u **Een variabele selecteren** en selecteert u vervolgens **bot.UnrecognizedTriggerPhrase**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Gedeeltelijke schermopname van het canvas in Power Virtual Agents met het selecteren van de invoervariabele.":::


1. Om de uitvoervariabele correct in te stellen op de QnA Maker-actie, selecteert u **UnrecognizedTriggerPhrase** in de actie **Bericht**, selecteert u vervolgens het pictogram om een variabele in te voegen, `{x}`, en selecteert u ten slotte **FinalAnswer**.

1. Selecteer in de contextwerkbalk **Opslaan** om de gegevens van het ontwerpcanvas voor het onderwerp op te slaan.

Het uiteindelijke canvas voor de agent ziet er zo uit.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding met het uiteindelijke canvas voor de agent met triggerwoordgroepen, acties en berichtsecties.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>De agent testen

1. Zet in het testvenster de optie **Bijhouden in verschillende onderwerpen** aan. Hiermee kunt u niet alleen de voortgang tussen onderwerpen bekijken, maar ook binnen één onderwerp.

1. Test de agent door de gebruikerstekst in de volgende volgorde in te voeren. Op het ontwerpcanvas worden de geslaagde stappen aangegeven met een groen vinkje.

    |Volgorde van vragen|Testvragen|Doel|
    |--|--|--|
    |1|Hello|Gesprek starten|
    |2|Openingstijden|Voorbeeldonderwerp. Dit is voor u geconfigureerd zonder dat u er iets voor hoeft te doen.|
    |3|Ja|In reactie op `Did that answer your question?`|
    |4|Uitstekend|In reactie op `Please rate your experience.`|
    |5|Ja|In reactie op `Can I help with anything else?`|
    |6|Hoe kan ik de doorvoerprestaties voor queryvoorspellingen verbeteren?|Met deze vraag wordt de terugvalactie geactiveerd, en wordt de tekst naar uw Knowledge Base verzonden om daar een antwoord te zoeken. Vervolgens wordt het antwoord weergegeven. De groene vinkjes controles voor de afzonderlijke acties geven aan dat de betreffende actie is gelukt.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Schermopname van een chatbot met op het canvas groene vinkjes voor geslaagde acties.":::

## <a name="publish-your-bot"></a>Uw bot publiceren

Als u de agent beschikbaar wilt maken voor alle leden van uw school of organisatie, moet u de agent publiceren.

1. Selecteer **Publiceren** in het navigatiemenu aan de linkerkant. Selecteer vervolgens **Publiceren** op de pagina.

1. Probeer uw bot uit op de demowebsite (zoek naar de link onder **Publiceren**).

    Er wordt een nieuwe webpagina geopend met uw bot. Vraag de bot dezelfde testvraag: `How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het uiteindelijke canvas voor de agent](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Uw bot delen

Als u de demo-website wilt delen, configureert u deze als een kanaal.

1. Selecteer **Beheren** > **Kanalen** in het navigatiemenu aan de linkerkant.

1. Select **Demowebsite** in de lijst met kanalen.

1. Kopieer de link en selecteer **Opslaan**. Plak de link naar de demowebsite in een e-mail en stuur deze naar de leden van uw school of organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de Knowledge Base, verwijdert u de QnA Maker-resources in de Azure-portal.

## <a name="next-step"></a>Volgende stap

[Analytische gegevens verkrijgen voor uw knowledge base](../How-To/get-analytics-knowledge-base.md)

Meer informatie over:
* [Power Virtual Agents](/power-virtual-agents/)
* [Power Automate](/power-automate/)
* [QnA Maker-connector](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) en de [instellingen voor de connector](/connectors/cognitiveservicesqnamaker/)