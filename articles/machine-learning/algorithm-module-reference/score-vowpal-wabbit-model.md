---
title: Vowpal Wabbit-model scoren
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Score Vowpal Wabbit model voor het genereren van scores voor een set invoer gegevens met behulp van een bestaand getraind Vowpal Wabbit-model.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898405"
---
# <a name="score-vowpal-wabbit-model"></a>Vowpal Wabbit-model scoren
In dit artikel wordt beschreven hoe u de module **Score Vowpal Wabbit model** in azure machine learning Designer kunt gebruiken om scores voor een set invoer gegevens te genereren met behulp van een bestaand getraind Vowpal Wabbit-model.  

Deze module biedt de nieuwste versie van het Vowpal Wabbit Framework, versie 8.8.1. Gebruik deze module om gegevens te scoren met behulp van een getraind model dat is opgeslagen in de VW-versie 8-indeling.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Het Wabbit-model van Score Vowpal configureren

1.  Voeg de module **Score Vowpal Wabbit model** toe aan uw experiment.  
  
2.  Voeg een getraind Vowpal Wabbit-model toe en sluit het aan op de linker-invoer poort. U kunt een getraind model gebruiken dat is gemaakt in hetzelfde experiment of een opgeslagen model zoeken in de categorie **gegevens sets** van het navigatie deel venster van de ontwerp functie. Het model moet echter wel beschikbaar zijn in Azure Machine Learning Designer.  
  
    > [!NOTE]
    > Alleen Vowpal Wabbit 8.8.1-modellen worden ondersteund; u kunt geen opgeslagen modellen koppelen die zijn getraind met behulp van andere algoritmen.
  
3.  Voeg de test gegevensset toe en verbind deze met de juiste invoer poort. Als de test-gegevensset een directory is die het test gegevensbestand bevat, geeft u de bestands naam van de test gegevens op met de **naam van het test gegevensbestand**. Als de test-gegevensset één bestand is, laat u **de naam van het test gegevensbestand** leeg.

4. In het tekstvak **VW argumenten** typt u een aantal geldige opdracht regel argumenten voor het uitvoer bare bestand Vowpal Wabbit.  

    Zie de sectie [technische opmerkingen](#technical-notes) voor informatie over welke Vowpal Wabbit-argumenten worden ondersteund en niet worden ondersteund in azure machine learning.  

5.  **Naam van het test gegevensbestand**: Typ de naam van het bestand dat de invoer gegevens bevat. Dit argument wordt alleen gebruikt als de gegevensset van de test een directory is.

6. **Bestands type opgeven**: Geef aan welke indeling uw trainings gegevens gebruikt. Vowpal Wabbit ondersteunt de volgende twee invoer bestands indelingen:  

   - **VW** vertegenwoordigt de interne indeling die wordt gebruikt door Vowpal Wabbit. Zie de [pagina met Vowpal Wabbit-wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) voor meer informatie. 
   - **SVMLight** is een indeling die wordt gebruikt door andere machine learning-hulpprogram ma's. 

7. Selecteer de optie, **Voeg een extra kolom met labels** toe als u labels samen met de scores wilt uitvoeren.  

   In het geval van het afhandelen van tekst gegevens is er meestal geen labels vereist voor Vowpal Wabbit en worden alleen de scores voor elke rij gegevens geretourneerd.  

8. Selecteer de optie, **Voeg een extra kolom met onbewerkte scores** toe als u onbewerkte scores wilt uitvoeren met de resultaten.  

9. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u de resultaten wilt visualiseren, klikt u met de rechter muisknop op de uitvoer van de [Wabbit-model module Score Vowpal](score-vowpal-wabbit-model.md) . De uitvoer duidt op een Voorspellings Score die is genormaliseerd van 0 tot 1. 

+ Als u de resultaten wilt evalueren, moet de uitvoer gegevensset specifieke namen van de Score kolom bevatten die voldoen aan de vereisten voor de module voor het evalueren van modellen.

  + Voor de regressie taak moet de te evalueren gegevensset één kolom hebben met de naam `Regression Scored Labels` , die de gescoorde labels vertegenwoordigt.
  + Voor de binaire classificatie taak moet de te evalueren gegevensset twee kolommen hebben, met de naam `Binary Class Scored Labels` , `Binary Class Scored Probabilities` die de gescoorde labels en de waarschijnlijkheid vertegenwoordigen.
  + Voor een taak met meerdere classificaties moet de te evalueren gegevensset één kolom hebben met de naam `Multi Class Scored Labels` , die de gescoorde labels vertegenwoordigt.

  Houd er rekening mee dat de resultaten van de Wabbit-model module Score Vowpal niet rechtstreeks kunnen worden geëvalueerd. Voordat de gegevens worden geëvalueerd, moet de gegevensset worden gewijzigd volgens de bovenstaande vereisten.

##  <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

### <a name="parameters"></a>Parameters

Vowpal Wabbit heeft veel opdracht regel opties voor het kiezen en afstemmen van algoritmen. Een volledige bespreking van deze opties is hier niet mogelijk. We raden u aan om de [pagina Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)te bekijken.  

De volgende para meters worden niet ondersteund in Azure Machine Learning Studio (klassiek).  

-   De invoer-en uitvoer opties die zijn opgegeven in [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Deze eigenschappen zijn al automatisch geconfigureerd door de module.  
  
-   Daarnaast is alle opties die meerdere uitvoer genereren of meerdere invoer accepteert niet toegestaan. Dit zijn onder andere *`--cbt`* , *`--lda`* , en *`--wap`* .  
  
-   Alleen bewaakte leer algoritmen worden ondersteund. Hiermee worden de volgende opties niet toegestaan *`–active`* : `--rank` , *`--search`* enzovoort.  

Alle andere argumenten dan hierboven worden beschreven, zijn toegestaan.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 