---
title: 'Functie selectie op basis van filters: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module voor het selecteren van functies op basis van filters in Azure Machine Learning voor het identificeren van de functies in een gegevensset met de grootste voorspellende energie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91945699"
---
# <a name="filter-based-feature-selection"></a>Functieselectie op basis van filters

In dit artikel wordt beschreven hoe u de module voor het selecteren van functies op basis van filters in Azure Machine Learning Designer kunt gebruiken. Deze module helpt u bij het identificeren van de kolommen in uw invoer gegevensset die de grootste voorspellende kracht hebben. 

In het algemeen verwijst het *selecteren van functies* naar het proces van het Toep assen van statistische tests op invoer, op basis van een opgegeven uitvoer. Het doel is om te bepalen welke kolommen meer voor spelling van de uitvoer zijn. De module op basis van filter functies bevat meerdere algoritmen voor het selecteren van onderdelen waaruit u kunt kiezen. De module bevat correlatie methoden, zoals Pearson-correlatie en Chi-kwadraat waarden. 

Wanneer u de module voor het selecteren van functies op basis van filters gebruikt, geeft u een gegevensset op en identificeert u de kolom die het label of de afhankelijke variabele bevat. Vervolgens geeft u één methode op die moet worden gebruikt voor het meten van de functie urgentie.

De module levert een gegevensset die de beste functie kolommen bevat, zoals wordt aangegeven door voorspellende energie. Ook worden de namen van de functies en de scores van de geselecteerde metriek uitgevoerd.  

## <a name="what-filter-based-feature-selection-is"></a>De selectie van functies op basis van filter  

Deze module voor het selecteren van functies wordt ' filter gebaseerd ' genoemd, omdat u de geselecteerde metriek gebruikt om irrelevante kenmerken te vinden. Vervolgens filtert u redundante kolommen uit uw model. U kiest één statistische maat eenheid die aansluit bij uw gegevens en de module berekent een score voor elke functie kolom. De kolommen worden weer gegeven op basis van hun functie scores. 

Door de juiste functies te kiezen, kunt u de nauw keurigheid en efficiëntie van de classificatie verbeteren. 

Normaal gesp roken gebruikt u alleen de kolommen met de beste scores om uw voorspellende model te bouwen. Kolommen met slechte onderdelen selectie scores kunnen in de gegevensset worden achtergelaten en worden genegeerd wanneer u een model bouwt.  

## <a name="how-to-choose-a-feature-selection-metric"></a>De metriek van een functie selecteren

