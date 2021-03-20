---
title: Converteren naar indicatorwaarden
titleSuffix: Azure Machine Learning
description: Gebruik de module converteren naar indicator waarden in Azure Machine Learning Designer om categorische kolommen te converteren naar een reeks binaire-indicator kolommen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420849"
---
# <a name="convert-to-indicator-values"></a>Converteren naar indicatorwaarden
In dit artikel wordt een module van Azure Machine Learning Designer beschreven.

Gebruik de module **converteren naar indicator waarden** in azure machine learning Designer om kolommen die categorische-waarden bevatten, te converteren naar een reeks binaire-indicator kolommen.  

In deze module wordt ook een definitie uitgevoerd van de trans formatie die wordt gebruikt om te converteren naar indicator waarden. U kunt deze trans formatie opnieuw gebruiken voor andere gegevens sets die hetzelfde schema hebben, met behulp van de module [trans formatie Toep assen](apply-transformation.md) .

## <a name="how-to-configure-convert-to-indicator-values"></a>Conversie naar indicator waarden configureren

1.  Zoek de **conversie naar indicator waarden** en sleep deze naar het concept van de pijp lijn. U kunt deze module vinden onder de categorie **gegevens transformatie** .
    > [!NOTE]
    > U kunt de module [meta gegevens bewerken](edit-metadata.md) voor de module **converteren naar Indiciator-waarden** gebruiken om de doel kolom (men) als categorische te markeren.

1. Verbind de module **converteren naar indicator waarden** in de gegevensset met de kolommen die u wilt converteren. 

1. Selecteer **kolom bewerken** om een of meer categorische kolommen te kiezen.

1. Selecteer de optie **categorische kolommen overschrijven** als u **alleen** de nieuwe Boole-kolommen wilt uitvoeren. Deze optie is standaard uitgeschakeld.
    

    > [!TIP]
    >  Als u de optie voor overschrijven kiest, wordt de bron kolom niet werkelijk verwijderd of gewijzigd. In plaats daarvan worden de nieuwe kolommen gegenereerd en weer gegeven in de uitvoer gegevensset, en de bron kolom blijft beschikbaar in de werk ruimte. Als u de oorspronkelijke gegevens wilt zien, kunt u de module [kolommen toevoegen](add-columns.md) op elk gewenst moment gebruiken om de bron kolom weer in te voegen.

1. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Stel dat u een kolom hebt met scores die aangeven of een server een hoge, gemiddelde of lage kans op fouten heeft.  

| Server-ID | Fout Score |
| --------- | ------------- |
| 10301     | Beperkt           |
| 10302     | Normaal        |
| 10303     | Hoog          |

Wanneer u **converteren toepast op indicator waarden**, zet de ontwerper één kolom met labels om in meerdere kolommen met Booleaanse waarden:  

| Server-ID | Fout Score-laag | Fout Score: medium | Fout score-hoog |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

De conversie werkt als volgt:  

-   In de kolom **fout Score** die het risico beschrijft, zijn er slechts drie mogelijke waarden (hoog, gemiddeld en laag) en ontbreken er geen waarden meer. Dat betekent dat er precies drie nieuwe kolommen worden gemaakt.  

-   De nieuwe indicator kolommen hebben de naam op basis van de kolom koppen en waarden van de kolom Bron, met behulp van dit patroon: *\<source column>- \<data value>* .  

-   Er moet een 1 in precies één indicator kolom zijn en 0 in alle andere indicator kolommen, aangezien elke server slechts één risico classificatie kan hebben.  

U kunt nu de drie indicator kolommen gebruiken als functies in een machine learning model.

De module retourneert twee uitvoer:

- **Gegevensset voor resultaten**: een gegevensset met geconverteerde indicator waarden kolommen. Kolommen die niet zijn geselecteerd voor het opschonen, zijn ook ' door gegeven '.
- **Indicator waarden transformeren**: een gegevens transformatie die wordt gebruikt voor het converteren naar indicator waarden die kunnen worden opgeslagen in uw werk ruimte en later worden toegepast op nieuwe gegevens.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Een opgeslagen indicator waarden-bewerking Toep assen op nieuwe gegevens

Als u de bewerkingen voor indicator waarden vaak wilt herhalen, kunt u de stappen voor het bewerken van gegevens opslaan als een *trans formatie* om deze opnieuw te gebruiken met dezelfde gegevensset. Dit is handig als u regel matig opnieuw moet importeren en vervolgens gegevens wilt opschonen die hetzelfde schema hebben.

1. Voeg de module [trans formatie Toep assen](apply-transformation.md) toe aan uw pijp lijn.

1. Voeg de gegevensset die u wilt reinigen toe en verbind de gegevensset met de juiste invoer poort.

1. Vouw de groep **gegevens transformatie** uit in het linkerdeel venster van Designer. Zoek de opgeslagen trans formatie en sleep deze naar de pijp lijn.

1. Verbind de opgeslagen trans formatie met de linker invoer poort van [Apply trans formatie](apply-transformation.md).

   Wanneer u een opgeslagen trans formatie toepast, kunt u niet selecteren welke kolommen u wilt transformeren. Dit komt doordat de trans formatie is gedefinieerd en automatisch van toepassing is op de gegevens typen die in de oorspronkelijke bewerking zijn opgegeven.

1. Verzend de pijp lijn.
 
## <a name="technical-notes"></a>Technische opmerkingen  

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

### <a name="usage-tips"></a>Gebruiks tips

-   Alleen kolommen die zijn gemarkeerd als categorische kunnen worden geconverteerd naar indicator kolommen. Als u de volgende fout ziet, is een van de geselecteerde kolommen waarschijnlijk niet categorische:  

     Fout 0056: de kolom met  \<column name> de naam bevindt zich niet in een toegestane categorie.  

     Standaard worden de meeste kolommen met teken reeksen verwerkt als teken reeks functies, dus u moet ze expliciet markeren als categorische met behulp van [meta gegevens bewerken](edit-metadata.md).  

-   Er is geen limiet voor het aantal kolommen dat u kunt converteren naar indicator kolommen. Omdat elke kolom met waarden echter meerdere indicator kolommen kan leveren, wilt u mogelijk slechts enkele kolommen tegelijk converteren en bekijken.  

-   Als de kolom ontbrekende waarden bevat, wordt er een afzonderlijke indicator kolom gemaakt voor de ontbrekende categorie met deze naam: *\<source column> -ontbreekt*  

-   Als de kolom die u converteert naar indicator waarden getallen bevat, moeten deze worden gemarkeerd als categorische, zoals elke andere functie kolom. Nadat u dit hebt gedaan, worden de getallen beschouwd als discrete waarden. Als u bijvoorbeeld een numerieke kolom hebt met MPG-waarden van 25 tot 30, wordt er een nieuwe indicator kolom gemaakt voor elke afzonderlijke waarde:  

    | Merk       | Snelweg MPG-25 | Snelweg MPG-26 | Snelweg MPG-27 | Snelweg MPG-28 | Snelweg MPG-29 | Snelweg (30) |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso Auto's | 0               | 0               | 0               | 0               | 0               | 1               |

- Om te voor komen dat er te veel dimensies aan uw gegevensset worden toegevoegd. We raden u aan eerst het aantal waarden in de kolom te controleren en de gegevens op de juiste locatie of worden te zoeken.  


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
