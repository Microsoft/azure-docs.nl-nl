---
title: Herhalend app-ontwerp-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS leert het beste in een iteratieve cyclus van model wijzigingen, utterance-voor beelden, publiceren en verzamelen van gegevens uit eindpunt query's.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 753d214b520affb59722bc29dbabc50c6e5968f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91538718"
---
# <a name="iterative-app-design-for-luis"></a>Iteratief app-ontwerp voor LUIS

Een Language Understanding-app (LUIS) leert en werkt het meest efficiënt met iteratie. Hier volgt een typische iteratie cyclus:

* Nieuwe versie maken
* Bewerk het LUIS-app-schema. Dit omvat:
    * Doel stellingen met voor beeld-uitingen
    * Entiteiten
    * Functies
* Trainen, testen en publiceren
    * Testen op het Voorspellings eindpunt voor actief leren
* Gegevens verzamelen van eindpunt query's

![Ontwerpcyclus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Een LUIS-schema bouwen

In het schema van een app wordt gedefinieerd wat de gebruiker vraagt (  de bedoeling _of het doel)_ en welke delen van de intentie details bevatten ( _entiteiten_ genoemd) die worden gebruikt om te helpen bij het bepalen van het antwoord.

Het app-schema moet specifiek zijn voor de app-domeinen om te bepalen welke woorden en zinsdelen relevant zijn, en om een standaard woord volgorde te bepalen.

Voor beeld-uitingen vertegenwoordigen gebruikers invoer, zoals herkende spraak of tekst, die de app verwacht tijdens runtime.

Het schema vereist intenties en _moet entiteiten hebben_ .

### <a name="example-schema-of-intents"></a>Voorbeeld schema van intenties

Het meest voorkomende schema is een intentie schema dat is ingedeeld met intents. Dit type schema gebruikt LUIS om de bedoeling van een gebruiker te bepalen.

Het schema type intentie kan entiteiten hebben als het LUIS kan bepalen wat de bedoeling van de gebruiker is. Bijvoorbeeld, een verzend entiteit (als _machine learning functie_ voor een intentie) helpt Luis te bepalen van de bedoeling van een verzen ding.

### <a name="example-schema-of-entities"></a>Voorbeeld schema van entiteiten

Een entiteits schema is gericht op entiteiten. Dit zijn de gegevens die worden opgehaald uit de uitingen van de gebruiker. Bijvoorbeeld, als een gebruiker heeft te zeggen, "Ik wil graag drie pizzas best Ellen." Er worden twee entiteiten geëxtraheerd: _drie_ en _pizzas_. Deze worden gebruikt om te voldoen aan de voor delen, die een bestelling zou moeten nemen.

Voor een entiteits schema is de bedoeling van de utterance minder belang rijk voor de client toepassing.

Een algemene methode voor het ordenen van een entiteits schema is het toevoegen van alle voor beeld-uitingen aan de **geen** intentie.

### <a name="example-of-a-mixed-schema"></a>Voor beeld van een gemengd schema

Het meest krachtige en rijpste schema is een intentie schema met een volledig scala aan entiteiten en functies. Dit schema kan worden gestart als een intentie-of entity-schema en kan worden uitgebreid om concepten van beide toe te voegen, omdat de client toepassing die gegevens nodig heeft.

## <a name="add-example-utterances-to-intents"></a>Voor beeld van uitingen aan intenties toevoegen

LUIS heeft een aantal voor beelden van uitingen in elk **doel** nodig. Het voor beeld uitingen vereist voldoende variatie van woord keuze en woord volgorde om te kunnen bepalen met welke intentie de utterance bedoeld is.

> [!CAUTION]
> Voeg geen voor beeld-uitingen toe. Begin met 15 tot 30 specifieke en verschillende voor beelden.

Elk voor beeld-utterance moet **vereiste gegevens bevatten om te kunnen worden geëxtraheerd** en gemaakt met **entiteiten**.

|Sleutel element|Doel|
|--|--|
|Intentie|**Classificeer** gebruikers uitingen in één intentie of actie. Voor beelden zijn `BookFlight` en `GetWeather` .|
|Entiteit|**Haal** gegevens op uit utterance die nodig zijn om de bedoeling te volt ooien. Voor beelden zijn onder meer datum en tijd van reis en locatie.|

Een LUIS-app kan worden ontworpen voor het negeren van uitingen die niet relevant zijn voor het domein van een app door de utterance toe te wijzen aan de **geen** intentie.

## <a name="test-and-train-your-app"></a>Uw app testen en trainen

Nadat u 15 tot 30 verschillende voor beeld-uitingen in elke intentie hebt, met de vereiste entiteiten waaraan een label is gekoppeld, moet u uw LUIS-app testen en [trainen](luis-how-to-train.md) .

## <a name="publish-to-a-prediction-endpoint"></a>Publiceren naar een Voorspellings eindpunt

De LUIS-app moet worden gepubliceerd zodat deze beschikbaar is in de lijst [Voorspellings eindpunt regio's](luis-reference-regions.md).

## <a name="test-your-published-app"></a>Uw gepubliceerde toepassing testen

U kunt uw gepubliceerde LUIS-app testen vanuit het HTTPS prediction-eind punt. Door te testen vanaf het Voorspellings eindpunt kan LUIS een wille keurige uitingen met lage betrouw baarheid kiezen voor [controle](luis-how-to-review-endpoint-utterances.md).

## <a name="create-a-new-version-for-each-cycle"></a>Een nieuwe versie maken voor elke cyclus

Elke versie is een moment opname in de tijd van de LUIS-app. Maak een nieuwe versie voordat u wijzigingen aanbrengt in de app. Het is eenvoudiger om terug te gaan naar een oudere versie dan om de intenties en uitingen te verwijderen.

De versie-ID bestaat uit tekens, cijfers of '. ' en mag niet langer zijn dan 10 tekens.

De eerste versie (0,1) is de standaard versie die actief is.

### <a name="begin-by-cloning-an-existing-version"></a>Begin door een bestaande versie te klonen

Een bestaande versie klonen die moet worden gebruikt als uitgangs punt voor elke nieuwe versie. Nadat u een versie hebt gekloond, wordt de nieuwe versie de **actieve** versie.

### <a name="publishing-slots"></a>Publicatie sleuven

U kunt publiceren naar de stage-en/of productie-sleuven. Elke sleuf kan een andere versie of dezelfde versie hebben. Dit is handig als u wijzigingen wilt controleren voordat u publiceert naar productie, die beschikbaar is voor bots of andere LUIS die apps aanroepen.

Getrainde versies zijn niet automatisch beschikbaar op het [eind punt](luis-glossary.md#endpoint)van uw Luis-app. U moet een versie [publiceren](luis-how-to-publish-app.md) of opnieuw publiceren zodat deze beschikbaar is in uw Luis-app-eind punt. U kunt publiceren naar **fase ring** en **productie**, zodat u twee versies van de app beschikbaar hebt op het eind punt. Als er meer versies van de app op een eind punt beschikbaar moeten zijn, moet u de versie exporteren en opnieuw importeren in een nieuwe app. De nieuwe app heeft een andere app-ID.

### <a name="import-a-version"></a>Een versie importeren

Een versie kan worden **geïmporteerd** als een nieuwe:
* App, met een nieuwe app-ID
* Versie van een bestaande app

Deze versie wordt de actieve versie en gebruikt de versie-ID in de `versionId` eigenschap van het app-bestand.

### <a name="export-a-version"></a>Een versie exporteren

Een versie kan vanuit de LUIS-Portal worden **geëxporteerd** op het niveau van de app of het versie niveau:

* App-niveau: Selecteer app op **mijn apps** pagina en selecteer vervolgens **exporteren**
* Versie niveau: Selecteer app-koppeling op **mijn apps** pagina, selecteer **beheren**, selecteer **versies**

Het enige verschil is dat de geëxporteerde versie op app-niveau de momenteel actieve versie is op het niveau van versie, u kunt een wille keurige versie kiezen die u wilt exporteren op de pagina **[instellingen](luis-how-to-manage-versions.md)** .

Het geëxporteerde bestand bevat **niet** :

* informatie over machine learning, omdat de app opnieuw wordt getraind nadat deze is geïmporteerd
* Informatie over Inzender

Als u een back-up wilt maken van uw LUIS-app-schema, exporteert u een versie vanuit de [Luis-Portal](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Wijzigingen in Inzender beheren met versies en inzenders

LUIS maakt gebruik van het concept van inzenders voor een app door Azure-machtigingen op resource niveau aan te bieden. Combi neer dit concept met versie beheer om gerichte samen werking mogelijk te maken.

Gebruik de volgende technieken om Inzender wijzigingen te beheren in uw app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Meerdere versies binnen dezelfde app beheren

Begin met het [klonen](luis-how-to-manage-versions.md#clone-a-version) van een basis versie voor elke auteur.

Elke auteur brengt wijzigingen aan in hun eigen versie van de app. Wanneer de auteur aan het model is voldaan, exporteert u de nieuwe versies naar JSON-bestanden.

Geëxporteerde apps `.json` of `.lu` bestanden kunnen worden vergeleken voor wijzigingen. Combi neer de bestanden om één bestand van de nieuwe versie te maken. Wijzig de `versionId` eigenschap om de nieuwe samengevoegde versie aan te duiden. Importeer die versie in de oorspronkelijke app.

Met deze methode kunt u één actieve versie, één fase versie en één gepubliceerde versie hebben. U kunt de resultaten van de actieve versie vergelijken met een gepubliceerde versie (fase of productie) in het [interactieve test venster](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Meerdere versies beheren als apps

De basis versie [exporteren](luis-how-to-manage-versions.md#export-version) . Elke auteur importeert de versie. De persoon die de app importeert, is de eigenaar van de versie. Wanneer de app is gewijzigd, exporteert u de versie.

Geëxporteerde apps zijn bestanden in JSON-indeling, die kunnen worden vergeleken met de basis export voor wijzigingen. Combi neer de bestanden om één JSON-bestand van de nieuwe versie te maken. Wijzig de eigenschap **versionId** in de JSON om de nieuwe samengevoegde versie aan te duiden. Importeer die versie in de oorspronkelijke app.

Meer informatie over het ontwerpen van bijdragen van deel [nemers](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Eind punt uitingen bekijken om de nieuwe iteratieve cyclus te starten

Wanneer u klaar bent met een iteratie cyclus, kunt u het proces herhalen. Begin met het [controleren van voorspellings eindpunt uitingen](luis-how-to-review-endpoint-utterances.md) Luis gemarkeerd met lage betrouw baarheid. Controleer deze uitingen voor zowel de juiste vooraf gedicteerde intentie als juiste en volledige entiteit geëxtraheerd. Nadat u de wijzigingen hebt gecontroleerd en geaccepteerd, moet de controle lijst leeg zijn.

## <a name="next-steps"></a>Volgende stappen

Leer concepten over [samen werking](luis-how-to-azure-subscription.md).
