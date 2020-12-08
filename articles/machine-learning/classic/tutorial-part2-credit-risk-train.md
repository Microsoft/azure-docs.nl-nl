---
title: 'ML Studio (klassiek) zelfstudie 2: Modellen voor kredietrisico trainen - Azure'
description: Een gedetailleerde zelfstudie voor het maken van een predictive analytics-oplossing voor kredietrisicobeoordeling in Azure Machine Learning Studio (klassiek). Deze zelfstudie is deel twee van een driedelige reeks. Ze laat zien hoe u modellen moet trainen en evalueren.
keywords: kredietrisico, predictive analytics-oplossing, risico-evaluatie
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 557c624b6ed683f701b6a8d38854cd8604e05b31
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325261"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Zelfstudie 2: Modellen voor kredietrisico trainen - Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassiek)   ![dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

In deze zelfstudie wordt uitgebreid ingegaan op het ontwikkelingsproces van een predictive analytics-oplossing. U ontwikkelt een eenvoudig model in Machine Learning Studio (klassiek).  Vervolgens implementeert u het model als een Azure Machine Learning-webservice.  Dit geïmplementeerde model kan voorspellingen doen op basis van nieuwe gegevens. Deze zelfstudie is **deel twee van een driedelige reeks**.

Stel dat u iemands kredietrisico moet voorspellen op basis van de gegevens die deze persoon in een kredietaanvraag heeft ingevuld.  

Kredietrisicobeoordeling is een complex probleem, maar in deze zelfstudie wordt het enigszins vereenvoudigd. Het wordt gebruikt als voorbeeld van hoe u een predictive analytics-oplossing kunt maken met behulp van Microsoft Azure Machine Learning Studio (klassiek). U gebruikt Azure Machine Learning Studio (klassiek) en een Machine Learning-webservice om deze oplossing te maken.  

In deze driedelige zelfstudie begint u met openbaar beschikbare kredietrisicogegevens.  Vervolgens ontwikkelt en traint u een voorspellend model.  En ten slotte implementeert u het model als een webservice.

In [deel één van de zelfstudie](tutorial-part1-credit-risk.md) hebt u een Machine Learning Studio (klassiek)-werkruimte gemaakt, gegevens geüpload en een experiment gemaakt.

In dit deel van de zelfstudie gaat u het volgende doen:
 
> [!div class="checklist"]
> * Meerdere modellen trainen
> * De modellen beoordelen en evalueren


In [deel drie van de zelfstudie](tutorial-part3-credit-risk-deploy.md), implementeert u het model als een webservice.

## <a name="prerequisites"></a>Vereisten

