---
title: 'Snelle Quantile regressie voor forests: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de Quantile regressie module van het snelle forest kunt gebruiken om een regressie model te maken waarmee waarden voor een opgegeven aantal quantiles kunnen worden voor speld.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907958"
---
# <a name="fast-forest-quantile-regression"></a>Regressie snelle forestkwantiel

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Met deze module kunt u een quantile regressie model in een snelle structuur maken in een pijp lijn. Quantile regressie van Fast forest is handig als u meer wilt weten over de verdeling van de voorspelde waarde, in plaats van een enkele gemiddelde Voorspellings waarde te verkrijgen. Deze methode heeft veel toepassingen, waaronder:  
  
- Voors pellen van prijzen  
  
- De prestaties van studenten schatten of een groei diagram Toep assen om de onderliggende ontwikkeling te beoordelen  
  
- Voorspellende relaties detecteren in gevallen waarbij er slechts een zwakke relatie tussen variabelen is  
  
Dit regressie algoritme is een gemarkeerde leer methode. Dit betekent dat **er een gecodeerde** gegevensset is vereist die een kolom Label bevat. Omdat het een regressie algoritme is, mag de kolom Label alleen numerieke waarden bevatten.

## <a name="more-about-quantile-regression"></a>Meer informatie over quantile regressie

Er zijn veel verschillende typen regressies. Eenvoudigweg gezegd, regressie betekent dat een model wordt toegepast op een doel uitgedrukt in een numerieke vector. Statistici zijn echter steeds meer geavanceerde methoden ontwikkeld voor regressie.

De eenvoudigste definitie van *quantile* is een waarde die een set gegevens opsplitst in groepen van gelijke grootte. de waarden voor quantile markeren dus de grenzen tussen groepen. Statistische spraak: quantiles zijn waarden die met regel matige tussen pozen worden uitgevoerd op basis van de inverse van de functie van de cumulatieve verdeling (CDF) van een wille keurige variabele.

Wanneer lineaire regressie modellen proberen de waarde van een numerieke variabele te voors pellen met behulp van één schatting, het *gemiddelde*, soms moet u het bereik of de volledige distributie van de doel variabele voors pellen. Voor dit doel zijn technieken als Bayesiaanse-regressie en quantile-regressie ontwikkeld.

Quantile regressie helpt u bij het begrijpen van de distributie van de voorspelde waarde. Quantile regressie modellen op basis van een structuur, zoals de toepassing die in deze module wordt gebruikt, hebben het extra voor deel dat ze kunnen worden gebruikt om niet-parametrische distributies te voors pellen.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Quantile regressie van Fast forest configureren

1. Voeg de **Quantile regressie** module van het snelle forest toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **machine learning-algoritmen** in de categorie **regressie** .

2. Geef in het rechterdeel venster van de Quantile regressie-module van **Fast forest** op hoe u wilt dat het model wordt getraind door de optie voor het maken van de **trainer** in te stellen.  
  
    - **Eén para meter**: als u weet hoe u het model wilt configureren, geeft u een specifieke set waarden als argumenten op. Wanneer u het model traint, gebruikt u [Train model](train-model.md).
  
    - **Parameter bereik**: als u niet zeker weet wat de beste para meters zijn, kunt u een para meter opruimen met behulp van de Hyper parameters-module [model](tune-model-hyperparameters.md) . De trainer herhaalt over meerdere waarden die u opgeeft om de optimale configuratie te vinden.

3. **Aantal bomen**, typt u het maximum aantal structuren dat kan worden gemaakt in de ensemble. Als u meer bomen maakt, leidt dit doorgaans tot een grotere nauw keurigheid, maar op de kosten van een langere training.  

4. **Aantal bladeren**, typ het maximum aantal bladeren of Terminal knooppunten dat in een wille keurige boom structuur kan worden gemaakt.  

5. **Mini maal aantal trainings instanties dat is vereist voor** het maken van een blad, geeft u het minimum aantal voor beelden op dat nodig is om een Terminal knooppunt (Leaf) in een boom structuur te creëren.  
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de opleidings gegevens ten minste 5 gevallen bevatten die aan dezelfde voor waarden voldoen.

6. Van de **fractie**, geeft u een getal tussen 0 en 1 op dat het gedeelte van de voor beelden vertegenwoordigt dat moet worden gebruikt bij het samen stellen van elke groep quantiles. Voor beelden worden wille keurig gekozen, met vervanging.

7. **Split-deling**, typ een getal tussen 0 en 1 dat het gedeelte van de functies vertegenwoordigt dat moet worden gebruikt in elke splitsing van de structuur. De gebruikte functies worden altijd wille keurig gekozen.

8. **Quantiles moet worden geschat**, typt u een door punt komma's gescheiden lijst van de Quantiles waarvoor u het model wilt trainen en maken van voor spellingen.
  
     Als u bijvoorbeeld een model wilt maken dat schattingen voor kwartielen, typt u `0.25; 0.5; 0.75` .  

9. Typ desgewenst een waarde voor **wille keurig getal Seed** als seeding van de generator voor wille keurige getallen die wordt gebruikt door het model.  De standaard waarde is 0, wat betekent dat er een wille keurige Seed wordt gekozen.
  
     U moet een waarde opgeven als u de resultaten van opeenvolgende uitvoeringen op dezelfde gegevens wilt reproduceren.  

10. Verbind de trainings-gegevensset en het niet-getrainde model met een van de trainings modules: 

    - Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](train-model.md) .

    - Als u de **modus trainer maken** instelt op het **parameter bereik**, gebruikt u de Hyper parameters-module voor het [afstemmen van modellen](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de eerste waarde in de lijst met para meters gebruikt.
    > 
    > - Als u één set parameter waarden doorgeeft aan de Hyper parameters-module voor het [Tune-model](tune-model-hyperparameters.md) , worden de waarden genegeerd en worden de standaard waarden gebruikt voor de cursist als er een reeks instellingen voor elke para meter wordt verwacht.
    > 
    > - Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.

11. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u de module training en gaat u naar uitvoer en het tabblad **Logboeken** in het rechter paneel. Klik op het pictogram **gegevensset registreren**.  U kunt het opgeslagen model vinden als een module in de boom structuur van de module.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
