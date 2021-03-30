---
title: Functies electie in het team data Science process
description: Hierin wordt het doel van de functie selectie beschreven en vindt u voor beelden van hun rol in het proces voor gegevens verbetering van machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321317"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Functies selecteren in Team Data Science Process (TDSP)
In dit artikel wordt het doel van de functie selectie beschreven en worden voor beelden gegeven van hun rol in het proces voor gegevens verbetering van machine learning. Deze voor beelden worden uit Azure Machine Learning Studio opgehaald.

De techniek en selectie van functies is een onderdeel van het team data Science process (TDSP), zoals beschreven in het artikel [Wat is het proces voor team data Science?](overview.md). Functie techniek en selectie zijn onderdelen van de stap **functies ontwikkelen** van de TDSP.

* **functie techniek**: dit proces probeert extra relevante functies te maken op basis van de bestaande onbewerkte functies in de gegevens en om de voorspellende stroom te verg Roten tot het leer algoritme.
* **functie selectie**: dit proces selecteert de sleutel subset van de oorspronkelijke gegevens functies in een poging om de dimensionaliteit van het trainings probleem te reduceren.

Normaal gesp roken **functie techniek** wordt eerst toegepast om extra functies te genereren, en vervolgens wordt de **functie selectie** stap uitgevoerd om irrelevante, redundante of zeer gecorreleerde functies te elimineren.

## <a name="filter-features-from-your-data---feature-selection"></a>Functies filteren vanuit de selectie van uw gegevens functie
Functie selectie kan worden gebruikt voor classificatie-of regressie taken. Het doel is om een subset van de functies te selecteren uit de oorspronkelijke gegevensset die de afmetingen reduceert door gebruik te maken van een minimale set functies om de maximale hoeveelheid afwijking in de gegevens weer te geven. Deze subset van functies wordt gebruikt om het model te trainen. Functies electie fungeert twee hoofd doelen.

* Eerst verhoogt de functie selectie vaak de classificatie nauw keurigheid door irrelevante, redundante of zeer gecorreleerde functies te elimineren.
* Ten tweede vermindert het het aantal functies, waardoor het model trainings proces efficiënter wordt. Efficiëntie is belang rijk voor informatie die kostbaar is om te trainen, zoals vector machines voor ondersteuning.

Hoewel de functie selectie het aantal functies in de gegevensset die wordt gebruikt om het model te trainen, beperkt, wordt er niet naar de term ' dimensionality reducation ' verwezen. Met methoden voor het selecteren van functies wordt een subset van de oorspronkelijke functies in de gegevens geëxtraheerd zonder dat ze worden gewijzigd.  Reductie methoden voor dimensionaliteit gebruiken gewerkte functies waarmee de oorspronkelijke functies kunnen worden getransformeerd en gewijzigd. Voor beelden van reductie methoden voor dimensionaliteit zijn Principal-onderdeel analyse, canonieke correlatie analyse en enkelvoudige waarde-ontleding.

Onder andere wordt een categorie van de functie selectie methoden in een gecontroleerde context aangeduid met een op filters gebaseerde functie selectie. Door de correlatie tussen elke functie en het doel kenmerk te evalueren, passen deze methoden een statistische meting toe om een score aan elke functie toe te wijzen. De functies worden vervolgens gerangschikt op basis van de score, die kan worden gebruikt om de drempel waarde in te stellen voor het bewaren of elimineren van een specifieke functie. Voor beelden van statistische metingen die worden gebruikt in deze methoden zijn correlatie, wederzijdse informatie en de Chi-kwadraat test.

In Azure Machine Learning Studio zijn er modules beschikbaar voor functie selectie. Zoals u kunt zien in de volgende afbeelding, bevatten deze modules op [filters gebaseerde functies electie][filter-based-feature-selection] en [Fisher lineaire discriminant analyse][fisher-linear-discriminant-analysis].

![Modules voor functie selectie](./media/select-features/feature-Selection.png)

Denk bijvoorbeeld aan het gebruik van de [functie selectie module op basis van filter][filter-based-feature-selection] . Voor het gemak gaat u verder met het voor beeld van tekst analyse. Stel dat u een regressie model wilt maken nadat een set van 256-functies zijn gemaakt via de module [hashing-functie][feature-hashing] , en dat de reactie variabele de ' Kol1 ' is die de Score van het boek beoordeling van 1 tot 5 bevat. Door ' functie Score methode ' in te stellen als ' Pearson-correlatie ', ' doel kolom ' in ' Kol1 ' en ' aantal gewenste functies ' op 50. Vervolgens geeft de [functie selectie op basis van module filter][filter-based-feature-selection] een gegevensset met 50-functies samen met het doel kenmerk ' Kol1 '. In de volgende afbeelding ziet u de stroom van dit experiment en de invoer parameters:

![Eigenschappen van module functie selectie Filter-Based](./media/select-features/feature-Selection1.png)

In de volgende afbeelding ziet u de resulterende gegevens sets:

![Resulterende gegevensset voor de functie selectie module op basis van filter](./media/select-features/feature-Selection2.png)

Elke functie wordt beoordeeld op basis van de correlatie tussen de Pearson en het doel kenmerk ' Kol1 '. De functies met de hoogste scores worden bewaard.

De bijbehorende scores van de geselecteerde functies worden weer gegeven in de volgende afbeelding:

![Scores voor de functie selectie module op basis van filter](./media/select-features/feature-Selection3.png)

Als u deze [functie voor het filteren op basis van filtering][filter-based-feature-selection] toepast, zijn er 50 van de 256-functies geselecteerd, omdat ze de meest gerelateerde functies hebben met de doel variabele Kol1, op basis van de Score methode Pearson correlatie.

## <a name="conclusion"></a>Conclusie
Functie techniek en functie selectie zijn twee vaak ontworpen en geselecteerde functies verhogen de efficiëntie van het trainings proces dat probeert de belang rijke informatie uit de gegevens op te halen. Ze verbeteren ook de kracht van deze modellen om de invoer gegevens nauw keurig te classificeren en om de resultaten betrouwbaarder te voors pellen. Functie techniek en selectie kunnen ook worden gecombineerd om het leren van de reken kracht te maken. Dit doet u door het aantal functies te verhogen en te verminderen dat nodig is voor het kalibreren of trainen van een model. Mathematische spraak: de functies die zijn geselecteerd om het model te trainen, zijn een minimale set onafhankelijke variabelen waarmee de patronen in de gegevens worden uitgelegd en die vervolgens kunnen worden voor speld.

Het is niet altijd nood zakelijk om functie-engineering of functie selectie uit te voeren. Of het nodig is of niet afhankelijk is van de verzamelde gegevens, de geselecteerde algoritme en het doel van het experiment.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis