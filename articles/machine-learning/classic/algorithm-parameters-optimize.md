---
title: 'ML Studio (klassiek): algoritmen optimaliseren-Azure'
description: Hierin wordt uitgelegd hoe u de optimale parameterset kiest voor een algoritme in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: a216dc7cb17b9e35e412c6bebe34c0cccfb732e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519300"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Para meters kiezen voor het optimaliseren van uw algoritmen in Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

In dit onderwerp wordt beschreven hoe u de juiste afstemming kunt instellen voor een algoritme in Azure Machine Learning Studio (klassiek). Voor de meeste machine learning algoritmen zijn para meters ingesteld. Wanneer u een model traint, moet u waarden opgeven voor die para meters. De effectiviteit van het getrainde model is afhankelijk van de model parameters die u kiest. Het proces van het vinden van de optimale set para meters wordt het *model selectie* genoemd.



Er zijn verschillende manieren om een model te selecteren. In machine learning is Kruis validatie een van de meest gebruikte methoden voor het selecteren van modellen en is het standaard model selectie mechanisme in Azure Machine Learning Studio (klassiek). Omdat Azure Machine Learning Studio (klassiek) zowel R als python ondersteunt, kunt u hun eigen model selectie mechanismen altijd implementeren met behulp van R of python.

Er zijn vier stappen in het proces van het vinden van de beste parameterset:

1. **Definieer de parameter ruimte**: voor de algoritme moet u eerst de exacte parameter waarden bepalen die u wilt overwegen.
2. **De instellingen voor kruis validatie definiëren**: bepalen hoe u kruis validatie vouwen kiest voor de gegevensset.
3. **Definieer de metrische gegevens**: Bepaal welke meet waarde moet worden gebruikt voor het bepalen van de beste set para meters, zoals nauw keurigheid, wortel gemiddelde van fout, precisie, intrekken of f-Score.
4. **Train, evalueer en Compare**: voor elke unieke combi natie van de parameter waarden, worden Kruis validatie uitgevoerd door en op basis van de fout metriek die u definieert. Nadat u de evaluatie en vergelijking hebt uitgevoerd, kunt u het beste model kiezen.

In de volgende afbeelding ziet u hoe dit kan worden bereikt in Azure Machine Learning Studio (klassiek).

![De beste parameterset zoeken](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>De parameter ruimte definiëren
U kunt de ingestelde para meters definiëren bij de stap model initialisatie. Het deel venster para meter van alle machine learning algoritmen heeft twee trainers modi: *één para meter* en een *bereik met para meters*. Kies de modus voor het bereik van de para meter. In de modus bereik van de para meter kunt u meerdere waarden invoeren voor elke para meter. U kunt door komma's gescheiden waarden opgeven in het tekstvak.

![Versterkte beslissings structuur met twee klassen, één para meter](./media/algorithm-parameters-optimize/fig2.png)

 U kunt ook de maximum-en minimum punten van het raster definiëren en het totale aantal punten dat moet worden gegenereerd met **behulp van de opbouw functie voor bereik**. Standaard worden de parameter waarden op een lineaire schaal gegenereerd. Maar als de **logboek schaal** is ingeschakeld, worden de waarden gegenereerd in de logaritmische schaal (dat wil zeggen, de verhouding van de aangrenzende punten is constant in plaats van het verschil). Voor para meters voor geheel getal kunt u een bereik definiëren met behulp van een koppel teken. Bijvoorbeeld: "1-10" betekent dat alle gehele getallen tussen 1 en 10 (inclusief) de ingestelde para meter vormen. Een gemengde modus wordt ook ondersteund. De para meter set ' 1-10, 20, 50 ' zou bijvoorbeeld gehele getallen 1-10, 20 en 50 bevatten.

![Versterkte beslissings structuur met twee klassen, parameter bereik](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Kruis validatie vouwen definiëren
De [partitie en de voorbeeld][partition-and-sample] module kunnen worden gebruikt om een wille keurige vouw toe te wijzen aan de gegevens. In de volgende voorbeeld configuratie voor de module definiëren we vijf vouwen en worden wille keurig een vouw nummer toegewezen aan de voorbeeld exemplaren.

![Partitie en voor beeld](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>De metrische gegevens definiëren
De Hyper parameters-module voor het [afstemmen van modellen][tune-model-hyperparameters] biedt ondersteuning voor het empirisch kiezen van de beste set para meters voor een bepaalde algoritme en gegevensset. Naast andere informatie met betrekking tot de training van het model, bevat het deel venster **Eigenschappen** van deze module de metrische gegevens voor het bepalen van de beste parameterset. Het heeft respectievelijk twee verschillende vervolg keuzelijsten voor classificatie-en regressie algoritmen. Als het algoritme dat wordt onderzocht een classificatie algoritme is, wordt de waarde voor regressie genegeerd en omgekeerd. In dit specifieke voor beeld is de metriek **nauw keurig**.   

![Sweep-para meters](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Trainen, evalueren en vergelijken
De Hyper parameters-module van hetzelfde [Tune-model][tune-model-hyperparameters] traint alle modellen die overeenkomen met de parameterset, evalueert diverse metrische gegevens en maakt vervolgens het beste getrainde model op basis van de metrische gegevens die u kiest. Deze module heeft twee verplichte invoer:

* De niet-getrainde leerer
* De gegevensset

De module heeft ook een optionele gegevensset-invoer. Verbind de gegevensset met vouw gegevens met de invoer van verplichte gegevensset. Als aan de gegevensset geen Vouw gegevens zijn toegewezen, wordt standaard automatisch een kruis validatie met 10 vouwen uitgevoerd. Als de vouw toewijzing niet is voltooid en er een validatie-gegevensset wordt opgegeven op de optionele poort van de gegevensset, wordt een test modus gekozen en wordt de eerste gegevensset gebruikt om het model te trainen voor elke parameter combinatie.

![Classificatie van versterkte beslissings structuur](./media/algorithm-parameters-optimize/fig6a.png)

Het model wordt vervolgens geëvalueerd op de validatie gegevensset. In de linker uitvoer poort van de module worden verschillende metrische gegevens weer gegeven als functies van parameter waarden. De juiste uitvoer poort geeft het getrainde model dat overeenkomt met het best presterende model volgens de gekozen metriek (**nauw keurigheid** in dit geval).  

![Validatie gegevensset](./media/algorithm-parameters-optimize/fig6b.png)

U kunt de exacte para meters zien die zijn gekozen door de juiste uitvoer poort te visualiseren. Dit model kan worden gebruikt in een score voor een testset of in een Operational-webservice nadat deze is opgeslagen als een getraind model.

<!-- Module References -->
[partition-and-sample]: /azure/machine-learning/studio-module-reference/partition-and-sample
[tune-model-hyperparameters]: /azure/machine-learning/studio-module-reference/tune-model-hyperparameters