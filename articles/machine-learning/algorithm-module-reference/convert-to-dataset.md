---
title: 'Converteren naar DataSet: module-verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module converteren naar gegevensset in Azure Machine Learning Designer om gegevens invoer te converteren naar de indeling van interne gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3ba69a6ddf38083e5665e20081f5f909b7100024
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508483"
---
# <a name="convert-to-dataset"></a>Converteren naar gegevensset

In dit artikel wordt beschreven hoe u de module converteren naar gegevensset in Azure Machine Learning Designer kunt gebruiken om gegevens voor een pijp lijn om te zetten in de interne indeling van de ontwerp functie.
  
Conversie is in de meeste gevallen niet vereist. Azure Machine Learning gegevens worden impliciet geconverteerd naar de indeling van de systeem eigen gegevensset wanneer een bewerking wordt uitgevoerd op de gegevens. 

Het is aan te raden om gegevens op te slaan in de indeling van de gegevensset als u een bepaalde vorm van normalisatie of reiniging hebt uitgevoerd voor een set gegevens en u ervoor wilt zorgen dat de wijzigingen worden gebruikt in andere pijp lijnen.  
  
> [!NOTE]
> Converteren naar dataset wijzigt alleen de indeling van de gegevens. Er wordt geen nieuwe kopie van de gegevens opgeslagen in de werk ruimte. Als u de gegevensset wilt opslaan, dubbelklikt u op de uitvoer poort, selecteert u **Opslaan als gegevensset** en voert u een nieuwe naam in.  
  
## <a name="how-to-use-convert-to-dataset"></a>Converteren naar dataset gebruiken  

U wordt aangeraden de module [meta gegevens bewerken](edit-metadata.md) te gebruiken om de gegevensset voor te bereiden voordat u converteren naar gegevensset gebruikt. U kunt kolom namen toevoegen of wijzigen, gegevens typen aanpassen en zo nodig andere wijzigingen aanbrengen.

1.  Voeg de module converteren naar gegevensset toe aan de pijp lijn. U kunt deze module vinden in de categorie **gegevens transformatie** in de ontwerp functie. 

2. Verbind het met een wille keurige module die een gegevensset uitvoert.   

    Zolang de gegevens [in tabel vorm](/python/api/azureml-core/azureml.data.tabulardataset)zijn, kunt u deze converteren naar een gegevensset. Dit geldt ook voor gegevens die zijn geladen via gegevens [importeren](import-data.md), gegevens die zijn gemaakt via [gegevens hand matig invoeren](enter-data-manually.md)of data sets die zijn getransformeerd via [trans formatie Toep assen](apply-transformation.md).

3.  Geef in de vervolg keuzelijst **actie** aan of u de gegevens wilt opschonen voordat u de gegevensset opslaat:  
  
    - **Geen**: gebruik de gegevens als.  
  
    - **SetMissingValue**: Stel een specifieke waarde in op een ontbrekende waarde in de gegevensset. De standaard aanduiding is het vraag teken (?), maar u kunt de aangepaste optie voor  **ontbrekende waarden** gebruiken om een andere waarde op te geven. Als u bijvoorbeeld een **taxi** voor een **aangepaste ontbrekende waarde** invoert, worden alle exemplaren van de **taxi** in de gegevensset gewijzigd in de ontbrekende waarde.
  
    - **ReplaceValues**: gebruik deze optie om een enkele exacte waarde op te geven die moet worden vervangen door een andere exacte waarde. U kunt ontbrekende waarden of aangepaste waarden vervangen door de **Vervang** methode in te stellen:

      - **Ontbrekend**: Kies deze optie om ontbrekende waarden in de invoer-gegevensset te vervangen. Voer bij **nieuwe waarde** de waarde in om de ontbrekende waarden te vervangen door.
      - **Aangepast**: Kies deze optie om aangepaste waarden in de invoer-gegevensset te vervangen. Voer voor **aangepaste waarde** de waarde in die u wilt zoeken. Als uw gegevens bijvoorbeeld de teken reeks bevatten `obs` die wordt gebruikt als tijdelijke aanduiding voor ontbrekende waarden, voert u in `obs` . Voer bij **nieuwe waarde** de nieuwe waarde in om de oorspronkelijke teken reeks te vervangen door.
  
    Houd er rekening mee dat de bewerking **ReplaceValues** alleen van toepassing is op exacte overeenkomsten. Deze teken reeksen worden bijvoorbeeld niet beïnvloed: `obs.` , `obsolete` .  
 
  
5.  Verzend de pijp lijn.  

## <a name="results"></a>Resultaten

+  Als u de resulterende gegevensset met een nieuwe naam wilt opslaan, selecteert u op het pictogram **gegevensset registreren** onder het tabblad **uitvoer** in het rechterdeel venster van de module.  
  
## <a name="technical-notes"></a>Technische opmerkingen  

-   Elke module die een gegevensset als invoer gebruikt, kan ook gegevens in het CSV-bestand of het TSV-bestand opnemen. Voordat een module code wordt uitgevoerd, worden de invoer voorverwerkt. Preverwerking is gelijk aan het uitvoeren van de module Convert to dataset op de invoer.  
  
-   U kunt niet converteren van de SVMLight-indeling naar een gegevensset.  
  
-   Wanneer u een aangepaste Vervang bewerking opgeeft, is de zoek-en-vervang bewerking van toepassing op de waarden voor volt ooien. Gedeeltelijke overeenkomsten zijn niet toegestaan. U kunt bijvoorbeeld een 3 vervangen door een-1 of met 33, maar u mag een 3 niet vervangen in een getal van twee cijfers zoals 35.  
  
-   Voor aangepaste Vervang bewerkingen mislukt de vervanging op de achtergrond als u een wille keurig teken gebruikt dat niet overeenkomt met het huidige gegevens type van de kolom.  

  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.