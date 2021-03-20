---
title: 'Gegevens groeperen in opslag locaties: module verwijzing'
titleSuffix: Azure Machine Learning
description: Informatie over het gebruik van de groeps gegevens in de module opslag locaties voor het groeperen van nummers of het wijzigen van de distributie van doorlopende gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91998476"
---
# <a name="group-data-into-bins-module"></a>Gegevens groeperen in module opslag locaties

In dit artikel wordt beschreven hoe u de groeps gegevens in de module opslag locaties in Azure Machine Learning Designer kunt gebruiken om nummers te groeperen of de distributie van doorlopende gegevens te wijzigen.

De module gegevens groeperen in opslag locaties ondersteunt meerdere opties voor binning-gegevens. U kunt aanpassen hoe de randen van de opslag locatie worden ingesteld en hoe waarden worden verdeeld in de opslag locaties. U kunt bijvoorbeeld het volgende doen:  

+ Voer hand matig een reeks waarden in die moeten fungeren als de grenzen van de opslag locatie.  
+ Waarden toewijzen aan opslag locaties met behulp van *quantiles* of percentiel posities.  
+ Een gelijkmatige verdeling van waarden in de opslag locaties afdwingen.  

## <a name="more-about-binning-and-grouping"></a>Meer informatie over binning en groeperen

*Binning* -of groepeer gegevens (ook wel *kwantisatiefouten* genoemd) is een belang rijk hulp middel bij het voorbereiden van numerieke gegevens voor machine learning. Het is handig in scenario's als deze:

+ Een kolom met doorlopende getallen heeft te veel unieke waarden om effectief te kunnen model leren. Zodat u de waarden automatisch of hand matig toewijst aan groepen, om een kleinere set discrete bereiken te maken.

+ U wilt een kolom met getallen vervangen door categorische-waarden die specifieke bereiken vertegenwoordigen.

    Stel dat u waarden in een kolom leeftijd wilt groeperen door aangepaste bereiken op te geven, zoals 1-15, 16-22, 23-30, enzovoort voor demografische gegevens van gebruikers.

+ Een gegevensset heeft enkele extreme waarden, die allemaal goed buiten het verwachte bereik vallen, en deze waarden hebben een zeer grote invloed op het getrainde model. Als u de afwijking in het model wilt beperken, kunt u de gegevens transformeren naar een uniforme distributie met behulp van de methode quantiles.

    Met deze methode bepaalt de module groeps gegevens in de opslag locaties de ideale opslaglocatie locaties en de breedte van de opslag locatie om ervoor te zorgen dat ongeveer hetzelfde aantal steek proeven in elke bin wordt onderverdeeld. Afhankelijk van de normalisatie methode die u kiest, worden de waarden in de opslag locaties omgezet naar percentielen of toegewezen aan een opslaglocatie nummer.

### <a name="examples-of-binning"></a>Voor beelden van binning

Het volgende diagram toont de distributie van numerieke waarden voor en na binning met de methode *quantiles* . In vergelijking met de onbewerkte gegevens zijn de gegevens binning en omgezet naar een eenheid-normale schaal.  

> [!div class="mx-imgBorder"]
> ![Visualisatie van resultaten](media/module/group-data-into-bins-result-example.png)

Omdat er zoveel manieren zijn om gegevens te groeperen, is het raadzaam om te experimenteren met verschillende methoden en waarden. 

## <a name="how-to-configure-group-data-into-bins"></a>Groeps gegevens in opslag locaties configureren

1. Voeg de **groeps gegevens** in de module opslag locaties toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **gegevens transformatie**.

2. Verbind de gegevensset die numerieke gegevens op de bin heeft. Kwantisatiefouten kan alleen worden toegepast op kolommen die numerieke gegevens bevatten. 

    Als de gegevensset niet-numerieke kolommen bevat, gebruikt u de module [select columns in dataset](select-columns-in-dataset.md) om een subset kolommen te selecteren waarmee u wilt werken.

