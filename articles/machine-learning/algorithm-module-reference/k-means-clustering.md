---
title: 'K-betekent clustering: module verwijzing'
titleSuffix: Azure Machine Learning
description: 'Meer informatie over het gebruik van de module K: Clustering in de Azure Machine Learning voor het trainen van cluster modellen.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 7e0b61c1ca6ae30044e4c9d4705bdce01eac1942
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93323695"
---
# <a name="module-k-means-clustering"></a>Module: K-betekent clustering

In dit artikel wordt beschreven hoe u de *clustering module K-betekent* in azure machine learning Designer kunt gebruiken om een niet-getraind k-means clustering model te maken. 
 
K: is een van de eenvoudigste en *meest bekende* algoritmen voor leren. U kunt het algoritme gebruiken voor een aantal machine learning taken, zoals: 

* [Abnormale gegevens detecteren](/archive/msdn-magazine/2013/february/data-clustering-detecting-abnormal-data-using-k-means-clustering).
* Clustering van tekst documenten.
* Gegevens sets analyseren voordat u andere classificatie-of regressie methoden gebruikt. 

U kunt als volgt een cluster model maken:

* Voeg deze module toe aan de pijp lijn.
* Verbind een gegevensset.
* Stel para meters in, zoals het aantal clusters dat u verwacht, de afstands metriek die moet worden gebruikt voor het maken van de clusters, enzovoort. 
  
Nadat u de module Hyper parameters hebt geconfigureerd, verbindt u het niet-trainde model met het [cluster Model Train](train-clustering-model.md). Omdat het algoritme van K-betekent een niet-gecontroleerde leer methode is, is een label kolom optioneel. 

+ Als uw gegevens een label bevatten, kunt u de label waarden gebruiken om de selectie van de clusters te begeleiden en het model te optimaliseren. 

+ Als uw gegevens geen label hebben, worden met de algoritme clusters gemaakt die mogelijke categorieën vertegenwoordigen, op basis van uitsluitend op de gegevens.  

##  <a name="understand-k-means-clustering"></a>K-means clustering begrijpen
 
Over het algemeen gebruikt clustering iteratieve technieken om cases in een gegevensset te groeperen in clusters met vergelijk bare kenmerken. Deze groeperingen zijn handig voor het verkennen van gegevens, het identificeren van afwijkingen in de gegevens en uiteindelijk voor het maken van voor spellingen. Clustering modellen kunnen u ook helpen bij het identificeren van relaties in een gegevensset die u mogelijk niet logisch hebt afgeleid door te bladeren of een eenvoudige waarneming. Om die reden wordt clustering vaak gebruikt in de vroege fasen van machine learning taken, om de gegevens te verkennen en onverwachte correlaties te ontdekken.  
  
 Wanneer u een cluster model configureert met behulp van de K-betekent-methode, moet u een doel nummer *K* opgeven dat het aantal *centroids* aangeeft dat u wilt gebruiken in het model. De massa middelpunt is een punt dat representatief is voor elk cluster. Het algoritme K-houdt elk binnenkomend gegevens punt toe aan een van de clusters door de binnenste cluster som van de kwadraten te minimaliseren. 
 
Wanneer de trainings gegevens worden verwerkt, begint het algoritme van K: met een initiële set wille keurig gekozen centroids. Centroids fungeren als start punt voor de clusters en past het-algoritme van Lloyd's toe om hun locaties iteratief te verfijnen. Het-algoritme voor K: stopt met het maken en verfijnen van clusters wanneer deze aan een of meer van de volgende voor waarden voldoen:  
  
-   De centroids stabiliseren, wat inhoudt dat de cluster toewijzingen voor afzonderlijke punten niet meer veranderen en dat het algoritme is geconvergeerd op een oplossing.  
  
-   Het algoritme heeft het opgegeven aantal herhalingen voltooid.  
  
 Nadat u de trainings fase hebt voltooid, gebruikt u de module [gegevens toewijzen aan clusters](assign-data-to-clusters.md) om nieuwe cases toe te wijzen aan een van de clusters die u hebt gevonden met behulp van het K-betekent-algoritme. U voert cluster toewijzing uit door de afstand tussen de nieuwe case en de massa middelpunt van elk cluster te berekenen. Elke nieuwe case wordt toegewezen aan het cluster met de dichtstbijzijnde massa middelpunt.  

## <a name="configure-the-k-means-clustering-module"></a>De K-betekent-cluster module configureren
  
1.  Voeg de **cluster module K-betekent** aan uw pijp lijn toe.  
  
2.  Selecteer de optie **trainer maken** om op te geven hoe het model moet worden getraind.  
  
    -   **Eén para meter**: als u de exacte para meters die u wilt gebruiken in het cluster model weet, kunt u een specifieke set waarden opgeven als argumenten.  
  
3.  Voor het **aantal centroids** typt u het aantal clusters waarmee u het algoritme wilt beginnen.  
  
     Het model is niet gegarandeerd om precies dit aantal clusters te produceren. Het algoritme begint met dit aantal gegevens punten en wordt herhaald om de optimale configuratie te vinden. U kunt verwijzen naar de [bron code van sklearn](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069).
  
