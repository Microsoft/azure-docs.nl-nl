---
title: Log Analytics en Excel integreren
description: Een Log Analytics query in Excel ophalen en resultaten in Excel vernieuwen.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 6fa181a35c46ed16e4e8c1884e66c54984c418ca
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703446"
---
# <a name="integrate-log-analytics-and-excel"></a>Log Analytics en Excel integreren

U kunt Azure Monitor Log Analytics en micro soft Excel integreren met behulp van de M-query en de Log Analytics-API. Met deze integratie kunt u Maxi maal 500.000 records naar Excel verzenden, zolang het totale volume van de resultaten niet groter is dan 61MiB.

> [!NOTE]
> Omdat Excel een lokale client toepassing is, zijn de beperkingen van lokale hardware en software van invloed op de prestaties en de mogelijkheid om grote gegevens sets te verwerken.

## <a name="create-your-m-query-in-log-analytics"></a>Uw M-query maken in Log Analytics 

1. **Maak uw query en voer deze uit** in log Analytics zoals u dat gewend bent. U hoeft zich geen zorgen te maken als de limiet van 10.000 records in de gebruikers interface wordt bereikt.  We raden u aan relatieve datums te gebruiken, zoals de functie ' geleden ' of de tijd kiezer van de gebruikers interface, zodat Excel de juiste gegevensset vernieuwt.
  
2. **Query exporteren** : wanneer u tevreden bent met de query en de resultaten ervan, exporteert u de query naar M met log Analytics **exporteren naar Power bi (M query)** menu optie onder het menu *exporteren* :

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics query met de optie voor gegevens en exporteren" border="true":::



Als u deze optie kiest, wordt een. txt-bestand gedownload met de M-code die u in Excel kunt gebruiken.

De query die hierboven wordt weer gegeven, exporteert de volgende M-code. Hier volgt een voor beeld van de M-code die is geëxporteerd voor de query in ons voor beeld:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Query verbinden met Excel 

Voor het importeren van de query. 

1. Open Microsoft Excel. 
1. Ga in het lint naar het menu **Data** . Selecteer **gegevens ophalen**. Selecteer in **andere bronnen** **lege query**:
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Optie importeren uit leeg in Excel" border="true":::

1. Selecteer in het venster Power query de optie **Geavanceerde editor**:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Geavanceerde query-editor voor Excel" border="true":::

 
1. Vervang de tekst in de geavanceerde editor door de query die is geëxporteerd uit Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Een lege query maken" border="true":::
 
1. Selecteer **gereed** en vervolgens **laden en sluiten**. Excel voert de query uit met behulp van de log Analytics-API en de resultatenset vervolgens weer gegeven.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Query resultaten in Excel" border="true":::

> [!Note]
> Als het aantal records kleiner is dan verwacht, overschrijdt het volume van de resultaten mogelijk de limiet van 61MiB. Probeer `project` of `project-away` in uw query te gebruiken om de kolommen te beperken tot de kolom die u nodig hebt.

##  <a name="refreshing--data"></a>Gegevens vernieuwen

U kunt uw gegevens rechtstreeks vanuit Excel vernieuwen. Selecteer de knop **vernieuwen** in de menu groep **Data** in het Excel-lint.
 
## <a name="next-steps"></a>Volgende stappen

Zie [gegevens importeren uit externe gegevens bronnen (Power query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) voor meer informatie over de integratie van Excel met externe gegevens bronnen.