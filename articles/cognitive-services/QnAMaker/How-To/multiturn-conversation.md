---
title: Gesprekken met meerdere schakelingen-QnA Maker
description: Gebruik prompts en context om de meerdere beurten, ook wel multi-turn genoemd, te beheren voor uw bot van de ene vraag naar de andere. Multi-turn is de mogelijkheid om een back-en-neer-conversatie te hebben waarbij de context van de vorige vraag de volgende vraag en het antwoord beïnvloedt.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 26fc976983fc08857e7771d58f15d0abcd9a1d3c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353218"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Opvolgprompts gebruiken om meerdere beurten in een gesprek te maken

Gebruik opvolgings prompts en context voor het beheren van de meerdere beurten, ook wel bekend als _multi-turn_, voor uw bot van de ene vraag naar de andere.

Bekijk de volgende demonstratie video om te zien hoe u werkt met meerdere zetten:

[![Gesprek op meerdere beurten in QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Wat is een gesprek op meerdere locaties?

Sommige vragen kunnen niet in één keer worden beantwoord. Wanneer u uw client toepassing (chat-bot) ontwerpt, kan een gebruiker een vraag stellen die moet worden gefilterd of verfijnd om het juiste antwoord te bepalen. U doet dit door de beschik bare vragen te presen teren door de gebruiker *te laten opvolgen*.

Wanneer een gebruiker een vraag stelt, retourneert QnA Maker het antwoord _en_ eventuele vervolg vragen. Met deze reactie kunt u de opvolgings vragen presen teren als keuzes.

> [!CAUTION]
> Vragen over meerdere schakelingen worden niet geëxtraheerd uit documenten met veelgestelde vragen. Als u meerdere extra-Schakel extractie nodig hebt, verwijdert u de vraag tekens die de QnA-paren als Veelgestelde vragen aanwijzen.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Voor beeld van een multi-turn-gesprek met een chat sessie

Met multi-bocht beheert een chat-bot een conversatie met een gebruiker om het laatste antwoord te bepalen, zoals wordt weer gegeven in de volgende afbeelding:

![Een dialoog venster waarin u wordt gevraagd een gebruiker via een gesprek te begeleiden](../media/conversational-context/conversation-in-bot.png)

Een gebruiker heeft in de voor gaande afbeelding een gesprek gestart door **Mijn account** in te voeren. De Knowledge Base heeft drie gekoppelde vraag-en antwoord paren. Om het antwoord te verfijnen, selecteert de gebruiker een van de drie opties in de Knowledge Base. De vraag (#1) heeft drie opvolgings prompts die in de chat-bot worden weer gegeven als drie opties (#2).

Wanneer de gebruiker een optie (#3) selecteert, wordt de volgende lijst met verfijnings opties (#4) weer gegeven. Deze reeks blijft (#5) totdat de gebruiker het juiste, laatste antwoord (#6) bepaalt.


### <a name="use-multi-turn-in-a-bot"></a>Meerdere zetten gebruiken in een bot

Nadat u uw KB hebt gepubliceerd, kunt u de knop **bot maken** selecteren om uw QnA Maker-bot te implementeren op de Azure bot-service. De prompts worden weer gegeven in de chat-clients die u hebt ingeschakeld voor uw bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Een multi-turn-gesprek maken op basis van de structuur van een document

Wanneer u een Knowledge Base maakt, wordt in het gedeelte **uw KB invullen** het selectie vakje **meerdere Schakel extractie van url's, PDF-of DOCX-bestanden inschakelen** .

![Selectie vakje voor het inschakelen van meerdere Schakel extractie](../media/conversational-context/enable-multi-turn.png)

Wanneer u deze optie selecteert, haalt QnA Maker de hiërarchie op die in de document structuur aanwezig is. De hiërarchie wordt omgezet in om prompts op te volgen en de basis van de hiërarchie fungeert als de bovenliggende QnA. In sommige documenten heeft de hoofdmap van de hiërarchie geen inhoud die als antwoord zou kunnen gebruiken. u kunt de standaard antwoord tekst opgeven als vervangende antwoord tekst om dergelijke hiërarchieën uit te pakken.

Structuur voor meerdere scha kelen kan alleen worden afgeleid van Url's, PDF-bestanden of DOCX-bestanden. Voor een voor beeld van een structuur bekijkt u een afbeelding van een [hand matig PDF-bestand van micro soft Surface-gebruiker](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="Scherm afbeelding toont een voor beeld van de structuur in een gebruikers handleiding." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Uw eigen document op meerdere zetten bouwen

Als u een document met meerdere scha kelen maakt, houdt u de volgende richt lijnen in acht:

* Gebruik koppen en subkoppen om de hiërarchie aan te duiden. U kunt bijvoorbeeld H1 de bovenliggende QnA en H2 aanduiden om de QnA aan te duiden die als prompt moet worden beschouwd. Gebruik de kleine grootte van de kop om volgende hiërarchie aan te duiden. Gebruik geen stijl, kleur of een ander mechanisme om de structuur in uw document te impliceren, QnA Maker de prompts voor meerdere schakelingen niet uit te pakken.

* Het eerste teken van de kop moet worden gekapitaliseerd.

* Beëindig geen kop met een vraag teken, `?` .

* U kunt het [voorbeeld document](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) gebruiken als voor beeld voor het maken van uw eigen multi-turn-document.

### <a name="adding-files-to-a-multi-turn-kb"></a>Bestanden toevoegen aan een meervoudige Schakel KB

Wanneer u een hiërarchisch document toevoegt, bepaalt QnA Maker opvolgings aanwijzingen van de structuur om een conversatie stroom te maken.

1. In QnA Maker selecteert u een bestaande Knowledge Base die is gemaakt met het **inschakelen van meerdere Schakel extractie vanuit url's, PDF-of DOCX-bestanden.** ingeschakeld.
1. Ga naar de pagina **instellingen** , selecteer het bestand of de URL die u wilt toevoegen.
1. Sla de kennis database op **en Train** deze.

> [!Caution]
> Ondersteuning voor het gebruik van een geëxporteerd TSV-of XLS-Knowledge Base-bestand als gegevens bron voor een nieuwe of lege kennis database wordt niet ondersteund. U moet dit bestands type **importeren** op de pagina **instellingen** van de QnA Maker-Portal om de prompts voor het exporteren van meerdere zetten toe te voegen aan een kennis database.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Maak een kennis database met de prompts in meerdere richtingen met de API maken

U kunt met behulp van de [QnA Maker Create API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)een kennis Case maken met vragen over meerdere schakelingen. De prompts worden toegevoegd in de `context` matrix van de eigenschap `prompts` .

## <a name="show-questions-and-answers-with-context"></a>Vragen en antwoorden met context weer geven

De weer gegeven vraag-en antwoord paren beperken tot alleen die context afhankelijke gesp rekken.

Selecteer **weergave opties** en selecteer vervolgens **context weer geven**. In de lijst worden de vraag-en-antwoord-paren weer gegeven die opvolgings prompts bevatten.

![Vraag-en-antwoord paren filteren op contextuele conversaties](../media/conversational-context/filter-question-and-answers-by-context.png)

De context voor meerdere scha kelen wordt weer gegeven in de eerste kolom.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="Scherm afbeelding toont de context sectie gemarkeerd." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

In de voor gaande afbeelding geeft **#1** vetgedrukte tekst in de kolom op die de huidige vraag aangeeft. De bovenliggende vraag is het bovenste item in de rij. Eventuele vragen hieronder zijn de gekoppelde vraag-en-antwoord paren. Deze items kunnen worden geselecteerd, zodat u direct naar de andere context items kunt gaan.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Een bestaand vraag-en antwoord paar toevoegen als een opvolgings prompt

De oorspronkelijke vraag, **Mijn account**, heeft vervolg vragen, zoals **accounts en aanmelding**.

![De antwoorden ' accounts and signing ' en vervolg vragen](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Voeg een opvolgings prompt toe aan een bestaand vraag-en antwoord paar dat momenteel niet is gekoppeld. Omdat de vraag niet is gekoppeld aan een vraag-en-antwoord paar, moet de huidige weer gave-instelling worden gewijzigd.

1. Als u een bestaand vraag-en antwoord paar wilt koppelen als een opvolgings prompt, selecteert u de rij voor het vraag-en-antwoord paar. Zoek voor de hand leiding voor het Opper vlak naar **Afmelden** om de lijst te verminderen.
1. In de rij voor **Afmelden** in de kolom **antwoord** selecteert u **opvolgings prompt toevoegen**.
1. Voer de volgende waarden in in de velden in het pop-upvenster **opvolgings prompt** :

    |Veld|Waarde|
    |--|--|
    |Weergavetekst|**Schakel het apparaat uit**. Dit is aangepaste tekst die in de opvolgings prompt wordt weer gegeven.|
    |Alleen context| Schakel dit selectievakje in. Er wordt alleen een antwoord geretourneerd als de vraag context aangeeft.|
    |Aan antwoord koppelen|Voer **het aanmeldings scherm gebruiken** in om het bestaande vraag-en antwoord paar te vinden.|


1.  Er wordt één overeenkomst geretourneerd. Selecteer dit antwoord als opvolging en selecteer vervolgens **Opslaan**.

    ![De pagina opvolgings prompt (PREVIEW-versie)](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Nadat u de opvolgings prompt hebt toegevoegd, selecteert u **opslaan en trainen** in de bovenste navigatie balk.

### <a name="edit-the-display-text"></a>De weergave tekst bewerken

Wanneer er een opvolgings prompt wordt gemaakt en een bestaand vraag-en-antwoord paar wordt ingevoerd als de **koppeling die moet worden beantwoord**, kunt u nieuwe **weergave tekst** invoeren. Met deze tekst wordt de bestaande vraag niet vervangen en er wordt geen nieuwe alternatieve vraag toegevoegd. Deze is gescheiden van die waarden.

1. Als u de weergave tekst wilt bewerken, zoekt en selecteert u de vraag in het veld **context** .
1. Selecteer in de rij voor die vraag de opvolgings prompt in de kolom antwoord.
1. Selecteer de weergave tekst die u wilt bewerken en selecteer vervolgens **bewerken**.

    ![De opdracht bewerken voor de weergave tekst](../media/conversational-context/edit-existing-display-text.png)

1. Wijzig de bestaande weergave tekst in het pop-upvenster **opvolgings prompt** .
1. Wanneer u klaar bent met het bewerken van de weergave tekst, selecteert u **Opslaan**.
1. In de bovenste navigatie balk kunt u **opslaan en trainen**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Een nieuw vraag-en-antwoord paar toevoegen als vervolg prompt

Wanneer u een nieuw vraag-en-antwoord paar toevoegt aan de Knowledge Base, moet elk paar worden gekoppeld aan een bestaande vraag als vervolg prompt.

1. Zoek en selecteer op de werk balk van de Knowledge Base het bestaande vraag-en antwoord paar voor **accounts en meld** u aan.

1. Selecteer in de kolom **antwoord** op deze vraag **opvolgen prompt toevoegen**.
1. Bij **opvolgings prompt (preview)** maakt u een nieuwe opvolgings prompt door de volgende waarden in te voeren:

    |Veld|Waarde|
    |--|--|
    |Weergavetekst|*Maak een Windows-account*. De aangepaste tekst die moet worden weer gegeven in de opvolgings prompt.|
    |Alleen context|Schakel dit selectievakje in. Dit antwoord wordt alleen geretourneerd als de vraag context aangeeft.|
    |Aan antwoord koppelen|Voer de volgende tekst in als antwoord:<br>*[Maak](https://account.microsoft.com/) een Windows-account met een nieuw of bestaand e-mail account*.<br>Wanneer u de data base opslaat en traint, wordt deze tekst geconverteerd. |
    |||

    ![Een nieuwe vraag en antwoord op vragen stellen](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecteer **nieuwe maken** en selecteer vervolgens **Opslaan**.

    Met deze actie maakt u een nieuw vraag-en antwoord paar en koppelt u de geselecteerde vraag aan de hand van een opvolgings prompt. De **context** kolom, voor beide vragen, geeft aan dat er een follow-up-prompt relatie is.

1. Selecteer **weergave opties** en selecteer vervolgens [**context weer geven (preview)**](#show-questions-and-answers-with-context).

    In de nieuwe vraag ziet u hoe deze is gekoppeld.

    ![Een nieuwe opvolgings prompt maken](../media/conversational-context/new-qna-follow-up-prompt.png)

    De bovenliggende vraag geeft een nieuwe vraag weer als een van de opties.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="Scherm afbeelding toont de context kolom, voor beide vragen, geeft aan dat er een follow-up-prompt relatie is." lightbox="../media/conversational-context/child-prompt-created.png":::

1. Nadat u de opvolgings prompt hebt toegevoegd, selecteert u **opslaan en trainen** in de bovenste navigatie balk.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Meerdere beurten weer geven tijdens het testen van opvolgings prompts

Wanneer u de vraag test met opvolgings aanwijzingen in het **test** venster, bevat het antwoord de opvolgings prompts.

![Het antwoord bevat de vervolg vragen](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Een JSON-aanvraag om een eerste antwoord en opvolgings prompts te retour neren

Gebruik het lege `context` object om het antwoord op de vraag van de gebruiker aan te vragen en een opvolgings prompt op te geven.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Een JSON-antwoord om een eerste antwoord en opvolgings prompts te retour neren

In de voor gaande sectie is een antwoord aangevraagd en wordt u gevraagd om **accounts in** te voeren en zich aan te melden. Het antwoord bevat de prompt informatie, die zich bevindt in *antwoorden [0]. context*, en de tekst die moet worden weer gegeven voor de gebruiker.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

De `prompts` matrix bevat tekst in de `displayText` eigenschap en de `qnaId` waarde. U kunt deze antwoorden weer geven als de volgende weer gegeven keuzes in de conversatie stroom en vervolgens de geselecteerde `qnaId` terug naar QnA Maker verzenden in de volgende aanvraag.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Een JSON-aanvraag om een niet-eerste antwoord en opvolgings prompts te retour neren

Vul het `context` object in om de vorige context op te laten bevatten.

In de volgende JSON-aanvraag gebruikt de huidige vraag *Windows hello om u aan te melden* . de vorige vraag is *accounts en* u aan te melden.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Een JSON-antwoord om een niet-eerste antwoord en opvolgings prompts te retour neren

Het _GenerateAnswer_ JSON-antwoord QnA Maker bevat de vervolg prompts in de `context` eigenschap van het eerste item in het `answers` object:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>De Knowledge Base doorzoeken met de QnA Maker-ID

Als u een aangepaste toepassing bouwt met behulp van de functie voor meerdere zetten. In het antwoord van de eerste vraag worden eventuele vervolg vragen en de bijbehorende koppeling `qnaId` geretourneerd. Nu u de ID hebt, kunt u deze door geven in de aanvraag tekst van de opvolgings prompt. Als de aanvraag tekst de bevat `qnaId` en het context object (dat de vorige QnA Maker eigenschappen bevat), retourneert GenerateAnswer de exacte vraag op id, in plaats van het classificatie algoritme te gebruiken om het antwoord te vinden op basis van de vraag tekst.


## <a name="display-order-is-supported-in-the-update-api"></a>Weergave volgorde wordt ondersteund in de API update

De [weergave tekst en weergave volgorde](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), geretourneerd in het JSON-antwoord, worden ondersteund voor bewerking door de [Update-API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Vragen om meerdere zetten toe te voegen of te verwijderen met de API voor bijwerken

U kunt vragen over meerdere schakelingen toevoegen of verwijderen met de [API QnA Maker update](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  De prompts worden toegevoegd in de `context` matrix van de eigenschap `promptsToAdd` en de `promptsToDelete` matrix.

## <a name="export-knowledge-base-for-version-control"></a>Knowledge Base exporteren voor versie beheer

QnA Maker ondersteunt versie beheer door gespreks stappen voor meerdere locaties in het geëxporteerde bestand op te nemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over contextuele gesp rekken in dit [dialoog venster](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) vindt u in het voor beeld van een [conceptueel bot-ontwerp voor multi-telegesprekken](/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Een knowledge base migreren](../Tutorials/migrate-knowledge-base.md)