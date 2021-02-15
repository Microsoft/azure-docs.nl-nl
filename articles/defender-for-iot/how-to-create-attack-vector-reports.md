---
title: Aanvals vector rapporten maken
description: Aanvals vector rapporten bieden een grafische weer gave van de beveiligings keten van apparaten die kunnen worden misbruikt.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e9960fc2120add845be8feda9bafdef95a9b5f94
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522322"
---
# <a name="attack-vector-reporting"></a>Aanvals vector rapportage

## <a name="about-attack-vector-reports"></a>Over aanvals vector rapporten

Aanvals vector rapporten bieden een grafische weer gave van de beveiligings keten van apparaten die kunnen worden misbruikt. Deze beveiligings problemen kunnen een aanvaller toegang geven tot belang rijke netwerk apparaten. De aanvals vector Simulator berekent aanvals vectoren in realtime en analyseert alle aanvals vectoren voor een specifiek doel.

Als u met de aanvals vector werkt, kunt u het effect van de oplossings activiteiten in de aanvals volgorde evalueren. U kunt vervolgens bepalen wanneer een systeem upgrade het pad van de aanvaller verstoort door de aanvals keten te verbreken of als een alternatief pad naar een aanval blijft. Deze informatie helpt u bij het bepalen van de prioriteit van herstel-en oplossings activiteiten.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Bekijk uw waarschuwingen in het beheer centrum.":::

> [!NOTE]
> Beheerders en beveiligings analisten kunnen de procedures uitvoeren die in deze sectie worden beschreven.

## <a name="create-an-attack-vector-report"></a>Een aanvals vector rapport maken

Een aanvals vector simulatie maken:

1. Selecteer :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="plus teken":::in het menu aan de linkerkant om een simulatie toe te voegen.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="De simulatie van de aanvals vector.":::

2. Simulatie-eigenschappen invoeren:

   - **Naam**: naam van simulatie.

   - **Maximale vectoren**: het maximum aantal vectoren in één simulatie.

   - **Weer geven in apparaattoewijzing**: de aanvals vector weer geven als een filter op de apparaattoewijzing.

   - **Alle bron apparaten**: met de aanvals vector worden alle apparaten als een aanvals bron beschouwd.

   - **Aanvals bron**: bij de aanvals vector worden alleen de opgegeven apparaten als een aanvals bron beschouwd.

   - **Alle doel apparaten**: met de aanvals vector worden alle apparaten als een aanvals doel beschouwd.

   - **Aanvals doel**: met de aanvals vector worden alleen de opgegeven apparaten als een aanvals doel beschouwd.

   - **Apparaten uitsluiten**: opgegeven apparaten worden uitgesloten van de simulatie van de aanvals vector.

   - **Subnetten uitsluiten**: opgegeven subnetten worden uitgesloten van de simulatie van de aanvals vector.

3. Selecteer **simulatie toevoegen**. De simulatie wordt toegevoegd aan de lijst simulaties.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Voeg een nieuwe simulatie toe.":::

4. Selecteer :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: deze optie als u de simulatie wilt bewerken.

   Selecteer :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: deze optie als u de simulatie wilt verwijderen.

   Selecteer :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: deze optie als u de simulatie wilt markeren als een favoriet.

5. Er wordt een lijst met aanvals vectoren weer gegeven en deze bevat een vector Score (van 100), een aanvals bron apparaat en een aanvals doel apparaat. Selecteer een specifieke aanval voor grafische weer gave van aanvals vectoren.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Aanvals vectoren.":::

## <a name="next-steps"></a>Volgende stappen

[Aanvals vector rapportage](how-to-create-attack-vector-reports.md)


