---
title: 'Kolommen toevoegen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module kolommen toevoegen in de ontwerp functie voor het slepen en neerzetten Azure Machine Learning om twee gegevens sets samen te voegen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 36de827dff239dbeebc66e330a76b7a65fefb909
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421954"
---
# <a name="add-columns-module"></a>Module kolommen toevoegen

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om twee gegevens sets samen te voegen. U combineert alle kolommen uit de twee gegevens sets die u opgeeft als invoer om één gegevensset te maken. Als u meer dan twee gegevens sets wilt samen voegen, gebruikt u meerdere exemplaren van de **kolom toevoegen**.



## <a name="how-to-configure-add-columns"></a>Het configureren van kolommen toevoegen
1. Voeg de module **Columns toevoegen** toe aan de pijp lijn.

2. Verbind de twee gegevens sets die u wilt samen voegen. Als u meer dan twee gegevens sets wilt combi neren, kunt u meerdere combi Naties van **toevoegen kolommen samen voegen**.

    - Het is mogelijk om twee kolommen te combi neren met een verschillend aantal rijen. De uitvoer gegevensset wordt aangevuld met ontbrekende waarden voor elke rij in de kleinere bron kolom.

    - U kunt geen afzonderlijke kolommen kiezen om toe te voegen. Alle kolommen van elke gegevensset worden samengevoegd wanneer u **kolommen toevoegen** gebruikt. Als u alleen een subset van de kolommen wilt toevoegen, gebruikt u kolommen selecteren in gegevensset om een gegevensset met de gewenste kolommen te maken.

3. Verzend de pijp lijn.

### <a name="results"></a>Resultaten
Nadat de pijp lijn is uitgevoerd:

- Als u de eerste rijen van de nieuwe gegevensset wilt zien, klikt u met de rechter muisknop op de module **kolommen toevoegen** en selecteert u visualiseren. Of selecteer de module en schakel over naar het tabblad **uitvoer** in het rechterdeel venster, klik op het histogram pictogram in de **poort uitvoer** om het resultaat te visualiseren.

Het aantal kolommen in de nieuwe gegevensset is gelijk aan de som van de kolommen van beide invoer gegevens sets.

Als er twee kolommen met dezelfde naam in de invoer gegevens sets staan, wordt een numeriek achtervoegsel toegevoegd aan de naam van de kolom. Als er bijvoorbeeld twee exemplaren van een kolom met de naam TargetOutcome zijn, wordt de naam van de linkerkolom TargetOutcome_1 gewijzigd en wordt de naam van de rechter kolom gewijzigd TargetOutcome_2.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 