3. Geef de binning-modus op. De modus binning bepaalt andere para meters, dus zorg ervoor dat u eerst de optie **binning modus** selecteert. De volgende typen binning worden ondersteund:

    - **Quantiles**: met de methode quantile worden waarden toegewezen aan opslag locaties op basis van percentiel posities. Deze methode staat ook bekend als binning met gelijke hoogte.

    - **Gelijke breedte**: met deze optie moet u het totale aantal bakken opgeven. De waarden in de kolom gegevens worden in de opslag locaties geplaatst, zodat elke bin hetzelfde interval heeft tussen de begin-en eind waarden. Als gevolg hiervan kunnen sommige opslag locaties meer waarden hebben als de gegevens clumped rond een bepaald punt zijn.

    - **Aangepaste randen**: u kunt de waarden opgeven die elke opslag locatie beginnen. De rand waarde is altijd de ondergrens van de lade. 
    
      Stel dat u waarden wilt groeperen in twee opslag locaties. Een waarde die groter is dan 0 en een waarde die kleiner is dan of gelijk is aan 0. In dit geval typt u **0** in een **door komma's gescheiden lijst met bin-** randen. De uitvoer van de module zal 1 en 2 zijn, waarmee de bin-index voor elke Rijwaarde wordt aangegeven. De lijst met door komma's gescheiden waarden moet een oplopende volg orde hebben, zoals 1, 3, 5, 7.
    
    > [!Note]
    > De modus *ENTROPIE MDL* is gedefinieerd in Studio (klassiek) en er is geen bijbehorend open-source pakket dat kan worden gebruikt voor ondersteuning in Designer.        

4. Als u de **Quantiles** -en binning-modus met **gelijke breedte** gebruikt, gebruikt u de optie **aantal bakken** om op te geven hoeveel bakken of *Quantiles* u wilt maken.

5. Gebruik voor **kolommen naar bin** de kolom kiezer om de kolommen te kiezen met de waarden die u wilt gebruiken voor de opslag locatie. Kolommen moeten een numeriek gegevens type zijn.

    Dezelfde binning-regel wordt toegepast op alle toepasselijke kolommen die u kiest. Als u bepaalde kolommen met een andere methode wilt opdelen, gebruikt u een afzonderlijk exemplaar van de groeps gegevens in de module opslag locaties voor elke set kolommen.

    > [!WARNING]
    > Als u een kolom kiest die geen toegestaan type is, wordt er een runtime-fout gegenereerd. De module retourneert een fout zodra een kolom van een niet-toegestaan type wordt gevonden. Als er een fout optreedt, controleert u alle geselecteerde kolommen. In de fout worden niet alle ongeldige kolommen weer geven.

6. Geef voor **uitvoer modus** op hoe u de waarden voor de afgemelde waarde wilt uitvoeren:

    + **Toevoegen**: maakt een nieuwe kolom met de binning-waarden en voegt deze toe aan de invoer tabel.

    + **InPlace**: vervangt de oorspronkelijke waarden door de nieuwe waarden in de gegevensset.

    + **ResultOnly**: retourneert alleen de resultaten kolommen.

7. Als u de **Quantiles** binning-modus selecteert, gebruikt u de optie **Quantile normalisatie** om te bepalen hoe waarden worden genormaliseerd voordat ze in Quantiles worden gesorteerd. Houd er rekening mee dat het normaliseren van waarden de waarden transformeert, maar geen invloed heeft op het uiteindelijke aantal opslag locaties.

    De volgende normalisatie typen worden ondersteund:

    + **Percentage**: de waarden worden genormaliseerd binnen het bereik [0100].

    + **PQuantile**: waarden worden genormaliseerd binnen het bereik [0, 1].

    + **QuantileIndex**: waarden worden genormaliseerd binnen het bereik [1, aantal bakken].

8. Als u kiest voor de optie **aangepaste randen** , voert u een door komma's gescheiden lijst met getallen in *die moeten worden gebruikt als een* door komma's gescheiden lijst met tekst vakken **van de door een komma** . 

    De waarden markeren het punt waar de opslag locaties worden verdeeld. Als u bijvoorbeeld één waarde voor een bak-rand opgeeft, worden er twee opslag locaties gegenereerd. Als u twee marge waarden opgeeft, worden er drie opslag locaties gegenereerd.

    De waarden moeten worden gesorteerd in de volg orde waarin de bakken zijn gemaakt, van laag naar hoog.

10. Selecteer de **categorische** -optie om aan te geven dat de kolommen met de kolom categorische moeten worden verwerkt.

11. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

De module gegevens groeperen in opslag locaties retourneert een gegevensset waarin elk element is binning volgens de opgegeven modus. 

Er wordt ook een *binning-trans formatie* geretourneerd. Deze functie kan worden door gegeven aan de module [trans formatie Toep assen](apply-transformation.md) op nieuwe voor beelden van gegevens met behulp van dezelfde binning-modus en-para meters.  

> [!TIP]
> Als u binning gebruikt voor uw trainings gegevens, moet u dezelfde binning-methode gebruiken voor gegevens die u gebruikt voor testen en voor spellingen. U moet ook dezelfde opslaglocatie locaties en bin-breedten gebruiken. 
> 
> Om ervoor te zorgen dat gegevens altijd worden getransformeerd met behulp van dezelfde binning-methode, wordt u aangeraden bruikbare gegevens transformaties op te slaan. Vervolgens kunt u ze Toep assen op andere gegevens sets met behulp van de module [trans formatie Toep assen](apply-transformation.md) .

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
