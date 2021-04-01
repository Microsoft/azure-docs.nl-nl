---
title: 'Neural netwerk regressie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Neural-module voor netwerk regressie in Azure Machine Learning om een regressie model te maken met behulp van een aanpasbaar Neural-netwerk algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890368"
---
# <a name="neural-network-regression-module"></a>Neural-module voor netwerk regressie

*Hiermee maakt u een regressie model met behulp van een Neural-netwerk algoritme*  
  
 Categorie: model/regressie Machine Learning/initialiseren
  
## <a name="module-overview"></a>Module overzicht  

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een regressie model te maken met behulp van een aanpasbaar Neural-netwerk algoritme.
  
 Hoewel Neural-netwerken algemeen bekend zijn voor gebruik in diep gaande lessen en het model leren van complexe problemen, zoals afbeeldings herkenning, kunnen ze eenvoudig worden aangepast aan regressie problemen. Een klasse van statistische modellen kan een Neural-netwerk worden genoemd als ze adaptieve gewichten gebruiken en niet-lineaire functies van hun invoer kunnen benaderen. Daarom is Neural netwerk regressie geschikt voor problemen waarbij een meer traditioneel regressie model niet aan een oplossing kan voldoen.
  
 Neural Network regressie is een *gemarkeerde* leer methode en vereist daarom een gecodeerde gegevensset, die een kolom Label bevat. Omdat een regressie model een numerieke waarde voor spelt, moet de kolom Label een numeriek gegevens type zijn.  
  
 U kunt het model trainen door het model en de gecodeerde gegevensset op te geven als invoer voor het [trainen van modellen](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden te voors pellen voor de nieuwe invoer voorbeelden.  
  
## <a name="configure-neural-network-regression"></a>Regressie voor Neural-netwerk configureren 

Neural-netwerken kunnen uitgebreid worden aangepast. In deze sectie wordt beschreven hoe u een model maakt met behulp van twee methoden:
  
+ [Een Neural-netwerk model maken met behulp van de standaard architectuur](#bkmk_DefaultArchitecture)  
  
    Als u de standaard Neural-netwerk architectuur accepteert, gebruikt u het deel venster **Eigenschappen** om para meters in te stellen die het gedrag van het Neural-netwerk bepalen, zoals het aantal knoop punten in de verborgen laag, het leer tempo en de normalisatie.

    Begin hier als u geen ervaring hebt met Neural-netwerken. De module ondersteunt een groot aantal aanpassingen, evenals model afstemming, zonder grondige kennis van Neural netwerken. 

+ Een aangepaste architectuur definiëren voor een Neural-netwerk 

    Gebruik deze optie als u extra verborgen lagen wilt toevoegen of de netwerk architectuur, de verbindingen en activerings functies volledig wilt aanpassen.
    
    Deze optie is het beste als u al enigszins bekend bent met Neural Networks. U gebruikt de net # taal om de netwerk architectuur te definiëren.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Een Neural-netwerk model maken met behulp van de standaard architectuur

1.  Voeg de **Neural-module netwerk regressie** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **machine learning**, **initialiseren**, in de categorie **regressie** . 
  
2. Geef aan hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: Kies deze optie als u al weet hoe u het model wilt configureren.

    -   **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.   

3.  In **specificatie van verborgen laag** selecteert u **volledig verbonden Case**. Met deze optie wordt een model gemaakt met behulp van de standaard Neural-netwerk architectuur, die voor een Neural netwerk regressie model deze kenmerken heeft:  
  
    + Het netwerk heeft precies één verborgen laag.
    + De uitvoer laag is volledig verbonden met de verborgen laag en de verborgen laag is volledig verbonden met de invoer laag.
    + Het aantal knoop punten in de verborgen laag kan worden ingesteld door de gebruiker (de standaard waarde is 100).  
  
    Omdat het aantal knoop punten in de invoer laag wordt bepaald door het aantal functies in de trainings gegevens, kan er in een regressie model slechts één knoop punt in de uitvoer laag zijn.  
  
4. Voor het **aantal verborgen knoop punten** typt u het aantal verborgen knoop punten. De standaard waarde is één verborgen laag met 100 knoop punten. (Deze optie is niet beschikbaar als u een aangepaste architectuur definieert met behulp van net #.)
  
5.  Voor het **leer tempo** typt u een waarde die de stap definieert die bij elke herhaling moet worden uitgevoerd, vóór correctie. Een hogere waarde voor het leer tempo kan ertoe leiden dat het model sneller wordt geconvergeerd, maar dat het lokale minima kan overschrijden.

6.  Geef voor **aantal Learning-iteraties** het maximum aantal keren op dat het algoritme de trainings cases verwerkt.


8.  Voor **de impuls** typt u een waarde die tijdens het leren moet worden toegepast als gewicht op knoop punten uit eerdere iteraties.

10. Selecteer de optie, **wille keurige voor beelden** om de volg orde van de aanvragen tussen herhalingen te wijzigen. Als u deze optie uitschakelt, worden cases in precies dezelfde volg orde verwerkt telkens wanneer u de pijp lijn uitvoert.
  
11. Voor **wille keurig getal zaad** kunt u eventueel een waarde invoeren die als seed moet worden gebruikt. Het opgeven van een Seed-waarde is handig als u de Herhaal baarheid wilt garanderen voor uitvoeringen van dezelfde pijp lijn.
  
13. Verbind een trainings gegevensset en Train het model:

    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](train-model.md) .  
  
    + Als u de **modus trainer maken** instelt op het **parameter bereik**, verbindt u een gecodeerde gegevensset en traint u het model met behulp van [Hyper parameters model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de standaard waarde in de lijst met enkelvoudige para meters gebruikt.  
    > 
    > Als u één set parameter waarden doorgeeft aan de Hyper parameters-module van het model voor het [afstemmen](tune-model-hyperparameters.md) van een reeks instellingen voor elke para meter, worden de waarden genegeerd en worden de standaard waarden gebruikt voor de kenniser.  
    > 
    > Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.  
  
   
14. Verzend de pijp lijn.  

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

- Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van de module **Train model** . Selecteer het pictogram **gegevensset registreren** om het model als een herbruikbare module op te slaan.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 