---
title: 'Aanbeveling evalueren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module aanbevolen procedures evalueren in Azure Machine Learning om de nauw keurigheid van de voor spellingen van het aanbevolen model te evalueren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76312257"
---
# <a name="evaluate-recommender"></a>Aanbevelingsfunctie voor evaluatie

In dit artikel wordt beschreven hoe u de module aanbevolen procedures evalueren in Azure Machine Learning Designer gebruikt. Het doel is om de nauw keurigheid te meten van voor spellingen die een aanbevelings model heeft gedaan. Met deze module kunt u verschillende soorten aanbevelingen evalueren:  
  
-   Classificaties voor speld voor een gebruiker en een item    
-   Aanbevolen items voor een gebruiker  
  
Wanneer u voor spellingen maakt met behulp van een aanbevelings model, worden er enigszins verschillende resultaten geretourneerd voor elk van deze ondersteunde Voorspellings typen. In de module beoordeling aanbevelen wordt het type voor spelling afgeleid van de kolom indeling van de Score gegevensset. De gescoorde gegevensset kan bijvoorbeeld het volgende bevatten:

- Gebruikers-item-beoordeling drieën
- Gebruikers en de aanbevolen items

De module past ook de juiste prestatie gegevens toe, op basis van het type voor spellingen dat wordt gemaakt. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Aanbevolen evaluatie configureren

In de module beoordeling aanbevelen wordt de Voorspellings uitvoer vergeleken met behulp van een aanbevelings model met de bijbehorende gegevens van de "vloer waarheid". De module [Score SVD aanbevelen](score-svd-recommender.md) levert bijvoorbeeld gescoorde gegevens sets op die u kunt analyseren met behulp van de aanbeveling evalueren.

### <a name="requirements"></a>Vereisten

Aanbevolen wordt om de volgende gegevens sets als invoer te evalueren. 
  
#### <a name="test-dataset"></a>Gegevensset testen

De test gegevensset bevat de gegevens van de ' grond waarheid ' in de vorm van triples van gebruikers items-beoordeling.  

#### <a name="scored-dataset"></a>Score gegevensset

De gescoorde gegevensset bevat de voor spellingen die het aanbevolen model heeft gegenereerd.  
  
De kolommen in deze tweede gegevensset zijn afhankelijk van het soort voor spelling dat u tijdens het Score proces hebt uitgevoerd. De gescoorde gegevensset kan bijvoorbeeld een van de volgende bevatten:

- Gebruikers, items en de classificaties die de gebruiker waarschijnlijk voor het item zou geven
- Een lijst met gebruikers en items die hiervoor worden aanbevolen 

### <a name="metrics"></a>Metrische gegevens

Prestatie gegevens voor het model worden gegenereerd op basis van het type invoer. In de volgende secties vindt u meer informatie.

## <a name="evaluate-predicted-ratings"></a>Voorspelde classificaties evalueren  

Wanneer u voorspelde beoordelingen evalueert, moet de Score van de gescoorde gegevensset (de tweede invoer voor het evalueren van de aanbevolen functie) het volgende bevatten:
  
-   De eerste kolom van de gegevensset bevat de gebruikers-id's.    
-   De tweede kolom bevat de item-id's.  
-   De derde kolom bevat de bijbehorende beoordelingen van gebruikers items.  
  
> [!IMPORTANT] 
> Voor een geslaagde evaluatie moeten de kolom namen `User` respectievelijk, `Item` , en `Rating` .  
  
Met de aanbeveling evalueren wordt de classificaties in de gegevensset ' vloer waarheid ' vergeleken met de voorspelde classificaties van de gescoorde gegevensset. Vervolgens wordt de gemiddelde absolute fout (MAE) en de wortel fout (RMSE) berekend.



## <a name="evaluate-item-recommendations"></a>Aanbevelingen voor item evalueren

Wanneer u de aanbevelingen van een item evalueert, gebruikt u een gescoorde gegevensset die de aanbevolen items voor elke gebruiker bevat:
  
-   De eerste kolom van de gegevensset moet de gebruikers-id bevatten.    
-   Alle volgende kolommen moeten de bijbehorende aanbevolen item-id's bevatten, geordend op basis van de relevantie van een item voor de gebruiker. 

Voordat u deze gegevensset verbindt, wordt u aangeraden de gegevensset te sorteren zodat de meest relevante items het eerst worden geleverd.  

> [!IMPORTANT] 
> Als u wilt evalueren, kunt u het beste de kolom namen gebruiken, enzovoort `User` `Item 1` `Item 2` `Item 3` .  
  
Bij evaluatie van evalueren wordt de gemiddelde genormaliseerde cumulatieve winst (NDCG) berekend en geretourneerd in de uitvoer gegevensset.  
  
Omdat het niet mogelijk is om de werkelijke "vloer waarheid" te weten voor de aanbevolen items, moet u aanbevolen worden de beoordelingen van gebruikers items in de gegevensset test gebruiken als toename in de berekening van de NDCG. Om te evalueren, moet de Score module voor aanbevolen beoordelingen alleen aanbevelingen voor items met classificaties van ' vloer waarheid ' (in de test gegevensset) produceren.  
  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
