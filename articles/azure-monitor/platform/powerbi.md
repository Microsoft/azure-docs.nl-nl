---
title: Azure Log Analytics-gegevens importeren in Power BI | Microsoft Docs
description: Power BI is een op de cloud gebaseerde Business Analytics-service van micro soft die uitgebreide visualisaties en rapporten biedt voor analyse van verschillende gegevens sets.  In dit artikel wordt beschreven hoe u Log Analytics gegevens kunt configureren en importeren in Power BI en hoe u deze kunt configureren om automatisch te vernieuwen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 69f00416f180f83c761be5ed444e80903e9fcbb6
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234440"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Azure Monitor logboek gegevens importeren in Power BI


[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) is een op de cloud gebaseerde Business Analytics-service van micro soft die uitgebreide visualisaties en rapporten biedt voor analyse van verschillende gegevens sets.  U kunt de resultaten van een Azure Monitor logboek query importeren in een Power BI-gegevensset, zodat u kunt profiteren van de functies, zoals het combi neren van gegevens uit verschillende bronnen en het delen van rapporten op de web-en mobiele apparaten.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Overzicht
Als u gegevens wilt importeren uit een [log Analytics werk ruimte](manage-access.md) in Azure Monitor naar Power bi, maakt u een gegevensset in Power bi op basis van een [logboek query](../log-query/log-query-overview.md) in azure monitor.  De query wordt uitgevoerd telkens wanneer de gegevensset wordt vernieuwd.  Vervolgens kunt u Power BI rapporten maken die gebruikmaken van gegevens uit de gegevensset.  Als u de gegevensset in Power BI wilt maken, exporteert u uw query van Log Analytics naar de [taal Power query (M)](/powerquery-m/power-query-m-language-specification).  Vervolgens gebruikt u dit om een query in Power BI Desktop te maken en deze vervolgens te publiceren naar Power BI als een gegevensset.  De Details voor dit proces worden hieronder beschreven.

![Log Analytics Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Query exporteren
Maak eerst een [logboek query](../log-query/log-query-overview.md) die de gegevens retourneert die u wilt vullen met de Power bi gegevensset.  Vervolgens exporteert u die query naar de [Power query-taal (M)](/powerquery-m/power-query-m-language-specification) die kan worden gebruikt door Power bi Desktop.

> [!WARNING]
> Zorg ervoor dat u [uw query optimaliseert](../log-query/query-optimization.md) zodat deze niet veel tijd in beslag neemt of kan time-out oplopen. Let op de **time span** -waarde in de geëxporteerde query die de tijds duur definieert van de gegevens die door de query worden opgehaald. Gebruik de kleinste tijds duur die u nodig hebt om de hoeveelheid gegevens te beperken die door de query wordt geretourneerd.

1. [Maak de logboek query in log Analytics](../log-query/log-analytics-tutorial.md) om de gegevens voor uw gegevensset uit te pakken.
2. Selecteer   >  **Power bi query (M)** exporteren.  Hiermee exporteert u de query naar een tekst bestand met de naam **PowerBIQuery.txt**. 

    ![Zoek opdracht in Logboeken exporteren](media/powerbi/export-analytics.png)

3. Open het tekst bestand en kopieer de inhoud ervan.

## <a name="import-query-into-power-bi-desktop"></a>Query importeren in Power BI Desktop
Power BI Desktop is een bureaublad toepassing waarmee u gegevens sets en rapporten kunt maken die naar Power BI kunnen worden gepubliceerd.  U kunt dit ook gebruiken om een query te maken met behulp van de Power Query-taal die is geëxporteerd vanuit Azure Monitor. 

1. Installeer [Power bi Desktop](https://powerbi.microsoft.com/desktop/) als u dit nog niet hebt en open vervolgens de toepassing.
2. Selecteer **gegevens**  >  **lege query** ophalen om een nieuwe query te openen.  Selecteer vervolgens **Geavanceerde editor** en plak de inhoud van het geëxporteerde bestand in de query. Klik op **Gereed**.

    ![Power BI Desktop query](media/powerbi/desktop-new-query.png)

5. De query wordt uitgevoerd en de resultaten worden weer gegeven.  Mogelijk wordt u gevraagd om referenties om verbinding te maken met Azure.  
6. Typ een beschrijvende naam voor de query.  De standaard waarde is **query1**. Klik op **sluiten en Toep assen** om de gegevensset aan het rapport toe te voegen.

    ![Power BI Desktop naam](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publiceren naar Power BI
Wanneer u naar Power BI publiceert, worden er een gegevensset en een rapport gemaakt.  Als u een rapport in Power BI Desktop maakt, wordt dit gepubliceerd met uw gegevens.  Als dat niet het geval is, wordt er een leeg rapport gemaakt.  U kunt het rapport wijzigen in Power BI of een nieuw item maken op basis van de gegevensset.

1. Een rapport maken op basis van uw gegevens.  Gebruik [Power bi desktop documentatie](/power-bi/desktop-report-view) als u er niet vertrouwd mee bent.  
1. Wanneer u klaar bent om deze naar Power BI te verzenden, klikt u op **publiceren**.  
1. Wanneer u hierom wordt gevraagd, selecteert u een bestemming in uw Power BI-account.  Tenzij u een specifieke bestemming hebt, gebruikt u **mijn werk ruimte**.

    ![Power BI Desktop publiceren](media/powerbi/desktop-publish.png)

1. Wanneer de publicatie is voltooid, klikt u op **openen in Power bi** om Power bi met uw nieuwe gegevensset te openen.


### <a name="configure-scheduled-refresh"></a>Geplande vernieuwing configureren
De gegevensset die in Power BI is gemaakt, heeft dezelfde gegevens die u eerder hebt gezien in Power BI Desktop.  U moet de gegevensset periodiek vernieuwen om de query opnieuw uit te voeren en deze vullen met de laatste gegevens van Azure Monitor.  

1. Klik op de werk ruimte waar u het rapport hebt geüpload en selecteer het menu **gegevens sets** . 
1. Selecteer het context menu naast uw nieuwe gegevensset en selecteer **instellingen**. 
1. Onder **referenties voor de gegevens bron** moet u een bericht hebben dat de referenties ongeldig zijn.  Dit komt doordat u nog geen referenties hebt ingevoerd voor de gegevensset die moet worden gebruikt bij het vernieuwen van de gegevens.  
1. Klik op **referenties bewerken** en geef de referenties op die toegang hebben tot de log Analytics werk ruimte in azure monitor. Als u twee ledige verificatie nodig hebt, selecteert u **OAuth2** om u **aan te melden** met uw referenties.

    ![Power BI schema](media/powerbi/powerbi-schedule.png)

5. Schakel onder **geplande vernieuwing** de optie in om **uw gegevens up-to-date te houden**.  U kunt desgewenst de **vernieuwings frequentie** en een of meer specifieke tijden wijzigen om het vernieuwen uit te voeren.

    ![Power BI vernieuwen](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Zoek opdrachten in Logboeken](../log-query/log-query-overview.md) voor het bouwen van query's die kunnen worden geëxporteerd naar Power bi.
* Meer informatie over [Power bi](https://powerbi.microsoft.com) voor het maken van visualisaties op basis van de export van Azure monitor logboek.