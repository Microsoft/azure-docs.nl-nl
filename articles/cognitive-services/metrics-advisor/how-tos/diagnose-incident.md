---
title: Incidenten diagnosticeren met metrische gegevens adviseur
titleSuffix: Azure Cognitive Services
description: Meer informatie over het vaststellen van een incident met metrische gegevens adviseur en het verkrijgen van gedetailleerde weer gaven van afwijkingen.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703419"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Instructies: een incident diagnosticeren met metrische gegevens adviseur

Met metrische gegevens adviseur beschikt u over verschillende functies voor diagnostische gegevens en krijgt u een gedetailleerde weer gave van gedetecteerde incidenten, en kunt u analyse van de hoofd oorzaak geven. Wanneer een groep afwijkingen op een metrische waarde wordt gedetecteerd, worden afwijkingen door de gegevens adviseur in een hiërarchie gegroepeerd en worden ze bovenop elkaar geanalyseerd.

> [!NOTE]
> De functie Advisor ondersteunt momenteel incident diagnostiek voor metrische gegevens met ten minste één dimensie en meting met het  *numerieke* type. De metrische gegevens moeten een geaggregeerde dimensie waarde hebben, zoals SUM voor elke dimensie, die wordt gebruikt om de diagnostische hiërarchie te maken. Met metrische gegevens adviseur kunt u [**instellingen voor automatisch totaliseren**](onboard-your-data.md#automatic-roll-up-settings) voor het genereren van geaggregeerde waarden. 

Klik op **incident hub** in het navigatie venster aan de linkerkant om alle incidenten te bekijken onder een bepaalde metriek. Boven aan de pagina kunt u verschillende metrische gegevens selecteren om de detectie configuraties en detectie resultaten te bekijken en het tijds bereik te wijzigen.

> [!TIP]
> U kunt ook naar de **incident hub** gaan door het volgende te doen:
> * Klik op een gegevens punt in de visualisatie voor uw metriek en gebruik de koppelingen onder aan het venster **Feedback toevoegen** dat wordt weer gegeven.
> * Klik op een van de afwijkingen op het tabblad **incidenten** voor uw metrische gegevens. 

De sectie **overzicht** bevat resultaten van de detectie, inclusief de aantallen van de afwijkingen en waarschuwingen binnen het geselecteerde tijds bereik.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Incident hub" lightbox="../media/diagnostics/incident-hub-overview.png":::

Gedetecteerde incidenten binnen de geselecteerde meet waarde en tijds bereik worden weer gegeven in de **lijst met incidenten**. Er zijn opties voor het filteren en ordenen van de incidenten. Bijvoorbeeld op Ernst. Klik op een van de incidenten om naar de pagina **incident** te gaan voor verdere diagnostische gegevens.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Lijst met incidenten" lightbox="../media/diagnostics/incident-list.png":::

Met de sectie **Diagnostische gegevens** kunt u een grondige analyse uitvoeren op een incident en hulpprogram ma's voor het identificeren van de hoofd oorzaken.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Een incident vaststellen" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Hoofd oorzaak advies

Wanneer een groep afwijkingen wordt gedetecteerd in een metriek en een incident veroorzaakt, probeert metrische gegevens adviseur de hoofd oorzaak van het incident te analyseren. **Hoofd oorzaak advies** biedt automatische suggesties voor mogelijke oorzaken van een incident. Deze functie is alleen beschikbaar als er sprake is van een geaggregeerde waarde binnen de dimensie. Als de metriek geen dimensie heeft, wordt de hoofd oorzaak zelf. Hoofd oorzaken worden weer gegeven aan de rechter kant van het paneel en er kunnen verschillende redenen zijn. Als er geen gegevens in de tabel staan, betekent dit dat uw dimensie niet voldoet aan de vereisten voor het uitvoeren van de analyse.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Hoofd oorzaak advies":::


Als de hoofd oorzaak van metrische gegevens met specifieke dimensies wordt opgegeven, kunt u op **Ga naar metriek** klikken om meer informatie over de metrische gegevens weer te geven.

## <a name="incident-tree"></a>Incident structuur

Naast de geautomatiseerde analyse van mogelijke hoofd oorzaken ondersteunt de gegevens adviseur hand matige analyse van de hoofd oorzaak met behulp van de **incident structuur**. Er zijn twee soorten incident structuur op incident pagina: de **snelle diagnose** structuur en de **interactieve boom structuur**.

De boom structuur voor snelle diagnose is voor het diagnosticeren van een actueel incident en het hoofd knooppunt is beperkt tot het huidige hoofd knooppunt van het incident. U kunt de structuur knooppunten uitvouwen en samen vouwen door erop te klikken. de bijbehorende reeks wordt samen met de huidige incident serie in de grafiek boven de boom weer gegeven.

Met de interactieve boom structuur kunt u de huidige incidenten en oudere incidenten, en de bijbehorende problemen, vaststellen. Als u de interactieve boom structuur gebruikt, klikt u met de rechter muisknop op een knoop punt om een actie menu te openen, waar u een dimensie kunt kiezen om uit te zoomen op de hoofd knooppunten en een dimensie om voor elk knoop punt in te zoomen. Als u op de knop Annuleren van de lijst dimensie bovenaan klikt, kunt u het inzoomen op of omlaag uit deze dimensie verwijderen. Klik met de rechter muisknop op een knoop punt om deze te selecteren en de reeks weer te geven samen met de huidige incident serie in de grafiek.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Incident structuur" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Anomalie analyse

Wanneer u informatie over incidenten bekijkt, moet u mogelijk gedetailleerde informatie over de verschillende dimensies en tijds tempels ophalen. Als uw gegevens een of meer dimensies hebben, kunt u de functie voor inzoomen gebruiken om een meer gedetailleerde weer gave te krijgen. 

Als u de functie voor inzoomen wilt gebruiken, klikt u op het tabblad **metrische boringen** in de hub van het **incident**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Metrisch boren ":::

De instelling **dimensies** is een lijst met dimensies voor een incident. u kunt voor elke optie andere beschik bare dimensie waarden selecteren. Nadat de dimensie waarden zijn gewijzigd. Met de instelling **tijds tempel** kunt u het huidige incident op verschillende momenten in de tijd bekijken.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Selecteer opties voor inzoomen en kies een dimensie

Er zijn twee soorten opties voor inzoomen: **Inzoomen** en **horizontale vergelijking**.

> [!Note]
> 1. Voor inzoomen kunt u de gegevens uit verschillende dimensie waarden verkennen, met uitzonde ring van de Currenly geselecteerde dimensies. 
> 2. Voor een horizontale vergelijking kunt u de gegevens uit verschillende dimensie waarden verkennen, met uitzonde ring van de volledige dimensies.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Dimensie inzoomen":::

### <a name="value-comparison-for-different-dimension-values"></a>Vergelijking van waarden voor verschillende dimensie waarden

Het tweede gedeelte van het tabblad inzoomen is een tabel met vergelijkingen voor verschillende dimensie waarden. Het bevat de waarde, de basislijn waarde, de verschil waarde, de Delta waarde en of het een afwijkend item is.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Vergelijking van inzoomen" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Vergelijking van waarden en verwachte waarden voor verschillende dimensie waarden

De derde sectie van het tabblad inzoomen is een histogram met de waarden en verwachte waarden voor verschillende dimensie waarden. Het histogram wordt gesorteerd op het verschil tussen de waarde en de verwachte waarde. U kunt de onverwachte waarde eenvoudig vinden met de grootste impact. In de bovenstaande afbeelding kunnen we bijvoorbeeld zien dat, met uitzonde ring van de waarde all up, **US7** het meest voor de afwijkingen bijdraagt.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Tabel inzoomen" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Visualisatie van RAW-waarden
Het laatste deel van het tabblad inzoomen is een lijn diagram van de onbewerkte waarden. Als deze grafiek is opgenomen, hoeft u niet naar de metrische pagina te navigeren om de details weer te geven.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Lijn diagram inzoomen" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Vergelijk bare afwijkingen weer geven met behulp van Time Series-clustering

Wanneer u een incident bekijkt, kunt u het tabblad **soort gelijke tijd reeks-clustering** gebruiken om de verschillende reeksen te zien die eraan zijn gekoppeld. Reeksen in één groep worden samen samenvatten. In de bovenstaande afbeelding kunnen we weten dat er ten minste twee reeks groepen zijn. Deze functie is alleen beschikbaar als aan de volgende vereisten wordt voldaan:

1. Metrische gegevens moeten een of meer dimensies of dimensie waarden hebben.
2. De reeks binnen een metriek moet een vergelijk bare trend hebben.

Beschik bare dimensies worden weer gegeven aan de bovenkant van het tabblad en u kunt een selectie maken om de reeks op te geven.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Reeks groep":::

## <a name="compare-time-series"></a>Tijd reeks vergelijken

Soms is het handig om een afwijkend element te vergelijken met meerdere andere reeksen in één visualisatie. Klik op het tabblad **hulp middelen vergelijken** en klik vervolgens op de knop Blue **+ toevoegen** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Reeks toevoegen om te vergelijken" lightbox="../media/diagnostics/add-series.png":::

Selecteer een reeks in uw gegevensfeed. U kunt dezelfde granulariteit of een andere naam kiezen. Selecteer de doel dimensies en laad de reeks trend en klik vervolgens op **OK** om deze te vergelijken met een vorige serie. De reeks wordt samen in één visualisatie gecombineerd. U kunt door gaan met het toevoegen van meer reeksen om meer inzicht te krijgen. Klik op de vervolg keuzelijst boven aan het tabblad **hulpprogram Ma's vergelijken** om de tijdreeks gegevens gedurende een bepaalde periode te vergelijken.  

> [!Warning]
> Als u een vergelijking wilt maken, kan de time series-gegevens analyse in gegevens punten worden verschoven zodat de granulatie van uw gegevens deze moet ondersteunen. Als uw gegevens bijvoorbeeld wekelijks zijn en u de **dag** voor het vergelijken van dagen gebruikt, worden er geen resultaten weer gegeven. In dit voor beeld gebruikt u in plaats daarvan het vergelijken van de **maand over maanden** .

Nadat u een tijd verschuiving hebt geselecteerd, kunt u selecteren of u de gegevens waarden, de Delta waarden of het percentage Delta wilt vergelijken.

> [!Note]
> * **Gegevens waarde** is de onbewerkte gegevens waarde.
> * De **Delta waarde** is het verschil tussen de ruwe waarde en de vergeleken waarde.
> * De **procentuele Delta waarde** is het verschil tussen de onbewerkte waarde en de vergeleken waarde gedeeld door de vergeleken waarde.

## <a name="related-incidents-across-metrics"></a>Gerelateerde incidenten over metrische gegevens

Soms moet u mogelijk de incidenten van verschillende metrische gegevens op hetzelfde moment controleren of gerelateerde incidenten in andere metrische gegevens. U vindt een lijst met verwante incidenten in de sectie **analyse van meerdere metrieken** . 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="gerelateerde incidenten over metrische gegevens":::

Voordat u verwante incidenten voor de huidige metriek kunt zien, moet u een relatie tussen de metrische gegevens toevoegen. Klik op **metrische grafiek instellingen** om een relatie toe te voegen. Alleen metrische gegevens met dezelfde dimensie namen kunnen worden gerelateerd. Gebruik de volgende parameters.

- Huidige gegevensfeed & metrische gegevens: de gegevensfeed en metric van het huidige incident
- Richting: de richting van de relatie tussen twee metrische gegevens. (nu niet van toepassing op de lijst Verwante incidenten)
- Een andere gegevensfeed & metrische gegevens: de gegevensfeed en metriek om verbinding te maken met de huidige metriek


## <a name="next-steps"></a>Volgende stappen 

- [Anomaliedetectie aanpassen met feedback](anomaly-feedback.md)
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)
