---
title: Init Image Transformationply Image Transformation
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Init Image Transformation in Azure Machine Learning designer voor het initialiseren van afbeeldingstransformatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: e5ac228d0aea76dc22db5383a40efe4ec2d252e0
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890416"
---
# <a name="init-image-transformation"></a>Afbeeldingstransformatie initiëren

In dit artikel wordt beschreven hoe u de module **Init Image Transformation** in Azure Machine Learning Designer gebruikt om afbeeldingstransformatie te initialiseren om op te geven hoe de afbeelding moet worden getransformeerd.

## <a name="how-to-configure-init-image-transformation"></a>Init-afbeeldingstransformatie configureren

1.  Voeg de **module Init Image Transformation** toe aan uw pijplijn in de ontwerpfunctie. 

2.  Geef **voor Formaat van grootte** aan of de grootte van de invoer-PIL-afbeelding moet worden opgegeven in de opgegeven grootte. Als u True kiest, kunt u de gewenste grootte van de uitvoerafbeelding opgeven in **Grootte**, standaard 256. 

3.  Geef **voor Middensnijding** op of de opgegeven PIL-afbeelding in het midden moet worden bijgesneden. Als u True kiest, kunt u de gewenste grootte van de uitvoerafbeelding van het bijsnijdformaat opgeven in **Bijsnijdengrootte**, standaard 224.  

4.  Geef **voor Pad** op of de opgegeven PIL-afbeelding aan alle zijden moet worden opgevuld met de padwaarde 0. Als u 'Waar' kiest, kunt u opvulling opgeven (hoeveel pixels u wilt toevoegen) op elke rand in **Padding.**

5.  Geef **voor Color jitter** op of de helderheid, het contrast en de verzadiging van een afbeelding willekeurig moeten worden gewijzigd.

6.  Geef **voor Grayscale** op of u een afbeelding wilt converteren naar grijstinten.

7.  Geef **voor Willekeurig formaat bijsnijding** op of de opgegeven PIL-afbeelding moet worden bijgesneden in een willekeurige grootte en aspectverhouding. Er wordt een bijsnijd van willekeurige grootte (van 0,08 tot 1,0) van de oorspronkelijke grootte en een willekeurige aspectverhouding (variëren van 3/4 tot 4/3) van de oorspronkelijke aspectverhouding gemaakt. Het formaat van dit bijsnijdingsformaat wordt ten slotte weer in de opgegeven grootte gebracht.
    Dit wordt vaak gebruikt bij het trainen van de inception-netwerken. Als u True kiest, kunt u de verwachte uitvoergrootte van elke rand opgeven in **Willekeurige** grootte, standaard 256.

8.  Geef **voor Willekeurig bijsnijden** op of de opgegeven PIL-afbeelding op een willekeurige locatie moet worden bijgesneden. Als u True kiest, kunt u de gewenste uitvoergrootte van het bijsnijdtype opgeven **in** Willekeurige bijsnijdgrootte , standaard 224.

9.  Geef **voor Willekeurige horizontale spiegeling** op of de opgegeven PIL-afbeelding willekeurig moet worden ge flipt met een waarschijnlijkheid van 0,5.

10.  Geef **voor Willekeurige verticale spiegeling** op of de opgegeven PIL-afbeelding willekeurig moet worden ge flipt met een waarschijnlijkheid van 0,5.

11.  Geef **voor Willekeurige rotatie** op of de afbeelding moet worden gedraaid op hoek. Als u True kiest, kunt u opgeven in het bereik van graden door Willekeurige rotatiegraden in te **stellen,** wat betekent (-degrees, +degrees), standaard 0.

12.  Geef **voor Willekeurige affine op** of een willekeurige affine-transformatie van de afbeelding moet worden gedefinieerd, waardoor het midden invariant blijft. Als u 'Waar' kiest, kunt u opgeven in het bereik van graden waaruit u wilt selecteren in Willekeurige **affine-graden,** wat betekent (-graden, +graden), standaard 0.

13.  Geef **voor Willekeurige grijstinten** op of een afbeelding willekeurig moet worden ge converteren naar grijstinten met een waarschijnlijkheid van 0,1.

14.  Geef **voor Willekeurig perspectief** op of perspectieftransformatie van de opgegeven PIL-afbeelding willekeurig moet worden uitgevoerd met een waarschijnlijkheid van 0,5.


