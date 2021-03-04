---
title: Waarschuwings regels of actie regels bijwerken wanneer de doel resource wordt verplaatst naar een andere Azure-regio
description: Achtergrond en instructies voor het bijwerken van waarschuwings regels of actie regels wanneer de doel resource wordt verplaatst naar een andere Azure-regio.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: eb6dbb74fe0d345a157049e79f7a3642499d7cfa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037979"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Waarschuwings regels of actie regels bijwerken wanneer de doel resource wordt verplaatst naar een andere Azure-regio

In dit artikel wordt beschreven waarom de bestaande [waarschuwings regels](./alerts-overview.md) en [actie regels](./alerts-action-rules.md) kunnen worden beïnvloed wanneer u andere Azure-resources tussen regio's verplaatst en hoe u deze problemen kunt identificeren en oplossen. Raadpleeg de documentatie over het verplaatsen van de hoofd [resource](../../azure-resource-manager/management/move-region.md) voor meer informatie over de resource verplaatsing tussen regio's nuttig en een controle lijst voor het ontwerpen van een Verplaats proces.

## <a name="why-the-problem-exists"></a>Waarom het probleem zich voordoet

Waarschuwings regels en actie regels verwijzen naar andere Azure-resources. Voor beelden hiervan zijn [Azure-vm's](../../site-recovery/azure-to-azure-tutorial-migrate.md), [azure SQL](../../azure-sql/database/move-resources-across-regions.md)en [Azure Storage](../../storage/common/storage-account-move.md). Wanneer u de resources verplaatst waarnaar wordt verwezen door de regels, werken de regels waarschijnlijk niet goed, omdat ze de bronnen waarnaar ze verwijzen, niet kunnen vinden.

Er zijn twee belang rijke redenen waarom uw regels na het verplaatsen van de doel resources mogelijk niet meer werken:

- Het bereik van uw regel verwijst expliciet naar de oude resource.
- Uw waarschuwings regel is gebaseerd op metrische gegevens.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Regel bereik verwijst expliciet naar de oude resource

Wanneer u een resource verplaatst, verandert de resource-ID in de meeste gevallen. Achter de schermen repliceert het systeem de bron naar de nieuwe regio voordat deze uit de oude regio wordt verwijderd. Dit proces vereist dat twee resources en dus twee verschillende resource-Id's tegelijkertijd voor een kleine periode bestaan. Aangezien resource-Id's uniek moeten zijn, moet er een nieuwe ID tijdens het proces worden gemaakt. 

**Wat is de invloed van het verplaatsen van de resource op bestaande regels?**

Waarschuwings regels en actie regels hebben een bereik van de resources waarop ze van toepassing zijn. Het bereik kan een volledig abonnement, een resource groep of een of meer specifieke resources zijn.
Dit is bijvoorbeeld een regel met een bereik met twee resources (twee virtuele machines):

![Waarschuwings regel voor meerdere resources](media/alerts-resource-move/multi-resource-alert-rule.png)

Als het regel bereik expliciet een resource vermeldt en de resource-ID van die resource is verplaatst en gewijzigd, zal die regel zoeken naar een onjuiste of niet-bestaande resource en dus mislukken.

**Hoe kunt u het probleem oplossen?**

Werk de betreffende regel bij of maak deze opnieuw om naar de nieuwe resource te verwijzen. Het proces voor het bijwerken van het bereik vindt u verderop in dit artikel.

Het probleem is van toepassing op deze regel typen:

- Waarschuwingsregels voor activiteitenlogboek
- Actieregels
- Metrische waarschuwingen: Zie de volgende sectie [waarschuwings regels op basis van metrische](#alert-rules-based-on-metrics)gegevens voor meer informatie.

> [!NOTE]
> Waarschuwings regels voor logboeken en Smart detector-waarschuwings regels worden niet beïnvloed omdat hun bereik een werk ruimte of Application Insights is. Geen van deze bereiken ondersteunt regio verplaatsingen.

## <a name="alert-rules-based-on-metrics"></a>Waarschuwings regels op basis van metrische gegevens

De metrische gegevens die Azure-resources verzenden zijn regionaal. Wanneer een resource wordt verplaatst naar een nieuwe regio, worden de metrische gegevens in die nieuwe regio gestart. Als gevolg hiervan moeten waarschuwings regels op basis van metrische gegevens worden bijgewerkt of opnieuw worden gemaakt, zodat ze naar de huidige metrische stroom in de juiste regio verwijzen.

Deze uitleg is van toepassing op zowel [metrische waarschuwings regels](alerts-metric-overview.md) als [beschikbaarheids test waarschuwings regels](../app/monitor-web-app-availability.md).

Als **alle** resources in het bereik zijn verplaatst, hoeft u de regel niet opnieuw te maken. U kunt alleen een veld van de waarschuwings regel, zoals de beschrijving van de waarschuwings regel, bijwerken en opslaan.
Als **slechts een deel** van de resources in het bereik is verplaatst, moet u de verplaatste resources uit de bestaande regel verwijderen en een nieuwe regel maken die alleen betrekking heeft op de verplaatste resources.

## <a name="procedures-to-fix-problems"></a>Procedures voor het oplossen van problemen

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Het identificeren van regels die zijn gekoppeld aan een verplaatste resource vanuit het Azure Portal

- **Voor waarschuwings regels** : Navigeer naar waarschuwingen > het beheren van waarschuwings regels > filteren op het betreffende abonnement en de verplaatste resource.
> [!NOTE]
> Waarschuwings regels van het activiteiten logboek bieden geen ondersteuning voor dit proces. Het is niet mogelijk om het bereik van een waarschuwings regel voor het activiteiten logboek bij te werken en deze te laten verwijzen naar een resource in een ander abonnement. In plaats daarvan kunt u een nieuwe regel maken waarmee de oude wordt vervangen.

- **Voor actie regels** : Navigeer naar waarschuwingen > acties beheren > actie regels (preview) > filteren op het insluitende abonnement en de verplaatste resource.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Het bereik van een regel wijzigen van de Azure Portal

1. Open de regel die u in de vorige stap hebt geïdentificeerd door erop te klikken.
2. Klik onder **resource** op **bewerken** en pas het bereik zo nodig aan.
3. Pas zo nodig andere eigenschappen van de regel aan.
4. Klik op **Opslaan**.

![Bereik van waarschuwings regel wijzigen](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Het bereik van een regel wijzigen met behulp van Azure Resource Manager sjablonen

1. Verkrijg de Azure Resource Manager sjabloon van de regel.  De sjabloon van een regel exporteren vanuit de Azure Portal:
   1. Ga naar de sectie resource groepen in de portal en open de resource groep met de regel.
   2. Schakel in de sectie Overzicht het selectie vakje **verborgen type weer geven** in en filter op het betreffende type regel.
   3. Selecteer de relevante regel om de details ervan weer te geven.
   4. Selecteer onder **instellingen** de optie **sjabloon exporteren**.
2. Wijzig de sjabloon. Als dat nodig is, kunt u splitsen in twee regels (die relevant zijn voor bepaalde gevallen van metrische waarschuwingen, zoals hierboven vermeld).
3. Implementeer de sjabloon opnieuw.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Het bereik van een regel wijzigen met behulp van REST API

1. De bestaande regel ophalen ([metrische waarschuwingen](/rest/api/monitor/metricalerts/get), [waarschuwingen voor activiteiten logboeken](/rest/api/monitor/activitylogalerts/get))
2. Het bereik wijzigen ([waarschuwingen voor activiteiten logboeken](/rest/api/monitor/activitylogalerts/update))
3. De regel opnieuw implementeren ([metrische waarschuwingen](/rest/api/monitor/metricalerts/createorupdate), [waarschuwingen voor activiteiten logboeken](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Het bereik van een regel wijzigen met behulp van Power shell

1. De bestaande regel ophalen ([metrische waarschuwingen](/powershell/module/az.monitor/get-azmetricalertrulev2), [waarschuwingen voor activiteiten logboeken](/powershell/module/az.monitor/get-azactivitylogalert), [actie regels](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Wijzig het bereik. Als dat nodig is, kunt u splitsen in twee regels (die relevant zijn voor bepaalde gevallen van metrische waarschuwingen, zoals hierboven vermeld).
3. Implementeer de regel opnieuw ([metrische waarschuwingen](/powershell/module/az.monitor/add-azmetricalertrulev2), [waarschuwingen voor activiteiten logboeken](/powershell/module/az.monitor/enable-azactivitylogalert), [actie regels](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Het bereik van een regel wijzigen met behulp van Azure CLI

1.  De bestaande regel ophalen ([metrische waarschuwingen](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show), [waarschuwingen voor activiteiten logboeken](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  Het regel bereik direct bijwerken ([metrische waarschuwingen](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), [waarschuwingen voor activiteiten logboeken](/cli/azure/monitor/activity-log/alert/scope))
3.  Als dat nodig is, kunt u splitsen in twee regels (die relevant zijn voor bepaalde gevallen van metrische waarschuwingen, zoals hierboven vermeld).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van andere problemen met [waarschuwings meldingen](alerts-troubleshoot.md), [metrische waarschuwingen](alerts-troubleshoot-metric.md)en [logboek waarschuwingen](alerts-troubleshoot-log.md).