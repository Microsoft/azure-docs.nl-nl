---
title: 'Aanbeveling van Score SVD: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Score SVD recommending in Azure Machine Learning voor voor spellingen van aanbevolen scores voor een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90893702"
---
# <a name="score-svd-recommender"></a>Aanbevelingsfunctie voor SVD-score

In dit artikel wordt beschreven hoe u de module Score SVD aanbevelen in Azure Machine Learning Designer gebruikt. Gebruik deze module om voor spellingen te maken met behulp van een getraind aanbevelings model op basis van het algoritme voor de ontleding van één waarde (SVD).

De SVD-aanbevolen functie kan twee verschillende soorten voor spellingen genereren:

- [Beoordelingen voor een bepaalde gebruiker en een bepaald item voors pellen](#prediction-of-ratings)
- [Het aanbevelen van items aan een gebruiker](#recommendations-for-users)

Wanneer u het tweede type voor spellingen maakt, kunt u in een van de volgende modi worden gebruikt:

- De **productie modus** houdt rekening met alle gebruikers of items. Deze wordt doorgaans gebruikt in een webservice.

  U kunt scores maken voor nieuwe gebruikers, niet alleen voor gebruikers die tijdens de training zijn gezien. Raadpleeg de [technische opmerkingen](#technical-notes)voor meer informatie. 

- De **evaluatie modus** werkt met een beperkt aantal gebruikers of items die kunnen worden geëvalueerd. Deze wordt doorgaans gebruikt tijdens pijplijn bewerkingen.

Voor meer informatie over de SVD-aanbevolen algoritme raadpleegt u de Research paper [matrix factorization technieken voor aanbevolen systemen](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Advieser voor Score SVD configureren

Deze module ondersteunt twee typen voor spellingen, elk met verschillende vereisten. 

###  <a name="prediction-of-ratings"></a>Voor spelling van classificaties

Wanneer u de classificaties voor spelt, wordt in het model berekend hoe een gebruiker reageert op een bepaald item, op basis van de trainings gegevens. De invoer gegevens voor de Score moeten zowel een gebruiker als het te beoordelen item bevatten.

1. Voeg een getraind aanbevelings model aan uw pijp lijn toe en verbind het met de **getrainde SVD-aanbeveling**. U moet het model maken met de module [Train SVD aanbevelen](train-SVD-recommender.md) .

2. Voor **Voorspellings type voor aanbevolen** functies selecteert u **beoordelings voorspelling**. Er zijn geen andere para meters vereist.

3. Voeg de gegevens toe waarvoor u voor spellingen wilt maken en verbind deze met de **gegevensset om** deze te beoordelen.

   Voor het model om beoordelingen te voors pellen, moet de invoer gegevensset gebruikers paren bevatten.

   De gegevensset kan een optionele derde kolom met beoordelingen bevatten voor het combi neren van gebruikers items in de eerste en tweede kolommen. Maar de derde kolom wordt genegeerd tijdens de voor spelling.

4. Verzend de pijp lijn.

### <a name="results-for-rating-predictions"></a>Resultaten voor de beoordeling van voor spellingen 

De uitvoer gegevensset bevat drie kolommen: gebruikers, items en de voorspelde classificatie voor elke invoer gebruiker en elk item.

###  <a name="recommendations-for-users"></a>Aanbevelingen voor gebruikers 

Als u items wilt aanbevelen voor gebruikers, geeft u een lijst met gebruikers en items op als invoer. Op basis van deze gegevens gebruikt het model de kennis over bestaande items en gebruikers om een lijst met items te genereren met een waarschijnlijk beroep op elke gebruiker. U kunt het aantal geretourneerde aanbevelingen aanpassen. En u kunt een drempel instellen voor het aantal eerdere aanbevelingen dat vereist is voor het genereren van een aanbeveling.

1. Voeg een getraind aanbevelings model aan uw pijp lijn toe en verbind het met de **getrainde SVD-aanbeveling**.  U moet het model maken met de module [Train SVD aanbevelen](train-svd-recommender.md) .

2. Als u items wilt aanbevelen voor een lijst met gebruikers, stelt u **Voorspellings typen voor aanbevolen** functies in op **item aanbeveling**.

3. Geef aan of u de Score module in productie of voor model evaluatie gebruikt voor de **Aanbevolen selectie van items**. Kies een van deze waarden:

    - **Van alle items**: Selecteer deze optie als u een pijp lijn hebt ingesteld die u wilt gebruiken in een webservice of in de productie omgeving.  Met deze optie schakelt u de *productie modus* in. De module maakt aanbevelingen van alle items die tijdens de training worden weer gegeven.

    - **Van geclassificeerde items (voor model evaluatie)**: Selecteer deze optie als u een model ontwikkelt of test. Met deze optie wordt de *evaluatie modus* ingeschakeld. In de module worden alleen aanbevelingen gedaan van de items in de ingevoerde gegevensset die zijn geclassificeerd.
    
    - **Van niet-geclassificeerde items (om nieuwe items voor te stellen voor gebruikers)**: Selecteer deze optie als u wilt dat de module alleen aanbevelingen van de items in de trainings gegevensset bevat die niet zijn geclassificeerd. 

4. Voeg de gegevensset toe waarvoor u voor spellingen wilt maken en verbind deze met de **gegevensset om** deze te beoordelen.

    - Voor **van alle items** moet de invoer gegevensset bestaan uit één kolom. Het bevat de id's van gebruikers voor wie aanbevelingen moeten worden gedaan.

      De gegevensset kan een extra twee kolommen met item-id's en classificaties bevatten, maar deze twee kolommen worden genegeerd. 

    - Voor **van geclassificeerde items (voor model evaluatie)** moet de invoer gegevensset bestaan uit gebruikers paren. De eerste kolom moet de gebruikers-id bevatten. De tweede kolom moet de bijbehorende item-id's bevatten.

      De gegevensset kan een derde kolom van classificaties van gebruikers items bevatten, maar deze kolom wordt genegeerd.

    - Voor **van niet-geclassificeerde items (om nieuwe items voor test Ellen voor gebruikers)**, moet de invoer gegevensset bestaan uit gebruikers paren. De eerste kolom moet de gebruikers-id bevatten. De tweede kolom moet de bijbehorende item-id's bevatten.

     De gegevensset kan een derde kolom van classificaties van gebruikers items bevatten, maar deze kolom wordt genegeerd.

5. **Maximum aantal items dat aan een gebruiker kan worden aanbevolen**: Voer het aantal items in dat voor elke gebruiker moet worden geretourneerd. Standaard raadt de module vijf items aan.

6. **Minimum grootte van de aanbevelings groep per gebruiker**: Voer een waarde in die aangeeft hoeveel eerdere aanbevelingen vereist zijn. Deze para meter is standaard ingesteld op 2, wat betekent dat ten minste twee andere gebruikers het item hebben aanbevolen.

   Gebruik deze optie alleen als u een score hebt in de evaluatie modus. De optie is niet beschikbaar als u **van alle items** of **van niet-geclassificeerde items (om nieuwe items te suggereren voor gebruikers)** selecteert.

7.  Voor **van niet-geclassificeerde items (om nieuwe items te suggereren voor gebruikers)**, gebruikt u de derde invoer poort, met de naam **trainings gegevens**, om items te verwijderen die al zijn geclassificeerd op basis van de Voorspellings resultaten.

    Als u dit filter wilt Toep assen, koppelt u de oorspronkelijke trainings gegevensset aan de invoer poort.

8. Verzend de pijp lijn.

### <a name="results-of-item-recommendation"></a>Resultaten van aanbeveling van het item

De gescoorde gegevensset die door Score SVD wordt geretourneerd, vermeldt de aanbevolen items voor elke gebruiker:

- De eerste kolom bevat de gebruikers-id's.
- Er worden een aantal extra kolommen gegenereerd, afhankelijk van de waarde die u hebt ingesteld voor het **maximum aantal items dat aan een gebruiker** moet worden aanbevolen. Elke kolom bevat een aanbevolen item (per id). De aanbevelingen worden geordend op basis van de gebruikers affiniteit. Het item met de hoogste affiniteit wordt in kolom **item 1** geplaatst.


##  <a name="technical-notes"></a>Technische opmerkingen

Als u een pijp lijn hebt met de SVD-aanbeveling en u het model naar productie verplaatst, moet u er rekening mee houden dat er belang rijke verschillen zijn tussen het gebruik van de aanbevolen functie in de evaluatie modus en het gebruik ervan in de productie modus.

Voor evaluatie, per definitie, zijn voor spellingen vereist die kunnen worden gecontroleerd tegen de achterliggende *waarheid* in een testset. Wanneer u de aanbevolen functie evalueert, moet deze alleen items voors pellen die zijn geclassificeerd in de testset. Hiermee beperkt u de mogelijke waarden die worden voor speld.

Wanneer u het model operationeel maken, wijzigt u doorgaans de Voorspellings modus om aanbevelingen te doen op basis van alle mogelijke items, zodat u de beste voor spellingen krijgt. Voor veel van deze voor spellingen is er geen overeenkomende grond waarheid. De nauw keurigheid van de aanbeveling kan dus niet worden gecontroleerd op dezelfde manier als tijdens pijplijn bewerkingen.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
