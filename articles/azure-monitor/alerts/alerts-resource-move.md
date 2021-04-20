---
title: Waarschuwingsregels of actieregels bijwerken wanneer de doelresource wordt verplaatst naar een andere Azure-regio
description: Achtergrondinformatie en instructies voor het bijwerken van waarschuwingsregels of actieregels wanneer de doelresource naar een andere Azure-regio wordt verplaatst.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: 727196f274db3abae75a38d3ecdf31a78dec0fab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725940"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Waarschuwingsregels of actieregels bijwerken wanneer de doelresource wordt verplaatst naar een andere Azure-regio

In dit artikel [](./alerts-overview.md) wordt beschreven waarom bestaande waarschuwingsregels en actieregels mogelijk worden beïnvloed wanneer u andere Azure-resources tussen regio's verplaatst en hoe u deze problemen kunt identificeren en oplossen. [](./alerts-action-rules.md) Raadpleeg de documentatie [over het verplaatsen van resources voor](../../azure-resource-manager/management/move-resources-overview.md) meer informatie over wanneer het verplaatsen van resources tussen regio's nuttig is en een controlelijst voor het ontwerpen van een verplaatsproces.

## <a name="why-the-problem-exists"></a>Waarom het probleem bestaat

Waarschuwingsregels en actieregels verwijzen naar andere Azure-resources. Voorbeelden zijn [azure-VM's,](../../site-recovery/azure-to-azure-tutorial-migrate.md) [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)en [Azure Storage.](../../storage/common/storage-account-move.md) Wanneer u de resources verplaatst waarnaar deze regels verwijzen, werken de regels waarschijnlijk niet meer goed omdat ze de resources waarnaar ze verwijzen niet kunnen vinden.

Er zijn twee belangrijke redenen waarom uw regels mogelijk niet meer werken na het verplaatsen van de doelbronnen:

- Het bereik van uw regel verwijst expliciet naar de oude resource.
- Uw waarschuwingsregel is gebaseerd op metrische gegevens.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Regelbereik verwijst expliciet naar de oude resource

Wanneer u een resource verplaatst, wordt de resource-id in de meeste gevallen gewijzigd. Achter de schermen repliceert het systeem de resource naar de nieuwe regio voordat deze uit de oude regio wordt verwijderd. Dit proces vereist dat er twee resources en dus twee verschillende resource-ID's tegelijk bestaan voor een korte periode. Omdat resource-id's uniek moeten zijn, moet er een nieuwe id worden gemaakt tijdens het proces. 

**Hoe is het verplaatsen van de resource van invloed op bestaande regels?**

Waarschuwingsregels en actieregels hebben een bereik van resources waar ze op van toepassing zijn. Het bereik kan een volledig abonnement, een resourcegroep of een of meer specifieke resources zijn.
Hier is bijvoorbeeld een regel met een bereik met twee resources (twee virtuele machines):

![Waarschuwingsregel voor meerdere resources](media/alerts-resource-move/multi-resource-alert-rule.png)

Als in het regelbereik expliciet een resource wordt vermeld en die resource de resource-id heeft verplaatst en gewijzigd, wordt met die regel naar een verkeerde of niet-bestaande resource op zoek gegaan en mislukt deze.

**Hoe los ik het probleem op?**

Werk de betreffende regel bij of maak deze opnieuw om naar de nieuwe resource te wijzen. Het proces voor het bijwerken van het bereik vindt u verder in dit artikel.

Het probleem is van toepassing op deze regeltypen:

- Waarschuwingsregels voor activiteitenlogboek
- Actieregels
- Waarschuwingen voor metrische gegevens: zie de volgende sectie Waarschuwingsregels op basis van metrische [gegevens voor meer informatie.](#alert-rules-based-on-metrics)

> [!NOTE]
> Waarschuwingsregels voor zoeken in logboeken en waarschuwingsregels voor slimme detector worden niet beïnvloed omdat hun bereik een werkruimte of Application Insights. Geen van deze scopes ondersteunt momenteel regiobewegingen.

## <a name="alert-rules-based-on-metrics"></a>Waarschuwingsregels op basis van metrische gegevens

De metrische gegevens die Door Azure-resources worden gebruikt, zijn regionaal. Wanneer een resource wordt verplaatst naar een nieuwe regio, begint deze de metrische gegevens in die nieuwe regio te gebruiken. Als gevolg hiervan moeten waarschuwingsregels op basis van metrische gegevens worden bijgewerkt of opnieuw worden gemaakt, zodat ze naar de huidige metrische stroom in de juiste regio wijzen.

Deze uitleg is van toepassing op zowel [metrische waarschuwingsregels als](alerts-metric-overview.md) [waarschuwingsregels voor beschikbaarheidstests.](../app/monitor-web-app-availability.md)

Als **alle** resources in het bereik zijn verplaatst, hoeft u de regel niet opnieuw te maken. U kunt gewoon elk veld van de waarschuwingsregel, zoals de beschrijving van de waarschuwingsregel, bijwerken en opslaan.
Als **slechts enkele** resources in het bereik zijn verplaatst, moet u de verplaatste resources uit de bestaande regel verwijderen en een nieuwe regel maken die alleen betrekking heeft op de verplaatste resources.

## <a name="procedures-to-fix-problems"></a>Procedures voor het oplossen van problemen

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Regels identificeren die zijn gekoppeld aan een verplaatste resource uit de Azure Portal

- **Voor waarschuwingsregels:** navigeer naar Waarschuwingen > waarschuwingsregels beheren > filteren op het abonnement dat het bevat en de verplaatste resource.
> [!NOTE]
> Waarschuwingsregels voor activiteitenlogboek bieden geen ondersteuning voor dit proces. Het is niet mogelijk om het bereik van een waarschuwingsregel voor activiteitenlogboek bij te werken en deze te laten wijzen naar een resource in een ander abonnement. In plaats daarvan kunt u een nieuwe regel maken die de oude vervangt.

- **Voor actieregels:** navigeer naar Waarschuwingen > Acties beheren > Actieregels (preview) > filteren op het abonnement en de verplaatste resource.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Bereik van een regel wijzigen vanuit de Azure Portal

1. Open de regel die u in de vorige stap hebt geïdentificeerd door erop te klikken.
2. Klik **onder Resource** op Bewerken **en** pas het bereik zo nodig aan.
3. Pas zo nodig andere eigenschappen van de regel aan.
4. Klik op **Opslaan**.

![Bereik van waarschuwingsregel wijzigen](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Het bereik van een regel wijzigen met behulp van Azure Resource Manager sjablonen

1. Verkrijg de Azure Resource Manager van de regel.   De sjabloon van een regel exporteren vanuit de Azure Portal:
   1. Navigeer naar de sectie Resourcegroepen in de portal en open de resourcegroep die de regel bevat.
   2. In de sectie Overzicht selectievakje het selectievakje **Verborgen type tonen** in en filtert u op het relevante type van de regel.
   3. Selecteer de relevante regel om de details ervan weer te geven.
   4. Selecteer **onder Instellingen** de optie Sjabloon **exporteren.**
2. Wijzig de sjabloon. Splits indien nodig in twee regels (relevant voor sommige gevallen van waarschuwingen voor metrische gegevens, zoals hierboven is vermeld).
3. De sjabloon opnieuw toepassen.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Bereik van een regel wijzigen met behulp van REST API

1. De bestaande regel op te halen ([waarschuwingen voor metrische gegevens,](/rest/api/monitor/metricalerts/get) [waarschuwingen voor activiteitenlogboek](/rest/api/monitor/activitylogalerts/get))
2. Het bereik wijzigen ([waarschuwingen voor activiteitenlogboek](/rest/api/monitor/activitylogalerts/update))
3. De regel opnieuw ( waarschuwingen voor[metrische gegevens,](/rest/api/monitor/metricalerts/createorupdate)waarschuwingen [voor activiteitenlogboek](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Bereik van een regel wijzigen met behulp van PowerShell

1. Haal de bestaande regel op ([metrische waarschuwingen,](/powershell/module/az.monitor/get-azmetricalertrulev2) [waarschuwingen voor activiteitenlogboek,](/powershell/module/az.monitor/get-azactivitylogalert) [actieregels](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Wijzig het bereik. Splits zo nodig in twee regels (relevant voor sommige gevallen van waarschuwingen voor metrische gegevens, zoals hierboven is aangegeven).
3. De regel opnieuw[(metrische](/powershell/module/az.monitor/add-azmetricalertrulev2)waarschuwingen, waarschuwingen voor [activiteitenlogboek,](/powershell/module/az.monitor/enable-azactivitylogalert) [actieregels) opnieuw teploeeren.](/powershell/module/az.alertsmanagement/set-azactionrule)

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Het bereik van een regel wijzigen met behulp van Azure CLI

1.  Haal de bestaande regel op ([waarschuwingen voor metrische gegevens,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show) [waarschuwingen voor activiteitenlogboek).](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)
2.  Het regelbereik rechtstreeks bijwerken ([waarschuwingen voor metrische gegevens,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update)waarschuwingen [voor activiteitenlogboek](/cli/azure/monitor/activity-log/alert/scope))
3.  Splits zo nodig in twee regels (relevant voor sommige gevallen van waarschuwingen voor metrische gegevens, zoals hierboven is aangegeven).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van andere [problemen met waarschuwingsmeldingen,](alerts-troubleshoot.md) [metrische waarschuwingen](alerts-troubleshoot-metric.md)en [logboekwaarschuwingen.](alerts-troubleshoot-log.md)