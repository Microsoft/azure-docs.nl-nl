---
title: Azure Monitor metrische gegevens aggregatie en weer gave wordt uitgelegd
description: Gedetailleerde informatie over hoe metrische gegevens worden geaggregeerd in Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: 1d83ef07714e0ce69f01aa240cc3058195c7b1af
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251978"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Monitor metrische gegevens aggregatie en weer gave wordt uitgelegd

In dit artikel wordt uitgelegd wat de aggregatie van metrische gegevens in de Azure Monitor Data Base van de tijd reeks is en dat de metrische gegevens van het [platform](data-platform.md) en [aangepaste metrische](metrics-custom-overview.md)gegevens worden Azure monitor weer gegeven. Dit artikel is ook van toepassing op standaard [Application Insights metrische gegevens](../app/app-insights-overview.md). 

Dit is een complex onderwerp en is niet nodig om alle informatie in dit artikel te begrijpen om Azure Monitor metrische gegevens effectief te gebruiken.

## <a name="overview-and-terms"></a>Overzicht en voor waarden

Wanneer u een metriek toevoegt aan een grafiek, selecteert Metrics Explorer automatisch de standaard aggregatie. De standaard instelling is zinvol in de basis scenario's, maar u kunt verschillende aggregaties gebruiken om meer inzicht te krijgen in de metrische gegevens. Voor het weer geven van verschillende aggregaties in een grafiek is het vereist dat u begrijpt hoe Metrics Explorer ze verwerkt.

Laten we eerst enkele voor waarden definiëren:

- **Metrische waarde** : één meet waarde die is verzameld voor een specifieke resource.
- **Tijds periode** : een algemene periode.
- **Tijds interval** : de periode tussen het verzamelen van twee metrische waarden. 
- **Tijds bereik** : de tijds periode die in een grafiek wordt weer gegeven. Typische standaard waarde is 24 uur. Alleen specifieke bereiken zijn beschikbaar. 
- **Tijd granulatie** of **tijd korrel** : de periode die wordt gebruikt om waarden samen te voegen om weer gave in een grafiek toe te staan. Alleen specifieke bereiken zijn beschikbaar. Het huidige minimum is 1 minuut. De waarde voor tijd granulatie moet kleiner zijn dan het geselecteerde tijds bereik dat u wilt gebruiken. anders wordt slechts één waarde weer gegeven voor de hele grafiek. 
- **Aggregatie type** : een statistiek type dat wordt berekend op basis van meerdere meet waarden.  
- **Aggregatie** : het proces waarbij meerdere invoer waarden worden gemaakt en vervolgens worden gebruikt voor het produceren van één uitvoer waarde via de regels die zijn gedefinieerd door het aggregatie type. U kunt bijvoorbeeld een gemiddelde van meerdere waarden maken.  

