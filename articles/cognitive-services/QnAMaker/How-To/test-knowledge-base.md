---
title: Een Knowledge Base testen-QnA Maker
description: Het testen van uw QnA Maker Knowledge Base is een belang rijk onderdeel van een iteratief proces om de nauw keurigheid van de reacties die worden geretourneerd, te verbeteren. U kunt de Knowledge Base testen via een verbeterde chat-interface, waarmee u ook bewerkingen kunt uitvoeren.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 273548ec095ce04772438a2d732b914d80d976cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353150"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Test uw Knowledge Base in QnA Maker

Het testen van uw QnA Maker Knowledge Base is een belang rijk onderdeel van een iteratief proces om de nauw keurigheid van de reacties die worden geretourneerd, te verbeteren. U kunt de Knowledge Base testen via een verbeterde chat-interface, waarmee u ook bewerkingen kunt uitvoeren.

## <a name="interactively-test-in-qna-maker-portal"></a>Interactief testen in QnA Maker Portal

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

1. Open uw kennis database door de naam ervan te selecteren op de pagina **mijn Knowledge bases** .
1. Als u toegang wilt krijgen tot het toetsen paneel testen, selecteert u **testen** in het bovenste paneel van de toepassing.
1. Voer een query in het tekstvak in en selecteer ENTER.
1. Het best overeenkomende antwoord van de Knowledge Base wordt geretourneerd als antwoord.

### <a name="clear-test-panel"></a>Test paneel wissen

Als u alle ingevoerde test query's en de bijbehorende resultaten wilt wissen uit de test console, selecteert u **opnieuw beginnen** in de linkerbovenhoek van het test paneel.

### <a name="close-test-panel"></a>Test paneel sluiten

Als u het test paneel wilt sluiten, selecteert u de knop **testen** opnieuw. Terwijl het test paneel is geopend, kunt u de inhoud van de Knowledge Base niet bewerken.

### <a name="inspect-score"></a>Score controleren

U kunt de details van het test resultaat bekijken in het deel venster inspecteren.

1.  Selecteer in het deel venster voor het testen van de schuif regelaar op **controleren** voor meer informatie over die reactie.

    ![Reacties controleren](../media/qnamaker-how-to-test-knowledge-bases/inspect.png)

2.  Het deel venster inspectie wordt weer gegeven. Het paneel bevat de belangrijkste Score intentie en alle geïdentificeerde entiteiten. In het deel venster wordt het resultaat van de geselecteerde utterance weer gegeven.

### <a name="correct-the-top-scoring-answer"></a>Het bovenste Score antwoord corrigeren

Als het bovenste Score antwoord onjuist is, selecteert u het juiste antwoord in de lijst en selecteert u **opslaan en trainen**.

