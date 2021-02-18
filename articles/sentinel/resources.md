---
title: Nuttige bronnen bij het werken met Azure Sentinel | Microsoft Docs
description: In dit document vindt u een lijst met nuttige resources wanneer u werkt met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: c404aa93669cd95dccb0ad185d71d2ec16256d0d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570440"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nuttige bronnen voor het werken met Azure Sentinel



Dit artikel bevat een lijst met bronnen die u kunnen helpen om meer informatie te krijgen over het werken met Azure Sentinel.

- **Azure Logic apps-connectors**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Controle en rapportage
Audit logboeken van Azure Sentinel worden onderhouden in [Azure-activiteiten logboeken](../azure-monitor/essentials/platform-logs-overview.md).

De volgende ondersteunde bewerkingen kunnen worden gecontroleerd.

|Naam van bewerking|    Resourcetype|
|----|----|
|Werkmap maken of bijwerken  |Micro soft. Insights/werkmappen|
|Werkmap verwijderen    |Micro soft. Insights/werkmappen|
|Werk stroom instellen   |Microsoft.Logic/workflows|
|Werk stroom verwijderen    |Microsoft.Logic/workflows|
|Opgeslagen zoek opdracht maken    |Micro soft. OperationalInsights/werk ruimten/savedSearches|
|Opgeslagen zoek opdracht verwijderen    |Micro soft. OperationalInsights/werk ruimten/savedSearches|
|Waarschuwings regels bijwerken |Micro soft. SecurityInsights/alertRules|
|Waarschuwings regels verwijderen |Micro soft. SecurityInsights/alertRules|
|Reactie acties van waarschuwings regel bijwerken |Micro soft. SecurityInsights/alertRules/acties|
|Reactie acties voor waarschuwings regels verwijderen |Micro soft. SecurityInsights/alertRules/acties|
|Blad wijzers bijwerken   |Micro soft. SecurityInsights/blad wijzers|
|Blad wijzers verwijderen   |Micro soft. SecurityInsights/blad wijzers|
|Cases bijwerken   |Micro soft. SecurityInsights/cases|
|Case-onderzoek bijwerken  |Micro soft. SecurityInsights/cases/onderzoeken|
|Case-opmerkingen maken   |Micro soft. SecurityInsights/cases/opmerkingen|
|Gegevens connectors bijwerken |Micro soft. SecurityInsights/dataConnectors|
|Gegevens connectors verwijderen |Micro soft. SecurityInsights/dataConnectors|
|Instellingen bijwerken    |Micro soft. SecurityInsights/Settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Audit-en rapportage gegevens weer geven in azure Sentinel

U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens onderzoek en analyses kunt uitvoeren.

1. Verbind de gegevens bron van de [Azure-activiteit](connect-azure-activity.md) . Nadat u dit hebt gedaan, worden controle gebeurtenissen gestreamd naar een nieuwe tabel in het scherm **Logboeken** met de naam AzureActivity.

1. Vervolgens voert u een query uit op de gegevens met behulp van KQL, zoals u zou doen met een andere tabel.

    Als u bijvoorbeeld wilt weten wie de laatste gebruiker was voor het bewerken van een bepaalde analyse regel, gebruikt u de volgende query (vervangen `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` door de regel-id van de regel die u wilt controleren):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```


## <a name="blogs-and-forums"></a>Blogs en forums

Onze gebruikers horen graag!

- Plaats **uw vragen** op de [TechCommunity ruimte](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) voor Azure Sentinel. 

- **Suggesties voor verbeteringen verzenden** via ons [gebruikers spraak](https://feedback.azure.com/forums/920458-azure-sentinel) programma.

- **Bekijk en geef een opmerking** over onze onderverklikker blog berichten van Azure:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gebruik gecertificeerd.](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Gebruiks Case verhalen van klanten lezen](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

