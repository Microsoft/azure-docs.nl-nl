---
title: Trans formatie van Transformationply-afbeelding van init-afbeelding
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de init-module voor het transformeren van installatie kopieën in Azure Machine Learning Designer om de afbeeldings transformatie te initialiseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421274"
---
# <a name="init-image-transformation"></a>Afbeeldingstransformatie initiëren

In dit artikel wordt beschreven hoe u de **initialisatie-afbeelding transformatie** module in azure machine learning Designer kunt gebruiken om de afbeeldings transformatie te initialiseren om aan te geven hoe u de afbeelding wilt transformeren.

## <a name="how-to-configure-init-image-transformation"></a>Trans formatie van init-afbeelding configureren

1.  Voeg de **transformatie module init image alimages** toe aan uw pijp lijn in de ontwerp functie. 

2.  Geef bij **formaat wijzigen** op of het formaat van de invoer pil-afbeelding moet worden gewijzigd in de opgegeven grootte. Als u ' waar ' kiest, kunt u de gewenste grootte van de uitvoer afbeelding **opgeven, standaard** 256. 

3.  Geef bij **snij punt** op of de opgegeven pil-afbeelding in het midden moet worden bijgesneden. Als u ' waar ' kiest, kunt u de gewenste grootte van de uitvoer afbeelding opgeven van de bijsnijd **grootte**, standaard 224.  

4.  Geef bij **pad** de opgegeven pil-installatie kopie op alle zijden met de pad waarde 0 op. Als u ' waar ' kiest, kunt u opvulling opgeven (hoeveel pixels u wilt toevoegen) op elke rand in **opvulling**.

5.  Geef bij **kleur-jitter** op of de helderheid, het contrast en de verzadiging van een afbeelding wille keurig moeten worden gewijzigd.

6.  Geef voor **grijs waarden** op of de afbeelding moet worden geconverteerd naar grijs waarden.

7.  Geef bij **wille keurig aangepast gewas** op of de opgegeven pil-afbeelding moet worden bijgesneden naar een wille keurige grootte en hoogte-breedte verhouding. Een wille keurige grootte (bereik van 0,08 tot 1,0) van de oorspronkelijke grootte en een wille keurige hoogte-breedte verhouding (bereik van 3/4 tot 4/3) van de oorspronkelijke hoogte-breedte verhouding wordt gemaakt. Het formaat van deze bijsnijding wordt ten slotte gewijzigd in de opgegeven grootte.
    Dit wordt vaak gebruikt voor het trainen van de aangemaakte netwerken. Als u ' waar ' kiest, kunt u de verwachte uitvoer grootte van elke rand in **wille keurige grootte** opgeven, standaard 256.

8.  Geef voor **wille keurige bijsnijding** op of de opgegeven pil-afbeelding op een wille keurige locatie moet worden bijgesneden. Als u ' waar ' kiest, kunt u de gewenste uitvoer grootte van het gewas opgeven in **wille keurige bijsnijd grootte**, standaard 224.

9.  Geef voor **wille keurige horizontale spiegeling** op of de opgegeven pil-afbeelding wille keurig horizon taal moet worden gespiegeld met de kans 0,5.

10.  Geef voor **wille keurig verticaal spie gelen** op of de opgegeven pil afbeelding wille keurig verticaal moet worden gespiegeld met de kans 0,5.

11.  Geef voor **wille keurige rotatie** op of de afbeelding moet worden geroteerd met hoek. Als u ' waar ' kiest, kunt u het aantal graden opgeven door **wille keurige rotatie graden** in te stellen, wat betekent (-graden, + graden) standaard 0.

12.  Voor **wille keurige affiniteit** geeft u op of u een wille keurige affiniteit wilt door voeren van de afbeelding die in het midden wordt bewaard. Als u ' waar ' kiest, kunt u binnen bereik van graden opgeven dat u in **wille keurige affiniteits graden** wilt selecteren. Dit houdt in dat (-graden, + graden) standaard 0 is.

13.  Voor **wille keurige grijs waarden** geeft u op of de afbeelding wille keurig moet worden geconverteerd naar een grijs waarde van 0,1.

14.  Geef voor **wille keurig perspectief** op of u de perspectief transformatie van de opgegeven pil-afbeelding wille keurig wilt uitvoeren met de kans 0,5.