16.  Maak verbinding met de module [Afbeeldingstransformatie](apply-image-transformation.md) toepassen om de hierboven opgegeven transformatie toe te passen op de gegevensset van de ingevoerde afbeelding.

17. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Nadat de transformatie is voltooid, kunt u getransformeerde afbeeldingen vinden in de uitvoer van de module [Afbeeldingstransformatie](apply-image-transformation.md) toepassen.


## <a name="technical-notes"></a>Technische opmerkingen  

Raadpleeg voor [https://pytorch.org/vision/stable/transforms.html](https://pytorch.org/vision/stable/transforms.html) meer informatie over afbeeldingstransformatie.

###  <a name="module-parameters"></a>Moduleparameters  

| Name                    | Bereik   | Type    | Standaard | Beschrijving                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Formaat wijzigen                  | Alle     | Boolean-waarde | True    | Het formaat van de invoer-PIL-afbeelding naar de opgegeven grootte |
| Grootte                    | >=1     | Geheel getal | 256     | De gewenste uitvoergrootte opgeven          |
| Middelste bijsnijd             | Alle     | Boolean-waarde | True    | De opgegeven PIL-afbeelding wordt in het midden bijgeplaatst  |
| Bijsnijdgrootte               | >=1     | Geheel getal | 224     | Geef de gewenste uitvoergrootte van het bijsnijdformaat op |
| Pad                     | Alle     | Booleaans | Niet waar   | Pad de opgegeven PIL-afbeelding aan alle zijden op met de opgegeven 'pad'-waarde |
| Opvulling                 | >=0     | Geheel getal | 0       | Opvulling op elke rand                   |
| Color jitter            | Alle     | Booleaans | Niet waar   | De helderheid, het contrast en de verzadiging van een afbeelding willekeurig wijzigen |
| Grijswaarden               | Alle     | Booleaans | Niet waar   | Afbeelding converteren naar grijstinten               |
| Bijsnijding van willekeurig resized     | Alle     | Booleaans | Niet waar   | De opgegeven PIL-afbeelding bijsnijden in willekeurige grootte en aspectverhouding |
| Willekeurige grootte             | >=1     | Geheel getal | 256     | Verwachte uitvoergrootte van elke rand        |
| Willekeurig bijsnijden             | Alle     | Booleaans | Niet waar   | De opgegeven PIL-afbeelding op een willekeurige locatie bijsnijden |
| Willekeurige bijsnijdgrootte        | >=1     | Geheel getal | 224     | Gewenste uitvoergrootte van het bijsnijden          |
| Willekeurige horizontale spiegeling  | Alle     | Boolean-waarde | True    | De opgegeven PIL-afbeelding willekeurig spiegelen met een bepaalde waarschijnlijkheid |
| Willekeurige verticale spiegeling    | Alle     | Booleaans | Niet waar   | De opgegeven PIL-afbeelding willekeurig spiegelen met een bepaalde waarschijnlijkheid |
| Willekeurige rotatie         | Alle     | Booleaans | Niet waar   | De afbeelding draaien op hoek                |
| Willekeurige rotatiegraden | [0,180] | Geheel getal | 0       | Bereik van graden waar uit moet worden geselecteerd          |
| Willekeurige affine           | Alle     | Booleaans | Niet waar   | Random affine transformation of the image keeping center invariant |
| Willekeurige affinegraden   | [0,180] | Geheel getal | 0       | Bereik van graden waar uit moet worden geselecteerd          |
| Willekeurige grijstinten        | Alle     | Booleaans | Niet waar   | Afbeelding willekeurig converteren naar grijstinten met waarschijnlijkheid 0,1 |
| Willekeurig perspectief      | Alle     | Booleaans | Niet waar   | Voert een perspectieftransformatie van de opgegeven PIL-afbeelding willekeurig uit met een waarschijnlijkheid van 0,5 |
| Willekeurig wissen          | Alle     | Booleaans | Niet waar   | Hiermee wordt willekeurig een rechthoekgebied in een afbeelding geselecteerd en worden de pixels gewist met een waarschijnlijkheid van 0,5 |

###  <a name="output"></a>Uitvoer  

| Naam                        | Type                    | Description                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Afbeeldingstransformatie van uitvoer | TransformationDirectory | Uitvoerafbeeldingstransformatie die kan worden verbonden met de module **Afbeeldingstransformatie** toepassen. |

## <a name="next-steps"></a>Volgende stappen

Zie de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 