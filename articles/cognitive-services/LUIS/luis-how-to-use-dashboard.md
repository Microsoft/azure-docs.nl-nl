---
title: Dash board-Language Understanding-LUIS
titleSuffix: Azure Cognitive Services
description: Verhelp de intenties en entiteiten met het dash board van uw getrainde app. In het dash board worden algemene app-gegevens weer gegeven, met de kenmerken van de intenties die moeten worden opgelost.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541404"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Het dash board gebruiken om uw app te verbeteren

Zoek en los problemen met de intenties van uw getrainde app op wanneer u bijvoorbeeld uitingen gebruikt. In het dash board worden algemene app-gegevens weer gegeven, met de kenmerken van de intenties die moeten worden opgelost.

Dashboard analyse bekijken is een iteratief proces, herhaal wanneer u uw model wijzigt en verbetert.

Deze pagina heeft geen relevante analyse voor apps waarvoor geen voor beeld-uitingen in de intenties, ook wel _alleen-patroon-_ apps worden genoemd.

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Welke problemen kunnen worden opgelost met het dash board?

De drie problemen die worden behandeld in het dash board zijn:

|Probleem|Grafiek kleur|Uitleg|
|--|--|--|
|Onevenwichtige gegevens|-|Dit gebeurt wanneer het aantal voor beeld-uitingen significant varieert. Alle doel stellingen moeten _ongeveer_ hetzelfde aantal voorbeeld uitingen hebben, behalve de geen intentie. Het mag slechts 10%-15% bevatten van het totale aantal uitingen in de app.<br><br> Als de gegevens niet in balans zijn, maar de intentie nauwkeurigheid boven bepaalde drempel waarde ligt, wordt deze onevenwichtigheid niet gerapporteerd als een probleem.<br><br>**Begin met dit probleem: het kan de hoofd oorzaak van de andere problemen zijn.**|
|Onduidelijke voor spellingen|Oranje|Dit doet zich voor wanneer het beste doel en de volgende intentie scores dicht genoeg zijn dat ze in de volgende training kunnen spie gelen, vanwege [negatieve steek proeven](luis-how-to-train.md#train-with-all-data) of meer voorbeeld uitingen die aan de intentie zijn toegevoegd. |
|Onjuiste voor spellingen|Red|Dit probleem treedt op wanneer een voor beeld-utterance niet wordt voor speld voor de gelabelde intentie (het doel van de intentie).|

Corrigerende voor spellingen worden weer gegeven met de kleur blauw.

In het dash board worden deze problemen weer gegeven en wordt aangegeven welke intenties worden beïnvloed en wordt voorgesteld wat u moet doen om de app te verbeteren.

## <a name="before-app-is-trained"></a>Voordat de app is getraind

Voordat u de app traint, bevat het dash board geen suggesties voor oplossingen. Train uw app om deze suggesties te bekijken.

## <a name="check-your-publishing-status"></a>De publicatie status controleren

De **publicatie status** kaart bevat informatie over de laatste publicatie van de actieve versie.

Controleer of de actieve versie de versie is die u wilt herstellen.

![Dash board toont de externe services van de app, gepubliceerde regio's en geaggregeerde endpoint treffers.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Dit toont ook alle externe services, gepubliceerde regio's en geaggregeerde eindpunt treffers.

## <a name="review-training-evaluation"></a>Trainings evaluatie evalueren

De **trainings evaluatie** kaart bevat de geaggregeerde samen vatting van de algehele nauw keurigheid van uw app op gebied. De score geeft de kwaliteit van de opzet aan.

![De trainings evaluatie kaart bevat het eerste informatie gebied over de algehele nauw keurigheid van uw app.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

In de grafiek ziet u de correct voorspelde intenties en de probleem gebieden met verschillende kleuren. Wanneer u de app verbetert met de suggesties, neemt deze score toe.

De voorgestelde oplossingen worden gescheiden door het probleem type en zijn het belangrijkst voor uw app. Als u de problemen per intentie wilt controleren en oplossen, gebruikt u de kaart **[bedoelingen met fouten](#intents-with-errors)** aan de onderkant van de pagina.

Elk probleem gebied heeft intenties die moeten worden opgelost. Wanneer u de naam van de doel groep selecteert, wordt de pagina **intentie** geopend met een filter dat is toegepast op de uitingen. Met dit filter kunt u zich richten op de uitingen die het probleem veroorzaken.

### <a name="compare-changes-across-versions"></a>Wijzigingen in versies vergelijken

Maak een nieuwe versie voordat u wijzigingen aanbrengt in de app. In de nieuwe versie maakt u de voorgestelde wijzigingen in het voor beeld van het intentie uitingen en traint u het opnieuw. Op **de kaart van de dashboard** pagina kunt u de wijzigingen **weer geven van de getrainde versie** gebruiken.

![Wijzigingen in versies vergelijken](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrigeer de versie door voor beeld-uitingen en-training toe te voegen of te bewerken

De primaire methode voor het herstellen van uw app is het toevoegen of bewerken van voor beeld-uitingen en-Retrain. Het nieuwe of gewijzigde uitingen moet voldoen aan de richt lijnen voor [verschillende uitingen](luis-concept-utterance.md).

Het toevoegen van voor beeld-uitingen moet worden uitgevoerd door iemand die:

* heeft een hoge mate van meer informatie over wat uitingen in de verschillende doel stellingen zijn.
* weet hoe uitingen in één intentie kan worden verward met een ander doel.
* kan bepalen of twee intenten, die veelvuldig met elkaar worden verward, in één intentie moeten worden samengevouwen. Als dit het geval is, moeten de verschillende gegevens worden opgehaald met entiteiten.

### <a name="patterns-and-phrase-lists"></a>Lijsten met patronen en zinsdelen

De Analytics-pagina geeft niet aan wanneer [patronen](luis-concept-patterns.md) of [woordgroepen lijsten](luis-concept-feature.md)moeten worden gebruikt. Als u ze toevoegt, kan het helpen met onjuiste of onduidelijke voor spellingen, maar niet bij het onevenwicht van de gegevens.

### <a name="review-data-imbalance"></a>Gegevens onevenwicht controleren

Begin met dit probleem: het kan de hoofd oorzaak van de andere problemen zijn.

De lijst met intenties van **gegevens onevenwichtigheden** bevat intenties die meer uitingen nodig hebben om het onevenwicht van de gegevens te corrigeren.

**Ga als volgt te werk om dit probleem op te lossen**:

* Voeg meer uitingen toe aan de intentie en Train opnieuw.

Voeg geen uitingen toe aan de geen intentie, tenzij dit wordt voorgesteld op het dash board.

> [!Tip]
> Gebruik de derde sectie op de pagina, **uitingen per intentie** met de instelling **uitingen (aantal)** , als een snelle visuele hulp lijn waarvan de intenties meer uitingen nodig hebben.
    ![Gebruik ' uitingen (nummer) ' om intenties te vinden met een onevenwichtigheid in de gegevens.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Bekijk onjuiste voor spellingen

In de lijst met **onjuiste Voorspellings** intentie worden intenties met uitingen weer gegeven die als voor beeld voor een specifiek doel worden gebruikt, maar worden voor speld voor verschillende doel stellingen.

**Ga als volgt te werk om dit probleem op te lossen**:

* Bewerk uitingen zodat het doel specifiek is en Train het opnieuw.
* Combi neer intenties als uitingen te nauw keurig zijn uitgelijnd en opnieuw trainen.

### <a name="review-unclear-predictions"></a>Onduidelijke voor spellingen controleren

De lijst met **onduidelijke Voorspellings** intentie bevat intenties met uitingen met Voorspellings scores die niet ver genoeg zijn vanaf hun dichtstbijzijnde gegelijke manier, waardoor de beste intentie voor de utterance kan worden gewijzigd in de volgende training vanwege [negatieve steek proeven](luis-how-to-train.md#train-with-all-data).

**Om dit probleem op te lossen**;

* Bewerk uitingen zodat het doel specifiek is en Train het opnieuw.
* Combi neer intenties als uitingen te nauw keurig zijn uitgelijnd en opnieuw trainen.

## <a name="utterances-per-intent"></a>Uitingen per intentie

Deze kaart toont de totale status van de app voor de doel stellingen. Als u de intenties en de weer gave herstelt, kunt u deze kaart voor problemen blijven bekijken.

In het volgende diagram ziet u een app met een goed gebalanceerde toepassing met bijna geen problemen.

![In het volgende diagram ziet u een app met een goed gebalanceerde toepassing met bijna geen problemen.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

In het volgende diagram ziet u een slecht gebalanceerde app met veel problemen die moeten worden opgelost.

![Scherm opname toont de voor spellingen per intentie met verschillende onduidelijke of onjuist voorspelde resultaten.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Beweeg de muis aanwijzer over elke intentie balk om informatie over de bedoeling op te halen.

![Scherm opname toont de voor spellingen per intentie met details van onduidelijke of onjuist voorspelde resultaten.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Gebruik de functie **sorteren op** om de intenties te rangschikken op basis van het probleem type, zodat u zich kunt concentreren op de meest problematische doel stellingen van het probleem.

## <a name="intents-with-errors"></a>Intenties met fouten

Met deze kaart kunt u problemen voor een specifiek doel bekijken. De standaard weergave van deze kaart is de meest problematische intentie, zodat u weet waar u uw inspanningen kunt richten.

![Met de kaart intentie met fouten kunt u problemen voor een specifiek doel bekijken. De kaart wordt standaard gefilterd op de meest problematische doel stellingen, zodat u weet waar u uw inspanningen kunt richten.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

In het bovenste cirkel diagram ziet u de problemen met de bedoeling van de drie probleem typen. Als er problemen zijn met de drie probleem typen, heeft elk type hieronder een eigen grafiek, samen met alle persoonlijke intenties.

### <a name="filter-intents-by-issue-and-percentage"></a>Intenties filteren op uitgifte en percentage

In deze sectie van de kaart kunt u voorbeeld uitingen vinden die buiten uw fout drempel vallen. IDEA liter wilt u de voor spellingen corrigeren. Dit percentage is zakelijk en door de klant gestuurd.

Bepaal de drempel percentages waarmee u vertrouwd bent voor uw bedrijf.

Met het filter kunt u intenties met een specifiek probleem zoeken:

|Filter|Voorgesteld percentage|Doel|
|--|--|--|
|Meest problematische doel stellingen|-|**Begin hier** : als u de uitingen in deze intentie herstelt, worden de apps meer dan andere oplossingen verbeterd.|
|Corrigeer de voor spellingen hieronder|60%|Dit is het percentage uitingen in de geselecteerde intentie die juist zijn, maar waarvoor een betrouw baarheid is ingesteld onder de drempel waarde. |
|Onduidelijke voor spellingen hierboven|15%|Dit is het percentage van de uitingen in de geselecteerde intentie die worden verward met het dichtstbijzijnde gegelijke doel.|
|Onjuiste voor spellingen hierboven|15%|Dit is het percentage uitingen in de geselecteerde intentie dat onjuist wordt voor speld. |

### <a name="correct-prediction-threshold"></a>Drempel waarde voor voor spellingen corrigeren

Wat is een vertrouw bare Voorspellings score voor u? Aan het begin van de ontwikkeling van apps kan 60% uw doel zijn. Gebruik de **onderstaande juiste voor spellingen** met het percentage van 60% om te zoeken naar uitingen in de geselecteerde intentie die moeten worden opgelost.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Onduidelijke of onjuiste Voorspellings drempel

Deze twee filters bieden u de mogelijkheid om uitingen te vinden in de geselecteerde intentie buiten de drempel waarde. U kunt deze twee percentages beschouwen als fout percentages. Als u vertrouwd bent met een 10-15%-fout frequentie voor voor spellingen, stelt u de drempel waarde voor het filter in op 15% om alle uitingen boven deze waarde te vinden.

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure-resources beheren](luis-how-to-azure-subscription.md)
