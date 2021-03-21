---
title: Gegevens verzamelen
description: Meer informatie over de voorbeeld gegevens die moeten worden verzameld tijdens het ontwikkelen van uw app
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738081"
---
# <a name="data-collection-for-your-app"></a>Gegevens verzameling voor uw app

Een Language Understanding-app (LUIS) heeft gegevens nodig als onderdeel van het ontwikkelen van apps.

## <a name="data-used-in-luis"></a>Gegevens die worden gebruikt in LUIS

LUIS gebruikt tekst als gegevens om uw LUIS-app te trainen en te testen op classificatie voor [intenties](luis-concept-intent.md) en voor het uitpakken van [entiteiten](luis-concept-entity-types.md). U hebt een groot voldoende gegevensset nodig dat u voldoende gegevens hebt om afzonderlijke gegevens sets te maken voor zowel trainingen als tests die de verscheidenheid en distributie hebben die hieronder specifiek worden genoemd.  De gegevens in elk van deze sets mogen niet overlappen.

## <a name="training-data-selection-for-example-utterances"></a>Trainings gegevens selectie voor bijvoorbeeld uitingen

Selecteer uitingen voor uw Trainingsset op basis van de volgende criteria:

* **Echte gegevens zijn het beste**:
    * **Echte gegevens uit de client toepassing**: Selecteer uitingen die echte gegevens uit uw client toepassing zijn.  Als de klant vandaag een webformulier met hun query verzendt en u een bot bouwt, kunt u beginnen met de webformuliergegevens.
    * **Gegevens** in de bron: als u geen bestaande gegevens hebt, overweegt u de uitingen.  Probeer te uitingen van uw werkelijke gebruikers populatie voor uw scenario om de beste benadering te krijgen van de werkelijke gegevens die uw toepassing te zien krijgt. Geuitingeneerde humane-producten zijn beter dan door de computer gegenereerde uitingen.  Wanneer u een gegevensset bouwt van synthetische uitingen die op specifieke patronen worden gegenereerd, is het niet erg belang rijk dat er veel van de natuurlijke variatie wordt weer gegeven bij mensen die de uitingen maken en niet optimaal generaliseren in de productie omgeving.
* **Gegevens diversiteit**:
    * **Regio diversiteit**: Zorg ervoor dat de gegevens voor elke intentie zo divers mogelijk zijn, zoals _formule ring_ (woord keuze) en _grammatica_.  Als u een doel stelling voor HR-beleid over vakantie dagen wilt voor komen, moet u ervoor zorgen dat u uitingen hebt die de voor waarden vertegenwoordigen die worden gebruikt voor alle regio's die u wilt gebruiken.  Zo kan bijvoorbeeld in Europa vragen over en worden gevraagd `taking a holiday` naar de IT-mede werkers `taking vacation days` .
    * **Taal diversiteit**: als u gebruikers met verschillende systeem eigen talen hebt die in een tweede taal communiceren, moet u ervoor zorgen dat uitingen die niet-systeem eigen sprekers vertegenwoordigen.
    * **Invoer diversiteit**: Houd rekening met het invoer traject van uw gegevens. Als u gegevens verzamelt van een persoon, een afdeling of een invoer apparaat (microfoon), zult u waarschijnlijk de diversiteit missen die belang rijk is voor uw app om meer te weten te komen over alle invoer paden.
    * **Diversiteit van interpunctie**: Houd er rekening mee dat mensen verschillende interpunctie niveaus in tekst toepassingen gebruiken en ervoor zorgen dat u een verscheidenheid hebt over de manier waarop interpunctie wordt gebruikt. Als u gebruikmaakt van gegevens die afkomstig zijn van spraak, heeft deze geen interpunctie, waardoor uw gegevens niet mogen worden gebruikt.
* **Gegevens distributie**: Zorg ervoor dat de gegevens die over de intenties worden verspreid, overeenkomen met dezelfde sprei ding van de gegevens die door uw client toepassing worden ontvangen. Als uw LUIS-app uitingen van aanvragen voor het plannen van een verlof (50%) classificeert, ziet u ook uitingen over het zoeken naar resterende verlof dagen (20%), het goed keuren van de Blades (20%) en enige buiten bereik en Chit Chat (10%) uw gegevensset moet de voorbeeld percentages van elk type utterance hebben.
* **Alle gegevens formulieren gebruiken**: als uw Luis-app gegevens in meerdere formulieren neemt, moet u deze formulieren opnemen in uw trainings uitingen. Als uw client toepassing bijvoorbeeld zowel spraak-als getypte tekst invoer accepteert, moet u uitingen met spraak-naar-tekst gegenereerd en getypte uitingen.  U ziet verschillende variaties in hoe mensen spreken van de manier waarop ze worden getypt, evenals verschillende fouten in spraak herkenning en type fout.  Al deze variaties moeten worden weer gegeven in uw trainings gegevens.
* **Positieve en negatieve voor beelden**: als u een Luis-app wilt leren kennen, moet u weten wat de bedoeling is (positief) en wat het niet (negatief) is. In LUIS kan uitingen alleen positief zijn voor één intentie. Wanneer een utterance wordt toegevoegd aan een intentie, wordt in LUIS automatisch een negatief voor beeld voor alle andere intenties utterance.
* **Gegevens buiten toepassings bereik**: als uw toepassing uitingen ziet die buiten uw gedefinieerde intenties vallen, moet u ervoor zorgen dat deze worden verstrekt. De voor beelden die niet zijn toegewezen aan een bepaalde gedefinieerde opzet, worden aangeduid met de **geen** intentie.  Het is belang rijk om realistische voor beelden te hebben voor de **geen** intentie om uitingen te voors pellen die buiten het bereik van de gedefinieerde intenties vallen.

    Als u bijvoorbeeld een HR-bot maakt gericht op verlof tijd en u drie intenties hebt:
    * een verlof plannen of bewerken
    * informatie over beschik bare verlof dagen
    * goed keuren/disapprove verlof

    U wilt er zeker van zijn dat u uitingen hebt die van beide intenties afdekken, maar ook de potentiële uitingen buiten dat bereik bedekken die de toepassing als volgt moet gebruiken:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Zeldzame voor beelden**: uw app moet zeldzame voor beelden bevatten, evenals algemene voor beelden.  Als uw app nooit zeldzame voor beelden heeft gezien, kunnen ze deze niet identificeren in de productie omgeving. Als u echte gegevens gebruikt, kunt u nauw keuriger bepalen hoe uw LUIS-app werkt in de productie omgeving.

