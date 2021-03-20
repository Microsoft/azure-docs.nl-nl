---
title: Azure Automation Wijzigingen bijhouden en voorraad implementatie bereik beperken
description: In dit artikel leest u hoe u met Scope configuraties kunt werken om het bereik van een Wijzigingen bijhouden-en inventaris implementatie te beperken.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92209679"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Het implementatie bereik voor Wijzigingen bijhouden en inventarisatie beperken

In dit artikel wordt beschreven hoe u met Scope configuraties kunt werken wanneer u de functie [Wijzigingen bijhouden en inventarisatie](overview.md) gebruikt om wijzigingen in uw vm's te implementeren. Zie voor meer informatie [doel controle oplossingen in azure monitor (preview-versie)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Over Scope configuraties

Een scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten (query's) die worden gebruikt om het bereik van Wijzigingen bijhouden en inventaris te beperken tot specifieke computers. De scope configuratie wordt gebruikt binnen de Log Analytics-werk ruimte om de computers te richten die moeten worden ingeschakeld. Wanneer u een computer toevoegt aan wijzigingen van de functie, wordt de computer ook toegevoegd aan een opgeslagen zoek opdracht in de werk ruimte.

## <a name="set-the-scope-limit"></a>De scope limiet instellen

Het bereik voor uw Wijzigingen bijhouden-en inventaris implementatie beperken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Automation**. Wanneer u begint te typen, worden in de lijst suggesties weer geven op basis van uw invoer. Selecteer **Automation-accounts**.

3. Selecteer in de lijst met Automation-accounts het account dat u hebt gekozen bij het inschakelen van Wijzigingen bijhouden en inventarisatie.

4. Selecteer in uw Automation-account **gekoppelde werk ruimte** onder **gerelateerde resources**.

5. Klik op **Ga naar werk ruimte**.

6. Selecteer **Scope configuraties (preview)** onder **gegevens bronnen van de werk ruimte**.

7. Selecteer het beletsel teken rechts van de  `MicrosoftDefaultScopeConfig-ChangeTracking` Scope configuratie en klik op **bewerken**.

8. Selecteer in het deel venster bewerken de optie **computer groepen selecteren**. In het deel venster computer groepen worden de opgeslagen Zoek opdrachten weer gegeven die worden gebruikt voor het maken van de scope configuratie. De opgeslagen zoek opdracht die wordt gebruikt door Wijzigingen bijhouden en inventaris is:

    |Name     |Categorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Change tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Selecteer de opgeslagen zoek opdracht om de query die wordt gebruikt om de groep te vullen, weer te geven en te bewerken. In de volgende afbeelding ziet u de query en de resultaten:

    ![Opgeslagen Zoek opdrachten](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden en inventaris beheren](manage-change-tracking.md)als u wilt werken met wijzigingen bijhouden en inventarisatie.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](../troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met functies.
