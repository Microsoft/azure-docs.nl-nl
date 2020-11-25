---
title: Waarden inperken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module clip values in Azure Machine Learning voor het detecteren van uitbijters en het knippen of vervangen van hun waarden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014887"
---
# <a name="clip-values"></a>Waarden inperken

In dit artikel wordt een module van Azure Machine Learning Designer beschreven.

Gebruik de module voor clip waarden voor het identificeren en vervangen van gegevens waarden die boven of onder een bepaalde drempel waarde vallen, met een gemiddelde, constante of andere vervanger.  

U koppelt de module aan een gegevensset met de getallen die u wilt knippen. Kies de kolommen waarmee u wilt werken en stel vervolgens een drempel waarde of waardebereik in en een vervangings methode. De module kan alleen de resultaten uitvoeren, of de gewijzigde waarden die zijn toegevoegd aan de oorspronkelijke gegevensset.

## <a name="how-to-configure-clip-values"></a>Clip waarden configureren

Voordat u begint, identificeert u de kolommen die u wilt knippen en de methode die u wilt gebruiken. Het is raadzaam om eerst alle knip methoden te testen op een kleine subset van gegevens.

De module past dezelfde criteria en vervangende methode toe op **alle** kolommen die u in de selectie opneemt. Zorg er daarom voor dat u kolommen uitsluit die u niet wilt wijzigen.

Als u knip methoden of andere criteria op sommige kolommen wilt Toep assen, moet u een nieuw exemplaar van de **clip waarden** voor elke set vergelijk bare kolommen gebruiken.

1.  Voeg de module **clip values** toe aan de pijp lijn en verbind deze met de gegevensset die u wilt wijzigen. U kunt deze module vinden onder **gegevens transformatie**, in de categorie **schalen en verminderen** . 
  
1.  Gebruik in de **lijst met kolommen** de kolom kiezer om de kolommen te kiezen waarop **clip waarden** worden toegepast.  
  
1.  Kies een van de volgende opties in de vervolg keuzelijst om **drempel waarden** in te stellen. Deze opties bepalen hoe u de boven-en ondergrens instelt voor acceptabele waarden versus waarden die moeten worden afgekapt.  
  
    - **ClipPeaks**: wanneer u waarden bijsnijden op pieken, geeft u alleen een bovenste grens op. Waarden die groter zijn dan die grens waarde worden vervangen.
  
    -  **ClipSubpeaks**: wanneer u waarden bijsnijden op subpieken, geeft u slechts een ondergrens op. Waarden die kleiner zijn dan die grens waarde, worden vervangen.  
  
    - **ClipPeaksAndSubpeaks**: wanneer u waarden bijsnijden met pieken en subpieken, kunt u zowel de boven-als de onderste grenzen opgeven. Waarden die buiten het bereik vallen, worden vervangen. Waarden die overeenkomen met de grens waarden worden niet gewijzigd.
  
1.  Afhankelijk van uw selectie in de vorige stap, kunt u de volgende drempel waarden instellen: 

    + **Onderste drempel waarde**: alleen weer gegeven als u **ClipSubPeaks** hebt gekozen
    + **Hoogste drempel waarde**: alleen weer gegeven als u **ClipPeaks** hebt gekozen
    + **Drempel waarde**: alleen weer gegeven als u **ClipPeaksAndSubPeaks** hebt gekozen

    Kies voor elk type drempel een **constante** of een **percentiel**.

1. Als u **constant** selecteert, typt u de maximum-of minimum waarde in het tekstvak. Stel dat u weet dat de waarde 999 is gebruikt als een tijdelijke aanduiding. U kunt een **constante** kiezen voor de bovenste drempel waarde, en 999 invoeren in **constantewaarde voor bovengrens**.
  
