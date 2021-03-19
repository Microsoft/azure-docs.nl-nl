---
title: 'Afwijkings detectie PCA-Based: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module PCA-Based anomalie detectie voor het maken van een anomalie detectie model op basis van Principal component Analysis (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898464"
---
# <a name="pca-based-anomaly-detection-module"></a>Module voor afwijkings detectie PCA-Based

In dit artikel wordt beschreven hoe u de module PCA-Based anomalie detectie in Azure Machine Learning Designer gebruikt om een anomalie detectie model te maken op basis van Principal component Analysis (PCA).

Deze module helpt u bij het bouwen van een model in scenario's waarin het eenvoudig is om trainings gegevens uit een klasse op te halen, zoals geldige trans acties, maar moeilijk voldoende voor beelden van de doel afwijkingen te verkrijgen. 

Als u bijvoorbeeld frauduleuze trans acties wilt detecteren, hebt u vaak niet voldoende voor beelden van fraude voor het trainen van. Maar mogelijk hebt u veel voor beelden van goede trans acties. De module PCA-Based anomalie detectie lost het probleem op door de beschik bare functies te analyseren om te bepalen wat een ' normale ' klasse vormt. De module past vervolgens afstands waarden toe om aanvragen te identificeren die afwijkingen vertegenwoordigen. Met deze benadering kunt u een model trainen door bestaande, onevenwichtige gegevens te gebruiken.

## <a name="more-about-principal-component-analysis"></a>Meer informatie over het analyseren van hoofd onderdelen

PCA is een vastgelegde techniek in machine learning. Het wordt vaak gebruikt in de verkennende analyse van gegevens omdat hiermee de binnenste structuur van de gegevens wordt onthuld en de variantie in de gegevens wordt uitgelegd.

PCA werkt met het analyseren van gegevens die meerdere variabelen bevatten. Er wordt gezocht naar correlaties tussen de variabelen en bepaalt de combi natie van waarden die de beste verschillen in resultaten vastleggen. Deze gecombineerde functie waarden worden gebruikt voor het maken van een compactere functie ruimte die de *belangrijkste onderdelen* wordt genoemd.

Voor een afwijkings detectie wordt elke nieuwe invoer geanalyseerd. Het algoritme voor anomalie detectie berekent de projectie op de eigenvectors, samen met een genormaliseerde reconstructie fout. De genormaliseerde fout wordt gebruikt als afwijkings Score. Hoe hoger de fout, hoe meer afwijkend het exemplaar is.

Raadpleeg de volgende artikelen voor meer informatie over hoe PCA werkt en over de implementatie van anomalie detectie:

- [Een wille keurig algoritme voor het analyseren van Principal-onderdelen](https://arxiv.org/abs/0809.2274), door Rokhlin, Szlan en Tygert

- [Structuur zoeken met wille keurigheid: Probabilistic-algoritmen voor het samen stellen van geschatte matrix ontledingen](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF-down load), door Halko, Martinsson en Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>PCA-Based anomalie detectie configureren

1. Voeg de module voor **afwijkings detectie op basis van PCA** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **anomalie detectie** .

2. Selecteer de optie **trainings modus** in het rechterdeel venster van de module. Geef aan of u het model wilt trainen met behulp van een specifieke set para meters of gebruik een parameter opruiming om de beste para meters te vinden.

    Als u weet hoe u het model wilt configureren, selecteert u de optie voor **één para meter** en geeft u een specifieke set waarden als argumenten op.

3. Voor het **aantal onderdelen dat in PCA moet worden gebruikt**, geeft u het aantal uitvoer functies of-onderdelen op dat u wilt gebruiken.

    Het besluit hoeveel onderdelen moeten worden opgenomen, is een belang rijk onderdeel van het ontwerp van experimenten dat gebruikmaakt van PCA. Algemene richt lijnen zijn dat u niet hetzelfde aantal PCA-onderdelen moet gebruiken als er variabelen zijn. In plaats daarvan moet u beginnen met een kleiner aantal onderdelen en deze verg Roten totdat aan een bepaald criterium wordt voldaan.

    De beste resultaten worden verkregen wanneer het aantal uitvoer onderdelen kleiner is *dan* het aantal functie kolommen dat beschikbaar is in de gegevensset.

4. Geef op hoeveel oversteek proeven moeten worden uitgevoerd tijdens wille keurige PCA-trainingen. In het geval van anomalie detectie problemen is het moeilijk om standaard PCA-technieken toe te passen. Als u een aantal oversteek proeven opgeeft, kunt u het aantal doel instanties verhogen.

    Als u **1** opgeeft, wordt er geen oversteek proef uitgevoerd. Als u een waarde opgeeft die hoger is dan **1**, worden extra voor beelden gegenereerd voor het maken van het model.

    Er zijn twee opties, afhankelijk van of u een para meter-sweep gebruikt of niet:

    - **Oversampling para meter voor wille keurige PCA**: Typ een enkel geheel getal dat de verhouding van de oversteek proef van de minderheids klasse op de normale klasse vertegenwoordigt. (Deze optie is beschikbaar wanneer u de trainings methode met **één para meter** gebruikt.)

    > [!NOTE]
    > U kunt de oversteeke gegevensset niet weer geven. Zie [technische opmerkingen](#technical-notes)voor meer informatie over het gebruik van oversteek proeven met PCA.

5. Selecteer de optie **gemiddelde normalisatie van invoer functie inschakelen** om alle invoer functies te normaliseren tot een gemiddelde van nul. Normalisatie of schalen naar nul wordt meestal aanbevolen voor PCA, omdat het doel van de PCA is om de variantie van variabelen te maximaliseren.

    Deze optie is standaard ingeschakeld. Schakel deze optie uit als waarden al zijn genormaliseerd via een andere methode of schaal.

6. Verbind een gelabelde trainings gegevensset en een van de trainings modules.

   Als u de optie **trainers modus maken** instelt op **één para meter**, gebruikt u de module [anomalie detectie model](train-anomaly-detection-model.md) .

7. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Wanneer de training is voltooid, kunt u het getrainde model opslaan. U kunt ook verbinding maken met de module [score model](score-model.md) om afwijkende scores te voors pellen.

De resultaten van een model voor anomalie detectie evalueren:

1. Zorg ervoor dat er een score kolom beschikbaar is in beide gegevens sets.

    Als u probeert een model voor anomalie detectie te evalueren en het fout bericht ' er is geen score kolom aanwezig in de Score van de gescoorde gegevensset ' te vergelijken, gebruikt u een typische evaluatie gegevensset die een kolom Label bevat, maar geen waarschijnlijkheids scores. Kies een gegevensset die overeenkomt met de schema-uitvoer voor afwijkings detectie modellen, waaronder **gescoorde labels** en kolommen met **gescoorde kansen** .

2. Zorg ervoor dat label kolommen zijn gemarkeerd.

    Soms worden de meta gegevens die zijn gekoppeld aan de kolom Label, verwijderd in de pipeline-grafiek. Als dit het geval is, kunt u, wanneer u de module [Evaluate model](evaluate-model.md) gebruiken om de resultaten van twee anomalie detectie modellen te vergelijken, de fout melding krijgen dat er geen label kolom is in de Score van de gescoorde gegevensset. Het is ook mogelijk dat er een fout melding wordt weer geven dat er geen label kolom is in de Score van de gescoorde gegevensset om te vergelijken.

    U kunt deze fouten vermijden door de module [meta gegevens bewerken](edit-metadata.md) voor de module [Evaluate model](evaluate-model.md) toe te voegen. Gebruik de kolom kiezer om de kolom klasse te kiezen en selecteer **Label** in de lijst **velden** .

3. Gebruik de [script module python uitvoeren](execute-python-script.md) om Label kolom Categorieën aan te passen als **1 (positief, normaal)** en **0 (negatief, abnormaal)**.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technische opmerkingen

Dit algoritme maakt gebruik van PCA om de subruimte die de klasse Normal bevat te benaderen. De subruimte wordt omspannen door eigenvectors die zijn gekoppeld aan de bovenste eigenvalues van de matrix voor gegevens COVARIANTIE. 

Voor elke nieuwe invoer berekent de afwijkende detector eerst de projectie op de eigenvectors en berekent de genormaliseerde reconstructie fout. Deze fout is de afwijkende Score. Hoe hoger de fout, hoe afwijkende het exemplaar. Zie voor meer informatie over hoe de normale ruimte wordt berekend Wikipedia: [Principal component Analysis](https://wikipedia.org/wiki/Principal_component_analysis). 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [uitzonde ringen en fout codes voor de ontwerp functie](designer-error-codes.md) voor een lijst met fouten die specifiek zijn voor de ontwerp modules.