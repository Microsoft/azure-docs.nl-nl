---
title: Geavanceerde functies van Azure Metrics Explorer
description: Meer informatie over geavanceerde functies van Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: be3d3f11e90c17bd8c4792418500da651039e480
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562800"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Geavanceerde functies van Azure Metrics Explorer

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u bekend bent met de basis functies van Metrics Explorer. Zie aan de slag [met Azure Metrics Explorer](metrics-getting-started.md)als u een nieuwe gebruiker bent en wilt weten hoe u uw eerste meet diagram maakt.

## <a name="metrics-in-azure"></a>Metrische gegevens in azure

[Metrische gegevens in azure monitor](data-platform-metrics.md) zijn de reeks gemeten waarden en aantallen die gedurende een bepaalde periode worden verzameld en opgeslagen. Er zijn standaard metrische gegevens (of platformen) en aangepaste metrische gegevens. De standaard metrische gegevens worden aan u door het Azure-platform zelf verschaft. Standaard waarden zijn de status-en gebruiks statistieken van uw Azure-resources. Terwijl aangepaste metrische gegevens worden verzonden naar Azure door uw toepassingen met behulp [van de Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](../app/api-custom-events-metrics.md), een  [Windows Azure Diagnostics-extensie (WAD)](./diagnostics-extension-overview.md)of door [Azure monitor rest API](./metrics-store-custom-rest-api.md).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Weer gaven met meerdere metrische gegevens en grafieken maken

U kunt grafieken maken waarin meerdere metrische regels worden getekend of meerdere metrische grafieken tegelijk worden weer gegeven. Met deze functionaliteit kunt u:

- gerelateerde metrische gegevens aan dezelfde grafiek correleren om te zien hoe de ene waarde aan een andere is gerelateerd
- meet waarden met verschillende maat eenheden in een nabijheid weer geven
- statistieken visueel samen voegen en vergelijken van meerdere resources

Als u bijvoorbeeld 5 opslag accounts hebt en u wilt weten hoeveel totale ruimte er tussen de twee is verbruikt, kunt u een (gestapeld) vlak diagram maken waarin de afzonderlijke en som van alle waarden op bepaalde tijdstippen worden weer gegeven.

### <a name="multiple-metrics-on-the-same-chart"></a>Meerdere metrische gegevens in hetzelfde diagram

