---
title: 'ML Studio (klassiek): Quickstart: Een gegevenswetenschappelijk experiment opzetten - Azure'
description: Deze quickstart over machine learning leidt u door een eenvoudig gegevenswetenschappelijk experiment. We voorspellen de prijs van een auto met behulp van een regressiealgoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 81842fb3a9a68e818a3e8c94aa6db690cb1d94f5
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95486276"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-machine-learning-studio-classic"></a>Quickstart: Uw eerste gegevenswetenschappelijke experiment maken in Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

In deze snelstart maakt u een Machine Learning-experiment in [Azure Machine Learning Studio (klassiek)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) om de prijs van een auto te voorspellen op basis van verschillende variabelen, zoals het merk en de technische specificaties.

Als u nog geen ervaring hebt met machine learning, is de videoserie [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) een geweldige inleiding tot machine learning, in gewone taal en met bekende begrippen.

In deze quickstart wordt de standaardwerkstroom voor een experiment gevolgd:

1. **Een model maken**
    - [De gegevens ophalen]
    - [De gegevens voorbereiden]
    - [Functies definiëren]
1. **Het model trainen**
    - [Een algoritme kiezen en toepassen]
1. **Het model beoordelen en testen**
    - [Prijzen van nieuwe auto's voorspellen]

[De gegevens ophalen]: #get-the-data
[De gegevens voorbereiden]: #prepare-the-data
[Functies definiëren]: #define-features
[Een algoritme kiezen en toepassen]: #choose-and-apply-an-algorithm
[Prijzen van nieuwe auto's voorspellen]: #predict-new-automobile-prices

## <a name="get-the-data"></a>De gegevens ophalen

Het eerste wat u voor machine learning nodig hebt, zijn gegevens.
Studio (klassiek) bevat een aantal voorbeeldgegevenssets die u kunt gebruiken. Daarnaast kunt u uit tal van bronnen gegevens importeren. Voor dit voorbeeld gebruiken we de voorbeeldgegevensset **Automobile price data (Raw)** . Deze is opgenomen in de werkruimte.
Deze gegevensset bevat vermeldingen voor verschillende auto's, inclusief informatie over het merk, het model, de technische specificaties en de prijs.

> [!TIP]
> U vindt een werkende kopie van het volgende experiment in de [Azure AI Gallery](https://gallery.azure.ai). Ga naar **[Your first data science experiment - Automobile price prediction](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** en klik op **Open in Studio** om een kopie van het experiment naar uw Machine Learning Studio-werkruimte (klassiek) te downloaden.

U voegt de gegevensset als volgt toe aan uw experiment.

1. Maak een nieuw experiment door onderaan het venster Machine Learning Studio (klassiek) op **+NEW** te klikken. Selecteer **EXPERIMENT** >  **Blank Experiment**.

1. Het experiment krijgt een standaardnaam die boven aan het canvas wordt weergegeven. Selecteer deze tekst en wijzig de naam in iets relevants, bijvoorbeeld **prijzen auto's voorspellen**. De naam hoeft niet uniek te zijn.

    ![De naam van het experiment wijzigen](./media/create-experiment/rename-experiment.png)

1. Aan de linkerkant van het experimentcanvas bevindt zich een palet met gegevenssets en modules. Typ in het zoekvak boven aan dit palet **automobile** om de gegevensset met het label **Automobile price data (Raw)** te zoeken. Sleep deze gegevensset naar het experimentcanvas.

    ![Zoek de 'automobile'-gegevensset en sleep deze naar het experimentcanvas](./media/create-experiment/type-automobile.png)

Als u wilt zien hoe deze gegevens eruitzien, klikt u op de uitvoerpoort onderaan de 'automobile'-gegevensset en selecteert u **Visualize**.

![Op de uitvoerpoort klikken en Visualize selecteren](./media/create-experiment/select-visualize.png)

> [!TIP]
> De invoer- en uitvoerpoorten van gegevenssets en modules worden aangeduid met kleine cirkels, waarbij de invoerpoorten zich boven en de uitvoerpoorten zich onder bevinden.
Als u een gegevensstroom door uw experiment wilt maken, verbindt u een uitvoerpoort van één module met een invoerpoort van een andere.
U kunt op elk gewenst moment op de uitvoerpoort van een gegevensset of module klikken om te zien hoe de gegevens eruitzien op dat punt in de gegevensstroom.

In deze gegevensset wordt elke auto weergegeven als een rij. De variabelen die aan elke auto zijn gekoppeld, worden weergegeven als kolommen. Aan de hand van de variabelen voor een specifieke auto wordt de prijs voorspeld in de kolom price, uiterst rechts (kolom 26).

![De autogegevens weergeven in het venster voor gegevensvisualisatie](./media/create-experiment/visualize-auto-data.png)

Sluit het visualisatievenster door op de **x** in de rechterbovenhoek te klikken.

## <a name="prepare-the-data"></a>De gegevens voorbereiden

Normaal gesproken moet een gegevensset worden voorverwerkt voordat deze kan worden geanalyseerd. Het is u mogelijk opgevallen dat er in verschillende rijen ontbreken in de kolommen van verschillende rijen. Deze ontbrekende waarden moeten worden opgeschoond, zodat de gegevens correct kunnen worden geanalyseerd. We verwijderen de rijen met ontbrekende waarden. Ook in de kolom **normalized-losses** ontbreekt een groot deel van de waarden. Deze kolom zal daarom helemaal worden uitgesloten van het model.

> [!TIP]
> Voor de meeste modules geldt dat voor het gebruik van de module de ontbrekende invoergegevens moeten worden opgeschoond.

Eerst voegen we een module toe waarmee de kolom **normalized-losses** volledig wordt verwijderd. Vervolgens voegen we een andere module toe waarmee alle rijen met ontbrekende gegevens worden verwijderd.

1. Typ **select columns** in het zoekvak bovenaan het modulepalet om de module [Select Columns in Dataset][select-columns] te vinden. Sleep deze vervolgens naar het experimentcanvas. Met deze module kunt u selecteren welke kolommen met gegevens u wilt opnemen in of uitsluiten voor het model.

1. Koppel de uitvoerpoort van de gegevensset **Automobile price data (Raw)** aan de invoerpoort van de module Select Columns in Dataset.

    ![De module Select Columns in Dataset toevoegen aan het experimentcanvas en hiermee verbinding maken](./media/create-experiment/type-select-columns.png)

1. Klik op de module [Select Columns in Dataset][select-columns] en klik in het deelvenster **Properties** op **Launch column selector**.

   - Klik links op **With rules**
   - Klik onder **Begin With** op **All columns**. Deze regels zorgen ervoor dat [Select Columns in Dataset][select-columns] alle kolommen doorgeeft (met uitzondering van de kolommen die we dadelijk zullen uitsluiten).
   - Selecteer in de vervolgkeuzelijsten **Exclude** en **column names** en klik in het tekstvak. Er wordt een lijst met kolommen weergegeven. Selecteer **normalized-losses** om dit aan het tekstvak toe te voegen.
   - Klik rechtsonder op de knop met het vinkje (OK) om de kolomkiezer te sluiten.

     ![De kolomkiezer starten en de kolom normalized-losses uitsluiten](./media/create-experiment/launch-column-selector.png)

     Het deelvenster met eigenschappen van de module **Select Columns in Dataset** geeft nu aan dat alle kolommen uit de gegevensset worden doorgegeven, met uitzondering van **normalized-losses**.

     ![Het deelvenster met eigenschappen geeft aan dat de kolom normalized-losses is uitgesloten](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > U kunt een opmerking aan een module toevoegen door te dubbelklikken op de module en tekst in te voeren. Zodoende kunt u in één oogopslag zien wat de module in uw experiment doet. Dubbelklik in dit geval op de module [Select Columns in Dataset][select-columns] en typ de opmerking 'normalized-losses uitsluiten'.

     ![Op een module dubbelklikken om een opmerking toe te voegen](./media/create-experiment/add-comment.png)

1. Sleep de module [Clean Missing Data][clean-missing-data] naar het canvas en verbindt deze met de module [Select Columns in Dataset][select-columns]. Selecteer in het deelvenster **Properties** de optie **Remove entire row** onder **Cleaning mode**. Deze opties zorgen ervoor dat de module [Clean Missing Data][clean-missing-data] de gegevens opschoont door rijen met ontbrekende waarden te verwijderen. Dubbelklik op de module en typ de opmerking 'Rijen met ontbrekende waarde verwijderen'.

    ![De opschoonmodus van de module Clean Missing Data instellen op Remove entire row](./media/create-experiment/set-remove-entire-row.png)

1. Voer het experiment uit door onder aan de pagina op **RUN** te klikken.

    Wanneer het experiment is voltooid, wordt er bij alle modules een groen vinkje weergegeven om aan te geven dat deze zijn voltooid. In de rechterbovenhoek wordt de status **Finished running** weergegeven.

    ![Zo ziet het experiment er ongeveer uit nadat het is uitgevoerd](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Waarom voeren we het experiment nu uit? Door het experiment uit te voeren, worden de kolomdefinities voor onze gegevens van de gegevensset doorgegeven via de module [Select Columns in Dataset][select-columns] en via de module [Clean Missing Data][clean-missing-data]. Hierdoor beschikken alle modules die u koppelt aan [Clean Missing Data][clean-missing-data] over dezelfde informatie.

Nu hebben we opgeschoonde gegevens. Als u de opgeschoonde gegevensset wilt weergeven, klikt u op de uitvoerpoort links van de module [Clean Missing Data][clean-missing-data] en selecteert u **Visualize**. Zoals u kunt zien, is de kolom **normalized-losses** verwijderd en ontbreken er geen waarden meer.

Nu de gegevens zijn opgeschoond, kunt u opgeven welke functies u wilt gebruiken in het voorspellende model.

## <a name="define-features"></a>Functies definiëren

In machine learning zijn *functies* afzonderlijke meetbare eigenschappen van iets waarin u geïnteresseerd bent. In onze gegevensset staat elke rij voor één auto en elke kolom bevat een kenmerk van die auto.

Voor een goede set kenmerken voor het maken van een voorspellend model, moet u experimenteren en beschikken over kennis van het probleem dat u wilt oplossen. Bepaalde kenmerken zijn beter voor het voorspellen van het doel dan andere. Sommige kenmerken hebben een nauwe correlatie met andere kenmerken en kunnen worden verwijderd. City-mpg en highway-mpg lijken bijvoorbeeld sterk op elkaar, waardoor we een van de twee kunnen verwijderen zonder de voorspelling al te veel te beïnvloeden.

Laten we een model bouwen dat gebruikmaakt van een subset kenmerken onze gegevensset. U kunt later terugkeren en andere kenmerken selecteren om het experiment vervolgens opnieuw uit te voeren en te zien of u betere resultaten krijgt. Maar probeer om te beginnen de volgende functies:

> make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Sleep nog een module [Select Columns in Dataset][select-columns] naar het experimentcanvas. Koppel de linkeruitvoerpoort van de module [Clean Missing Data][clean-missing-data] aan de invoerpoort van de module [Select Columns in Dataset][select-columns].

    ![De module Select Columns in Dataset koppelen aan de module Clean Missing Data](./media/create-experiment/connect-clean-to-select.png)

1. Dubbelklik op de module en typ 'Select features for prediction' (Kenmerken voor de voorspelling selecteren).

1. Klik in het deelvenster **Properties** op **Launch column selector**.

1. Klik op **With rules**.

1. Klik onder **Begin With** op **No columns**. Selecteer in de filterrij **Include** en **column names** en selecteer onze lijst met kolomnamen in het tekstvak. Dit filter zorgt ervoor dat de module alleen de kolommen (kenmerken) doorgeeft die wij opgeven.

1. Klik op de knop met het vinkje (OK).

    ![De kolommen (kenmerken) selecteren die u wilt opnemen in de voorspelling](./media/create-experiment/select-columns-to-include.png)

Deze module produceert een gefilterde gegevensset met alleen de kenmerken die we willen doorgeven aan het learning-algoritme dat we in de volgende stap gebruiken. U kunt later terugkeren en het opnieuw proberen met een andere selectie kenmerken.

## <a name="choose-and-apply-an-algorithm"></a>Een algoritme kiezen en toepassen

Nu de gegevens klaar zijn, kunt u een voorspellend model bouwen door het model te trainen en te testen. We gebruiken onze gegevens om het model te trainen. Vervolgens testen we het model om te controleren in hoeverre de prijzen met dit model kunnen worden voorspeld.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Classificatie* en *regressie* zijn twee soorten beheerde machine learning-algoritmen. Classificatie voorspelt een antwoord uit een gedefinieerde set categorieën, zoals een kleur (rood, blauw of groen). Regressie wordt gebruikt om een getal te voorspellen.

Omdat we de prijs willen voorspellen, wat een getal is, gebruiken we een regressiealgoritme. In dit voorbeeld gebruiken we een *lineair regressiemodel*.


We trainen het model met behulp van een gegevensset die de prijs bevat. Het model scant de gegevens en zoekt naar correlaties tussen de kenmerken van een auto en de prijs. Vervolgens gaan we het model testen. We voeren een aantal kenmerken in van auto's waarvan we de prijs kennen en kijken hoe dicht de voorspellingen van het model bij de bekende prijzen komen.

We gebruiken onze gegevens zowel voor trainings- als testdoeleinden door ze op te splitsen in afzonderlijke trainings- en testsets.

1. Selecteer de module [Split Data][split], sleep deze naar het experimentcanvas en koppel de module aan de laatste module [Select Columns in Dataset][select-columns].

1. Klik op de module [Split Data][split] om deze te selecteren. Stel **Fraction of rows in the first output dataset** in het deelvenster **Properties** (rechts van het canvas) in op 0,75. Zodoende gebruiken we 75 procent van de gegevens om het model te trainen en gebruiken we 25 procent van de gegevens om het model te testen.

    ![De splitsfractie van de module Split Data in instellen op 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Door de parameter **Random seed** te wijzigen, kunt u verschillende willekeurig samples voor trainings- en testdoeleinden gebruiken. Deze parameter bepaalt de seeding van de pseudo-willekeurige nummergenerator.

1. Voer het experiment uit. De modules [Select Columns in Dataset][select-columns] en [Split Data][split] geven daarop kolomdefinities door aan de modules die we hierna zullen toevoegen.  

1. Als u een leeralgoritme wilt selecteren, moet u de categorie **Machine Learning** in het modulepalet links van het canvas uitvouwen en vouwt u vervolgens **Initialize Model** uit. Er worden verschillende categorieën weergegeven die kunnen worden gebruikt om de machine learning-algoritmen te initialiseren. Selecteer voor dit experiment de module [Linear Regression][linear-regression] in de categorie **Regression** en sleep de module naar het experimentcanvas. (U kunt de module ook zoeken door 'linear regression' in het zoekvak van het palet te typen.)

1. Zoek de module [Train Model][train-model] en sleep deze naar het experimentcanvas. Koppel de uitvoer van de module [Linear Regression][linear-regression] aan de linkerinvoer van de module [Train model][train-model] en koppel de trainingsgegevensuitvoer (linkerpoort) van de module [Split Data][split] aan de rechterinvoer van de module [Train Model][train-model].

    ![De module Train Model verbinden met de modules Linear Regression en Split Data](./media/create-experiment/connect-train-model.png)

1. Klik op de module [Train Model][train-model], klik in het deelvenster **Properties** op **Launch column selector** en selecteer vervolgens de kolom **price**. **Price** is de waarde die door het model wordt voorspeld.

    U selecteert de kolom **prijs** in de kolomkiezer door deze vanuit de lijst **Available columns** te verplaatsen naar de lijst **Selected columns**.

    ![Selecteer de kolom price voor de module Train Model](./media/create-experiment/select-price-column.png)

1. Voer het experiment uit.

Nu beschikken we over een getraind regressiemodel dat kan worden gebruikt om nieuwe autogegevens te beoordelen voor het maken van prijsvoorspellingen.

![Zo ziet het experiment er ongeveer uit nadat het is uitgevoerd](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Prijzen van nieuwe auto's voorspellen

Nu we het model met 75 procent van de gegevens hebben getraind, kunnen we het model gebruiken om de overige 25 procent van onze gegevens te beoordelen om te zien hoe goed het model werkt.

1. Zoek de module [Score Model][score-model] en sleep deze naar het experimentcanvas. Koppel de uitvoer van de module [Train Model][train-model] aan de linkerinvoerpoort van de module [Score Model][score-model]. Koppel de testgegevensuitvoer (rechterpoort) van de module [Split Data][split] aan de rechterinvoerpoort van de module [Score Model][score-model].

    ![De module Score Model verbinden met de modules Train Model en Split Data](./media/create-experiment/connect-score-model.png)

1. Voer het experiment uit en geef de uitvoer van de module [Score Model][score-model]. Hiervoor klikt u op de uitvoerpoort van [Score Model][score-model] en selecteert u **Visualize**. De uitvoer bevat de voorspelde waarden voor de prijs en de bekende waarden uit de testgegevens.  

    ![Uitvoer van de module Score Model](./media/create-experiment/score-model-output.png)

1. Ten slotte testen we de kwaliteit van de resultaten. Selecteer de module [Evaluate Model][evaluate-model], sleep deze naar het experimentcanvas en koppel de uitvoer van de module [Score Model][score-model] aan de linkerinvoer van de module [Evaluate Model][evaluate-model]. Het laatste experiment ziet er ongeveer als volgt uit:

    ![Laatste experiment](./media/create-experiment/complete-linear-regression-experiment.png)

1. Voer het experiment uit.

Als u de uitvoer van de module [Evaluate Model][evaluate-model] wilt weergeven, klikt u op de uitvoerpoort en selecteert u **Visualize**.

![Evaluatieresultaten van het experiment](./media/create-experiment/evaluation-results.png)

De volgende statistieken worden weergegeven voor het model:

- **Gemiddelde absolute fout** (Mean Absolute Error of MAE): Het gemiddelde aan absolute fouten (een *fout* is het verschil tussen de voorspelde waarde en de werkelijke waarde).
- **Standaardafwijking** (Root Mean Squared Error of RMSE): De vierkantswortel uit het gemiddelde aan kwadratische fouten voor de voorspellingen op basis van de testgegevensset.
- **Relatieve absolute fout**: Het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
- **Relatieve kwadratische fout**: Het gemiddelde aan kwadratische fouten ten opzichte van het kwadratische verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
- **Determinatiecoëfficiënt**: ook wel **R²-waarde** genoemd, is een statistische meetwaarde die aangeeft hoe goed het model is in het voorspellen van de gegevens.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een kleine waarde geeft aan dat de voorspelling dichter bij de werkelijke waarde ligt. Hoe dichter de **determinatiecoëfficiënt** bij één (1.0) ligt, hoe nauwkeuriger de voorspellingen.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een eenvoudig experiment uitgevoerd met behulp van een voorbeeldgegevensset. Als u het proces van het maken en implementeren van een model nader wilt verkennen, gaat u naar de zelfstudie over voorspellende oplossingen.

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellende oplossing ontwikkelen in Studio (klassiek)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model