De module Filter-Based onderdelen electie biedt diverse metrische gegevens voor het beoordelen van de informatie waarde in elke kolom. Deze sectie bevat een algemene beschrijving van elke metriek en hoe deze wordt toegepast. U vindt aanvullende vereisten voor het gebruik van elke metriek in de [technische opmerkingen](#technical-notes) en in de [instructies](#how-to-configure-filter-based-feature-selection) voor het configureren van elke module.

-   **Pearson-correlatie**  

    Correlatie statistieken van Pearson of correlatie coëfficiënt van Pearson worden ook bekend in statistische modellen als de `r` waarde. Voor twee variabelen retourneert deze een waarde die de sterkte van de correlatie aangeeft.

    De correlatie coëfficiënt van Pearson wordt berekend door de covariantie van twee variabelen te nemen en te delen door het product van de standaard afwijkingen. Wijzigingen van de schaal in de twee variabelen hebben geen invloed op de coëfficiënt.  

-   **Chi-kwadraat**  

    De tweezijdige Chi-kwadraat test is een statistische methode waarmee wordt gemeten hoe het sluiten van verwachte waarden tot werkelijke resultaten leidt. De methode veronderstelt dat variabelen wille keurig zijn en worden getekend uit een adequaat voor beeld van onafhankelijke variabelen. Met de resulterende Chi-kwadraat statistiek wordt aangegeven hoe ver resultaten van het verwachte (wille keurig) resultaat zijn.  


> [!TIP]
> Als u een andere optie voor de selectie methode voor aangepaste functies nodig hebt, gebruikt u de module [R-script uitvoeren](execute-r-script.md) . 

## <a name="how-to-configure-filter-based-feature-selection"></a>Filter-Based functie selectie configureren

U kiest een standaard statistische metrische waarde. De module berekent de correlatie tussen een paar kolommen: de kolom Label en een functie kolom.

1.  Voeg de module voor het selecteren van Filter-Based onderdelen toe aan uw pijp lijn. U kunt deze vinden in de categorie **functie selectie** in de ontwerper.

2. Verbind een invoer-gegevensset die ten minste twee kolommen bevat die mogelijke functies zijn.  

    Om ervoor te zorgen dat een kolom wordt geanalyseerd en een functie score wordt gegenereerd, gebruikt u de module [meta gegevens bewerken](edit-metadata.md) om het kenmerk **IsFeature** in te stellen. 

    > [!IMPORTANT]
    > Zorg ervoor dat de kolommen die u opgeeft als invoer, mogelijke functies zijn. Een kolom met één waarde bevat bijvoorbeeld geen informatie waarde.
    >
    > Als u weet dat sommige kolommen slechte functies zouden kunnen hebben, kunt u ze uit de kolom selectie verwijderen. U kunt ook de module [meta gegevens bewerken](edit-metadata.md) gebruiken om ze als **categorische** te markeren. 
3.  Voor de **functie Score methode** kiest u een van de volgende statistische methoden die u kunt gebruiken bij het berekenen van scores.  

    | Methode              | Vereisten                             |
    | ------------------- | ---------------------------------------- |
    | Pearson-correlatie | Label kan tekst of numeriek zijn. De functies moeten numeriek zijn. |
    Chi-kwadraat| Labels en functies kunnen tekst of numeriek zijn. Gebruik deze methode voor het berekenen van de functie prioriteit voor twee categorische-kolommen.|

    > [!TIP]
    > Als u de geselecteerde metriek wijzigt, worden alle andere selecties opnieuw ingesteld. Zorg er daarom voor dat u deze optie eerst instelt.
4.  Selecteer de optie **alleen werk kolommen gebruiken** om een score te genereren voor kolommen die eerder als functies zijn gemarkeerd. 

    Als u deze optie uitschakelt, wordt door de module een score gemaakt voor elke kolom die anders aan de criteria voldoet, tot het aantal kolommen dat is opgegeven in het **aantal gewenste functies**.  

5.  Selecteer bij **doel kolom** de optie voor het starten van een **kolom selecteren** om de kolom label te kiezen op naam of op index. (Indexen zijn op één gebaseerd.)  
    Een label kolom is vereist voor alle methoden waarbij statistische correlatie is betrokken. De module retourneert een fout in de ontwerp fase als u geen label kolom of meerdere label kolommen selecteert. 

6.  Voor het **aantal gewenste functies** voert u het aantal functie kolommen in dat u als resultaat wilt retour neren:  

    - Het minimum aantal functies dat u kunt opgeven, is één, maar we raden u aan deze waarde te verhogen.  

    - Als het opgegeven aantal gewenste functies groter is dan het aantal kolommen in de gegevensset, worden alle functies geretourneerd. Zelfs functies met een Score van nul worden geretourneerd.  

    - Als u minder resultaat kolommen opgeeft dan er functie kolommen zijn, worden de functies gerangschikt op aflopende Score. Alleen de belangrijkste functies worden geretourneerd. 

7.  Verzend de pijp lijn.

> [!IMPORTANT]
> Als u de **functie selectie op basis van filters** wilt gebruiken in de afleiding, moet u [Select column Transform](./select-columns-transform.md) gebruiken om de functie geselecteerd resultaat op te slaan en [trans formatie](./apply-transformation.md) toe te passen om de geselecteerde functie van trans formatie toe te passen op de Score gegevensset.
>
> Raadpleeg de volgende scherm afbeelding om uw pijp lijn te bouwen, om ervoor te zorgen dat de kolom selecties hetzelfde zijn voor het Score proces.
> [!div class="mx-imgBorder"]
> ![Voorbeeld pijplijn](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Resultaten

Nadat de verwerking is voltooid:

+ Klik met de rechter muisknop op de module en selecteer **visualiseren** om een volledige lijst met de geanalyseerde functie kolommen en de bijbehorende scores weer te geven.  

+ Als u de gegevensset wilt weer geven op basis van de criteria voor de selectie van functies, klikt u met de rechter muisknop op de module en selecteert u **visualiseren**. 

Als de gegevensset minder kolommen bevat dan u verwacht, controleert u de instellingen van de module. Controleer ook de gegevens typen van de kolommen die worden opgegeven als invoer. Als u bijvoorbeeld het **aantal gewenste functies** instelt op 1, bevat de uitvoer gegevensset slechts twee kolommen: de kolom Label en de meest gerangschikte functie kolom.


##  <a name="technical-notes"></a>Technische opmerkingen  

### <a name="implementation-details"></a>Implementatiegegevens

Als u Pearson-correlatie gebruikt voor een numerieke functie en een categorische-label, wordt de functie score als volgt berekend:  

1.  Voor elk niveau in de kolom categorische berekent u het voorwaardelijke gemiddelde van de numerieke kolom.  

2.  Correleer de kolom van voorwaardelijke middelen met de numerieke kolom.  

### <a name="requirements"></a>Vereisten  

-   Er kan geen score voor de functie selectie worden gegenereerd voor een kolom die is ingesteld als een **Label** of een **Score** kolom.  

-   Als u een score methode wilt gebruiken met een kolom van een gegevens type dat niet wordt ondersteund door de-methode, wordt er een fout gegenereerd door de module. Of een Score van nul wordt toegewezen aan de kolom.  

-   Als een kolom logische waarden (True/False) bevat, worden deze verwerkt als `True = 1` en `False = 0` .  

-   Een kolom kan geen functie zijn als deze is aangewezen als een **Label** of **Score**.  

### <a name="how-missing-values-are-handled"></a>Hoe ontbrekende waarden worden verwerkt  

-   U kunt geen doel kolom (label) opgeven voor elke kolom met alle ontbrekende waarden.  

-   Als een kolom ontbrekende waarden bevat, wordt deze door de module genegeerd wanneer de score voor de kolom wordt berekend.  

-   Als een kolom die is opgegeven als een functie kolom, alle ontbrekende waarden heeft, wijst de module een nul-score toe.   


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