16.  Maak verbinding met het [Toep assen van de installatie kopie transformatie](apply-image-transformation.md) module om de opgegeven trans formatie toe te passen op de gegevensset van de invoer installatie kopie.

17. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de trans formatie is voltooid, kunt u getransformeerde afbeeldingen vinden in de uitvoer van de [installatie kopie transformatie module Toep assen](apply-image-transformation.md) .


## <a name="technical-notes"></a>Technische opmerkingen  

Raadpleeg [https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html) voor meer informatie over afbeeldings transformatie.

###  <a name="module-parameters"></a>Module parameters  

| Name                    | Bereik   | Type    | Standaard | Beschrijving                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Formaat wijzigen                  | Alle     | Boolean-waarde | True    | De opgegeven grootte van de PIL-invoer afbeelding aanpassen |
| Grootte                    | >= 1     | Geheel getal | 256     | Geef de gewenste uitvoer grootte op          |
| Midden bijsnijden             | Alle     | Boolean-waarde | True    | Hiermee wordt de opgegeven PIL-afbeelding in het midden bijgesneden  |
| Bijsnijd grootte               | >= 1     | Geheel getal | 224     | Geef de gewenste uitvoer grootte van het gewas op |
| Pad                     | Alle     | Booleaans | Niet waar   | De opgegeven PIL-installatie kopie op alle zijden met de opgegeven "pad"-waarde aanvullen |
| Opvulling                 | >= 0     | Geheel getal | 0       | Opvulling voor elke rand                   |
| Kleur-jitter            | Alle     | Booleaans | Niet waar   | De helderheid, het contrast en de verzadiging van een afbeelding wille keurig wijzigen |
| In grijs waarden               | Alle     | Booleaans | Niet waar   | Afbeelding naar grijs waarde converteren               |
| Wille keurig aangepast gewas     | Alle     | Booleaans | Niet waar   | De opgegeven PIL-afbeelding bijsnijden naar een wille keurige grootte en hoogte-breedte verhouding |
| Wille keurige grootte             | >= 1     | Geheel getal | 256     | Verwachte uitvoer grootte van elke rand        |
| Wille keurig gewas             | Alle     | Booleaans | Niet waar   | De opgegeven PIL-installatie kopie op een wille keurige locatie bijsnijden |
| Wille keurige snij grootte        | >= 1     | Geheel getal | 224     | Gewenste uitvoer grootte van het gewas          |
| Wille keurige horizontale spie gelen  | Alle     | Boolean-waarde | True    | De opgegeven PIL-afbeelding wille keurig horizon taal spie gelen met een bepaalde kans |
| Wille keurig verticaal spie gelen    | Alle     | Booleaans | Niet waar   | De opgegeven PIL-afbeelding wille keurig verticaal spie gelen met een bepaalde kans |
| Wille keurige draaiing         | Alle     | Booleaans | Niet waar   | De afbeelding op hoek draaien                |
| Graden met wille keurige draaiing | [0180] | Geheel getal | 0       | Bereik van graden waaruit moet worden geselecteerd          |
| Wille keurige affiniteit           | Alle     | Booleaans | Niet waar   | Wille keurige affiniteits transformatie van de afbeelding die in het midden wordt bewaard |
| Wille keurige affiniteits graden   | [0180] | Geheel getal | 0       | Bereik van graden waaruit moet worden geselecteerd          |
| Wille keurige grijs waarde        | Alle     | Booleaans | Niet waar   | Afbeelding kan wille keurig worden geconverteerd naar grijs waarden met kans 0,1 |
| Wille keurig perspectief      | Alle     | Booleaans | Niet waar   | Hiermee wordt de perspectief transformatie van de opgegeven PIL-afbeelding wille keurig uitgevoerd met kans 0,5 |
| Wille keurig wissen          | Alle     | Booleaans | Niet waar   | Hiermee wordt wille keurig een rechthoekige regio in een afbeelding geselecteerd en worden de pixels gewist met de kans 0,5 |

###  <a name="output"></a>Uitvoer  

| Naam                        | Type                    | Description                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Trans formatie van uitvoer afbeelding | TransformationDirectory | Trans formatie van uitvoer afbeelding die kan worden verbonden om de **installatie kopie transformatie module toe te passen** . |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 