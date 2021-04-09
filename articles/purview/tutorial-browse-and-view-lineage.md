---
title: 'Zelfstudie: Door assets bladeren in Azure Purview en hun herkomst bekijken'
description: In deze zelfstudie wordt beschreven hoe u naar assets kunt zoeken in de catalogus en hoe u de gegevensherkomst weergeeft.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696090"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Zelfstudie: Door assets bladeren in Azure Purview (preview) en hun herkomst bekijken

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

In deze zelfstudie leert u hoe u kunt zoeken in assets in Azure Purview en hoe u de belangrijke gegevens kunt bekijken, zoals herkomst.

Deze zelfstudie is *deel 3 van een vijfdelige zelfstudie* waarin u de basisprincipes leert van het beheren van gegevens-governance van een gegevensdomein met behulp van Azure Purview. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 2: Ga naar de startpagina van Azure Purview (preview) en zoek naar een asset](tutorial-asset-search.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Zoeken naar assets in de catalogus.
> * De herkomst van assets weergeven.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Zelfstudie: Ga naar de startpagina van Azure Purview (preview) en zoek naar een asset](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Zoeken naar assets in de catalogus

In de vorige zelfstudie hebt u geleerd hoe u met zoeken assets kunt ontdekken in de Azure Purview-catalogus. Een andere manier om assets in de catalogus te ontdekken, is door de bladerervaring van de catalogus te gebruiken.

In deze sectie wordt beschreven hoe u door de Azure Purview-catalogus kunt bladeren.

1. Navigeer naar uw Azure Purview-resource in Azure Portal en selecteer **Purview Studio openen**. U wordt automatisch naar de startpagina van Purview Studio geleid.

1. Selecteer op de **Start** pagina **In assets bladeren**.

   De pagina **In assets bladeren** wordt weergegeven, waarin een samenvatting wordt weergegeven van alle typen assets in uw catalogus.

1. Als u de verschillende beschikbare Azure Data Lake Gen2-assets in uw catalogus wilt verkennen, selecteert u de tegel **Azure Data Lake Gen2**.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Schermopname van de pagina Bladeren op assettype met Azure Data Lake Gen2 geselecteerd.":::

1. Als er meerdere assets zijn, kunt u de kolomheader **Naam** selecteren om de assets alfabetisch te sorteren. In deze zelfstudie is er slechts één Azure Data Lake Storage Gen2-asset.

1. Selecteer de naam van de asset.

1. Selecteer de resourceset **Contoso_GrossProfit_{N}.ssv**. Als deze asset niet aanwezig is in uw catalogus, kiest u een andere.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Lijst met Azure Data Lake Storage Gen2-resources":::

## <a name="view-the-lineage-of-assets"></a>De herkomst van assets weergeven

Bekijk op de pagina Assetgegevens de bron van de gegevens.

1. Selecteer de resourceset **Lineage** tab of the **Contoso_GrossProfit_{N}.ssv**.

   De asset die u hebt geselecteerd, wordt weergegeven. In de herkomstinformatie die u ziet, ziet u dat deze resourceset is gekopieerd van uw Azure Blob-opslagaccount naar Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Schermopname van de weergave Herkomst voor de asset.":::

1. Selecteer de blobasset op deze pagina en selecteer de link **Overschakelen naar asset**.

   Houd er rekening mee dat het venster is overgeschakeld naar de Azure Blob-resourceset, die de bron is van de oorspronkelijke Azure Data Lake Storage Gen2.

1. Selecteer het tabblad **Overzicht** om de asset te onderzoeken en de gegevens te bevestigen.

Voor informatie over het maken van een Azure Data Factory-gegevensstroomactiviteit tussen een Azure Blob- en een Azure Data Lake Storage Gen2-resourceset en het bekijken van de herkomst, zie [Azure Data Factory-gegevensstroomactiviteit](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Zoeken naar assets in de catalogus.
> * De herkomst van assets weergeven.

Ga naar de volgende zelfstudie voor meer informatie over resourcesets, assetgegevens, schema's en classificaties.

> [!div class="nextstepaction"]
> [Resourcesets, assetgegevens, schema's en classificaties](tutorial-schemas-and-classifications.md)
