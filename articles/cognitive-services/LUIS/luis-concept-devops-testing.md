---
title: Testen op DevOps voor LUIS-apps
description: Uw Language Understanding-app (LUIS) testen in een DevOps-omgeving.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788448"
---
# <a name="testing-for-luis-devops"></a>Testen op LUIS DevOps

Software-engineers die een Language Understanding-app (LUIS) ontwikkelen, kunnen DevOps-procedures rond [broncode beheer](luis-concept-devops-sourcecontrol.md)en [geautomatiseerde builds](luis-concept-devops-automation.md), [testen](luis-concept-devops-testing.md)en [release beheer](luis-concept-devops-automation.md#release-management) Toep assen door deze richt lijnen te volgen.

In flexibele software ontwikkeling methodologieën speelt tests een integrale rol bij het bouwen van kwaliteits software. Elke belang rijke wijziging van een LUIS-app moet vergezeld gaan van tests die zijn ontworpen om de nieuwe functionaliteit te testen die de ontwikkelaar in de app bouwt. Deze tests worden in uw bron code opslagplaats gecontroleerd, samen met de `.lu` bron van uw Luis-app. De implementatie van de wijziging is voltooid wanneer de app voldoet aan de tests.

Tests zijn een essentieel onderdeel van [CI/cd-werk stromen](luis-concept-devops-automation.md). Wanneer wijzigingen in een LUIS-app worden voorgesteld in een pull-aanvraag (PR) of nadat wijzigingen zijn samengevoegd in uw hoofd vertakking, moeten de tests door CI-werk stromen worden uitgevoerd om te controleren of de updates geen regressies hebben veroorzaakt.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Testen van eenheden en batch tests

Er zijn twee verschillende soorten tests voor een LUIS-app die u moet uitvoeren in doorlopende integratie werk stromen:

- **Eenheids tests** : relatief eenvoudige tests die de belangrijkste functionaliteit van uw Luis-app verifiëren. Er wordt een eenheids test gegeven wanneer de verwachte intentie en de verwachte entiteiten worden geretourneerd voor een bepaalde test utterance. Alle eenheids tests moeten worden uitgevoerd om de test uitvoering te volt ooien.  
Dit soort tests is vergelijkbaar met [interactieve tests](./luis-concept-test.md) die u kunt uitvoeren in de [Luis-Portal](https://www.luis.ai/).

- **Batch tests** : batch tests zijn een allesomvattende test op uw huidige getrainde model om de prestaties ervan te meten. In tegens telling tot eenheids tests is batch tests niet geslaagd | fouten testen. De verwachting met batch tests is niet dat elke test de verwachte intentie en verwachte entiteiten retourneert. In plaats daarvan helpt een batch-test u de nauw keurigheid van elke intentie en entiteit in uw app te bekijken, zodat u de tijd kunt vergelijken wanneer u verbeteringen aanbrengt.  
Dit soort tests is hetzelfde als de [batch tests](./luis-how-to-batch-test.md) die u interactief kunt uitvoeren in de Luis-Portal.

U kunt eenheids testen gebruiken vanaf het begin van uw project. Batch tests zijn alleen echt van waarde wanneer u het schema van uw LUIS-app hebt ontwikkeld en u aan de slag gaat met het verbeteren van de nauw keurigheid.

Voor zowel de eenheids tests als voor batch tests moet u ervoor zorgen dat uw test uitingen gescheiden worden gehouden van uw trainings uitingen. Als u test op dezelfde gegevens die u traint, krijgt u de onbedoelde indruk dat uw app goed presteert op het vlak van de test gegevens. Tests moeten onduidelijk zijn door het model om te testen hoe goed het generaliseren is.

### <a name="writing-tests"></a>Schrijf tests

Wanneer u een set tests schrijft, moet u voor elke test het volgende definiëren:

* Utterance testen
* Verwachte intentie
* Verwachte entiteiten.

Gebruik de LUIS- [syntaxis voor batch bestanden](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) voor het definiëren van een groep tests in een bestand met JSON-indeling. Bijvoorbeeld:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Sommige test Programma's, zoals [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) biedt ook ondersteuning voor test bestanden met LUDown-indeling.

#### <a name="designing-unit-tests"></a>Eenheids tests ontwerpen

Eenheids tests moeten zijn ontworpen om de kern functionaliteit van uw LUIS-app te testen. In elke iteratie, of Sprint, van uw app-ontwikkeling moet u een voldoende aantal tests schrijven om te controleren of de sleutel functionaliteit die u implementeert in die iteratie goed werkt.

In elke eenheids test kunt u voor een bepaalde test utterance het volgende doen:

* Testen of het juiste doel is geretourneerd
* Test of de sleutel entiteiten die essentieel zijn voor uw oplossing, worden geretourneerd.
* Test of de [Voorspellings Score](./luis-concept-prediction-score.md) voor intentie en entiteiten de drempel waarde die u definieert, overschrijdt. U kunt bijvoorbeeld besluiten dat een test is geslaagd als de Voorspellings score voor de intentie en voor uw sleutel entiteiten groter is dan 0,75.

Bij eenheids tests is het een goed idee om te testen of uw belangrijkste entiteiten zijn geretourneerd in de Voorspellings reactie, maar om eventuele valse positieven te negeren. Fout- *positieven* zijn entiteiten die worden gevonden in de Voorspellings reactie, maar die niet zijn gedefinieerd in de verwachte resultaten voor uw test. Door fout-positieven te negeren, wordt het minder verlieslatend voor het ontwikkelen van eenheids tests, terwijl u zich nog steeds kunt richten op het testen dat de gegevens die naar uw oplossing zijn, worden geretourneerd in een Voorspellings reactie.

> [!TIP]
> De [NLU. ](https://github.com/microsoft/NLU.DevOps) Het hulp programma DevOps ondersteunt al uw Luis-test behoeften. `compare`Als de opdracht wordt gebruikt in de [eenheids test modus](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) , wordt bevestigd dat alle tests worden door gegeven en worden fout positieve resultaten genegeerd voor entiteiten die geen label hebben in de verwachte resultaten.

#### <a name="designing-batch-tests"></a>Batch tests ontwerpen

Batch test sets moeten een groot aantal test cases bevatten, ontworpen om te testen in alle intenties en alle entiteiten in uw LUIS-app. Zie [batch tests in de Luis-Portal](./luis-how-to-batch-test.md) voor meer informatie over het definiëren van een batch-testset.

### <a name="running-tests"></a>Tests uitvoeren

De LUIS-Portal biedt functies die u helpen bij interactieve tests:

* [**Interactieve tests**](./luis-concept-test.md) bieden u de mogelijkheid om een voor beeld-utterance te verzenden en een antwoord te krijgen van Luis-herkende intenties en entiteiten. U kunt het succes van de test testen door visuele inspectie te controleren.

* [**Batch tests**](./luis-how-to-batch-test.md) gebruiken een batch test bestand als invoer om uw actieve getrainde versie te valideren om de nauw keurigheid van de voor spelling te meten. Met een batch test kunt u de nauw keurigheid van elke intentie en entiteit in uw actieve versie bekijken, waarbij de resultaten worden weer gegeven in een grafiek.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Testen uitvoeren in een geautomatiseerde build-werk stroom

De interactieve test functies in de LUIS-Portal zijn handig, maar voor DevOpse geautomatiseerde tests die worden uitgevoerd in een CI/CD-werk stroom, worden bepaalde vereisten door lopen:

* Test hulpprogramma's moeten worden uitgevoerd in een werk stroom stap op een build-server. Dit betekent dat de hulpprogram ma's op de opdracht regel moeten kunnen worden uitgevoerd.
* De test hulpprogramma's moeten een groep tests kunnen uitvoeren op basis van een eind punt en automatisch de verwachte resultaten controleren op basis van de werkelijke resultaten.
* Als de tests mislukken, moeten de test hulpprogramma's een status code retour neren om de werk stroom te stoppen en de build te laten mislukken.

LUIS biedt geen opdracht regel programma of een API op hoog niveau die deze functies biedt. We raden u aan de NLU te gebruiken [. DevOps](https://github.com/microsoft/NLU.DevOps) -hulp programma voor het uitvoeren van tests en het controleren van resultaten, zowel op de opdracht regel als tijdens automatische tests binnen een CI/cd-werk stroom.

Voor de test functies die beschikbaar zijn in de LUIS-Portal is geen gepubliceerd eind punt vereist en maakt deel uit van de LUIS-ontwerp functies. Wanneer u tests in een geautomatiseerde build-werk stroom implementeert, moet u de versie van de LUIS-app publiceren zodat deze naar een eind punt wordt getest, zodat test hulpprogramma's zoals NLU. DevOps kan Voorspellings aanvragen verzenden als onderdeel van testen.

> [!TIP]
> * Als u uw eigen test oplossing implementeert en code schrijft om test uitingen naar een eind punt te verzenden, moet u er rekening mee houden dat als u de LUIS-ontwerp sleutel gebruikt, het toegestane transactie tempo beperkt is tot 5TPS. U kunt de verzend snelheid beperken of in plaats daarvan een Voorspellings sleutel gebruiken.
> * Bij het verzenden van test query's naar een eind punt, moet u `log=false` in de query reeks van uw Voorspellings aanvraag gebruiken. Op deze manier zorgt u ervoor dat uw test uitingen niet wordt geregistreerd door LUIS en uiteindelijk wordt beëindigd in de controle lijst van het eind punt uitingen die wordt weer gegeven door de LUIS-functie voor het maken van [actieve lessen](./luis-concept-review-endpoint-utterances.md) en, als gevolg hiervan, per ongeluk worden toegevoegd aan de trainings-uitingen van uw app.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Eenheids tests uitvoeren op de opdracht regel en in CI/CD-werk stromen

U kunt de [NLU gebruiken. DevOps](https://github.com/microsoft/NLU.DevOps) -pakket om tests uit te voeren op de opdracht regel:

* Gebruik het NLU. DevOps [test opdracht](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) voor het verzenden van tests van een test bestand naar een eind punt en voor het vastleggen van de werkelijke Voorspellings resultaten in een bestand.
* Gebruik het NLU. [Opdracht DevOps Compare](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) om de werkelijke resultaten te vergelijken met de verwachte resultaten die in het invoer test bestand zijn gedefinieerd. Met de `compare` opdracht wordt een nunit-test uitvoer gegenereerd. Als u deze optie gebruikt in de [eenheids test modus](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) met de `--unit-test` vlag, moet u bevestigen dat alle tests zijn geslaagd.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Batch tests uitvoeren op de opdracht regel en in CI/CD-werk stromen

U kunt ook de NLU gebruiken. DevOps-pakket om batch tests uit te voeren op de opdracht regel.

* Gebruik het NLU. DevOps [test opdracht](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) voor het verzenden van tests van een test bestand naar een eind punt en voor het vastleggen van de werkelijke Voorspellings resultaten in een bestand, net als bij eenheids testen.
* Gebruik het NLU. DevOps [compare (opdracht](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) ) in de [prestatie test modus](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) om de prestaties van uw app te meten, kunt u de prestaties van uw app ook vergelijken met een basislijn prestatie-benchmark, bijvoorbeeld de resultaten van de laatste door voering naar de hoofd-of de huidige release. In de prestatie test modus wordt met de `compare` opdracht nunit test output en [batch test resultaten](./luis-glossary.md#batch-test) gegenereerd in JSON-indeling.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS niet-deterministische training en het effect op tests

Wanneer LUIS een model traint, zoals een intentie, moeten beide positieve gegevens worden gebruikt: de gelabelde training uitingen die u hebt opgegeven om de app te trainen voor het model en negatieve gegevens gegevens die *niet* geldig zijn voor beelden van het gebruik van het model. Tijdens de training bouwt LUIS de negatieve gegevens van het ene model op uit alle positieve gegevens die u hebt opgegeven voor de andere modellen, maar in sommige gevallen kan er een onevenwichtige hoeveelheid gegevens worden geproduceerd. Om dit te voor komen, LUIS voor beelden van een subset van de negatieve gegevens op een niet-deterministische manier om te optimaliseren voor een beter gebalanceerde opleidingsset, verbeterde model prestaties en een snellere training tijd.

Als gevolg van deze niet-deterministische training is het mogelijk dat er iets [andere voor spellingen worden ontvangen tussen verschillende trainings sessies](./luis-concept-prediction-score.md), meestal voor intenties en/of entiteiten waarbij de [Voorspellings Score](./luis-concept-prediction-score.md) niet hoog is.

Als u niet-deterministische training wilt uitschakelen voor de LUIS-app-versies die u bouwt voor het testen, gebruikt u de [API versie-instellingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de `UseAllTrainingData` instelling ingesteld op `true` .

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [implementeren van CI/cd-werk stromen](luis-concept-devops-automation.md)
* Meer informatie over het [implementeren van DevOps voor Luis met github](luis-how-to-devops-with-github.md)