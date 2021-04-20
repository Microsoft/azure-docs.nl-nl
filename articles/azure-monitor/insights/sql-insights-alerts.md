---
title: Waarschuwingen maken met SQL-inzichten (preview)
description: Waarschuwingen maken met SQL-inzichten in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726858"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Waarschuwingen maken met SQL-inzichten (preview)
SQL-inzichten bevat een set waarschuwingsregelsjablonen die u kunt gebruiken om waarschuwingsregels te maken [in](../alert/../alerts/alerts-overview.md) Azure Monitor voor veelvoorkomende SQL-problemen. De waarschuwingsregels in SQL Insights zijn waarschuwingsregels voor logboeken op basis van prestatiegegevens die zijn opgeslagen in de *tabel InsightsMetrics* in Azure Monitor Logboeken.  

> [!NOTE]
> Als u een waarschuwing wilt maken voor SQL-inzichten met behulp van een Resource Manager-sjabloon, Resource Manager [voorbeelden van SQL-sjablonen voor SQL-inzichten.](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)


> [!NOTE]
> Als u aanvragen hebt voor meer sjablonen voor SQL Insights-waarschuwingsregel, kunt u feedback verzenden via de koppeling onder aan deze pagina of met behulp van de koppeling SQL Insights-feedback in de Azure Portal.

## <a name="enable-alert-rules"></a>Waarschuwingsregels inschakelen 
Gebruik de volgende stappen om de waarschuwingen in de Azure Monitor in te Azure Portal.De waarschuwingsregels die worden gemaakt, vallen binnen het bereik van alle SQL-resources die worden bewaakt onder het geselecteerde bewakingsprofiel.  Wanneer een waarschuwingsregel wordt geactiveerd, wordt deze geactiveerd op het specifieke SQL-exemplaar of de specifieke database.

> [!NOTE]
> U kunt ook aangepaste regels voor [logboekwaarschuwingen](../alerts/alerts-log.md) maken door query's uit te stellen op de gegevenssets in de *tabel InsightsMetrics* en deze query's vervolgens op te slaan als waarschuwingsregel. 

Selecteer **SQL (preview)** in **de sectie Inzichten** van het Azure Monitor menu in de Azure Portal. Klik op **Waarschuwingen**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="De knop Waarschuwingen":::

Het **deelvenster** Waarschuwingen wordt aan de rechterkant van de pagina geopend. Standaard worden geactiveerde waarschuwingen voor SQL-resources weergegeven in het geselecteerde bewakingsprofiel op basis van de waarschuwingsregels die u al hebt gemaakt. Selecteer **Waarschuwingssjablonen.** Hiermee wordt de lijst met beschikbare sjablonen weergegeven die u kunt gebruiken om een waarschuwingsregel te maken.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Waarschuwingssjablonen":::

Controleer op **de pagina Waarschuwingsregel** maken de standaardinstellingen voor de regel en bewerk deze zo nodig. U kunt ook een [actiegroep selecteren om](../alerts/action-groups.md) meldingen en acties te maken wanneer de waarschuwingsregel wordt geactiveerd. Klik **op Waarschuwingsregel inschakelen** om de waarschuwingsregel te maken nadat u alle eigenschappen ervan hebt geverifieerd.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Pagina Waarschuwingsregels":::

Als u de waarschuwingsregel onmiddellijk wilt implementeren, klikt u **op Waarschuwingsregel implementeren.** Klik **op Sjabloon weergeven** als u de regelsjabloon wilt weergeven voordat u deze daadwerkelijk implementeert.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Waarschuwingsregel implementeren":::

Als u ervoor kiest om de sjablonen weer te geven, **selecteert u Implementeren** op de sjabloonpagina om de waarschuwingsregel te maken.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Implementeren vanuit weergavesjabloon":::


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [waarschuwingen in Azure Monitor](../alerts/alerts-overview.md).