4.  De **initialisatie van** eigenschappen wordt gebruikt om het algoritme op te geven dat wordt gebruikt voor het definiëren van de eerste cluster configuratie.  
  
    -   **Eerste N**: het eerste aantal gegevens punten wordt gekozen uit de gegevensset en gebruikt als eerste manier. 
    
         Deze methode wordt ook wel de *smeden-methode* genoemd.  
  
    -   **Wille keurig**: het algoritme plaatst een gegevens punt in een cluster wille keurig en berekent het oorspronkelijke gemiddelde als de massa middelpunt van de wille keurig toegewezen punten van het cluster. 

         Deze methode wordt ook wel de *wille keurige partitie* methode genoemd.  
  
    -   **K-betekent + +**: dit is de standaard methode voor het initialiseren van clusters.  
  
         De **K-houdt + +-** algoritme is voorgesteld in 2007 door David Arthur en Sergei Vassilvitskii om te voor komen dat er geclusterd wordt door het standaard-K-algoritme. **K-betekent + +** verbetert de standaard K-betekent dat u een andere methode gebruikt voor het kiezen van de eerste cluster centrums.  
  
    
5.  Voor **wille keurig getal zaad** typt u optioneel een waarde die moet worden gebruikt als seed voor de initialisatie van het cluster. Deze waarde kan een aanzienlijk effect hebben op de selectie van een cluster.  
  
6.  Kies voor **metriek** de functie die u wilt gebruiken voor het meten van de afstand tussen cluster vectoren of tussen nieuwe gegevens punten en de wille keurig gekozen massa middelpunt. Azure Machine Learning ondersteunt de volgende waarden voor de cluster afstand:  
  
    -   **Euclidean**: de Euclidean-afstand wordt vaak gebruikt als een meting van een cluster spreiding voor k-means clustering. Deze metrische gegevens hebben de voor keur omdat hiermee de gemiddelde afstand tussen punten en de centroids wordt geminimaliseerd.
  
7.  Voor **herhalingen** typt u het aantal keren dat het algoritme moet worden herhaald om de trainings gegevens te herhalen voordat de selectie van centroids wordt voltooid.  
  
     U kunt deze para meter aanpassen om de nauw keurigheid van de trainings tijd af te stemmen.  
  
8.  Voor de **modus label toewijzen** kiest u een optie die aangeeft hoe een label kolom, als deze aanwezig is in de gegevensset, moet worden afgehandeld.  
  
     Omdat k-means clustering een onbewaakte machine learning methode is, zijn labels optioneel. Als uw gegevensset echter al een kolom label heeft, kunt u deze waarden gebruiken om de selectie van de clusters te begeleiden, of u kunt opgeven dat de waarden moeten worden genegeerd.  
  
    -   **Kolom Label negeren**: de waarden in de kolom Label worden genegeerd en worden niet gebruikt bij het maken van het model.
  
    -   **Ontbrekende waarden vullen**: de waarden van de label kolom worden gebruikt als functies om de clusters te bouwen. Als er rijen ontbreken in een label, wordt de waarde toegerekend met behulp van andere functies.  
  
    -   **Overschrijven van dichtstbijzijnde naar Center**: de waarden van de label kolom worden vervangen door voorspelde label waarden met behulp van het label van het punt dat zich het dichtst bij de huidige massa middelpunt bevindt.  

8.  Selecteer de optie **kenmerken normaliseren** als u functies wilt normaliseren vóór de training.
  
     Als u normalisatie toepast vóór de training, worden de gegevens punten genormaliseerd naar `[0,1]` MinMaxNormalizer.

10. Train het model.  
  
    -   Als u de **modus trainer maken** instelt op **één para meter**, voegt u een gecodeerde gegevensset toe en traint u het model met behulp van de module [clustering model trainen](train-clustering-model.md) .  
  
## <a name="results"></a>Resultaten

Nadat u klaar bent met het configureren en trainen van het model, hebt u een model dat u kunt gebruiken voor het genereren van scores. Er zijn echter meerdere manieren om het model te trainen en meerdere manieren om de resultaten weer te geven en te gebruiken: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Een moment opname van het model vastleggen in uw werk ruimte

Als u de module [clustering-model trainen](train-clustering-model.md) hebt gebruikt:

1. Selecteer de module **clustering model trainen** en open het rechter paneel.

2. Selecteer het tabblad **uitvoer** . Selecteer het pictogram **gegevensset registreren** om een kopie van het getrainde model op te slaan.

Het opgeslagen model vertegenwoordigt de trainings gegevens op het moment dat u het model hebt opgeslagen. Als u later de trainings gegevens die in de pijp lijn worden gebruikt, bijwerkt, wordt het opgeslagen model niet bijgewerkt. 

### <a name="see-the-clustering-result-dataset"></a>De gegevensset van het cluster resultaat bekijken 

Als u de module [clustering-model trainen](train-clustering-model.md) hebt gebruikt:

1. Klik met de rechter muisknop op de module **clustering model trainen** .

2. Selecteer **Visualiseren**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tips voor het genereren van het beste cluster model  

Het is bekend dat het *seeding* proces dat wordt gebruikt tijdens clusteren, het model aanzienlijk kan beïnvloeden. Seeding betekent dat de eerste plaatsing van punten in mogelijke centroids.
 
Als de gegevensset bijvoorbeeld veel uitbijters bevat en er een uitschieter wordt gekozen voor het seeden van de clusters, zijn er geen andere gegevens punten goed in het cluster en kan het cluster een singleton zijn. Dat wil zeggen dat het slechts één punt kan hebben.  
  
U kunt dit probleem op een aantal manieren voor komen:  
  
-   Wijzig het aantal centroids en voer meerdere seed-waarden uit.  
  
-   Maak meerdere modellen, variërend van metrische gegevens of meer.  
  
Over het algemeen is het mogelijk dat een wille keurige configuratie een lokaal geoptimaliseerde set clusters oplevert voor het clusteren van modellen. Met andere woorden, de set clusters die door het model wordt geretourneerd, is alleen geschikt voor de huidige gegevens punten en kan niet worden gegeneraliseerd met andere gegevens. Als u een andere initiële configuratie gebruikt, kan de K-betekent-methode een andere, superieure, configuratie hebben. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.