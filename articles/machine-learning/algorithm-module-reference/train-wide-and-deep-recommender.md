---
title: De module Wide & diep aanbevolen trainen gebruiken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Wide & diep aanbevolen in Azure Machine Learning Designer voor het trainen van een aanbevelings model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655373"
---
# <a name="train-wide--deep-recommender"></a>Uitgebreide aanbevolen &er trainen
In dit artikel wordt beschreven hoe u de module **train & diep aanbevolen** in azure machine learning Designer kunt gebruiken om een aanbevelings model te trainen. Deze module is gebaseerd op brede & diep gaande lessen, dat door Google wordt voorgesteld.

De **Uitdiepse aanbeveling van de trein &** een gegevensset leest over de beoordeling van de gebruikers-item classificatie en, optioneel, sommige gebruikers-en item functies. Er wordt een getraind breed & diep aanbevolen.  U kunt het getrainde model vervolgens gebruiken om waarderings voorspellingen of aanbevelingen te genereren met behulp van de module voor de [Score breedte en de diep aanbevolener](score-wide-and-deep-recommender.md) .  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Meer informatie over aanbevelings modellen en de uitgebreide aanbevolen &er  

Het belangrijkste doel van een aanbevelings systeem is het aanbevelen van een of meer *items* aan *gebruikers* van het systeem. Voor beelden van een item kunnen een film, een restaurant, een boek of een nummer zijn. Een gebruiker kan een persoon, een groep personen of een andere entiteit met voorkeurs instellingen voor item zijn.  

Er zijn twee belang rijke benaderingen voor aanbevolen systemen. 

+ De eerste is de **op inhoud gebaseerde** benadering, waarmee u functies voor zowel gebruikers als items kunt gebruiken. Gebruikers kunnen worden beschreven door eigenschappen zoals leeftijd en geslacht, en items kunnen worden beschreven door eigenschappen als auteur en fabrikant. Typische voor beelden van systemen met aanbeveling op basis van inhoud zijn te vinden op de sociale matchmaking-sites. 
+ De tweede benadering is het **samen werken met filters**, waarbij alleen id's van de gebruikers en de items worden gebruikt en impliciete informatie over deze entiteiten wordt opgehaald uit een (sparse) matrix met beoordelingen van de gebruikers aan de items. We hebben meer informatie over een gebruiker van de items die ze hebben geclassificeerd en van andere gebruikers die dezelfde items hebben geclassificeerd.  

De uitgebreidere & diep aanbevolen deze benaderingen te combi neren met behulp van gezamenlijke filtering met een op inhoud gebaseerde aanpak. Het wordt daarom als een **hybride aanbeveling** beschouwd. 

Hoe dit werkt: wanneer een gebruiker relatief nieuw is voor het systeem, worden de voor spellingen verbeterd door gebruik te maken van de functie-informatie over de gebruiker, waardoor het probleem bekend ' koud-start ' wordt opgelost. Als u echter een voldoende aantal classificaties van een bepaalde gebruiker hebt verzameld, is het mogelijk om volledig gepersonaliseerde voor spellingen te maken op basis van hun specifieke beoordelingen, in plaats van alleen op hun eigen functies. Daarom is er sprake van een soepele overgang van aanbevelingen op basis van inhoud naar aanbevelingen op basis van gezamenlijke filtering. Zelfs als er geen gebruikers-of item functies beschikbaar zijn, werkt brede & diep aanbevolen in de modus voor samen werking.  