Maak eerst [een nieuwe grafiek](metrics-getting-started.md#create-your-first-metric-chart). Klik op **metrische gegevens toevoegen** en herhaal de stappen om een andere metriek toe te voegen in hetzelfde diagram.

   > [!NOTE]
   > Normaal gesp roken wilt u geen metrische gegevens met verschillende maat eenheden hebben (bijvoorbeeld ' milliseconden ' en ' KB ') of met een aanzienlijk andere schaal op één grafiek. In plaats daarvan kunt u meerdere grafieken gebruiken. Klik op de knop grafiek toevoegen om meerdere grafieken te maken in Metrics Explorer.

### <a name="multiple-charts"></a>Meerdere grafieken

Klik op de **grafiek toevoegen** en maak een andere grafiek met een andere metriek.

### <a name="order-or-delete-multiple-charts"></a>Meerdere grafieken best Ellen of verwijderen

Als u meerdere grafieken wilt best Ellen of verwijderen, klikt u op het symbool met weglatings tekens ( **...** ) om het menu grafiek te openen en kiest u de juiste menu **opdracht van omhoog**, **omlaag** of **verwijderen**.

## <a name="changing-aggregation"></a>Aggregatie wijzigen

Wanneer u een metriek toevoegt aan een grafiek, selecteert Metrics Explorer automatisch de standaard aggregatie. De standaard instelling is zinvol in de basis scenario's, maar u kunt een andere aggregatie gebruiken om extra inzichten over de metriek te krijgen. Voor het weer geven van verschillende aggregaties in een grafiek is het vereist dat u begrijpt hoe Metrics Explorer ze verwerkt. 

Metrische gegevens zijn de reeks metingen (of metrische waarden) die gedurende de tijds periode zijn vastgelegd. Wanneer u een grafiek uitzet, worden de waarden van de geselecteerde metriek afzonderlijk geaggregeerd tijdens de *tijd korrel*. U selecteert de grootte van de tijd korrel [met het deel venster Metrics Explorer tijd kiezer](metrics-getting-started.md#select-a-time-range). Als u geen expliciete selectie van de tijd korrel maakt, wordt de tijd granulatie automatisch geselecteerd op basis van het momenteel geselecteerde tijds bereik. Zodra de graan tijd is vastgesteld, worden de metrische waarden die zijn vastgelegd tijdens elke periode van het tijds interval geaggregeerd en op de grafiek geplaatst, één data Point per tijd.

Stel bijvoorbeeld dat in de grafiek de waarde voor de **reactie tijd** van de server wordt weer gegeven met behulp van de **gemiddelde** aggregatie over de tijds duur van de **laatste 24 uur** :

- Als de tijd granulatie is ingesteld op 30 minuten, wordt de grafiek getekend vanuit 48 geaggregeerde data Points (het lijn diagram verbindt bijvoorbeeld 48 punten in het teken gebied van de grafiek). Dat wil zeggen 24 uur x 2 data Points per uur. Elk data Point vertegenwoordigt het *gemiddelde* van alle vastgelegde reactie tijden voor server aanvragen die zijn opgetreden tijdens elk van de relevante 30 minuten.
- Als u de tijd granulatie naar 15 minuten overschakelt, krijgt u 96 geaggregeerde data Points.  Dat wil zeggen 24 uur x 4 Data Points per uur.

Er zijn vijf aggregatie typen voor basis statistieken beschikbaar in de metrische gegevens Verkenner: **som**, **aantal**, **min**, **Max** en **gemiddeld**. De **som** aggregatie wordt soms ook wel de **totale** aggregatie genoemd. Voor veel metrische gegevens worden in Metrics Explorer de aggregaties die volledig irrelevant zijn, verborgen en kunnen ze niet worden gebruikt.

- **Sum** : de som van alle waarden die zijn vastgelegd in het aggregatie-interval
- **Count** : het aantal metingen dat over het aggregatie-interval is vastgelegd. Houd er rekening mee dat **Count** gelijk is aan **Sum** in gevallen waarin de metrische gegevens altijd worden vastgelegd met de waarde 1. Dit is gebruikelijk wanneer de metrische gegevens het aantal afzonderlijke gebeurtenissen bijhouden, en elke meting vertegenwoordigt één gebeurtenis (dat wil zeggen dat de code een metrische record afrondt wanneer een nieuwe aanvraag binnenkomt)
- **Gemiddeld** : het gemiddelde van de metrische waarden die zijn vastgelegd in het aggregatie-interval
- **Min** -de kleinste waarde die is vastgelegd tijdens het aggregatie-interval
- **Max** : de grootste waarde die over het aggregatie-interval is vastgelegd

## <a name="apply-filters-to-charts"></a>Filters toep assen op grafieken

U kunt filters toep assen op de grafieken waarin metrische gegevens met dimensies worden weer gegeven. Als de metrische "trans actie Count" bijvoorbeeld een dimensie heeft, "antwoord type", waarmee wordt aangegeven of het antwoord van de trans acties is geslaagd of mislukt en vervolgens filteren op deze dimensie een grafiek regel voor alleen geslaagde (of alleen mislukte) trans acties zou verwerken. 

### <a name="to-add-a-filter"></a>Een filter toevoegen

1. Selecteer **filter toevoegen** boven de grafiek

2. Selecteer welke dimensie (eigenschap) u wilt filteren

   ![Scherm opname van de afmetingen (eigenschappen) die u kunt filteren.](./media/metrics-charts/028.png)

3. Selecteer welke dimensie waarden u wilt gebruiken bij het tekenen van de grafiek (in dit voor beeld ziet u hoe de geslaagde opslag transacties worden gefilterd):

   ![Scherm opname van het filteren van de geslaagde opslag transacties.](./media/metrics-charts/029.png)

4. Nadat u de filter waarden hebt geselecteerd, klikt u op een van de filter selectie vakjes om deze te sluiten. De grafiek toont nu hoeveel opslag transacties zijn mislukt:

   ![Scherm afbeelding die laat zien hoeveel opslag transacties zijn mislukt](./media/metrics-charts/030.png)

5. U kunt stap 1-4 herhalen om meerdere filters op dezelfde grafieken toe te passen.



## <a name="apply-splitting-to-a-chart"></a>Splitsen Toep assen op een grafiek

U kunt een metriek per dimensie splitsen om te visualiseren hoe verschillende segmenten van de metrische gegevens met elkaar worden vergeleken en de begrote segmenten van een dimensie identificeren.

### <a name="apply-splitting"></a>Splitsing Toep assen

1. Klik op **splitsing Toep assen** boven de grafiek.
 
   > [!NOTE]
   > Splitsen kan niet worden gebruikt voor grafieken met meerdere metrische gegevens. U kunt ook meerdere filters hebben, maar er is slechts één Splits dimensie toegepast op één grafiek.

2. Kies een dimensie waarop u de grafiek wilt segmenteren:

   ![Scherm afbeelding met de geselecteerde dimensie waarop u de grafiek wilt segmenteren.](./media/metrics-charts/031.png)

   Nu toont de grafiek nu meerdere regels, één voor elk segment van de dimensie:

   ![Scherm opname waarin meerdere lijnen worden weer gegeven, één voor elk segment van de dimensie.](./media/metrics-charts/032.png)

3. Klik ergens buiten de **groeperings kiezer** om het venster te sluiten.

   > [!NOTE]
   > Gebruik zowel filteren als opsplitsen op dezelfde dimensie om de segmenten te verbergen die niet relevant zijn voor uw scenario en om grafieken gemakkelijker te kunnen lezen.

## <a name="lock-boundaries-of-chart-y-axis"></a>Grenzen van grafiek y-as vergren delen

Het vergren delen van het bereik van de y-as wordt belang rijk wanneer de grafiek kleinere schommelingen van grotere waarden weergeeft. 

Wanneer het volume van geslaagde aanvragen bijvoorbeeld van 99,99% tot 99,5% daalt, kan dit een aanzienlijke vermindering van de Quality of service betekenen. Het merkt van een kleine schommeling van de numerieke waarde zou echter lastig of zelfs onmogelijk van de standaard instellingen voor de grafiek zijn. In dit geval kunt u de laagste grens van de grafiek vergren delen op 99%, waardoor deze kleine weg duidelijker wordt. 

Een ander voor beeld is een schommeling in het beschik bare geheugen, waarbij de waarde technisch nooit 0 is. Als u het bereik naar een hogere waarde herstelt, kan dit het beschik bare geheugen gemakkelijker maken. 

Als u het bereik van de y-as wilt beheren, gebruikt u de '... ' menu grafiek en selecteer **grafiek instellingen** om toegang te krijgen tot geavanceerde grafiek instellingen.

![Scherm afbeelding die de optie grafiek instellingen markeert.](./media/metrics-charts/033.png)

 Wijzig de waarden in de sectie bereik van de Y-as of gebruik de knop **automatisch** om de standaard waarden te herstellen.
 
 ![Scherm afbeelding die de sectie voor het bereik van de Y-as markeert.](./media/metrics-charts/034.png)

> [!WARNING]
> Het vergren delen van de grenzen van de y-as voor de grafieken die verschillende aantallen of sommen gedurende een bepaalde periode bijhouden (en dus gebruik van Count, Sum, minimum of maximum aggregaties), is het meestal nodig om een vaste tijd granulatie op te geven in plaats van de automatische standaard waarden te gebruiken. Dit is nodig omdat de waarden in grafieken worden gewijzigd wanneer de granulatie van de tijd automatisch wordt gewijzigd door het browser venster van de gebruiker opnieuw te wijzigen of door de ene scherm resolutie naar de andere te gaan. De resulterende wijziging van de tijd granulatie heeft gevolgen voor het uiterlijk van de grafiek, waarbij de huidige selectie van het bereik van de y-as ongeldig is.

## <a name="change-colors-of-chart-lines"></a>Kleuren van diagram lijnen wijzigen

Nadat u de grafieken hebt geconfigureerd, krijgen de grafiek lijnen automatisch een kleur uit een standaard palet. U kunt deze kleuren wijzigen.

Als u de kleur van een grafiek lijn wilt wijzigen, klikt u op de gekleurde balk in de legenda die overeenkomt met de grafiek. Het dialoog venster Kleuren kiezer wordt geopend. Gebruik de kleur kiezer om de kleur voor de regel te configureren.

![Scherm afbeelding die laat zien hoe u de kleur wijzigt](./media/metrics-charts/035.png)

Nadat de grafiek kleuren zijn geconfigureerd, blijven ze die manier behouden wanneer u de grafiek vastmaakt aan een dash board. In de volgende sectie ziet u hoe u een grafiek vastmaakt.

## <a name="pin-charts-to-dashboards"></a>Grafieken vastmaken aan dash boards

Nadat u de grafieken hebt geconfigureerd, kunt u deze toevoegen aan de Dash boards, zodat u deze weer in de context van andere telemetrie-bewaking of delen met uw team.

Een geconfigureerde grafiek vastmaken aan een dash board:

Nadat u uw grafiek hebt geconfigureerd, klikt **u op vastmaken aan dash board** in de rechter bovenhoek van de grafiek.

![Scherm opname van het vastmaken aan de grafiek](./media/metrics-charts/036.png)

## <a name="create-alert-rules"></a>Waarschuwingsregels maken

U kunt de criteria die u hebt ingesteld voor het visualiseren van uw metrische gegevens, gebruiken als basis voor een waarschuwings regel op basis van metrische gegevens. De nieuwe waarschuwings regel bevat uw doel resource, metrische gegevens, opsplitsen en filter dimensies uit uw diagram. U kunt deze instellingen later wijzigen in het deel venster waarschuwings regel maken.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Als u een nieuwe waarschuwings regel wilt maken, klikt u op **nieuwe waarschuwings regel**

![Knop nieuwe waarschuwings regel gemarkeerd in rood](./media/metrics-charts/042.png)

U wordt naar het deel venster waarschuwings regels maken geleid met de onderliggende metrische dimensies van uw grafiek die vooraf zijn ingevuld om het eenvoudiger te maken om aangepaste waarschuwings regels te genereren.

![Waarschuwingsregel maken](./media/metrics-charts/041.png)

Raadpleeg dit [artikel](alerts-metric.md) voor meer informatie over het instellen van metrische waarschuwingen.

## <a name="troubleshooting"></a>Problemen oplossen

*Ik zie geen gegevens in mijn grafiek.*

* Filters zijn van toepassing op alle grafieken in het deel venster. Zorg ervoor dat, terwijl u zich in de ene grafiek bevindt, geen filter is ingesteld waarmee alle gegevens op een andere worden uitgesloten.

* Als u verschillende filters op verschillende grafieken wilt instellen, maakt u deze op verschillende Blades en slaat u ze op als afzonderlijke favorieten. Als u wilt, kunt u deze vastmaken aan het dash board, zodat u ze naast elkaar kunt zien.

* Als u een grafiek segmenteert op basis van een eigenschap die niet op de metriek is gedefinieerd, is er niets in de grafiek. Probeer de segmentatie (splitsen) uit te scha kelen of kies een andere eigenschap.

## <a name="next-steps"></a>Volgende stappen

  Lees [aangepaste KPI-Dash boards maken](../learn/tutorial-app-dashboards.md) voor meer informatie over de aanbevolen procedures voor het maken van Dash boards met maat staven met metrische gegevens.
