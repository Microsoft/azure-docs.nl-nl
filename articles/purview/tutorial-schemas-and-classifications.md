---
title: "Zelfstudie: Resourcesets, gegevens, schema's en classificaties verkennen in Azure Purview (preview)"
description: In deze zelfstudie wordt beschreven hoe u resourcesets, asset-details, schema's en classificaties gebruikt.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693319"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Zelfstudie: Resourcesets, gegevens, schema's en classificaties verkennen in Azure Purview (preview)

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

In deze zelfstudie gaat u de belangrijkste onderdelen van uw catalogus verkennen: resourcesets, assetgegevens, schema's en classificaties.

Deze zelfstudie is *deel 4 van een vijfdelige zelfstudie* waarin u de basisprincipes leert van het beheren van gegevens-governance van een gegevensdomein met behulp van Azure Purview. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 3: Blader door assets in Azure Purview (preview) en bekijk hun herkomst](tutorial-browse-and-view-lineage.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Resourcesets weergeven.
> * Assetgegevens weergeven.
> * Bewerk het schema en voeg classificaties toe.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Zelfstudie:  Blader door assets in Azure Purview (preview) en bekijk hun herkomst](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="view-resource-sets"></a>Resourcesets weergeven

Een resourceset is een enkel object in de catalogus dat veel fysieke objecten in de opslag vertegenwoordigt. De objecten delen vaak een gemeenschappelijk schema, en in de meeste gevallen een naamconventie of mapstructuur. De datumnotatie is bijvoorbeeld *jjjj/mm/dd*. Zie [Resourcesets begrijpen](concept-resource-sets.md) voor meer informatie over resourcesets.

1. Navigeer naar uw Azure Purview-resource in Azure Portal en selecteer **Purview Studio openen**. U wordt automatisch naar de startpagina van Purview Studio geleid.

2. Voer **contoso_staging_positivecashflow_ n** in het vak **Assets zoeken** in en selecteer vervolgens **Contoso_Staging_PositiveCashFlow_{N}.csv** in de zoekresultaten.

## <a name="view-asset-details"></a>Assetgegevens weergeven

1. Op het tabblad **Overzicht** worden de **Beschrijving**, **Woordenlijsttermen** en **Eigenschappen** weergegeven, zoals de **qualifiedName**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Schermopname van het tabblad Overzicht van een assetpagina van een resourceset.":::

1. Let op de volgende twee velden onder **Eigenschappen**:

   * **partitionCount**: Hiermee wordt het aantal fysieke bestanden aangegeven dat is gekoppeld aan deze resourceset.
   * **schemaCount**: Hiermee wordt het aantal variaties van het schema aangegeven dat in deze resourceset is gevonden.

   Azure Purview vult deze eigenschappen binnen 24 uur nadat u de scan in [deel 1 van deze reeks zelfstudies](tutorial-scan-data.md) hebt voltooid.

1. Selecteer het tabblad **Contactpersonen** om de waarden van **Experts** en **Eigenaren** te controleren.

## <a name="edit-the-schema-and-add-classifications"></a>Het schema bewerken en classificaties toevoegen

1. Selecteer het tabblad **Schema**. Bekijk de kolomnamen en de classificaties die eraan zijn gekoppeld. U ziet dat de eigenschappen automatisch zijn ingevuld met de scan.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Schermopname van het tabblad Schema.":::

1. Als u de asset wilt bewerken, selecteert u de knop **Bewerken** in de linkerbovenhoek. Selecteer vervolgens het tabblad **Schema**.

1. Voeg een **Beschrijving** toe voor een kolom of wijzig de naam van de kolom in een meer beschrijvende naam.

1. Voeg een classificatie toe op het niveau van de asset door de vervolgkeuzelijst **Classificatie van kolomniveau** te selecteren voor een kolomnaam die geen ingestelde classificatie heeft.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Bewerkingspagina voor Schema":::

1. Wanneer u klaar bent met uw wijzigingen, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Resourcesets weergeven.
> * Assetgegevens weergeven.
> * Bewerk het schema en voeg classificaties toe.

Ga naar de volgende zelfstudie voor meer informatie over de woordenlijst en hoe u nieuwe begrippen voor assets definieert en importeert.

> [!div class="nextstepaction"]
> [Woordenlijsttermen maken en importeren](tutorial-import-create-glossary-terms.md)