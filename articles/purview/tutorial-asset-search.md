---
title: 'Zelfstudie: Ga naar de startpagina van Azure Purview en zoek naar een asset'
description: In deze zelfstudie wordt beschreven hoe u functies gebruikt op de startpagina van Azure Purview en hoe u kunt zoeken in de catalogus.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677835"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Zelfstudie: Ga naar de startpagina van Azure Purview (preview) en zoek naar een asset

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

In deze zelfstudie raakt u vertrouwd met Azure Purview door de functies van de startpagina te verkennen en te zoeken naar een asset in de catalogus.

Dit artikel is *deel 2 van een vijfdelige zelfstudie* waarin u de basisprincipes leert van het beheren van gegevens-governance van een gegevensdomein met behulp van Azure Purview. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 1: Gegevens scannen met Azure Purview](tutorial-scan-data.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Navigeren door de startpagina van Azure Purview.
> * Zoeken naar een asset.
> * Een asseteigenaar toevoegen.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Zelfstudie: Gegevens scannen met Azure Purview](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Navigeren door de startpagina van Azure Purview

De volgende stappen helpen u door de startpagina van Azure Purview.

1. Navigeer naar uw Azure Purview-resource in Azure Portal en selecteer **Purview Studio openen**. U wordt automatisch naar de startpagina van Purview Studio geleid.

   Bovenaan de startpagina ziet u de naam van uw catalogus en een set met catalogusanalyses. Inbegrepen zijn het aantal bronnen, gegevensassets en woordenlijst termen. In de samenvatting ziet u dat er in totaal meer dan 200 assets zijn en 113 woordenlijsttermen. Dit aantal wordt bijgewerkt als uw organisatie meer termen scant en toevoegt aan Azure Purview.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Schermopname van de startpagina van Azure Purview.":::

1. Selecteer **Door assets bladeren** om al uw assets weer te geven.

## <a name="search-for-an-asset"></a>Zoeken naar een asset

Voordat u begint, is dit een snelle opfrisser van wat basisterminologie:

* **Asset**: Een enkel object in de catalogus dat beknopt is en de definitie van een entiteit in het gegevensdomein bevat. Voorbeelden van entiteiten zijn een SQL-tabel, een Power BI-rapport en uw data factory-activiteit.
  
* **Schema**: Een schema, ook wel een kolom of kenmerk genoemd, vertegenwoordigt de structuur van een asset of een resourceset.

* **Resourceset**: Een enkel object in de catalogus dat veel fysieke objecten in de opslag vertegenwoordigt. Deze objecten delen vaak een gemeenschappelijk schema, en in de meeste gevallen een naamconventie of mapstructuur. De datumnotatie is bijvoorbeeld *jjjj/mm/dd*. Zie [Resourcesets begrijpen](concept-resource-sets.md) voor meer informatie.

* **Assettype**: Een groepering van assets en resourcesets onder een logische naam, die meestal wordt toegewezen aan de naam van het gegevensplatform.

Volg deze stappen om te zoeken naar een asset en markeer uzelf als eigenaar:

1. Voer in het vak **Zoeken in catalogus** op uw startpagina de naam in van de resourcegroep die uw gegevensdomein bevat (de resourcegroep die u hebt gemaakt in de vorige zelfstudie). Er wordt een lijst met suggesties weergegeven.

1. Selecteer **Zoekresultaten weergeven**. Omdat al uw assets met de naam van uw resourcegroep beginnen, worden deze allemaal weergegeven in de zoekresultaten. Buiten deze zelfstudie zoekt u naar specifieke assetnamen, niet naar resourcegroepen.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Schermopname van de lijst met suggesties voor zoeken in de catalogus.":::

1. U kunt de filters in het linkernavigatievenster gebruiken om te filteren op assettype, classificatie, contact, label en woordenlijstterm.

1. Als u wilt zoeken naar een resourceset, begint u met het typen van de naam van de set. Er wordt een lijst met zoeksuggesties met de juiste trefwoorden weergegeven. U kunt ook zoeken naar absolute namen door ze tussen aanhalingstekens te plaatsen.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Assets op trefwoord zoeken in Azure Purview":::

## <a name="edit-an-asset"></a>Een asset bewerken

1. Selecteer een van de assets in de zoekresultaten. Selecteer vervolgens **Bewerken**

1. Op het tabblad **Overzicht** kunt u een beschrijving toevoegen.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Schermopname met het veld Beschrijving op het tabblad Overzicht.":::

1. Selecteer het tabblad **Contactpersonen**. Klik naast **Eigenaren** in **Gebruiker of groep selecteren** en typ uw zakelijke e-mailadres.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Schermopname van ingevulde velden.":::

    Er wordt automatisch een naamsuggestie weergegeven.

1. Voer naast **Experts**, in **Gebruiker of groep selecteren** een naam in (bijvoorbeeld de naam van uw manager) en selecteer vervolgens **Opslaan**.

    De velden beschrijving, naam eigenaar en naam expert zijn nu ingevuld.

## <a name="next-steps"></a>Volgende stappen

Neem voordat u verdergaat met de volgende zelfstudie in deze reeks, nog wat extra tijd om zelf de startpagina van Azure Purview te verkennen. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Navigeren door de startpagina van Azure Purview.
> * Zoeken naar een asset.
> * Een asseteigenaar toevoegen.

Ga naar de volgende zelfstudie voor meer informatie over het zoeken naar assets in Azure Purview en het ontdekken van de herkomst van assets.

> [!div class="nextstepaction"]
> [Door assets bladeren en hun herkomst bekijken](tutorial-browse-and-view-lineage.md)
