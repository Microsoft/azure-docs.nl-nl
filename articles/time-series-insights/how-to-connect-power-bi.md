---
title: Verbind uw omgeving met Power BI-Azure Time Series Insights | Microsoft Docs
description: Informatie over het verbinden van Azure Time Series Insights naar Power BI voor het delen, het diagram en weer geven van gegevens in uw organisatie.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 07e79dbde142400677901ee02903144f9a42cd6b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740712"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Gegevens visualiseren van Azure Time Series Insights in Power BI

Azure Time Series Insights is een platform voor het opslaan, beheren, opvragen en visualiseren van Time Series-gegevens in de Cloud. [Power bi](https://powerbi.microsoft.com) is een Business Analytics-hulp programma met uitgebreide visualisatie mogelijkheden waarmee u inzichten en resultaten kunt delen binnen uw organisatie. Beide services kunnen nu worden geïntegreerd zodat u de krachtige analyse van Azure Time Series Insights kunt uitbreiden met de krachtige gegevens visualisatie en eenvoudig delen van Power BI.

U leert het volgende:

* Azure Time Series Insights verbinding maken met Power BI met behulp van de systeem eigen Azure Time Series Insights-connector
* Visuals maken met uw tijdreeks gegevens in Power BI
* Het rapport publiceren naar Power BI en delen met de rest van uw organisatie


## <a name="prerequisites"></a>Vereisten

* Neem een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u nog geen abonnement hebt.
* Down load en installeer de nieuwste versie van [Power bi Desktop](https://powerbi.microsoft.com/downloads/)
* Een [Azure time series Insights Gen2-omgeving](./how-to-provision-manage.md) hebben of maken

Controleer de [beleids regels voor toegang tot de omgeving](./concepts-access-policies.md) en zorg ervoor dat u directe toegang of gast toegang hebt tot de Gen2-omgeving van Azure time series Insights. 

> [!IMPORTANT]
> * Down load en installeer de nieuwste versie van [Power bi Desktop](https://powerbi.microsoft.com/downloads/). Als u de stappen in dit artikel wilt volgen, moet u ervoor zorgen dat u ten minste de 2.88.321.0-versie (december 2020) van Power BI Desktop hebt geïnstalleerd. 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Gegevens verbinden van Azure Time Series Insights naar Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. gegevens exporteren naar Power BI bureau blad

Aan de slag:

1. Open de Azure Time Series Insights Gen2 Explorer om uw gegevens te openen. Dit zijn de gegevens die worden geëxporteerd naar Power BI.
1. Wanneer u een weer gave hebt gemaakt waarin u tevreden bent, gaat u naar het vervolg keuzemenu **meer acties** en selecteert u **verbinding maken met Power bi**.

    [![Azure Time Series Insights Gen2 Explorer exporteren](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Stel de para meters voor het exporteren in:

   * **Gegevens indeling**: Kies of u de **samengevoegde gegevens** of **onbewerkte gebeurtenissen** wilt exporteren naar Power bi. 

       > [!NOTE]
       > * Als u onbewerkte gebeurtenissen exporteert, kunt u die gegevens later in Power BI samen voegen. Als u echter geaggregeerde gegevens exporteert, kunt u de onbewerkte gegevens in Power BI niet meer herstellen. 
       > * Er is een limiet van 250.000 gebeurtenissen voor onbewerkte gebeurtenis niveau gegevens.

   * **Tijds bereik**: Kies of u een **vast** tijds bereik of de **meest recente** gegevens in Power bi wilt zien. Als u het vaste tijds bereik kiest, worden de gegevens in de zoek reeks die u hebt gediagram, geëxporteerd naar Power BI. Als u het meest recente tijds bereik kiest Power BI, worden de meest recente gegevens voor de door u gekozen Zoek reeks Power BI weer gegeven (bijvoorbeeld als u één uur aan gegevens in een grafiek hebt genoteerd en de instelling ' laatst ' kiest, worden er altijd query's uitgevoerd voor het laatste 1 uur aan gegevens.)
  
   * **Opslag type**: Kies of u de geselecteerde query wilt uitvoeren voor een **warme Store** of een **koude opslag**. 

    > [!TIP]
    > * Azure Time Series Insights Explorer selecteert automatisch de aanbevolen para meters, afhankelijk van de gegevens die u hebt geselecteerd om te exporteren. 

1. Nadat u uw instellingen hebt geconfigureerd, selecteert u **query kopiëren naar klem bord**.

    [![Modale export van Azure Time Series Insights Explorer](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. Start Power BI Desktop.
   
3. Selecteer in Power BI Desktop op het tabblad **Start** de optie **gegevens ophalen** in de **linkerbovenhoek.**

    [![Gegevens ophalen in Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. Zoek naar **Azure time series Insights**, selecteer **Azure time series Insights (bèta)** en vervolgens **verbinding maken**.

    [![Power BI verbinden met Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    U kunt ook naar het tabblad **Azure** gaan, **Azure time series Insights (bèta)** selecteren en vervolgens **verbinding maken**.

5. Plak de query die u hebt gekopieerd uit Azure Time Series Insights Explorer in het **aangepaste query** veld en druk vervolgens op **OK**.

    [![Plak de aangepaste query en selecteer OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  De gegevens tabel wordt nu geladen. Druk op **laden** om in Power bi te laden. Als u trans formaties voor de gegevens wilt maken, kunt u dit nu doen door te klikken op **gegevens transformeren**. U kunt ook uw gegevens transformeren nadat deze is geladen.

    [![Controleer de gegevens in de tabel en selecteer laden](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Een rapport maken met visuele elementen

Nu u de gegevens in Power BI hebt geïmporteerd, is het tijd om een rapport met visuals te maken.

1. Zorg ervoor dat op de linkerkant van het venster de **rapport** weergave is geselecteerd.

    [![Scherm afbeelding toont pictogram rapport weergave.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Selecteer in de kolom **Visualisaties** uw visuele element van uw keuze. Selecteer bijvoorbeeld **lijn diagram**. Hiermee wordt een leeg lijn diagram aan uw canvas toegevoegd.

1.  Selecteer in de lijst **velden** de optie **_Timestamp** en sleep deze naar het **asveld** om tijd weer te geven langs de X-as. Zorg ervoor dat u overschakelt naar **_Timestamp** als de waarde voor de **as** (standaard is **datum hiërarchie**).

    [![_Timestamp selecteren](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Selecteer opnieuw in de lijst **velden** de variabele die u wilt uitzetten en sleep deze naar het veld **waarden** om waarden weer te geven op de Y-as. Selecteer de waarde voor de tijd reeks-ID en sleep deze naar het veld **legenda** om meerdere regels in de grafiek te maken, één per tijd reeks-id. Hiermee wordt een weer gave weer gegeven die vergelijkbaar is met de functie die wordt geleverd door de Azure Time Series Insights Explorer. 

    [![Een basis lijn diagram maken](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Als u nog een grafiek aan uw canvas wilt toevoegen, selecteert u ergens op het canvas buiten het lijn diagram en herhaalt u dit proces.

    [![Extra grafieken maken om te delen](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Zodra u het rapport hebt gemaakt, kunt u het publiceren naar Power BI Reporting Services en delen met anderen in uw organisatie.

## <a name="advanced-editing"></a>Geavanceerd bewerken
Als u al een gegevensset in Power BI hebt geladen, maar u de query wilt wijzigen (zoals de para meters voor datum/tijd of omgevings-ID), kunt u dit doen via de Geavanceerde editor functionaliteit van Power BI. Raadpleeg de [documentatie van Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) voor meer informatie over het aanbrengen van wijzigingen met behulp van de **Power query-editor**. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Power bi bureau blad](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Meer informatie over het [opvragen van gegevens](concepts-query-overview.md) in azure time series Insights Gen2.
