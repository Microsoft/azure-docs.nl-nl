---
title: 'Quickstart: Een knowledge base maken, trainen en publiceren - QnA Maker'
description: U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. Dit artikel bevat een voorbeeld van het maken van een QnA Maker-knowledge base op basis van een eenvoudige webpagina met veelgestelde vragen om vragen te beantwoorden in QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: c59529db0981a1071b76714c48aacaf675e4b17a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987904"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Quickstart: Uw QnA Maker-knowledge base maken, trainen en publiceren

U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. Dit artikel bevat een voorbeeld van het maken van een QnA Maker-knowledge base op basis van een eenvoudige webpagina met veelgestelde vragen om vragen te beantwoorden in QnA Maker.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.
> * Een QnA Maker-[resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) die in Azure Portal is gemaakt. Onthoud uw Azure Active Directory-id, het abonnement en de naam van de QnA-resource die u hebt geselecteerd tijdens het maken van de resource.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Uw eerste QnA Maker-knowledge base maken

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

1. Meld u bij de [QnAMaker.ai](https://QnAMaker.ai)-portal met uw Azure-referenties.

2. Selecteer **Een knowledge base maken** in de QnA Maker-portal.

3. Sla op de pagina **Maken** **Stap 1** over als u uw QnA Maker-resource al hebt.

    Selecteer **Een QnA-service maken** als u de resource nog niet hebt gemaakt. U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. Onthoud uw Azure Active Directory-id, het abonnement en de naam van de QnA-resource die u hebt geselecteerd tijdens het maken van de resource.

    Ga terug naar de QnA Maker-portal, vernieuw de browserpagina en ga door naar **Stap 2** als u klaar bent met het maken van de resource in Azure Portal.

4. Selecteer in **Stap 2** uw Active Directory, abonnement en service (resource), en de taal voor alle Knowledge Bases die in de service worden gemaakt.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Schermopname van het selecteren van een knowledge base in de QnA Maker-service":::

5. Geef uw knowledge base in **Stap 3** de naam **Mijn voorbeeld-QnA-KB**.

6. Configureer in **Stap 4** de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Schakel uitpakken van meerdere paden in vanuit URL's, .pdf- of .docx-bestanden.**|Ingeschakeld|
    |**Standaardtekst met meerdere paden**| Selecteer een optie|
    |**+ URL toevoegen**|`https://www.microsoft.com/en-us/software-download/faq`|
    |**Chit-chat**|**Professional** selecteren|

7. Selecteer in **stap 5**, **Uw KB maken**.

    Tijdens het extractieproces, dat enige tijd duurt, wordt het document gelezen en worden de vragen en antwoorden geïdentificeerd.

    Nadat QnA Maker de knowledge base heeft gemaakt, wordt de pagina **Knowledge Base** geopend. U kunt de inhoud van de knowledge base op deze pagina bewerken.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

1. Meld u bij de [QnAMaker.ai](https://QnAMaker.ai)-portal met uw Azure-referenties.

2. Selecteer **Een knowledge base maken** in de QnA Maker-portal.

3. Sla op de pagina **Maken** **Stap 1** over als u uw QnA Maker-resource al hebt.

    Selecteer **Een QnA-service maken** als u de resource nog niet hebt gemaakt. U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. Onthoud uw Azure Active Directory-id, het abonnement en de naam van de QnA-resource die u hebt geselecteerd tijdens het maken van de resource.

    Ga terug naar de QnA Maker-portal, vernieuw de browserpagina en ga door naar **Stap 2** als u klaar bent met het maken van de resource in Azure Portal.

4. Selecteer in **Stap 2** uw Active Directory, abonnement en service (resource), en de taal voor alle Knowledge Bases die in de service worden gemaakt.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="Schermopname van het selecteren van een beheerde preview van een Knowledge Base in de QnA Maker-service":::

5. Als u in **Stap 2** de eerste Knowledge Base voor uw service maakt, hebt u de mogelijkheid om een specifieke taalinstelling op te geven voor elke Knowledge Base. As de taalinstelling voor de eerste Knowledge Base eenmaal is gedefinieerd, kunt u de instellingen voor de service later niet meer wijzigen.

6. Geef uw Knowledge Base in  **Stap 3** de naam  **Mijn voorbeeld-QnA-KB**. 

7. Configureer in **Stap 4** de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Schakel uitpakken van meerdere paden in vanuit URL's, .pdf- of .docx-bestanden.**|Ingeschakeld|
    |**Standaardtekst met meerdere paden**| Selecteer een optie|
    |**+ Bestand toevoegen**| Download de handleiding voor de Surface-laptop op: https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf 
    |**Chit-chat**|**Professional** selecteren|

8. Selecteer in **stap 5**, **Uw KB maken**.

    Tijdens het extractieproces, dat enige tijd duurt, wordt het document gelezen en worden de vragen en antwoorden geïdentificeerd.

    Nadat QnA Maker de knowledge base heeft gemaakt, wordt de pagina **Knowledge Base** geopend. U kunt de inhoud van de knowledge base op deze pagina bewerken.

---

## <a name="add-a-new-question-and-answer-set"></a>Een nieuwe vraag-en-antwoordreeks toevoegen

1. Selecteer in de QnA Maker-portal op de pagina **Bewerken** **+ Vraag-en-antwoordpaar toevoegen** in de contextwerkbalk.
1. Voeg de volgende vraag toe:

    `How many Azure services are used by a knowledge base?`

1. Voeg het antwoord toe in _markdown_-indeling:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Voeg de vraag toe als tekst en het antwoord met markdown-indeling.":::

    Het markdownsymbool, `*`, wordt gebruikt voor opsommingstekens. De `\n` wordt gebruikt voor een nieuwe regel.

    Op de pagina **Bewerken** wordt de markdown weer gegeven. Wanneer u later het deelvenster **Testen** gebruikt, ziet u dat de markdown goed wordt weergegeven.

## <a name="save-and-train"></a>Opslaan en trainen

Selecteer in de rechterbovenhoek **Save and train** (Opslaan en trainen) om de wijzigingen op te slaan en QnA Maker te trainen. Bewerkingen worden alleen bewaard als ze worden opgeslagen.

## <a name="test-the-knowledge-base"></a>De knowledge base testen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

1. Selecteer in de rechterbovenhoek van de QnA Maker-portal **Testen** om te testen of de wijzigingen worden toegepast.
2. Voer in het tekstvak een voorbeeld van een gebruikersquery in.

    `I want to know the difference between 32 bit and 64 bit Windows`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/query-dialogue.png" alt-text="Voer in het tekstvak een voorbeeld van een gebruikersquery in.":::

3. Selecteer **Inspect** (Inspecteren) om het antwoord gedetailleerder te onderzoeken. Het testvenster wordt gebruikt om uw wijzigingen in de knowledge base te testen voordat u uw knowledge base publiceert.

4. Selecteer nogmaals **Testen** om het deelvenster **Testen** te sluiten.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

1. Selecteer in de rechterbovenhoek van de QnA Maker-portal **Testen** om te testen of de wijzigingen worden toegepast.
2. Voer in het tekstvak een voorbeeld van een gebruikersquery in.

    `whats the size of the touchscreen`

3. Als u de MRC-functie voor uw Knowledge Base inschakelt door **Display short answer** (Kort antwoord weergeven) te selecteren, ziet u naast de antwoordzin in het testvenster ook een nauwkeurig antwoord, indien beschikbaar. 

    ![Beheerd ingeschakeld testvenster](../media/conversational-context/test-pane-with-managed.png)
    

4. Selecteer Inspect (Inspecteren) om het antwoord gedetailleerder te onderzoeken. Het testvenster wordt gebruikt om uw wijzigingen in de knowledge base te testen voordat u uw knowledge base publiceert. 
5. Selecteer nogmaals **Testen** om het deelvenster **Testen** te sluiten.
---

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren

Wanneer u een knowledge base publiceert, wordt de inhoud van uw knowledge base verplaatst van de `test`-index naar een `prod`-index in Azure Search.

![Schermafbeelding van het verplaatsen van de inhoud van uw knowledge base](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Selecteer in de QnA Maker-portal **Publiceren**. Selecteer vervolgens **Publish** (Publiceren) op de pagina om te bevestigen.

    De QnA Maker-service is nu gepubliceerd. U kunt het eindpunt in uw toepassing of botcode gebruiken.

    ![Schermopname van geslaagde publicatie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Een bot maken

Na het publiceren kunt u een bot maken op de pagina **Publiceren**:

* U kunt snel een aantal bots maken, die allemaal verwijzen naar dezelfde knowledge base voor verschillende regio's of abonnementen voor de individuele bots.
* Als u slechts één bot wilt gebruiken voor de knowledge base, gebruikt u de koppeling **Alle bots weergeven in Azure Portal** om een lijst met uw huidige bots weer te geven.

Wanneer u wijzigingen aanbrengt in de knowledge base en deze opnieuw publiceert, hoeft u geen verdere actie te ondernemen voor de bot. Deze is al geconfigureerd voor gebruik van de knowledge base en kan overweg met alle toekomstige wijzigingen in de knowledge base. Steeds wanneer u een knowledge base publiceert, worden alle bots die met de knowledge base zijn verbonden, automatisch bijgewerkt.

1. Selecteer in de QnA Maker-portal op de pagina **Publiceren** de optie **Bot maken**. Deze knop wordt alleen weergegeven nadat u de knowledge base hebt gepubliceerd.

    ![Schermopname van het maken van een bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Er wordt een nieuw browsertabblad geopend voor Azure Portal, met daarin de pagina Maken van de Azure Bot Service. De Azure Bot Service configureren. De bot en QnA Maker kunnen het abonnement voor de web-appservice delen, maar ze kunnen de web-app niet delen. Dit betekent dat de **naam van de app** voor de bot anders moet zijn dan de naam van de app voor de QnA Maker-service.

    * **Wel doen**
        * Wijzig de gebruikersnaam van de bot als deze niet uniek is.
        * Selecteer de SDK-taal. Nadat de bot is gemaakt, kunt u de code downloaden naar uw lokale ontwikkelomgeving en het ontwikkelingsproces voortzetten.
    * **Niet doen**
        * Wijzig de volgende instellingen niet in Azure Portal als u de bot maakt. Deze zijn vooraf ingevuld voor uw bestaande knowledge base:
           * QnA-verificatiesleutel
           * App Service-plan en -locatie


1. Open de resource **Botservice** nadat de bot is gemaakt.
1. Selecteer onder **Botbeheer** **Testen in webchat**.
1. Voer in het chatbericht **Typ uw bericht** het volgende in:

    `Azure services?`

    De chatbot reageert met een antwoord uit uw knowledge base.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Voer een gebruikersquery in om de webchat testen.":::

## <a name="what-did-you-accomplish"></a>Wat hebt u gedaan?

U hebt een nieuwe knowledge base gemaakt, een openbare URL toegevoegd aan de knowledge base, uw eigen QnA-paar toegevoegd, getraind en getest, en de knowledge base gepubliceerd.

Nadat u de knowledge base hebt gepubliceerd, hebt u een bot gemaakt en de bot getest.

Dit is in een paar minuten gedaan zonder dat u code hoeft te schrijven of de inhoud moest opschonen.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de QnA Maker- en Bot-framework-resources in Azure Portal als u niet verdergaat met de volgende quickstart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vragen toevoegen met metagegevens](add-question-metadata-portal.md)

Voor meer informatie:

* [Markdown-indeling in antwoorden](../reference-markdown-format.md)
* QnA Maker-[gegevensbronnen](../Concepts/data-sources-and-content.md).