Meer informatie over de uitgebreide aanbevolen & dieper en het onderliggende Probabilistic-algoritme vindt u in het relevante Research Paper: [Wide & diepe learning voor aanbevolen systemen](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>& diep aanbevolen voor een Train configureren  

+ [De trainings gegevens voorbereiden](#prepare-data)
+ [Het model trainen](#train-the-model)

### <a name="prepare-data"></a>Gegevens voorbereiden

Voordat u probeert de module te gebruiken, is het essentieel dat uw gegevens de indeling hebben die wordt verwacht door het aanbevelings model. Er is een opleidings gegevensverzameling van de **beoordeling van gebruikers items en beoordelingen** vereist, maar u kunt ook gebruikers functies en item functies (indien beschikbaar) opnemen in afzonderlijke gegevens sets.

#### <a name="required-dataset-of-user-item-ratings"></a>Vereiste gegevensset van gebruiker-item-classificaties

De invoer gegevens die worden gebruikt voor de training moeten het juiste type gegevens in de juiste indeling bevatten: 

+ De eerste kolom moet gebruikers-id's bevatten.
+ De tweede kolom moet item-id's bevatten.
+ De derde kolom bevat de classificatie voor het combi neren van gebruikers items. Classificatie waarden moeten numeriek type zijn. 

Een typische set gebruikers-item-classificaties kan er bijvoorbeeld als volgt uitzien:

|UserId|MovieId|Waardering|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Gegevensset voor gebruikers functies (optioneel)

De gegevensset van **gebruikers functies** moet id's voor gebruikers bevatten en dezelfde id's gebruiken die in de eerste kolom van de gegevensset gebruikers-items-classificaties zijn opgenomen. De resterende kolommen kunnen een wille keurig aantal functies bevatten waarin de gebruikers worden beschreven.  

Een typische set gebruikers kan er bijvoorbeeld als volgt uitzien: 

|UserId|Leeftijd|Geslacht|Lend|Locatie|
|------------|--------------|-----------------------|---------------|------------|
|1|25|man| Drama    |Europa|
|223|40|vrouwelijk|Romance (|Azië|

#### <a name="item-features-dataset-optional"></a>Gegevensset voor item functies (optioneel)

De gegevensset van item functies moet item-id's in de eerste kolom bevatten. De resterende kolommen kunnen een wille keurig aantal beschrijvende functies voor de items bevatten.  

Een typische set met item functies kan er bijvoorbeeld als volgt uitzien:  

|MovieId|Titel|Oorspronkelijke taal|Genres|Year|
|-------------|-------------|-------------------|-----------|---------------|
|68646|De Godfather|Engels|Drama|1972|
|31381|Met de wind weg|Engels|Geschiedenis|1939|

### <a name="train-the-model"></a>Het model trainen

1.  Voeg de module **breed en diep aanbevolen trainer** toe aan uw experiment in de Designer en verbind deze met de gegevensset van de training.  
  
2. Als u een afzonderlijke gegevensset hebt van gebruikers onderdelen en/of de functies van het item, verbindt u deze met de module **Wide Train en diep aanbevolen** .  
  
    - **Gegevensset voor gebruikers functies**: Verbind de gegevensset waarmee gebruikers worden beschreven in de tweede invoer.
    - **Item functie gegevensset**: Verbind de gegevensset waarmee items worden beschreven in de derde invoer.  
    
3.  **Epoches**: Geef aan hoe vaak het algoritme de volledige trainings gegevens moet verwerken. 

    Hoe hoger dit nummer, hoe beter de training. de trainings kosten zijn echter meer tijd en kunnen leiden tot overmontage.

4. **Batch grootte**: Typ het aantal trainings voorbeelden dat in één trainings stap wordt gebruikt. 

     Deze afstemming kan invloed hebben op de snelheid van de training. Een hogere Batch grootte leidt tot een minder tijdrovende kosten-epoche, maar kan de convergentie tijd verg Roten. En als batch te groot is voor GPU/CPU, treedt er een geheugen fout op.

5.  **Wide Part Optimizer**: Selecteer één Optimizer om verlopen toe te passen op het brede deel van het model.

6.  **Breed optimalisatie leer tempo**: Voer een getal in tussen 0,0 en 2,0 dat het leer tempo van de Wide Part Optimizer definieert.

    Deze afstemming bepaalt de grootte van de stap bij elke trainings stap, terwijl u naar een minimum van de verlies functie gaat. Een te groot leer tempo kan leiden tot meer informatie over de minima, terwijl een te klein leer tempo het convergentie probleem kan veroorzaken.

7.  **Dimensie gekruiste functie**: Typ de dimensie door de gewenste gebruikers-id's en de onderdelen van de item-id in te voeren. 

    Met de uitgebreide aanbevolen & diep gaande functie wordt de trans formatie op basis van gebruikers-id en item-id standaard uitgevoerd. Het gekruiste resultaat wordt gehasht op basis van dit aantal om ervoor te zorgen dat de dimensie.

8.  **Optimizer van diepe onderdelen**: Selecteer één Optimizer om verlopen toe te passen op het diepe deel van het model.

9.  **Diep optimalisatie-leer tempo**: Voer een getal in tussen 0,0 en 2,0 dat het leer tempo van het diepe deel Optimizer definieert.

10.  **Dimensie** voor het insluiten van gebruikers: Typ een geheel getal om de dimensie van het insluiten van gebruikers-id's op te geven.

     De Wide & diepe aanbeveling maakt de gedeelde gebruikers-id-insluitingen en item-id-insluitingen voor zowel een breed deel als een diep rijk deel.

11.  **Artikel insluitings dimensie**: Typ een geheel getal om de dimensie van het insluiten van item-id's op te geven.

12.  **Categorische-functies dimensie insluiten**: Voer een geheel getal in om de afmetingen van de categorische-functie insluitingen op te geven.

     In een diepe component van Wide & diep aanbevolen wordt een insluitings vector voor elke categorische-functie beschreven. En deze insluitings vectoren delen dezelfde dimensie.

13.  **Verborgen eenheden**: Typ het aantal verborgen knoop punten van een diep onderdeel. Het knoop punt nummer in elke laag wordt gescheiden door komma's. Bijvoorbeeld: op type ' 1.000.500.100 ' geeft u op dat het diepe onderdeel drie lagen heeft, waarbij de eerste laag respectievelijk 1000 knoop punten, 500 knoop punten en 100 knoop punten heeft.

14.  **Activerings functie**: Selecteer één activerings functie die op elke laag wordt toegepast. de standaard waarde is ReLU.

15.  **Dropout**: Voer een getal tussen 0,0 en 1,0 in om de kans te bepalen dat de uitvoer in elke laag tijdens de training wordt verwijderd.

     Dropout is een regularisatie-methode om te voor komen dat Neural-netwerken worden overpast. Een gemeen schappelijk besluit voor deze waarde is om te beginnen met 0,5, wat bijna goed lijkt voor een breed scala aan netwerken en taken.

16.  **Batch normalisatie**: Selecteer deze optie om batch normalisatie na elke verborgen laag in het diepe onderdeel te gebruiken.

     Batch normalisatie is een techniek voor het bestrijden van het interne covariate-verschuivings probleem tijdens netwerk training. Over het algemeen kan het helpen de snelheid, prestaties en stabiliteit van de netwerken te verbeteren. 

17.  Voer de pijplijn uit.

## <a name="results"></a>Resultaten

Wanneer de uitvoering van de pijp lijn is voltooid, kunt u het model voor scores gebruiken door de [brede en diep gaande training](train-wide-and-deep-recommender.md) te verbinden met [Score breed en diep aanbevolen](score-wide-and-deep-recommender.md), om waarden te voors pellen voor nieuwe invoer voorbeelden.

##  <a name="technical-notes"></a>Technische opmerkingen

De brede &e diep gaande treinen en diepe Neural netwerken om de sterke mate van het onthouden en generaliseren te combi neren. Het breedste onderdeel accepteert een aantal onbewerkte functies en functie transformaties om functie-interacties te onthouden. En met minder functie techniek, is het diepe onderdeel generaliseert tot onduidelijke functie combinaties door middel van functies voor het insluiten van een geringe, compacte functie. 

In de implementatie van een Wide & diep gaande aanbeveling maakt de module gebruik van een standaard model structuur. Het grote onderdeel maakt het insluiten van gebruikers, het insluiten van items en de trans formatie van de verschillende producten van gebruikers-id's en item-id's als invoer. Voor het diepe deel van het model wordt een insluitings vector voor elke categorische-functie beschreven. Samen met andere numerieke functie vectoren worden deze vectoren vervolgens in het diepe feed-forward Neural-netwerk ingevoerd. Het brede deel en het diepe deel worden gecombineerd door het optellen van het uiteindelijke uitvoer logboek conflicteert als de voor spelling, ten slotte naar één veelvoorkomende verlies functie voor gezamenlijke training.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met beschik bare modules](module-reference.md) van Azure machine learning. 
