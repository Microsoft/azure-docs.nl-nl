---
title: 'Gegevens hand matig invoeren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de module gegevens hand matig invoeren in Azure Machine Learning kunt gebruiken om een kleine gegevensset te maken door waarden te typen. De gegevensset kan meerdere kolommen bevatten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90908081"
---
# <a name="enter-data-manually-module"></a>De module gegevens hand matig invoeren

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik de module **gegevens hand matig invoeren** om een kleine gegevensset te maken door waarden te typen. De gegevensset kan meerdere kolommen bevatten.
  
Deze module kan handig zijn in scenario's zoals:  
  
- Er wordt een kleine set waarden voor testen gegenereerd.  
- Een korte lijst met labels maken.  
- Het typen van een lijst met kolom namen die moeten worden ingevoegd in een gegevensset.

## <a name="create-a-dataset"></a>Een gegevensset maken 
  
1. Voeg de module [gegevens hand matig invoeren](./enter-data-manually.md) toe aan de pijp lijn. U kunt deze module vinden in de categorie **gegevens invoer en uitvoer** in azure machine learning. 
  
1. Selecteer een van de volgende opties voor **DataFormat**. Deze opties bepalen hoe de gegevens die u opgeeft, moeten worden geparseerd. De vereisten voor elke indeling verschillen aanzienlijk, dus zorg ervoor dat u de verwante onderwerpen leest.  
  
   - **ARFF**: bestands indeling voor kenmerk relatie die wordt gebruikt door Fridge.   
   - **CSV**: indeling met door komma's gescheiden waarden. Zie [Convert to CSV](./convert-to-csv.md)(Engelstalig) voor meer informatie.    
   - **SVMLight**: indeling die wordt gebruikt door Vowpal Wabbit en andere machine learning frameworks.    
   - **TSV**: notatie van door tabs gescheiden waarden.

   Als u een indeling kiest en geen gegevens levert die voldoen aan de indelings specificaties, treedt er een runtime-fout op.
  
1. Klik in het tekstvak **Data** om te beginnen met het invoeren van gegevens. Voor de volgende indelingen is speciale aandacht vereist:  
  
   - **CSV**: als u meerdere kolommen wilt maken, plakt u een door komma's gescheiden tekst of typt u meerdere kolommen door komma's tussen velden te gebruiken.
  
     Als u de optie **HasHeader** selecteert, kunt u de eerste rij met waarden gebruiken als de kolomkop.  
  
     Als u deze optie uitschakelt, worden de kolom namen (Kol1, col2, enzovoort) gebruikt. U kunt later kolom namen toevoegen of wijzigen met behulp van [meta gegevens bewerken](./edit-metadata.md).  
  
   - **TSV**: als u meerdere kolommen wilt maken, plakt u een door tabs gescheiden tekst of typt u meerdere kolommen door tabs te gebruiken tussen velden.  
  
     Als u de optie **HasHeader** selecteert, kunt u de eerste rij met waarden gebruiken als de kolomkop.  
  
     Als u deze optie uitschakelt, worden de kolom namen (Kol1, col2, enzovoort) gebruikt. U kunt later kolom namen toevoegen of wijzigen met behulp van [meta gegevens bewerken](./edit-metadata.md).  
  
   - **ARFF**: plakken in een bestaand ARFF-indelings bestand. Als u waarden rechtstreeks typt, moet u de optionele header en de vereiste kenmerk velden toevoegen aan het begin van de gegevens. 

     De volgende koptekst-en kenmerk rijen kunnen bijvoorbeeld worden toegevoegd aan een eenvoudige lijst. De kolomkop zou zijn `SampleText` . Houd er rekening mee dat het teken reeks type niet wordt ondersteund.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Typ of plak waarden met behulp van de SVMLight-indeling.  
  
     Het volgende voor beeld vertegenwoordigt bijvoorbeeld de eerste paar regels van de SVMLight-gegevensset van de bloed donatie:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Wanneer u de module [gegevens hand matig invoeren](./enter-data-manually.md) uitvoert, worden deze regels als volgt geconverteerd naar een gegevensset van kolommen en index waarden:  
  
     |Kolom 1|Kolom 2|Col3|Col4|Labels|  
     |-|-|-|-|-|  
     |0,00016|0,004|0,999961|0,00784|1|  
     |0|0,004|0,999955|0,008615|1|  
  
1. Selecteer de Enter-toets na elke rij om een nieuwe regel te starten.      
     
   Als u meerdere keren selecteert om meerdere lege navolgende rijen toe te voegen, worden de lege rijen verwijderd of afgekapt.  
  
   Als u rijen met ontbrekende waarden maakt, kunt u deze altijd later filteren.  
  
1. Verbind de uitvoer poort met andere modules en voer de pijp lijn uit.  
  
   Als u de gegevensset wilt weer geven, klikt u met de rechter muisknop op de module en selecteert u **visualiseren**.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 