1. Als u **percentiel** kiest, beperkt u de kolom waarden tot een percentiel bereik. 

    Stel bijvoorbeeld dat u alleen de waarden in het bereik van 10-80 percentiel wilt blijven gebruiken en alle andere wilt vervangen. Kies **percentiel** en typ vervolgens 10 voor **percentiel waarde voor onderste drempel** en typ 80 voor **percentiel waarde voor hoogste drempel**. 

    Zie de sectie over [percentielen](#examples-for-clipping-using-percentiles) voor een aantal voor beelden van het gebruik van percentiel bereiken.  
  
1.  Definieer een vervangende waarde.

    Getallen die exact overeenkomen met de grenzen die u hebt opgegeven, worden beschouwd als binnen het toegestane bereik van waarden en worden dus niet vervangen. Alle getallen die buiten het opgegeven bereik vallen, worden vervangen door de vervangende waarde. 
  
    + **Vervang waarde voor pieken**: definieert de waarde die moet worden vervangen voor alle kolom waarden die groter zijn dan de opgegeven drempel.  
    + **Vervang waarde voor subpieken**: definieert de waarde die moet worden gebruikt als vervanging voor alle kolom waarden die kleiner zijn dan de opgegeven drempel.  
    + Als u de optie **ClipPeaksAndSubpeaks** gebruikt, kunt u afzonderlijke vervangings waarden opgeven voor de waarden voor boven en onder afgekapt.  

    De volgende vervangings waarden worden ondersteund:  
  
    -   **Drempelwaarde**: Hiermee worden afgekapte waarden vervangen door de opgegeven drempel waarde.  
  
    -   **Gemiddelde**: Hiermee worden afgekapte waarden vervangen door het gemiddelde van de kolom waarden. Het gemiddelde wordt berekend voordat waarden worden afgekapt.  
  
    -   **Mediaan**: Hiermee worden afgekapte waarden vervangen door de mediaan van de kolom waarden. De mediaan wordt berekend voordat waarden worden afgekapt.   
  
    -   **Ontbrekend**. Hiermee worden afgekapte waarden vervangen door de ontbrekende (lege) waarde.  
  
1.  **Indicator kolommen toevoegen**: Selecteer deze optie als u een nieuwe kolom wilt genereren die aangeeft of de opgegeven knip bewerking moet worden toegepast op de gegevens in die rij. Deze optie is handig wanneer u een nieuwe set knip-en vervangings waarden test.  
  
1. **Vlag voor overschrijven**: Geef aan hoe u de nieuwe waarden wilt genereren. Standaard maken **clip waarden** een nieuwe kolom met de piek waarden die zijn afgekapt tot de gewenste drempel waarde. Nieuwe waarden overschrijven de oorspronkelijke kolom.  
  
    Als u de oorspronkelijke kolom wilt hand haven en een nieuwe kolom met de afgekapte waarden wilt toevoegen, schakelt u deze optie uit.  
  
1.  Verzend de pijp lijn.  
  
    Klik met de rechter muisknop op de module **clip waarden** , selecteer **visualiseren** of selecteer de module en schakel over naar het tabblad **uitvoer** in het rechterdeel venster, klik op het histogram pictogram in de **poort uitvoer** om de waarden te controleren en ervoor te zorgen dat de knip bewerking aan uw verwachtingen voldoet.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Voor beelden voor knippen met behulp van percentielen

Als u wilt weten hoe knippen door percentielen werkt, kunt u een gegevensset met 10 rijen gebruiken die één exemplaar hebben elk van de waarden 1-10.  
  
- Als u percentiel als de bovenste drempel waarde gebruikt, wordt voor het negen tigste percentiel 90 procent van alle waarden in de gegevensset kleiner dan die waarde.  
  
- Als u percentiel als de laagste drempel waarde gebruikt, op het tiende percentiel, moet 10 procent van alle waarden in de gegevensset kleiner zijn dan die waarde.  
  
1.  Kies **ClipPeaksAndSubPeaks** voor **set met drempel waarden**.  
  
1.  Voor **bovenste drempel waarde** kiest u **percentiel** en voor **percentiel nummer**, typt u 90.  
  
1.  Kies **ontbrekende waarde** voor **bovenste vervangende waarde**.  
  
1.  Voor een **lagere drempel waarde** kiest u **percentiel** en voor **percentiel nummer**, type 10.  
  
1.  Kies **ontbrekende waarde** voor **lagere vervangende waarde**.  
  
1.  Schakel de optie **overschrijvings vlag** uit en selecteer de optie de **kolom indicator toevoegen**.  
  
Probeer nu dezelfde pijp lijn te 60 gebruiken als de bovenste percentiel drempel en 30 als de laagste percentiel drempel en gebruik de drempel waarde als de vervangings waarde. De volgende tabel vergelijkt deze twee resultaten:  
  
1.  Vervangen door ontbreekt; Bovenste drempel waarde = 90; Laagste drempel waarde = 20  
  
1.  Vervangen door drempel waarde; Bovenste percentiel = 60; Lager percentiel = 40  
  
|Oorspronkelijke gegevens|Vervangen door ontbrekende|Vervangen door drempel waarde|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, ONWAAR<br /><br /> 4, ONWAAR<br /><br /> 5, ONWAAR<br /><br /> 6, ONWAAR<br /><br /> 7, ONWAAR<br /><br /> 8, ONWAAR<br /><br /> 9, ONWAAR<br /><br /> TRUE|4, WAAR<br /><br /> 4, WAAR<br /><br /> 4, WAAR<br /><br /> 4, WAAR<br /><br /> 5, ONWAAR<br /><br /> 6, ONWAAR<br /><br /> 7, WAAR<br /><br /> 7, WAAR<br /><br /> 7, WAAR<br /><br /> 7, WAAR| 
 
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
