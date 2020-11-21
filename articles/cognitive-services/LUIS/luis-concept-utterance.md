---
title: Goed voor beeld van uitingen-LUIS
description: Uitingen zijn invoer van de gebruiker die uw app nodig heeft voor de interpretatie. Verzamel zinsdelen die u denkt dat gebruikers worden ingevoerd. Uitingen bevatten die hetzelfde zijn, maar die anders zijn gemaakt in woord lengte en woord plaatsing.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 96b9754908f437ccf81e002e9e9dd17af0bab4e3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019072"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Begrijpen wat goede uitingen zijn voor uw LUIS-app

**Uitingen** zijn invoer van de gebruiker die uw app moet interpreteren. Om LUIS te trainen om de intenties en entiteiten van hen te extra heren, is het belang rijk dat u voor elke intentie een groot aantal verschillende voor beeld-uitingen vastlegt. Actief leren of het proces van het voortzetten van de nieuwe uitingen is essentieel voor machine learning-informatie die LUIS biedt.

Verzamel uitingen die u denkt dat gebruikers worden ingevoerd. Neem uitingen op. Dit betekent hetzelfde als op verschillende manieren:

* Lengte van Utterance-short, medium en Long voor uw client-app
* Lengte van woord en woord groep
* Plaatsing van woorden: entiteit aan het begin, Midden en einde van utterance
* Gecontroleerd
* Pluralization
* Als gevolg
* Keuze uit zelfstandig naam woord en werk woord
* [Interpunctie](luis-reference-application-settings.md#punctuation-normalization) : een goed RAS met de juiste, onjuiste en geen grammatica

## <a name="how-to-choose-varied-utterances"></a>Een variabele uitingen kiezen

Wanneer u voor het eerst aan de slag gaat door [bijvoorbeeld uitingen toe te voegen](./luis-how-to-add-entities.md) aan uw Luis-model, zijn hier enkele principes die u moet onthouden.

### <a name="utterances-arent-always-well-formed"></a>Uitingen zijn niet altijd goed gevormd

Dit kan een zin zijn, zoals "een ticket aan Parijs voor mij" of een fragment van een zin, zoals "boeken" of "Parijs vlucht".  Gebruikers maken vaak spel fouten. Overweeg bij het plannen van uw app of u [Bing spellingcontrole](luis-tutorial-bing-spellcheck.md) gebruikt om gebruikers invoer te corrigeren voordat u deze aan Luis door gegeven.

Als u de spelling van de gebruiker uitingen niet wilt controleren, moet u LUIS trainen op uitingen met type-en spel fouten.

### <a name="use-the-representative-language-of-the-user"></a>De representatieve taal van de gebruiker gebruiken

Wanneer u uitingen kiest, moet u er rekening mee houden dat u een veelvoorkomende term of woord groep zou kunnen hebben voor de typische gebruiker van uw client toepassing. Ze hebben mogelijk geen domein ervaring. Wees voorzichtig met het gebruik van termen of zinsdelen die een gebruiker alleen zou zeggen als hij een expert was.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Kies verschillende terminologie en formule ring

U zult merken dat zelfs als u een gevarieerde zin maakt, u nog steeds een woorden lijst moet herhalen.

Doe het volgende voor beeld uitingen:

|Voorbeelden van utterances|
|--|
|Hoe krijg ik een computer?|
|Hoe krijg ik een computer?|
|Ik wil een computer verkrijgen, hoe ga ik daar naartoe?|
|Wanneer kan ik een computer?|

De basis term hier, ' computer ', is niet gevarieerd. Gebruik alternatieven als desktop computer, laptop, werk station of zelfs alleen machine. LUIS kan op intelligente wijze synoniemen uit de context afleiden, maar wanneer u uitingen voor training maakt, is het altijd beter om ze te variëren.

## <a name="example-utterances-in-each-intent"></a>Voor beeld van uitingen in elke intentie

Elke intentie moet voorbeeld uitingen hebben, ten minste 15. Als u een intentie hebt die geen voorbeeld uitingen heeft, kunt u LUIS niet trainen. Als u een intentie hebt met een of meer voor beeld uitingen, is het mogelijk dat LUIS de bedoeling niet nauw keurig voors pellen.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Kleine groepen van 15 uitingen toevoegen voor elke ontwerp herhaling

Voeg in elke herhaling van het model geen grote hoeveelheid uitingen toe. Voeg uitingen toe aan hoeveel heden van 15. [Train](luis-how-to-train.md), [Publiceer](luis-how-to-publish-app.md)en [test](luis-interactive-test.md) het opnieuw.

LUIS bouwt efficiënte modellen met uitingen die zorgvuldig zijn geselecteerd door de auteur van het LUIS-model. Het is niet waardevol om te veel uitingen toe te voegen, omdat het Verwar ring leidt.

Het is beter om met een paar uitingen te beginnen en vervolgens [eind punt uitingen te controleren](luis-how-to-review-endpoint-utterances.md) op juiste intentie van voor spelling en extractie van entiteiten.

## <a name="utterance-normalization"></a>Utterance normalisatie

Utterance normalisatie is het proces van het negeren van de effecten van typen tekst, zoals interpunctie en diakritische tekens, tijdens de training en voor spelling.

De utterance normalisatie-instellingen zijn standaard uitgeschakeld. Deze instellingen zijn onder meer:

* Word-formulieren
* Diakritische tekens mag
* Interpunctie

Als u een normalisatie-instelling inschakelt, worden de scores in het **test** deel venster, batch tests en eindpunt query's gewijzigd voor alle uitingen voor die normalisatie-instelling.

Wanneer u een versie in de LUIS-Portal kloont, gaan de versie-instellingen door naar de nieuwe gekloonde versie.

Stel de versie-instellingen in via de LUIS-Portal, in de sectie **beheren** , op de pagina **Toepassings instellingen** of de [API-versie-instellingen bijwerken](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings). Meer informatie over deze normalisatie wijzigingen in de [verwijzing](luis-reference-application-settings.md).

### <a name="word-forms"></a>Word-formulieren

Als **woord vormen** worden genormaliseerd, worden de verschillen in woorden die buiten het hoofd gebied uitvouwen, genegeerd.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Diakritische tekens mag

Diakritische tekens zijn tekens of tekens in de tekst, bijvoorbeeld:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Lees tekens
Het normaliseren van **interpunctie** betekent dat voordat uw modellen worden getraind en voordat uw eindpunt query's worden voor speld, wordt interpunctie verwijderd uit de uitingen.

Interpunctie is een afzonderlijk token in LUIS. Een utterance die een punt bevat aan het einde en een utterance die geen punt aan het einde bevatten, zijn twee afzonderlijke uitingen en kunnen twee verschillende voor spellingen ontvangen.

Als interpunctie niet is genormaliseerd, LUIS niet standaard interpunctie markeringen negeren, omdat sommige client toepassingen significant kunnen zijn voor deze markeringen. Zorg ervoor dat uw voor beeld-uitingen zowel interpunctie als geen interpunctie gebruiken voor beide stijlen om dezelfde relatieve scores te retour neren.

Zorg ervoor dat het model interpunctie verwerkt in het voor beeld uitingen (met en zonder interpunctie) of in de [patronen](luis-concept-patterns.md) waar het gemakkelijker is om interpunctie te negeren met de speciale syntaxis: `I am applying for the {Job} position[.]`

Als interpunctie geen specifieke betekenis heeft in uw client toepassing, kunt u overwegen [interpunctie te negeren](#utterance-normalization) door interpunctie te normaliseren.

### <a name="ignoring-words-and-punctuation"></a>Woorden en interpunctie negeren

Als u specifieke woorden of interpunctie in patronen wilt negeren, gebruikt u een [patroon](luis-concept-patterns.md#pattern-syntax) met de syntaxis _negeren_ van de vier Kante haken, `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Training met alle uitingen

Training is doorgaans niet-deterministisch: de utterance-voor spelling kan enigszins variëren in verschillende versies of apps.
U kunt niet-deterministische trainingen verwijderen door de API voor [versie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -instellingen `UseAllTrainingData` te wijzigen met de naam/waarde-paar om alle trainings gegevens te gebruiken.

## <a name="testing-utterances"></a>Uitingen testen

Ontwikkel aars moeten hun LUIS-toepassing met echt verkeer testen door uitingen naar de URL voor het [Voorspellings eindpunt](luis-how-to-azure-subscription.md) te sturen. Deze uitingen worden gebruikt om de prestaties van de intenties en entiteiten te verbeteren met [beoordeling uitingen](luis-how-to-review-endpoint-utterances.md). Tests die zijn verzonden met het deel venster LUIS website testen, worden niet via het eind punt verzonden en bijdragen dus niet aan actief leren.

## <a name="review-utterances"></a>Uitingen controleren

Nadat uw model is getraind, gepubliceerd en [endpoint](luis-glossary.md#endpoint) -query's ontvangen, [controleert u de uitingen](luis-how-to-review-endpoint-utterances.md) die door Luis is voorgesteld. LUIS selecteert eind punt uitingen met een lage score voor de intentie of entiteit.

## <a name="best-practices"></a>Aanbevolen procedures

Bekijk [Aanbevolen procedures](luis-concept-best-practices.md) en pas deze toe als onderdeel van uw reguliere ontwerp cyclus.

## <a name="label-for-word-meaning"></a>Label voor woord betekenis

Als het woord of de rang schikking van Word hetzelfde is, maar niet hetzelfde is, moet u deze niet aan de entiteit labelen.

De volgende uitingen, het woord `fair` is een homograph. De spelling is hetzelfde, maar heeft een andere betekenis:

|Utterance|
|--|
|Wat voor soort graafschap komt in het gebied Seattle van deze zomer?|
|Is de huidige beoordeling voor de Seattle-beoordeling eerlijk?|

Als u wilt dat een gebeurtenis entiteit alle gebeurtenis gegevens vindt, labelt u het woord `fair` in het eerste utterance, maar niet in de tweede.


## <a name="next-steps"></a>Volgende stappen
Zie [voor beeld uitingen toevoegen](./luis-how-to-add-entities.md) voor informatie over het trainen van een Luis-app om inzicht te krijgen in gebruikers uitingen.