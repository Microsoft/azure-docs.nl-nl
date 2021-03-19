---
title: 'Gegevens normaliseren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module normaliseren gegevens in Azure Machine Learning om een gegevensset te transformeren met behulp van *normalisatie*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: de0a23ca9dea210d91fe259b06622226549ba3b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890444"
---
# <a name="normalize-data-module"></a>Gegevens module normaliseren

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een gegevensset te transformeren met behulp van *normalisatie*.

Normalisatie is een techniek die vaak wordt toegepast als onderdeel van de gegevens voorbereiding voor machine learning. Het doel van normalisatie is het wijzigen van de waarden van de numerieke kolommen in de gegevensset voor het gebruik van een gemeen schappelijke schaal, zonder verschillen in de waardenbereiken te vervormen of gegevens verlies. Normalisatie is ook vereist voor sommige algoritmen om de gegevens correct te model leren.

Stel bijvoorbeeld dat uw invoer gegevensset één kolom bevat met waarden van 0 tot 1 en een andere kolom met waarden van 10.000 tot 100.000. Het grote verschil in de *schaal* van de getallen kan problemen veroorzaken bij het combi neren van de waarden als onderdelen tijdens het model leren.

*Normalisatie* voor komt deze problemen door nieuwe waarden te maken die de algemene distributie en verhoudingen in de bron gegevens behouden, terwijl waarden binnen een schaal worden toegepast op alle numerieke kolommen die in het model worden gebruikt.

Deze module biedt verschillende opties voor het transformeren van numerieke gegevens:

- U kunt alle waarden wijzigen in een 0-1-schaal of de waarden transformeren door ze te vertegenwoordigen als percentiel posities in plaats van absolute waarden.
- U kunt normalisatie Toep assen op één kolom of op meerdere kolommen in dezelfde gegevensset.
- Als u de pijp lijn moet herhalen of dezelfde normalisatie stappen op andere gegevens wilt Toep assen, kunt u de stappen als een normalisatie transformatie opslaan en deze Toep assen op andere gegevens sets die hetzelfde schema hebben.

> [!WARNING]
> Voor sommige algoritmen moeten gegevens worden genormaliseerd voordat een model kan worden getraind. Andere algoritmen voeren hun eigen gegevens schaaling of normalisatie uit. Daarom moet u, wanneer u een machine learning algoritme kiest om te gebruiken bij het bouwen van een voorspellend model, de gegevens vereisten van het algoritme controleren voordat u normalisatie toepast op de trainings gegevens.

##  <a name="configure-normalize-data"></a>Normaliseren van gegevens configureren

U kunt per keer slechts één normalisatie methode Toep assen met behulp van deze module. Daarom wordt dezelfde normalisatie methode toegepast op alle kolommen die u selecteert. Als u verschillende normalisatie methoden wilt gebruiken, moet u een tweede exemplaar van **normaliseren gegevens** gebruiken.

1. Voeg de module **Normal data-gegevens** toe aan de pijp lijn. U kunt de module vinden in Azure Machine Learning, onder **gegevens transformatie**, in de categorie **schalen en verminderen** .

2. Verbind een gegevensset die ten minste één kolom met alle getallen bevat.

3. Gebruik de kolom kiezer om te kiezen welke numerieke kolommen moeten worden genormaliseerd. Als u geen afzonderlijke kolommen kiest, worden standaard **alle** numerieke kolom typen in de invoer opgenomen en wordt hetzelfde normalisatie proces toegepast op alle geselecteerde kolommen. 

    Dit kan leiden tot vreemde resultaten als u numerieke kolommen opneemt die niet normaal mogen worden genormaliseerd. Controleer de kolommen altijd zorgvuldig.

    Als er geen numerieke kolommen worden gedetecteerd, controleert u de meta gegevens van de kolom om te controleren of het gegevens type van de kolom een ondersteund numeriek type is.

    > [!TIP]
    > Om ervoor te zorgen dat kolommen van een specifiek type als invoer worden opgegeven, probeert u de module [select columns in dataset uit](./select-columns-in-dataset.md) te voeren voordat u **gegevens normaliseert**.

