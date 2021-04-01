---
title: Azure Monitor werkmap sjablonen-regio's verplaatsen
description: Een werkmap sjabloon verplaatsen naar een andere regio
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 9787118b1912a5697c02e116be99b1f396748937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100609293"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Een Azure Workbook-sjabloon verplaatsen naar een andere regio

In dit artikel wordt beschreven hoe u bronnen van Azure-werkmap sjablonen verplaatst naar een andere Azure-regio. U kunt uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, voor het implementeren van functies of services die alleen beschikbaar zijn in specifieke regio's, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning.

Er is momenteel geen portal-gebruikers interface voor het maken van werkmap sjabloon resources. de enige huidige manier om ze te maken, is [via Azure Resource Manager sjabloon (arm-sjabloon) implementaties](../visualize/workbooks-automate.md). De eenvoudigste manier om een sjabloon te verplaatsen, is als zodanig dat u de vorige ARM-sjabloon opnieuw moet gebruiken en bijwerken zodat deze kan worden geïmplementeerd in de nieuwe regio.

## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat werkmap sjablonen worden ondersteund in de doel regio.

## <a name="prepare"></a>Voorbereiden

* Bepaal de eerder gebruikte ARM-sjabloon voor de werkmap sjabloon.

## <a name="move"></a>Verplaatsen

1. Werk de eerder gebruikte sjabloon bij om te verwijzen naar de nieuwe regio.

   > [!NOTE]
   > Mogelijk moet u een nieuwe naam voor de werkmap sjabloon gebruiken om dubbele namen te voor komen.

2. Implementeer de bijgewerkte sjabloon via ARM-sjabloon implementatie om een nieuwe werkmap sjabloon te maken in de gewenste regio.

## <a name="verify"></a>Verifiëren

Gebruik de Azure-werkmappen Blader interface om de zojuist geïmplementeerde werkmap sjabloon te vinden. Zorg ervoor dat de locatie de doel locatie is.

## <a name="clean-up"></a>Opschonen

Als uw werkmap sjabloon in de nieuwe regio is gemaakt, verwijdert u de oorspronkelijke werkmap sjabloon in de vorige regio.
1. Zoek de werkmap sjabloon in de Azure-werkmappen bladeren in de gebruikers interface.
2. Selecteer de werkmap sjabloon die u wilt verwijderen.
3. Selecteer de opdracht verwijderen.

Als u de naam van uw werkmap sjabloon hebt gewijzigd om deze te importeren in een nieuwe regio, kunt u de werkmap sjabloon een andere naam geven nadat het oorspronkelijke item is verwijderd met behulp van de opdracht naam wijzigen in de resource weergave van de Azure-werkmap sjabloon.

## <a name="next-steps"></a>Volgende stappen

Wilt u een werkmap verplaatsen in plaats van een sjabloon? Zie [een Azure-werkmap verplaatsen naar een andere regio](./workbooks-move-region.md).

