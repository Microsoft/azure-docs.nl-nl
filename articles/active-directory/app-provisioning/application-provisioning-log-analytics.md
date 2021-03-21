---
title: Begrijpen hoe het inrichten met Azure Monitor-Logboeken in Azure Active Directory kan worden geïntegreerd.
description: Begrijpen hoe het inrichten met Azure Monitor-Logboeken in Azure Active Directory kan worden geïntegreerd.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561565"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Meer informatie over het inrichten van inrichtingen met Azure Monitor-logboeken

Inrichtings integratie met Azure Monitor logboeken en Log Analytics. Met Azure monitoring kunt u bijvoorbeeld werkmappen maken, ook wel Dash boards genoemd, inrichtings logboeken voor 30 dagen opslaan en aangepaste query's en waarschuwingen maken. In dit artikel wordt beschreven hoe u de inrichtings logboeken integreert met Azure Monitor-Logboeken. Zie [Provisioning-logboeken](../reports-monitoring/concept-provisioning-logs.md)voor meer informatie over hoe het inrichten van Logboeken in het algemeen werkt.

## <a name="enabling-provisioning-logs"></a>Inrichtings logboeken inschakelen

U moet al bekend zijn met Azure monitoring en Log Analytics. Als dat niet het geval is, gaat u naar meer informatie en gaat u terug naar meer informatie over het inrichtings logboek van de toepassing. Zie [Azure monitor Overview](../../azure-monitor/overview.md)voor meer informatie over Azure-bewaking. Zie [overzicht van logboek query's in azure monitor](../../azure-monitor/logs/log-query-overview.md)voor meer informatie over Azure monitor logboeken en log Analytics.

Zodra u Azure monitoring hebt geconfigureerd, kunt u Logboeken inschakelen voor het inrichten van toepassingen. De optie bevindt zich op de pagina **Diagnostische instellingen** .

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Diagnostische instellingen openen" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Inrichtings logboeken voor toepassingen inschakelen" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Als u net onlangs een werk ruimte hebt ingericht, kan het enige tijd duren voordat u er logboeken naar kunt verzenden. Als er een fout bericht wordt weer gegeven dat het abonnement niet is geregistreerd voor gebruik van *micro soft. Insights* , kunt u na enkele minuten teruggaan.
 
## <a name="understanding-the-data"></a>Informatie over de gegevens
De onderliggende gegevens stroom die het inrichten van logboek viewers verzendt, is bijna identiek. Azure Monitor logboeken krijgt bijna dezelfde stroom als de Azure Portal-gebruikers interface en de Azure-API. Er zijn slechts enkele **verschillen** in de logboek velden, zoals beschreven in de volgende tabel. Zie [List provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta)voor meer informatie over deze velden.

|Azure Monitor-logboeken   |Azure Portal gebruikers interface   |Azure-API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor-werkmappen

Azure Monitor werkmappen bieden een flexibel canvas voor gegevens analyse. Ze bieden ook voor het maken van uitgebreide visuele rapporten in de Azure Portal. Zie [Azure monitor Workbooks Overview](../../azure-monitor/visualize/workbooks-overview.md)(Engelstalig) voor meer informatie.

Het inrichten van toepassingen wordt geleverd met een reeks vooraf gemaakte werkmappen. U kunt ze vinden op de pagina werkmappen. Als u de gegevens wilt bekijken, moet u ervoor zorgen dat alle filters (time Range, jobID, appName) worden ingevuld. U moet er ook voor zorgen dat u een app hebt ingericht, anders zijn er geen gegevens in de logboeken.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Werkmappen voor het inrichten van toepassingen" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Inrichtings dashboard voor toepassingen" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Aangepaste query's

U kunt aangepaste query's maken en de gegevens in azure-Dash boards weer geven. Zie [Dash boards van log Analytics gegevens maken en delen](../../azure-monitor/logs/get-started-queries.md)voor meer informatie. Zorg er ook voor dat u [overzicht van logboek query's in azure monitor](../../azure-monitor/logs/log-query-overview.md)bekijkt.

Hier volgen enkele voor beelden om aan de slag te gaan met het inrichten van toepassingen.

Query's uitvoeren op de logboeken voor een gebruiker a op basis van hun ID in het bron systeem:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Aantal overzichten per fout code:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Aantal gebeurtenissen per dag samenvatten per actie:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Voer 100 gebeurtenissen en project sleutel eigenschappen in:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Aangepaste waarschuwingen

Met Azure Monitor kunt u aangepaste waarschuwingen configureren, zodat u een melding krijgt over belang rijke gebeurtenissen met betrekking tot het inrichten. Het is bijvoorbeeld mogelijk dat u een waarschuwing wilt ontvangen over pieken in fouten. Of het is mogelijk dat er pieken optreden in uitgeschakeld of verwijderd. Een ander voor beeld van waar u mogelijk wilt worden gewaarschuwd, is het ontbreken van een inrichting, wat aangeeft dat er iets mis is.

Zie [reageren op gebeurtenissen met Azure monitor-waarschuwingen](../../azure-monitor/alerts/tutorial-response.md)voor meer informatie over waarschuwingen.

Waarschuwen wanneer er sprake is van een piek in storingen. Vervang de jobID door de jobID voor uw toepassing.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Waarschuwen wanneer er sprake is van een piek in storingen." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Er is mogelijk een probleem waardoor de inrichtings service niet meer actief is. Gebruik de volgende waarschuwing om te detecteren wanneer er tijdens een bepaald tijds interval geen inrichtings gebeurtenissen zijn.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Er is mogelijk een probleem waardoor de inrichtings service niet meer actief is." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Waarschuwen wanneer er een piek is in uitgeschakeld of verwijderd.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Waarschuwen wanneer er een piek is in uitgeschakeld of verwijderd." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Bijdragen van de community

We nemen een open-source-en community-benadering voor het inrichten van query's en dash boards voor toepassingen. Als u een query, waarschuwing of werkmap hebt gemaakt die anderen nuttig vinden, moet u deze publiceren naar de [AzureMonitorCommunity github opslag plaats](https://github.com/microsoft/AzureMonitorCommunity). Geef ons vervolgens een e-mail met een koppeling. We bekijken en publiceren deze naar de service, zodat anderen er ook kunnen profiteren. U kunt contact met ons opnemen via provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Volgende stappen

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Aan de slag met query's in Azure Monitor-logboeken](../../azure-monitor/logs/get-started-queries.md)
- [Waarschuwings groepen maken en beheren in de Azure Portal](../../azure-monitor/alerts/action-groups.md)
- [De log Analytics-weer gaven voor Azure Active Directory installeren en gebruiken](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [API voor het inrichten van Logboeken](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)