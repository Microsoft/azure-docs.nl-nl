---
title: 'Train SVD-aanbeveling: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Train SVD aanbevelen in Azure Machine Learning om een Bayesiaanse-aanbeveling te trainen met behulp van de SVD-algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 77407f253bb347160ea331bd7384d8085f21b040
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654455"
---
# <a name="train-svd-recommender"></a>Aanbevelingsfunctie van SVD-training

In dit artikel wordt beschreven hoe u de module Train SVD aanbevelen in Azure Machine Learning Designer gebruikt. Met deze module kunt u een aanbevelings model trainen op basis van het algoritme voor de ontleding van één waarde (SVD).  

De Train SVD Aanbevelener-module leest een gegevensset van de beoordeling van de gebruikers items. Er wordt een getrainde SVD-aanbeveling geretourneerd. U kunt het getrainde model vervolgens gebruiken om beoordelingen te voors pellen of aanbevelingen te genereren door de module [Score SVD aanbevelen](score-svd-recommender.md) te verbinden.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Meer informatie over aanbevelings modellen en de SVD-aanbeveling  

Het belangrijkste doel van een aanbevelings systeem is het aanbevelen van een of meer *items* aan *gebruikers* van het systeem. Voor beelden van een item zijn mogelijk een film, een restaurant, een boek of een nummer. Een gebruiker kan een persoon, een groep personen of een andere entiteit met voorkeurs instellingen voor item zijn.  

Er zijn twee belang rijke benaderingen voor aanbevolen systemen: 

+ Een **op inhoud gebaseerde** benadering maakt gebruik van functies voor zowel gebruikers als items. Gebruikers kunnen worden beschreven op basis van eigenschappen zoals leeftijd en geslacht. Items kunnen worden beschreven door eigenschappen als auteur en fabrikant. U vindt typische voor beelden van aanbevelingen op basis van inhoud op sociale matchmaking-sites. 
+ Voor **samen werking filteren** worden alleen id's van de gebruikers en de items gebruikt. Het ontvangt impliciete informatie over deze entiteiten van een (sparse) matrix met beoordelingen van de gebruikers aan de items. We hebben informatie over een gebruiker van de items die ze hebben geclassificeerd en van andere gebruikers die dezelfde items hebben geclassificeerd.  

De SVD-aanbevolen code gebruikt id's van de gebruikers en de items, en een matrix met beoordelingen van de gebruikers aan de items. Het is een *samen werkende aanbeveling*. 

Voor meer informatie over de SVD-aanbeveling raadpleegt u de relevante Research Paper: [matrix factorization technieken voor aanbevolen systemen](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Advies van Train SVD configureren  

### <a name="prepare-data"></a>Gegevens voorbereiden

Voordat u de module gebruikt, moeten de invoer gegevens de indeling hebben die het aanbevelings model verwacht. Er is een opleidings gegevensverzameling van de beoordeling van de gebruiker-item classificatie vereist.

+ De eerste kolom bevat gebruikers-id's.
+ De tweede kolom bevat item-id's.
+ De derde kolom bevat de classificatie voor het combi neren van gebruikers items. Classificatie waarden moeten numeriek type zijn.  

De gegevensset voor **film classificaties** in azure machine learning Designer ( **gegevens sets** selecteren en vervolgens **steek proeven**) toont de verwachte indeling:

![Filmbeoordelingen](media/module/movie-ratings-dataset.png)

In dit voor beeld ziet u dat één gebruiker verschillende films heeft beoordeeld. 

### <a name="train-the-model"></a>Het model trainen

1.  Voeg de module Train SVD Adviseering toe aan uw pijp lijn in de ontwerp functie en verbind deze met de trainings gegevens.  
   
2.  Geef voor **aantal factoren** het aantal factoren op dat met de aanbevolen code moet worden gebruikt.  
    
    Elke factor meet de hoeveelheid van de gebruiker met betrekking tot het item. Het aantal factoren is ook de dimensionaliteit van de latente factor ruimte. Wanneer het aantal gebruikers en items toeneemt, is het beter om een groter aantal factoren in te stellen. Maar als het aantal te groot is, kunnen de prestaties verloren.
    
3.  Het **aantal iteraties van aanbevelings algoritmen** geeft aan hoe vaak de invoer gegevens moeten worden verwerkt door de algoritme. Hoe hoger dit aantal, hoe nauw keuriger de voor spellingen zijn. Een hoger nummer betekent echter een tragere training. De standaardwaarde is 30.

4.  Voer voor het **leer tempo** een getal in tussen 0,0 en 2,0 dat de stap grootte voor Learning definieert.

    Het leer tempo bepaalt de grootte van de stap bij elke iteratie. Als de grootte van de stap te groot is, is het mogelijk dat u de optimale oplossing kunt overschrijden. Als de grootte van de stap te klein is, is de training langer nodig om de beste oplossing te vinden. 
  
5.  Verzend de pijp lijn.  

## <a name="results"></a>Resultaten

Wanneer de uitvoering van de pijp lijn is voltooid en u het model voor scores wilt gebruiken, verbindt u de [training van Train SVD](train-svd-recommender.md) om de [SVDer](score-svd-recommender.md)aan te bevelen om waarden te voors pellen voor nieuwe invoer voorbeelden.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