Voltooi [deel één van de zelfstudie](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Meerdere modellen trainen

Een van de voordelen van het gebruik van Azure Machine Learning Studio (klassiek) voor het maken van machine learning-modellen is de mogelijkheid om meerdere typen modellen tegelijkertijd in één experiment te proberen en de resultaten te vergelijken. Dit type experiment helpt u de beste oplossing voor uw probleem te vinden.

In het experiment dat we ontwikkelen in deze zelfstudie, maakt u twee verschillende soorten modellen en vergelijkt vervolgens hun scoreresultaten om te beslissen welk algoritme u wilt gebruiken in ons laatste experiment.  

Er zijn verschillende modellen waaruit u kunt kiezen. Als u de beschikbare modellen wilt zien, vouwt u het **Machine Learning**-knooppunt uit in het modulepalet en vervolgens **Initialize Model** en de knooppunten eronder. Voor dit experiment selecteert u de modules [Ondersteuningsvectormachine met twee klassen][two-class-support-vector-machine] (SVM) en de module [Versterkte beslissingsstructuur met twee klassen][two-class-boosted-decision-tree].

> [!TIP]
> Raadpleeg [Algoritmen kiezen voor Microsoft Azure Machine Learning Studio (klassiek)](../how-to-select-algorithms.md) als u informatie nodig hebt om te bepalen welk Machine Learning-algoritme het beste tegemoetkomt aan het specifieke probleem dat u probeert om op te lossen.
> 
> 

U voegt zowel de module [Versterkte beslissingsstructuur met twee klassen][two-class-boosted-decision-tree] als de module [Ondersteuningsvectormachine met twee klassen][two-class-support-vector-machine] toe aan dit experiment.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree

Stel eerst het Boosted Decision Tree-model in.

1. Zoek de module [Versterkte beslissingsstructuur met twee klassen][two-class-boosted-decision-tree] in het modulepalet en sleep deze naar het canvas.

1. Zoek de module [Train Model][train-model], sleep deze naar het canvas en sluit vervolgens de uitvoer van de module [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] aan op de linkerinvoerpoort van de module [Train Model][train-model].
   
   De module [Versterkte beslissingsstructuur met twee klassen][two-class-boosted-decision-tree] initialiseert het algemene model en [Trainingsmodel][train-model] gebruikt trainingsgegevens voor het trainen van het model. 

1. Koppel de uitvoer naar linkeruitvoerpoort van de linkermodule [Execute R Script][execute-r-script] aan de rechterinvoerpoort van de module [Train Model][train-model] (in deze zelfstudie [gebruikt u de gegevens die afkomstig zijn van de linkerkant](#train) van de module Split Data voor het trainen).
   
   > [!TIP]
   > U hoeft twee van de invoer- en een van de uitvoerpoorten van de module [Execute R Script][execute-r-script] niet nodig voor dit experiment, daarom kunt u ze ongekoppeld laten. 
   > 
   > 

Dit gedeelte van ons experiment ziet er nu ongeveer als volgt uit:  

![Een model trainen](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nu moet u de module [Trainingsmodel][train-model] vertellen dat u wilt dat het model de waarde van het kredietrisico voorspelt.

1. Selecteer de module [Train Model][train-model]. Klik in het deelvenster **Properties** op **Launch column selector**.

1. In het dialoogvenster **Select a single column** typt u "Kredietrisico" in het zoekveld onder **Available Columns**, selecteert u "Kredietrisico" hieronder en klikt u op de rechter pijlknop ( **>** ) om "Kredietrisico’s" naar **Selected Columns** te verplaatsen. 

    ![Selecteer de kolom Kredietrisico voor de module Train Model](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klik op het **OK**-selectievakje.

### <a name="two-class-support-vector-machine"></a>Two-Class Support Vector Machine

Vervolgens stelt u het SVM-model in.  

Eerst een beetje uitleg over SVM. Boosted Decision Trees werken goed met functies van elk type. Omdat de SVM-module een lineaire classificatie genereert, heeft het model dat wordt gegenereerd echter de beste testfout wanneer alle numerieke functies dezelfde schaal hebben. Als u alle numerieke functies naar dezelfde schaal wilt converteren, gebruikt u een 'Tanh'-transformatie (met de module [Gegevens normaliseren][normalize-data]). Dit transformeert onze cijfers naar het bereik [0,1]. De SVM-module converteert tekenreeksfuncties naar categorische functies en vervolgens naar binaire 0/1-functies, zodat u niet handmatig tekenreeksfuncties hoeft te transformeren. Ook moet u de kolom Kredietrisico (kolom 21) niet transformeren - deze is numeriek, maar het is de waarde die we het model aanleren om te voorspellen, daarom moet u deze ongewijzigd laten.  

Als u het SVM-model instelt, doe dan het volgende:

1. Zoek de module [Ondersteuningsvectormachine met twee klassen][two-class-support-vector-machine] in het modulepalet en sleep deze naar het canvas.

1. Klik met de rechtermuisknop op de module [Trainingsmodel][train-model], selecteer **Kopiëren**, klik vervolgens met de rechtermuisknop op het canvas en selecteer **Plakken**. De kopie van de module [Trainingsmodel][train-model] bevat dezelfde kolomselectie als het origineel.

1. Koppel de uitvoer van de module [Ondersteuningsvectormachine met twee klassen][two-class-support-vector-machine] aan de linkerinvoerpoort van de tweede [Trainingsmodel][train-model]-module.

1. Zoek de module [Gegevens normaliseren][normalize-data] en sleep deze naar het canvas.

1. Koppel de linkeruitvoer van de linkermodule [Execute R Script][execute-r-script] aan de invoerpoort van deze module (let op dat de uitvoerpoort van een module met meer dan één andere module kan zijn verbonden).

1. Koppel de linkeruitvoerpoort van de module [Gegevens normaliseren][normalize-data] aan de rechterinvoerpoort van de tweede [Trainingsmodel][train-model]-module.

Zo ziet dit deel van het experiment er ongeveer uit nadat het is uitgevoerd:  

![Het tweede model trainen](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Configureer nu de module [Gegevens normaliseren][normalize-data]:

1. Selecteer de module [Gegevens normaliseren][normalize-data]. Selecteer in het **Properties**-venster **Tanh** als parameter voor **Transformation method**.

1. Klik op **Launch column selector**, selecteer "No columns" voor **Begin With**, selecteer **Include** in de eerste vervolgkeuzelijst, selecteer **column type** in de tweede vervolgkeuzelijst en selecteer **Numeric** in de derde vervolgkeuzelijst. Hiermee wordt aangegeven dat alle numerieke kolommen (en alleen numerieke) worden getransformeerd.

1. Klik op het plusteken (+) aan de rechterkant van deze rij. Hiermee maakt u een rij van de vervolgkeuzelijsten. Selecteer **Exclude** in de eerste vervolgkeuzelijst, selecteer **column names** in de tweede vervolgkeuzelijst en voer "Kredietrisico" in het tekstveld in. Hiermee wordt aangegeven dat de kolom Kredietrisico's moet worden genegeerd (u moet dit doen omdat deze kolom numeriek is en zou worden omgezet als u deze niet uitsluit).

1. Klik op het **OK**-selectievakje.  

    ![Selecteer de module Normalize Data](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


De module [Gegevens normaliseren][normalize-data] is nu ingesteld om een Tanh-transformatie uit te voeren op alle numerieke kolommen, met uitzondering van de kolom Kredietrisico's.  

## <a name="score-and-evaluate-the-models"></a>De modellen beoordelen en evalueren

U gebruikt de testgegevens die waren gescheiden door de module [Gegevens splitsen][split] om onze getrainde modellen te beoordelen. U kunt vervolgens de resultaten van de twee modellen vergelijken om te zien welke betere resultaten heeft gegenereerd.  

### <a name="add-the-score-model-modules"></a>De modules Score Model toevoegen

1. Zoek de module [Beoordelingsmodel][score-model] en sleep deze naar het canvas.

1. Zoek de module [Train Model][train-model], sleep deze naar het canvas en sluit vervolgens de uitvoer van de module [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] aan op de linkerinvoerpoort van de module [Score Model][score-model].

1. Verbind de rechtermodule [R-script uitvoeren][execute-r-script] (onze testgegevens) met de rechterinvoerpoort van de module [Beoordelingsmodel][score-model].

    ![Module Score Model verbonden](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   De module [Score Model][score-model] kan nu de kredietinformatie uit de testgegevens halen via het model en de voorspellingen vergelijken die het model genereert met de kolom Kredietrisico in de testgegevens.

1. Kopieer en plak de module [Beoordelingsmodel][score-model] om een tweede kopie te maken.

1. Koppel de uitvoer van het SVM-model (de uitvoerpoort van de module [Train Model][train-model] die verbonden is met de module [Two-Class Support Vector Machine][two-class-support-vector-machine]) aan de invoerpoort van de tweede [Score Model][score-model]-module.

1. Voor het SVM-model moet u de dezelfde transformatie uitvoeren op de testgegevens als u met de trainingsgegevens heeft gedaan. Kopieer en plak daarom de module [Gegevens normaliseren][normalize-data] om een tweede kopie te maken en verbind deze met de rechtermodule [R-script uitvoeren][execute-r-script].

1. Koppel de linkeruitvoerpoort van de tweede module [Gegevens normaliseren][normalize-data] aan de rechterinvoerpoort van de tweede [Trainingsmodel][score-model]-module.

    ![Beide Score Model-modules verbonden](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>De module Evaluate Model toevoegen

Voor het evalueren van de twee beoordelingsresultaten en om deze te vergelijken, gebruikt u een module [Model evalueren][evaluate-model].  

1. Zoek de module [Model evalueren][evaluate-model] en sleep deze naar het canvas.

1. Koppel de uitvoerpoort van de [Score Model][score-model]-module die is gekoppeld aan het Boosted Decision Tree-model aan de linkerinvoerpoort van de [Evaluate Model][evaluate-model]-module.

1. Verbind de andere module [Beoordelingsmodel][score-model] met de rechterinvoerpoort.  

    ![Module Evaluate Model verbonden](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Voer het experiment uit en controleer de resultaten

Als u wilt het experiment uitvoeren, klikt u op de knop **RUN** onder het canvas. Dit kan enkele minuten duren. Een draaiende indicator op elke module laat zien dat deze wordt uitgevoerd en er wordt een groen vinkje weergegeven wanneer de module is voltooid. Wanneer alle modules een groen vinkje hebben, is het experiment voltooid.

Het experiment zou er nu ongeveer zo uit moeten zien:  

![Beide modellen evalueren](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Klik op de uitvoerpoort onder in de module [Model evalueren][evaluate-model] en klik op **Visualiseren**.  

De module [Model evalueren][evaluate-model] produceert een tweetal curven en metrische gegevens waarmee u de resultaten van de twee modellen kunt vergelijken. U kunt de resultaten als Receiver Operator Characteristic (ROC)-curven, Precision/Recall-curven of Lift-curven weergeven. Aanvullende gegevens die worden weergegeven, bevatten een verwarringsmatrix, cumulatieve waarden voor het gebied onder de curve (AUC) en andere metrische gegevens. U kunt de drempelwaarde wijzigen door de schuifregelaar naar links of rechts te bewegen en te zien hoe dit van invloed is op de set met metrische gegevens.  

Aan de rechterkant van de grafiek, klikt u op **Scored dataset** of **Scored dataset to compare** om de bijbehorende curve te markeren en de bijbehorende metrische gegevens te tonen die hieronder worden weergegeven. In de legenda voor de curven komt "Scored dataset" overeen met de linkerinvoerpoort van de module[Evaluate Model][evaluate-model]. In ons geval is dit het Boosted Decision Tree-model. "Scored dataset to compare" komt overeen met de juiste invoerpoort - het SVM-model in ons geval. Wanneer u op een van deze labels klikt, wordt de curve voor dit model gemarkeerd en de worden bijbehorende metrische gegevens weergegeven, zoals u ziet in de volgende afbeelding.  

![ROC-curve voor modellen](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Door deze waarden te onderzoeken, kunt u bepalen welk model de resultaten die u zoekt het dichtst benadert. U kunt teruggaan en uw experiment herhalen door parameterwaarden in de verschillende modellen te wijzigen. 

De wetenschap en kunst van het interpreteren van deze resultaten en het afstemmen van de modelprestaties vallen buiten deze zelfstudie. U kunt de volgende artikelen lezen voor meer informatie:
- [Modelprestaties evalueren in Azure Machine Learning Studio (klassiek)](evaluate-model-performance.md)
- [Parameters kiezen voor het optimaliseren van uw algoritmen in Azure Machine Learning Studio (klassiek)](algorithm-parameters-optimize.md)
- [Modelresultaten in Azure Machine Learning Studio (klassiek) interpreteren](interpret-model-results.md)

> [!TIP]
> Telkens wanneer u het experiment uitvoert, wordt een record van deze iteratie opgeslagen in de uitvoeringsgeschiedenis. U kunt deze iteraties bekijken en ze opnieuw bekijken door te klikken op **VIEW RUN HISTORY** onder het canvas. U kunt ook klikken op **Prior Ru** in het **Properties**-venster om terug te keren naar de iteratie direct vóór de versie die u hebt geopend.
> 
> U kunt een kopie van de iteraties van uw experiment maken door te klikken op **SAVE AS** onder het canvas. 
> Gebruik de eigenschappen **Summary** en **Description** van het experiment om bij te houden wat u hebt geprobeerd in uw iteraties.
> 
> Zie [Experimentiteraties in Azure Machine Learning Studio (klassiek) beheren](manage-experiment-iterations.md) voor meer informatie.  
> 
> 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen voltooid: 
 
> [!div class="checklist"]
> * Een experiment maken
> * Meerdere modellen trainen
> * De modellen beoordelen en evalueren

U kunt nu de modellen voor deze gegevens implementeren.

> [!div class="nextstepaction"]
> [Zelfstudie 3 - modellen implementeren](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/