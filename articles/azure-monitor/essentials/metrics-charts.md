---
title: Geavanceerde functies van de Azure Metrics Explorer
description: Meer informatie over Geavanceerd gebruik van de Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: 444185dc41532bfa55c271e2f0027eb8b71de42f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959896"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Geavanceerde functies van de Azure Metrics Explorer

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u bekend bent met de basis functies van de Azure Metrics Explorer-functie van Azure Monitor. Als u een nieuwe gebruiker bent en wilt weten hoe u uw eerste meet diagram maakt, raadpleegt u [aan de slag met de metrics Explorer](./metrics-getting-started.md).

In Azure Monitor zijn [metrische gegevens](data-platform-metrics.md) een reeks gemeten waarden en aantallen die gedurende een bepaalde periode worden verzameld en opgeslagen. Metrische gegevens kunnen standaard (ook wel ' platform ' genoemd) of aangepast zijn. 

Standaard metrische gegevens worden door het Azure-platform verschaft. Ze zijn gebaseerd op de status-en gebruiks statistieken van uw Azure-resources. 

## <a name="resource-scope-picker"></a>Resource bereik kiezer
Met de kiezer voor het bereik van resources kunt u metrische gegevens weer geven over enkele resources en meerdere resources. In de volgende secties wordt uitgelegd hoe u de resource bereik kiezer gebruikt. 

### <a name="select-a-single-resource"></a>Eén resource selecteren
Selecteer **Metrische gegevens** in het menu van **Azure Monitor** of in de sectie **Controle** van het menu van een resource. Kies vervolgens **een bereik selecteren** om de bereik kiezer te openen. 

Gebruik de bereik kiezer om de resources te selecteren waarvan u de metrische gegevens wilt zien. Het bereik moet worden ingevuld als u de Azure Metrics Explorer hebt geopend vanuit het menu van een resource. 

![Scherm afbeelding die laat zien hoe u de kiezer van het resource bereik kunt openen.](./media/metrics-charts/scope-picker.png)

Voor sommige resources kunt u de metrische gegevens van één resource per keer weer geven. Deze resources bevinden zich in het menu **resource typen** in het gedeelte **alle resource typen** .

![Scherm opname met één resource.](./media/metrics-charts/single-resource-scope.png)

Nadat u een resource hebt geselecteerd, ziet u alle abonnementen en resource groepen die deze resource bevatten.

