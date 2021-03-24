---
title: Activa bulksgewijs bewerken voor label classificaties, woordenlijst termen en contact personen wijzigen
description: Meer informatie over bulksgewijs bewerken van assets in azure controle sfeer liggen.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 149a70ea9b3fad919e771e8eb279e01d1492b3b3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952453"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>Activa bulksgewijs bewerken voor het labelen van termen in de woorden lijst

In dit artikel wordt beschreven hoe u meerdere woordenlijst termen in één actie kunt markeren voor een lijst met geselecteerde assets.

### <a name="add-assets-to-view-selected-list-using-search"></a>Activa toevoegen om de geselecteerde lijst weer te geven met behulp van zoeken

1. Zoek naar het gegevens activum dat u wilt toevoegen aan de lijst voor bulk bewerking.

2. Beweeg de muis aanwijzer op de pagina met zoek resultaten om aan de activa toe te voegen die u wilt toevoegen aan de lijst met **geselecteerde** bulk bewerkingen om een selectie vakje weer te geven.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Scherm afbeelding van het selectie vakje.":::

3. Schakel het selectie vakje in om het toe te voegen aan de geselecteerde lijst van bulk bewerkings **weergave** . Nadat u hebt toegevoegd, ziet u het pictogram geselecteerde items onder aan de pagina.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Scherm afbeelding van de lijst.":::

4. Herhaal de bovenstaande stappen om alle gegevensassets toe te voegen aan de lijst.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Activa toevoegen om de geselecteerde lijst te bekijken van de pagina activa Details

1. Schakel op de pagina Asset-Details het selectie vakje in de rechter bovenhoek in om de activa toe te voegen aan de **geselecteerde** lijst voor bulk bewerkingen.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Scherm afbeelding van de Asset.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Bewerk assets in de lijst geselecteerde weer gave om termen van een woorden lijst toe te voegen, te vervangen of te verwijderen.

1. U bent klaar met het identificeren van alle gegevensassets die bulksgewijs moeten worden bewerkt. Selecteer **geselecteerde lijst weer geven** op de pagina met zoek resultaten of op de pagina Asset Details.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Scherm opname van de weer gave.":::

2. Controleer de lijst en selecteer **verwijderen** als u de voor waarden wilt verwijderen.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Scherm opname van de verwijderen.":::

3. Selecteer bulk bewerking om termen voor de woorden lijst toe te voegen, te verwijderen of te vervangen voor alle geselecteerde activa.

4. Als u de terminologie wilt toevoegen, selecteert u bewerking als **toevoegen**. Selecteer alle termen in de woorden lijst die u wilt toevoegen in de nieuwe waarde. Selecteer Toep assen wanneer u klaar bent.
    - Met een toevoeg bewerking wordt een nieuwe waarde toegevoegd aan de lijst met termen die al zijn gemarkeerd voor gegevensassets.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Scherm afbeelding van de toevoegen.":::

5. Als u de termen wilt vervangen door de woorden lijst, selecteert u bewerking **vervangen door**. Selecteer alle termen in de woorden lijst die u wilt vervangen in de nieuwe waarde. Selecteer Toep assen wanneer u klaar bent.
    - Door te vervangen bewerking worden alle woordenlijst termen voor geselecteerde gegevensassets vervangen door de termen die zijn geselecteerd in nieuwe waarde.
   
6. Als u de termen van de woorden lijst wilt verwijderen, selecteert u bewerking als **verwijderd**. Selecteer Toep assen wanneer u klaar bent.
    - Met de bewerking verwijderen worden alle woordenlijst termen voor geselecteerde gegevensassets verwijderd.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Scherm afbeelding van de voor waarden verwijderen.":::

7. Herhaal bovenstaande stappen voor classificaties, eigen aren en experts.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Scherm afbeelding van de classificaties en contact personen.":::

8. Als u klaar bent, sluit u de Blade bulksgewijs bewerken door **sluiten** of **Alles verwijderen en sluiten** te selecteren. Als u sluiten selecteert, worden de geselecteerde assets niet verwijderd. met alle verwijderen en sluiten worden alle geselecteerde assets verwijderd.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Scherm opname van de afsluiting.":::

   > [!Important]
   > Het aanbevolen aantal assets voor bulk bewerkingen is 25. Als u meer dan 25 selecteert, kunnen er prestatie problemen optreden.
   > Het **geselecteerde vak weer geven** wordt alleen weer gegeven als er ten minste één activum is geselecteerd.


Volg de [zelf studie: Maak en importeer woorden lijst termen](how-to-create-import-export-glossary.md) voor meer informatie.
