---
title: 'Procedure: bladeren door de Data Catalog'
description: Dit artikel geeft een overzicht van de manier waarop u door de Azure controle sfeer liggen-Data Catalog op basis van het type activum kunt bladeren.
author: hrasheed-msft
ms.author: hrasheed
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: b8cdbbc29472ae10920c347dde308c352bf0b68a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552479"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Bladeren door de Azure controle sfeer liggen Data Catalog

In dit artikel wordt beschreven hoe u gegevens in uw Azure controle sfeer liggen kunt detecteren Data Catalog met behulp van de hiërarchische naam ruimte van de gegevens bron.

## <a name="browse-experience"></a>Browse-ervaring

Een gegevens verbruiker kan gegevens detecteren met behulp van de vertrouwde hiërarchische naam ruimte voor elk van de gegevens bronnen met behulp van een Verkenner-weer gave. Zodra de gegevens bron is geregistreerd en gescand, haalt de gegevens kaart informatie over de structuur (hiërarchische naam ruimte) van de gegevens bron op. Deze informatie wordt gebruikt om de Surf ervaring voor gegevens detectie te maken.

U kunt bijvoorbeeld eenvoudig een gegevensset met de naam *DateDimension* vinden in een map met de naam *dimensies* in azure data Lake Storage gen 2. U kunt de ervaring ' bladeren per asset type ' gebruiken om naar het opslag account van de ADLS gen 2 te gaan en vervolgens naar de service > container (s) bladeren > map (en) om de map specifieke *dimensies* te bereiken en vervolgens de tabel *DateDimension* weer te geven.

Er wordt een systeem eigen browse-ervaring met een hiërarchische naam ruimte gegeven voor elk van de bijbehorende gegevens bronnen.

## <a name="browse-the-data-catalog-by-asset-type"></a>Bladeren door de Data Catalog per activum type

1. U kunt door gegevens assets bladeren door het **type bladeren per activum** op de start pagina te selecteren.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Start pagina van controle sfeer liggen" border="true":::

1. Op de pagina **typen van assets** worden tegels gecategoriseerd door gegevens bronnen. Als u de assets in elke gegevens bron verder wilt verkennen, selecteert u de betreffende tegel.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Bladeren door activa typen pagina" border="true":::

1. Op de volgende pagina worden assets op het hoogste niveau onder het gekozen gegevens type weer gegeven. Kies een van de assets om de inhoud ervan verder te verkennen.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Specifieke Blader pagina voor het activa type. Voor beeld weer gegeven is Azure Storage account" border="true":::

1. De weer gave Explorer wordt geopend. Start surfen door de Asset te selecteren in het linkerdeel venster. Onderliggende assets worden weer gegeven in het rechterdeel venster van de pagina.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Verkenner-weer gave" border="true":::

1. Als u de details van een Asset wilt weer geven, selecteert u de naam of de knop met de weglatings tekens uiterst rechts.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Selecteer de knop met weglatings tekens om de pagina Asset details weer te geven" border="true":::

## <a name="view-related-data-assets"></a>Gerelateerde gegevensassets weergeven

Zodra u zich op de pagina Asset-Details bevindt, kunt u ook andere gerelateerde gegevens assets bekijken. U kunt bijvoorbeeld naar de bovenliggende map van *DateDimension* navigeren om de map met *dimensies* weer te geven of zelfs naar de container navigeren om de assets onder de specifieke hiërarchie te zien.

1. Selecteer op de pagina activa Details het tabblad **gerelateerd** om andere gerelateerde assets te bekijken.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Tabblad Start gerelateerde" border="true":::

1. De volledige hiërarchie van het huidige activum wordt aan de linkerkant weer gegeven.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Hiërarchische structuur" border="true":::

1. Selecteer een wille keurig niveau in de hiërarchie om de directe activa onder dat niveau weer te geven.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Andere hiërarchie selecteren" border="true":::

## <a name="next-steps"></a>Volgende stappen

- [Termen van een woorden lijst maken, importeren en exporteren](how-to-create-import-export-glossary.md)
- [Term sjablonen voor de woorden lijst voor bedrijven beheren](how-to-manage-term-templates.md)