![Het bovenste Score antwoord corrigeren](../media/qnamaker-how-to-test-knowledge-bases/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

U kunt alternatieve formulieren van een vraag toevoegen aan een bepaald antwoord. Typ de alternatieve antwoorden in het tekstvak en selecteer ENTER om ze toe te voegen. Selecteer **opslaan en trainen** om de updates op te slaan.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Een nieuw antwoord toevoegen

U kunt een nieuw antwoord toevoegen als een van de bestaande antwoorden die overeenkomen onjuist is of als het antwoord niet aanwezig is in de Knowledge Base (er is geen goede overeenkomst gevonden in de KB).

Klik aan de onderkant van de lijst met antwoorden op het tekstvak om een nieuw antwoord in te voeren en druk op ENTER om het te voegen.

Selecteer **opslaan en trainen** om dit antwoord te behouden. Er is nu een nieuwe vraag-antwoord paar toegevoegd aan uw Knowledge Base.

> [!NOTE]
> Alle wijzigingen in uw kennis database worden alleen opgeslagen wanneer u op de knop **opslaan en trainen** drukt.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

1. Open uw kennis database door de naam ervan te selecteren op de pagina **mijn Knowledge bases** .
1. Als u toegang wilt krijgen tot het toetsen paneel testen, selecteert u **testen** in het bovenste paneel van de toepassing. 
1. Er wordt een selectie vakje weer gegeven op het bovenste **antwoord**, dat standaard is geselecteerd. Deze optie wordt gebruikt voor het inschakelen van op MRC gebaseerde antwoord bereik detectie in het test paneel. 
1. Voer een query in het tekstvak in en selecteer ENTER. 
1. Voor elke query geldt dat als er sprake is van een nauw keurig antwoord/kort antwoord dat in het antwoord wordt weer gegeven, samen met het best overeenkomende antwoord dat in de Knowledge Base aanwezig is, u ook een kort antwoord hebt voor uw query.
    ![Beheerd ingeschakeld testvenster](../media/qnamaker-how-to-test-knowledge-bases/test-pane-with-managed-detail.png)
1. Als u het selectie vakje **kort antwoord weer geven** uitschakelt, wordt alleen het beste antwoord in de Knowledge Base geretourneerd als antwoord.

### <a name="clear-test-panel"></a>Test paneel wissen

Als u alle ingevoerde test query's en de bijbehorende resultaten wilt wissen uit de test console, selecteert u **opnieuw beginnen** in de linkerbovenhoek van het test paneel.

### <a name="close-test-panel"></a>Test paneel sluiten

Als u het test paneel wilt sluiten, selecteert u de knop **testen** opnieuw. Terwijl het test paneel is geopend, kunt u de inhoud van de Knowledge Base niet bewerken.

### <a name="inspect-score"></a>Score controleren

U kunt de details van het test resultaat bekijken in het deel venster inspecteren.

1.  Selecteer in het deel venster voor het testen van de schuif regelaar op **controleren** voor meer informatie over die reactie.

    ![Voor beeld van antwoorden controleren](../media/qnamaker-how-to-test-knowledge-bases/inspect-with-managed.png)

2.  Het deel venster inspectie wordt weer gegeven. Het paneel bevat de belangrijkste Score intentie en alle geïdentificeerde entiteiten. In het deel venster wordt het resultaat van de geselecteerde utterance weer gegeven.
3. In het deel venster wordt de betrouwbaarheids Score van de antwoord-passeren weer gegeven, samen met een Score van de gedetecteerde antwoord periode.

### <a name="correct-the-top-scoring-answer"></a>Het bovenste Score antwoord corrigeren

Als het bovenste Score antwoord onjuist is, selecteert u het juiste antwoord in de lijst en selecteert u **opslaan en trainen**.

![De voorbeeld reactie voor het bovenste Score blad corrigeren](../media/qnamaker-how-to-test-knowledge-bases/choose-answer-managed.png)

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

U kunt alternatieve formulieren van een vraag toevoegen aan een bepaald antwoord. Typ de alternatieve antwoorden in het tekstvak en selecteer ENTER om ze toe te voegen. Selecteer **opslaan en trainen** om de updates op te slaan.

![Preview-versie van alternatieve vragen toevoegen](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question-with-managed.png)

### <a name="add-a-new-answer"></a>Een nieuw antwoord toevoegen

U kunt een nieuw antwoord toevoegen als een van de bestaande antwoorden die overeenkomen onjuist is of als het antwoord niet aanwezig is in de Knowledge Base (er is geen goede overeenkomst gevonden in de KB).

Klik aan de onderkant van de lijst met antwoorden op het tekstvak om een nieuw antwoord in te voeren en druk op ENTER om het te voegen.

Selecteer **opslaan en trainen** om dit antwoord te behouden. Er is nu een nieuwe vraag-antwoord paar toegevoegd aan uw Knowledge Base.

---

### <a name="test-the-published-knowledge-base"></a>De gepubliceerde kennis database testen

U kunt de gepubliceerde versie van de Knowledge Base testen in het test venster. Zodra u de KB hebt gepubliceerd, selecteert u het vak **gepubliceerde KB** en verzendt u een query om de resultaten van de gepubliceerde KB op te halen.

![Testen op basis van een gepubliceerde KB](../media/qnamaker-how-to-test-knowledge-bases/test-against-published-knowledge-base.png)

## <a name="batch-test-with-tool"></a>Batch test met hulp programma

Gebruik het hulp programma voor batch tests wanneer u het volgende wilt doen:

* beste antwoord en score voor een set vragen bepalen
* het verwachte antwoord voor een set met vragen valideren

### <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/cognitive-services/)
* [Maak een QnA Maker-service](../Quickstarts/create-publish-knowledge-base.md) of gebruik een bestaande service die gebruikmaakt van de Engelse taal.
* Het `.docx`voorbeeldbestand [meerdere paden downloaden ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Download het [batchtestprogramma](https://aka.ms/qnamakerbatchtestingtool), pak het uitvoerbaar bestand uit vanuit het `.zip`-bestand.

### <a name="sign-into-qna-maker-portal"></a>Aanmelden bij QnA Maker-portal

[Meld u aan](https://www.qnamaker.ai/) bij de QnA Maker-portal.

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Een Knowledge Base maken op basis van het .docx-voorbeeldbestand meerdere paden

1. Selecteer **Een Knowledge Base maken** in de menubalk.
1. Sla **Stap 1** over, want u moet al een QnA Maker-resource hebben, ga naar **Stap 2** om uw bestaande resourcegegevens te selecteren:
    * Azure Active Directory-ID
    * Azure-abonnementsnaam
    * Azure QnA Service-naam
    * Taal, de Engelse taal
1. Voer de naam `Multi-turn batch test quickstart` in als de naam van uw Knowledge Base.

1. Configureer in **Stap 4** de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Schakel uitpakken van meerdere paden in vanuit URL's, .pdf- of .docx-bestanden.**|Ingeschakeld|
    |**Standaardantwoordtekst**| `Batch test - default answer not found.`|
    |**+ Bestand toevoegen**|Selecteer de gedownloade `.docx`-bestandslijst in de vereisten.|
    |**Chit-chat**|**Professional** selecteren|

1. Selecteer in **stap 5**, **Uw KB maken**.

    Wanneer het proces is voltooid, geeft de portal de bewerkbare Knowledge Base weer.

### <a name="save-train-and-publish-knowledge-base"></a>De knowledge base opslaan, trainen en publiceren

1. Selecteer **Opslaan en trainen** in de werkbalk om de Knowledge Base op te slaan.
1. Selecteer **Publiceren** in de werkbalk en selecteer dan nogmaals **Publiceren** om de Knowledge Base te publiceren. Door de Knowledge Base te publiceren is deze beschikbaar voor query's vanuit een openbaar URL-eindpunt. Wanneer het publiceren is voltooid, slaat u de host-URL en eindpuntsleutelgegevens op die worden weergegeven op de pagina **Publiceren**.

    |Vereiste gegevens| Voorbeeld|
    |--|--|
    |Gepubliceerde host|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Gepubliceerde sleutel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (tekenreeks van 32 tekens weergegeven na `Endpoint`)|
    |App-id|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (tekenreeks van 36 tekens weergegeven als onderdeel van `POST`) |

### <a name="create-batch-test-file-with-question-ids"></a>Batchtestbestand maken met vraag-ID's

Maak een bestand met de naam `batch-test-data-1.tsv` met een teksteditor om het batchtestprogramma te gebruiken. Het bestand moet een UTF-8-indeling hebben en de volgende kolommen, gescheiden door een tab.

|TSV-invoerbestandsvelden|Opmerkingen|Voorbeeld|
|--|--|--|
|Knowledge Base-ID|Uw Knowledge Base-ID dat u kunt vinden op de pagina Publiceren. Test tegelijkertijd verschillende Knowledge Bases in dezelfde service in een enkel bestand door verschillende Knowledge Base-ID's te gebruiken in een enkel bestand.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (tekenreeks van 36 tekens weergegeven als onderdeel van `POST`) |
|Vraag|De vraag die een gebruiker zou invoeren. Maximaal 1000 tekens.|`How do I sign out?`|
|Tags met metagegevens|optioneel|`topic:power` maakt gebruik van de `key:value` indeling|
|Top-parameter|optioneel|`25`|
|ID van verwacht antwoord|optioneel|`13`|

Voor deze Knowledge Base voegt u drie rijen van alleen de twee vereiste kolommen toe aan het bestand. De eerste kolom is het Knowledge Base-ID en de tweede kolom moet de volgende lijst vragen zijn:

|Kolom 2, vragen|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Deze vragen zijn de exacte tekst uit de Knowledge Base en moeten als waarde 100 retourneren voor waarschijnlijkheid.

Voeg vervolgens enkele vragen toe die vergelijkbaar zijn met deze vragen, maar niet precies hetzelfde op drie rijen met dezelfde Knowledge Base-ID:

|Kolom 2, vragen|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Zorg ervoor dat elke kolom wordt gescheiden door alleen een tab-scheidingsteken. Voorloop- of volgspaties worden aan de kolomgegevens toegevoegd en zorgen voor uitzonderingen in het programma als het type of de grootte onjuist is.

Als het batchtestbestand in Excel wordt geopend, ziet het eruit als de volgende afbeelding. De Knowledge Base-ID is vervangen door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` voor de beveiliging. Zorg voor uw eigen batchtest dat de kolom uw Knowledge Base-ID weergeeft.

> [!div class="mx-imgBorder"]
> ![Invoer eerste versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>Batchbestand testen

Voer op de opdrachtregel het batchtestprogramma uit met de volgende CLI-indeling.

Vervang `YOUR-RESOURCE-NAME` en `ENDPOINT-KEY` met uw eigen waarden voor servicenaam en eindpuntsleutel. Deze waarden vindt u op de pagina **Instellingen** in de QnA Maker-portal.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
De test wordt voltooid en genereert het `out.tsv`-bestand:

> [!div class="mx-imgBorder"]
> ![Uitvoer eerste versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-1-output.png)

De Knowledge Base-ID is vervangen door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` voor de beveiliging. De kolom van uw eigen batchtest geeft uw Knowledge Base-ID weer.

De test uitvoer van een betrouwbaarheids Score, in de vierde kolom, toont de eerste drie vragen die een Score van 100 zoals verwacht retour neren, omdat elke vraag precies hetzelfde is als in de Knowledge Base. De laatste drie vragen, met een nieuwe vorm van de vraag, retour neren 100 niet als de betrouwbaarheids Score. Om de score voor de test en uw gebruikers te verhogen, moet u meer alternatieve vragen toevoegen aan de Knowledge Base.

### <a name="testing-with-the-optional-fields"></a>Testen met optionele velden

Wanneer u de indeling en het proces begrijpt, kunt u een testbestand genereren om met uw Knowledge Base uit te voeren op basis van een gegevensbron zoals chatlogboeken.

Omdat de gegevensbron en het proces zijn geautomatiseerd, kan het testbestand vaak worden uitgevoerd met verschillende instellingen om de juiste waarden te bepalen.

Als u bijvoorbeeld een chatlogboek hebt en u wilt vaststellen welk chattekst van toepassing is op welke metagegevensvelden, maakt u een testbestand en stelt u voor elke regel de metagegevensvelden in. Voer de test uit en beoordeel de rijen die overeenkomen met de metagegevens. Over het algemeen moeten de overeenkomsten positief zijn, maar u moet de resultaten beoordelen op fout-positieve resultaten. Een fout-positief is een rij die overeenkomt met de metagegevens, maar op basis van de tekst niet overeen zou moeten komen.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Optionele velden gebruiken in het invoerbestand voor batchtesten

Gebruik het volgende diagram om de veldwaarden te vinden voor de optionele gegevens.

|Kolomnummer|Optionele kolom|Locatie van gegevens|
|--|--|--|
|3|metagegevens|Bestaande Knowledge Base exporteren voor bestaande `key:value` paren.|
|4|top|De standaardwaarde `25` wordt aanbevolen.|
|5|Vraag-en-antwoord-set-ID|Exporteer bestaande Knowledge Base voor ID-waarden. U ziet ook dat de ID's in het uitvoerbestand zijn geretourneerd.|

### <a name="add-metadata-to-the-knowledge-base"></a>Metagegevens toevoegen aan de Knowledge Base

1. Voeg in de QnA-portal op de pagina **Bewerken** aan de volgende vragen metagegevens toe van `topic:power`:

    |Vragen|
    |--|
    |Uw Surface Pro 4 opladen|
    |Het niveau van de accu controleren|

    Twee QnA-paren hebben de metagegevensset.

    > [!TIP]
    > Om de metagegevens en QnA-ID's van elke set te zien, moet u de Knowledge Base exporteren. Selecteer de pagina **Instellingen** en selecteer vervolgens **Exporteren** als `.xls`-bestand. Zoek dit gedownloade bestand en open het met Excel om te controleren op metagegevens en ID.

1. Selecteer **Opslaan en trainen**, selecteer dan de pagina **Publiceren** en vervolgens de knop **Publiceren**. Door deze acties zijn de wijziging beschikbaar voor de batchtest. Download de Knowledge Base vanuit de pagina **Instellingen**.

    Het gedownloade bestand heeft de juiste indeling voor de metagegevens en de juiste vraag-en-antwoord-set-ID. Gebruik deze velden in het volgende gedeelte

    > [!div class="mx-imgBorder"]
    > ![Geëxporteerde Knowledge Base met metagegevens](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>Een tweede batchtest maken

Er zijn twee belangrijke scenario's voor batchtesten:
* **Chatlogboekbestanden verwerken**, bepaal het meestvoorkomende antwoord voor een nog niet bekeken vraag. De meestvoorkomende situatie is wanneer u een logboekbestand van query's moet verwerken, zoals gebruikersvragen van een chatbot. Maak een batchbestandtest met alleen de vereiste kolommen. De test retourneert het meestvoorkomende antwoord voor elke vraag. Dat betekent niet dat het meestvoorkomende antwoord ook het goede antwoord is. Wanneer deze test is voltooid, gaat u verder met de validatietest.
* **Validatietest**, valideer het verwachte antwoord. Voor deze test moeten alle vragen en bijbehorende verwachte antwoorden in de batchtest zijn gevalideerd. Hier is mogelijk wat handmatige verwerking nodig.

In de volgende procedure wordt ervan uitgegaan dat het scenario het verwerken is van chatlogboeken met

1. Maak een nieuw batchtestbestand om de optionele gegevens in op te nemen: `batch-test-data-2.tsv`. Voeg de zes rijen van het oorspronkelijke invoer bestand voor batch tests toe en voeg de meta gegevens, het hoogste en het QnA-paar ID voor elke rij toe.

    Stel de metagegevens voor elke kolom in op dezelfde waarde: `topic:power`, om het geautomatiseerde proces te simuleren van het vergelijken van nieuwe tekst van chatlogboeken met de Knowledge Base.

    > [!div class="mx-imgBorder"]
    > ![Invoer tweede versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-2-input.png)

1. Voer de test opnieuw uit, wijzig de namen van het invoer- en uitvoerbestand om aan te geven dat het de tweede test is.

    > [!div class="mx-imgBorder"]
    > ![Uitvoer tweede versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Testresultaten en een geautomatiseerd testsysteem

Dit testuitvoerbestand kan worden geparseerd als onderdeel van een geautomatiseerde doorlopende testpijplijn.

Deze specifieke testuitvoer moet als volgt worden gelezen: elke rij is gefilterd met metagegevens en omdat niet elke rij overeenkwam met de metagegevens in de Knowledge Base, is het standaardantwoord geretourneerd voor deze niet-overeenkomende rijen ('geen goede overeenkomst gevonden in KB'). Van de rijen die overeenkomen, zijn de QnA-ID en Score geretourneerd.

Alle rijen hebben het label 'onjuist' geretourneerd, omdat geen enkele rij overeenkwam met het verwachte antwoord-ID.

U moet met deze resultaten kunnen zien dat u de tekst van een chatlogboek kunt gebruiken als de query voor elke rij. Zonder iets te weten over de gegevens vertellen de resultaten u veel over de gegevens dat u kunt gebruiken voor het vervolg:

* metagegevens
* QnA-ID
* Score

Was het filteren met metagegevens een goed idee voor de test? Ja en nee. Het testsysteem moet testbestanden maken voor elk metagegevenspaar en ook een test zonder metagegevensparen.

### <a name="clean-up-resources"></a>Resources opschonen

Als u deze Knowledge Base verder niet gaat testen, verwijder dan het batchbestandprogramma en de testbestanden.

Als u deze Knowledge Base verder niet gaat gebruiken, verwijder dan de Knowledge Base met de volgende stappen:

1. Selecteer in de QnA Maker-portal **Mijn Knowledge Bases** in het hoofdmenu.
1. Selecteer in de lijst met Knowledge Bases het pictogram **Verwijderen** op de rij van de Knowledge Base van deze quickstart.

[Naslag documentatie over het hulp programma](../reference-tsv-format-batch-testing.md) is onder andere:

* het opdracht regel voorbeeld van het hulp programma
* de indeling voor bestanden voor TSV-invoer en-uitvoer

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase publiceren](../quickstarts/create-publish-knowledge-base.md)