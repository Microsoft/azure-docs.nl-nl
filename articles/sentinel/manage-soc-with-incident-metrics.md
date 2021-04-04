---
title: Uw SOC beter beheren met metrische gegevens over incidenten in azure Sentinel | Microsoft Docs
description: Gebruik informatie uit het scherm en de werkmap met metrische gegevens van het verklikker incident van Azure om u te helpen uw SOC (Security Operations Center) te beheren.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660726"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Uw SOC beter beheren met metrische gegevens over incidenten

> [!IMPORTANT]
> De functies voor metrische gegevens van incidenten zijn momenteel beschikbaar als open bare preview.
> Deze functies worden zonder service level agreement gegeven en worden niet aanbevolen voor productie werkbelastingen.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als SOC-Manager (Security Operations Center) hebt u de algemene efficiëntie-metrische gegevens en metingen binnen hand bereik nodig om de prestaties van uw team te meten. U wilt incident bewerkingen in de loop van de tijd zien door veel verschillende criteria, zoals Ernst, MITRE tactiek, Mean time to sorteren, Mean time to resolve, en meer. Azure Sentinel maakt deze gegevens nu beschikbaar voor u met de nieuwe **SecurityIncident** -tabel en het schema in log Analytics en de bijbehorende werkmap voor de efficiency van de **beveiligings bewerkingen** . U kunt de prestaties van uw team in de loop van de tijd visualiseren en dit inzicht gebruiken om de efficiëntie te verbeteren. U kunt ook uw eigen KQL-query's schrijven en gebruiken voor de incident tabel om aangepaste werkmappen te maken die voldoen aan uw specifieke controle behoeften en Kpi's.

## <a name="use-the-security-incidents-table"></a>De tabel beveiligings incidenten gebruiken

De **SecurityIncident** -tabel is ingebouwd in azure Sentinel. U vindt het met de andere tabellen in de **SecurityInsights** -verzameling onder **Logboeken**. U kunt een query uitvoeren zoals een andere tabel in Log Analytics.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Tabel met beveiligings incidenten":::

Telkens wanneer u een incident maakt of bijwerkt, wordt er een nieuw logboek item toegevoegd aan de tabel. Zo kunt u de wijzigingen bijhouden die in incidenten zijn aangebracht, en nog krachtigere SOC-metrische gegevens maken, maar u moet mindful zijn wanneer u query's voor deze tabel maakt, omdat u mogelijk dubbele vermeldingen voor een incident moet verwijderen (afhankelijk van de exacte query die u uitvoert). 

Als u bijvoorbeeld een lijst wilt weer geven met alle incidenten die zijn gesorteerd op het incident nummer, maar u alleen het meest recente logboek per incident wilt retour neren, kunt u dit doen met behulp van de [samenvatte operator](/azure/data-explorer/kusto/query/summarizeoperator) KQL in combi natie met de `arg_max()` [aggregatie functie](/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>Meer voorbeeld query's

Gemiddelde tijd voor sluiting:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Gemiddelde tijd tot sorteren:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Efficiëntie werkmap voor beveiligings bewerkingen

Om de **SecurityIncidents** -tabel aan te vullen, hebben we u een out-of-the-box-werkmap sjabloon voor **beveiligings bewerkingen** die u kunt gebruiken voor het BEwaken van uw Soc-activiteiten. De werkmap bevat de volgende metrische gegevens: 
- Incident gemaakt in de loop van de tijd 
- Incidenten die zijn gemaakt door de classificatie, Ernst, eigenaar en status te sluiten 
- Gemiddelde tijd tot sorteren 
- Gemiddelde tijd tot sluiten 
- Incidenten die zijn gemaakt met Ernst, eigenaar, status, product en tactieken in de loop van de tijd 
- Tijd tot sorteren percentielen 
- Tijd tot sluiten percentielen 
- Gemiddelde tijd tot sorteren per eigenaar 
- Recente activiteiten 
- Recente afsluit classificaties  

U kunt deze nieuwe werkmap sjabloon vinden door **werkmappen** te kiezen in het menu van de Azure-Sentinel navigatie en het tabblad **sjablonen** te selecteren. Kies de efficiëntie van de **beveiligings bewerkingen** in de galerie en klik op een van de knoppen **opgeslagen werkmap weer geven** en **sjabloon weer geven** .

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Galerie met beveiligings incidenten werkmappen":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Werk blad met beveiligings incidenten voltooid":::

U kunt de sjabloon gebruiken om uw eigen aangepaste werkmappen te maken die zijn afgestemd op uw specifieke behoeften.

## <a name="securityincidents-schema"></a>SecurityIncidents-schema

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Volgende stappen

- U moet een Microsoft Azure-abonnement hebben om met Azure Sentinel aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Leer hoe u [uw gegevens kunt onboarden in Azure Sentinel](quickstart-onboard.md) en [krijg inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).