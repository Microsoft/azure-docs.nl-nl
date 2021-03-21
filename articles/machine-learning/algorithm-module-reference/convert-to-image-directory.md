---
title: Converteren naar afbeeldingsmap
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module converteren naar afbeelding om de gegevensset te converteren naar de indeling van de afbeelding.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94555580"
---
# <a name="convert-to-image-directory"></a>Converteren naar afbeeldingsmap

In dit artikel wordt beschreven hoe u de module converteren naar afbeelding kunt gebruiken om de gegevensset van de afbeelding te converteren naar het gegevens type van de *afbeelding* . Dit is een gestandaardiseerde gegevens indeling in aan afbeeldingen gerelateerde taken zoals afbeeldings classificatie in azure machine learning Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Converteren naar afbeeldings Directory gebruiken  

1. Bereid eerst uw installatie kopie gegevensset voor. 

    Voor leren onder Super visie moet u het label van de trainings gegevensset opgeven. Het gegevenssetbestand van de afbeelding moet de volgende structuur hebben:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    Er zijn meerdere submappen in de map gegevensset van de afbeelding. Elke submap bevat respectievelijk installatie kopieën van één categorie. De namen van de submappen worden beschouwd als de labels voor taken zoals afbeeldings classificatie. Raadpleeg [Torchvision-gegevens sets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) voor meer informatie.

    > [!WARNING]
    > Momenteel worden gegevens sets die zijn geëxporteerd uit gegevenslabeling, niet ondersteund in de ontwerp functie.

    Afbeeldingen met deze uitbrei dingen (in kleine letters) worden ondersteund:. jpg,. JPEG,. png,. ppm,. bmp,.................... TIFF,. webp. U kunt ook meerdere typen installatie kopieën in één map hebben. Het is niet nodig om hetzelfde aantal installatie kopieën in elke categorie map te bevatten.

    U kunt de map of het gecomprimeerde bestand met de extensie '. zip ', '. tar ', '. gz ' en '. bz2 ' gebruiken. **Gecomprimeerde bestanden worden aanbevolen voor betere prestaties.** 
    
    ![Gegevensset voor beeld van afbeelding](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > Voor een afleiding moet de map gegevensset van de afbeelding alleen niet-geclassificeerde afbeeldingen bevatten.

1. [Registreer de gegevensset van de installatie kopie als een bestands gegevensset](../how-to-create-register-datasets.md) in uw werk ruimte, omdat de invoer van Convert naar afbeelding Directory module moet een **Bestands gegevensset** zijn.

1. Voeg de gegevensset van de geregistreerde installatie kopie toe aan het canvas. U kunt de geregistreerde gegevensset vinden in de categorie **gegevens sets** in de module lijst links van het canvas. De ontwerper ondersteunt momenteel geen gegevensset voor het visualiseren van afbeeldingen.

    > [!WARNING]
    > U **kunt** de module **gegevens importeren** niet gebruiken voor het importeren van de gegevensset van een afbeelding, omdat het uitvoer type van de module **import data** de data frame Directory is. deze map bevat alleen een teken reeks voor het bestandspad.

1. Voeg de module **converteren naar afbeeldings mappen** toe aan het canvas. U kunt deze module vinden in de categorie ' Computer Vision/image-gegevens transformatie ' in de module lijst. Verbind deze met de gegevensset van de installatie kopie.
    
3.  Verzend de pijp lijn. Deze module kan worden uitgevoerd op een GPU of CPU.

## <a name="results"></a>Resultaten

De uitvoer van de modus **converteren naar afbeeldings mappen** is in de indeling **afbeeldings mappen** en kan worden verbonden met andere installatie kopieën met betrekking tot de indeling van de invoer poort, ook wel afbeelding map.

![Converteren naar afbeelding map-uitvoer](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>Technische opmerkingen 

###  <a name="expected-inputs"></a>Verwachte invoer  

| Naam          | Type                  | Beschrijving   |
| ------------- | --------------------- | ------------- |
| Invoer gegevensset | AnyDirectory, ZipFile | Invoer gegevensset |

###  <a name="output"></a>Uitvoer  

| Naam                   | Type           | Beschrijving            |
| ---------------------- | -------------- | ---------------------- |
| Uitvoer afbeelding map | ImageDirectory | Uitvoer afbeelding map |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.