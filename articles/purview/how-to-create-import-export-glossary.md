---
title: Termen van een woorden lijst maken, importeren en exporteren
description: Meer informatie over het maken, importeren en exporteren van termen van een woorden lijst in azure controle sfeer liggen.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 7466e143f345ea305c7e9ef118d09fb6f685ac16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694480"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Termen van een woorden lijst maken, importeren en exporteren

In dit artikel wordt beschreven hoe u in azure controle sfeer liggen Data Catalog een term voor een zakelijke woorden lijst maakt en waarin u de termen van de woorden lijst kunt importeren en exporteren met behulp van CSV-bestanden.

## <a name="create-a-new-term"></a>Een nieuwe term maken

Voer de volgende stappen uit om een nieuwe woordenlijst term te maken:

1. Selecteer het pictogram van de woorden lijst in de linkernavigatiebalk op de start pagina om naar de pagina met de lijst met termen te gaan.

2. Selecteer op de pagina **woordenlijst termen** **+ nieuwe term**. Er wordt een pagina geopend met de standaard sjabloon voor het **systeem** geselecteerd. Kies de sjabloon waarvoor u een woordenlijst term wilt maken en selecteer **door gaan**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Scherm afbeelding van het maken van de nieuwe term." border="true":::

3. Geef de nieuwe term een naam, die uniek moet zijn in de catalogus. De term naam is hoofdletter gevoelig, wat betekent dat u een term kunt hebben met de naam **sample** en **sample** in de catalogus.

4. Een **definitie** toevoegen.

5. Stel de **status** voor de term in. Nieuwe voor waarden zijn standaard ingesteld op **concept** status.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Scherm opname van de status keuzes." border="true":::

   Deze status markeringen zijn meta gegevens die zijn gekoppeld aan de term. Op dit moment kunt u de volgende status voor elke term instellen:

   - **Concept**: deze term is nog niet officieel geïmplementeerd.
   - **Goedgekeurd**: deze term is officieel/standaard/goedgekeurd.
   - **Verlopen**: deze term mag niet meer worden gebruikt.
   - **Waarschuwing**: deze term vereist aandacht.

6. Voeg **resources** en een **acroniem** toe. Als de term deel uitmaakt van de hiërarchie, kunt u in het tabblad Overzicht bovenliggende termen op het **bovenliggende** item toevoegen.

7. Voeg **synoniemen** en **gerelateerde voor waarden** toe op het tabblad verwante.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Scherm afbeelding van de nieuwe term > gerelateerde tabblad." border="true":::

8. Selecteer eventueel het tabblad **contact personen** om experts en technici toe te voegen aan uw termijn.

9. Selecteer **maken** om uw term te maken.

## <a name="import-terms-into-the-glossary"></a>Voor waarden in de woorden lijst importeren

De Azure controle sfeer liggen Data Catalog biedt een sjabloon. CSV-bestand waarin u uw voor waarden in uw woorden lijst kunt importeren.

U kunt voor waarden in de catalogus importeren. De dubbele voor waarden in het bestand worden overschreven.

U ziet dat de termen namen hoofdletter gevoelig zijn. `Sample`En `saMple` kunnen bijvoorbeeld beide bestaan in dezelfde verklarende woorden lijst.

### <a name="to-import-terms-follow-these-steps"></a>Voer de volgende stappen uit om voor waarden te importeren:

1. Wanneer u zich op de pagina met **woordenlijst termen** bevindt, selecteert u **voor waarden importeren**.

2. De pagina term sjabloon wordt geopend. Vergelijk de term sjabloon met de soort. CSV dat u wilt importeren.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Scherm afbeelding van de pagina met de termen woorden, de knop voor waarden importeren.":::

3. Down load de CSV-sjabloon en gebruik deze om uw voor waarden in te voeren die u wilt toevoegen.

   > [!Important]
   > Het systeem biedt alleen ondersteuning voor het importeren van kolommen die beschikbaar zijn in de sjabloon. In de sjabloon systeem standaard worden alle standaard kenmerken weer.
   > Aangepaste term sjablonen hebben echter de kenmerken van Box en aanvullende aangepaste kenmerken die in de sjabloon zijn gedefinieerd. Daarom is het. CSV-bestand verschilt zowel van het totale aantal kolommen als van kolom namen, afhankelijk van de geselecteerde term sjabloon. U kunt het bestand ook controleren op problemen na het uploaden.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Scherm afbeelding van de pagina met woordenlijst termen, Selecteer bestand om te importeren.":::

4. Wanneer u klaar bent met het invullen van het. CSV-bestand, selecteert u het bestand dat u wilt importeren en selecteert u **OK**.

5. Het bestand wordt door het systeem geüpload en alle voor waarden aan uw catalogus toegevoegd.

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Termen uit de woorden lijst exporteren met aangepaste kenmerken

U moet termen uit de woorden lijst kunnen exporteren zolang de geselecteerde termen tot dezelfde term sjabloon behoren.

1. Wanneer u zich in de woorden lijst bevindt, wordt standaard de knop **exporteren** uitgeschakeld. Wanneer u de voor waarden selecteert die u wilt exporteren, wordt de knop **exporteren** ingeschakeld als de geselecteerde termen bij dezelfde sjabloon horen.

2. Selecteer **exporteren** om de geselecteerde voor waarden te downloaden.

 > [!Important]
   > Als de voor waarden in een hiërarchie deel uitmaken van verschillende term sjablonen, moet u ze in een andere periode splitsen. CSV-bestanden die moeten worden geïmporteerd. Het bijwerken van een bovenliggend item van een term wordt op dit moment niet ondersteund met het import proces.


## <a name="next-steps"></a>Volgende stappen

Volg de [zelf studie: Maak en importeer woorden lijst termen](tutorial-import-create-glossary-terms.md) voor meer informatie.
