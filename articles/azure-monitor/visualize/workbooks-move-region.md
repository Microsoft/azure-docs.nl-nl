---
title: Azure Monitor werkmappen-regio's verplaatsen
description: Een werkmap verplaatsen naar een andere regio
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610164"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Een Azure-werkmap verplaatsen naar een andere regio

In dit artikel wordt beschreven hoe u Azure Workbook-resources naar een andere Azure-regio kunt verplaatsen. U kunt uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, voor het implementeren van functies of services die alleen beschikbaar zijn in specifieke regio's, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning.

## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat werkmappen worden ondersteund in de doel regio.

* Deze instructies zijn van toepassing op zowel gedeelde werkmappen ( `microsoft.insights/workbooks` ) als persoonlijke werkmappen () die zijn `microsoft.insights/myworkbooks` opgeslagen in azure monitor en op de meeste bron typen.

  Voor werkmappen die specifiek zijn gekoppeld aan het resource type Application Insights, worden deze werkmappen echter opgeslagen in de Azure-regio waar de Application Insights-bron wordt opgeslagen.

  Deze werkmappen kunnen niet afzonderlijk worden verplaatst naar een andere regio.

## <a name="move"></a>Verplaatsen

De eenvoudigste manier om een Azure-werkmap te verplaatsen, is door ' opslaan als ' te gebruiken in het hulp programma werkmappen in de portal-gebruikers interface:

1. Open de doel werkmap in de werkmap viewer.
2. Gebruik de werkbalk knop bewerken om de bewerkings modus in te voeren.
3. Gebruik de werkbalk knop ' opslaan als '.
4. Kies in het formulier opslaan een naam en de gewenste regio voor de werkmap. Zorg ervoor dat de andere velden voor abonnement, resource groep en delen geschikt zijn.

   > [!NOTE]
   > Mogelijk moet u een nieuwe naam voor de werkmap gebruiken om dubbele namen te voor komen.

5. Sla op. 

## <a name="verify"></a>Verifiëren

Gebruik de Azure-werkmappen Blader interface om de nieuwe werkmap te zoeken. Zorg ervoor dat de locatie de doel locatie is.

## <a name="clean-up"></a>Opschonen

Nadat de werkmap in de nieuwe regio is gemaakt, verwijdert u de oorspronkelijke werkmap in de vorige regio.
1. Open de oorspronkelijke werkmap in de werkmap viewer.
2. Gebruik de werkbalk knop bewerken om de bewerkings modus in te voeren.
3. Kies in de vervolg keuzelijst werkset bewerken (potlood pictogram) de optie werkmap verwijderen.

Als u de naam van uw werkmap wijzigt om deze te importeren in een nieuwe regio, kunt u de werkmap een andere naam geven nadat de oorspronkelijke werkmap is verwijderd met behulp van de vervolg keuzelijst Bewerk hulpprogramma's van de werk balk bewerken.

## <a name="next-steps"></a>Volgende stappen

Wilt u een werkmap sjabloon verplaatsen in plaats van een werkmap? Zie [een Azure-werkmap sjabloon verplaatsen naar een andere regio](./workbook-templates-move-region.md).

Zie [werkmappen en werkmap sjablonen implementeren](../visualize/workbooks-automate.md) via arm-sjablonen.