4. **0 voor constante kolommen gebruiken wanneer dit selectie** vakje is ingeschakeld: Selecteer deze optie als een wille keurige numerieke kolom één ongewijzigde waarde bevat. Dit zorgt ervoor dat dergelijke kolommen niet worden gebruikt in normalisatie bewerkingen.

5. Kies in de vervolg keuzelijst **transformatie methode** één wiskundige functie die u wilt Toep assen op alle geselecteerde kolommen. 
  
    - **Zscore**: converteert alle waarden naar een z-Score.
    
      De waarden in de kolom worden getransformeerd met behulp van de volgende formule:  
  
      ![normalisatie met z&#45;scores](media/module/aml-normalization-z-score.png)
  
      Gemiddelde en standaard afwijking worden voor elke kolom afzonderlijk berekend. Standaard deviatie voor populatie wordt gebruikt.
  
    - **MinMax**: met de min-maximum normalisatie wordt elke functie lineair geschaald naar het interval van [0, 1].
    
      U kunt het interval voor [0, 1] opnieuw schalen door de waarden van elke functie te verschuiven zodat de minimale waarde 0 is en vervolgens te delen door de nieuwe maximale waarde (dit is het verschil tussen de oorspronkelijke maximum-en minimum waarden).
      
      De waarden in de kolom worden getransformeerd met behulp van de volgende formule:  
  
      ![normalisatie met de functie min&#45;Max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistiek**: de waarden in de kolom worden getransformeerd met behulp van de volgende formule:

      ![formule voor normalisatie per logistiek functie](media/module/aml-normalization-logistic.png "AML_normalization-logistiek")  
  
    - **Logaritmisch**: met deze optie worden alle waarden geconverteerd naar een logaritmische schaal.
  
      De waarden in de kolom worden getransformeerd met behulp van de volgende formule:
  
      ![formule logboek&#45;normale distributie](media/module/aml-normalization-lognormal.png "AML_normalization-logaritmisch")
    
      Hier zijn μ en σ de para meters van de distributie, berekend op basis van de gegevens als Maxi maal waarschijnlijke schattingen voor elke kolom afzonderlijk.  
  
    - **TanH**: alle waarden worden geconverteerd naar een hyperbolische tangens.
    
      De waarden in de kolom worden getransformeerd met behulp van de volgende formule:
    
      ![normalisatie met de functie tanh](media/module/aml-normalization-tanh.png "AML_normalization-TANH")

6. Verzend de pijp lijn, of dubbel klik op de module **normaliseren gegevens** en selecteer **geselecteerde uitvoeren**. 

## <a name="results"></a>Resultaten

In de module **normaliseren gegevens** worden twee uitvoer gegenereerd:

- Als u de getransformeerde waarden wilt weer geven, klikt u met de rechter muisknop op de module en selecteert u **visualiseren**.

    Standaard worden waarden getransformeerd. Als u de getransformeerde waarden wilt vergelijken met de oorspronkelijke waarden, gebruikt u de module [kolommen toevoegen](./add-columns.md) om de gegevens sets opnieuw samen te voegen en de kolommen naast elkaar weer te geven.

- Als u de trans formatie wilt opslaan zodat u dezelfde normalisatie methode op een andere gegevensset kunt Toep assen, selecteert u de module en selecteert u **gegevensset registreren** onder het tabblad **uitvoer** in het rechterdeel venster.

    U kunt de opgeslagen trans formaties vervolgens laden vanuit de groep **trans formaties** van het navigatie deel venster links en deze Toep assen op een gegevensset met hetzelfde schema met behulp van [Apply Transform](apply-transformation.md).  


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 