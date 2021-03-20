---
title: Terminologie-persoonlijker
description: Personaler maakt gebruik van terminologie van versterking van het onderwijs. Deze voor waarden worden gebruikt in de Azure Portal en de Api's.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: cd0d85be5447aad0f2a3c37041e7d5d5d047a468
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777289"
---
# <a name="personalizer-terminology"></a>Aangepaste terminologie

Personaler maakt gebruik van terminologie van versterking van het onderwijs. Deze voor waarden worden gebruikt in de Azure Portal en de Api's.

## <a name="conceptual-terminology"></a>Conceptuele terminologie

* **Learning-lus**: u maakt een persoonlijke resource, een _Learning-lus_, voor elk deel van uw toepassing dat kan profiteren van personalisatie. Als u meer dan één ervaring hebt om persoonlijker te maken, maakt u een lus voor elke.

* **Model**: een personalr model legt alle gegevens vast die zijn geleerd over het gedrag van gebruikers, en trainings gegevens ophalen uit de combi natie van de argumenten die u verzendt voor het classificeren en beloningen en met een opleidings gedrag dat door het leer beleid wordt bepaald.

* **Online modus**: het standaard [leer gedrag](#learning-behavior) voor persoonlijke instellingen van uw leer proces, gebruikt machine learning om het model te bouwen waarmee de **meest voorkomende actie** voor uw inhoud wordt voor speld.

* **Leerling-modus**: een [leer gedrag](#learning-behavior) waarmee u een personaler model kunt warmen om te trainen zonder dat dit van invloed is op de resultaten en acties van de toepassing.

## <a name="learning-behavior"></a>Leer gedrag:

* **Online modus**: de beste actie te retour neren. Uw model reageert op rang gesprekken met de beste actie en zal belonings gesprekken gebruiken om de selecties in de loop van de tijd te ontdekken en te verbeteren.
* De **[leerling-modus](concept-apprentice-mode.md)**: leer als leerlingen. Uw model leert u door het gedrag van uw bestaande systeem te observeren. Rang gesprekken retour neren altijd de **standaard actie** (basis lijn) van de toepassing.

## <a name="personalizer-configuration"></a>Aangepaste configuratie

Personaler is geconfigureerd vanuit het [Azure Portal](https://portal.azure.com).

* **Beloningen**: Configureer de standaard waarden voor de berekenings tijd, de standaard beloning en het aggregatie beleid voor beloningen.

* **Verkennen**: het percentage van de rangings aanvragen configureren dat voor het verkennen moet worden gebruikt

* **Frequentie van model updates**: hoe vaak het model opnieuw wordt getraind.

* **Bewaren van gegevens**: het aantal dagen aan gegevens dat moet worden opgeslagen. Dit kan invloed hebben op offline-evaluaties, die worden gebruikt voor het verbeteren van uw leer proces.

## <a name="use-rank-and-reward-apis"></a>Rang en beloning-Api's gebruiken

* **Positie**: Bekijk de acties met functies en de context functies en gebruik verkennen of misbruik om de bovenste actie (inhouds item) te retour neren.

    * **Acties**: acties zijn de inhouds items, zoals producten of promoties, waaruit u kunt kiezen. Personaler kiest de hoogste actie (geretourneerde actie-ID) die wordt weer gegeven aan uw gebruikers via de classificatie-API.

    * **Context**: Geef informatie op over uw context om een nauw keurigere positie te geven, bijvoorbeeld:
        * Uw gebruiker.
        * Het apparaat waarop deze zich bevinden.
        * De huidige tijd.
        * Andere gegevens over de huidige situatie.
        * Historische gegevens over de gebruiker of context.

        Uw specifieke toepassing heeft mogelijk andere context informatie.

    * **[Functies](concepts-features.md)**: een eenheid van informatie over een inhouds item of een gebruikers context. Zorg ervoor dat u alleen gebruikmaakt van functies die zijn samengevoegd. Gebruik geen specifieke tijden, gebruikers-Id's of andere niet-geaggregeerde gegevens als onderdelen.

        * Een _actie functie_ is meta gegevens over de inhoud.
        * Een _context functie_ is meta gegevens over de context waarin de inhoud wordt weer gegeven.

* **Exploratie**: de personaler-service wordt gebruikt wanneer, in plaats van de beste actie te retour neren, een andere actie voor de gebruiker wordt gekozen. De Personaler-service vermijdt drift, stagnation en kan worden aangepast aan het doorlopende gedrag van gebruikers door te verkennen.

* **Misbruik**: de personaler-service gebruikt het huidige model om de beste actie te bepalen op basis van gegevens in het verleden.

* **Duur** van het experiment: de hoeveelheid tijd die de personaler-service wacht op een beloning, vanaf het moment dat de positie oproep voor die gebeurtenis is opgetreden.

* **Inactieve gebeurtenissen**: een inactieve gebeurtenis is de locatie waar u positie noemt, maar u weet niet zeker dat de gebruiker het resultaat ooit zal zien vanwege beslissingen van de client toepassing. Inactieve gebeurtenissen bieden u de mogelijkheid om persoonlijke resultaten te maken en op te slaan. vervolgens besluit u deze later te negeren zonder dat dit van invloed is op het machine learning model.


* **Beloning**: een meting van de manier waarop de gebruiker heeft gereageerd op de geretourneerde actie-id van de absolute API, als een score tussen 0 en 1. De waarde van 0 tot 1 wordt ingesteld door uw bedrijfs logica, op basis van de manier waarop de keuze heeft geholpen bij het bereiken van uw zakelijke doel stellingen voor persoonlijke instellingen. De learning-lus slaat deze beloning niet op als afzonderlijke gebruikers geschiedenis.

## <a name="evaluations"></a>Evaluaties

### <a name="offline-evaluations"></a>Offline evaluaties

* **Evaluatie**: een offline-evaluatie bepaalt het beste leer beleid voor uw lus op basis van de gegevens van uw toepassing.

* **Trainings beleid**: hoe personaler een model voor elke gebeurtenis moet treinen, wordt bepaald door sommige para meters die van invloed zijn op de werking van het machine learning algoritme. Er wordt een nieuw leer proces gestart met een standaard **learningbeleid**, waarmee de prestaties kunnen worden vertraagd. Bij het uitvoeren van [evaluaties](concepts-offline-evaluation.md)maakt personaler een nieuw leer beleid dat speciaal is geoptimaliseerd voor de gebruiks voorbeelden van uw lus. Personaler presteert aanzienlijk beter met beleids regels die zijn geoptimaliseerd voor elke specifieke lus, die tijdens de evaluatie wordt gegenereerd. Het leer beleid heet de _leer instellingen_ voor de **model-en leer instellingen** voor de personaler-resource in de Azure Portal.

### <a name="apprentice-mode-evaluations"></a>Evaluaties van de leerlingen-modus

De leerling-modus biedt de volgende **metrische gegevens** over de evaluatie:
* **Basis lijn**: gemiddelde beloning: gemiddelde beloningen van de standaard waarde van de toepassing (basis lijn).
* **Persoonlijkere – gemiddelde beloning**: gemiddelde van het totale aantal beloningen personaler zou mogelijk zijn bereikt.
* **Gemiddelde algemene compensatie**: verhouding van de prijs van de basis lijn en Personaliteit, genormaliseerd via de meest recente 1000-gebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [ethiek en het verantwoordelijke gebruik](ethics-responsible-use.md)