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
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2465cf74dbf4fbf074aed8f8e4097a236e628425
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660641"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nuttige bronnen voor het werken met Azure Sentinel



Dit artikel bevat een lijst met bronnen die u kunnen helpen om meer informatie te krijgen over het werken met Azure Sentinel.

Azure Logic Apps-connectors: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Controle en rapportage
Audit logboeken van Azure Sentinel worden onderhouden in [Azure-activiteiten logboeken](../azure-monitor/platform/platform-logs-overview.md).

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



## <a name="vendor-documentation"></a>Documentatie van de leverancier

| **Leverancier**  | **Incident gebruiken in azure-Sentinel** | **Koppeling**|
|----|----|----|
| GitHub| Wordt gebruikt voor toegang tot de community-pagina| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF configureren| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Cursus over Kusto-querytaal| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs en forums

Plaats uw vragen op de [TechCommunity ruimte](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) voor Azure Sentinel.

Bekijk Azure Sentinel-blog berichten van de [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) en [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u een lijst met resources die handig zijn wanneer u met Azure Sentinel werkt. U vindt aanvullende informatie over de beveiliging en naleving van Azure in de [Microsoft Azure beveiligings-en nalevings blog](/archive/blogs/azuresecurity/).