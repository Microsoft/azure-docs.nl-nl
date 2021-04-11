---
title: Waarschuwingen maken met SQL Insights (preview-versie)
description: Waarschuwingen maken met SQL Insights in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609421"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Waarschuwingen maken met SQL Insights (preview-versie)
SQL Insights bevat een reeks sjablonen voor waarschuwings regels die u kunt gebruiken om [waarschuwings regels te maken in azure monitor](../alert/../alerts/alerts-overview.md) voor algemene SQL-problemen. De waarschuwings regels in SQL Insights zijn logboek waarschuwings regels op basis van prestatie gegevens die zijn opgeslagen in de *InsightsMetrics* -tabel in azure monitor logs.  

> [!NOTE]
> Als u aanvragen voor meer SQL Insights-waarschuwings regel sjablonen hebt, verzendt u feedback via de koppeling onder aan deze pagina of gebruikt u de koppeling voor de SQL Insights-feedback in de Azure Portal.

## <a name="enable-alert-rules"></a>Waarschuwings regels inschakelen 
Gebruik de volgende stappen om de waarschuwingen in Azure Monitor van de Azure Portal in te scha kelen.De waarschuwings regels die worden gemaakt, zijn van toepassing op alle SQL-resources die onder het geselecteerde bewakings profiel worden bewaakt.  Wanneer een waarschuwings regel wordt geactiveerd, wordt deze geactiveerd op het specifieke SQL-exemplaar of in de opgegeven Data Base.

> [!NOTE]
> U kunt ook aangepaste [waarschuwings regels voor logboeken](../alerts/alerts-log.md) maken door query's uit te voeren op de gegevens sets in de *InsightsMetrics* -tabel en deze query's vervolgens als waarschuwings regel op te slaan. 

Selecteer **SQL (preview)** in het gedeelte **inzichten** van het menu Azure monitor van de Azure Portal. Klik op **waarschuwingen**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Knop waarschuwingen":::

Het deel venster **waarschuwingen** wordt weer gegeven aan de rechter kant van de pagina. Standaard worden geactiveerde waarschuwingen voor SQL-resources in het geselecteerde bewakings profiel weer gegeven op basis van de waarschuwings regels die u al hebt gemaakt. Selecteer **waarschuwings sjablonen**, waarin de lijst met beschik bare sjablonen wordt weer gegeven die u kunt gebruiken om een waarschuwings regel te maken.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Waarschuwings sjablonen":::

Controleer op de pagina **waarschuwings regel maken** de standaard instellingen voor de regel en bewerk deze indien nodig. U kunt ook een [actie groep](../alerts/action-groups.md) selecteren om meldingen en acties te maken wanneer de waarschuwings regel wordt geactiveerd. Klik op **waarschuwings regel inschakelen** om de waarschuwings regel te maken zodra u alle bijbehorende eigenschappen hebt gecontroleerd.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Pagina waarschuwings regels":::

Klik op **waarschuwings regel implementeren** als u de waarschuwings regel onmiddellijk wilt implementeren. Klik op **sjabloon weer geven** als u de regel sjabloon wilt weer geven voordat u deze daad werkelijk implementeert.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Waarschuwings regel implementeren":::

Als u ervoor kiest om de sjablonen weer te geven, selecteert u **implementeren** op de pagina sjabloon om de waarschuwings regel te maken.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Implementeren vanuit weergave sjabloon":::


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [waarschuwingen vindt u in azure monitor](../alerts/alerts-overview.md).

