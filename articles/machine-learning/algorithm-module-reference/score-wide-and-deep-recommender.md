---
title: De diepte-module & diep aanbevolen Score gebruiken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Score Wide & diep aanbevolen in Azure Machine Learning voor voor spellingen van aanbevelingen voor een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905172"
---
# <a name="score-wide-and-deep-recommender"></a>Aanbevelingsfunctie voor wide en deep learning-scores

In dit artikel wordt beschreven hoe u de **Score breedte en de diep aanbevolen** module in azure machine learning Designer kunt gebruiken om voor spellingen te maken op basis van een getraind aanbevelings model, gebaseerd op het brede & uitgebreide leer proces van Google.

Met de brede en diepe aanbeveling kunt u twee verschillende soorten voor spellingen genereren:

- [Beoordelingen voor een bepaalde gebruiker en een bepaald item voors pellen](#predict-ratings)

- [Het aanbevelen van items aan een bepaalde gebruiker](#recommend-items)


Wanneer u het laatste type voor spellingen maakt, kunt u in de *productie modus* of de *evaluatie modus* uitvoeren.

- De **productie modus** houdt rekening met alle gebruikers of items en wordt doorgaans gebruikt in een webservice. U kunt scores maken voor nieuwe gebruikers, niet alleen voor gebruikers die tijdens de training zijn gezien. 

- De **evaluatie modus** werkt op een beperkt aantal gebruikers of items die kunnen worden geëvalueerd en wordt doorgaans gebruikt tijdens het experimenteren.

Meer informatie over de brede en diepe aanbeveling en de onderliggend theorie vindt u in het relevante Research Paper:  [Wide & diepe learning voor aanbevolen systemen](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Score breedte en diepe aanbeveling configureren

Deze module ondersteunt verschillende soorten aanbevelingen, elk met verschillende vereisten. Klik op de koppeling voor het type gegevens dat u hebt en het type aanbeveling dat u wilt maken.

+ [Voor spel classificaties](#predict-ratings)
+ [Aanbevolen items](#recommend-items)

### <a name="predict-ratings"></a>Voor spel classificaties

Wanneer u de classificaties voor spelt, wordt in het model berekend hoe een bepaalde gebruiker reageert op een bepaald item, op basis van de trainings gegevens. Daarom moeten de invoer gegevens voor de Score zowel een gebruiker als het te beoordelen item bevatten.

1. Voeg een getraind breed & diep aanbevolen model toe aan uw experiment en verbind deze met het **getrainde brede en diepe aanbevelings model**.  U moet het model maken met behulp van een [brede en diepe aanbevolen trainer](train-wide-and-deep-recommender.md).

2. **Voorspellings soort aanbevolen**: Selecteer **beoordelings voorspelling**. Er zijn geen verdere para meters vereist.

3. Voeg de gegevens toe waarvoor u de voor spellingen wilt maken en verbind deze met de **gegevensset voor de Score**.

    Om beoordelingen te voors pellen, moet de invoer gegevensset gebruikers paren bevatten.

    De gegevensset kan een optionele derde kolom met beoordelingen bevatten voor het combi neren van gebruikers items in de kolommen eerste en tweede, maar de derde kolom wordt genegeerd tijdens de voor spelling.

4.  (Optioneel). Als u een gegevensset van gebruikers functies hebt, verbindt u deze met **gebruikers functies**.

    De gegevensset van gebruikers functies moet de gebruikers-id in de eerste kolom bevatten. De overige kolommen moeten waarden bevatten die de gebruikers kenmerken, zoals geslacht, voor keuren, locatie, enzovoort.
  
    Functies van gebruikers die items hebben geclassificeerd in de trainings gegevensset, worden genegeerd door een **Score breed en diep aanbevolen**, omdat ze tijdens de training al zijn geleerd. Filter daarom uw gegevensset vooraf om alleen gebruikers met een *koude start* te gebruiken of gebruikers die geen items hebben geclassificeerd.

    > [!WARNING]
    > Als het model is getraind zonder gebruikers functies te gebruiken, kunt u geen gebruikers functies introduceren tijdens het scoren.

5. Als u een gegevensset van item functies hebt, kunt u deze verbinden met **item functies**.

    De gegevensset van het item bevat een item-id in de eerste kolom. De overige kolommen moeten waarden bevatten die de items kenmerken.

    Functies van geclassificeerde items in de trainings gegevensset worden genegeerd door de **Score breed en de diep gaande aanbeveling** , omdat deze al zijn geleerd tijdens de training. Beperk daarom uw score gegevensset voor *koude start items* of items die door geen enkele gebruiker zijn geclassificeerd.

    > [!WARNING]
    > Als het model is getraind zonder gebruik te maken van de functie onderdelen, kunt u geen artikel functies introduceren tijdens het scoren.

7. Voer het experiment uit.

### <a name="results-for-rating-predictions"></a>Resultaten voor de beoordeling van voor spellingen 

De uitvoer gegevensset bevat drie kolommen met de gebruiker, het item en de voorspelde classificatie voor elke invoer gebruiker en elk item.

Daarnaast worden de volgende wijzigingen toegepast tijdens de Score:

-  Ontbrekende waarden voor de functie kolom numeriek gebruiker of item worden automatisch vervangen door het **gemiddelde** van de niet-ontbrekende trainings waarden. Voor de functie categorische worden ontbrekende waarden vervangen door dezelfde categorische-waarde dan alle mogelijke waarden van deze functie.
-  Er wordt geen vertaling toegepast op de functie waarden om hun sparsity te behouden.

### <a name="recommend-items"></a>Aanbevolen items

Als u items wilt aanbevelen voor gebruikers, geeft u een lijst met gebruikers en items op als invoer. Op basis van deze gegevens gebruikt het model de kennis over bestaande items en gebruikers om een lijst met items te genereren met een waarschijnlijk beroep op elke gebruiker. U kunt het aantal geretourneerde aanbevelingen aanpassen en een drempel instellen voor het aantal eerdere aanbevelingen dat vereist is om een aanbeveling te genereren.

1. Voeg een getraind breed en diep aanbevolen model toe aan uw experiment en verbind deze met het **getrainde brede en diepe aanbevelings model**.  U moet het model maken met behulp van een [brede en diepe aanbevolen trainer](train-wide-and-deep-recommender.md).

2. Als u items wilt aanbevelen voor een bepaalde lijst met gebruikers, stelt u de **Voorspellings soort voor aanbevolen** functies in op **item aanbeveling**.

3. **Aanbevolen item selecteren**: Geef aan of u de Score module in productie of voor model evaluatie gebruikt door een van deze waarden te kiezen:

    - **Van geclassificeerde items (voor model evaluatie)**: Selecteer deze optie als u een model ontwikkelt of test. Met deze optie wordt de **evaluatie modus** ingeschakeld. in de module worden alleen aanbevelingen gedaan van die items in de ingevoerde gegevensset die zijn geclassificeerd.
    - **Van alle items**: Selecteer deze optie als u een experiment wilt instellen voor gebruik in een webservice of productie omgeving.  Met deze optie schakelt u de **productie modus** in. de module maakt aanbevelingen van alle items die tijdens de training worden weer gegeven.
    - **Van niet-geclassificeerde items (om nieuwe items voor te stellen voor gebruikers)**: Selecteer deze optie als u wilt dat de module alleen aanbevelingen van de items in de trainings gegevensset bevat die niet zijn geclassificeerd. 
4. Voeg de gegevensset toe waarvoor u voor spellingen wilt maken en verbind deze met de **gegevensset om** deze te beoordelen.

    - Als u kiest voor de optie **van alle items**, moet de invoer gegevensset bestaan uit één en slechts één kolom die de id's van gebruikers bevat waarvoor aanbevelingen moeten worden gedaan.

        De gegevensset kan een extra twee kolommen met item-id's en classificaties bevatten, maar deze twee kolommen worden genegeerd. 

    - Als u kiest voor de optie, **van nominale items (voor model evaluatie)**, moet de invoer gegevensset bestaan uit **gebruikers paren**. De eerste kolom moet de **gebruikers** -id bevatten. De tweede kolom moet de bijbehorende **item** -id's bevatten.

        De gegevensset kan een derde kolom van classificaties van gebruikers items bevatten, maar deze kolom wordt genegeerd.
        
    - Voor **van niet-geclassificeerde items (om nieuwe items voor test Ellen voor gebruikers)**, moet de invoer gegevensset bestaan uit gebruikers paren. De eerste kolom moet de gebruikers-id bevatten. De tweede kolom moet de bijbehorende item-id's bevatten.

        De gegevensset kan een derde kolom van classificaties van gebruikers items bevatten, maar deze kolom wordt genegeerd.

5. (Optioneel). Als u een gegevensset van **gebruikers functies** hebt, verbindt u deze met **gebruikers functies**.

    De eerste kolom in de gegevensset voor gebruikers functies moet de gebruikers-id bevatten. De overige kolommen moeten waarden bevatten die de gebruiker kenmerken, zoals geslacht, voor keuren, locatie, enzovoort.

    Functies van gebruikers die geclassificeerde items hebben, worden genegeerd door de **Score breed en diep aanbevolen**, omdat deze functies tijdens de training al zijn geleerd. Daarom kunt u uw gegevensset vooraf filteren om alleen *koude gebruikers* toe te voegen of gebruikers die geen items hebben geclassificeerd.

    > [!WARNING]
    >  Als het model is getraind zonder gebruikers functies te gebruiken, kunt u geen onderdelen Toep assen gebruiken tijdens de score.

6. Beschrijving Als u een gegevensset van **item functies** hebt, kunt u deze verbinden met **item functies**.

    De eerste kolom in de gegevensset item functies moet de item-id bevatten. De overige kolommen moeten waarden bevatten die de items kenmerken.

    Functies van geclassificeerde items worden genegeerd door de **Score breed en diep aanbevolen**, omdat deze functies tijdens de training al zijn geleerd. Daarom kunt u uw score gegevensset beperken tot *koude start items* of items die niet door gebruikers zijn geclassificeerd.

    > [!WARNING]
    >  Als het model is getraind zonder de onderdelen van het item te gebruiken, mag u geen item functies gebruiken als u een score gebruikt.  

7. **Maximum aantal items dat aan een gebruiker** kan worden aanbevolen: Typ het aantal items dat voor elke gebruiker moet worden geretourneerd. Standaard worden 5 items aanbevolen.

8. **Minimum grootte van de aanbevelings groep per gebruiker**: Typ een waarde die aangeeft hoeveel eerdere aanbevelingen zijn vereist.  Deze para meter is standaard ingesteld op 2, wat betekent dat het item moet worden aanbevolen door ten minste twee andere gebruikers.

    Deze optie moet alleen worden gebruikt als u een score in de evaluatie modus hebt. De optie is niet beschikbaar als u **van alle items** of **van niet-geclassificeerde items (om nieuwe items te suggereren voor gebruikers)** selecteert.

9. Voor **van niet-geclassificeerde items (om nieuwe items te suggereren voor gebruikers)**, gebruikt u de derde invoer poort, met de naam **trainings gegevens**, om items te verwijderen die al zijn geclassificeerd op basis van de Voorspellings resultaten.

    Als u dit filter wilt Toep assen, koppelt u de oorspronkelijke trainings gegevensset aan de invoer poort.

10. Voer het experiment uit.
### <a name="results-of-item-recommendation"></a>Resultaten van aanbeveling van het item

De gescoorde gegevensset die wordt geretourneerd door de **Score breed en de diep gaande aanbeveling** geeft een lijst van de aanbevolen items voor elke gebruiker.

- De eerste kolom bevat de gebruikers-id's.
- Er worden een aantal extra kolommen gegenereerd, afhankelijk van de waarde die u hebt ingesteld voor het **maximum aantal items dat aan een gebruiker** moet worden aanbevolen. Elke kolom bevat een aanbevolen item (per id). De aanbevelingen worden geordend op basis van de gebruikers-item affiniteit, waarbij het item met de hoogste affiniteit in kolom, **item 1**, is geplaatst.

##  <a name="technical-notes"></a>Technische opmerkingen

In deze sectie vindt u antwoorden op enkele veelgestelde vragen over het gebruik van de brede & diep aanbevolen om voor spellingen te maken.  

###  <a name="cold-start-users-and-recommendations"></a>Gebruikers en aanbevelingen voor koude start

Als u aanbevelingen wilt maken, moet u voor de module **Score Wide en diepe aanbevolen** dezelfde invoer gebruiken als bij het trainen van het model, met inbegrip van een gebruikers-id. Dat komt doordat het algoritme moet weten of er tijdens de training iets over deze gebruiker is geleerd. 

Voor nieuwe gebruikers hebt u echter mogelijk geen gebruikers-ID, maar alleen enkele gebruikers functies, zoals leeftijd, geslacht, enzovoort.

U kunt nog steeds aanbevelingen maken voor gebruikers die nieuw zijn op uw systeem door ze te verwerken als gebruikers met een *koude start*. Voor dergelijke gebruikers gebruikt het aanbevelings algoritme geen eerdere geschiedenis of vorige beoordelingen, alleen gebruikers functies.

Voor de voor spelling is een koude start gebruiker gedefinieerd als een gebruiker met een ID die niet is gebruikt voor training. Om ervoor te zorgen dat de Id's niet overeenkomen met de Id's die in de training worden gebruikt, kunt u nieuwe id's maken. U kunt bijvoorbeeld wille keurige Id's binnen een opgegeven bereik genereren of een reeks Id's vooraf toewijzen voor gebruikers met een koude start.

Als u echter geen gezamenlijke filter gegevens hebt, zoals een vector van gebruikers functies, is het beter om een classificatie of regressie meer te gebruiken.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Productie gebruik van de brede en diepe aanbeveling

Als u met de brede en diepe aanbeveling hebt geexperimenteerd en vervolgens het model naar productie hebt verplaatst, moet u rekening houden met de volgende belang rijke verschillen bij het gebruik van de aanbeveling in de evaluatie modus en in de productie modus:

- Voor evaluatie, per definitie, zijn voor spellingen vereist die kunnen worden gecontroleerd tegen de achterliggende *waarheid* in een testset. Wanneer u de aanbevolen functie evalueert, moet u daarom alleen items voors pellen die zijn geclassificeerd in de testset. Dit kan nood zakelijk zijn om de mogelijke waarden te beperken die worden voor speld.

    Wanneer u echter het model operationeel maken, wijzigt u doorgaans de Voorspellings modus om aanbevelingen te doen op basis van alle mogelijke items, om de beste voor spellingen te krijgen. Voor veel van deze voor spellingen is er geen overeenkomende grond waarheid, waardoor de nauw keurigheid van de aanbeveling niet op dezelfde manier kan worden gecontroleerd als tijdens het experimenteren.

- Als u geen gebruikers-ID opgeeft in productie en alleen een functie Vector levert, kunt u een lijst met alle aanbevelingen voor alle mogelijke gebruikers ontvangen. Zorg ervoor dat u een gebruikers-ID opgeeft.

    Als u het aantal geretourneerde aanbevelingen wilt beperken, kunt u ook het maximum aantal items opgeven dat per gebruiker wordt geretourneerd. 



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met beschik bare modules](module-reference.md) van Azure machine learning. 