### <a name="quality-instead-of-quantity"></a>Kwaliteit in plaats van hoeveelheid

Houd rekening met de kwaliteit van uw bestaande gegevens voordat u meer gegevens toevoegt.  Met LUIS maakt u gebruik van machine onderwijs.  De combi natie van uw labels en de machine learning functies die u definieert, zijn wat uw LUIS-app gebruikt.  Het is niet eenvoudig om te vertrouwen op het aantal labels om de beste voor spelling te maken.  De verscheidenheid van voor beelden en hun representatie van wat uw LUIS-app te zien krijgt in productie is het belangrijkste onderdeel.

### <a name="preprocessing-data"></a>Voor verwerking van gegevens

De volgende voor verwerkings stappen helpen u bij het bouwen van een betere LUIS-app:

* **Dubbele waarden verwijderen**: dubbele uitingen zijn niet zo onwaarschijnlijk, maar ze kunnen niet helpen. Als u ze verwijdert, worden labels ook opgeslagen.
* **Hetzelfde preproces van de client-app Toep assen**: als uw client toepassing die het Luis prediction-eind punt aanroept, tijdens runtime gegevens verwerking toepast voordat de tekst naar Luis wordt verzonden, moet u de Luis-app trainen op gegevens die op dezelfde manier worden verwerkt. Als uw client toepassing bijvoorbeeld gebruikmaakt van [Bing spellingcontrole](../bing-spell-check/overview.md) voor het corrigeren van de spelling van invoer naar Luis, corrigeert u uw training en test u uitingen voordat u toevoegt aan Luis.
* **Geen nieuwe opschoon processen Toep assen die niet worden gebruikt door de client**-app: als uw client-app rechtstreeks door spraak gegenereerde tekst accepteert zonder opschoning zoals grammatica of interpunctie, moet uw uitingen overeenkomen met de ontbrekende interpunctie en eventuele andere misherkenning die u moet gebruiken.
* **Gegevens niet opschonen**: niet-gemanipuleerde invoer verwijderen die mogelijk van vervormde spraak herkenning, onbedoelde toetsaanslagen of verkeerd gespelde tekst wordt weer gegeven. Als uw app invoer zoals deze wordt weer geven, is het belang rijk dat deze op deze manier wordt getraind en getest. Voeg een _Ongeldige invoer_ intentie toe als u er niet van uitgaat dat uw app deze begrijpt. Voorzie deze gegevens van labels om uw LUIS-app te helpen bij het voors pellen van de juiste reactie tijdens runtime. Uw client toepassing kan een geschikt antwoord op onleesbare uitingen kiezen, zoals `Please try again` .

### <a name="labeling-data"></a>Labelen van gegevens

* **Tekst labelen alsof deze juist is**: het voor beeld uitingen moet alle formulieren van een entiteit met label hebben. Dit omvat tekst die foutief is gespeld, verkeerd is getypt en onjuist is vertaald.

### <a name="data-review-after-luis-app-is-in-production"></a>Gegevens beoordeling nadat de LUIS-app in productie is genomen

[Bekijk eind punt uitingen](luis-concept-review-endpoint-utterances.md) om utterance verkeer te controleren wanneer u een app hebt geïmplementeerd voor productie.  Zo kunt u uw trainings uitingen bijwerken met echte gegevens, waardoor uw app wordt verbeterd. Elke app die is gemaakt met gegevens over een Gereal of niet-echt scenario moet worden verbeterd op basis van het daad werkelijke gebruik.

## <a name="test-data-selection-for-batch-testing"></a>Test gegevens selectie voor batch tests

Alle hierboven genoemde principes voor training uitingen zijn van toepassing op uitingen die u moet gebruiken voor uw [testset](./luis-how-to-batch-test.md). Zorg ervoor dat de distributie over intenties en entiteiten de echte distributie zo nauw keurig mogelijk spiegelt.

Hergebruik uitingen niet uit uw Trainingsset in uw testset. Hierdoor worden de resultaten onjuist weer geven en krijgt u niet de juiste indicatie van hoe uw LUIS-app in productie zal worden uitgevoerd.

Zodra de eerste versie van uw app is gepubliceerd, moet u uw testset bijwerken met uitingen van echt verkeer om ervoor te zorgen dat uw testset uw productie distributie weerspiegelt en u de realistische prestaties gedurende een periode kunt bewaken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over hoe LUIS uw gegevens wijzigt vóór de voor spelling](luis-concept-data-alteration.md)