Metrische gegevens zijn een reeks metrische waarden die volgens een regel matig tijds interval worden vastgelegd. Wanneer u een grafiek uitzet, worden de waarden van de geselecteerde metriek afzonderlijk geaggregeerd over de tijd granulatie (ook wel bekend als tijd korrel). U selecteert de grootte van de tijd granulatie met het [deel venster Metrics Explorer tijd kiezer](metrics-getting-started.md#select-a-time-range). Als u geen expliciete selectie maakt, wordt de tijd granulatie automatisch geselecteerd op basis van het momenteel geselecteerde tijds bereik. Als dit eenmaal is geselecteerd, worden de metrische waarden die zijn vastgelegd tijdens elke granulatie interval geaggregeerd en op de grafiek geplaatst: één data Point per interval.

## <a name="aggregation-types"></a>Aggregatie typen 

Er zijn vijf basis aggregatie typen beschikbaar in de metrics Explorer. Met metrische gegevens Verkenner worden de aggregaties verborgen die niet relevant zijn en kunnen niet worden gebruikt voor een bepaalde metriek. 

- **Sum** : de som van alle waarden die zijn vastgelegd in het aggregatie-interval. Wordt ook wel de totale aggregatie genoemd.
- **Count** : het aantal metingen dat over het aggregatie-interval is vastgelegd. Bij Count wordt de waarde van de meting niet weer geven, alleen het aantal records. 
- **Gemiddeld** : het gemiddelde van de metrische waarden die zijn vastgelegd in het aggregatie-interval. Voor de meeste metrische gegevens is deze waarde som/aantal. 
- **Min** -de kleinste waarde die is vastgelegd tijdens het aggregatie-interval.
- **Max** : de grootste waarde die over het aggregatie-interval is vastgelegd.

Stel bijvoorbeeld dat een grafiek de **totale metrische netwerk** gegevens voor een virtuele machine weergeeft met de aggregatie van de **som** over de laatste 24-uurs tijds duur. Het tijds bereik en de granulatie kunnen worden gewijzigd in de rechter bovenhoek van de grafiek, zoals in de volgende scherm afbeelding wordt weer gegeven.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Scherm opname van tijd bereik en tijd selecteren granulatie kiezer" border="true":::

Voor tijd granulatie = 30 minuten en het tijds bereik = 24 uur:

- De grafiek wordt getekend vanuit 48 data Points. Dat is 24 uur x 2 data Points per uur (60 min/30 min) samengevoegd van 1 minuut data Points. 
- Het lijn diagram verbindt 48 punten in het teken gebied van de grafiek. 
- Elk data Point vertegenwoordigt de som van alle verzonden uitgaande bytes van het netwerk tijdens elk van de relevante Peri Oden van 30 minuten. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Scherm opname van gegevens op een lijn grafiek die is ingesteld op 24-uurs tijd bereik en een tijd granulatie van 30 minuten" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Klik op de installatie kopieën in deze sectie om een grotere versie weer te geven.*

Als u de tijd granulatie naar 15 minuten overschakelt, wordt de grafiek getekend van 96 geaggregeerde gegevens punten. Dat wil zeggen, 60 min/15min = 4 Data Points per uur x 24 uur.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Scherm opname van gegevens op een lijn grafiek die is ingesteld op 24-uurs tijd en een tijd van 15 minuten" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Voor een tijd granulatie van vijf minuten krijgt u 24 x (60/5) = 288 punten. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Scherm opname van gegevens op een lijn grafiek die is ingesteld op 24-uurs tijd en een tijd van 5 minuten" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Voor een tijd granulatie van 1 minuut (zo klein mogelijk in de grafiek) krijgt u 24 x 60/1 = 1440 punten. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Scherm opname van gegevens op een lijn diagram die is ingesteld op 24-uurs tijd bereik en een tijd granulatie van 1 minuut" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

De grafieken zien er anders uit voor deze sommen, zoals wordt weer gegeven in de vorige scherm opnamen.  U ziet hoe deze VM veel uitvoer in een kleine tijds periode ten opzichte van de rest van het tijd venster bevat.  

Met de tijd granulatie kunt u de verhouding ' signaal-naar-ruis ' in een grafiek aanpassen. Met hogere aggregaties verwijdert u ruis en vloeiende pieken.  Let op de variaties in de onderste grafiek van 1 minuut en hoe deze zich versoepelen wanneer u naar hogere granulatie waarden gaat. 

Dit probleem is belang rijk wanneer u deze gegevens naar andere systemen verzendt, bijvoorbeeld waarschuwingen. Normaal gesp roken wilt u meestal niet gewaarschuwd worden door zeer korte pieken in de CPU-tijd van meer dan 90%. Maar als de CPU gedurende 5 minuten op 90% blijft, is dat waarschijnlijk belang rijk. Als u een waarschuwings regel instelt op CPU (of een wille keurige waarde), waardoor de nauw keurigheid van de tijd groter is, kan het aantal waarschuwingen dat u ontvangt, worden verminderd. 

Het is belang rijk om te bepalen wat ' normaal ' is voor uw werk belasting om te weten welk tijds interval het meest geschikt is. Dit is een van de voor delen van [dynamische waarschuwingen](alerts-dynamic-thresholds.md). Dit is een ander onderwerp dat hier niet wordt besproken.  

## <a name="how-the-system-collects-metrics"></a>Hoe de gegevens worden verzameld door het systeem

Gegevens verzameling varieert per metriek. Er zijn twee typen verzamelings perioden.

### <a name="measurement-collection-frequency"></a>Frequentie van meting verzameling 

- **Regel matig** : de metrische gegevens worden verzameld met een consistent tijds interval dat niet varieert.

- **Op activiteit gebaseerd** : de metrische gegevens worden verzameld op basis van het moment dat een trans actie van een bepaald type plaatsvindt. Elke trans actie heeft een metrische vermelding en een tijds tempel. Ze worden niet regel matig verzameld, zodat er sprake is van een verschillend aantal records gedurende een bepaalde periode. 

### <a name="granularity"></a>Granulariteit

Het minimale tijds interval is 1 minuut, maar het onderliggende systeem kan gegevens sneller vastleggen, afhankelijk van de metriek. Zo wordt het CPU-percentage bijvoorbeeld elke 15 seconden met een regel matig interval bijgehouden. Omdat HTTP-fouten worden bijgehouden als trans acties, kunnen ze heel veel meer dan één minuut duren. Andere metrische gegevens, zoals SQL-opslag, worden elke 20 minuten vastgelegd. Deze keuze is tot de afzonderlijke resource provider en het type. Probeer het kleinste interval mogelijk te maken.

### <a name="dimensions-splitting-and-filtering"></a>Dimensies, splitsen en filteren

Metrische gegevens worden vastgelegd voor elke afzonderlijke resource. Het niveau waarop de metrische gegevens worden verzameld, opgeslagen en kunnen worden gediagrameerd, kan echter verschillen. Dit niveau wordt weer gegeven door extra metrische gegevens die beschikbaar zijn in **metrische dimensies**. Elke afzonderlijke resource provider krijgt de definitie van de gedetailleerde gegevens die ze verzamelen. Azure Monitor definieert alleen hoe dergelijke details moeten worden gepresenteerd en opgeslagen. 

Wanneer u een metriek in metrische Explorer benadert, hebt u de mogelijkheid om de grafiek te splitsen op basis van een dimensie.  Het splitsen van een grafiek houdt in dat u de onderliggende gegevens bekijkt om meer details te bekijken en om te zien dat gegevens in metrische Explorer zijn gediagrameerd of gefilterd.

Bijvoorbeeld: [micro soft. ApiManagement/service](metrics-supported.md#microsoftapimanagementservice) heeft *locatie* als een dimensie voor veel metrische gegevens. 

- De **capaciteit** is een van deze metrische gegevens. Het hebben van de *locatie* dimensie impliceert dat het onderliggende systeem een metrische record voor de capaciteit van elke locatie opslaat, in plaats van slechts één voor het geaggregeerde bedrag. U kunt deze gegevens vervolgens ophalen of splitsen in een metrieke grafiek.  

- Als u de **totale duur van gateway aanvragen** wilt bekijken, zijn er twee dimensie *locaties* en *hostnamen*, zodat u de locatie van de duur en de hostnaam van de server opnieuw kunt weten.  

- Een van de flexibele metrische gegevens, **aanvragen**, heeft 7 verschillende dimensies. 
 
Controleer het artikel Azure Monitor [metrische gegevens](metrics-supported.md) dat wordt ondersteund voor meer informatie over elke metriek en de beschik bare dimensies. Daarnaast kan de documentatie voor elke resource provider en elk type aanvullende informatie geven over de dimensies en wat ze meten.

U kunt splitsen en filteren samen gebruiken om een probleem te verhelpen. Hieronder ziet u een voor beeld van een afbeelding met de *gemiddelde schrijf bewerkingen voor schijven* voor een groep virtuele machines in een resource groep. We hebben een samen telling van alle virtuele machines met deze metrische gegevens, maar het is mogelijk dat u wilt zien wat er daad werkelijk verantwoordelijk is voor de pieken rondom 06.00. Bevinden ze zich op dezelfde computer? Hoeveel computers zijn er betrokken?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Scherm opname van het totale aantal geschreven bytes op de schijf voor alle virtuele machines in de resource groep contoso hotels" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Klik op de installatie kopieën in deze sectie om een grotere versie weer te geven.*

Bij het Toep assen van splitsen worden de onderliggende gegevens weer gegeven, maar dit is een beetje van een rommel. Hiermee worden er 20 Vm's in de bovenstaande grafiek geaggregeerd. In dit geval hebben we onze muis gebruikt voor het aanwijzen van de grote piek op 06.00 die ons vertelt dat CH-DCVM11 de oorzaak is. het is echter moeilijk om de rest van de gegevens die zijn gekoppeld aan de virtuele machine weer te geven vanwege andere Vm's die het diagram verruimen. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Scherm opname van schijf schrijf bewerkingen voor alle virtuele machines in contoso Hotels resource groep splitsen op naam van virtuele machine" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

Door filteren te gebruiken kunnen we de grafiek opschonen om te zien wat er echt gebeurt. U kunt de Vm's die u wilt weer geven, controleren of uitschakelen. Let op de stippel lijnen. Deze worden beschreven in een latere sectie.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Scherm opname van schijf schrijf bewerkingen voor alle virtuele machines in contoso Hotels resource groep splitsen en gefilterd op de naam van de virtuele machine" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Voor meer informatie over het weer geven van gesplitste dimensie gegevens in een metrische Explorer-grafiek raadpleegt u [geavanceerde functies van Metrics Explorer-filters en splitsing](metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>NULL-waarden en nulwaarden

Wanneer het systeem metrische gegevens van een resource verwacht, maar niet wordt ontvangen, wordt een NULL-waarde geregistreerd.  NULL wijkt af van een nul-waarde, die belang rijker wordt bij het berekenen van aggregaties en grafieken. NULL-waarden worden niet meegerekend als geldige metingen. 

NULL-waarden worden op verschillende grafieken anders weer gegeven. De sprei ding gaat overs Laan om een punt in de grafiek weer te geven. In de staaf diagram worden de balk overs laan weer gegeven. Op lijn diagrammen kan NULL worden weer gegeven als [gestippelde of onderbroken lijnen](metrics-troubleshoot.md#chart-shows-dashed-line) , zoals die worden weer gegeven in de scherm opname in de vorige sectie. Bij het berekenen van gemiddelden die NULL-waarden bevatten, zijn er minder gegevens punten om het gemiddelde van te maken.  Dit gedrag kan soms leiden tot onverwachte neerzetten in waarden in een grafiek, maar dit is meestal minder dan wanneer de waarde is geconverteerd naar een nul en wordt gebruikt als een geldig data Point.  

Bij [aangepaste metrische](metrics-custom-overview.md) gegevens worden altijd nullen gebruikt wanneer er geen data worden ontvangen. Met [platform metrieken](data-platform.md)wordt door elke resource provider besloten of er nullen of nullen moeten worden gebruikt op basis van wat het meest zinvol is voor een bepaalde metriek.

Azure Monitor-waarschuwingen gebruiken de waarden die door de resource provider worden geschreven naar de metrische data base. het is dus belang rijk om te weten hoe de resource provider NULLs verwerkt door de gegevens eerst weer te geven.

## <a name="how-aggregation-works"></a>Hoe aggregatie werkt

De metrische grafieken in het vorige systeem tonen verschillende typen geaggregeerde gegevens. Het systeem voegt vooraf de gegevens samen, zodat de aangevraagde grafieken sneller kunnen worden weer gegeven zonder veel herhaalde berekeningen.  

In dit voorbeeld:

- Er wordt een **fictieve** transactionele metrische waarde verzameld met de naam **HTTP-fouten** 
- *Server* is een dimensie voor de metrische gegevens van de **HTTP-fout** .
- We hebben drie servers: Server A, B en C.

Om de uitleg te vereenvoudigen, beginnen we alleen met het aggregatie type SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Aggregatie van sub minute naar 1 minuut

Eerste onbewerkte metrische gegevens worden verzameld en opgeslagen in de data base van de Azure Monitor metriek. In dit geval heeft elke server transactie records die zijn opgeslagen met een tijds tempel omdat *Server* een dimensie is. Gezien de kleinste periode die u kunt weer geven als een klant is 1 minuut, worden deze tijds tempels eerst geaggregeerd in metrische waarden voor 1 minuut voor elke afzonderlijke server.  Het aggregatie proces voor Server B wordt weer gegeven in de onderstaande afbeelding. Servers A en C worden op dezelfde manier uitgevoerd en hebben verschillende gegevens.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Scherm opname van de transactionele items van een sub-minuut in aggregaties van 1 minuut. " border="false":::

De resulterende aggregatie waarden van 1 minuut worden opgeslagen als nieuwe vermeldingen in de data base metrische gegevens, zodat deze kunnen worden verzameld voor latere berekeningen. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Scherm afbeelding met meerdere geaggregeerde vermeldingen van 1 minuut over de dimensie van de server. Server A, B en C afzonderlijk weer gegeven" border="false":::

### <a name="dimension-aggregation"></a>Dimensie aggregatie

De berekeningen van 1 minuut worden vervolgens samengevouwen door dimensie en opnieuw opgeslagen als afzonderlijke records.   In dit geval worden alle gegevens van alle afzonderlijke servers geaggregeerd naar een interval van 1 minuut en opgeslagen in de data base metrics voor gebruik in latere aggregaties.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Scherm afbeelding met meerdere gecombineerde vermeldingen van 1 minuut van server A, B en C samengevoegd tot 1 minuut alle servers volledig" border="false":::

Ter duidelijkheid toont de volgende tabel de methode voor aggregatie.

| Periode   | Server A | Server B | Server C | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minuut 1 | 1        | 1        | 1        | 3          |
| Minuut 2 | 0        | 5        | 1        | 6          |
| Minuut 3 | 0        | 5        | 1        | 6          |
| Minuut 4 | 2        | 3        | 4        | 9          |
| Minuut 5 | 1        | 0        | 3        | 4          |
| Minuut 6 | 1        | 0        | 4        | 5          |
| Minuut 7 | 1        | 2        | 4        | 7          |
| Minuut 8 | 0        | 1        | 0        | 1          |
| Minuut 9 | 1        | 1        | 4        | 6          |
| Minuut 10| 2        | 1        | 0        | 3          |

Er wordt slechts één dimensie hierboven weer gegeven, maar hetzelfde aggregatie-en opslag proces vindt plaats voor **alle dimensies** die door een metriek worden ondersteund.

- Verzamel waarden in 1-minuten geaggregeerde set met die dimensie. Sla deze waarden op. 
- Vouw de dimensie samen tot een geaggregeerde som van 1 minuut. Sla deze waarden op. 

We introduceren een andere dimensie van HTTP-fouten met de naam netwerk adapter. Stel dat we een verschillend aantal adapters per server hebben.

- Server A heeft 1 adapter
- Server B heeft 2 adapters
- Server C heeft 3 adapters

We verzamelen gegevens voor de volgende trans acties afzonderlijk. Ze worden gemarkeerd met:

- Een tijd
- Een waarde
- De server waarvan de trans actie afkomstig is
- De adapter waarvan de trans actie afkomstig is

Elk van deze subminuut stromen zou vervolgens worden geaggregeerd in waarden van 1 minuut en opgeslagen in de data base met Azure Monitor metriek:

- Server A, adapter 1
- Server B, adapter 1
- Server B, adapter 2
- Server C, adapter 1
- Server C, adapter 2
- Server C, Adapter 3

Daarnaast worden ook de volgende samengevouwen aggregaties opgeslagen:

- Server A, adapter 1 (omdat er niets kan worden samengevouwen, wordt het opnieuw opgeslagen)
- Server B, adapter 1 + 2
- Server C, adapter 1 + 2 + 3
- Servers, alle adapters

Dit geeft aan dat metrische gegevens met een groot aantal dimensies een grotere hoeveelheid aggregaties hebben. Het is niet belang rijk om te weten wat de permutaties zijn. u hoeft alleen maar te begrijpen wat de reden is. Het systeem wil zowel de afzonderlijke gegevens als de geaggregeerde gegevens die zijn opgeslagen voor snel ophalen voor toegang voor een grafiek. Het systeem kiest ofwel de meest relevante opgeslagen aggregatie of de onderliggende onbewerkte gegevens, afhankelijk van wat u wilt weer geven. 

### <a name="aggregation-with-no-dimensions"></a>Aggregatie zonder dimensies

Omdat deze metriek een dimensie *Server* heeft, kunt u de onderliggende gegevens voor Server a, B en C bekijken via splitsen en filteren, zoals eerder in dit artikel is beschreven. Als de metriek geen *Server* als dimensie heeft, kan de klant alleen toegang krijgen tot de geaggregeerde som van 1 minuut die in het diagram zwart wordt weer gegeven. Dat wil zeggen, de waarden van 3, 6, 6, 9, enzovoort. Het systeem kan ook niet het onderliggende werk doen om gesplitste waarden samen te voegen, maar ze zouden ze nooit gebruiken in metrische Explorer of ze verzenden via de metrische gegevens REST API. 

## <a name="viewing-time-granularities-above-1-minute"></a>Nauw keurigheid van de tijd voor weer gave van meer dan 1 minuut

Als u vraagt om metrische gegevens met een grotere granulariteit, gebruikt het systeem de geaggregeerde som van 1 minuut om de sommen te berekenen voor de grotere tijd granularies.  Hieronder ziet u de optel methode voor de tijd granulars van 2 en 5 minuten. Opnieuw wordt alleen het aggregatie type SUM voor eenvoud weer gegeven.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Scherm afbeelding met meerdere geaggregeerde vermeldingen van 1 minuut in de dimensie van de server die is geaggregeerd met 2-minuten en 5-minimale tijds perioden." border="false":::

Voor de granulatie tijd van 2 minuten.

| Periode       | Middelen       
| -------------|-------------|  
| Minuut 1 & 2 | (3 + 6) = 9 |
| Minuut 3 & 4 | (6 + 9) = 15|
| Minuut 4 & 5 | (4 + 5) = 9 |
| Minuut 6 & 7 | (7 + 1) = 8 |
| Minuut 8 & 9 | (6 + 3) = 9 |

Voor granulatie tijd van 5 minuten.

| Periode              |            Middelen        |
|---------------------|------------------------|  
| Minuut 1 tot en met 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minuut 6 t/m 10 | 5 + 7 + 1 + 6 + 3 = 22 |

Het systeem gebruikt de opgeslagen geaggregeerde gegevens die de beste prestaties bieden. 

Hieronder ziet u het grotere diagram voor het bovenstaande aggregatie proces van 1 minuut, waarbij enkele van de pijlen links uitvallen om de Lees baarheid te verbeteren.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Scherm opname van het samen voegen van vorige 3 scherm afbeeldingen. Meerdere geaggregeerde vermeldingen van 1 minuut over de samen voeging van een server met een interval van 1 minuut, 2 minuten en 5 minuten. Server A, B en C afzonderlijk weer gegeven" border="false":::

## <a name="more-complex-example"></a>Complexere voor beeld

Hieronder volgt een groter voor beeld met waarden voor een fictieve waarde met de naam HTTP-reactie tijd in milliseconden.  Hier worden extra complexiteits niveaus geïntroduceerd.

1. Aggregatie voor Sum, Count, min en Max en de berekening voor gemiddelde worden weer gegeven.
2. We tonen NULL-waarden en bepalen hoe ze berekeningen hebben. 

Bekijk het volgende voorbeeld. De vakken en pijlen geven voor beelden van hoe de waarden worden geaggregeerd en berekend. 

Hetzelfde preaggregatie proces van 1 minuut, zoals beschreven in de vorige sectie, vindt plaats voor de som, het aantal, het minimum en het maximum.  Gemiddeld is echter niet vooraf geaggregeerd. Het wordt opnieuw berekend met behulp van geaggregeerde gegevens om berekenings fouten te voor komen.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Scherm afbeelding met een complex voor beeld van aggregatie en berekening van som, aantal, min, Max en gemiddelde van 1 minuut tot 10 minuten." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Overweeg minuut 6 voor de aggregatie van 1 minuut zoals hierboven is gemarkeerd. Deze minuut is het punt waar Server B offline is gegaan en de rapportage gegevens heeft gestopt, mogelijk als gevolg van een herstart. 

Van minuut 6 hierboven zijn de berekende aggregatie typen van 1 minuut: 

| Type aggregatie | Waarde        | Opmerkingen |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Count            | 2            | Toont het effect van NULL-waarden.  Als de server online was, zou de waarde 3 zijn.  |
| Minimum          | 20           | |
| Maximum          | 53           | |
| Gemiddeld          | 73/2       | Altijd de som gedeeld door de telling. Het wordt nooit opgeslagen en wordt altijd opnieuw berekend voor elke granulatie met de geaggregeerde getallen voor de granulariteit. Let op de herberekening voor de nauw keurigheid van vijf minuten en een tijd van 10 minuten zoals hierboven is gemarkeerd. |

De rode tekst kleur geeft aan welke waarden in het normale bereik kunnen worden beschouwd en laat zien hoe ze worden door gegeven (of mislukt) naarmate de tijd granulatie wordt bereikt. U ziet dat de *minimum* -en *maximum* waarde duiden op onderliggende afwijkingen, terwijl het *gemiddelde* en de *som* verloren gaan.  

U kunt ook zien dat de NULL-waarden een betere berekening van het gemiddelde bieden dan wanneer er in plaats daarvan nullen zijn gebruikt.  

> [!NOTE] 
> Hoewel in dit voor beeld niet het geval is, is *Count* gelijk aan *Sum* in gevallen waarin een metriek altijd wordt vastgelegd met de waarde 1. Dit is gebruikelijk wanneer een metriek het exemplaar van een transactionele gebeurtenis registreert, bijvoorbeeld het aantal HTTP-fouten dat in een eerder voor beeld in dit artikel wordt genoemd.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Metrics Explorer](metrics-getting-started.md)
- [Geavanceerde Metrics Explorer](metrics-charts.md)