![Scherm opname met beschik bare resources.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Als u de mogelijkheid wilt om de metrische gegevens voor meerdere resources tegelijk weer te geven of om de metrische gegevens over een abonnement of resource groep weer te geven, selecteert u **upstem**.

Wanneer u tevreden bent met uw selectie, selecteert u **Toep assen**.

### <a name="view-metrics-across-multiple-resources"></a>Metrische gegevens over meerdere resources weer geven
Sommige resource typen kunnen een query uitvoeren op metrische gegevens over meerdere resources. De resources moeten zich in hetzelfde abonnement en dezelfde locatie bevallen. Deze resource typen vindt u boven aan het menu **resource typen** . 

Zie [meerdere resources selecteren](./metrics-dynamic-scope.md#select-multiple-resources)voor meer informatie.

![Scherm opname met verschillende typen resources.](./media/metrics-charts/multi-resource-scope.png)

Voor typen die compatibel zijn met meerdere resources kunt u een query uitvoeren voor metrische gegevens over een abonnement of meerdere resource groepen. Zie [een resource groep of een abonnement selecteren](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription)voor meer informatie.

## <a name="multiple-metric-lines-and-charts"></a>Meerdere metrische lijnen en grafieken

U kunt in de Azure Metrics Explorer grafieken maken waarin meerdere metrische regels worden getekend of meerdere metrische grafieken tegelijk worden weer gegeven. Met deze functionaliteit kunt u:

- Correleer gerelateerde metrische gegevens in dezelfde grafiek om te zien hoe de ene waarde in verhouding staat tot de andere.
- Meet gegevens weer geven die gebruikmaken van verschillende meet eenheden, in de buurt van elkaar.
- Hiermee worden metrische gegevens visueel geaggregeerd en vergeleken met meerdere resources.

Stel dat u vijf opslag accounts hebt en u wilt weten hoeveel ruimte er samen wordt verbruikt. U kunt een (gestapeld) vlak diagram maken waarin de afzonderlijke waarden en de som van alle waarden op bepaalde tijdstippen worden weer gegeven.

### <a name="multiple-metrics-on-the-same-chart"></a>Meerdere metrische gegevens in hetzelfde diagram

Als u meerdere metrische gegevens in hetzelfde diagram wilt weer geven, maakt u eerst [een nieuwe grafiek](./metrics-getting-started.md#create-your-first-metric-chart). Selecteer vervolgens **metrische gegevens toevoegen**. Herhaal deze stap om nog een metrische waarde toe te voegen aan hetzelfde diagram.

> [!NOTE]
> Normaal gesp roken mogen uw grafieken geen metrische gegevens combi neren die gebruikmaken van verschillende maat eenheden. Vermijd bijvoorbeeld het combi neren van één metriek die milliseconden gebruikt met een andere waarde die kilo bytes gebruikt. Vermijd ook het combi neren van metrische gegevens waarvan de schalen aanzienlijk verschillen. 
>
> In dergelijke gevallen kunt u in plaats daarvan meerdere grafieken gebruiken. Selecteer in de metrics Explorer de optie **grafiek toevoegen** om een nieuwe grafiek te maken.

### <a name="multiple-charts"></a>Meerdere grafieken

Selecteer **grafiek toevoegen** om een andere grafiek te maken die gebruikmaakt van een andere metriek.

Als u meerdere grafieken wilt ordenen of verwijderen, selecteert u de knop met het weglatings teken (**...**) om het menu grafiek te openen. Vervolgens kiest **u omhoog**, **omlaag** of **verwijderen**.

## <a name="time-range-controls"></a>Besturings elementen voor tijd bereik

Naast het wijzigen van het tijds bereik met het [deel venster tijd kiezer](metrics-getting-started.md#select-a-time-range)kunt u ook de besturings elementen in het grafiek gebied pannen en inzoomen.
### <a name="pan"></a>Pannen

Als u wilt pannen, klikt u op de pijl naar links en rechts aan de rand van de grafiek.  Hiermee wordt het geselecteerde tijds bereik weer verplaatst naar een halve tijd van de grafiek.  Als u de afgelopen 24 uur bijvoorbeeld bekijkt, klikt u op de pijl-links om het tijds bereik te verschuiven naar een dag en een halve tot 12 uur geleden.

De meeste metrische gegevens ondersteunen 93 dagen retentie, maar u kunt slechts 30 dagen per keer weer geven.  Met de besturings elementen voor pannen bekijkt u de afgelopen 30 dagen en gaat u snel vijf tien dagen per keer teruglopen om de rest van de Bewaar periode weer te geven.

![GIF-animatie met de linker-en rechter regelaar-besturings elementen.](./media/metrics-charts/metrics-pan-controls.gif)

### <a name="zoom"></a>Zoom

U kunt op het diagram klikken en slepen om in te zoomen op een sectie van een grafiek.  Als u inzoomt, wordt het tijds bereik van de grafiek bijgewerkt om uw selectie op te slaan en wordt er een kleinere tijd geselecteerd als de tijd korrel is ingesteld op ' automatisch '.  Het nieuwe tijds bereik is van toepassing op alle grafieken in metrische gegevens.

![GIF-animatie waarin de functie voor het zoomen op metrische gegevens wordt weer gegeven.](./media/metrics-charts/metrics-zoom-control.gif)

## <a name="aggregation"></a>Aggregatie

Wanneer u een metriek toevoegt aan een grafiek, past de metrics Explorer automatisch een standaard aggregatie toe. De standaard instelling is zinvol in basis scenario's. U kunt echter een andere aggregatie gebruiken om meer inzicht te krijgen in de metrische gegevens. 

Voordat u verschillende aggregaties in een grafiek gebruikt, moet u weten hoe de metrische gegevens Verkenner deze verwerkt. Metrische gegevens zijn een reeks metingen (of metrische waarden) die gedurende een bepaalde periode worden vastgelegd. Wanneer u een grafiek uitzet, worden de waarden van de geselecteerde metriek afzonderlijk geaggregeerd tijdens de *tijd korrel*. 

U selecteert de grootte van de tijdgranulariteit door het [deel venster tijd kiezer](./metrics-getting-started.md#select-a-time-range)van de metrieken Explorer te gebruiken. Als u de tijdgranulariteit niet expliciet selecteert, wordt standaard het momenteel geselecteerde tijds bereik gebruikt. Nadat de tijd is vastgesteld, worden de metrische waarden die zijn vastgelegd tijdens elke tijd korrel, geaggregeerd in de grafiek, één gegevens punt per tijd.

Stel bijvoorbeeld dat een grafiek de waarde voor de *reactie tijd* van de server weergeeft. Er wordt gebruikgemaakt van de *gemiddelde* aggregatie over de tijds duur van de *laatste 24 uur*. In dit voorbeeld:

- Als de tijd granulatie is ingesteld op 30 minuten, wordt de grafiek getekend van 48 geaggregeerde gegevens punten. Dat wil zeggen dat het lijn diagram 48 punten in het grafiek teken gebied verbindt (24 uur x 2 gegevens punten per uur). Elk gegevens punt vertegenwoordigt het *gemiddelde* van alle vastgelegde reactie tijden voor server aanvragen die zijn opgetreden tijdens elk van de relevante Peri Oden van 30 minuten.
- Als u de tijd granulatie naar 15 minuten overschakelt, krijgt u 96 geaggregeerde gegevens punten.  Dat wil zeggen dat u 24 uur x 4 gegevens punten per uur ontvangt.

Metrics Explorer heeft vijf statistische aggregatie typen: som, aantal, min, Max en gemiddeld. De *som* aggregatie wordt soms de *totale* aggregatie genoemd. Voor veel metrische gegevens verbergt de metrische gegevens Verkenner de aggregaties die irrelevant zijn en kunnen niet worden gebruikt.

* **Sum**: de som van alle waarden die zijn vastgelegd tijdens het aggregatie-interval.

    ![Scherm opname van een Sum-aanvraag.](./media/metrics-charts/request-sum.png)

* **Aantal**: het aantal metingen dat tijdens het aggregatie-interval is vastgelegd. 
    
    Wanneer de metriek altijd wordt vastgelegd met de waarde 1, is de totale aggregatie gelijk aan de aggregatie van de som. Dit scenario is gebruikelijk wanneer de metrische gegevens het aantal afzonderlijke gebeurtenissen bijhouden en elke meting een gebeurtenis vertegenwoordigt. De code verzendt elke keer dat een nieuwe aanvraag arriveert een metrische record.

    ![Scherm opname van een aantal aanvragen.](./media/metrics-charts/request-count.png)

* **Gemiddelde**: het gemiddelde van de metrische waarden die tijdens het aggregatie-interval zijn vastgelegd.

    ![Scherm opname van een gemiddelde aanvraag.](./media/metrics-charts/request-avg.png)

* **Min**: de kleinste waarde die tijdens het aggregatie-interval is vastgelegd.

    ![Scherm opname van een minimum aanvraag.](./media/metrics-charts/request-min.png)

* **Max**: de grootste waarde die tijdens het aggregatie-interval is vastgelegd.

    ![Scherm opname van een maximum aanvraag.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filters

U kunt filters toep assen op grafieken waarvan de metrische gegevens dimensies hebben. Stel dat een metrische waarde voor het aantal trans acties met de dimensie ' respons type ' is. Deze dimensie geeft aan of de reactie van trans acties is geslaagd of mislukt. Als u filtert op deze dimensie, ziet u een grafiek lijn voor alleen geslaagde (of alleen mislukte) trans acties. 

### <a name="add-a-filter"></a>Een filter toevoegen

1. Selecteer **filter toevoegen** boven de grafiek.

2. Selecteer een dimensie (eigenschap) om te filteren.

   ![Scherm opname van de afmetingen (eigenschappen) die u kunt filteren.](./media/metrics-charts/028.png)

3. Selecteer de operator die u wilt Toep assen op de dimensie (eigenschap). De standaard operator is = (is gelijk aan)

   ![Scherm opname van de operator die u met het filter kunt gebruiken.](./media/metrics-charts/filter-operator.png)

4. Selecteer welke dimensie waarden u wilt Toep assen op het filter bij het tekenen van de grafiek (in dit voor beeld ziet u hoe de geslaagde opslag transacties worden gefilterd):

   ![Scherm opname waarin de geslaagde gefilterde opslag transacties worden weer gegeven.](./media/metrics-charts/029.png)

5. Nadat u de filter waarden hebt geselecteerd, klikt u op een van de filter selectie vakjes om deze te sluiten. De grafiek toont nu hoeveel opslag transacties zijn mislukt:

   ![Scherm afbeelding die laat zien hoeveel opslag transacties zijn mislukt.](./media/metrics-charts/030.png)

6. U kunt stap 1-5 herhalen om meerdere filters op dezelfde grafieken toe te passen.


## <a name="metric-splitting"></a>Metrische splitsing

U kunt een metriek per dimensie splitsen om te visualiseren hoe verschillende segmenten van de metriek worden vergeleken. Bij het splitsen kunt u ook de begrote segmenten van een dimensie identificeren.

### <a name="apply-splitting"></a>Splitsing Toep assen

1. Selecteer **splitsing Toep assen** boven de grafiek.
 
   > [!NOTE]
   > Grafieken met meerdere metrische gegevens kunnen de functie voor het splitsen niet gebruiken. Hoewel een grafiek meerdere filters kan hebben, kan er maar één splitsings dimensie zijn.

2. Kies een dimensie om uw grafiek te segmenteren:

   ![Scherm afbeelding met de geselecteerde dimensie waarop de grafiek moet worden gesegmenteerd.](./media/metrics-charts/031.png)

   In de grafiek worden nu meerdere regels weer gegeven, één voor elk dimensie segment:

   ![Scherm opname waarin meerdere lijnen worden weer gegeven, één voor elk segment van de dimensie.](./media/metrics-charts/segment-dimension.png)
   
3. Kies een limiet voor het aantal waarden dat moet worden weer gegeven na het splitsen van de geselecteerde dimensie. De standaard limiet is 10, zoals wordt weer gegeven in het bovenstaande diagram. Het bereik van de limiet is 1-50.
   
   ![Scherm opname met de gesplitste limiet, waarmee het aantal waarden wordt beperkt na het splitsen.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Kies de sorteer volgorde voor segmenten: oplopend of aflopend. De standaard selectie is Aflopend.
   
   ![Scherm opname van de sorteer volgorde op gesplitste waarden.](./media/metrics-charts/segment-dimension-sort.png)

5. Klik ergens buiten de **groeperings kiezer** om het venster te sluiten.
   

   > [!NOTE]
   > Als u segmenten wilt verbergen die niet relevant zijn voor uw scenario en om uw diagrammen gemakkelijker te kunnen lezen, moet u zowel filteren als splitsen gebruiken voor dezelfde dimensie.

## <a name="locking-the-range-of-the-y-axis"></a>Het bereik van de y-as vergren delen

Het vergren delen van het bereik van de waardeas (y) wordt belang rijk in grafieken waarin kleine schommelingen van grote waarden worden weer gegeven. 

Zo kan een daling in het volume van geslaagde aanvragen van 99,99 procent tot 99,5 procent duiden op een aanzienlijke vermindering van de Quality of service. Maar merkt is een kleine schommeling van de numerieke waarde moeilijk of zelfs onmogelijk als u de standaard instellingen voor grafieken gebruikt. In dit geval kunt u de laagste grens van de grafiek vergren delen op 99 procent om een kleine slag duidelijker te maken. 

Een ander voor beeld is een schommeling in het beschik bare geheugen. In dit scenario is de waarde technisch nooit 0. Als u het bereik herstelt naar een hogere waarde, kan het beschik bare geheugen gemakkelijker te herkennen zijn. 

Als u het bereik van de y-as wilt beheren, opent u het menu Grafiek (**...**). Selecteer vervolgens **grafiek instellingen** om toegang te krijgen tot geavanceerde grafiek instellingen.

![Scherm afbeelding die de selectie van de grafiek instellingen markeert.](./media/metrics-charts/033.png)

Wijzig de waarden in de sectie bereik van de **Y-as** of selecteer **automatisch** om terug te keren naar de standaard waarden.
 
 ![Scherm afbeelding die de sectie voor het bereik van de Y-as markeert.](./media/metrics-charts/034.png)

> [!WARNING]
> Als u de grenzen van de y-as wilt vergren delen voor grafieken die aantallen of sommen bijhouden gedurende een bepaalde periode (met behulp van aantal, som, min of Max aggregaties), moet u meestal een vaste tijd granulatie opgeven. In dit geval moet u niet vertrouwen op de automatische standaard waarden. 
>
> U kiest een vaste tijd granulatie omdat de grafiek waarden worden gewijzigd wanneer de granulatie van de tijd automatisch wordt gewijzigd nadat een gebruiker het formaat van een browser venster heeft gewijzigd of de scherm resolutie wijzigt. De resulterende wijziging van de tijd granulatie is van invloed op het uiterlijk van de grafiek, waarbij de huidige selectie van het bereik van de y-as ongeldig wordt.

## <a name="line-colors"></a>Lijn kleuren

Nadat u de grafieken hebt geconfigureerd, krijgen de grafiek lijnen automatisch een kleur uit een standaard palet. U kunt deze kleuren wijzigen.

Als u de kleur van een grafiek lijn wilt wijzigen, selecteert u de gekleurde balk in de legenda die overeenkomt met de grafiek. Het dialoog venster Kleuren kiezer wordt geopend. Gebruik de kleur kiezer om de lijn kleur te configureren.

![Scherm afbeelding die laat zien hoe u de kleur wijzigt.](./media/metrics-charts/035.png)

Uw aangepaste kleuren blijven behouden wanneer u de grafiek vastmaakt aan een dash board. In de volgende sectie ziet u hoe u een grafiek vastmaakt.

## <a name="pinning-to-dashboards"></a>Vastmaken aan dash boards 

Nadat u een grafiek hebt geconfigureerd, kunt u deze toevoegen aan een dash board. Als u een grafiek aan een dash board vastmaakt, kunt u deze toegankelijk maken voor uw team. U kunt ook inzicht krijgen door deze weer te geven in de context van andere telemetrie-bewaking.

Als u een geconfigureerde grafiek wilt vastmaken aan een dash board, selecteert u in de rechter bovenhoek van de grafiek **vastmaken aan dash board**.

![Scherm afbeelding die laat zien hoe u een grafiek vastmaakt aan een dash board.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Waarschuwingsregels

U kunt uw visualisatie criteria gebruiken om een regel voor de op metrische basis waarschuwing te maken. De nieuwe waarschuwings regel bevat de doel resources van uw grafiek, metrische gegevens, opsplitsen en filter dimensies. U kunt deze instellingen wijzigen met behulp van het deel venster waarschuwings regel maken.

Selecteer **nieuwe waarschuwings regel** om te beginnen.

![Scherm opname waarin de knop nieuwe waarschuwings regel wordt weer gegeven rood gemarkeerd.](./media/metrics-charts/042.png)

Het deel venster waarschuwings regel maken wordt geopend. In het deel venster ziet u de metrische dimensies van de grafiek. De velden in het deel venster zijn vooraf ingevuld om u te helpen bij het aanpassen van de regel.

![Scherm opname van het deel venster voor het maken van regels.](./media/metrics-charts/041.png)

Zie [metrische waarschuwingen maken, weer geven en beheren](../alerts/alerts-metric.md)voor meer informatie.

## <a name="correlate-metrics-to-logs"></a>Metrische gegevens correleren aan logboeken
Om klanten te helpen bij het vaststellen van de hoofd oorzaak van afwijkingen in hun metrische grafiek, hebben we analyse in logboeken gemaakt. Door in Logboeken in te zoomen kunnen klanten pieken in hun metrische grafiek correleren aan logboeken en query's. 

Voordat we de ervaring zien, willen we eerst de verschillende typen logboeken en query's introduceren. 

| Termijn             | Definitie  | 
|------------------|-------------|
| Activiteitenlogboeken    | Biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (het beheer vlak) naast updates voor Service Health gebeurtenissen. Gebruik het activiteiten logboek om te bepalen wat, wie en wanneer voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op de resources in uw abonnement. Er is één activiteiten logboek voor elk Azure-abonnement.  |   
| Diagnostisch logboek   | Verstrek inzicht in de bewerkingen die zijn uitgevoerd in een Azure-resource (het gegevens vlak), bijvoorbeeld om een geheim te verkrijgen van een Key Vault of een aanvraag naar een Data Base te maken. De inhoud van bron Logboeken is afhankelijk van de Azure-service en het resource type. **Opmerking:** Moet worden verschaft door de service en worden ingeschakeld door de klant  | 
| Aanbevolen logboek | Op scenario's gebaseerde query's die klanten kunnen gebruiken om afwijkingen in hun metrieke Explorer te onderzoeken.  |

Op dit moment zijn er analyse logboeken beschikbaar voor geselecteerde resource providers. De resource providers met de volledige analyse van logboeken zijn: 

* Application Insights 
* Automatisch schalen 
* App Services  
* Storage  

Hieronder ziet u een voor beeld van toepassingen voor de resource provider van Application Insights.

![Pieken in storingen in de Blade metrische gegevens van app Insights](./media/metrics-charts/drill-into-log-ai.png)

Als u de piek in mislukte aanvragen wilt vaststellen, klikt u op Details in Logboeken.

![Scherm opname van de vervolg keuzelijst logboeken](./media/metrics-charts/drill-into-logs-dropdown.png)

Als u op de fout optie klikt, wordt u geleid tot een aangepaste Blade met de fout die de mislukte bewerkingen, de belangrijkste uitzonderings typen en afhankelijkheden bevat. 

![Scherm afbeelding van de Blade fout met app Insights](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Veelvoorkomende problemen met Inzoomen op Logboeken

* Het logboek en de query's zijn uitgeschakeld: als u de aanbevolen logboeken en query's wilt weer geven, moet u de diagnostische logboeken naar Log Analytics verzenden. Lees [dit document](./diagnostic-settings.md) voor meer informatie over hoe u dit doet. 
* Er worden alleen activiteiten logboeken weer gegeven. de functie voor het in-en uitzoomen van Logboeken is alleen beschikbaar voor Select Resource providers. Er worden standaard activiteiten logboeken gegeven. 

 
## <a name="troubleshooting"></a>Problemen oplossen

Als er geen gegevens in uw grafiek worden weer gegeven, raadpleegt u de volgende informatie over het oplossen van problemen:

* Filters zijn van toepassing op alle grafieken in het deel venster. Terwijl u zich richt op een grafiek, moet u ervoor zorgen dat u geen filter instelt waarmee alle gegevens in een andere grafiek worden uitgesloten.

* Als u verschillende filters op verschillende grafieken wilt instellen, maakt u de grafieken op verschillende Blades. Sla de grafieken vervolgens op als afzonderlijke favorieten. Als u wilt, kunt u de grafieken vastmaken aan het dash board zodat u ze samen kunt zien.

* Als u een grafiek segmenteert op basis van een eigenschap die niet door de metrische waarde wordt gedefinieerd, wordt er geen inhoud weer gegeven in de grafiek. Probeer de segmentatie (splitsen) uit te scha kelen of kies een andere eigenschap.

## <a name="next-steps"></a>Volgende stappen

Zie [aangepaste KPI-Dash boards](../app/tutorial-app-dashboards.md)maken voor het maken van Dash boards die kunnen worden uitgevoerd met behulp van metrische gegevens.