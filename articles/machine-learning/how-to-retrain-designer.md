---
title: Pijplijn parameters gebruiken voor het opnieuw trainen van modellen in de ontwerp functie
titleSuffix: Azure Machine Learning
description: Train modellen met gepubliceerde pijp lijnen en pijplijn parameters in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 6efb0f095f8a157f723a3b7c0c2b229546ebb36b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708463"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Pijplijn parameters gebruiken voor het opnieuw trainen van modellen in de ontwerp functie


In dit artikel leert u hoe u Azure Machine Learning Designer kunt gebruiken om een machine learning model opnieuw te trainen met pijplijn parameters. U gebruikt gepubliceerde pijp lijnen om uw werk stroom te automatiseren en para meters in te stellen voor het trainen van uw model op nieuwe gegevens. Met pijplijn parameters kunt u bestaande pijp lijnen opnieuw gebruiken voor verschillende taken.  

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een machine learning-model trainen.
> * Een pijplijn parameter maken.
> * Publiceer uw trainings pijplijn.
> * Train uw model opnieuw met nieuwe para meters.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte
* Volledige deel 1 van deze instructie-to-reeks, [transformatie gegevens in de ontwerp functie](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

In dit artikel wordt ervan uitgegaan dat u enige kennis hebt van het bouwen van pijp lijnen in de ontwerp functie. Voor een begeleide Inleiding voltooit u de [zelf studie](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Voorbeeld pijplijn

De pijp lijn die in dit artikel wordt gebruikt, is een gewijzigde versie van de voor [Spelling](samples-designer.md#classification) van een voorbeeld pijplijn voor een pijp lijn in de ontwerp-start pagina. De pijp lijn gebruikt de module [gegevens importeren](algorithm-module-reference/import-data.md) in plaats van de voor beeld-gegevensset om te laten zien hoe u modellen traint met uw eigen gegevens.

![Scherm opname van de aangepaste voorbeeld pijplijn met een vak waarin de module import data is gemarkeerd](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Een pijplijn parameter maken

Pijplijn parameters worden gebruikt voor het bouwen van veelzijdige pijp lijnen die later opnieuw kunnen worden ingediend met verschillende parameter waarden. Bij sommige veelvoorkomende scenario's worden gegevens sets of sommige Hyper-para meters voor het opnieuw trainen bijgewerkt. Maak pijplijn parameters om variabelen dynamisch in runtime in te stellen. 

Pijplijn parameters kunnen worden toegevoegd aan de gegevens bron-of module parameters in een pijp lijn. Wanneer de pijp lijn opnieuw wordt ingediend, kunnen de waarden van deze para meters worden opgegeven.

Voor dit voor beeld wijzigt u het pad van de trainings gegevens van een vaste waarde naar een para meter, zodat u uw model op verschillende gegevens kunt trainen. U kunt ook andere module parameters toevoegen als pijplijn parameters op basis van uw use-case.

1. Selecteer de module **gegevens importeren** .

    > [!NOTE]
    > In dit voor beeld wordt de module gegevens importeren gebruikt om toegang te krijgen tot gegevens in een geregistreerd Data Store. U kunt vergelijk bare stappen volgen als u alternatieve patronen voor gegevens toegang gebruikt.

1. Selecteer uw gegevens bron in het detail venster van de module, rechts van het canvas.

1. Geef het pad op naar uw gegevens. U kunt ook **Bladeren naar pad** selecteren om door de bestands structuur te bladeren. 

1. Mouseover het veld **pad** en selecteer het beletsel teken boven het veld met het **pad** dat wordt weer gegeven.

1. Selecteer **toevoegen aan pijplijn parameter**.

1. Geef een parameter naam en een standaard waarde op.

   ![Scherm afbeelding die laat zien hoe u een pijplijn parameter maakt](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selecteer **Opslaan**.

   > [!NOTE]
   > U kunt ook een module parameter losmaken van de pijplijn parameter in het deel venster module details, vergelijkbaar met het toevoegen van pijplijn parameters.
   >
   > U kunt de pijplijn parameters inspecteren en bewerken door het tandwiel pictogram **instellingen** naast de titel van uw pijp lijn concept te selecteren. 
   >    - Nadat u de koppeling hebt verbroken, kunt u de pijplijn parameter verwijderen in het deel venster **instellingen** .
   >    - U kunt ook een pijplijn parameter toevoegen in het deel venster **instellingen** en vervolgens Toep assen op een van de para meters van een module.

1. Verzend de pijplijn uitvoering.

## <a name="publish-a-training-pipeline"></a>Een trainings pijplijn publiceren

Publiceer een pijp lijn naar een pijp lijn-eind punt om uw pijp lijnen in de toekomst eenvoudig te hergebruiken. Een pijplijn eindpunt maakt een REST-eind punt om in de toekomst pijp lijn aan te roepen. In dit voor beeld kunt u met het eind punt van de pijp lijn de pijp lijn opnieuw gebruiken om een model op verschillende gegevens te trainen.

1. Selecteer boven het Designing-canvas **publiceren** .
1. Selecteer of maak een pijplijn eindpunt.

   > [!NOTE]
   > U kunt meerdere pijp lijnen publiceren naar een enkel eind punt. Elke pijp lijn in een bepaald eind punt krijgt een versie nummer, dat u kunt opgeven wanneer u het eind punt van de pijp lijn aanroept.

1. Selecteer **Publiceren**.

## <a name="retrain-your-model"></a>Uw model opnieuw trainen

Nu u een gepubliceerde trainings pijplijn hebt, kunt u deze gebruiken om uw model op nieuwe gegevens te trainen. U kunt vanuit de studio-werk ruimte of via een programma uitvoeringen vanuit een pijplijn eindpunt verzenden.

### <a name="submit-runs-by-using-the-studio-portal"></a>Uitvoeringen verzenden met behulp van de Studio Portal

Voer de volgende stappen uit om een hulp programma voor het uitvoeren van een geparametriseerde pijp lijn te verzenden vanuit de Studio portal:

1. Ga naar de pagina **eind punten** in uw studio-werk ruimte.
1. Selecteer het tabblad **pijplijn eindpunten** . Selecteer vervolgens het eind punt van de pijp lijn.
1. Selecteer het tabblad **gepubliceerde pijp lijnen** . Selecteer vervolgens de pijplijn versie die u wilt uitvoeren.
1. Selecteer **Indienen**.
1. In het dialoog venster Setup kunt u de parameter waarden voor de run opgeven. Voor dit voor beeld werkt u het gegevenspad bij om uw model te trainen met een niet-Amerikaanse gegevensset.

![Scherm afbeelding die laat zien hoe u een door para meters uitgestelde pijplijn uitvoering kunt instellen in de ontwerp functie](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Uitvoeringen verzenden met behulp van code

U kunt het REST-eind punt van een gepubliceerde pijp lijn vinden in het deel venster Overzicht. Als u het eind punt aanroept, kunt u de gepubliceerde pijp lijn opnieuw trainen.

Als u een REST-aanroep wilt uitvoeren, hebt u een OAuth 2,0 Bearer-type verificatie-header nodig. Zie voor meer informatie over het instellen van verificatie voor uw werk ruimte en het maken van een geparametriseerde REST-aanroep een [Azure machine learning pijp lijn bouwen voor batch scores](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een gepara meter trainings pijplijn-eind punt maakt met behulp van de ontwerp functie.

Voor een volledig overzicht van hoe u een model kunt implementeren voor het maken van voor spellingen, raadpleegt u de [zelf studie over ontwerpen](tutorial-designer-automobile-price-train-score.md) voor het trainen en implementeren van een regressie model.
