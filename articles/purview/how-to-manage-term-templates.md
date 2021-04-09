---
title: Term sjablonen voor de woorden lijst voor bedrijven beheren
description: Meer informatie over het beheren van term sjablonen voor zakelijke woorden lijst in een Azure controle sfeer liggen Data Catalog.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553344"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Term sjablonen voor de woorden lijst voor bedrijven beheren

Met Azure controle sfeer liggen kunt u een woorden lijst maken met termen die belang rijk zijn voor het verrijken van uw gegevens. Elke nieuwe term die wordt toegevoegd aan uw controle sfeer liggen-Data Catalog verklarende woorden lijst is gebaseerd op een term sjabloon waarmee de velden voor de term worden bepaald. In dit artikel wordt beschreven hoe u een term sjabloon en aangepaste kenmerken maakt die kunnen worden gekoppeld aan de termen van de woorden lijst.

## <a name="manage-term-templates-and-custom-attributes"></a>Term sjablonen en aangepaste kenmerken beheren

U kunt met behulp van term sjablonen aangepaste kenmerken maken, ze groeperen en een sjabloon Toep assen tijdens het maken van voor waarden. Zodra een term is gemaakt, kan de sjabloon die aan de term is gekoppeld, niet meer worden gewijzigd.

1. Selecteer op de pagina **Verklarende termen** de optie **term sjablonen beheren**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Scherm opname van de terminologie > knop term sjablonen beheren.":::

2. Op de pagina worden zowel het systeem als aangepaste kenmerken weer gegeven. Selecteer het tabblad **aangepast** om term sjablonen te maken of te bewerken.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Scherm opname van de terminologie > pagina term sjablonen beheren.":::

3. Selecteer **+ nieuwe term sjabloon** en voer een sjabloon naam en-beschrijving in.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Scherm opname van termen van de woorden lijst > beheer term sjablonen > nieuwe term sjablonen":::

4. Selecteer **+ nieuw kenmerk** om een nieuw aangepast kenmerk voor de term sjabloon te maken. Voer een kenmerk naam en beschrijving in. De naam van het aangepaste kenmerk moet uniek zijn binnen een term sjabloon, maar kan dezelfde naam hebben die in meerdere sjablonen opnieuw kan worden gebruikt.

   Kies het veld type in de lijst met opties **tekst**, **één keuze**, **meerdere keuzen** of  **datums**. U kunt ook een teken reeks voor de standaard waarde opgeven voor tekst veld typen.  Het kenmerk kan ook worden gemarkeerd als **vereist**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Scherm opname van de terminologie > nieuwe kenmerk pagina.":::

5. Als alle aangepaste kenmerken zijn gemaakt, selecteert u **voor beeld** om de volg orde van aangepaste kenmerken te rangschikken. U kunt aangepaste kenmerken in de gewenste volg orde slepen en neerzetten.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Scherm opname van de termen van de woorden lijst > sjabloon voor de voorbeeld term.":::

6. Zodra alle aangepaste kenmerken zijn gedefinieerd, selecteert u **maken** om een term sjabloon met aangepaste kenmerken te maken.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Scherm opname van termen van de woorden lijst > nieuwe term sjabloon-maken knop.":::

7. Bestaande aangepaste kenmerken kunnen als verlopen worden gemarkeerd door het selectie **vakje als verlopen** te controleren. Wanneer het kenmerk is verlopen, kan het niet opnieuw worden geactiveerd. Het kenmerk Expires wordt voor bestaande voor waarden grijs weer gegeven. Toekomstige nieuwe voor waarden die zijn gemaakt met deze sjabloon, tonen niet langer het kenmerk dat is gemarkeerd als verlopen.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Scherm opname van de termen van de woorden lijst > kenmerk bewerken om het te markeren als verlopen.":::

## <a name="next-steps"></a>Volgende stappen

Volg de [zelf studie: Maak en importeer woorden lijst termen](tutorial-import-create-glossary-terms.md) voor meer informatie.
