---
title: Aan de slag met de Azure Metrics Explorer
description: Meer informatie over het maken van uw eerste grafiek met metrische gegevens met Metrics Explorer in Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.openlocfilehash: df745e7612dbd5b5bb9029b89d7f74974270c2d1
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962710"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Aan de slag met Azure Metrics Explorer

## <a name="where-do-i-start"></a>Waar moet ik beginnen?
Azure Monitor Metrics Explorer is een onderdeel van de Microsoft Azure-portal waarmee grafieken kunnen worden getekend, trends visueel worden gecorreleerd en pieken en spannings dips in metrische waarden worden onderzocht. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resources te onderzoeken. Begin in de volgende volg orde:

1. [Kies een resource en metrische gegevens](#create-your-first-metric-chart) en u ziet een eenvoudige grafiek. [Selecteer vervolgens een tijds bereik](#select-a-time-range) dat relevant is voor uw onderzoek.

1. Probeer [dimensie filters toe te passen en te splitsen](#apply-dimension-filters-and-splitting). Met filters en splitsen kunt u analyseren welke segmenten van de metrische gegevens bijdragen aan de algemene metrische waarde en mogelijke uitschieters identificeren.

1. Gebruik [Geavanceerde instellingen](#advanced-chart-settings) om de grafiek aan te passen voordat u aan dash boards vastmaakt. [Waarschuwingen configureren](../alerts/alerts-metric-overview.md) voor het ontvangen van meldingen wanneer de waarde van de metriek groter of kleiner dan een drempel overschrijdt.

## <a name="create-your-first-metric-chart"></a>Uw eerste metrieke grafiek maken

Als u een metrische grafiek wilt maken, vanuit uw resource, resource groep, abonnement of Azure Monitor weer gave, opent u het tabblad **metrische gegevens** en volgt u deze stappen:

1. Klik op de knop een bereik selecteren om de resource bereik kiezer te openen. Hiermee kunt u de resources selecteren waarvoor u de metrische gegevens wilt bekijken. De resource moet al zijn ingevuld als u metrische gegevens Verkenner hebt geopend vanuit het menu van de resource. [Lees dit artikel](./metrics-dynamic-scope.md)voor meer informatie over het weer geven van metrische gegevens over meerdere resources.
    > ![Een resource selecteren](./media/metrics-getting-started/scope-picker.png)

2. Voor sommige resources moet u een naam ruimte kiezen. De naamruimte is slechts een manier om metrische gegevens te organiseren, zodat u ze gemakkelijk kunt vinden. Zo hebben opslagaccounts afzonderlijke naamruimten voor het opslaan van bestanden, tabellen, blobs en wachtrijen. Veel resourcetypen hebben slechts één naamruimte.

3. Selecteer een metrische waarde in een lijst met beschik bare metrische gegevens.

    > ![Een metrische waarde selecteren](./media/metrics-getting-started/metrics-dropdown.png)

4. U kunt desgewenst [de metrische aggregatie wijzigen](../essentials/metrics-charts.md#aggregation). U kunt bijvoorbeeld aangeven dat de grafiek minimum-, maximum-of gemiddelde waarden van de metriek moet weer geven.

> [!TIP]
> Gebruik de knop **Metrische waarde toevoegen** en herhaal deze stappen als u meerdere metrische waarden wilt zien die in dezelfde grafiek zijn getekend. Voor meerdere grafieken in één weer gave selecteert u de knop **grafiek toevoegen** bovenaan.

## <a name="select-a-time-range"></a>Een tijds bereik selecteren

> [!WARNING]
> De [meeste metrische gegevens in Azure worden gedurende 93 dagen bewaard](../essentials/data-platform-metrics.md#retention-of-metrics). U kunt echter niet meer dan 30 dagen aan gegevens in één grafiek opvragen. U kunt het diagram [pannen](metrics-charts.md#pan) om de volledige Bewaar periode weer te geven. De beperking van 30 dagen geldt niet voor [metrische gegevens op basis van een logboek](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

In de grafiek worden standaard de meest recente 24 uur aan metrische gegevens weergegeven. Gebruik het deel venster **tijd kiezer** om het tijds bereik te wijzigen, in te zoomen of uit te zoomen op de grafiek. 

![Venster voor het wijzigen van het tijdsbereik](./media/metrics-getting-started/time.png)

> [!TIP]
> Gebruik het **tijd penseel** om een interessant gebied van de grafiek (Prikker of een DIP) te onderzoeken. Plaats de muis aanwijzer aan het begin van het gebied, klik op de linkermuisknop en houd de muis knop ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. In de grafiek wordt ingezoomd op dat tijdsbereik. 

## <a name="apply-dimension-filters-and-splitting"></a>Dimensies filteren en splitsen

[Filteren](../essentials/metrics-charts.md#filters) en [splitsen](../essentials/metrics-charts.md#apply-splitting) zijn krachtige diagnostische hulpprogram ma's voor metrische gegevens die dimensies hebben. Deze functies laten zien hoe diverse metrische gegevens segmenten ("dimensie waarden") van invloed zijn op de algemene waarde van de metrische gegevens en waarmee u mogelijke uitbijtingen kunt identificeren.

- Met **Filteren** kunt u kiezen welke dimensiewaarden in de grafiek worden opgenomen. U kunt bijvoorbeeld geslaagde aanvragen weer geven bij het grafieken van de waarde voor de *reactie tijd* van de server. U moet het filter Toep assen op het *succes van de aanvraag* dimensie. 

- Met **Splitsen** bepaalt u of in het diagram elke waarde van een dimensie wordt weergegeven op een afzonderlijke regel of dat de waarden worden geaggregeerd op één regel. U kunt bijvoorbeeld één regel weer geven voor een gemiddelde reactie tijd voor alle Server exemplaren, of afzonderlijke regels voor elke server weer geven. U moet splitsen Toep assen op de dimensie van het *Server exemplaar* om afzonderlijke regels te bekijken.

Zie [voorbeelden van grafieken](../essentials/metric-chart-samples.md) waarin een filter of splitsing is toegepast. In het artikel worden de stappen beschreven die zijn gebruikt om de grafieken te configureren.

## <a name="share-your-metric-chart"></a>Uw metrieke grafiek delen
Er zijn momenteel twee manieren om uw metrische grafiek te delen. Hieronder vindt u instructies voor het delen van informatie uit uw metrische grafieken via Excel en een koppeling.
 
### <a name="download-to-excel"></a>Downloaden naar Excel
Klik op delen en selecteer downloaden naar Excel. Het downloaden moet onmiddellijk worden gestart.

![scherm afbeelding voor het delen van metrische grafiek via Excel](./media/metrics-getting-started/share-excel.png)

### <a name="share-a-link"></a>Een koppeling delen
Klik op delen en selecteer Koppeling kopiëren. U ontvangt een melding dat de koppeling is gekopieerd.

![scherm afbeelding voor het delen van metrische grafieken via een koppeling](./media/metrics-getting-started/share-link.png)


## <a name="advanced-chart-settings"></a>Geavanceerde instellingen voor grafieken

U kunt de stijl, titel en geavanceerde instellingen van de grafiek aanpassen. Wanneer u klaar bent met uw aanpassingen, kunt u deze vastmaken aan een dashboard om uw werk op te slaan. U kunt ook waarschuwingen voor metrische gegevens configureren. Volg de [product documentatie](../essentials/metrics-charts.md) voor meer informatie over deze en andere geavanceerde functies van Azure monitor Metrics Explorer.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over geavanceerde functies van Metrics Explorer](../essentials/metrics-charts.md)
* [Meerdere resources in Metrics Explorer weer geven](./metrics-dynamic-scope.md)
* [Problemen met Metrics Explorer oplossen](metrics-troubleshoot.md)
* [Een lijst met beschikbare metrische gegevens voor Azure-services zien](./metrics-supported.md)
* [Voorbeelden van geconfigureerde grafieken zien](../essentials/metric-chart-samples.md)
