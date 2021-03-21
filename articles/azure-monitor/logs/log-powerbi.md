---
title: Integratie met Power BI en Excel Log Analytics
description: Resultaten van Log Analytics naar Power BI verzenden
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 894b87bfa17a59db20b424199a4021ad75b182fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041056"
---
# <a name="log-analytics-integration-with-power-bi"></a>Integratie met Power BI Log Analytics

Dit artikel richt zich op manieren om gegevens van Log Analytics te voeren in micro soft Power BI om visueel aantrekkelijke rapporten en dash boards te maken. 

## <a name="background"></a>Achtergrond 

Azure Monitor-Logboeken is een platform dat een end-to-end oplossing biedt voor opname van Logboeken. [Azure Monitor Log Analytics](../platform/data-platform.md#) is de interface voor het opvragen van deze logboeken. Zie [Azure monitor data platform](../data-platform.md)(Engelstalig) voor meer informatie over het hele Azure monitor gegevens platform, inclusief log Analytics. 

Micro soft Power BI is het gegevens visualisatie platform van micro soft. Zie [de Home page van Power bi](https://powerbi.microsoft.com/)voor meer informatie over hoe u aan de slag kunt gaan. 


Over het algemeen kunt u de functies van gratis Power BI gebruiken om rapporten en dash boards te integreren en visueel aantrekkelijker te maken.

Voor meer geavanceerde functies moet u mogelijk een Power BI Pro of een Premium-account aanschaffen. Deze functies zijn onder andere: 
 - uw werk delen 
 - geplande vernieuwingen
 - Power BI-apps 
 - gegevens stromen en incrementeel vernieuwen 

Meer informatie vindt u in de [informatie over Power bi prijzen en functies](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Query's integreren  

Power BI gebruikt de [M-query taal](/powerquery-m/power-query-m-language-specification/) als de belangrijkste query taal. 

Log Analytics query's kunnen worden geëxporteerd naar M en worden rechtstreeks in Power BI gebruikt. Nadat u een geslaagde query hebt uitgevoerd, selecteert u de optie **exporteren naar Power bi (M query)** via de knop **exporteren** in log Analytics gebruikers interface bovenste actie balk.


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="Keuze lijst met opties voor Log Analytics query voor exporteren" border="true":::

Log Analytics maakt een txt-bestand met de M-code die rechtstreeks in Power BI kan worden gebruikt.

## <a name="connecting-your-logs-to-a-dataset"></a>Uw logboeken verbinden met een gegevensset 

Een Power BI-gegevensset is een bron van gegevens die gereed is voor rapportage en visualisatie. Als u een Log Analytics query wilt verbinden met een gegevensset, kopieert u de M-code die is geëxporteerd van Log Analytics naar een lege query in Power BI. 

Zie [informatie over Power BI gegevens sets](/power-bi/service-datasets-understand/)voor meer informatie. 

## <a name="collect-data-with-power-bi-dataflows"></a>Gegevens verzamelen met Power BI gegevens stromen 

Power BI gegevens stromen u ook in staat om gegevens te verzamelen en op te slaan. Zie [Power BI gegevens stromen](/power-bi/service-dataflows-overview)voor meer informatie.

Een gegevensstroom is een type ' Cloud ETL ' dat is ontworpen om u te helpen bij het verzamelen en voorbereiden van uw gegevens. Een gegevensset is het model dat is ontworpen om u te helpen bij het verbinden van verschillende entiteiten en om deze te model leren voor uw behoeften.

## <a name="incremental-refresh"></a>Incrementeel vernieuwen 

Zowel Power BI gegevens sets als Power BI gegevens stromen hebben een incrementele vernieuwings optie. Power BI gegevens stromen en Power BI gegevens sets ondersteunen deze functie, maar u moet Power BI Premium om het te gebruiken.  


Met incrementeel vernieuwen worden kleine query's uitgevoerd en worden kleinere hoeveel heden gegevens per uitvoering bijgewerkt, in plaats van dat alle gegevens opnieuw moeten worden opgenomen en wanneer u de query uitvoert. U hebt de mogelijkheid om grote hoeveel heden gegevens op te slaan, maar telkens wanneer de query wordt uitgevoerd, een nieuwe toename van gegevens toe te voegen. Dit gedrag is ideaal voor het uitvoeren van meer rapporten.

Power BI incrementeel vernieuwen is afhankelijk van het bestaan van een *datum/tijd* die is opgeslagen in de resultatenset. Voordat u incrementeel vernieuwen configureert, moet u ervoor zorgen dat de resultatenset van de Log Analytics query ten minste één *datum/tijd* heeft ingediend. 

Zie [Power BI gegevens sets en incrementeel](/power-bi/service-premium-incremental-refresh) vernieuwen en [Power BI gegevens stromen en incrementeel vernieuwen](/power-bi/service-dataflows-incremental-refresh)voor meer informatie en het configureren van incrementeel vernieuwen.

## <a name="reports-and-dashboards"></a>Rapporten en dashboards

Nadat uw gegevens naar Power BI zijn verzonden, kunt u Power BI blijven gebruiken om rapporten en dash boards te maken.

Zie [deze hand leiding voor het maken van uw eerste Power bi model en-rapport](/learn/modules/build-your-first-power-bi-report/)voor meer informatie.  

## <a name="excel-integration"></a>Excel-integratie

U kunt de M-integratie die in Power BI wordt gebruikt, gebruiken om te integreren met een Excel-spread sheet. Zie voor meer informatie deze [hand leiding voor het integreren met Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) en plak de M-query die is geëxporteerd uit log Analytics.

Meer informatie vindt u in [integreren log Analytics en Excel](log-excel.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met [log Analytics query's](./log-query-overview.md).